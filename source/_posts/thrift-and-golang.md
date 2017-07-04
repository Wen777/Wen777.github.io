---
title: Thrift Quickstart
date: 2016-09-21 19:16:12
desc:
tags:
---

# WHAT IS Thrift

{% blockquote Apache Thrift -- https://thrift.apache.org/ %}
The Apache Thrift software framework, for scalable cross-language services development...
{% endblockquote %}

<!--more-->

Thrift is one of most powerful RPC frameworks all over the world, supported by Facebook. It supports many languages like C++, JAVA, Python, Golang, etc. Once you create the Thrift file, it can generate basic structure of code in different languages by Thrift file.

# WHY WE CHOOSE Thrift

Thrift provide several useful features:

* Compressed data
* Once a developer creates a Thrift file, the file defines the way that different languages can follow to share data across the limitation of languages which is a benefit for people who are working on several micro-service projects.
* [The typical use case is for building distributed systems, mostly in house.](http://www.doublecloud.org/2014/01/apache-thrift-hello-world-sample/)

# HOW TO USE Thrift in Golang

#### Requirements:

* Installed Thrift
* Thrift file(s)
* Choose a programming language which supports Thrift.

#### IDEA:

The following is a simple example which shows the architecture. We are going to create a command line tool and a Thrift daemon server.
To do some simple computing on the daemon server, we will use cmd to send the task to the daemon server by RPC.

```text
*Diagram of whole project*

┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
                    Clients(cmd)       
└ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
                        ▲
                        |
                        │
                        ▼
┌───────────────────────────────────────────────┐
│                    RPC(TCP)                   │
└───────────────────────────────────────────────┘
┌───────────────────────────────────────────────┐
│                  Daemon server                │
└───────────────────────────────────────────────┘
```

#### User Case

*METHOD*

* PING: Check the connection of the daemon server and the status of the Thrift daemon server.
* ADD: Return the sum of input.
* MINUS: Return the result of computing.
* MULTIPLY: Return the result of computing.
* DIVISION: Return the result of computing.

*PARAMETER*

* INPUT: Array, optional.
* OUTPUT: error, res.

#### Thrift File

```thrift
namespace go swhsiang.computing

struct StatusOfService {
    1: required string version;
    // Listening which port
    2: required string network;
}

struct InputOfComputing {
    1: required list<i32> num_arr;
}

struct OutputOfComputing {
    1: required string error;
    2: optional i32 res;
}

service Computing {
    StatusOfService ping(),
    OutputOfComputing compute(1:InputOfComputing input);
}

```

#### IMPLEMENTATION

[Github Repo](https://github.com/swhsiang/go-thrift-cmd)

# Reference

* [Thrift: The Missing Guide](https://diwakergupta.github.io/thrift-missing-guide/)
