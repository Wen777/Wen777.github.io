---
title: Dive into rqlite
date: 2016-09-14 12:18:12
desc: rqlite is an implementation of Raft consensus protocol which based on SQLite.
tags:
---

## Structure Of This Post:

* Purpose.
* Explanation of terms.
* Dive into source code.

## WHY

Distributed computing is a hot topic of computer science. Studying any implementation of real projects is a good start.

## WHAT

Before we start to dive into source code of [rqlite](https://github.com/rqlite/rqlite), we need to understand basic concepts of this project.

### Distributed Computing

* Description:
{% blockquote Wikipedia -- https://en.wikipedia.org/wiki/Distributed_computing Distributed Computing %}
***Distributed Computing*** is a field of computer science that studies distributed systems.

In distributed computing, a problem is divided into many tasks, each of which is solved by one or more computers,[3] which communicate with each other by message passing.

Three significant characteristics of distributed systems are: concurrency of components, lack of a global clock, and independent failure of components. Examples of distributed systems vary from SOA-based systems to massively multiplayer online games to peer-to-peer applications.
{% endblockquote %}

### Raft

* [Home page](https://raft.github.io/)

* Description:
{% blockquote Wikipedia -- https://en.wikipedia.org/wiki/Raft_(computer_science) Raft %}
***Raft*** is a consensus algorithm designed as an alternative to [Paxos](http://bit.ly/2cHR8M8). It was meant to be more understandable than Paxos by means of separation of logic, but it is also formally proven safe and offers some new features.

Raft offers a generic way to distribute a state machine across a cluster of computing systems, ensuring that each node in the cluster agrees upon the same series of state transitions
{% endblockquote %}


### SQLite

* [Home page](https://www.sqlite.org/)

* Description:
{% blockquote Wikipedia -- https://en.wikipedia.org/wiki/SQLite SQLite %}
***SQLite*** is a relational database management system contained in a C programming library. In contrast to many other database management systems, SQLite is not a client–server database engine. Rather, it is embedded into the end program.

SQLite is ACID-compliant and implements most of the SQL standard, using a dynamically and weakly typed SQL syntax that does not guarantee the domain integrity.[5]

SQLite is a popular choice as embedded database software for local/client storage in application software such as web browsers. It is arguably the most widely deployed database engine, as it is used today by several widespread browsers, operating systems, and embedded systems (such as mobile phones), among others.[6] SQLite has bindings to many programming languages.
{% endblockquote %}


## HOW (The architecture of SQLite)
