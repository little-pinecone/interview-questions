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

## Stack and Heap

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

# Collections

The core collection interfaces are shown in the image below:

![core collection interfaces screenshot](assets/img/core-collection-interfaces.png)

General facts:

1. Note, that a `Map` is not a *true* `Collection`. However, it's generally discussed among other collections for the sake of simplicity.
2. Collection interfaces are `generic`. Therefore, we should specify the type of object contained in the collection we're declaring.
3. The modification operations in each interface are designated optional. Consult documentation of the selected 
implementation to make sure you won't get `UnsupportedOperationException` by calling a method that isn't implemented. 

Below you'll find the implementations of the collection interfaces

## List

* insertion order
* duplicate values allowed
* we control where in the list each element is inserted and can access elements by their index
* fail-fast behavior of an iterator cannot be guaranteed in the presence of unsynchronized concurrent modification
* the `List.of()` and `List.copyOf` methods return unmodifiable sets
* the `Collections.synchronizedList(...);` provides synchronization
* general-purpose implementations: `ArrayList`, `LinkedList`
* special-purpose implementations: `CopyOnWriteArrayList`
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html)

**ArrayList**

* optimized for storing objects, fast random access
* permits null elements
* resizable, the `ensureCapacity` method may reduce the amount of incremental reallocation
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html)

**LinkedList**

* optimized for data manipulation (inserting and deleting elements)
* permits null elements
* when accessed through the Queue interface, acts as a FIFO queue
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/LinkedList.html)

**CopyOnWriteArrayList**

* a thread-safe variant of `ArrayList`
* performant if read-only operations vastly outnumber mutative operations and the set size stays small
* we iterate over an immutable snapshot of the content
* the iterator won't reflect changes to the list since the iterator was created
* all mutative operations are implemented by making a fresh copy
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/CopyOnWriteArrayList.html)

**Time complexity**

| List                 | add()                           | remove()                   | get()                      | contains() | next()                     | Underlying data structure |
|:---------------------|:--------------------------------|:---------------------------|:---------------------------|:-----------|:---------------------------|:--------------------------|
| ArrayList            | O(n) (*)                        | O(n)                       | *O(1)*{: .text-green-000 } | O(n)       | *O(1)*{: .text-green-000 } | Array                     |
| LinkedList           | *O(1)*{: .text-green-000 } (**) | *O(1)*{: .text-green-000 } | O(n)                       | O(n)       | *O(1)*{: .text-green-000 } | Linked List               |
| CopyOnWriteArrayList | O(n)                            | O(n)                       | *O(1)*{: .text-green-000 } | O(n)       | *O(1)*{: .text-green-000 } | Array                     |

(*) O(1) if a copy is not needed
{: .fs-2 }

(**) O(n) if `add(index i)`
{: .fs-2 }

## Set

* unique values
* unspecified behaviour if the value of an element is changed in a manner that affects `equals` (caution when storing mutable objects)
* fail-fast behavior of an iterator cannot be guaranteed in the presence of unsynchronized concurrent modification
* the `Set.of()` and `Set.copyOf` methods return unmodifiable sets
* * the `Collections.synchronizedSet(...);` provides synchronization
* general-purpose implementations: `HashSet`, `LinkedHashSet`, `TreeSet`
* special-purpose implementations: `EnumSet`, `CopyOnWriteArraySet`
* concurrent implementations: `ConcurrentSkipListSet`
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Set.html)

**HashSet**

* not ordered
* permits the null element
* don't set the initial capacity too high if iteration performance is important
* useful for counting, reports
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/HashSet.html)

**LinkedHashSet**

* insertion order
* permits the null element
* iteration times for this class are unaffected by capacity
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/LinkedHashSet.html)

**TreeSet**

* elements are ordered using their natural ordering, or by a Comparator provided at creation time
* element comparison is done using `compareTo()` method and not `equals` like in the Set interface
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeSet.html)

**EnumSet**

* all elements must come from a single enum type
* doesn't permit the null element (`NullPointerException`)
* very compact and efficient, operations are likely (though not guaranteed) to be much faster than their `HashSet` counterparts
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/EnumSet.html)

