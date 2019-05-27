# Reactive programming

## Wikipedia

> Reactive programming is a declarative programming paradigm concerned with data streams and the propagation of change. With this paradigm it is possible to express static (e.g., arrays) or dynamic (e.g., event emitters) data streams with ease, and also communicate that an inferred dependency within the associated execution model exists, which facilitates the automatic propagation of the changed data flow.

Is too generic and theoretical as usual.

## Stackoverflow

Stackoverflow's canonical answer is obviously not suitable for newcomers.

https://stackoverflow.com/questions/1028250/what-is-functional-reactive-programming

> ![first stack overflow answer by popularity](./images/so-definition.png)

> ![second stack overflow answer by popularity](./images/so-definition-2.png)

## Reactive Manifesto

Reactive Manifesto sounds like the kind of thing you show to your project manager or the businessmen at your company.

https://www.reactivemanifesto.org/

> ![reactive manifesto definition 1](./images/rm-1.png)

> ![reactive manifesto definition 2](./images/rm-2.png)

## Microsoft

Microsoft's Rx terminology is so heavy and Microsoftish that most of us are left confused.

> The Reactive Extensions (Rx) is a library for composing asynchronous and event-based programs using observable sequences and LINQ-style query operators.

> Rx = Observables + LINQ + Schedulers

https://archive.codeplex.com/?p=rx

## Other

Terms like "reactive" and "propagation of change" don't convey anything specifically different to what your typical MV* and favorite language already does. 

Of course my framework views react to the models. Of course change is propagated. If it wouldn't, nothing would be rendered.

# Simple definition

Reactive programming is programming with asynchronous data streams.

# Idea

It's not new. Event buses or click events in your projects are an asynchronous event stream.

Reactive is that idea on steroids.

Create streams. It's cheap. Anything can be source: var, list, input, caches, etc.
On top is toolbox with stream manipulations: combine, create, filter, map and other operations.

# Example

    a = b + c

## Imperative programming

change _b_:

    b = 2

_a_ stays the same until:

    a = b + c

## Reactive programming  

change _b_:

    b = 2
    
_a_ automatically recalculated

# Idea 

A stream is a sequence of ongoing events ordered in time. 

