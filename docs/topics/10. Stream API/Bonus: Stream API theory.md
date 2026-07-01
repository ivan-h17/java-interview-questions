# 10.10. Stream API theory from Shortcut

These notes are rewritten for this repository's Java backend interview style. They follow the Shortcut lesson topic order but avoid copying the exported lesson verbatim.

## 1. What Stream API is

Stream API, introduced in Java 8, is a declarative way to process sequences of data.

Instead of describing how to iterate with indexes and temporary variables, a stream pipeline describes what should happen:

```java
List<String> result = names.stream()
    .filter(name -> name.length() > 3)
    .map(String::toUpperCase)
    .toList();
```

Streams are useful for filtering, mapping, grouping, searching, reducing and composing data transformations. They are not a replacement for every loop. A loop can be clearer when code needs indexes, complex branching, checked exception handling, mutation or explicit `break` / `continue`.

## 2. Core Concepts

### Stream is not a collection

A `Collection` stores data. A `Stream<T>` is a one-shot pipeline over a source.

Important differences:

- A collection can usually be traversed repeatedly; a stream is consumed by a terminal operation.
- A collection has storage; a stream describes traversal and transformations.
- A stream can be lazy, infinite or backed by resources such as files or database cursors.
- A stream may hide expensive work behind compact lambdas.

### Pipeline structure

A typical stream pipeline has three parts:

- source: collection, array, file, generator, range, custom `Spliterator`;
- intermediate operations: `filter`, `map`, `flatMap`, `sorted`, `distinct`, `peek`, `limit`, `skip`;
- terminal operation: `collect`, `toList`, `reduce`, `count`, `findFirst`, `forEach`, `toArray`.

Intermediate operations are lazy. Calling `filter()` or `map()` does not traverse the data. Execution starts when a terminal operation is invoked.

### Internal mental model

At a high level, a stream pipeline stores stages until terminal execution:

```java
List<Integer> result = numbers.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * 10)
    .toList();
```

The stream does not first build a full filtered list and then map it. For stateless operations, it usually processes elements vertically:

- take one element from the source;
- apply `filter`;
- if it passes, apply `map`;
- pass the mapped value toward the terminal operation;
- continue until the terminal operation is satisfied.

This is why `filter().map().findFirst()` can stop early.

### Spliterator

`Spliterator<T>` is the traversal mechanism behind streams. Compared with `Iterator`, it can:

- advance element by element with `tryAdvance`;
- split remaining work with `trySplit`;
- estimate remaining size with `estimateSize`;
- report characteristics such as `ORDERED`, `SIZED`, `SUBSIZED`, `DISTINCT`, `SORTED`, `IMMUTABLE`, `CONCURRENT`, `NONNULL`.

The stream implementation uses this metadata for optimization and parallel execution planning.

Good `Spliterator` characteristics are a contract. Claiming `DISTINCT`, `SORTED`, `SIZED` or `NONNULL` incorrectly can lead to wrong assumptions and bugs. Conservative metadata is safer than false metadata.

### Short-circuiting

Some operations can finish without consuming the full source:

- `findFirst`;
- `findAny`;
- `anyMatch`;
- `allMatch`;
- `noneMatch`;
- `limit`;
- `takeWhile`.

Short-circuiting is especially important for large or infinite streams.

### Stateless and stateful operations

Stateless operations handle each element independently:

- `filter`;
- `map`;
- `flatMap`;
- `peek` when used only for observation.

Stateful operations need information across elements and may buffer or coordinate:

- `sorted`;
- `distinct`;
- `limit`;
- `skip`;
- `takeWhile`;
- `dropWhile`.

`sorted()` may need all elements before it can emit the first result. `distinct()` tracks seen values. In parallel streams, stateful and ordered operations often reduce the benefit of parallelism.

## 3. Creating Streams

### From collections

```java
Stream<String> stream = list.stream();
Stream<String> parallel = list.parallelStream();
```

