# Data Intensive Applications

Many applications today are *data-intensive*, rather than *compute-intensive*. As CPUs are getting stronger every day, it is rarely a limitation, bigger problems are usually the amount of data, complexity of the data, and the speed it changes. For example, many systems are needed to store, stream / batch process, and cache data they received or owned.

If this sounds too obvious, it is because most of the time we just use data systems without thinking much. We do not write a storage engine from scratch when we need to use one, because databases are a perfect fit for the job.

> The Internet was done so well that most people think of it as a natural resource like the Pacific Ocean, rather than something that was man-made. When was the last time a technology with a scale like that was so error-free?
>
> --- Alan Kay, in interview with *Dr Dobb's Journal*.

Today's data systems are built on top of many other systems and abstractions, there is no one-fits-all solution, each system is specifically designed to solve its problem well. There are many factors influencing the design decision and trade-off of systems, including skills and experience of the people involved, legacy systems dependencies, time-scale for delivery, risk tolerance, etc... In this series, we focus on three concerns that are important for most systems:

- ***Reliability*** The system should continue to work correctly upon faults.
- ***Scalability*** The system should be able to deal with growth such as data, traffic and complexity.
- ***Maintainability*** New engineers should be able to maintain, learn and implement new features productively.

All of the above concerns are only valid to a reasonable extent, for example, in order to allow your system to work reliably when our planet Earth is swallowed by a black hole, you have to deploy your system on another planet --- good luck getting the budget approved!

### Reliability

You might say why don't we prevent faults instead of trying to tolerate them? The answer is simple --- we cannot guarantee faults from not happening, for example, a common hard disk has a mean time to failure of about 10-50 years, if you have a cluster of 10,000 disks, you should expect an average of one disk to die per day [[ref]](https://www.backblaze.com/blog/backblaze-drive-stats-for-2023/). Over the years, we have used many techniques such as RAID, dual power supplies, and hot-swappable CPUs. These methods are well understood and can keep a machine running uninterrupted for years.

**Hardware:** Redundancy of hardware components is sufficient for most applications, but as data volumes increase, many applications have begun using a larger number of machines, which proportionally increases the rate of hardware faults. Multi-machine set-up can also have the operational advantage of eliminating planned downtime with rolling upgrades.

