---
layout: post
title: "Disruptor Illustrated by Metaphor"
date: 2014-03-29 22:02:36 +0800
comments: true
categories: 
---
# What is disruptor
LMAX disruptor is a high performance alternative to bounded queues for exchanging data between concurrent threads. Disruptor is first applied in financial and messaging systems for its high performance.

Disruptor throughput compared to queue    
![Throughput](images/throughput.png)

Latency compared to queue    
![Latency](images/latency.png)

Showing mechanical sympathy to modern CPU designs, disruptor is so fast in a parallel and staged environment. Following explains this in detail. 

# Parallel Programming

The best way to explain technology is by metaphor. As is said:
> <p>
> Technology is similar, thought behind is the key.
> <p>

## Metaphor in real world
_You are the boss of a fast food restaurant, what are you supposed to do to serve more customers? You hire a waitress with fast hands._

Waitress with fast hands    
![Fasthand](images/fasthand.jpg)

_However the speed of waitress is limited, so you hire another waitress. Now the two process requests in parallel, and total throughput is doubled._

Hire two waitresses    
<img src="images/morewaitress.gif" alt="Morewaitress" width="500">

## Technology
Parallel programming is just the same. with a high speed CPU, programs can run fast, result in high throughput and low latency. However CPU speed is limited to several GHZ, so more CPU cores are introduced, and programs can run in parallel. 

CPU speed is limited, but the core numbers obey Moore's law    
![Moore's law](images/moore.jpg)

# Staged processing

## Metaphor
_Your restaurant grows rapidly, so you must extend your business to more cities, even more countries. As the boss you can not manage all of your employee by yourself, instead you hire managers._

an organization is composed of multiple stages    
![Organization charts](images/organizational_charts.gif)

## Technology
Modern CPU has multiple level caches, and the architecture is similar to an organization.

CPU cache contains multiple stages    
![CPU cache](images/cache.png)

# Amdahl's law

## Metaphor
_In real world there is all kind of inefficiency, one department may depend on another. That's why management theories exists._ 

## Technology
In a program, how can we optimize process in such a parallel and staged environment? 

**Amdahl's law**

The speedup of a program using multiple processors in parallel computing is limited by the time needed for the sequential fraction of the program. If %5 of the program cannot compute in parallel, the speedup can only reach 20 times no matter how many processors there are. 

Amdahl's law    
![Amdahl's law](images/amdahl.png)

To remove inefficiency, just find the bottleneck that can not run in parallel, namely avoid shared resources.

# Optimization: Batch message
The first bottleneck is main memory access, which is expensive.

## Metaphor
_Manager collect status of employees, but they do not send to the boss you one by one, instead they wait until all the status are collected, and send to you in a batch._

## Technology
Similarly, CPU cache is split to cache lines, and batch read data from main memory.  

Disruptor use ring buffer to place objects padding together in memory so as to avoid false sharing and cache miss.

Ring buffer    
![ringbuffer](images/ringbuffer.jpg)

# Optimization: Lock free synchronization
Another bottleneck is shared locks.

## Metaphor
_People manager and product manager both manage employees. They can exchange status of employees directly since their offices are close to each other._

## Technology
Synchronization for shared resource is expensive. 

1. Lock: involves context switch, which is quite expensive.
1. CAS: lock instruction pipeline and complex to program.

lock penalty    
![Lock penalty](images/lockpenalty.png)

Disruptor use lock free memory barrier to synchronize sequence of ring buffer. Two cores in CPU talk to each other directly via memory barrier to exchange resource state, so as to avoid accessing shared locks.

memory barrier    
![Memory barrier](images/memorybarrier.png)

# Reference
1. [LMAX disruptor on github](http://lmax-exchange.github.io/disruptor/)
1. [Disruptor Technical Paper](http://disruptor.googlecode.com/files/Disruptor-1.0.pdf)
1. [Mechanical sympathy](http://mechanical-sympathy.blogspot.com/)
1. [Disruptor presentation@QCON](http://www.infoq.com/presentations/LMAX)
1. [Moore's law@Wikipedia](http://en.wikipedia.org/wiki/Moore_Law)
1. [Amdahl's law@Wikipedia](http://en.wikipedia.org/wiki/Amdahl's_law)
1. [Ring buffer@Wikipedia](http://en.wikipedia.org/wiki/Circular_buffer)
1. [Memory Barriers: a Hardware View for Software Hackers](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&ved=0CDAQFjAA&url=http%3A%2F%2Fwww.rdrop.com%2F~paulmck%2Fscalability%2Fpaper%2Fwhymb.2009.04.05a.pdf&ei=cJfUUrS5BM2CogSWy4DIAw&usg=AFQjCNHA6MjCwoLJFe5dQTye_uI9dimTNg&sig2=tU3Q4l6EbVsTMf7fOqH5hA)
