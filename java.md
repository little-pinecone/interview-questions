---
layout: default
title: Java
nav_order: 2
---

# Contents
{:.no_toc}

* TOC
{:toc}

# Memory management

JVM takes care of memory management leaving only some configuration decisions to developers.

## Describe Stack and Heap

These are the two memory spaces in JVM.

`Stack`

1. Created for each thread, therefore thread safe.
2. Stores local variables, references to the objects on the heap, methods.
3. Memory is freed when the methods finish executing and the variables are no longer needed, ordered (LIFO).
4. Easy, fast access.
5. `StackOverFlowError` when resources are exhausted, the available memory can be configured with the `-Xss` option.

`Heap`

1. Created with an application, it's shared among all threads.
2. Stores the objects and their fields.
3. Needs complex memory management techniques like Garbage Collectors to free memory space, there is no order or memory 
allocation pattern.
4. Access is slower.
5. `OutOfMemoryError` when resources are exhausted, the available memory can be configured with the `-Xmx` and `-Xms` 
options.

## Generations

Heap memory stores objects according to their age.

`Young Generation`

1. Stores new objects.
2. Is cleared with `minor garbage collection`.
3. If an object lives long enough it is promoted to Old Generation.

`Old Generation`

1. Stores the objects promoted from Young Generation.
2. Is cleared with `major garbage collection`.

## Metaspace

It is an off-heap [memory manager used to allocate memory for class metadata](https://wiki.openjdk.java.net/display/HotSpot/Metaspace). 
When a class loader gets collected, all class metadata it accumulated is released.

## Garbage collection

Automatic management for clearing the memory from all objects from Young and Old Generations that are no longer needed.

`Garbage-First (G1) Garbage Collector` is selected by default on most system configurations. This collector is mostly 
concurrent, it scales well and keeps garbage collection pauses short.

Manual selection of a garbage collector:

1. Adjust the size of the heap space first.
2. If that doesn't help, follow the [Oracle guidelines for selecting a collector](https://docs.oracle.com/en/java/javase/17/gctuning/available-collectors.html#GUID-9E4A6B11-BB94-424F-90EF-401287A1C333):
* `serial` collector for application with small data set, run on a single processor, without pause-time requirements;
* `parallel` collector when peak app performance is a priority and there are no pause-time requirements;
* `mostly concurrent` collector when response time is more important than overall throughput and short collection pauses
are required;
* `fully concurrent` collector when response time is a high priority.

## Read more

* [How Java memory management works – a quick introduction](https://codesoapbox.dev/how-java-memory-management-works-a-quick-introduction/)
* [Introduction to Garbage Collection Tuning](https://docs.oracle.com/en/java/javase/17/gctuning/introduction-garbage-collection-tuning.html#GUID-326EB4CF-8C8C-4267-8355-21AB04F0D304)

# Data structures

## Lists

`ArrayList`

* when you need to store objects, have fast random access
* not fixed size, grows by 50%
* allows nulls
* insertion order
* duplicate values

`LinkedList`

* when you need to manipulate data
* stores objects and references
* insertion order
* duplicate values

## Sets

`HashSet`

* when you want to create a set of unique values (counting, reports)
* fail-fast iterator
* allows nulls
* not ordered
* unique values

`LinkedHashSet`
`TreeSet`

##Maps

`HashMap`
`LinkedHashMap`
`TreeMap`

## Time complexity matrix

The following table is based on the [Runtime Complexity of Java Collections](https://gist.github.com/psayre23/c30a821239f4818b0709)gist.

| List                 | add()                      | remove()                   | get()                      | contains() | next()                     | Underlying data structure |
|:---------------------|:---------------------------|:---------------------------|:---------------------------|:-----------|:---------------------------|:--------------------------|
| ArrayList            | *O(1)*{: .text-green-000 } | O(n)                       | *O(1)*{: .text-green-000 } | O(n)       | *O(1)*{: .text-green-000 } | Array                     |
| LinkedList           | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 } | O(n)                       | O(n)       | *O(1)*{: .text-green-000 } | Linked List               |
| CopyOnWriteArrayList | O(n)                       | O(n)                       | *O(1)*{: .text-green-000 } | O(n)       | *O(1)*{: .text-green-000 } | Array                     |

| Set                   | add()                          | remove()                       | contains()                     | next()                         | size()                     | Underlying data structure |
|:----------------------|:-------------------------------|:-------------------------------|:-------------------------------|:-------------------------------|:---------------------------|:--------------------------|
| HashSet               | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h/n)                         | *O(1)*{: .text-green-000 } | Hash Table                |
| LinkedHashSet         | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | Hash Table + Linked List  |
| EnumSet               | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | Bit Vector                |
| TreeSet               | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 } | Red-black tree            |
| CopyOnWriteArraySet   | O(n)                           | O(n)                           | O(n)                           | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | Array                     |
| ConcurrentSkipListSet | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | Skip List                 |

| Queue                 | offer()                        | peek()                     | poll()                         | remove()                   | size()                     | Underlying data structure |
|:----------------------|:-------------------------------|:---------------------------|:-------------------------------|:---------------------------|:---------------------------|:--------------------------|
| PriorityQueue         | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 } | *O(log(n))*{: .text-blue-000 } | O(n)                       | *O(1)*{: .text-green-000 } | Priority Heap             |
| LinkedList            | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 } | Array                     |
| ArrayDequeue          | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | *O(1)*{: .text-green-000 } | Linked List               |
| ConcurrentLinkedQueue | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | O(n)                       | Linked List               |
| ArrayBlockingQueue    | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | *O(1)*{: .text-green-000 } | Array                     |
| PriorityBlockingQueue | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 } | *O(log(n))*{: .text-blue-000 } | O(n)                       | *O(1)*{: .text-green-000 } | Priority Heap             |
| SynchronousQueue      | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | *O(1)*{: .text-green-000 } | None!                     |
| DelayQueue            | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 } | *O(log(n))*{: .text-blue-000 } | O(n)                       | *O(1)*{: .text-green-000 } | Priority Heap             |
| LinkedBlockingQueue   | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | *O(1)*{: .text-green-000 } | Linked List               |

| Map                   | get()                          | containsKey()                  | next()                         | Underlying data structure |
|:----------------------|:-------------------------------|:-------------------------------|:-------------------------------|:--------------------------|
| HashMap               | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h / n)                       | Hash Table                |
| LinkedHashMap         | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | Hash Table + Linked List  |
| IdentityHashMap       | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h / n)                       | Array                     |
| WeakHashMap           | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h / n)                       | Hash Table                |
| EnumMap               | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | Array                     |
| TreeMap               | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | Red-black tree            |
| ConcurrentHashMap     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h / n)                       | Hash Tables               |
| ConcurrentSkipListMap | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 }     | Skip List                 |