**Software:** There can also be software faults due to insufficient testing, improper error handling, leaked resources and cascading failures. There are no quick answers but things to keep in mind are (1) **simple tests are able to prevent most of the critical errors** [[ref]](https://cs.brown.edu/~ugur/fits_all.pdf), (2) sufficient monitoring, and isolation can provide a certain degree of guarantee.

**Human Error:** Lastly there can also be human errors such as misconfiguration. Humans are always unreliable, we should design a system that has minimal opportunities for humans to make errors, decouple error-prone areas of the code, add detailed monitoring, and logs and allow easy recovery. Note that as the amount of data increases, corner cases will start to appear more often and can potentially become an operational burden and go unmaintainable.

### Scalability

One common cause of performance degradation is increased load - user grows from 1 million to  10 million, concurrent users grow from 10 thousand to 100 thousand, system is under pressure to process much more data than usual. Scalability describes how well the system can scale with *load* increases, the term *load* is not a one-dimensional label, it can mean different things in different contexts, thus it is meaningless to simply say some system X does not scale.

We can quantify scalability in terms of a group of *load parameters*, Examples of such parameters include response latency, throughput per second, cache hit rate, read and write requests to database, etc... The best choice of the parameters depends on what matters most to your system, perhaps you care more about the average case. Perhaps a small number of extreme cases is the main bottleneck.

In a batch processing system like Hadoop, you probably care about ***throughput*** --- the number of records processed per second; In an online system, you probably care more about ***response time*** and ***latency***.

> Response time and latency are often confused. Response time is what the end-user sees, how long the server sends a response back; whereas latency measures the time a request waits to be processed.

**Response time is a distribution rather than some discrete number**, you get different response times even if you send the same request multiple times with a short interval. Beware that *average* over a distribution is not a good metric, it does not really tell you how many users are actually experiencing that value. Usually, it is better to use ***percentile***, *median* is 50th percentile, abbreviated as *p50*, if your *p50* is 200ms, then you know half of your requests return under 200ms. Outliners are measured in higher percentiles such as p99 and p99.9.

In a system that processes multiple requests in parallel, the tail percentile becomes especially important, this is because all requests have to wait for the slowest request returns to complete. Therefore percentile is often used in Service Level Agreements (SLA) to define performance contracts.

We often talk about vertical and horizontal scaling, vertical scaling increases an individual node's capability and horizontal scaling increases the number of nodes. Generally, if you could get away with vertical scaling, go for it until it forces you to be distributed, scaling out a stateful system introduces you to a more error-prone solution, which makes your system harder to maintain. Most importantly, most real-world businesses do not need to process that much data. On the other hand, as tools and abstractions get better over time, going distributed becomes easy, and in terms of data-intensive application which is what we are focusing on here, a distributed system with horizontal scaling, as we will see, can be easier to maintain, and more capable of handling large amount of data, because there is only so much you can do with a single node.

Lastly, there is no one-size-fits-all magic sauce solution for scaling a system. The problem may be access pattern, the complexity of the data, response time requirements and often, a mixture of all the above. You need to build it around assumptions on operations that you care more about and do sensible trade-offs.

### Maintainability

It is well known that the major cost of software development does not lie in initial development but rather ongoing maintenance --- keeping the system up and running, fixing bugs, introducing new features, adapting to new platforms, repaying technical debts, etc... A system that falls behind in maintainability is commonly called a *legacy system*. People hate to maintain such systems because it involves fixing other people's mistakes, hacking around a limitation, forcing it to do things it was not originally designed for etc... We should design and write our system in a way that avoids this problem, we will pay close attention to three principles: ***operability***, ***simplicity*** and ***evolvability***.

***Operability:*** Make it easy to keep the system up and running smoothly without hassle. Some suggest that good operations work around bad software but good software can't run with bad operations. While we should automate as many processes as we can, there are areas that ultimately requires human intervention. Examples include monitoring, tracking down bugs, and applying security patches. When designing our system, we should provide visibility into the system, good documentation, minimize dependencies, sensible defaults allow overrides etc...

**Simplicity:** Make it easy for new developers to understand the system. A complex system makes code harder to debug, harder to estimate story and harder to repay technical debt. Common symptoms include tangled dependencies, tightly coupled modules, inconsistent naming etc... Making a system simple does not mean to reduce functionalities, it just means that we should design our system in a way where functionalities are framed within the right position. A great tool we have is abstraction, a good abstraction can separate concerns, making it easy for a developer to achieve more with just the relevant part of the code. For example, CPU and Internet protocol suites are great abstractions.

**Evolvability:** Make it easy to add new features, and anticipate changes. It is highly unlikely that a system will remain unchanged throughout its lifetime, not just in terms of functionalities, but also in external factors such as regulation, moving to a new platform and adapting to new users. In terms of organization processes, ***agile*** sets a framework for moving quickly and iteratively.

### Summary

In this article, we looked at data-intensive applications in three perspectives: ***reliability***, ***scalability*** and ***maintainability***. Application has to meet various requirements in order for it to be useful to a wide range of users. There is no simple answer and for each of them, satisfying requirements  takes hard work and in many cases, we need to trade-off instead. However, certain kinds of patterns and tools can help us effectively achieve these goals.

## Appendix

### Netflix

Netflix has a system called Chaos Monkeys, it can randomly shut down production instances to test the ability to survive such events.

### Twitter

There were two main operations at Twitter that matter most - post tweets and home timeline

1. post tweets - The user can publish new messages to their subscribers
2. home timeline - The user can view messages from users he follows

Data published by Twitter in 2012 shows that post tweets are around 4.6k requests per second, peaked at over 12k requests per second and home timeline is around 300k per second. Twitter was handling it like the following:

- post tweets - single append to the database, indicating user has posted a tweet
- home timeline - aggregate and sort all tweets posted by users the current user followed when an update is needed

    ```sql
    select tweet.*, user.* from user
    inner join tweet on tweet.userId = user.id
    inner join follow on follow.followeeId = user.id
    where follow.followerId = currentUserId
    ```

Then they switched to a second approach:

- post tweets - writes to all its followers' home timeline caches
- home timeline - loads from the cache

On average, a user is followed by 75 users, so the one write tweet operation becomes 4.6k*75=345k requests/seconds. In extreme cases, one user can have more than 30 million followers, this makes that one tweet have 30 million write requests! Doing this in timely manner is a challenge given Twitter tries to deliver new messages to its followers within 5 seconds.

Twitter ended up going for a hybrid approach, most users go with the second approach and a small amount of users with a large amount of followers are fetched separately and results are merged [[ref]](https://www.infoq.com/presentations/Twitter-Timeline-Scalability/).

### Amazon

Amazon measures latency in p99.9, the requests that take the longest time often carry the most data --- there is where the most valuable customers are [[ref]](https://dl.acm.org/doi/10.1145/1323293.1294281). But it was also deemed too expensive to optimize for p99.99. It was also observed that a 100ms increase in response time increases sales by 1% [[ref]](https://glinden.blogspot.com/2006/12/slides-from-my-talk-at-stanford.html).
