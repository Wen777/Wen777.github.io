---
title: Dive into rqlite 1
date: 2016-09-14 12:18:12
tags: [raft, distributed_computing]
desc: rqlite is an implementation of Raft consensus protocol which based on SQLite.
---

## Structure Of This Post:

* Purpose.
* Explanation of terms.
* Dive into source code.

## WHY

Distributed computing is a hot topic of computer science. Studying any implementation of real projects is a good start. We can learn how to organize a project and how to integrate different libraries, packages, and algorithms into one project.

<!--more-->

## WHAT

Before we start to dive into the source code of [rqlite](https://github.com/rqlite/rqlite), we need to understand basic concepts of this project.

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
{% blockquote Philip O'Toole -- http://www.slideshare.net/PhilipOToole/rqlite-replicating-sqlite-via-raft-consensu Raft %}
***Raft*** is a distributed consensus protocol.

Such protocol are used to ensure multiple different nodes-server-always agree on a given set of values.
{% endblockquote %}

### SQLite

* [Home page](https://www.sqlite.org/)

* Description:
{% blockquote Wikipedia -- https://en.wikipedia.org/wiki/SQLite SQLite %}
***SQLite*** is a relational database management system contained in a C programming library. In contrast to many other database management systems, SQLite is not a client–server database engine. Rather, it is embedded into the end program.

SQLite is ACID-compliant and implements most of the SQL standard, using a dynamically and weakly typed SQL syntax that does not guarantee the domain integrity.[5]

SQLite is a popular choice as embedded database software for local/client storage in application software such as web browsers. It is arguably the most widely deployed database engine, as it is used today by several widespread browsers, operating systems, and embedded systems (such as mobile phones), among others.[6] SQLite has bindings to many programming languages.
{% endblockquote %}


## HOW (The architecture of RQLite)

What's the architecture of this project?

---Diagram comes from the documentation of rqlite---

```{text}
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐    ┌ ─ ─ ─ ─ ┐
            Clients                   Other
└ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘    │  Nodes  │
               │                    ─ ─ ─ ─ ─
               │                        ▲
               │                        │
               │                        │
               ▼                        ▼
┌─────────────────────────────┐ ┌───────────────┐
│           HTTP(S)           │ │      TCP      │
└─────────────────────────────┘ └───────────────┘
┌───────────────────────────────────────────────┐
│             Raft (hashicorp/raft)             │
└───────────────────────────────────────────────┘
┌───────────────────────────────────────────────┐
│               matt-n/go-sqlite3               │
└───────────────────────────────────────────────┘
┌───────────────────────────────────────────────┐
│                   sqlite3.c                   │
└───────────────────────────────────────────────┘
┌───────────────────────────────────────────────┐
│                 RAM or disk                   │
└───────────────────────────────────────────────┘
```


---The structure of code of RQLite.---

```
├── auth
│   ├── credential_store.go
│   └── credential_store_test.go
├── CHANGELOG.md
├── circle.yml
├── cluster
│   ├── service.go
│   └── service_test.go
├── cmd
│   ├── rqlite
│   │   ├── execute.go
│   │   ├── main.go
│   │   ├── query.go
│   │   └── README.md
│   └── rqlited
│       └── main.go
├── CONTRIBUTING.md
├── db
│   ├── db.go
│   └── db_test.go
├── doc
│   ├── BACKUPS.md
│   ├── CLI.md
│   ├── CLUSTER_MGMT.md
│   ├── CONSISTENCY.md
│   ├── DESIGN.md
│   ├── DIAGNOSTICS.md
│   ├── README.md
│   └── SECURITY.md
├── doc.go
├── gen_artifacts.sh
├── gofmt.sh
├── http
│   ├── service.go
│   └── service_test.go
├── LICENSE
├── package.sh
├── README.md
├── store
│   ├── store.go
│   └── store_test.go
├── system_test
│   ├── cluster_test.go
│   ├── helpers.go
│   └── single_node_test.go
├── tcp
│   ├── doc.go
│   ├── mux.go
│   └── mux_test.go
├── Vagrantfile
└── vagrant_setup.sh
```

# Conclusion

The above texts are the basic information of RQLite. Next post, we will focus on [hashicorp/raft
](https://github.com/hashicorp/raft). Understand how to use the library and how to integrate with SQLite.

# Reference:

* [rqlite Replicating SQLite via Raft Consensu](http://www.slideshare.net/PhilipOToole/rqlite-replicating-sqlite-via-raft-consensu)
* [SQLite use case](https://sqlite.org/whentouse.html)
