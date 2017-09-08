---
title: In-memory NoSQL written in Go Part 1
date: 2017-09-08 21:54:30
desc:
tags:
---

![database](http://www.dataversity.net/wp-content/uploads/2012/10/NoSQL.png)
@[credit](http://www.dataversity.net/the-nosql-movement-what-is-it/)

# WHAT's NoSQL ?

> A NoSQL (originally referring to "non SQL" or "non relational")[1] database provides a mechanism for storage and retrieval of data that is modeled in means other than the tabular relations used in relational databases.

From [Wiki](https://en.wikipedia.org/wiki/NoSQL)

<!-- more -->

# Motivation

MongoDB is the first NoSQL database I've used. Back to 2014, there was a promising web framework called [Meteor](https://www.meteor.com/) and Meteor natively supports MongoDB as its database. Because the two startups that I worked for chose Meteor as the web framework to build products, I got more understandings about NoSQL. As a result, I start wondering how to implement a NoSQL database.

After I read a [blog post](https://jeffknupp.com/blog/2014/09/01/what-is-a-nosql-database-learn-by-writing-one-in-python/) that tells how to implement an in-memory database in Python, I decide to use Go to write an in-memory database for practice.

------

# Structure of This Post

* Part 1:
    * [Implementation](#implementation)
        * [Constraints](#constraints) 
        * [Spec](#spec)
* Part 2:
    * Implementation
        * Explanation of Code

------

# <a name="implementation">Implementation</a>

***[Code](https://github.com/swhsiang/gone)***

## <a name="constraints">Constraints</a>

* An in-memory database written in `Go`
* Use `map` as the storage to keep data
* Only use `string` type key as index
* Support the two types value, integer and string
* A TCP/IP server supports use ASCII string
* Support commands: GET, PUT, DELETE

## <a name="spec">Spec</a>

### Supported Commands

* PUT
    * KEY, VALUE
    Add new entity

* GET
    * KEY
    Read an entity

* DELETE
    * KEY
    DELETE an entity

### Message Structure

#### Components

A message must contain `COMMAND` and `KEY` two fields. Three smicolon signs are required but `VALUE` and `VALUE TYPE` are optional which depend on the `COMMAND`.

```
COMMAND; [KEY]; [VALUE]; [VALUE TYPE]
```

#### Examples

* `PUT; foo; 1; INT`

* `GET; foo;;`

* `DELETE;foo;;`
