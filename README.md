Scala Retry
===========

[![Build Status](https://travis-ci.org/hipjim/scala-retry.svg?branch=master)](https://travis-ci.org/hipjim/scala-retry)

Simple retry mechanism for arbitrary function calls in scala.

We live in times where software systems are built by small services that are talking to each other.
In such systems, transient errors can occur and interfere with the normal program execution flow.
Transient faults are temporary abnormal conditions such as offline services, infrastructure faults, or network issues.
It is common practice to retry transient faults using a retry loop mechanism.
This minimalistic library tries to solve this problem by implementing the retry side effect
and give the user different retry mechanism.

## Maven artifacts

[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.hipjim/scala-retry_2.11/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.github.hipjim/scala-retry_2.11)

```scala
libraryDependencies += "com.github.hipjim" % "scala-retry_2.11" % "0.2.0"
```

```xml
<dependency>
  <groupId>com.github.hipjim</groupId>
  <artifactId>scala-retry_2.11</artifactId>
  <version>0.2.0</version>
</dependency>
```

## Tutorial

```scala
import scala.concurrent.duration._
import util.retry.blocking.{RetryStrategy, Failure, Retry, Success}

// define the retry strategy
implicit val retryStrategy =
    RetryStrategy.fixedBackOff(retryDuration = 1.seconds, retryAttempts = 2)

// pattern match the result
val r = Retry(1 / 1) match {
    case Success(x) => x
    case Failure(t) => log("I got 99 problems but you won't be one", t)
}

// recover in case of a failure
val recover = Retry(1 / 0) recover {
    case NonFatal(t) => Int.MaxValue
}

// get or else in case of failure
val result = Retry(1 / 0).getOrElse(1)

// can be used in for comprehensions
val result = for {
  x <- Retry(1 / 0) // fails with java.lang.ArithmeticException: / by zero
  y <- Retry(1 / 1) // success
} yield x + y // result is Failure with java.lang.ArithmeticException: / by zero

```
