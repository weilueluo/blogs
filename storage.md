# Database

> As a developer we do not care about the internal of database, but we do care about choosing a suitable database and finetuning database for performance. 
>
> transactional workload vs analytics workload
>
> log-structured database vs page-oriented database

### Simple Database

Let's build the world's simplest database to store key-value data with just two operations:

- Store a key-value pair by appending to the end of a file

- Get the value given the key from database by searching matching line, extract the value of the matching keys, and return the latest.


This simple setup is surprising efficient for small scale data, because appending to a file is generally a fast operation. However, it is also obvious that this database does not perform well when the amount of data grows very large. The following table shows some statistics of this simple database with a naive implementation on my machine.

- **CPU** 12th Gen Intel(R) Core(TM) i7-12700K

| Operation (Simple Database)                          | Speed      |
| ---------------------------------------------------- | ---------- |
| write `10,000` records                               | `0.0042s`  |
| write `100,000` records                              | `0.0433s`  |
| write `1,000,000` records                            | `0.5262s`  |
| read `1,000` random records from `10,000` records    | `0.0890s`  |
| read `1,000` random records from `100,000` records   | `0.9261s`  |
| read `1,000` random records from `1,000,000` records | `10.0677s` |

## Index

As we saw, while our write queries are pretty efficient, our read queries slow down drastically as our database grows. In order to efficiently find value of a given key, we can use an ***index***. An index is a data structure derived from the primary data, storing additional metadata that speed up read queries, avoid scanning the full database. Index is an important trade-off --- while an well-chosen index speeds up reads, but it also generally slows down writes, because now we have additional data structure to maintain every time we do a write.

### HashMap

A simple index can be just a hash map mapping key to byte offset of the value in the database. With this simple setup, we can achieve 10-100x faster reads and ~2x slower writes! Unless your business is so critical on read speed, I believe anyone would signup for an index!

| Operation (Simple Database)                          | Speed     |
| ---------------------------------------------------- | --------- |
| write `10,000` records                               | `0.0084s` |
| write `100,000` records                              | `0.0897s` |
| write `1,000,000` records                            | `1.1117s` |
| read `1,000` random records from `10,000` records    | `0.0023s` |
| read `1,000` random records from `100,000` records   | `0.0134s` |
| read `1,000` random records from `1,000,000` records | `0.1794s` |

This simple setup is essentially what Bitcask does --- the default storage engine of Riak [[ref]](https://riak.com/assets/bitcask-intro.pdf). It offers high performance reads and writes, given the index must fit into the available RAM, because the index must be loaded into memory, and read is just one disk seek. If the data is already present in the file system cache, it does not even require a disk access.