Collections are the most common stream source. Lists and arrays have a meaningful encounter order. Hash-based collections usually should not be treated as having business-stable order.

### From arrays

```java
Stream<String> stream = Arrays.stream(array);
Stream<String> part = Arrays.stream(array, 1, 3);
```

Primitive arrays produce primitive streams:

```java
IntStream ints = Arrays.stream(new int[] {1, 2, 3});
```

Be careful with `Stream.of(primitiveArray)`: it creates a stream with one element, the array object.

### From explicit values

```java
Stream<String> stream = Stream.of("a", "b", "c");
```

Use `Stream.of(...)` when the values are already known and no intermediate collection is needed.

For nullable values, prefer:

```java
Stream<String> stream = Stream.ofNullable(possiblyNull);
```

Do not use `Stream.of(null)` as a null-handling pattern.

### Infinite and generated streams

```java
Stream<Double> randoms = Stream.generate(Math::random);
Stream<Integer> naturals = Stream.iterate(0, n -> n + 1);
```

Generated and iterative streams are often infinite. Always bound them with `limit`, `takeWhile` or the Java 9 three-argument `iterate`:

```java
Stream<Integer> upToTen = Stream.iterate(0, n -> n < 10, n -> n + 1);
```

### Numeric ranges

```java
IntStream.range(0, 5);        // 0, 1, 2, 3, 4
IntStream.rangeClosed(1, 5);  // 1, 2, 3, 4, 5
```

Ranges are useful for numeric processing and occasional index-based work.

## 4. Intermediate Operations

### filter

`filter(Predicate<T>)` keeps elements that satisfy a condition.

```java
List<Integer> evens = numbers.stream()
    .filter(n -> n % 2 == 0)
    .toList();
```

### map

`map(Function<T, R>)` transforms each element.

```java
List<String> upper = names.stream()
    .map(String::toUpperCase)
    .toList();
```

`map` should return a new value. Do not hide persistence, network calls or mutation inside a mapper.

### flatMap

`flatMap(Function<T, Stream<R>>)` maps one input element to zero, one or many output elements, then flattens the nested streams.

```java
List<Integer> flat = nestedLists.stream()
    .flatMap(Collection::stream)
    .toList();
```

Use it for nested collections, optional values, validation errors or one-to-many transformations.

### sorted

`sorted()` and `sorted(comparator)` sort the stream.

Sorting is stateful and may require buffering the whole stream. Use `min()` or `max()` instead of `sorted().findFirst()` when only one extreme value is needed.

### distinct

`distinct()` removes duplicates using `equals()` and `hashCode()`.

For domain objects, distinctness depends on correctly implemented equality. Be careful with mutable fields used in equality.

### limit and skip

`limit(n)` keeps at most the first `n` elements. `skip(n)` discards the first `n`.

These operations depend on encounter order. In parallel ordered streams, they may require coordination.

### peek

`peek(Consumer<T>)` observes elements as they pass through the pipeline.

Use it only for temporary debugging or tracing. It is lazy, can be skipped by short-circuiting, and may run on multiple threads in parallel streams. Do not use `peek` for auditing, persistence, validation or business effects.

### Lambdas and effectively final variables

Stream operations take functional interfaces such as `Predicate`, `Function`, `Consumer`, `Supplier` and `BinaryOperator`.

Local variables captured by lambdas must be final or effectively final:

```java
String prefix = "user:";

List<String> ids = names.stream()
    .map(name -> prefix + name)
    .toList();
```

This compiles because `prefix` is not reassigned.

This does not compile:

```java
int count = 0;
names.stream().forEach(name -> count++);
```

Workarounds such as one-element arrays, mutable holders, external lists or `AtomicInteger` are usually a smell. They add external mutable state and can break in parallel streams. Prefer `count()`, `reduce()`, `collect()`, `IntStream.range()` or a plain loop.

### Side effects

A side effect is any lambda action that changes state outside the current stream result:

