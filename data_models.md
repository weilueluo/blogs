# Data Models

> The limit of my language means the limit of my world --- Ludwig Wittgenstein, Tractatus Logico-Philosophicus (1922)

Data models are perhaps the most essential part of developing software. When you are given a problem, you often need to implicitly map real world objects of that problem into the computer objects that you can work with. A good data models can be very flexible and allow you to easily construct them and start working on it. What is good for human to interpret can be difficult for machine to interpret, it is rare that we have both aligned, thus many problems we will see in following sections are about trade-offs between flexibility and efficiency.

Data models connects different systems together. As a developer, you model real world objects into computer objects; these objects are then translated into specific storage format such as `json` and `xml`; on a lower level, the database engineers will need to layout these data format in term of blocks or bytes in the memory; and on even lower levels, hardware engineers represents bytes in term of electrical signals, lights, magnetic fields and more.

### SQL

Today, the most popular data model is SQL, which describe data in terms of relations. Since its birth during 1980s, it has grown to be the most popular data model for 20-30 years. For a relatively new field like computer science, 20-30 years is like an eternity. The reason for its widespread of popularity is because its ease of use [[ref]](https://www.seas.upenn.edu/~zives/03f/cis550/codd.pdf). Other data models like the network model and hierarchical model require developers to think too much about the underlying database implementation, hurting readability, for example, hierarchical model organize data into tree-like structure and requires developer to remember how to navigate within the tree, changes to parent can introduce logical impairs to engineers' mental models., hindering developer experience. Thus these competitors generated a lot of hypes in its time but never lasted.

Although SQL is popular. but it suffers from a number of problem in practice. Most applications today are done in object-oriented programming language, this means data models are represented as object in application code. This is different from relational model where data is represented in terms of rows and columns, this means an awkward translation layer is required to translate both terms. This scenario is sometimes called *impedance mismatch*. Object Relation Mapping (ORM) frameworks like ActiveRecord and Hibernate are useful for reducing boilerplates but cannot completely hide the details and can introduce unnecessary learning overheads.

SQL allows database to store data efficiently and allow engineers to query for data exactly as needed using SQL. However, people find it inconvenient to have an translation layer between application code and the database. I think its completely fine to have a translation layer, although it is more code, but it allows maximum flexibility and performance, it is also a good practice to encapsulate database related operations into another abstraction, in case you want to change your database in the future. Many tools building on top of this translation layer advertise they bring additional engineering efficiency benefits, but often hides the fact that they also adds unnecessary complexity and learning cost for developers. Moreover, they are also somewhat afterthought, not what the SQL is originally intended for, but manually writing all the SQL queries is too painful and these frameworks are well accepted.

### NoSQL

Recently in 2010s, NoSQL is the latest and most promising attempt to overthrow relational model's dominance. NoSQL is like a remote HashMap with additional features, and with this simpler design, it offers three main benefits compared to traditional SQL models.

- Greater scalability.
- Frustration over restrictiveness of SQL schemas.
- Specialized queries.

With this simpler model, we cannot easily model many-to-one and many-to-many relationship, and we generally gets weaker support for joins, we will need to emulate these by making multiple queries. But it turns out some applications have little or zero many-to-many relationships, so NoSQL found its niche in the market.

Relational models got more prominent than network model because its ease of use, we all know network model is the most natural, but relational model open up all its data which made it easier to query, and people liked it. Similarly, document model gave a simpler mental model, and it is very efficient on its own. Sure, data might grow and eventually interconnect, hard to manage, but if you know it is not going to happen frequently, you can make a trade off. You do not simply get more complicated application code by choosing a document database, you have to choose your database depending on the business context.

### Graph

As we saw, the amount of many-to-many relationships plays a huge part in deciding what data model to use --- if you have mostly one-to-many relationships, the document model is most appropriate; if you have many many-to-many relationships, then relational model is appropriate. But as connections becomes more complex, it becomes natural to model data as a graph, for example, social networks, web, or road networks.

Graphs are presented using vertices and edges, many well-known algorithms such as *PageRank* and *dijkstra* can operate on this model, making it appealing in specific scenarios.

### Summary

Data model is a huge subject and we only took a brief look at each of them in this article. Historically, data started laying out as hierarchical model, but it was not good at modelling many-to-many relationships, so relational model was invented; more recently, people finds some applications does not  fits well in relational databases, thus NoSQL is born and diverged into document model and graph model. All three models are good in different ways, and they are all widely used, today, an application often involves using multiple data models, this idea is sometimes called *polyglot persistence*.

