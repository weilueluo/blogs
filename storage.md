# Database

> [!NOTE]
>
> This note is not done



> As a developer we do not care about the internal of database, but we do care about choosing a suitable database and finetuning database for performance.
>
> transactional workload vs analytics workload
>
> log-structured database vs page-oriented database
>
> optimization

### Simple Database

Let's build the world's simplest database to store key-value data with just two operations:

- Store a key-value pair by appending to the end of a file

- Get the value given the key from database by searching matching line, extract the value of the matching keys, and return the latest.


This simple setup is surprising efficient for small scale data, because appending to a file is generally a fast operation. However, it is also obvious that this database does not perform well when the amount of data grows very large. The following table shows some statistics of this simple database with a naive implementation on my machine [[ref]](https://github.com/weilueluo/db-samples/blob/master/simple/simple.ipynb).

| Operation                                            | Speed      |
| ---------------------------------------------------- | ---------- |
| write `10,000` records                               | `0.0042s`  |
| write `100,000` records                              | `0.0433s`  |
| write `1,000,000` records                            | `0.5262s`  |
| read `1,000` random records from `10,000` records    | `0.0890s`  |
| read `1,000` random records from `100,000` records   | `0.9261s`  |
| read `1,000` random records from `1,000,000` records | `10.0677s` |

## Index

As we saw, our write queries are pretty efficient, but our read queries slow down drastically as our database grows. In order to efficiently find value of a given key, we can use an ***index***. An index is a data structure derived from the primary data, storing additional metadata that speed up read queries, avoid the need of scanning the full database.

### HashMap

A index can be just a simple hash map mapping key to byte offset of the value in the database. With this simple setup, we can achieve 10-100x faster reads, trading with ~2x slower writes. This is an important trade-off --- while an well-chosen index speeds up reads, it always slows down writes, because we have an additional data structure to update every time we do a write. I believe most people would signup for an index unless your business is so critical on read speed.

| Operation (hash map index)                           | Speed     |
| ---------------------------------------------------- | --------- |
| write `10,000` records                               | `0.0084s` |
| write `100,000` records                              | `0.0897s` |
| write `1,000,000` records                            | `1.1117s` |
| read `1,000` random records from `10,000` records    | `0.0023s` |
| read `1,000` random records from `100,000` records   | `0.0134s` |
| read `1,000` random records from `1,000,000` records | `0.1794s` |

This simple setup [[ref]](https://github.com/weilueluo/db-samples/blob/master/index-hashmap/index-hashmap.ipynb) is essentially what Bitcask does --- the default storage engine of Riak [[ref]](https://riak.com/assets/bitcask-intro.pdf). It offers high performance reads and writes, given the index must fit into the available RAM, because the index must be loaded into memory. If the data is already present in the file system cache, it does not even require a disk access on read. In the case where your index can't be loaded into memory, you can use alternative like Berkeley DB which designed to operate directly on disk.

## LSM-Tree

An obvious issue with our simple database setup is our append-only data file (a.k.a. ***log***) just grows indefinitely, we are appending an additional value for every new write, this will eventually consumes all available memory. We generally have two options: remove stale data during free time, or find and replace old value during write. Both options are viable but in this section, we are going to focus on removing stale data during free time. We are going to solve two major problems with simple hash map index --- index larger than available memory and inefficient ranged query due to unsorted nature of hash map.

### Compaction

The process of removing stale data is called ***compaction***. Stale data are duplicated keys exists in the *log*, how can we efficiently remove these entries? A simple algorithm would be go through all entries from latest to oldest, adding keys to a hash set and remove entries with keys already exists. This works only if all keys fits into memory. 

Let's do better by changing our *log* into a sorted data structure. Now we can go through the entries once and remove duplicates without using additional space. In addition, we do not want to go through all the data at once during compaction because that would take too long, instead, it would be better to split the big data file into multiple files, so called segments, and run compaction on individual segment rather than the whole dataset at once.

Wait, a sorted data structure? Does this mean our write becomes *O(logn)*? Yes but it is faster than you might think, since our data is split into segments, our *n* is the size of the segment rather than the size of the whole database. In practice, a write usually consists of two operations: (1) insert data into an in-memory data structure called ***memtable***, typically implemented with b-tree or skip list, providing fast write, which will be flushed to disk when it reached certain size, called ***Sorted String Table (SSTable)***; (2) append data to a ***write-ahead log (WAL)*** for data recovery.

### Merge

After compacting multiple segments, we merge them to prevent over-fragmentation. Generally speaking, during a read operation, if data is not present in the *memtable*, we will need to utilize *SSTable* on disk. We do not know which *SSTable* to look at, so we need to search in individual table from newest to oldest. Thus reducing the number of SSTables is important for improving performance. In practice, a read also consists of looking into other places, for example pending immutable memtables that are ready to be flushed to disk; and using bloom filter to determine which SSTables may contain the data.

In addition, since compaction reduce size of *SSTables*, merging can be done together with compaction. Also, since **SSTables** are append-only, it is immutable, the compact and merge process can be done in a background thread, and we can still serve reads with stale data.

### LSM-Tree Database

***LSM-tree*** stands for ***Log-Structured Merge-tree***. The procedure we just describe is essential what LSM-tree based database like LevelDB [[ref]](https://github.com/google/leveldb/blob/main/doc/impl.md) and RocksDB [[ref]](https://rocksdb.blogspot.com/2013/11/the-history-of-rocksdb.html) works under the hood for reads and writes. There are a great amount of details we did not cover in this section, let's have a brief overview.

- **Deletion** A special entry (sometimes called *tomestone*) is written as an indicator to invalidate all previous entries of that key.
- **Crash Recovery** WAL