- mutating an external list;
- incrementing an external counter;
- saving to a database;
- publishing a message;
- writing to a file;
- changing an entity field.

Streams work best when intermediate operations are pure transformations. Side effects inside `map`, `filter`, `flatMap` and `peek` are difficult to reason about, especially with lazy and parallel execution.

`forEach` is the terminal operation designed for side effects, but even then it should be used deliberately.

## 5. Terminal Operations

Terminal operations start execution and consume the stream.

After a terminal operation, the stream cannot be reused:

```java
Stream<String> s = names.stream();
s.count();
s.toList(); // invalid
```

Create a fresh stream from the source when another traversal is needed.

### collect

`collect()` performs mutable reduction through a `Collector`.

Common collectors:

- `Collectors.toList()`;
- `Collectors.toSet()`;
- `Collectors.toMap()`;
- `Collectors.groupingBy()`;
- `Collectors.partitioningBy()`;
- `Collectors.joining()`.

### toList evolution

There are several list-producing options:

- `collect(Collectors.toList())`: available since Java 8; returns a `List`, but the exact implementation and mutability are not guaranteed by the API contract.
- `collect(Collectors.toUnmodifiableList())`: Java 10+; returns an unmodifiable list and rejects null elements.
- `stream.toList()`: Java 16+; concise terminal operation returning an unmodifiable list and allowing null elements.

If a mutable `ArrayList` is required, be explicit:

```java
List<String> mutable = stream.collect(Collectors.toCollection(ArrayList::new));
```

### forEach vs loop

Use streams when the code is a clear data transformation:

```java
List<String> activeNames = users.stream()
    .filter(User::isActive)
    .map(User::name)
    .toList();
```

Prefer loops when you need:

- indexes as part of the logic;
- checked exceptions without wrappers;
- complex `break` or `continue`;
- several mutable variables;
- step-by-step operational workflow;
- clearer debugging;
- in-place list updates.

### count

`count()` returns the number of elements as `long`.

```java
long active = users.stream()
    .filter(User::isActive)
    .count();
```

### min and max

`min(comparator)` and `max(comparator)` return `Optional<T>` because the stream may be empty.

Use them instead of sorting when you only need one minimum or maximum value.

### reduce

`reduce()` folds stream elements into one result.

```java
int sum = numbers.stream()
    .reduce(0, Integer::sum);
```

Without an identity value, `reduce` returns `Optional<T>`:

```java
Optional<Integer> product = numbers.stream()
    .reduce((a, b) -> a * b);
```

For parallel streams, the operation must be associative and the identity must be neutral. Subtraction is a classic wrong example.

### findFirst and findAny

`findFirst()` returns the first element in encounter order.

`findAny()` may return any element and is often more parallel-friendly.

Both return `Optional<T>`.

### match operations

`anyMatch`, `allMatch` and `noneMatch` are short-circuiting terminal operations.

For empty streams:

- `anyMatch` returns `false`;
- `allMatch` returns `true`;
- `noneMatch` returns `true`.

### toArray

`toArray()` without arguments returns `Object[]`.

For a typed array, use:

```java
String[] values = names.stream()
    .toArray(String[]::new);
```

Do not cast `Object[]` to `String[]`.

## 6. Collectors

### groupingBy

`groupingBy` groups elements by classifier:

```java
Map<String, List<Employee>> byDepartment = employees.stream()
    .collect(Collectors.groupingBy(Employee::department));
```

With downstream collectors, it can count, map or summarize values per group:

```java
Map<String, Long> countByDepartment = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::department,
        Collectors.counting()
    ));
```

### partitioningBy

`partitioningBy` is a specialized grouping by boolean predicate:

```java
Map<Boolean, List<User>> byActive = users.stream()
    .collect(Collectors.partitioningBy(User::isActive));
```

Use it when both `true` and `false` buckets matter.

### Counting, summing and averaging

Collectors such as `counting`, `summingInt`, `averagingInt` and `summarizingInt` are useful downstream of grouping:

```java
Map<String, Integer> salaryByDepartment = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::department,
        Collectors.summingInt(Employee::salary)
    ));
```

For a simple whole-stream numeric aggregation, primitive streams are often clearer:

```java
int total = employees.stream()
    .mapToInt(Employee::salary)
    .sum();
```

### joining

`Collectors.joining()` concatenates character sequences:

```java
String csv = names.stream()
    .collect(Collectors.joining(", "));
```

Prefer it to `reduce("", String::concat)` for strings.

### toMap

`Collectors.toMap()` fails on duplicate keys unless a merge function is supplied.

```java
Map<String, User> byEmail = users.stream()
    .collect(Collectors.toMap(
        User::email,
        Function.identity(),
        (left, right) -> left
    ));
```

For one-to-many relationships, `groupingBy` is usually clearer than forcing a merge.

## 7. Optional

`Optional<T>` represents a value that may be present or absent. In Stream API, it appears naturally from terminal operations such as:

- `findFirst`;
- `findAny`;
- `min`;
- `max`;
- `reduce` without identity.

### Creating Optional

```java
Optional<String> present = Optional.of("value");
Optional<String> empty = Optional.empty();
Optional<String> fromNullable = Optional.ofNullable(possiblyNull);
```

Use `Optional.of(value)` only when `value` must not be null. Use `ofNullable` when null is possible.

### Common methods

Useful methods include:

- `map`;
- `flatMap`;
- `filter`;
- `orElse`;
- `orElseGet`;
- `orElseThrow`;
- `ifPresent`;
- `stream`.

`orElse` evaluates its fallback eagerly. `orElseGet` evaluates the supplier only when the optional is empty.

### Stream and Optional together

Flatten optional values with:

```java
List<User> users = optionalUsers.stream()
    .flatMap(Optional::stream)
    .toList();
```

This is clearer than `filter(Optional::isPresent).map(Optional::get)`.

### Optional anti-patterns

Avoid:

- `get()` without checking presence;
- `isPresent()` + `get()` when `map`, `orElse`, `orElseThrow` or `ifPresent` is clearer;
- `Optional.of(valueThatMayBeNull)`;
- `Optional` fields in entities or DTOs;
- `Optional` parameters;
- `Optional<List<T>>` instead of returning an empty list;
- creating `Optional` objects in tight performance-critical loops.

Good use cases:

- return type when absence is expected;
- terminal stream results;
- simple transformation chains around maybe-present values.

## 8. Primitive Streams

Primitive stream types avoid boxing:

- `IntStream`;
- `LongStream`;
- `DoubleStream`.

They provide numeric operations:

- `sum`;
- `average`;
- `min`;
- `max`;
- `summaryStatistics`.

Example:

```java
double averageAge = users.stream()
    .mapToInt(User::age)
    .average()
    .orElse(0.0);
```

Convert back to object streams with `boxed()` or `mapToObj()` only when needed:

```java
Stream<Integer> boxed = IntStream.range(1, 4).boxed();
```

## 9. Parallel Streams

A parallel stream splits data into chunks, processes chunks concurrently and combines results:

```java
List<Result> results = input.parallelStream()
    .map(this::expensiveCpuWork)
    .toList();
```

Parallel streams are mainly for CPU-bound, independent, sufficiently large work over sources that split well.

### ForkJoinPool common pool

Parallel streams commonly use `ForkJoinPool.commonPool()`. This pool is JVM-wide and shared with other common-pool users.

That matters in backend services. One endpoint using heavy parallel streams can compete with unrelated work and increase tail latency.

### Work stealing

`ForkJoinPool` uses work stealing: idle worker threads can take tasks from busier workers. This improves load balancing when chunks take uneven time.

Work stealing does not fix:

- blocking I/O;
- poor source splitting;
- tiny tasks;
- expensive merging;
- shared mutable state;
- strict ordering requirements.

### Custom ForkJoinPool

