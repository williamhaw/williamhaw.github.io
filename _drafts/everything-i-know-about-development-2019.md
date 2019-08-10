---
layout: post
title: Everything I Know About Development in 2019
category: ''
tags: []

---
I'm starting this series so that I can track how far I've gone in my career as an engineer.

In true [Dan Abramov](https://overreacted.io/things-i-dont-know-as-of-2018/) style I'll be listing what I **don't** know as well, so that I can concentrate on learning those things in the future.

<!--excerpt-->

# Java

I started nearly four years ago as the first engineering hire in a tiny [startup](http://clickdrive.io/) mainly running Java in the cloud and in our vehicle IOT solution. I still think that the stuff I worked on there was really interesting, and in many cases I was the main contributor to the features of our solution.

Those include:
- Building a diff-based package manager using [Merkle trees](https://en.wikipedia.org/wiki/Merkle_tree) to automate deployments to our devices over the air while minimizing bandwidth usage over 4G.
- Implementing a message queue protocol over UDP to reliably send streaming data to our cloud servers.
- Hacking car CAN bus networks and reverse engineering recorded messages.
- Implementing a video telemetry recording and playback app for a client to monitor and gice feedback to driving students and instructors.

## Know

* Core Java
  - Data Structures
    - Basic data structures and when to use the generic interface rather than the concrete type.
      - Lists (ArrayList, LinkedList)
      - Sets (HashSet, TreeSet)
      - Maps (HashMap, TreeMap)
    - Generics
  - Key Classes and Concepts
    - Comparators 

      A generic way to compare two objects, used for sorting, binary trees, etc.
    - Date/DateTime
    - Exceptions/Errors

      Errors shouldn't be handled, Exceptions may be handled or passed up the call stack. I also consider checked exceptions(specifying handled exceptions in public methods) to be a mistake.
    - Enums
      - Use as reflection and serialization-safe singleton
  - The contract between hashcode() and equals()

    Basically, if two objects are equal, the hashcode must be the same. The converse does not apply.
  - Concurrency
    - Executor
      Why do thread pools exist? Creating threads is resource intensive due to allocating 1MB of stack per thread by default.
    - Runnables and Futures

      These data structures can wrap operations that execute asynchronously in an Executor.
    - Threads and Daemon Threads
      Daemon Threads are expected to live as long as the JVM is running.
    - Locking and Mutual Exclusion
      - wait(), notify() and notifyAll()
      - The synchronized keyword
      - Using objects as locks
      - ReentrantLock
      - ReadWriteLock
      - Condition await(), signal() and signalAll()
    - Thread-Safe Data Structures
      - BlockingQueue
      - ConcurrentMap
    - Double Checked Locking

      Unfortunately it's [not guaranteed to work](https://www.javaworld.com/article/2074979/double-checked-locking--clever--but-broken.html).
* Dependency Injection

  One part of dependency injection is writing your classes to accept their dependencies in the constructor / setup code. The other part is either wiring up the dependencies from the main() function or using a library/framework to magically wire them up.
* JVM basics
  - Garbage Collection
  - Profiling

## Don't Know

* Massive frameworks like Spring and OSGI

* ORMS (Hibernate, JPA?)

  To be honest, the companies I have worked for have not had a use for big, magic-heavy frameworks like Spring, but I have an idea to implement a REST API with Spring Boot with user accounts secured by Spring Security and backed by some database using Hibernate for a "real" use case, so I'm pretty excited to see how that will turn out.

* The Java Memory Model
  
  I feel like I've only scratched the surface of this mysterious beast and all the ways it can come bite me.

* Lambdas and Streams

  I did try to use lambdas before but was deterred because of the need to handle checked exceptions. Only recently I understood that best practice when using lambdas is to just throw RuntimeException. Also at the time the on-device code had to run on Java 7 as there had been some bugs with upgrading to Java 8 for ARM, so I didn't have much chance to use the newer language features.

# Scala

For the past year, I've been working at a [technology company in the travel industry](https://www.agoda.com), mainly using Scala to integrate with third party supplier APIs.

> A language that doesn't affect the way you think about programming, is not worth knowing.
> -<cite>Alan Perlis, [Epigrams on Programming](http://pu.inf.uni-tuebingen.de/users/klaeren/epigrams.html) #19, 1982</cite>

More and more it seems to me that Scala is one of those languages which has affected the way I think about programming. The biggest change is that types actually feel like my friend now, in that I can use the richer type system and constructs to automatically enforce many things that I would have to write code for in Java.

## Know

* Functional Programming
  - Monads
    - Using data structures that are lawful monads like Option and Seq
  - For-Comprehensions for that sweet syntactic sugar
  - Immutable data structures as default
  - 
* Play
  - Contributed to a couple of internal Play apps at work
* Akka
  - Basic knowledge of Actors and actor message passing
  - Scheduling within the ActorSystem
* Circe for JSON serialisation/deserialisation
* Scalaxb for generating case class models from XML and using typeclasses to serialise them
* Scalapb to generate code from Protobuf
  - Using includes to define new message from other protobuf libraries
* Unit testing
  
  To be honest, I only converted to the religion of automated testing after understanding how to use mocks effectively (then again, overuse of mocking is a code smell). Also, I like the way ScalaTest and Specs2 make a testing DSL out of Scala. (For e.g using `should()` to assert equality.)


## Don't Know

* Functional Programming
  - Not sure about when I should write my own monads
  - Monad Transformers (not sure when and why would I need these)
  - Tagless Final style
  - Effect libraries like ZIO and Monix
  - Functional programming libraries like cats and scalaz
  - Type safe database queries (doobie?)
* Akka
  - Actor hierarchies - I've heard one sticking point is how to implement backpressure effectively
  - Akka Cluster (not sure what problem this solves quite yet)

# Sysadmin/DevOps

## Know

* How to set up a Linux machine manually
* Configuration of Nginx
  - As a proxy
  - As a static file server
  - As a video streaming server
* Docker/Docker Compose

## Don't Know

# Databases

## Know

* DynamoDB

* SQL - currently working with SQL Server

## Don't Know

# Architecture/High Level Design

## Know

* Object Oriented Design
  - Abstraction
  - Encapsulation
  - Inheritance
  - Polymorphism

* Functional Design
  - Immutability
  - Pure Functions
  - First Class Functions
  - Recursion
    - Structural
    - Tail Recursion

* Design Patterns

  There are twenty three canonical design patterns, but I'll list the ones I've used before and therefore understand the best.

  - Creational
    - Factory
      
      When the caller doesn't need to control the creation of the returned object.
    - Builder

      When the caller needs granularity in creating the returned object.
    - Singleton 

      Singletons with mutable state are **NOT** a good idea.

  - Structural
    - Adapter
    - Facade

  - Behavioural
    - Chain of Responsibility
    - Command
    - Iterator
    - Observer 

      Common interview question: How is the Observer pattern different from Publish-Subscribe?
      Answer: The subject needs to know which listeners are listening as it keeps a list of listeners to trigger the event. Similarly, the listener needs to know which subject to listen to. Pub-Sub allows you to decouple the subject and its listeners usually through a message queue as the subject does not need to know what is listening and the listener does not need to know what published the event.
    - Visitor

      I'm kind of fifty-fifty when choosing to implement this pattern as it does make the behaviour harder to follow.
    - Strategy 

      Not really needed if your programming language allows you to compose behaviour.

## Don't Know

# Distributed Systems

## Know

* Kafka - a distributed append-only log masquerading as a message queue
* Cassandra

## Don't Know

# Soft Skills

## Know

* What to do when I don't agree with another engineer's approach. (Find a neutral third party to discuss with.)

## Don't Know

# Attitude

## Know

* I'm lazy and forgetful (especially compared to a computer). So I try to find ways to automate stuff or write things down.

## Don't Know

# Other

## Know

* Static site generation
  - Jekyll (This blog is built with it.)
* Touch typing

## Don't Know