**CopyOnWriteArraySet**

* uses an internal `CopyOnWriteArrayList` for all of its operations. Thus, it shares the same basic properties.
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/CopyOnWriteArraySet.html)

**ConcurrentSkipListSet**

* insertion, removal, and access operations safely execute concurrently by multiple threads
* elements are ordered using their natural ordering, or by a Comparator provided at creation time
* doesn't permit the null element
* iterating in ascending order is faster that in descending order
*[javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ConcurrentSkipListSet.html)

**Time complexity**

| Set                   | add()                          | remove()                       | contains()                     | next()                         | size()                     | Underlying data structure                                                         |
|:----------------------|:-------------------------------|:-------------------------------|:-------------------------------|:-------------------------------|:---------------------------|:----------------------------------------------------------------------------------|
| HashSet               | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h/n)                         | *O(1)*{: .text-green-000 } | Hash Map                                                                          |
| LinkedHashSet         | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | Hash Table + Linked List                                                          |
| TreeSet               | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 } | [Red-black tree](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree)            |
| EnumSet               | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | Bit Vector                                                                        |
| CopyOnWriteArraySet   | O(n)                           | O(n)                           | O(n)                           | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | CopyOnWriteArrayList                                                              |
| ConcurrentSkipListSet | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | ConcurrentSkipListMap                                                             |

## Map

* unique keys
* unspecified behaviour if the key is changed in a manner that affects `equals` (caution when using mutable objects as keys)
* fail-fast behavior of an iterator cannot be guaranteed in the presence of unsynchronized concurrent modification
* a map's contents can be viewed as a set of keys (`keySet`), collection of values (`values`), or set of key-value mappings (`entrySet`)
* the `Map.of()`, `Map.copyOf` and `Map.ofEntries` methods return unmodifiable sets
* the `Collections.synchronizedMap(...);` provides synchronization
* general-purpose implementations: `HashMap`, `LinkedHashMap`, `TreeMap`
* special-purpose implementations: `EnumSet`, `WeakHashMap`, `IdentityHashMap`
* concurrent implementations: `ConcurrentHashMap`, `ConcurrentSkipListMap`
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Map.html)

**HashMap**

* not ordered
* permits null values and null key
* don't set the initial capacity too high if iteration performance is important
* useful for counting by the key, local cache
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/HashMap.html)

**LinkedHashMap**

* insertion order
* in access-ordered linked hash maps, merely querying the map with `get` is a structural modification and affects iteration order
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/LinkedHashMap.html)

**TreeMap**

* elements are ordered using their natural ordering, or by a Comparator provided at creation time
* element comparison is done using `compareTo()` method and not `equals` like in the Set interface
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeMap.html)

**EnumMap**

* all keys must come from a single enum type
* maps are maintained in the natural order of their keys (the order in which the enum constants are declared)
* doesn't permit null keys (`NullPointerException`), permits null values
* very compact and efficient
* weakly consistent iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/EnumSet.html)

**IdentityHashMap**

* intentionally violates Map's general contract, designed for use only in the rare cases wherein reference-equality semantics are required
* a typical use of this class is topology-preserving object graph transformations (e.g. serialization or deep-copying)
or maintaining proxy objects (e.g. maintaining a proxy object for each object in the program being debugged)
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/IdentityHashMap.html)

**WeakHashMap**

* an entry will automatically be removed when its key is no longer in ordinary use
* fail-fast iterator
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/WeakHashMap.html)

**ConcurrentHashMap**

*Speedups for parallel compared to sequential forms are common but not guaranteed. 
Parallel operations involving brief functions on small maps may execute more slowly than sequential forms if the 
underlying work to parallelize the computation is more expensive than the computation itself. 
Similarly, parallelization may not lead to much actual parallelism if all processors are busy performing unrelated tasks.*

* even though all operations are thread-safe, retrieval operations do not entail locking, and there is not any support 
for locking the entire table in a way that prevents all access
* all arguments to all task methods must be non-null
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html)