![stream definition image](https://camo.githubusercontent.com/36c0a9ffd8ed22236bd6237d44a1d3eecbaec336/687474703a2f2f692e696d6775722e636f6d2f634c344d4f73532e706e67)

--a---b-c---d---X---|->

a, b, c, d are emitted values
X is an error
| is the 'completed' signal
---> is the timeline

It can emit three different things: 

* value
* error
* completed signal

We capture these emitted events only asynchronously, by defining a function that will execute when a value is emitted, another function when an error is emitted, and another function when 'completed' is emitted.

Subscribing is the "listening" to the stream. 

Observers are the functions we are defining. 

Subject/Observable is the stream is being observed. 

![Create, combine, listen](./images/def-03.PNG)

## RxJava basics

![Basics 01](./images/simpleObs-01.png)

![Basics 02](./images/simpleObs-02.png)

![Basics 03](./images/simpleObs-03.png)

![Basics 04](./images/simpleObs-04.png)

![Basics 05](./images/simpleObs-05.png)

# Reasoning

It's hard to reason about reactive programming.

Why RP? Why RxJava? Where is Android?

Why should we learn this?

## Story about cars and horses

we are in 19th century

![19th century](./images/horse_09.jpg)

horses are main transpotation

![horse](./images/horse_00.jpg)

somebody from future shows you a car

![car](./images/horse_02.jpg)

how to use it?

![how to use a car](./images/horse_01.jpg)

driving signs and conventions

![driving signs and conventions](./images/horse_03.jpg)

learning and exams

![rules and exams](./images/horse_04.jpg)

parallel parking

![parallel parking](./images/horse_06.jpg)

roads

![roads](./images/horse_05.jpg)

you seat at the car and nothing happens

![car](./images/horse-car2.jpg)

You love horses

![people and horses](./images/horse_07.jpg)

conquer a whole continent

![conquer](./images/horse_08.jpg)

nothing bad about horses

![horse](./images/horse_00.jpg)

but cars are better

![car](./images/horse_02.jpg)

# Problems

## Software requirements changed

Resource                | 10 years ago | now          |
------------------------|--------------|--------------|
Server nodes            | 10's         | 1000's       |
Response times          | seconds      | milliseconds |
Maintenance downtimes   | hours        | none         |
Data volume             | GBs          | TBs -> PBs   |
Clients                 | 1-2          | up to 10's   |

## Software development complexity

Technologies count is constantly increasing.
Learning curve is high. 
Complexity is proportional to software size. 

## Costs

* Software development
* Hardware
* Maintenance

# Reactive programming history
    
## Founders and evangelists

Erik Meijer https://en.wikipedia.org/wiki/Erik_Meijer_(computer_scientist), reactive programming author in Cloud Programmability Team in Microsoft. 

David Karnok, Research Assistant at MTA SZTAKI (Hungary, Budapest) https://www.linkedin.com/in/david-karnok-725b3189/

He started in 2010 and made reactive programming in java 2 years before Netflix. 

In 2011 published reactive4java https://github.com/akarnokd/reactive4java

In 2013 he merged efforts with Netflix to create RxJava. 

Now RxJava counts more than 5k commits since then. 

Ben Christensen (http://benjchristensen.com/), one of men behind Netflix API, RxJava and more, now a Facebook engineer.

## Other important people

Jonas Bonér (http://jonasboner.com/) and Roland Kuhn (https://rolandkuhn.com/) ‒ creators of Akka (Lightbend) and Reactive Manifesto authors.

Stephane Maldini (https://github.com/smaldini), project Reactor lead, prinipal engineer at Pivotal.

André Staltz (https://staltz.com/), JavaScript expert and reactive programming evangelist.

Martin Odersky (http://lampwww.epfl.ch/~odersky/), creator of Scala.

Venkat Subramaniam (https://agiledeveloper.com/aboutus.html), java expert and tech evangelist.

# Implementations

Java <9 CompletableFuture / Java 9 http://www.reactive-streams.org/ 

RxJava http://reactivex.io/

Reactor https://projectreactor.io/

Coroutines and channels in Kotlin https://github.com/Kotlin/kotlinx.coroutines

akka in Scala https://doc.akka.io/docs/akka/current/guide/actors-motivation.html

# RxJava and Android

Targets older Java version

A mature and solid library

# Pre basics

## Iterator pattern 

https://en.wikipedia.org/wiki/Iterator_pattern

### Problems to solve

- the elements of an aggregate object should be accessed and traversed without exposing its representation (data structures)
- new traversal operations should be defined for an aggregate object without changing its interface

### Solution

- define a separate (iterator) object that encapsulates accessing and traversing an aggregate object
- clients use an iterator to access and traverse an aggregate without knowing its representation (data structures)

## Observer pattern 

https://en.wikipedia.org/wiki/Observer_pattern

### Problems to solve

- a one-to-many dependency between objects should be defined without making the objects tightly coupled
- it should be ensured that when one object changes state an open-ended number of dependent objects are updated automatically
- it should be possible that one object can notify an open-ended number of other objects

### Solution

- define Subject and Observer objects
- so that when a subject changes state, all registered observers are notified and updated automatically

# Basics

Just a stream of data events, but made right

![The Observer pattern done rigth](./images/def-01.PNG)

- push vs pull (backpressure)
- async vs sync
- concurrency and parallelism (onNext(), onCompleted(), onError() cannot be emmited concurrently, aka thread-safe)

![thread safety is ok](./images/rxjava-thread-safe-01.png)

![thread safety is not ok](./images/rxjava-thread-safe-02.png)

![asynchronous merge](./images/rxjava-thread-safe-03.png)

Why not just allow onNext() to be invoked concurrently?

* defensive code to check threads
* some operations has to be sync (scan/reduce) to accumulate data
* performance

- lazy vs eager

* subscription, not construction starts work
* observables can be reused

- duality

An Rx Observable is the async "dual" of an Iterable

- cardinality

The Observable supports asynchronously pushing multiple values

/              | One              | Many                    |
---------------|------------------|-------------------------|
synchronous    | T getData()      | Iterable<T> getData()   |
asynchronous   | throws Exception | Observable<T> getData() |

# Functional reactive programming 

```           
           asynchronous
           values
           events
           push
functional reactive
lambdas
closures
pure
composable
```

Functional code is idempotent, depends only on arguments.  
Pure functions has no side-effects (no state). 

# Reactive manifesto

https://en.wikipedia.org/wiki/Reactive_programming 

- message-driven (react to events / event-driven)
- elastic (react to load / scalable)
- resilient (react to failures)
- responsive (react to users)

```
             responsive
         ↗                ↖
scalable          ↑         resilient
         ↖                ↗
           message-driven 
```

Reactive Manifesto https://www.reactivemanifesto.org/

Reactive Manifesto 2.0 https://www.lightbend.com/blog/reactive-manifesto-20

# RxJava formal definition 

RxJava is a Java VM implementation of [Reactive Extensions](http://reactivex.io/): a library for composing asynchronous and event-based programs by using observable sequences.

It extends the observer pattern to support sequences of data/events and adds operators that allow you to compose sequences together declaratively while abstracting away concerns about things like low-level threading, synchronization, thread-safety and concurrent data structures.

Source: https://github.com/ReactiveX/RxJava

# RxJava contract

An Observable is the asynchronous "dual" to the synchronous/pull Iterable

event          | Iterable (pull)  | Observable         |
---------------|------------------|--------------------|
retrieve data  | T next()         | onNext(T)          |
discover error | throws Exception | onError(Exception) |
complete       | returns          | onCompleted()      |

Completable (0 emits)

Single (1 emit)

Observable/Flowable (many emits)

## Asynchronous operations

Rx makes asynchronous operations easier

https://gist.github.com/benjchristensen/4670979

https://gist.github.com/benjchristensen/4671081

Rx avoids callback hell

https://gist.github.com/benjchristensen/4677544

How to implement it in Rx 

https://stackoverflow.com/questions/28402376/how-to-compose-observables-to-avoid-the-given-nested-and-dependent-callbacks

https://github.com/Netflix/ReactiveLab/blob/952362b89a4d4115ae0eecf0e73f273ecb27ba98/reactive-lab-gateway/src/main/java/io/reactivex/lab/gateway/routes/RouteForDeviceHome.java

easy switching back and forth between threads

# Operators

## Marble diagrams

![Marble 01](./images/marble-01.png)
![Marble 02](./images/marble-02.png)
![Marble 03](./images/marble-03.png)
![Marble 04](./images/marble-04.png)

## Desicion tree

http://reactivex.io/documentation/operators.html#tree

## Creating observables
    
Create    

![Marble Create](./images/m-create.png)

Just

![Marble Just](./images/m-just.png)

From

![Marble From](./images/m-from.png)

Interval

![Marble Interval](./images/m-interval.png)

Timer

![Marble Timer](./images/m-timer.png)

Range

![Marble Range](./images/m-range.png)

## Filtering observables

Filter

![Marble Filter](./images/m-filter.png)

Distinct

![Marble Distinct](./images/m-distinct.png)

Skip

![Marble Skip](./images/m-skip.png)

Debounce 

![Marble Debounce](./images/m-debounce.png)

Throttle

![Debounce-throttle](./images/debounce-throttle.png)

http://demo.nimius.net/debounce_throttle/

## Transforming observables

Map

![Marble Map](./images/m-map.png)

Buffer 

![Marble Buffer](./images/m-buffer.png)

FlatMap 

![Marble FlatMap](./images/m-flatmap.png)

## Combining observables

CombineLatest

![Marble CombineLatest](./images/m-combinelatest.png)

Merge

![Marble Merge](./images/m-merge.png)

Zip

![Marble Zip](./images/m-zip.png)

Concat

![Marble Concat](./images/m-concat.png)

## Other useful

Contains 

![Marble Contains](./images/m-contains.png)

Reduce

![Marble Reduce](./images/m-reduce.png)

# Schedulers

Sync and async work

Easy thread switching

Standard schedulers

- computation
- io
- from(Executor executor)
- single
- trampoline (fifo)

AndroidSchedulers

- mainThread

# Subjects

PublishSubject

![Subject Publish](./images/s-publish.png)

BehaviourSubject

![Subject Behavior](./images/s-behavior.png)

ReplaySubject

![Subject Replay](./images/s-replay.png)

AsyncSubject

![Subject Async](./images/s-async.png)

# Backpreassure

Flowable

BackpressureStrategy

- buffer
- drop
- latest

# Simple examples

![Simple example 01](./images/ex-01.png)

![Simple example 02](./images/ex-02.png)

![Simple example 03](./images/ex-03.png)

![Simple example 04](./images/ex-04.png)

![Simple example 05](./images/ex-05.png)

![Simple example 06](./images/ex-06.png)

![Simple example 07](./images/ex-07.png)

![Simple example 08](./images/ex-08.png)

![Simple example 09](./images/ex-09.png)

![Simple example 10](./images/ex-10.png)

![Simple example 11](./images/ex-11.png)

![Simple example 12](./images/ex-12.png)

![Simple example 13](./images/ex-13.png)

![Simple example 14](./images/ex-14.png)

![Simple example 15](./images/ex-15.png)

# RxJava and android real world examples

## Exponential backoff 

![Exponential backoff 01](./images/exp-backoff-01.png)

![Exponential backoff 02](./images/exp-backoff-02.png)

https://medium.com/over-engineering/rxify-exponential-backoff-on-retry-48bb66912391

## Some more examples

Detecting and testing stalled streams https://www.nurkiewicz.com/2017/09/detecting-and-testing-stalled-stream.html
Fixed-rate vs. fixed-delay https://www.nurkiewicz.com/2017/09/fixed-rate-vs-fixed-delay-rxjava-faq.html

## Managing State with RxJava

Managing State with RxJava, Jake Wharton https://www.youtube.com/watch?v=0IKHxjkgop4

![wwjd](./images/wwjd.jpg)

![wwjd2](./images/wwjd2.jpg)

# Performance

v1 vs v2

Tomcat vs Rx performance measures 

https://speakerdeck.com/benjchristensen/applying-reactive-programming-with-rxjava-at-goto-chicago-2015

![performance-01](./images/perf-01.png)

![performance-02](./images/perf-02.png)

![performance-03](./images/perf-03.png)

# Cons 

## Traditional vs reactive costs

Netflix point by Tomasz Nurkiewicz (backend)

![blockin programming costs](./images/cost_blocking.png)

![reactive programming costs](./images/cost_reactive.png)

![costs tipping point](./images/cost_tipping_point.png)

In October 2018 15% of world traffic is Netflix.

![world top 10 traffic apps](./images/cost_traffic_share.png)

Netflix point.

![netflix point](./images/cost_netflix_point.png)

### Little's Law

Little's Law https://en.wikipedia.org/wiki/Little%27s_law

L = λ ⋅ W

Tomcat, 100 threads (L), 100 ms/request (W), λ - ?

λ = 1k requests/second (e.g. on a laptop)

L — среднее по времени число запросов в рассматриваемой части системы [шт.],  
W — среднее время, за которое запросы проходят через данную часть системы [с],  
λ — скорость поступления запросов в систему [шт./с]

https://habr.com/ru/company/yandex/blog/431650/ - latency home reading with Little's law explanation

Have you heard about 'space-time trade off'? (caching)

What about 'human-hardware trade off'?

Usually, to the left of Netflix point, you want to spend as least as possible on development understanding that you could make balance on pure software buying more servers to solve problems. 

But in some cases, to the rigth of Netflix point, humans are more precious than hardware, but in scale you save on hardware more. 

## Costs in clients

It's almost the same, but without hardware.

Spend less on development and you get difficult to maintain products. 

Spend more and you get less difficulties to maintain. 

## Simplicity

May you live in interesting times (Chinese curse)

Ubiquitous language (https://martinfowler.com/bliki/UbiquitousLanguage.html)

### Measure of code quality

What is a universal measure of a code quality? 

- simple (simple is a matter of taste and expericence)
- tested (crappy code and poor tests)
- open/closed
- SOLID
- high cohesion
- low coupling
- cyclomatic complexity
- DRY
...

### Measuere of code quality

boring

I don't care about language, framework, and particularly reactive library

### 10x developer

Who is 10x developer? 

Who enables 10 other developers

## Other cons

It takes some time to dive in

Stacktraces are meaningless (no context)

It is difficult to follow a request as events and callbacks are processed 

... unhandled exceptions, and incorrectly handled state changes ... These types of issues have proven to be quite difficult to debug

Exceptions from hell (NPE) 

Timeout exceptions (with no hint where it happend)

Order is no longer guaranteed

Backpressure is difficult

Everyone makes own implementation and terms

# Sources

https://en.wikipedia.org/wiki/Reactive_programming
https://stackoverflow.com/questions/1028250/what-is-functional-reactive-programming
https://www.reactivemanifesto.org/
Reactive Manifesto 2.0 https://www.lightbend.com/blog/reactive-manifesto-20
https://archive.codeplex.com/?p=rx
The introduction to Reactive Programming you've been missing, Andre Medeiros (alias "Andre Staltz"), 2014, https://gist.github.com/staltz/868e7e9bc2a7b8c1f754 
Ubiquitous language, Eric Evans (https://martinfowler.com/bliki/UbiquitousLanguage.html)

https://en.wikipedia.org/wiki/Observer_pattern
https://en.wikipedia.org/wiki/Iterator_pattern

Erik Meijer https://en.wikipedia.org/wiki/Erik_Meijer_(computer_scientist)
Jonas Bonér http://jonasboner.com/ 
Roland Kuhn https://rolandkuhn.com/
Martin Odersky http://lampwww.epfl.ch/~odersky/
Stephane Maldini https://github.com/smaldini
André Staltz https://staltz.com/
Venkat Subramaniam https://agiledeveloper.com/aboutus.html

Podcast with David Karnok https://github.com/artem-zinnatullin/TheContext-Podcast/blob/master/show_notes/Episode_3_Part_1.md
David Karnok reactive4Java repo https://github.com/akarnokd/reactive4java
https://projectreactor.io/
http://www.reactive-streams.org/ 
http://reactivex.io/
https://github.com/Kotlin/kotlinx.coroutines
https://doc.akka.io/docs/akka/current/guide/actors-motivation.html
https://github.com/ReactiveX/RxJava

Netflix architecture https://medium.com/netflix-techblog/optimizing-the-netflix-api-5c9ac715cf19
Netflix embrace concurrency https://medium.com/netflix-techblog/reactive-programming-in-the-netflix-api-with-rxjava-7811c3a1496a

Some collection of info around RxJava https://github.com/xiaomeixw/NoRxJava

Reactive Streams with Rx, Ben Christensen https://www.youtube.com/watch?v=g-ixzOcMDF4
http://benjchristensen.com/
https://speakerdeck.com/benjchristensen/reactive-streams-with-rx-at-javaone-2014
https://gist.github.com/benjchristensen/4670979
https://gist.github.com/benjchristensen/4671081
https://gist.github.com/benjchristensen/4677544
https://stackoverflow.com/questions/28402376/how-to-compose-observables-to-avoid-the-given-nested-and-dependent-callbacks
https://github.com/Netflix/ReactiveLab/blob/952362b89a4d4115ae0eecf0e73f273ecb27ba98/reactive-lab-gateway/src/main/java/io/reactivex/lab/gateway/routes/RouteForDeviceHome.java

Cycle.js and functional reactive user interfaces, Andre Staltz, https://youtu.be/uNZnftSksYg?t=545
What is reactive programming, Martin Odersky https://www.youtube.com/watch?v=7D9QfMj_KwI
Reactive programming: lessons learned by Tomasz Nurkiewicz, https://www.youtube.com/watch?v=5TJiTSWktLU
Exploring RxJava 2 for Android, Jake Wharton https://www.youtube.com/watch?v=htIXKI5gOQU
Managing State with RxJava, Jake Wharton https://www.youtube.com/watch?v=0IKHxjkgop4
Java Streams vs Reactive Streams: Which, When, How, and Why? by Venkat Subramaniam https://www.youtube.com/watch?v=kG2SEcl1aMM
Reactive Programming in Java by Venkat Subramaniam https://www.youtube.com/watch?v=f3acAsSZPhU
RxJava доставляет, Artem Zinnatullin https://www.youtube.com/watch?v=3jdvLrYZfB4

Latency explained with Little's Law (ru) https://habr.com/ru/company/yandex/blog/431650/ 
Tomcat vs rx performance measures https://speakerdeck.com/benjchristensen/applying-reactive-programming-with-rxjava-at-goto-chicago-2015

Simple RxJava examples https://gist.github.com/cesarferreira/510aa2456dc0879f559f

Exponential backoff https://medium.com/over-engineering/rxify-exponential-backoff-on-retry-48bb66912391

Detecting and testing stalled streams https://www.nurkiewicz.com/2017/09/detecting-and-testing-stalled-stream.html
Fixed-rate vs. fixed-delay https://www.nurkiewicz.com/2017/09/fixed-rate-vs-fixed-delay-rxjava-faq.html

debounce vs throttle http://demo.nimius.net/debounce_throttle/