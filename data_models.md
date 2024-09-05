# Data Models

> The limit of my language means the limit of my world --- Ludwig Wittgenstein, Tractatus Logico-Philosophicus (1922)

Data models are perhaps the most essential part of developing software. When you are given a problem, you often need to implicitly map real world objects of that problem into the computer objects that you can work with. A good data models can be very flexible and allow you to easily construct them and start working on it. On the other hand, we also need to trade flexibility versus efficiency because often what is simple for human is not easy for machine to interpret.

At the same time, data models connects different systems together. As a developer, you model real world objects into computer objects; these objects are then translated into specific storage format such as `json` and `xml`; the database engineers will need to layout these data format in term of blocks or bytes in the memory; and on even lower levels, hardware engineers represents bytes in term of electrical signals, lights, magnetic fields and more.

### SQL

Today, the most popular data model is SQL, which describe data in terms of relations. Since its birth during 1980s, it has grown to be the most popular data model for 20-30 years. For a relatively new field like computer science, 20-30 years is like an eternity. The reason for its widespread of popularity is because its ease of use [[ref]][1]. Other data model like the network model and hierarchical model requires engineers to think about the underlying database implementation, for example, hierarchical model organize data into tree-like structure and requires developer to remember how to navigate within the tree, changes to parent can introduce logical impairs to engineers' mental models. These competitors generated a lot of hypes in its time but never lasted.

Although SQL is popular. but it suffers from a number of problem in practice. Most application today is done in object-oriented programming language, this means data models are represented as object in application code. This is different from relational model where data is represented in terms of rows and columns, this means an awkward translation layer is required to translate both terms. This is sometimes called *impedance mismatch*. Object Relation Mapping (ORM) framework like ActiveRecord and Hibernate reduces boilerplates but cannot completely hide the details and can introduce unnecessary learning overheads.

SQL allows database to store data efficiently and allow engineers to query for data exactly as needed using SQL. However, people find it inconvenient to have an translation layer between application code and the database. I think its completely fine to have a translation layer, although it is more code, but it allows maximum flexibility and performance. Many tools building on top of this translation layer advertise they bring additional engineering efficiency benefits, but often they also bring unnecessary complexity and learning cost. They are also somewhat afterthought, not what the SQL is originally intended for.

### NoSQL

Recently in 2010s, NoSQL is the latest and most promising attempt to overthrow relational model's dominance. I see NoSQL as a remote HashMap with additional features, and with this simpler design, it offers three main benefits

- Greater scalability.
- Frustration over restrictiveness of SQL schemas.
- Specialized queries.

With this simpler model, we cannot easily model many-to-one and many-to-many relationship, and generally weak support for joins, we will need to emulate these by making multiple queries. But wait, isn't it making things very inefficient? Aren't we repeating history by making an overly simplified assumption? The answer turns out to be, no.

Remember why relational models got more prominent than network model? It is because its ease of use, we all know network model is the most natural, but relational model open up all its data which made it easier to query, and people liked it. Similarly, document model gave a simpler mental model, and it is very efficient on its own. Sure, data might grow and eventually interconnect, hard to manage, but if you know it is not going to be huge problem, you can make a trade off. It is always a trade off depending on your business context. You do not get more complicated application code by choosing a document database if you are not dealing with too many many-to-many and many-to-one relationship.

### Graph

The amount of many-to-many relationships plays a huge part in deciding what data model to use --- if you have mostly one-to-many relationships, the document model is most appropriate; if you have many many-to-many relationships, the relational model is appropriate. But as connections becomes more complex, it becomes natural to model data as a graph, for example, social networks, web, or road networks.

Graphs are presented using vertices and edges, many well-known algorithms such as *PageRank* and *dijkstra* can operate on this model.

### Summary

Data model is a huge subject and we only took a brief look at each of them in this article. Historically, data started laying out as hierarchical model, but it was not good at modelling many-to-many relationships, so relational model was invented; more recently, people finds some applications does not  fits well in relational databases, thus NoSQL is born and diverged into document model and graph model. 

All three models are good in different ways, and they are all widely used, today, an application often involves using multiple data models, this idea is sometimes called *polyglot persistence*.



[1]: https://www.seas.upenn.edu/~zives/03f/cis550/codd.pdf
