---
layout: post
title: Scala, Six Months In
category: ''
tags:
- Scala
date: 2019-03-31 13:28:37 +0000

---
After switching jobs, I was introduced to Scala at my workplace, Agoda.

<!--excerpt-->

# Nice Things

I've kept the comparisons to the languages themselves, instead of functionality that are provided by libraries.

## Null Safety

Even though Optionals have been part of the Java 8 standard library for some time, few libraries use it in their APIs which forces you to deal with the existence of null references. However in Scala, the use of Options is idiomatic, which when combined with pattern matching, allows you to handle non-existence separately from error values. That is the biggest reason why null pointer exceptions are such an ubiquitous pain in the ass in Java.

## Case Classes

Compare this:

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

with the equivalent:

    case class MyCaseClass(name: String, year: Int)

It's really refreshing being able to just define your data in a class with sensible defaults for printing and comparison. Even though all the methods in the Java example were generated automatically by Intellij, there's so much more boilerplate than in Scala.

## Pattern Matching

    def makeADecision(argument: Option): Unit = argument match {
    	case Some(_) => println("Has a value")
    	case None    => println("No value in argument")
    }

Yep, switch-case on steroids! No more calling `instanceof()` to check the class

## Functional Programming

# ... Not So Nice Things

## Compilation Speed

It's a **lot** slower to compile Scala as compared to Java. Enough that the job that only checks if the pull request compiles successfully regularly takes five minutes to complete on a typical build agent in our Continuous Integration setup.

## ~~Simple~~ Complex Build Tool

This is subjective, but sbt is one of the more complicated build tools that I have come across. Till now, I've mainly gotten around that problem by copying sbt task definitions from other existing projects.