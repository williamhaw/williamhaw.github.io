---
layout: post
title: Using Mockito With Scala Value Classes
category: ''
tags:
- Scala
- Value Classes
- Mockito
- Specs2
- Scalatest

---

tl;dr: When mocking functions that have value classes as arguments, use Mockito matchers on the wrapped value instead of on the value class (i.e. `MyId(any[Int])` instead of `any[MyId])` or else NullPointerExceptions will be thrown at runtime.

<!--excerpt-->

I puzzled over unexpected NullPointerExceptions when checking values in value classes for half an hour until finally finding the answer. I couldn't find a complete article about this gotcha, so here's a short writeup.

[Value classes](https://docs.scala-lang.org/overviews/core/value-classes.html) have two uses in Scala:

1. To avoid allocation of an extra wrapper class at runtime.
2. As a tag for certain important pieces of data instead of passing raw strings and integers around.

For example,

```scala
case class MyId(value: Int) extends AnyVal

trait ExampleService {
    def exists(id: MyId): Boolean
}

class Caller(service: ExampleService) {
    def businessLogic(id: MyId): Boolean = {
        //... some business logic here
        service.exists(id)
    }
}
```


However, when asserting the value of MyId in ScalaTest below, a NullPointerException is produced at runtime.

```scala
class MyIdTest extends AnyFlatSpec with Matchers with MockitoSugar {

    "Caller" should "call ExampleService with id 1" in {
        val mockService = mock[ExampleService]
        val caller = new Caller(mockService)
        caller.businessLogic(MyId(1))
        verify(mockService).exists(any[MyId])
    }
}
```

The right way to assert the value passed into the mock is actually:

```scala
verify(mockService).exists(MyId(any[Int])) //match any value
verify(mockService).exists(MyId(1))        //match a particular value
```

In Specs2 (throws NullPointerException), this would look like:

```scala
class MyIdSpec extends Specification with Mockito{
  "Caller" should {
    "call ExampleService with id 1 in specs2" in {
      val mockService = mock[ExampleService]
      val caller = new Caller(mockService)
      caller.businessLogic(MyId(1))
      there was one(mockService).exists(any[MyId])
    }
  }
}
```

To fix:

```scala
there was one(mockService).exists(MyId(anyInt)) //match any value
there was one(mockService).exists(MyId(===(1))) //match a particular value
```

[Repository with code examples](https://github.com/williamhaw/mockito-scala-value-classes-example)