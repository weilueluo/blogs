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

## Simple Database

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

## HashMap

A index can be just a simple hash map mapping key to byte offset of the value in the database. With this simple setup, we can achieve 10-100x faster reads, trading with ~2x slower writes. This is an important trade-off --- while an well-chosen index speeds up reads, it always slows down writes, because we have an additional data structure to update every time we do a write. I believe most people would signup for an index unless your business is so critical on read speed.

| Operation (hash map index)                           | Speed     |
| ---------------------------------------------------- | --------- |
| write `10,000` records                               | `0.0084s` |
| write `100,000` records                              | `0.0897s` |
| write `1,000,000` records                            | `1.1117s` |
| read `1,000` random records from `10,000` records    | `0.0023s` |
| read `1,000` random records from `100,000` records   | `0.0134s` |
| read `1,000` random records from `1,000,000` records | `0.1794s` |

This simple setup [[ref]](https://github.com/weilueluo/db-samples/blob/master/index-hashmap/index-hashmap.ipynb) is essentially what Bitcask does --- the default storage engine of Riak [[ref]](https://riak.com/assets/bitcask-intro.pdf). It offers high performance reads and writes, given the index must fit into the available RAM, because the index must be loaded into memory. If the data is already present in the file system cache, it does not even require a disk access on read. In the case where your index can't be loaded into memory, you can use alternative like Berkeley DB which designed to operate directly on disk. Note that on disk hash map is expensive, 

## LSM-Tree

An obvious issue with our simple database setup is our append-only data file (a.k.a. ***log***) just grows indefinitely, we are appending an additional value for every new write, this will eventually consumes all available memory. We can try to find and replace old value during write. This option is viable but in this section, we are going to focus on another option: removing stale data during free time, and see how it leads to databases based on LSM-tree.

### Compaction

The process of removing stale data is called ***compaction***. Stale data are outdated duplicated keys exists in the *log*. One simple way to remove these keys would be go through all entries from latest to oldest, adding keys to a hash set and remove entries with keys already exists. This works only if all keys fits into memory as this uses *O(n)* space.

Let's do better by changing our *log* into a sorted data structure. Now duplicates are grouped so we can go through the entries once and remove duplicates without using additional space resulting in a *O(1)* space algorithm. Another improvement we can make is splitting the *log* into multiple smaller data files (a.k.a ***segments***), and run compaction on subset of the segments rather than the whole *log* at once.

Wait, a sorted data structure? Does this mean our write becomes *O(logn)*? Yes but it is faster than you might think, since our data is split into segments, our *n* is the size of the segment rather than the size of the whole database. In practice, a write usually consists of insert data into an in-memory data structure called ***memtable***, typically implemented with b-tree or skip list, providing fast write. This data structure will be flushed to disk as an immutable data file after reaching certain size, this immutable on disk data file is called ***Sorted String Table (SSTable)***.

### Merge

After compacting multiple segments, we merge them to prevent over-fragmentation. Generally speaking, during a read operation, if data is not present in the *memtable*, we will need to utilize *SSTables* on disk. While we can make use of *bloom filter* to determine which *SSTable* may contain the data, we generally need to search within individual table from newest to oldest. Thus reducing the number of SSTables can be important for improving performance. 

You still need an index in memory when searching within each *SSTable*, but it can be sparse because data is sorted and scanning through a few kilobytes is very fast. You might ask since the data is sorted why not binary search? This is because as a key-value store, values often comes in variable length, it is difficult to tell where a entry starts and ends without an index.

<img src="C:/Users/luowe/AppData/Roaming/Typora/typora-user-images/image-20240915162154247.png" alt="image-20240915162154247" style="zoom:80%;" />

One thing convenient about compaction and merge is they can be done together as one step in the background. This is because compaction generally reduces size of the *SSTables* and *SSTables* are immutable, allowing us to serve reads concurrently with stale data.

<img src="C:/Users/luowe/AppData/Roaming/Typora/typora-user-images/image-20240915162101899.png" alt="image-20240915162101899" style="zoom:80%;" />

### LSM

The procedure we just describe is essential what ***Log-Structured Merge-Tree (LSM-Tree)*** based database like LevelDB [[ref]](https://github.com/google/leveldb/blob/main/doc/impl.md) and RocksDB [[ref]](https://rocksdb.blogspot.com/2013/11/the-history-of-rocksdb.html) works under the hood for reads and writes. Lucene, a full-text search engine used by Elastic Search uses a similar method for storing its *term dictionary* where it maps a *term* to all document ids where this *term* appeared in a key-value store.

There are a great amount of details we did not cover in this section, let's have a brief overview on other important bits.

- **Deletion** A special entry (sometimes called *tomestone*) is written as an indicator to invalidate all previous entries of that key.
- **Crash Recovery** data is first written to an append-only log on disk (a.k.a. ***Write-Ahead Log (WAL)***), allowing replay on crash recovery. In case of partially written entry in WAL, data validation like checksums is used to ensure only fully written entries are replayed.
- **Concurrency Control** Since writes are appended to WAL in strictly sequential order, a common implementation is to have only one writer thread, but since disk write is sequential, it is enough to provide remarkably high throughput. Read can be concurrent because *SSTables* are immutable.

You might say this whole idea based on append-only *log* is too wasteful, actually, it is. We need to do additional work to compaction and merge the database. However, people have not come up with a perfect structure that solves all the problems, this is a trade-off we have to make. In practice, the LSM structure is simple and effective. When compared to hash index, it brings several benefits:

- **Memory** LSM-tree does not requires all keys to be fitted into memory.
- **Disaster Recovery** Disaster recovery becomes simple as you do not need to worry about partially written SSTables due to its immutability.
- **Cost** Compaction and merge simply go through data sequentially, can be done efficiently.
- **Ranged Queries** You can easily scan related keys for example from Kitty000 to Kitty999 efficiently since keys are sorted.

LSM-tree databases are not perfect, you might have already recognize a problem with LSM-tree setup --- looking for a missing key can be inefficient because you need to look at the *memtables*, then all the *SSTables* filtered by *bloom filters*. There are also strategies for determining the order and timing of how *SSTables* are compacted and merged. *LevelDB* and *RocksDB* uses *level-tier*, *HBase* uses *size-tier*, and *Cassandra* supports both.

## B-Tree

