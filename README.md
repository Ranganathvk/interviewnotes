# Interview Notes

Interview questions and answers.

**Browse by area:** [INIT.md](INIT.md) · **Java modules (split notes):** [Java/README.md](Java/README.md)

> Legacy detailed TOC below. Links are **relative to this repo**. Sections for Cucumber and Agile point to files not in this workspace.

# Object-oriented programming(OOPS)

  - [OOPS](oop/oops.md#object-oriented-programmingoops)
    * [Abstraction](oop/oops.md#abstraction)
    * [Encapsulation](oop/oops.md#encapsulation)
    * [Polymorphism](oop/oops.md#polymorphism)
    * [Abstract Class](oop/oops.md#abstract-class)
    * [Does Abstract class have constructor?](oop/oops.md#does-abstract-class-have-constructor)
    * [Abstract class Vs Interface](oop/oops.md#abstract-class-vs-interface)
    * [What to choose – interface or abstract class](oop/oops.md#what-to-choose--interface-or-abstract-class)
    * [Why Java 8 has introduced default methods?](oop/oops.md#why-java-8-has-introduced-default-methods)
    * [Why Java 8 has introduced static methods?](oop/oops.md#why-java-8-has-introduced-static-methods)
    * [Why Java does not allow multiple inheritance?](oop/oops.md#why-java-does-not-allow-multiple-inheritance)
    * [Method Overloading and Overriding](oop/oops.md#method-overloading-and-overriding)
      * [Method Overloading](oop/oops.md#method-overloading)
      * [Rules for Method Overloading](oop/oops.md#rules-for-method-overloading)
      * [Method Overriding](oop/oops.md#method-overriding)
      * [Rules for Method Overriding](oop/oops.md#rules-for-method-overriding)
    * [Association](oop/oops.md#association)
    * [Aggregation](oop/oops.md#aggregation)
    * [Composition](oop/oops.md#composition)
    * [Association vs. Aggregation vs. Composition](oop/oops.md#association-vs-aggregation-vs-composition)

# Java

  - [Java module index](Java/README.md)
  - [Object class](Java/basic/object-class.md)
  - [String](Java/basic/strings.md)
  - [Exceptions](Java/exceptions/exceptions.md)
  - [Generics](Java/generics/generics.md)
  - [HashMap internals](Java/collection/hashmap-internals.md)
  - [Concurrency synchronizers](Java/multi-threading/concurrency-synchronizers.md)
  - [Executors & thread pools](Java/multi-threading/executors.md)
  - [Virtual threads](Java/multi-threading/virtual-threads.md)
  - [JVM internals](Java/jvm/jvm.md)
  - [Memory & performance](Java/jvm/memory-and-performance.md)
  - [Garbage collection](Java/gc/gc.md)
  - [Basic](Java/basic/java-basics.md)

    * [Java 8](Java/basic/java8.md#java-8)
      * [Java 8 Features](Java/basic/java-basics.md#java-8-features)
      * [New features](Java/basic/java8.md#new-features)
      * [Lambda Expressions](Java/basic/java8.md#lambda-expressions)
      * [Lambda Expressions](Java/basic/java8.md#lambda-expressions)
        1. [Examples of lambda expressions](Java/basic/java8.md#examples-of-lambda-expressions)
        1. [Why use Lambda Expression](Java/basic/java8.md#why-use-lambda-expression)
        1. [Lambda Expressions Syntax](Java/basic/java8.md#java-lambda-expression-syntax-)
        1. [Lambda Expressions Examples](Java/basic/java8.md#lambda-expressions-examples)
      * [Stream](Java/basic/java8.md#stream)
        * [Stream features](Java/basic/java8.md#stream-features)
      * [Intermediate and Terminal operations](Java/basic/java8.md#intermediate-and-terminal-operations)
        * [Intermediate vs Terminal operations](Java/basic/java8.md#intermediate-vs-terminal-operations)
        * [Intermediate Operations](Java/basic/java8.md#intermediate-operations)
        * [Terminal Operations](Java/basic/java8.md#terminal-operations-)

      * [Functional Interface](Java/basic/java8.md#functional-interface)
        * [Custom Functional Interface](Java/basic/java8.md#custom-functional-interface)
        * [Predefined Functional Interfaces](Java/basic/java8.md#predefined-functional-interfaces)
        * [Predicate](Java/basic/java8.md#predicate)
        * [Function](Java/basic/java8.md#function)
        * [Supplier](Java/basic/java8.md#supplier)
        * [Consumer](Java/basic/java8.md#consumer)
        * [BiFunction](Java/basic/java8.md#bifunction)
        * [BiConsumer](Java/basic/java8.md#biconsumer)
      * [Method References](Java/basic/java8.md#method-references)
        * [Method References Types](Java/basic/java8.md#method-references-types)
      * Test
    * [Why String is Immutable?](Java/basic/java-basics.md#why-string-is-immutable)
    * [StringBuffer and StringBuilder](Java/basic/java-basics.md#stringbuffer-and-stringbuilder)
    * [equals and hashcode contract](Java/basic/java-basics.md#equals-and-hashcode-contract)
    * [Comparable and Comparator interfaces](Java/basic/java-basics.md#comparable-and-comparator-interfaces)
      * [Comparable vs Comparator](Java/basic/java-basics.md#comparable-and-comparator)
    * [static keyword](Java/basic/java-basics.md#static-keyword)
    * [Shallow Copy and Deep Copy](Java/basic/java-basics.md#shallow-copy-and-deep-copy)
    * [Serialization and De-serialization](Java/basic/java-basics.md#serialization-and-de-serialization)
    * [Serialization scenarios with Inheritance](Java/basic/java-basics.md#serialization-scenarios-with-inheritance)
    * [How to make a class Immutable?](Java/basic/java-basics.md#how-to-make-a-class-immutable)
    * [Class loaders in Java](Java/basic/java-basics.md#class-loaders-in-java)
    * [Garbage Collector](Java/basic/java-basics.md#garbage-collector)
      * [How does the garbage collector work?](Java/basic/java-basics.md#how-does-the-garbage-collector-work)
      * [Types of garbage collectors](Java/basic/java-basics.md#types-of-garbage-collectors)
      * [Garbage Collection and types of Garbage Collectors](Java/basic/java-basics.md#garbage-collection-and-types-of-garbage-collectors)
      * [final, finally and finalize()](Java/basic/java-basics.md#final-finally-and-finalize)
    * [String, StringBuffer, StringBuilder](Java/basic/java-basics.md#string-stringbuffer-stringbuilder)
    * [Reflection](Java/basic/java-basics.md#reflection)
    * [Exceptions](Java/basic/java-basics.md#exceptions)
      * [Checked and Unchecked Exception](Java/basic/java-basics.md#checked-and-unchecked-exception)
      * [Custom exception](Java/basic/java-basics.md#custom-exception)
      * [OutOfMemoryError](Java/basic/java-basics.md#outofmemoryerror)
    * [Generics](Java/basic/java-basics.md#generics-in-java)
      * [Benefits of Generics](Java/basic/java-basics.md#benefits-of-generics)
      * [Collection Framework examples for Generics.](Java/basic/java-basics.md#collection-framework-examples-for-generics)
      * [Type Parameter Naming Conventions](Java/basic/java-basics.md#type-parameter-naming-conventions)
      * [Multiple Type Parameters](Java/basic/java-basics.md#multiple-type-parameters)
    * [Immutable object](Java/basic/java-basics.md#immutable-object)
      * [Benefits of immutable objects](Java/basic/java-basics.md#benefits-of-immutable-objects)
      * [Immutable Class](Java/basic/java-basics.md#immutable-class)
      * [Wrong way to write an immutable class](Java/basic/java-basics.md#wrong-way-to-write-an-immutable-class)
      * [Right way to write an immutable class](Java/basic/java-basics.md#right-way-to-write-an-immutable-class)
    * [Pass by reference and Pass by value](Java/basic/java-basics.md#pass-by-reference-and-pass-by-value)
    * [Shallow cloning and Deep cloning](Java/basic/java-basics.md#shallow-cloning-and-deep-cloning)
    * [Instance variable and a Static variable](Java/basic/java-basics.md#instance-variable-and-a-static-variable)
    * [Local variables vs Instance and static variables](Java/basic/java-basics.md#local-variables-vs-instance-and-static-variables)
    * [Access Modifiers](Java/basic/java-basics.md#access-modifiers)
    * [Volatile keyword](Java/basic/java-basics.md#volatile-keyword)
    * [synchronized vs volatile](Java/basic/java-basics.md#synchronized-vs-volatile)
    * Test
  - [Collection Framework](Java/collection/collection-framework.md#collection-framework)

    * [ArrayList](Java/collection/collection-framework.md#arraylist)
      * [Accessing elements](Java/collection/collection-framework.md#accessing-elements)
      * [Removing elements](Java/collection/collection-framework.md#removing-elements)
      * [Iterating over an ArrayList](Java/collection/collection-framework.md#iterating-over-an-arraylist)
      * [Searching for elements in an ArrayList](Java/collection/collection-framework.md#searching-for-elements-in-an-arraylist)
      * [Sorting an ArrayList](Java/collection/collection-framework.md#sorting-an-arraylist)
    * [HashMap](Java/collection/collection-framework.md#hashmap) · [HashMap deep dive](Java/collection/hashmap-internals.md)
      * [HashMap Internal working](Java/collection/collection-framework.md#hashmap-internal-working)
      * Sort Map By Value Example
      * [How put() method of HashMap works internally](Java/collection/collection-framework.md#how-put-method-of-hashmap-works-internally)
      * [How  HashMap get() method works internally](Java/collection/collection-framework.md#how--hashmap-get-method-works-internally)
      * [When null Key is inserted in a HashMap](Java/collection/collection-framework.md#when-null-key-is-inserted-in-a-hashmap)
      * [HashMap implementation changes in Java 8](Java/collection/collection-framework.md#hashmap-implementation-changes-in-java-8)
      * [Interal working of put() and get() methods of HashMap](Java/collection/collection-framework.md#interal-working-of-put-and-get-methods-of-hashmap-)
      * [HashMap collisions](Java/collection/collection-framework.md#hashmap-collisions)
    * [equals and hashCode method in HashMap when the key is a custom class](Java/collection/collection-framework.md#equals-and-hashcode-method-in-hashmap-when-the-key-is-a-custom-class)
    * [Concurrent HashMap](Java/collection/collection-framework.md#concurrent-hashmap)
      * [ConcurrentHashMap vs Synchronized HashMap](Java/collection/collection-framework.md#concurrenthashmap-vs-synchronized-hashmap)
    * [HashSet class](Java/collection/collection-framework.md#hashset-class)
    * [TreeMap](Java/collection/collection-framework.md#treemap)
    * [TreeSet](Java/collection/collection-framework.md#treeset)
    * [fail-safe and fail-fast iterators](Java/collection/collection-framework.md#fail-safe-and-fail-fast-iterators)
  - [Multi threading](Java/multi-threading/multi-threading.md#multi-threading) · [Virtual threads](Java/multi-threading/virtual-threads.md) · [Memory & performance](Java/jvm/memory-and-performance.md)

    * [Life Cycle of a Thread](Java/multi-threading/multi-threading.md#life-cycle-of-a-thread)
    * [Creating a Thread](Java/multi-threading/multi-threading.md#creating-a-thread)
    * [Life Cycle of a Thread](Java/multi-threading/multi-threading.md#life-cycle-of-a-thread)
    * [Creating a Thread](Java/multi-threading/multi-threading.md#creating-a-thread)
    * [Difference between Runnable vs Thread](Java/multi-threading/multi-threading.md#difference-between-runnable-vs-thread)
    * [synchronized keyword](Java/multi-threading/multi-threading.md#synchronized-keyword)
    * [static synchronization](Java/multi-threading/multi-threading.md#static-synchronization)
    * [What does join() method?](Java/multi-threading/multi-threading.md#q-what-does-join-method)
    * [Deadlock](Java/multi-threading/multi-threading.md#deadlock)
    * [How to avoid deadlock?](Java/multi-threading/multi-threading.md#how-to-avoid-deadlock)
    * [What will happen if I directly call the run() method and not the start() method to execute a thread?](Java/multi-threading/multi-threading.md#what-will-happen-if-i-directly-call-the-run-method-and-not-the-start-method-to-execute-a-thread)
    * [Once a thread has been started can it be started again?](Java/multi-threading/multi-threading.md#once-a-thread-has-been-started-can-it-be-started-again)
    * [Why wait, notify and notifyAll methods are defined in the Object class instead of Thread class?](Java/multi-threading/multi-threading.md#why-wait-notify-and-notifyall-methods-are-defined-in-the-object-class-instead-of-thread-class)
    * [Why wait(), notify(), notifyAll() methods must be called from synchronized block?](Java/multi-threading/multi-threading.md#why-wait-notify-notifyall-methods-must-be-called-from-synchronized-block)
    * [wait() vs sleep() methods](Java/multi-threading/multi-threading.md#wait-vs-sleep-methods)
    * [Executor Framework](Java/multi-threading/multi-threading.md#executor-framework)
      * [High level concurrency features Executor framework](Java/multi-threading/multi-threading.md#high-level-concurrency-features-executor-framework)
      * [Executor Interface](Java/multi-threading/multi-threading.md#executor-interface)
      * [ExecutorService Interface](Java/multi-threading/multi-threading.md#executorservice-interface)
      * [ExecutorService Interface Examples](Java/multi-threading/multi-threading.md#executorservice-interface-examples)
      * [Different Between execute() and submit() Methods](Java/multi-threading/multi-threading.md#different-between-execute-and-submit-methods)
      * [ScheduledExecutorService Interface](Java/multi-threading/multi-threading.md#scheduledexecutorservice-interface)
      * [Future Interface](Java/multi-threading/multi-threading.md#future-interface)
      * [Executor Framework-2](Java/multi-threading/multi-threading.md#executor-framework-2)

# Data Structures and Algorithms

* [Lists](ds-algo/data-structure.md#lists)
  * [ArrayList](ds-algo/data-structure.md#arraylist)
  * [LinkedList](ds-algo/data-structure.md#linkedlist)
  * [Stack](ds-algo/data-structure.md#stack)
  * [Vector](ds-algo/data-structure.md#vector)
  * [CopyOnWriteArrayList](ds-algo/data-structure.md#copyonwritearraylist)
  * [Collections.synchronizedList](ds-algo/data-structure.md#collectionssynchronizedlist)
* [Sets](ds-algo/data-structure.md#sets)
  * [HashSet](ds-algo/data-structure.md#hashset)
    * [LinkedHashSet](ds-algo/data-structure.md#linkedhashset)
    * [TreeSet](ds-algo/data-structure.md#treeset)
    * [ConcurrentSkipListSet](ds-algo/data-structure.md#concurrentskiplistset)
    * [CopyOnWriteArraySet](ds-algo/data-structure.md#copyonwritearrayset)
    * [EnumSet](ds-algo/data-structure.md#enumset)
* [Maps](ds-algo/data-structure.md#maps)
  * [HashMap](ds-algo/data-structure.md#hashmap)
  * [HashMap implementation details](ds-algo/data-structure.md#hashmap-implementation-details)
  * [LinkedHashMap](ds-algo/data-structure.md#linkedhashmap)
  * [Hashtable](ds-algo/data-structure.md#hashtable)
  * [ConcurrentHashMap](ds-algo/data-structure.md#concurrenthashmap)
  * [TreeMap](ds-algo/data-structure.md#treemap)
  * [ConcurrentSkipListMap](ds-algo/data-structure.md#concurrentskiplistmap)
* [Queues](ds-algo/data-structure.md#queues)
  * [LinkedList](ds-algo/data-structure.md#linkedlist-1)
  * [ArrayBlockingQueue](ds-algo/data-structure.md#arrayblockingqueue)
  * [LinkedBlockingQueue](ds-algo/data-structure.md#linkedblockingqueue)
  * [ConcurrentLinkedQueue](ds-algo/data-structure.md#concurrentlinkedqueue)
  * [Deque classes](ds-algo/data-structure.md#deque-classes)
  * [PriorityQueue](ds-algo/data-structure.md#priorityqueue)
  * [PriorityBlockingQueue](ds-algo/data-structure.md#priorityblockingqueue)
  * [DelayQueue](ds-algo/data-structure.md#delayqueue)
  * [SynchronousQueue](ds-algo/data-structure.md#synchronousqueue)
* [equals and hashCode](ds-algo/data-structure.md#equals-and-hashcode)
* [Hierarchy and classes](ds-algo/data-structure.md#hierarchy-and-classes)


* Frequently asked
  * Stack
  * CustomStack
  * LinkedListStack
  * LinkedListReversal
  * MyLinkedList
  * PrimesPrimeChecker
  * RemoveDuplicateFromString
  * Palindrome
  * FindLargestThree
  * FibonacciSeries
  * BalancedBracketsUsingString
  * BalancedBracketsUsingDeque
  * [Anagram](ds-algo/Problems.md#anagram)
    * [Problem analysis](ds-algo/Problems.md#problem-analysis)
    * [Code implementation (sorting)](ds-algo/Problems.md#code-implementation-sorting)
    * [Code implementation (hash)](ds-algo/Problems.md#code-implementation-hash)
  * Anagrams (code sample — external repo)
    * Anagrams
    * ValidAnagram
    * PrintAnagramTogether
    * AnagramOfFirstAsSubstring
    * GroupAnagramsTogether
  
  * [Find the median of two positively ordered arrays](ds-algo/Problems.md#find-the-median-of-two-positively-ordered-arrays)
    * [What is the median of an array](ds-algo/Problems.md#what-is-the-median-of-an-array)
    * [Problem analysis](ds-algo/Problems.md#problem-analysis-1)
    * [Code](ds-algo/Problems.md#code)
    * [Code (Brute Force approach)](ds-algo/Problems.md#code-brute-force-approach)
  * [Subarray Sum Equals K](ds-algo/Problems.md#subarray-sum-equals-k)
    * [Problem analysis](ds-algo/Problems.md#problem-analysis-2)
    * [Code - Optimization by Hashmap](ds-algo/Problems.md#code---optimization-by-hashmap)
    * [Code - Brute Force approach](ds-algo/Problems.md#code---brute-force-approach)


* [Data Types](ds-algo/Data-Types.md#data-types)
  * [System-defined data types (Primitive data types)](ds-algo/Data-Types.md#system-defined-data-types-primitive-data-types)
  * [User defined data types](ds-algo/Data-Types.md#user-defined-data-types)
* [Data Structures](ds-algo/Data-Types.md#data-structures)
  * [Linear data structures](ds-algo/Data-Types.md#linear-data-structures)
  * [Non – linear data structures](ds-algo/Data-Types.md#non--linear-data-structures)
  * [Commonly Used Rates of Growth](ds-algo/Data-Types.md#commonly-used-rates-of-growth)
  * [Arrays](ds-algo/Data-Types.md#arrays)
    * [Advantages of Arrays](ds-algo/Data-Types.md#advantages-of-arrays)
    * [Disadvantages of Arrays](ds-algo/Data-Types.md#disadvantages-of-arrays)
    * [Dynamic Arrays](ds-algo/Data-Types.md#dynamic-arrays)
      
  * [Linked List](ds-algo/Data-Types.md#linked-list)
      * [Advantages of Linked List](ds-algo/Data-Types.md#advantages-of-linked-lists)
      * [Disadvantages of Linked List](ds-algo/Data-Types.md#disadvantages-of-linked-lists)
      * [Comparison of Linked Lists with Arrays & Dynamic Arrays](ds-algo/Data-Types.md#comparison-of-linked-lists-with-arrays--dynamic-arrays)
      * Test
* [Sorting](ds-algo/Sorting.md#sorting)
  * [Bubble Sort](ds-algo/Sorting.md#bubble-sort)
    * [Implementation](ds-algo/Sorting.md#implementation)
    * [Performance](ds-algo/Sorting.md#performance)
  * [Selection Sort](ds-algo/Sorting.md#selection-sort)
    * [Implementation](ds-algo/Sorting.md#implementation-1)
    * [Performance](ds-algo/Sorting.md#performance-1)
  * [Insertion Sort](ds-algo/Sorting.md#insertion-sort)
    * [Advantages](ds-algo/Sorting.md#advantages-1)
    * [Algorithm](ds-algo/Sorting.md#algorithm-1)
    * [Implementation](ds-algo/Sorting.md#implementation-2)
    * [Example](ds-algo/Sorting.md#example)
    * [Analysis](ds-algo/Sorting.md#analysis)
    * [Performance](ds-algo/Sorting.md#performance-2)
    * [Comparisons to Other Sorting Algorithms](ds-algo/Sorting.md#comparisons-to-other-sorting-algorithms)
  * [Shell Sort](ds-algo/Sorting.md#shell-sort)
    * Implementation
    * Performance
  * [Merge Sort](ds-algo/Sorting.md#merge-sort)
    * Implementation
    * Performance
  * [Heap Sort](ds-algo/Sorting.md#heap-sort)
    * Implementation
    * Performance
  * [Quicksort](ds-algo/Sorting.md#quicksort)
    * Implementation
    * Performance
  * [Tree Sort](ds-algo/Sorting.md#tree-sort)
    * Implementation
    * Performance
  * [Comparison of Sorting Algorithms](ds-algo/Sorting.md#comparison-of-sorting-algorithms)
  * Test
  * Test
  * Test
  * Test
  * Test
  
# Spring

- [Spring](spring/spring.md#spring)
  * [Dependency Injection](spring/spring.md#dependency-injection)
    * [Types of Dependency Injection](spring/spring.md#types-of-dependency-injection)
    * [Constructor-based DI](spring/spring.md#constructor-based-di-)
    * [Setter-method injection](spring/spring.md#setter-method-injection)
    * [Constructor Vs Setter injection ](spring/spring.md#constructor-vs-setter-injection)
  * [BeanFactory and ApplicationContext](spring/spring.md#beanfactory-and-applicationcontext)
  * [Spring Bean life-cycle](spring/spring.md#spring-bean-life-cycle)
  * [Spring Bean Scopes](spring/spring.md#spring-bean-scopes)
    * [What happens when we inject a prototype scope bean in a singleton scope bean?](spring/spring.md#what-happens-when-we-inject-a-prototype-scope-bean-in-a-singleton-scope-bean)
    * [How to inject a prototype scope bean in a singleton scope bean?](spring/spring.md#how-to-inject-a-prototype-scope-bean-in-a-singleton-scope-bean)
    * [Stereotype Annotations](spring/spring.md#stereotype-annotations)
    * [@Controller vs @RestController annotation](spring/spring.md#controller-vs-restcontroller-annotation:)
    * [@Qualifier annotation](spring/spring.md#qualifier-annotation)
    * [@Transactional annotation](spring/spring.md#transactional-annotation)
    * [@ControllerAdvice annotation](spring/spring.md#controlleradvice-annotation)
    * [@Bean annotation](spring/spring.md#bean-annotation)
    * [@Component vs @Bean annotation](spring/spring.md#component-vs-bean-annotation)
    * [Spring Boot Security using OAuth2 with JWT](spring/spring.md#spring-boot-security-using-oauth2-with-jwt)
    * [OAuth2 Terminology](spring/spring.md#oauth2-terminology)
    * [Json Web Token(JWT)](spring/spring.md#json-web-tokenjwt)
    * [Spring Boot Rest Authentication with JWT Token Flow](spring/spring.md#spring-boot-rest-authentication-with-jwt-token-flow)
  * [Web server vs  application server](spring/spring.md#web-server-and--application-server)
  * [Spring Transaction Management](spring/Spring-Transaction-Management.md#spring-transaction-management)
    * [Global Transactions](spring/Spring-Transaction-Management.md#global-transactions)
    * [Local Transactions](spring/Spring-Transaction-Management.md#local-transactions)
    * [Programmatic Approach:](spring/Spring-Transaction-Management.md#programmatic-approach)
    * [Declarative Approach (@Transactional)](spring/Spring-Transaction-Management.md#declarative-approach-transactional)
    * [propagation](spring/Spring-Transaction-Management.md#propagation)
    * [isolation](spring/Spring-Transaction-Management.md#isolation)
    * [readOnly](spring/Spring-Transaction-Management.md#isolation)
    * [timeout](spring/Spring-Transaction-Management.md#isolation)

# Spring Annotations

- [@Autowired](spring/annotations.md#autowired)
- [@Inject vs @Autowired](spring/annotations.md#inject-vs-autowired)
- [Component Scanning](spring/annotations.md#component-scanning)
- [@ComponentScan](spring/annotations.md#componentscan)
  - [@ComponentScan Without Arguments](spring/annotations.md#componentscan-without-arguments)
  - [@ComponentScan With Arguments](spring/annotations.md#componentscan-with-arguments)
  - [@ComponentScan with Exclusions](spring/annotations.md#componentscan-with-exclusions)
  - [@ComponentScan in a Spring-Boot application](spring/annotations.md#componentscan-in-a-spring-boot-application)
- [Difference between @Component, @Repository & @Service annotations?](spring/annotations.md#difference-between-component-repository--service-annotations)
  - [@Repository](spring/annotations.md#repository)
  - [@Service](spring/annotations.md#service)
  - [@Controller](spring/annotations.md#controller)
  - Test

# Spring MVC

- [Spring MVC](spring/spring-mvc.md#spring-mvc)
- [Spring MVC flow](spring/spring-mvc.md#spring-mvc-flow)
- [Spring Web Annotations](spring/spring-mvc.md#spring-web-annotations)
  * [@RequestMapping](spring/spring-mvc.md#requestmapping)
  * [@RequestBody](spring/spring-mvc.md#requestbody)
  * [@PathVariable](spring/spring-mvc.md#pathvariable)
  * [@RequestParam](spring/spring-mvc.md#requestparam)
  * [@ResponseBody](spring/spring-mvc.md#responsebody)
  * [@ExceptionHandler](spring/spring-mvc.md#exceptionhandler)
    * [@ResponseStatus](spring/spring-mvc.md#responsestatus)
  * [@Controller](spring/spring-mvc.md#controller)
  * [@RestController](spring/spring-mvc.md#restcontroller)
  * [@ModelAttribute](spring/spring-mvc.md#modelattribute)
  * [@CrossOrigin](spring/spring-mvc.md#crossorigin)
  * [Scheduler in cluster environment or run on multiple instances](spring/spring-mvc.md#scheduler-in-cluster-environment-or-run-on-multiple-instances)
    * [Spring Scheduled Task running in clustered environment with ShedLock](spring/spring-mvc.md#spring-scheduled-task-running-in-clustered-environment-with-shedlock)
    * [Configuration](spring/spring-mvc.md#configuration)
    * [Creating Tasks](spring/spring-mvc.md#creating-tasks)
    * Spring
    * [Execute a Quartz Job only once in a multi-instance environment](spring/spring-mvc.md#execute-a-quartz-job-only-once-in-a-multi-instance-environment)

# Spring Boot

- [Spring Boot](spring/spring-boot.md#spring-boot)
- [Spring Boot Primary Goals](spring/spring-boot.md#spring-boot-primary-goals)
- [Running Spring boot app at different port on server startup](spring/spring-boot.md#running-spring-boot-app-at-different-port-on-server-startup)
- [Key Spring Boot features](spring/spring-boot.md#key-spring-boot-features)
- [Spring Boot Starters ](spring/spring-boot.md#spring-boot-starters)
- [Spring Boot Autoconfiguration ](spring/spring-boot.md#spring-boot-autoconfiguration)
- [Easy-to-Use Embedded Servlet Container Support ](spring/spring-boot.md#easy-to-use-embedded-servlet-container-support)
- [Spring Boot Annotations](spring/spring-boot.md#spring-boot-annotations)
  - [@SpringBootApplication](spring/spring-boot.md#springbootapplication)
    - [@EnableAutoConfiguration](spring/spring-boot.md#enableautoconfiguration)
    - [@ConditionalOnClass and @ConditionalOnMissingClass](spring/spring-boot.md#conditionalonclass-and-conditionalonmissingclass)
    - [@ConditionalOnBean and @ConditionalOnMissingBean](spring/spring-boot.md#conditionalonbean-and-conditionalonmissingbean)
    - [@ConditionalOnProperty](spring/spring-boot.md#conditionalonproperty)
    - [@ConditionalOnResource](spring/spring-boot.md#conditionalonresource)
    - [@ConditionalOnWebApplication and @ConditionalOnNotWebApplication](spring/spring-boot.md#conditionalonwebapplication-and-conditionalonnotwebapplication)
    - [@ConditionalExpression](spring/spring-boot.md#conditionalonwebapplication-and-conditionalonnotwebapplication)
    - [@Conditional](spring/spring-boot.md#conditional)
  - [Spring Boot Actuator](spring/spring-boot.md#spring-boot-actuator)
    - [pring-boot-actuator maven dependency](spring/spring-boot.md#spring-boot-actuator-maven-dependency)
    - [Predefined Endpoints](spring/spring-boot.md#predefined-endpoints)
    - [/info Endpoint](spring/spring-boot.md#info-endpoint)
    - [/metrics Endpoint](spring/spring-boot.md#metrics-endpoint)
    - [Custom Endpoint](spring/spring-boot.md#custom-endpoint)
- [Building an Application with Spring Boot](spring/spring-boot.md#building-an-application-with-spring-boot)
  - [Starting with Spring Initializer](spring/spring-boot.md#starting-with-spring-initializer)
  - [Create a Simple Web Application](spring/spring-boot.md#create-a-simple-web-application)
  - [Create an Application class](spring/spring-boot.md#create-an-application-class)
  - [Run the Application](spring/spring-boot.md#run-the-application)
  - [Add Unit Tests](spring/spring-boot.md#add-unit-tests)
  - [Add Production-grade Services](spring/spring-boot.md#add-production-grade-services)
- [Spring Boot CRUD Web Application with Thymeleaf, Spring MVC, Spring Data JPA, Hibernate, MySQL](spring/spring-boot.md#spring-boot-crud-web-application-with-thymeleaf-spring-mvc-spring-data-jpa-hibernate-mysql)
    - [Create Spring Boot Project](spring/spring-boot.md#create-spring-boot-project)
    - [Maven Dependencies](spring/spring-boot.md#maven-dependencies)
    - [Configure and Setup MySQL Database](spring/spring-boot.md#configure-and-setup-mysql-database)
    - [Model Layer - Create JPA Entity](spring/spring-boot.md#model-layer---create-jpa-entity--)
    - [Repository Layer](spring/spring-boot.md#repository-layer---employeerepositoryjava)
    - [Service Layer](spring/spring-boot.md#service-layer)
    - [Controller Layer](spring/spring-boot.md#controller-layer---employeecontrollerjava)
    - [View Layer: Thymeleaf](spring/spring-boot.md#view-layer-thymeleaf)
    - [Run Spring application and demo](spring/spring-boot.md#run-spring-application-and-demo)
    - [ Unit Testing REST APIs](spring/spring-boot.md#unit-testing-rest-apis)
    - [ CrudRepository vs. JpaRepository](spring/spring-boot.md#crudrepository-vs-jparepository)


    

# Spring AOP(Aspect Oriented Programming)
 - [Spring AOP(Aspect Oriented Programming)](spring/spring-aop.md#spring-aop)

# HTTP methods overview

- [HTTP methods overview](misc/http.md#http-methods-overview)
  - [Idempotency in HTTP](misc/http.md#idempotency-in-http)
  - [Safe Methods](misc/http.md#safe-methods)
  - [Why DELETE method is defined as idempotent](misc/http.md#why-delete-method-is-defined-as-idempotent)
  - [Why PATCH method is not idempotent](misc/http.md#why-patch-method-is-not-idempotent)
  - [Why is PUT idempotent?](misc/http.md#why-is-put-idempotent)





# HTTP Status Codes

- [HTTP Status Codes](spring/http-Verbs.md#http-status-codes )
  - [1×× Informational](spring/http-Verbs.md#1-informational)
  - [2×× Success](spring/http-Verbs.md#2-success)

    - [200 OK](spring/http-Verbs.md#2-success)
    - [201 Created](spring/http-Verbs.md#2-success)
    - [202 Accepted](spring/http-Verbs.md#2-success)
    - [204 No Content](spring/http-Verbs.md#2-success)
    - [205 Reset Content](spring/http-Verbs.md#2-success)
  - [3×× Redirection](spring/http-Verbs.md#3-redirection)

    - [300 Multiple Choices](spring/http-Verbs.md#3-redirection)
  - [4×× Client Error](spring/http-Verbs.md#4-client-error)
  - [5×× Server Errorl](spring/http-Verbs.md#5-server-error)

# Hibernate

- [Hibernate](hibernate/hibernate.md)
  * [JPA vs Hibernate](hibernate/hibernate.md#jpa-vs-hibernate)
  * [@Entity annotation](hibernate/hibernate.md#entity-annotation)
  * [@Id & @GeneratedValue](hibernate/hibernate.md#id--generatedvalue)
  * [get() and load() methods of Hibernate Session](hibernate/hibernate.md#get-and-load-methods-of-hibernate-session)
  * [Session and SessionFactory](hibernate/hibernate.md#session-and-sessionfactory)
  * [First Level and Second Level Cache](hibernate/hibernate.md#first-level-and-second-level-cache)
  * Test

# Angular

- [Single-page application(SPA) vs. Multiple-page application(MPA)](angular/Angular.md#single-page-applicationspa-vs-multiple-page-applicationmpa)
  - [Single-page application(SPA)](angular/Angular.md#single-page-application)
  - [Pros of the Single-Page Application](angular/Angular.md#pros-of-the-single-page-application)
  - [Cons of the Single-Page Application](angular/Angular.md#cons-of-the-single-page-application)
  - [Multiple-page application(MPA)](angular/Angular.md#multiple-page-applicationmpa)
  - [Pros of the Multiple-Page Application](angular/Angular.md#pros-of-the-multiple-page-application)
  - [Cons of the multiple-page application](angular/Angular.md#cons-of-the-multiple-page-application)
- [Angular](angular/Angular.md)
  * [AngularJS vs Angular](angular/Angular.md#angularjs-vs-angular)
      * [AngularJS](angular/Angular.md#a-angularjs)
      * [Angular](angular/Angular.md#b-angular)
      * [Why Angular](angular/Angular.md#why-angular)
        * [Components](angular/Angular.md#components)
        * [Templates](angular/Angular.md#templates)
        * [Dependency injection](angular/Angular.md#dependency-injection)
        * [Angular Routing and Navigation](angular/Angular.md#angular-routing-and-navigation)
    * [Angular Module](angular/Angular.md#angular-module)
    * [Bootstrapping Module](angular/Angular.md#bootstrapping-module)
    * [JIT vs AOT compilation](angular/Angular.md#jit-vs-aot-compilation)
    * [Advantages of AOT](angular/Angular.md#advantages-of-aot)
    * [What are the ways to control AOT compilation?](angular/Angular.md#what-are-the-ways-to-control-aot-compilation)
    * [How to optimize loading large data in angular?](angular/Angular.md#how-to-optimize-loading-large-data-in-angular)
    * [How an Angular application gets started or loaded?](angular/Angular.md#how-an-angular-application-gets-started-or-loaded)
    * [Angular Services](angular/angular-services.md#angular-services)
    * [What is Interpolation](angular/Angular.md#what-is-interpolation)
    * [Angular Routing and Navigation](angular/angular-routing.md#1-angular-routing-and-navigation)
    * [Angular Component](angular/angular-components.md)
      * [What Is an Angular Component?](angular/angular-components.md#what-is-an-angular-component)
        * [Template](angular/angular-components.md#template)
        * [Class](angular/angular-components.md#class)
        * [Component Metadata](angular/angular-components.md#component-metadata)
      * [Communication Between Components](angular/angular-components.md#communication-between-components)
        * [Parent to Child](angular/angular-components.md#communication-between-components)
        * [Child to Parent](angular/angular-components.md#communication-between-components)
        * [Child to Parent](angular/angular-components.md#communication-between-components)
        * [Unrelated Components](angular/angular-components.md#communication-between-components)
    * [Angular Directive](angular/angular-directive.md)
      * [Components Directives](angular/angular-directive.md#components-directives)
      * [Structural Directives](angular/angular-directive.md#structural-directives)
      * [Attribute Directives](angular/angular-directive.md#attribute-directives)
      * [How to Create Custom Directives?](angular/angular-directive.md#how-to-create-custom-directives)
      * [Structural Directive vs Attribute Directive](angular/angular-directive.md#structural-directive-vs-attribute-directive)
      * [Component vs Directive](angular/angular-directive.md#component-vs-directive)
    * [Angular Pipes](angular/angular-pipes.md)
      * [Syntax to use Pipes in Angular Application](angular/angular-pipes.md#syntax-to-use-pipes-in-angular-application)
      * [Types of Pipes in Angular](angular/angular-pipes.md#types-of-pipes-in-angular)
        * [1. Built-in Pipes](angular/angular-pipes.md#1-built-in-pipes)
        * [2.Custom Pipes](angular/angular-pipes.md#2custom-pipes)
      * [Parameterized Pipe](angular/angular-pipes.md#parameterized-pipe)
      * [Custom Pipe](angular/angular-pipes.md#custom-pipe)
      * [Pure vs Impure Pipe](angular/angular-pipes.md#pure-vs-impure-pipe)
    * [Angular Data Binding](angular/angular-data-binding.md)
      * [1. Interpolation](angular/angular-data-binding.md)
      * [2. Property binding](angular/angular-data-binding.md)
      * [3. Event binding](angular/angular-data-binding.md)
      * [4. Two-way data binding](angular/angular-data-binding.md)
    * [Angular Lazy Loading](angular/angular-lazy-loading.md)
    * [Angular Lifecycle Hooks](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [ngOnChanges](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [ngOnInit](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [ngDoCheck](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [ngAfterContentInit](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [ngAfterContentChecked](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [ngAfterViewInit](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [ngAfterViewChecked](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [ngOnDestroy](angular/angular-lifecycle-hooks.md#angular-lifecycle-hooks)
      * [constructor() vs ngOnInit()](angular/angular-lifecycle-hooks.md#constructor-vs-ngoninit)
    * [RxJS](angular/RxJS.md#rxjs)
      * [Utility functions provided by RxJS](angular/RxJS.md#utility-functions-provided-by-rxjs)
      * [RxJS Operator](angular/RxJS.md#rxjs-operator)
      * [What is subscribing](angular/RxJS.md#what-is-subscribing)
      * [Observable](angular/RxJS.md#observable)
      * [Observer](angular/RxJS.md#observer)
      * [Error handling in observables](angular/RxJS.md#error-handling-in-observables)
      * [Observable creation functions](angular/RxJS.md#observable-creation-functions)
      * [What will happen if you do not supply handler for observer?](angular/RxJS.md#what-will-happen-if-you-do-not-supply-handler-for-observer)
      * [Promise vs Observable](angular/RxJS.md#promise-vs-observable)
      * [Async Pipe](angular/RxJS.md#async-pipe)
      * [HttpClient](angular/RxJS.md#httpclient)
        * [Error handling](angular/RxJS.md#error-handling)

# Design Patterns

- [Design Patterns](design-pattern/design-pattern.md)
  - [Creational Design Patterns](design-pattern/creational-design-pattern.md)
    * [Simple Factory](design-pattern/creational-design-pattern.md#-simple-factory)
    * [Factory Method](design-pattern/creational-design-pattern.md#-factory-method)
    * [Abstract Factory](design-pattern/creational-design-pattern.md#-abstract-factory)
    * [Builder](design-pattern/creational-design-pattern.md#-builder)
    * [Prototype](design-pattern/creational-design-pattern.md#-prototype)
    * [Singleton](design-pattern/creational-design-pattern.md#-singleton)
    * [singleton class vs a static class](design-pattern/creational-design-pattern.md#singleton-class-vs-a-static-class)
    - [Structural Design Patterns](design-pattern/structural-design-pattern.md)
      * [Adapter](design-pattern/structural-design-pattern.md#-adapter)
      * [Bridge](design-pattern/structural-design-pattern.md#-bridge)
      * [Composite](design-pattern/structural-design-pattern.md#-composite)
      * [Decorator](design-pattern/structural-design-pattern.md#-decorator)
      * [Facade](design-pattern/structural-design-pattern.md#-facade)
      * [Flyweight](design-pattern/structural-design-pattern.md#-flyweight)
      * [Proxy](design-pattern/structural-design-pattern.md#-proxy)
    - [Behavioral Design Patterns](design-pattern/behavioral-design-pattern.md)
      * [Chain of Responsibility](design-pattern/behavioral-design-pattern.md#-chain-of-responsibility)
      * [Command](design-pattern/behavioral-design-pattern.md##-command)
      * [Iterator](design-pattern/behavioral-design-pattern.md##-iterator)
      * [Mediator](design-pattern/behavioral-design-pattern.md##-mediator)
      * [Memento](design-pattern/behavioral-design-pattern.md##-memento)
      * [Observer](design-pattern/behavioral-design-pattern.md##-observer)
      * [Visitor](design-pattern/behavioral-design-pattern.md##-visitor)
      * [Strategy](design-pattern/behavioral-design-pattern.md##-strategy)
      * [State](design-pattern/behavioral-design-pattern.md##-state)
      * [Template Method](design-pattern/behavioral-design-pattern.md##-template-method)

* [Designing a Java/J2EE application](design-pattern/design-pattern.md#designing-a-javaj2ee-application)

  * [Create a tiered architecture](design-pattern/design-pattern.md#create-a-tiered-architecture)
  * [Create a data model](design-pattern/design-pattern.md#create-a-data-model)
  * [Create a design model](design-pattern/design-pattern.md#create-a-design-model)
  * [Design considerations when decomposing business use cases into relevant classes](design-pattern/design-pattern.md#design-considerations-when-decomposing-business-use-cases-into-relevant-classes)
  * [Vertical slicing](design-pattern/design-pattern.md#vertical-slicing)
  * [Ensure the system is configurable](design-pattern/design-pattern.md#ensure-the-system-is-configurable)
  * [Design considerations during design, development and deployment phases](design-pattern/design-pattern.md#design-considerations-during-design-development-and-deployment-phases)
* [Identifying performance and/or memory issues in your Java/J2EE application](design-pattern/design-pattern.md#identifying-performance-andor-memory-issues-in-your-javaj2ee-application)

# Microservices

  - [Microservices](micro-services/micro-services.md)
    * [SOA(Service-oriented architecture) vs Microservices](micro-services/micro-services.md#soaservice-oriented-architecture-vs-microservices)
    * [Advantages of Microservices Architecture](micro-services/micro-services.md#advantages-of-microservices-architecture)
    * [Disadvantages of Microservices](micro-services/micro-services.md#disadvantages-of-microservices)
    * [Challenges of Microservices Architecture](micro-services/micro-services.md#challenges-of-microservices-architecture)
    * [Gateway](micro-services/micro-services.md#gateway)
    * [Microservices Monitor](micro-services/micro-services.md#microservices-monitor)
    * [Microservices vs Monolithic Architecture](micro-services/micro-services.md#microservices-vs-monolithic-architecture)
    * [How does Microservice Architecture work?](micro-services/micro-services.md#how-does-microservice-architecture-work)
    * [Communicating Between Microservices](micro-services/micro-services.md#communicating-between-microservices)
    * [SOAP vs REST](micro-services/micro-services.md#soap-vs-rest)
    * [Eureka Naming Server](micro-services/micro-services.md#eureka-naming-server)
    * [Zuul](micro-services/micro-services.md#zuul)
    * [Zipkin](micro-services/micro-services.md#zipkin)
    * [Versioning of microservices](micro-services/micro-services.md#versioning-of-microservices)
    * [How to send custom business errors or exceptions from a RESTful microservice to client application?](micro-services/micro-services.md#how-to-send-custom-business-errors-or-exceptions-from-a-restful-microservice-to-client-application)
    * [What are best practices for microservices architecture?](micro-services/micro-services.md#what-are-best-practices-for-microservices-architecture)
    * [Microservices caching](micro-services/micro-services.md#microservices-caching)
    * [SOLID (solid design principles)](micro-services/micro-services.md#solid-solid-design-principles)
    * [How frequent a microservice be released into production?](micro-services/micro-services.md#how-frequent-a-microservice-be-released-into-production)
    * [How to achieve zero-downtime during the deployments?](micro-services/micro-services.md#how-to-achieve-zero-downtime-during-the-deployments)

      * [Blue-green deployment](micro-services/micro-services.md#blue-green-deployment)
    * [How to slowly move users from older version of application to newer version?](micro-services/micro-services.md#how-to-slowly-move-users-from-older-version-of-application-to-newer-version)
    * [How will you monitor fleet of microservices in production?](micro-services/micro-services.md#how-will-you-monitor-fleet-of-microservices-in-production)
    * [How will you troubleshoot a failed API request that is spread across multiple services?](micro-services/micro-services.md#how-will-you-troubleshoot-a-failed-api-request-that-is-spread-across-multiple-services)
    * [What are different layers of a single microservice?](micro-services/micro-services.md#what-are-different-layers-of-a-single-microservice)
    * [Spring Cloud](micro-services/micro-services.md#spring-cloud)
    * [application.yml vs bootstrap.yml](micro-services/micro-services.md#applicationyml-vs-bootstrapyml)
    * [Netflix Eureka Server : implement service discovery in microservices architecture](micro-services/micro-services.md#netflix-eureka-server--implement-service-discovery-in-microservices-architecture)

      * [Netflix Eureka Server](micro-services/micro-services.md#netflix-eureka-server)
      * [Consul Server](micro-services/micro-services.md#consul-server)
      * [How does Eureka Server work?](micro-services/micro-services.md#how-does-eureka-server-work)
    * [Externalize configuration in a distributed system](micro-services/micro-services.md#externalize-configuration-in-a-distributed-system)
    * [@RefreshScope : Refresh configuration changes on the fly in Spring Cloud environment](micro-services/micro-services.md#refresh-configuration-changes-on-the-fly-in-spring-cloud-environment)
    * [Achieve client side load balancing in Spring Microservices using Spring Cloud](micro-services/micro-services.md#achieve-client-side-load-balancing-in-spring-microservices-using-spring-cloud)
    * [client side load-balancer Ribbon in your microservices architecture](micro-services/micro-services.md#client-side-load-balancer-ribbon-in-your-microservices-architecture)
    * [Use both LoadBalanced as well as normal RestTemplate object in the single microservice](micro-services/micro-services.md#use-both-loadbalanced-as-well-as-normal-resttemplate-object-in-the-single-microservice)
    * [Use of Eureka for service discovery in Ribbon Load Balancer](micro-services/micro-services.md#use-of-eureka-for-service-discovery-in-ribbon-load-balancer)
    * [Managing transactions](micro-services/Transactions.md#managing-transactions)

      * [ACID Transactions](micro-services/Transactions.md#acid-transactions)
      * [What is a distributed transaction?](micro-services/Transactions.md#what-is-a-distributed-transaction)
      * [Two-phase commit (2pc) pattern](micro-services/Transactions.md#two-phase-commit-2pc-pattern)
      * [Benefits of using 2pc](micro-services/Transactions.md#benefits-of-using-2pc)
      * [Disadvantages of using 2pc](micro-services/Transactions.md#disadvantages-of-using-2pc)
      * [Saga pattern](micro-services/Transactions.md#saga-pattern)
      * [Advantages of the Saga pattern](micro-services/Transactions.md#advantages-of-the-saga-pattern)
      * [Disadvantages of the Saga pattern](micro-services/Transactions.md#disadvantages-of-the-saga-pattern)
      * Test
      * Test
    * [Security in Microservices](micro-services/Security%20in%20Microservices.md#security-in-microservices)

      * [Why Basic Authentication is not suitable in Microservices Context?](micro-services/Security%20in%20Microservices.md#why-basic-authentication-is-not-suitable-in-microservices-context)
      * [Why OAuth2?](micro-services/Security%20in%20Microservices.md#why-oauth2)
      * [OAuth2 Roles](micro-services/Security%20in%20Microservices.md#oauth2-roles)
      * [OAuth 2.0 grant types (OAuth flows)](micro-services/Security%20in%20Microservices.md#oauth-20-grant-types-oauth-flows)
      * [When shall I use resource owner credentials?](micro-services/Security%20in%20Microservices.md#when-shall-i-use-resource-owner-credentials)
      * [When shall I use Authorization Code grant?](micro-services/Security%20in%20Microservices.md#when-shall-i-use-authorization-code-grant)
      * [When shall I use client credentials?](micro-services/Security%20in%20Microservices.md#when-shall-i-use-client-credentials)
      * [OAuth2 and Microservices](micro-services/Security%20in%20Microservices.md#oauth2-and-microservices)
      * [What is JWT?](micro-services/Security%20in%20Microservices.md#what-is-jwt)
      * [What are use cases for JWT?](micro-services/Security%20in%20Microservices.md#what-are-use-cases-for-jwt)
      * [How does JWT looks like?](micro-services/Security%20in%20Microservices.md#how-does-jwt-looks-like)
      * [What is AccessToken and RefreshToken?](micro-services/Security%20in%20Microservices.md#what-is-accesstoken-and-refreshtoken)
      * [How to use a RefreshToken to request a new AccessToken?](micro-services/Security%20in%20Microservices.md#how-to-use-a-refreshtoken-to-request-a-new-accesstoken)
      * [How to call the protected resource using AccessToken?](micro-services/Security%20in%20Microservices.md#how-to-call-the-protected-resource-using-accesstoken)
      * [Can a refreshToken be never expiring? How to make refreshToken life long valid?](micro-services/Security%20in%20Microservices.md#can-a-refreshtoken-be-never-expiring-how-to-make-refreshtoken-life-long-valid)
      * [Generate AccessToken for Client Credentials.](micro-services/Security%20in%20Microservices.md#generate-accesstoken-for-client-credentials)
      * [Why there is no RefreshToken support in Oauth2 Client Credentials workflow?](micro-services/Security%20in%20Microservices.md#why-there-is-no-refreshtoken-support-in-oauth2-client-credentials-workflow)
      * [Security in inter-service communication](micro-services/Security%20in%20Microservices.md#security-in-inter-service-communication)

# API Gateway

- [API Gateway](micro-services/API-Gateway.md#api-gateway)
- [How to retry failed requests at some other available instance using Client Side Load Balancer?](micro-services/API-Gateway.md#how-to-retry-failed-requests-at-some-other-available-instance-using-client-side-load-balancer)
- [Circuit Breaker](micro-services/API-Gateway.md#circuit-breaker)
- [What is difference between using a Circuit Breaker and a naive approach where we try/catch a remote method call and protect for failures?](micro-services/API-Gateway.md#what-is-difference-between-using-a-circuit-breaker-and-a-naive-approach-where-we-trycatch-a-remote-method-call-and-protect-for-failures)
- [Circuit Breaker Pattern](micro-services/API-Gateway.md#circuit-breaker-pattern)
- [Open, Closed and Half-Open states of Circuit Breaker](micro-services/API-Gateway.md#open-closed-and-half-open-states-of-circuit-breaker)
- [Use-cases for Circuit Breaker Pattern](micro-services/API-Gateway.md#use-cases-for-circuit-breaker-pattern)
- [Benefits of using Circuit Breaker Pattern](micro-services/API-Gateway.md#benefits-of-using-circuit-breaker-pattern)
- [What is Hystrix?](micro-services/API-Gateway.md#what-is-hystrix)
- [Features of Hystrix library](micro-services/API-Gateway.md#features-of-hystrix-library)
- [How to use Hystrix for fallback execution?](micro-services/API-Gateway.md#how-to-use-hystrix-for-fallback-execution)
- [When not to use Hystrix fallback on a particular microservice?](micro-services/API-Gateway.md#when-not-to-use-hystrix-fallback-on-a-particular-microservice)
- [ignore certain exceptions in Hystrix fallback execution](micro-services/API-Gateway.md#ignore-certain-exceptions-in-hystrix-fallback-execution)
- [Request Collapsing feature in Hystrix](micro-services/API-Gateway.md#request-collapsing-feature-in-hystrix)
- [Circuit Breaker vs Hystrix](micro-services/API-Gateway.md#circuit-breaker-and-hystrix)
- [Where exactly should I use Circuit Breaker Pattern?](micro-services/API-Gateway.md#where-exactly-should-i-use-circuit-breaker-pattern)
- [Where it should not be used?](micro-services/API-Gateway.md#where-it-should-not-be-used)

# Kafka

- [Kafka](kafka/kafka.md)
  * [Advantages of Kafka](kafka/kafka.md#advantages-of-kafka)
  * [Offset in Kafka](kafka/kafka.md#offset-in-kafka)
  * [Consumer group in Kafka](kafka/kafka.md#consumer-group-in-kafka)
  * [ZooKeeper in Kafka](kafka/kafka.md#zookeeper-in-kafka)
  * [Can I use Kafka without Zookeeper?](kafka/kafka.md#can-i-use-kafka-without-zookeeper)
  * [What is Zookeeper in Kafka? Can we use Kafka without Zookeeper?](kafka/kafka.md#what-is-zookeeper-in-kafka-can-we-use-kafka-without-zookeeper)
  * [Partition in Kafka](kafka/kafka.md#partition-in-kafka)
  * [APIs of Kafka](kafka/kafka.md#apis-of-kafka)
    * [Load balancing of the server in Kafka](kafka/kafka.md#load-balancing-of-the-server-in-kafka)
    * [Retention period in Kafka cluster](kafka/kafka.md#retention-period-in-kafka-cluster)
    * [RabbitMQ vs Apache Kafka](kafka/kafka.md#rabbitmq-vs-apache-kafka)
    * [Explain Apache Kafka Use Cases?](kafka/kafka.md#explain-apache-kafka-use-cases)
    * [Kafka Cluster, and its key benefits](kafka/kafka.md#kafka-cluster-and-its-key-benefits)
    * [Replicas in Kafka](kafka/kafka.md#replicas-in-kafka)
    * [Starting a Kafka server](kafka/kafka.md#starting-a-kafka-server)
    * [In the Producer, when does QueueFullException occur?](kafka/kafka.md#in-the-producer-when-does-queuefullexception-occur)
    * [Confluent Kafka vs. Apache Kafka](kafka/kafka.md#Confluent-Kafka-vs-Apache-Kafka)

# MongoDB

- [MongoDB](mongo-db/mongo-db.md#mongodb)
  * [Key Components](mongo-db/mongo-db.md#key-components)
  * [Document](mongo-db/mongo-db.md#document)
  * [Collection](mongo-db/mongo-db.md#collection)
  * [Namespace](mongo-db/mongo-db.md#namespace)
  * [Databases in MongoDB](mongo-db/mongo-db.md#databases-in-mongodb)
  * [Mongo Shell](mongo-db/mongo-db.md#mongo-shell)
  * [Advantages of MongoDB:](mongo-db/mongo-db.md#advantages-of-mongodb)
  * [Features of MongoDB](mongo-db/mongo-db.md#features-of-mongodb)
  * [Indexes in MongoDB](mongo-db/mongo-db.md#indexes-in-mongodb)
  * [MongoDB](mongo-db/mongo-db.md)

# Database

- [Database](database/database.md#database)
- [SQL](database/database.md#sql)
  - [Joins](database/database.md#joins)
    - [Inner join](database/database.md#inner-join)
    - [Left outer join](database/database.md#left-outer-join)
    - [Right outer join](database/database.md#right-outer-join)
    - [Full join](database/database.md#full-join)
    - [Cross join](database/database.md#cross-join)
    - [Self Join](database/database.md#self-join)
  - [Keys](database/database.md#keys)
  - [Indexes](database/database.md#indexes)
    - [Non-clustered index](database/database.md#indexes)
    - [Clustered index](database/database.md#indexes)
    - [Composite index](database/database.md#indexes)
    - [Cardinality](database/database.md#indexes)
    - [Bitmap-index](database/database.md#indexes)
    - [Data structures used](database/database.md#indexes)
    - [SQL - Indexes](database/database.md#sql---indexes)
    - [The CREATE INDEX Command](database/database.md#the-create-index-command)
    - [Unique Indexes](database/database.md#unique-indexes)
    - [Composite Indexes](database/database.md#composite-indexes)
    - [Implicit Indexes](database/database.md#implicit-indexes)
    - [The DROP INDEX Command](database/database.md#the-drop-index-command)
    - [When should indexes be avoided?](database/database.md#when-should-indexes-be-avoided)
    
  - [Queries](database/database.md#queries)

  - [SQL vs NoSQL](database/database.md#sql-vs-nosql)
    - [SQL](database/database.md#sql-1)
    - [NoSQL](database/database.md#nosql)
    - [SQL vs NoSQL](database/database.md#sql-vs-nosql)
    - [Database Types](database/database.md#database-types)
    - [Schema](database/database.md#schema)
    - [Data Model](database/database.md#data-model)
    - [Ability to Scale](database/database.md#ability-to-scale)
    - [ACID vs BASE](database/database.md#acid-vs-base)
    - [Use Cases](database/database.md#use-cases)
    - [Reasons to use an SQL database](database/database.md#reasons-to-use-an-sql-database)
    - [Reasons to use a NoSQL database](database/database.md#reasons-to-use-a-nosql-database)
- [UNION vs UNION ALL](database/database.md#union-vs-union-all)
- [TRUNCATE vs DELETE, vs  DROP](database/database.md#truncate-vs-delete-vs--drop)
- [TRUNCATE](database/database.md#truncate)
- [DELETE](database/database.md#delete)
- [DROP](database/database.md#drop)
    
  - [Frequently asked queries](database/database.md#frequently-asked-queries)
    - [Find all departments with sales more than 1000](database/database.md#frequently-asked-queries)
    - [Print all employee ids with their manager ids](database/database.md#frequently-asked-queries)
    - [Print all manager names with count of directs](database/database.md#frequently-asked-queries)
    - Find Nth highest salary in SQL - Oracle, MSSQL and MySQL
      -Nth maximum salary in MySQL using LIMIT keyword
      -2nd highest salary in MySQL without subquery
      -3rd highest salary in MySQL using LIMIT clause
      -Test
      -Test
      -Test
      -Test
# Maven

  - [Maven](maven/maven.md#Maven)
  - [pom.xml](maven/maven.md#pomxml)
  - [Maven build life-cycle](maven/maven.md#maven-build-life-cycle)
    - [validate](maven/maven.md#maven-build-life-cycle)
    - [compile](maven/maven.md#maven-build-life-cycle)
    - [test](maven/maven.md#maven-build-life-cycle)
    - [package](maven/maven.md#maven-build-life-cycle)
    - [verify](maven/maven.md#maven-build-life-cycle)
    - [install](maven/maven.md#maven-build-life-cycle)
    - [deploy](maven/maven.md#maven-build-life-cycle)

# Unix
  - [Unix](unix/Unix.md#unix)
    - [Find](unix/Unix.md#find)
    - [Search](unix/Unix.md#search)
    - [Processes](unix/Unix.md#processes)
    - [File permissions](unix/Unix.md#file-permissions)
    - [List files](unix/Unix.md#list-files)
    - [Symlinks](unix/Unix.md#symlinks)
    - [Checking logs](unix/Unix.md#checking-logs)
    - [Text manipulation](unix/Unix.md#text-manipulation)
    - [Sed](unix/Unix.md#sed)
    - [Networking](unix/Unix.md#networking)
    - [Space](unix/Unix.md#space)
    - [Miscellaneous](unix/Unix.md#miscellaneous)

# DevOps Tools
  - [Docker](devops/docker/docker.md)
  - [Git](devops/git/git.md#git)
    * [Advantages of git](devops/git/git.md#advantages-of-git)
    * [git push](devops/git/git.md#git-push)
    * [git pull vs git fetch](devops/git/git.md#git-pull-vs-git-fetch)
    * [conflict](devops/git/git.md#conflict)
    * [resolve a conflict](devops/git/git.md#resolve-a-conflict)
    * [git clone](devops/git/git.md#git-clone)
    * [git pull origin](devops/git/git.md#git-pull-origin)
    * [git commit a](devops/git/git.md#git-commit-a)
  - [Jenkins](devops/jenkins/jenkins.md)
    * [Continuous delivery](devops/jenkins/jenkins.md#continuous-delivery)
  - [Kubernetes](devops/kubernetes/kubernetes.md)

# Swagger
  - [Swagger](spring/swagger.md#swagger)
  - [Integrate Swagger into your microservices](spring/swagger.md#integrate-swagger-into-your-microservices)
  - [Swagger annotations](spring/swagger.md#swagger-annotations)
    - [@Api](spring/swagger.md#swagger-annotations)
    - [@ApiOperation](spring/swagger.md#swagger-annotations)
    - [@ApiResponse](spring/swagger.md#swagger-annotations)
    - [@ApiParam](spring/swagger.md#swagger-annotations)
  - [Maven plugin](spring/swagger.md#maven-plugin)

# Cucumber

- Cucumber
- Advantages of Cucumber
- Example
- Features in Cucumber
- Feature file
- Step Definition
- Gherkin
- user login feature Example
- Test harness in Cucumber
  - Selenium vs Cucumber
  - TDD(Test-Driven Development)
  - BDD and TDD
  - Cucumber profiles
  - Scenario Outline
  - Scenario and Scenario Outline
  - Step Definition
  - Background in a Feature file
  - Junit Test runner class
  - Tags in cucumber-bdd
  - Cucumber Hooks
  - Name any two testing framework that can be integrated with Cucumber?
  - What are the two files required to run a cucumber test?
  - Examples Table or Scenario Outline vs Data Table

# Agile
  - Agile Scrum Ceremonies
    * Sprint Planning
    * Daily Scrum
    * Sprint Review
    * Sprint Retrospective

# Amazon Web Services (AWS)

- [AWS](aws/aws.md#amazon-web-services-aws)
- [Key components of AWS](aws/aws.md#key-components-of-aws)
- [Elastic Cloud Compute (EC2)](aws/aws.md#elastic-cloud-compute-ec2)
- [Simple Storage Service (S3)](aws/aws.md#simple-storage-service-s3)
  - [Objects and Buckets](aws/aws.md#objects-and-buckets)
  - [S3 Storage Classes](aws/aws.md#s3-storage-classes)
  - [Durability and Availability](aws/aws.md#durability-and-availability)
  - [Storage Classes for Frequently Accessed Objects](aws/aws.md#storage-classes-for-frequently-accessed-objects)
  - [Storage Classes for Infrequently Accessed Objects](aws/aws.md#storage-classes-for-infrequently-accessed-objects)
  - [Storage Class for Both Frequently and Infrequently Accessed Objects](aws/aws.md#storage-class-for-both-frequently-and-infrequently-accessed-objects)
  - [Access Permissions](aws/aws.md#access-permissions)
    - [Bucket Policies](aws/aws.md#bucket-policies)
    - [User Policies](aws/aws.md#user-policies)
    - [Bucket and Object Access Control Lists](aws/aws.md#bucket-and-object-access-control-lists)
    - [Encryption](aws/aws.md#encryption)
    - [Versioning](aws/aws.md#versioning)

- [Relational Database Service (RDS)](aws/aws.md#relational-database-service-rds)
  - Database Engines
  - Licensing
  - Instance Classes
  - Standard
  - Memory Optimized
  - Burstable Performance
  - Scaling Vertically
  - Storage
  - General-Purpose SSD
  - Provisioned IOPS SSD
  - Magnetic
  - Scaling Horizontally with Read Replicas
  - High Availability with Multi-AZ
  - Backup and Recovery
  

- [Route 53](aws/aws.md#route-53)
- [CloudWatch](aws/aws.md#cloudwatch)
  - [CloudWatch Metrics](aws/aws.md#cloudwatch-metrics)
  - [CloudWatch Alarms](aws/aws.md#cloudwatch-alarms)
  - [CloudWatch Dashboards](aws/aws.md#cloudwatch-dashboards)
  - [CloudWatch Logs](aws/aws.md#cloudwatch-logs)
  - [Metric Filters](aws/aws.md#metric-filters)
  - [CloudWatch Events](aws/aws.md#cloudwatch-events)
- [CloudTrail](aws/aws.md#cloudtrail)
  - [API and Non-API Events](aws/aws.md#api-and-non-api-events)
  - [Management and Data Events](aws/aws.md#management-and-data-events)
  - [Event History](aws/aws.md#event-history)
  - [Trails](aws/aws.md#trails)
  - [Log File Integrity Validation](aws/aws.md#log-file-integrity-validation)
- [Cost Explorer](aws/aws.md#cost-explorer)
  - [Cost and Usage](aws/aws.md#cost-and-usage)
  - [Reservation reports](aws/aws.md#reservation-reports)
  - [Reserved Instances Utilization](aws/aws.md#reserved-instances-utilization)
  - [Reserved Instances Coverage](aws/aws.md#reserved-instances-coverage)
  - [Reserved instance recommendations](aws/aws.md#reserved-instance-recommendations)
- [Amazon DynamoDB](aws/aws.md#amazon-dynamodb)
  - [Items and Tables](aws/aws.md#items-and-tables)
  - [Scaling Horizontally](aws/aws.md#scaling-horizontally)
  - [Queries and Scans](aws/aws.md#queries-and-scans)
- [AWS Lambda](aws/AwsLambda.md#aws-lambda)
  - [Introducing AWS Lambda](aws/AwsLambda.md#introducing-aws-lambda)
  - [Key benefits of serverless computing](aws/AwsLambda.md#key-benefits-of-serverless-computing)
    - [No ware to manage:](aws/AwsLambda.md#no-ware-to-manage)
    - [Faster execution time:](aws/AwsLambda.md#faster-execution-time)
    - [Really low costs:](aws/AwsLambda.md#really-low-costs)
    - [Support of popular programming languages:](aws/AwsLambda.md#support-of-popular-programming-languages)
    - [Microservices compatible:](aws/AwsLambda.md#microservices-compatible)
    - [Event-driven applications:](aws/AwsLambda.md#event-driven-applications)
  - [Cons or Disadvantage  of serverless computing](aws/AwsLambda.md#cons-or-disadvantage--of-serverless-computing)
    - [Execution duration:](aws/AwsLambda.md#execution-duration)
    - [Complexity:](aws/AwsLambda.md#complexity)
    - [Lack of tools](aws/AwsLambda.md#lack-of-tools)
    - [Vendor lock-in:](aws/AwsLambda.md#vendor-lock-in)
  - Test
  - Test
  - Test

# Good Practices
  - [Good Practices for REST API Design](misc/GoodPractices.md#good-practices-for-rest-api-design)
    * [Accept and respond with JSON](misc/GoodPractices.md#accept-and-respond-with-json)
    * [Use nouns instead of verbs in endpoint paths](misc/GoodPractices.md#use-nouns-instead-of-verbs-in-endpoint-paths)
    * [Nesting resources for hierarchical objects](misc/GoodPractices.md#nesting-resources-for-hierarchical-objects)
    * [Handle errors gracefully and return standard error codes](misc/GoodPractices.md#handle-errors-gracefully-and-return-standard-error-codes)
    * [Allow filtering, sorting, and pagination](misc/GoodPractices.md#allow-filtering-sorting-and-pagination)
    * [Maintain Good Security Practices](misc/GoodPractices.md#maintain-good-security-practices)
    * [Cache data to improve performance](misc/GoodPractices.md#cache-data-to-improve-performance)
    * [Versioning our APIs](misc/GoodPractices.md#versioning-our-apis)
  - [Java code review checklist](misc/GoodPractices.md#java-code-review-checklist)
    * [Basic Checks (before)](misc/GoodPractices.md#code-quality)
    * [The code was tested](misc/GoodPractices.md#code-quality)
    * [Clean Code](misc/GoodPractices.md#code-quality)
    * [Best Practices](misc/GoodPractices.md#code-quality)
    * [Exception Handling](misc/GoodPractices.md#exception-handling)
  - [Common Java Vulnerabilities](misc/owasp.md#common-java-vulnerabilities)
    * [Injection Flaws](misc/owasp.md#injection-flaws)
    * [Cross Site Scripting (XSS)](misc/owasp.md#cross-site-scripting-xss)
      * [How does XSS work?](misc/owasp.md#how-does-xss-work)
    * [Cross Site Request Forgery(CSRF)](misc/owasp.md#cross-site-request-forgerycsrf)
    * Test
  - [Resolving a Production Issue on a Live Server](misc/GoodPractices.md#resolving-a-production-issue-on-a-live-server)
    - [Notify All Stakeholders](misc/GoodPractices.md#notify-all-stakeholders)
    - [Reproduce : Replicate the production environment locally](misc/GoodPractices.md#reproduce--replicate-the-production-environment-locally)
    - [Root Cause Analysis & Fix](misc/GoodPractices.md#root-cause-analysis--fix)
    - [Re-test & Regression Testing](misc/GoodPractices.md#re-test--regression-testing)
    - [Backup the System Before Implementing Complex Solution](misc/GoodPractices.md#backup-the-system-before-implementing-complex-solution)
    - [Document the Problem and How it was Resolved](misc/GoodPractices.md#document-the-problem-and-how-it-was-resolved)