Some code submits a parallel stream pipeline into a custom `ForkJoinPool`. This can isolate work, but it is a workaround, not a clean per-stream configuration API.

If you create a custom pool, manage its lifecycle and measure the result. For blocking tasks in modern Java, virtual threads or explicit executors are usually a better design.

### Why parallel streams are not always faster

Common reasons:

- collection is small;
- per-element work is cheap;
- source splits poorly, such as `LinkedList` or `Stream.iterate`;
- pipeline has stateful operations such as `sorted` or `distinct`;
- order must be preserved with `findFirst`, `limit`, `skip` or `forEachOrdered`;
- lambdas block on database, network or file I/O;
- shared mutable state causes synchronization or races;
- result combining is expensive.

Good candidates:

- large arrays or `ArrayList`;
- `IntStream.range` / `LongStream.range`;
- independent CPU-heavy operations;
- no required encounter order;
- no shared mutable state;
- measured speedup under realistic load.

Bad candidates:

- request fan-out to HTTP or JDBC;
- small collections;
- UI or request-thread-sensitive code;
- transaction-heavy code;
- pipelines that mutate external state.

### Ordering

`forEach()` does not preserve encounter order in parallel streams.

`forEachOrdered()` preserves order but can reduce parallel performance.

Collectors such as `toList()` preserve encounter order when the stream has one, but preserving order may require extra coordination.

### Parallel reduce

Parallel `reduce` requires:

- neutral identity;
- associative accumulator;
- compatible combiner.

Correct:

```java
long sum = numbers.parallelStream()
    .reduce(0L, Long::sum);
```

Wrong:

```java
long value = numbers.parallelStream()
    .reduce(0L, (a, b) -> a - b);
```

Subtraction is not associative, so parallel splitting and combining can change the result.

## 10. Laziness and Frequent Mistakes

### Laziness

Intermediate operations do nothing until a terminal operation runs.

This means:

- `peek()` without terminal operation does nothing;
- exceptions inside lambdas are thrown during terminal execution;
- short-circuiting may avoid processing later elements;
- streams can process infinite sources when bounded correctly.

### Common mistakes

Reusing a stream:

```java
Stream<String> s = names.stream();
s.count();
s.toList(); // invalid
```

Mutating the source during stream execution:

```java
names.stream()
    .forEach(name -> names.remove(name)); // unsafe
```

Using `forEach` to build a result:

```java
List<String> result = new ArrayList<>();
names.stream()
    .filter(name -> name.length() > 3)
    .forEach(result::add); // avoid
```

Prefer:

```java
List<String> result = names.stream()
    .filter(name -> name.length() > 3)
    .toList();
```

Putting database or remote calls inside `map`:

```java
orders.stream()
    .map(order -> paymentClient.fetchStatus(order.id()))
    .toList();
```

This hides latency, failure behavior, rate limiting and retry policy inside a data transformation.

## 11. Interview Checklist

A strong Stream API answer should mention:

- streams are pipelines, not collections;
- intermediate operations are lazy;
- terminal operations consume the stream;
- streams are one-shot;
- `filter`, `map`, `flatMap` serve different purposes;
- `sorted` and `distinct` may buffer;
- `findFirst`, `findAny` and match operations can short-circuit;
- `Stream.toList()`, `Collectors.toList()` and unmodifiable collectors differ;
- `toArray(Type[]::new)` is needed for typed arrays;
- `Optional.stream()` and `Stream.ofNullable()` simplify nullable/optional flattening;
- side effects and external mutation are dangerous;
- effectively-final capture prevents local variable reassignment, not mutable object mutation;
- primitive streams avoid boxing;
- parallel streams need CPU-bound independent work and good source splitting;
- `ForkJoinPool.commonPool()` is shared;
- work stealing helps load balance but does not solve blocking or bad pipeline design;
- reduce operations must be associative for parallel correctness;
- backend code should push filtering, sorting and aggregation to the database when appropriate.
