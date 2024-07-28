# Designing Data Intensive Application

## Reliable, Scalable, and Maintainable Applications

Many applications today is *data-intensive*, rather than *compute-intensive*. As CPUs are getting stronger everyday, it is rarely a limitation, bigger problems are usually amount of data, complexity of the data, and the speed it changes. For examples, many systems are needed to store, stream / batch process, and cache data they received or owned.

If this sounds too obvious, it is because most of the time we just use data systems without thinking much. We do not write a storage engine from scratch when we need to use one, because databases are perfect fit for the job.

> The Internet was done so well that most people think of it as a natural resource like Pacific Ocean, rather than something that was man-made. When was the last time a technology with a scale like that so error-free?
>
> --- Alan Kay, in interview with *Dr Dobb's Journal*.

Today's data systems are built on top of many other systems and abstractions, there is no one fits all solution, each system is specifically designed to solve its own problem well. There are many factors influencing the design decision and trade-off of systems, including skills and experience of the people involved, legacy systems dependencies, time-scale for delivery, risk tolerance, etc... In this series, we focus on three concerns that are important for most systems:

- ***Reliability*** The system should continue to work correctly upon faults.
- ***Scalability*** The system should be able to deal with growth such as data, traffic and complexity.
- ***Maintainability*** New engineers should be able to maintain, learn and implement new features productively.

All of above concerns are only valid to a reasonable extent, for example, in order to allow your system to work reliably when our plant Earth is swallowed by a black hole, you got to deploy your system on another planet --- good luck getting the budget approved!

### Reliability

You might say why don't we prevent faults instead of trying to tolerate them? The answer is simple --- we cannot guarantee faults from not happening, for example a common hard disk has a mean time to failure of about 10-50 years, if you have a cluster of 10,000 disks, you should expect on average one disk to die per day [[ref]][https://www.backblaze.com/blog/backblaze-drive-stats-for-2023/]. Over the years, we have used many techniques such as RAID, dual power supplies, and hot-swappable CPUs. These methods are well understood and can keep a machine running uninterrupted for years.

**Hardware:** Redundancy of hardware components are sufficient for most applications, but as data volumes increases, many applications have begun using larger number of machines, which proportionally increase rate of hardware faults. Multi-machines set up can also have operational advantage of eliminating planned downtime with rolling upgrades.

