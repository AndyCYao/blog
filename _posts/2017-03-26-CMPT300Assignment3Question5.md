---
layout: default
title: "Assignment 3 CMPT 300 An Analysis of Linux Scalability to May Cores"
date: 2017-03-26
categories: SFU
---
# Conceptual Questions:
1.) When a process spends a lot of execution time in the kernel, is that good or bad for scalability issue?

# Abstract:
The authors claim the linux community should not give up on traditional Linux OS with regards to parallel computing. The authors were able to scale the OS with a technique call *sloppy counters*

# Introduction
There are other kernel designs propose for scalability, including BarrelFish, Corey and Fos. 
But the others in this paper wants to see if they can scale using traditional kernel design.

They used linux because of its traditional kernel design. the apps they used to test are  Map Reduce, gmake, Exim mail server, PostGress SQL. collectively call MOSBENCH. 

The author identified three broad kinds of scalability problem for the MOSBENCH apps.

1. Problem caused by Linux kernel implementation
2. Problem caused by Application user level design.
3. Problem caused by way the app uses Linux kernel services. 

*Sloppy Counter* is a technique the authors used, it augments share counters to make some uses more scalable without having to change all uses of the shared counter. 

# Related Work.
Other similar projects include Stanford FLASH, Shared memory machine. Scale software include  scalable locking, wait-free syncronization, multiprocessor scheduler, fast message passing using shared memory. 

# The Mosbench Application.
The applications they chose to test are from two groups, 

1.) those that did not scale well on Linux (memcached, apache, metis, a MapReduce Library)
2.) those designed for parallel execution and are kernel intensive (gmake, postgresSQL, Exim, Psearchy)

The authors artifically made the work intense on the kernel, especially on the kernel components (network stack, file name cache, page cache, etc. )

# Kernel Optimzations
## Scalability Tutorial
In theory, if workload consists of unrelated tasks, then increasing number of cores allows higher throughput due to tasks running in parallel. 

However, in real life, parallel tasks usually interacts, and interaction forces serial execution. 

Amadahl's Law summarize that however small the serial portion. It will eventually prevent added cores from increasing perfomrance. 

Some typical problems include:
	
1. Tasks may lock a shared datastructure. increase number of cores increase the lock wait time. 
2. Tasks may compete for space in a limited size shared hardware cache. increasing number of cores increase cache miss rate.  
3. Task may be competing for limited share hardware resources. 
4. There are too few tasks to keep core busy, increasing the number of cores lead to more idle cores. 

Typical hardware cache coherence protocol does the following.

1. Each core has a data cache for its own use. 
2. When core writes data that other cores have cached, the protocol forces the write to wait while it finds the cache copies and invalidates them. 
3. When a core reads data that another core had written, the protocol needs to find the cache that holds the modified data, and fetches the data to the reading core. 

The author claims cache miss is a sympton of scaling problem

There is a trade off between algorithm efficiency and scalability. To scale, there maybe use of inefficient algorithm., so that each core is busily used and don't use the lock mechamism. 

## Multicore packet processing

## Sloppy Counter 
Sloppy counter maintain the invariant that the sum of per-core counters and number of resources equals the value in the shared counter. 

## Lock-free comparison. 
The authors used the linux's lock-free page cache look up protocol to do lock free comparisons. 

# Evaluation
The authors show that with their above customization of the kernel, they were able to scale most of the MOSBENCH apps. They acknowledged that there are factors that are not scalable, such as non-parallelizable components in application or underlying hardware, imperfect load balance, hardware bottlenecks. 