---
layout: post
title: Everything I Know About Development in 2019
category: Everything I Know About Development
tags: []

---
I'm starting this series so that I can track how far I've gone in my career as an engineer.

In true [Dan Abramov](https://overreacted.io/things-i-dont-know-as-of-2018/) style I'll be listing what I **don't** know as well, so that I can concentrate on learning those things in the future.

<!--excerpt-->

# Python

I learned this at University and it has been useful ever since.

## Know

* Basic data structures
* List comprehensions
* Lambdas
* Generators and iterators
* Using a dictionary for switch-case logic
* The Global Interpreter Lock
* Automation scripts using the `subprocess` module
* Bottle

  A minimal, zero-dependency web framework.
* Numpy and SciPy
* Keras

## Don't Know

* Real object-oriented programming in Python
  
  I've always used Python more as a scripting language than a "production" language, so I could improve there.

* More opinionated frameworks like Flask and Django
* Tensorflow
* Real data science stuff

  I've tried training neural networks on real data and got really horrible results. I'd like to learn how to clean and represent data better.

# Java

I started nearly four years ago as the first engineering hire in a tiny [startup](http://clickdrive.io/) mainly running Java in the cloud and in our vehicle IOT solution. I still think that the stuff I worked on there was really interesting, and in many cases I was the main contributor to the features of our solution.

Those include:
- Building a diff-based package manager using [Merkle trees](https://en.wikipedia.org/wiki/Merkle_tree) to automate deployments to our devices over the air while minimising bandwidth usage over 4G.
- Implementing a message queue protocol over UDP to reliably send streaming data to our cloud servers.
- Hacking car CAN bus networks and reverse engineering recorded messages.
- Implementing a video telemetry recording and playback app for a client to monitor and give feedback to driving students and instructors.

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
      - Use as reflection and serialisation-safe singleton
  - The contract between hashcode() and equals().

    Basically, if two objects are equal, the hashcode must be the same. The converse does not apply.
  - Concurrency
    - Executor
      
      Why do thread pools exist? Because creating threads is resource intensive due to allocating 1MB of stack per thread by default.
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

  The first part of dependency injection is writing your classes to accept their dependencies through the constructor / setup code. The other part is either wiring up the dependencies by hand from the main() function or using a library/framework to auto-magically wire them up.
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

For the past year, I've been working at a [technology company in the travel industry](https://www.agoda.com), mainly using Scala to integrate with third party supplier APIs. I think it's interesting in much different ways from my previous job as it's more about exploring the landscape of optimisation possibilities and maintaining and debugging several complex systems at scale. I also got to appreciate that there are two parts to scaling: the actual performance of the system, and coordinating the efforts of many engineers.

Those include:
- Actually integrating suppliers end to end
- Using A-B testing on the backend to measure increase (or decrease) in number of bookings
- Creating a system that automatically turns off suppliers when too many booking calls fail (reduced calls to our rotating ops team).
- Rewriting a query to filter and process petabytes of log data
- Developing a generic DSL system (using Groovy backed by Scala) to transform our internal requests and responses to and from supplier requests and responses.


> A language that doesn't affect the way you think about programming, is not worth knowing.
> -<cite>Alan Perlis, [Epigrams on Programming](http://pu.inf.uni-tuebingen.de/users/klaeren/epigrams.html) #19, 1982</cite>

More and more it seems to me that Scala is one of those languages which has affected the way I think about programming. The biggest change is that types actually feel like my friend now, in that I can use the richer type system and constructs to automatically enforce many things that I would have to write code for in Java.

## Know

* Functional Programming
  - Monads
    - map() and flatMap()
    - Using data structures that are lawful monads like Option and Seq
  - For-Comprehensions for that sweet syntactic sugar
  - Immutable data structures as default
  - Pattern matching and structuring/destructuring
  - Typeclasses
  - Algebraic Data Types
* Asynchronous programming
  - Futures and ExecutionContexts
* Play 2
  - Contributed to a couple of internal Play apps at work

  I don't have much to say about this, it's a nicely designed framework that gets out of my way.
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
  - Actor hierarchies - I've heard one sticking point is how to implement backpressure effectively.
  - Akka Cluster (not sure what problem this solves quite yet)

# JavaScript/ Web Frontend

## Know

- Vanilla Javascript
- Jquery
- Bootstrap
- HTML5 Canvas
- HTML5 Video
- Leaflet/Leaflet TimeDimension

## Don't Know

- Modern JS frameworks like React and Vue (although I have been trying to get into learning Vue recently).
- ES2015, 2016, 2017, 2018, 2019 - basically all the new stuff
- SASS and SCSS
- JS build tooling and pipelines (never used Webpack)
- UI/UX Design

# Sysadmin/DevOps

## Know

* How to set up a Linux machine manually
* Configuration of Nginx
  - As a proxy
  - As a static file server
  - As a video streaming server
* Grafana dashboards
* Docker/Docker Compose
* Basic Kubernetes concepts and commands
* Basic Puppet scripts

## Don't Know

* Kubernetes failure modes
* Helm

# Databases

## Know

* DynamoDB
  - Partition Keys and Sort Keys
  - Scan vs Query
  - Access using Java and Python bindings

* SQL - currently working with SQL Server and some query tools backed by Hadoop

## Don't Know

Basically the in-depth stuff a DBA would know - I always go to them for help optimising queries. At work, the database devs run a bot that scans Github for stored procedures and notifies them for review.

# Architecture/High Level Design

## Know

* The Software Development Life Cycle (SDLC)
  - Gathering Requirements
  - Design
  - Implementation
  - Testing
  - Launch
  - Maintenance

* Object Oriented Design
  - Abstraction

    Extract common patterns from different use cases and put them in one place.
  - Encapsulation

    Don't leak internal details to consumers of your classes.
  - Inheritance

    I've seen this done badly more often than not, and modern OO design seems to go for composition over inheritance.
  - Polymorphism

  	The same method call has different behaviour depending on the class/type of the object it is called on. There are actually eight types of polymorphism as described [here](https://dev.to/jvanbruegge/what-the-heck-is-polymorphism-nmh).

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
    - [Factory](https://refactoring.guru/design-patterns/factory-method)
      
      When the caller doesn't need to control the creation of the returned object.
    - [Builder](https://refactoring.guru/design-patterns/builder)

      When the caller needs granularity in creating the returned object.
    - [Singleton ](https://refactoring.guru/design-patterns/singleton)

      Singletons with mutable state are **NOT** a good idea.

  - Structural
    - [Adapter](https://refactoring.guru/design-patterns/adapter)
    - [Facade](https://refactoring.guru/design-patterns/facade)

  - Behavioural
    - [Chain of Responsibility](https://refactoring.guru/design-patterns/chain-of-responsibility)
    - [Command](https://refactoring.guru/design-patterns/command)
    - [Iterator](https://refactoring.guru/design-patterns/iterator)
    - [Observer](https://refactoring.guru/design-patterns/observer)

      Common interview question: How is the Observer pattern different from Publish-Subscribe?
      Answer: The subject needs to know which listeners are listening as it keeps a list of listeners to trigger the event. Similarly, the listener needs to specify which subject to listen to. Pub-Sub allows you to decouple the subject and its listeners usually through a message queue as the publisher does not need to know what is listening and the subscriber does not need to know what published the event.
    - [Visitor](https://refactoring.guru/design-patterns/visitor)

      I'm kind of fifty-fifty when choosing to implement this pattern as it does make the behaviour harder to follow.
    - [Strategy](https://refactoring.guru/design-patterns/strategy)

      Choose which algorithm to use at runtime! Not really needed if your programming language allows you to compose behaviour.
    - [Template Method](https://refactoring.guru/design-patterns/template-method)

      One of the best uses of inheritance where the base algorithm is implemented in the parent class and the child classes specifies the details.

## Don't Know

I don't have much experience with designing full-stack architectures. (I'm not very strong at the front-end or mobile.)

# Distributed Systems

## Know

* Message queues
  - ZeroMQ - a message queue that looks and acts like a socket
  - Kafka - a distributed append-only log masquerading as a message queue
* Distributed databases
  - Cassandra - a distributed append-only log masquerading as a database
* Eventual Consistency
* Sockets and programming at the network layer

## Don't Know

* Implementing backpressure in a distributed system with message queues.
* Kafka Streams - apparently you can write transforms and have them run inside of Kafka

# Soft Skills

## Know

* What to do when I don't agree with another engineer's approach. (Find a neutral third party to discuss it with.)

* How to ask questions to technical people.
  1. Find out who the right person to ask is.
  2. Do some work to understand the problem (RTFM, write a minimal test, etc)
  3. Phrase it as "I tried X so that I could make Y work, but Z happened."
  4. Post the solution if you found it so that others can benefit.

  Really good link [here](http://www.catb.org/~esr/faqs/smart-questions.html).

* To only say things when I am confident of them (in a work environment).

## Don't Know

* How to ask questions to non-technical people - could do this better.
* How to defuse an argument once it has started.

# Attitude

## Know

* I'm lazy and forgetful (especially compared to a computer). So I try to find ways to automate stuff or write things down.

* When designing distributed systems, anything that can fail will fail eventually. So think of error handling right from the start and then set up good monitoring and logging systems.

* Not to judge engineers by the tools they happen to use, but by the quality of their thinking.

  I think this applies to things like editor wars, functional vs OOP, GUI vs command line or Linux vs Windows or Mac where there's so much unnecessary gatekeeping going on.

* To get what I need, I need only to ask for it.
  
  Of course, the devil is in the details - who to ask, being polite and respectful and having contingency plans are some of them.

## Don't Know

* How to be consistent in practising and learning - I don't think I'm very good at this.

# Other

## Know

* Static site generation
  - Jekyll (This blog is built with it.)
* Touch typing (Not really as effective as I'd like to be though.)

## Don't Know

* Mobile
  
  I've played with iOS and Android development in the past, but I haven't kept up.