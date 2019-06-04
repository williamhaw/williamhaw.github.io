---
layout: post
title: Scala, Six Months In
category: ''
tags:
- Scala
date: '2019-03-31T13:28:37.000+00:00'

---
After switching jobs, I was introduced to Scala at my new workplace. I've compiled a list of things that I have learned until now. I've kept the comparisons to the languages themselves, instead of functionality that can be provided by libraries.

<!--excerpt-->

# Nice Things

## Immutability and Type Inference

Declaring local variables in idiomatic Scala starts off by declaring it `val`, which makes the reference immutable, then change it to `var` if you _really_ need to mutate it (so far I've never found the need to do that). Contrast that with Java, which is the other way around (declare a `final` variable if you want immutability, otherwise it is mutable by default). It's much easier to reason about the behaviour of these variables when you don't have to worry that the reference can be changed later on.

Also nice is type inference:
```scala
val myString = "Hello, World." //myString is of type String
```

Java only recently got [this](https://developer.oracle.com/java/jdk-10-local-variable-type-inference.html) in Java 10.

## Null Safety

Even though Optionals have been part of the Java standard library since Java 8, few libraries use it in their APIs which forces you to deal with the existence of null references. However in Scala, the use of Options is idiomatic, which when combined with pattern matching, allows you to handle non-existence separately from error values. That is the biggest reason why null pointer exceptions are such an ubiquitous pain in the ass in Java.

## Case Classes

Compare this:

```java
public class MyPojo {
    private String name;
    private Integer year;

    public MyPojo(String name, Integer year) {
        this.name = name;
        this.year = year;
    }

    public String getName() {
        return name;
    }

    public Integer getYear() {
        return year;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        MyPojo myPojo = (MyPojo) o;
        return name.equals(myPojo.name) &&
                year.equals(myPojo.year);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, year);
    }

    @Override
    public String toString() {
        return "MyPojo{" + name + "," + year + '}';
    }
}
```

with the equivalent:

```scala
case class MyCaseClass(name: String, year: Int)
```

It's really refreshing being able to just define your data in a one-liner class with sensible defaults for printing and comparison. Even though all the methods in the Java example were generated automatically by Intellij, there's so much more boilerplate than in Scala.

## Pattern Matching

```scala
def makeADecision(argument: Option): Unit = argument match {
    case Some(_) => println("Has a value")
    case None    => println("No value in argument")
}
```

Yep, switch-case on steroids! No more calling `instanceof()`and casting the object to a child class. Then again there's a [JEP](https://openjdk.java.net/jeps/305) for pattern matching in Java.

***

# Concepts

## Functional Programming

> A monad is just a monoid in the category of endofunctors, what's the problem?

There's a [lot](https://medium.com/beingprofessional/understanding-functor-and-monad-with-a-bag-of-peanuts-8fa702b3f69e) of [literature](https://blog.redelastic.com/a-guide-to-scala-collections-exploring-monads-in-scala-collections-ef810ef3aec3) explaining [monads](http://appliedscala.com/blog/2016/understanding-monads/), so I won't presume to be able to explain them here, but most data structures in the Scala collections library, as well as things like Futures and Options are monads and therefore express computation in an easily composable way.

## Types Are Your Friend

I especially liked learning about [sealed traits](https://gist.github.com/mattbarackman/82b1712add45ceffa8ffe56f81b8bcc2), which when paired with pattern matching, lets me actually catch more errors at compile time due to exhaustiveness checking. That's kind of the point of having an explicit type system anyway.

## Typeclasses

Separate your data and logic! Though this uses scary "implicit" variables/methods to do so... Here's a good [introduction](https://alvinalexander.com/scala/fp-book/type-classes-101-introduction).

***

# ... Not So Nice Things

## Compilation Speed

It's a **lot** slower to compile Scala as compared to Java. Enough that the job that only checks if the pull request compiles successfully regularly takes five minutes to complete on a typical build agent in our Continuous Integration setup.

## ~~Simple~~ Complex Build Tool

This is subjective, but sbt is one of the more complicated build tools that I have come across. Till now, I've mainly gotten around that problem by copying sbt task definitions from other existing projects, but I despair of actually understanding it.

# Conclusion
In conclusion, I think there are many language design choices in Scala that actually make it _nice_ to work in as compared to Java. Then again I used to work in a Java 7/8 shop. Nowadays Java 11 has incorporated many new language features inspired by what other languages are doing, so YMMV.