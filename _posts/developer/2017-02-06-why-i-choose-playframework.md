---
layout: single
title: Why I choose Play Framework
read_time: true
category: developer
tags: [play framework, java, scala, akka, reactive programming]
header:
    teaser: /assets/images/posts/developer/play-framework/play-logo.png
comments: true
author_profile: false
share: true
related: true
permalink: /:categories/:year-:month-:day-:title/
excerpt: My views about the play framework. A developer friendly, solid and fast framework built for web and mobile that can scale massively. 
published: true
---

The ***Play framework*** is a web framework for the JVM that breaks away from the Servlet Specification and JEE best practices those were in use for a while.
Play inherits a fully reactive programming model through the use of **Futures** for asynchronous programming and **Akka** for simplifying concurrency at its best.
Besides the advantages over the so-called Servlet based web frameworks, Play has some extreme advantages. It has full support for two of the most popular 
languages on JVM, ***Java and Scala.***

## Reactive, Stateless, asynchronous and non-blocking under the hood
Every web framework does essentially the same job of processing the HTTP request. Traditionally, web frameworks assign a new request to a thread, 
which contains the book keeping information needed for a CPU core to execute instructions  and the thread processes the request until it sends a 
response back to the client. This is inefficient for web systems because servicing a typical request requires blocking the thread for a number of 
tasks like database transactions, external web service calls, etc. 

Play has a **stateless** design, meaning it does not rely on server-side session state. Instead, session state is kept in a cookie and is therefore available to any server node that receives a request.
It supports **asynchronous** requests, which does not require you to tie up a thread while requests are in progress. Play uses work stealing for scheduling the tasks that comes with each request, 
and is implemented using [ForkJoin Framework (JSR-166)](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html){:target="_blank"}.

> When a HTTP request gets into Play:
1. The server calls `Global.onRouteRequest(rh: RequestHeader): Handler`
2. Most of the Time this Handler is actually an `EssentialAction`
3. An `EssentialAction` is just a function `(RequestHeader) => Iteratee[Array[Byte], Result]`.
4. This functions are called, and the resulting `Iteratee` is feed with chunks `(Array[Byte])` from the request body.
5. Eventually, you get a `Result`, which is sent back to the client.

## Rapid Code and Test Cycle
For developers, a killer Play feature is its ability to dynamically recompile the application when they press "reload" in the browser.
If there are compilation errors, they show up right in the browser. If the build succeeds, the new page shows up. Play uses sophisticated incremental compilation techniques to rebuild only those classes that have changed.

Lets have a look at what it does:
```sh
$ sbt run
```
On starting the application in `DEV` mode by issuing the run command,
- It creates a common class loader, to load those `JARs` that are used both by your application and sbt. They won't be reloaded.
- Creates the application class loader, child of the common class loader, which loads all application dependencies.
- Each time a request hits the server, the application calls the reloader, asking to check if there's any code changes. The reloader will then delegate recompilation (or other necessary tasks, like copying static assets) to SBT.
- On successful compilation, play removes the old application classloader, and create a new one with the updated classes.
- Then it restarts the application, without restarting the JVM.

The stateless behaviour of Play makes this possible without worrying about users' session data.

## Powerful build system
Play uses [SBT](http://www.scala-sbt.org/){:target="_blank"} as its build tool. SBT is the de facto build tool for Scala and is increasingly accepted in the Java community as well.
SBT makes life much easier with play and one can build, package and run applications with minimal efforts.

### Create a new application using SBT
#### Play Java Seed
```sh
  $ sbt new playframework/play-java-seed.g8
```  
#### Play Scala Seed
```sh
  $ sbt new playframework/play-scala-seed.g8
```

We can choose from a list of [starter projects](https://playframework.com/download#starters){:target="_blank"} too.

## Proven in production
Play has proven its power across various production systems. Some of the major players who found Play as a framework for success are:
- Linkedin
- Samsung
- Verizon
- Walmart Canada and counting...


Play is a full stack framework for modern Web applications with a NIO-based development and production servers, full support for MVC, 
a persistence engine, a fully integrated testing framework (unit testing and functional testing), a powerful async Web Service client, an async-based job manager, multiple extensions 
are available through modules, support for full customization on the framework behavior through its plugin mechanism, dependency management, validation framework and even more advanced 
features such as WebSockets. It can be a great relief for developers who want to be more productive. 