**ConcurrentSkipListMap**

* insertion, removal, update and access operations safely execute concurrently by multiple threads
* elements are ordered using their natural ordering, or by a Comparator provided at creation time
* doesn't permit null keys or values
* iterating keys in ascending order is faster that in descending order
* weakly consistent iterator
*[javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ConcurrentSkipListMap.html)

**Time complexity**

| Map                   | get()                          | containsKey()                  | next()                         | Underlying data structure |
|:----------------------|:-------------------------------|:-------------------------------|:-------------------------------|:--------------------------|
| HashMap               | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h / n)                       | Hash Table                |
| LinkedHashMap         | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | Hash Table + Linked List  |
| TreeMap               | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | Red-black tree            |
| EnumMap               | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | Array                     |
| IdentityHashMap       | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h / n)                       | Array                     |
| WeakHashMap           | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h / n)                       | Hash Table                |
| ConcurrentHashMap     | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 }     | O(h / n)                       | Hash Tables               |
| ConcurrentSkipListMap | *O(log(n))*{: .text-blue-000 } | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 }     | Skip List                 |

## Queue and Dequeue

* Queue: usually FIFO, whatever the ordering used, the head of the queue is the element that would be removed by a call to remove or poll
* Dequeue: both FIFO or LIFO, all new elements can be inserted, retrieved and removed at both ends
* general-purpose implementations: `PriorityQueue`, `ArrayDeque`, `LinkedList`
* concurrent implementations: `ConcurrentLinkedQueue`, `LinkedBlockingQueue`, `ArrayBlockingQueue`, 
`PriorityBlockingQueue`, `DelayQueue`. `SynchronousQueue`, `LinkedBlockingDeque`, `LinkedTransferQueue`
* [Queue javadoc](https://docs.oracle.com/javase/tutorial/collections/interfaces/queue.html), [Dequeue javadoc](https://docs.oracle.com/javase/tutorial/collections/interfaces/deque.html)

**PriorityQueue**

* heap implementation of an unbounded priority queue
* not ordered, use `Arrays.sort(pq.toArray())` if you need ordered traversal
* elements are ordered using their natural ordering, or by a Comparator provided at creation time
* doesn't permit the null element
* [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/PriorityQueue.html)

**ArrayDeque**

* efficient, resizable array implementation of the Deque interface
* has no capacity restrictions, grows as necessary to support usage
* doesn't permit the null element
* faster than `Stack` when used as a stack, faster than `LinkedList` when used as a queue
* fail-fast iterator
*  [javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayDeque.html)

**Time complexity matrix**

| Queue                 | offer()                        | peek()                     | poll()                         | remove()                   | size()                     | Underlying data structure |
|:----------------------|:-------------------------------|:---------------------------|:-------------------------------|:---------------------------|:---------------------------|:--------------------------|
| LinkedList            | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 } | Array                     |
| PriorityQueue         | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 } | *O(log(n))*{: .text-blue-000 } | O(n)                       | *O(1)*{: .text-green-000 } | Priority Heap             |
| ArrayDequeue          | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | *O(1)*{: .text-green-000 } | Linked List               |
| ConcurrentLinkedQueue | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | O(n)                       | Linked List               |
| LinkedBlockingQueue   | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | *O(1)*{: .text-green-000 } | Linked List               |
| ArrayBlockingQueue    | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | *O(1)*{: .text-green-000 } | Array                     |
| PriorityBlockingQueue | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 } | *O(log(n))*{: .text-blue-000 } | O(n)                       | *O(1)*{: .text-green-000 } | Priority Heap             |
| DelayQueue            | *O(log(n))*{: .text-blue-000 } | *O(1)*{: .text-green-000 } | *O(log(n))*{: .text-blue-000 } | O(n)                       | *O(1)*{: .text-green-000 } | Priority Heap             |
| SynchronousQueue      | *O(1)*{: .text-green-000 }     | *O(1)*{: .text-green-000 } | *O(1)*{: .text-green-000 }     | O(n)                       | *O(1)*{: .text-green-000 } | None!                     |

# Generics