**Software:** There can also be software faults due to insufficient testing, improper error handling, leaked resources and cascading failures. There are no quick answers but things to keep in mind are (1) **simple tests are able to prevent most of the critical errors** [[ref]][https://cs.brown.edu/~ugur/fits_all.pdf ], (2) sufficient monitoring, isolation can provide a certain degree of guarantee.

**Human Error:** Lastly there can also be human errors such as misconfiguration. Human are always unreliable, we should design system that has minimal opportunities for human to make error, decouple error-prone area of the code, add detailed monitoring, logs and allow easy recovery. Note that as amount of data increases, corner cases will starts to appear more often and can potentially becomes an operational burden and go unmaintainable.

### Scalability

One common cause of performance degradation is increased load - user grows from 1 million to  10 million, concurrent users grows from 10 thousands to 100 thousands, system is under pressure to process much more data than usual. Scalability describes the how well the system can scale with *load* increases, the term *load* is not a one dimensional label, it can mean different things in different context, thus it is meaningless to simply say some system X does not scale.

We can quantify scalability in term of a group of *load parameters*, example of such parameter includes response latency, throughput per second, cache hit rate, read and write requests to database, etc... The best choice of the parameters depends on what matter most to your system, perhaps you care more about average case. Perhaps small number of extreme cases is the main bottleneck.

In a batch processing system like hadoop, you probably care about ***throughput*** --- number of records process per seconds; In a online system, you probably care more about ***response time*** and ***latency***.

> Response time and latency are often confused. Response time is what the end-user sees, how long did the server sends a response back; whereas latency measure the time a request waiting to be processed.

**Response time is a distribution rather than some discrete number**, you get different response time even if you send the same request multiple times with a short interval. Beware that *average* over a distribution is not a good metric, it does not really tell you how many users are actually experiencing that value. Usually it is better to use ***percentile***, *median* is 50th percentile, abbreviated as *p50*, if your *p50* is 200ms, then you know half of your requests returns under 200ms. Outliners are measured in higher percentile such as p99 and p99.9.

In a system that process multiple requests in parallel, tail percentile becomes especially important, this is because all requests have to wait for the slowest request returns to complete. Therefore percentile is often used in Service Level Agreement (SLA) to define performance contract.

We often talk about vertical and horizontal scaling, vertical scaling increases individual node's capability and horizontal scaling increases number of nodes. Generally, if you could get away with vertical scaling, go for it until it forces you to be distributed, scaling out stateful system introduces you to a more error-prone solution, which makes your system harder to maintain. Most importantly, most real world business does not need to process that much data. On the other hand, as tools and abstractions gets better over time, going distributed becomes easy, and in term of data intensive application which is what we are focusing here, distributed system with horizontal scaling, as we will see, can be easier to maintain, and more capable of handling large amount of data, because there is only so much you can do with a single node.

Lastly, there is no one-size-fits-all magic sauce solution / architecture for scaling a system. The problem maybe access pattern, complexity of the data, response time requirements and often, a mixture of all above. You need to build it around assumptions on operations that you care more about, and do sensible trade-off.

### Maintainability

It is well known that the major cost of software development does not lies in initial development but rather ongoing maintenance --- keeping system up and running, fixing bugs, introducing new features, adapting to new platforms, repaying technical debts, etc... A system that fall behind in maintainability is commonly called a *legacy system*. People hate to maintain such system because it involves fixing other people's mistakes, hacking around a limitation, forcing it to do things it was not originally designed for etc... We should design and write our system in a way that avoid these problem, we will pay close attention to three principles: ***operability***, ***simplicity*** and ***evolvability***.

***Operability:*** Make it easy to keep the system up and running smoothly without hassle. Some suggests that good operations and work around bad software but good software can't run with bad operations. While we should automate as many process as we can, there are areas that ultimately requires human intervention. Examples includes monitoring, tracking down bugs, and applying security patches. When designing our system, we should provide visibility into the system, good documentation, minimize dependencies, sensible defaults and allow overrides etc...

**Simplicity:** Make it easy for new developer to understand the system. A complex system makes code harder to debug, harder to estimate story and harder to repay technical debt. Common symptoms includes tangled dependencies, tightly coupled modules, and inconsistent naming etc... Making a system simple does not mean to reduce functionalities, it just means that we should design our system in a way where functionalities are framed within the right position. A great tool we have is abstraction, a good abstraction can separates concerns, making it easy for developer to achieve more with just the relevant part of the code. For examples, CPU and Internet protocol suite are great abstraction.

**Evolvability:** Make it easy to add new features, and anticipate changes. It is highly unlikely that a system will remain un-change throughout its lifetime, not just in term of functionalities, but also external factors such as regulation, moving to new platform and adapting to new users. In term of organization processes, ***agile*** set a framework for moving quickly and iteratively.

### Summary

In this article, we looked at data intensive application in three perspectives: ***reliability***, ***scalability*** and ***maintainability***. Application has to meet various requirements in order for it to be useful to a wide range of users. There is no simple answer and each of them, satisfying requirements  takes hard work and many cases we need to trade-off instead. However, certain kinds of patterns and tools can help us effectively achieving these goals.

## Company Stories

### Netflix

Netflix has a system called Chaos Monkeys, it can randomly shutdown production instances to test ability to survive such event.

### Twitter

There were two main operations at Twitter that matter most - post tweets and home timeline

1. post tweets - user can publish new messages to their subscribers
2. home timeline - user can view messages from users he follows

Data published by twitter at 2012 shows that post tweet is around 4.6k requests per second, peaked over 12k requests per seconds and home timeline is around 300k per seconds. At first, twitter handles it like following:

- post tweets - single append to the database, indicate user has post a tweet
    home timeline - aggregate and sort all tweets posted by users the current user followed

    ```sql
    select tweet.*, user.* from user
    inner join tweet on tweet.userId = user.id
    inner join follow on follow.followeeId = user.id
    where follow.followerId = currentUserId
    ```

Soon it is obvious that user home timeline is loading too slowly, so Twitter switch to second approach.

- post tweets - writes to all its followers' home timeline cache
    home timeline - simply loads the cache

On average, a user is followed by 75 users, so write tweet operation becomes 4.6k*75=345k requests / seconds. In extreme case, one user can have more than 30 million followers, this makes that one tweet to have 30 millions write requests! Doing this in timely manner is a challenge given Twitter tries to delivery new messages to his followers within 5 seconds.

A final twist is Twitter ended up going for a hybrid approach, most users goes with hybrid approach and small amount of users with large amount of followers is fetched separately, and merged together [[5]][https://www.infoq.com/presentations/Twitter-Timeline-Scalability/].

### Amazon

Amazon measures latency in p99.9, the requests that takes longest time often carry the most data --- there is where the most valuable customers are [[3]][https://dl.acm.org/doi/10.1145/1323293.1294281]. But it was also deemed too expensive to optimize for p99.99. It was also observed that a 100ms increase in response time increases sales by 1% [[4]][https://glinden.blogspot.com/2006/12/slides-from-my-talk-at-stanford.html].
