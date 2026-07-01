# Bonus: Generics Theory

This note summarizes the Shortcut Generics theory material in the repository's interview-prep style. It is meant as a compact theory pass before answering the Records and Generics questions.

## 1. Why Java added generics

Before generics, Java code often used `Object` as a universal type:

```java
class Box {
    private Object value;

    void set(Object value) {
        this.value = value;
    }

    Object get() {
        return value;
    }
}
```

This works, but it moves type safety from compile time to runtime. Callers must cast values manually, and an incorrect assumption can fail later with `ClassCastException`.

```java
Box box = new Box();
box.set("text");

Integer value = (Integer) box.get(); // fails at runtime
```

Collections had the same issue before Java 5:

```java
List list = new ArrayList();
list.add("text");

Integer value = (Integer) list.get(0);
```

Generics solve this by making the type part of the API contract:

```java
List<String> names = new ArrayList<>();
names.add("Ivan");

String name = names.get(0);
```

The compiler now prevents adding or reading values as the wrong type.

## 2. What generics provide

Generics implement parametric polymorphism: the same class, interface or method can work with different types while preserving a relationship between inputs and outputs.

Common benefits:

- compile-time type safety;
- fewer explicit casts;
- clearer API contracts;
- reusable containers and algorithms;
- earlier detection of type mistakes.

For example, `Box<T>` says that the same type used for writing is also used for reading:

```java
class Box<T> {
    private T value;

    void set(T value) {
        this.value = value;
    }

    T get() {
        return value;
    }
}
```

With `Box<String>`, `T` is treated as `String` by the compiler. With `Box<Integer>`, `T` is treated as `Integer`.

## 3. Type parameters and type arguments

In `class Box<T>`, `T` is a type parameter. In `Box<String>`, `String` is a type argument.

Common naming conventions:

- `T`: type;
- `E`: element;
- `K`: key;
- `V`: value;
- `R`: result;
- `S`, `U`: additional generic types.

These names are conventions, not special keywords. In public APIs, a meaningful name can be better when the role is domain-specific.

Generic types can have several type parameters:

```java
class Pair<A, B> {
    private final A first;
    private final B second;

    Pair(A first, B second) {
        this.first = first;
        this.second = second;
    }
}
```

`Map<K, V>` follows the same idea: one type for keys and another type for values.

## 4. Generic methods

A method can declare its own type parameter independently of the class:

```java
static <T> T first(List<T> values) {
    return values.get(0);
}
```

The `<T>` before the return type introduces the method-level type parameter. The compiler usually infers it from the arguments and expected return type.

Generic methods are useful when the type relationship is local to one operation. A whole class does not need to be generic if only one method needs a type parameter.

Generic methods can also declare several type parameters:

```java
static <K, V> Map<K, V> mapOf(K key, V value) {
    Map<K, V> result = new HashMap<>();
    result.put(key, value);
    return result;
}
```

## 5. Type inference and the diamond operator

Java can often infer generic types from context:

```java
List<String> names = new ArrayList<>();
```

The diamond operator `<>` avoids repeating `String` on the right side.

Generic method calls also use inference:

```java
String value = first(List.of("a", "b"));
```

Inference improves readability, but it has limits. If a signature is too abstract or overloaded, the compiler may infer a type that is broader than expected or fail with a hard-to-read error.

## 6. Type erasure

Java generics are implemented mostly through type erasure. Generic checks happen at compile time, and most type parameters are erased in bytecode.

For an unbounded type parameter:

```java
class Box<T> {
    T value;
}
```

`T` erases to `Object`.

For a bounded type parameter:

```java
class Box<T extends Number> {
    T value;
}
```

`T` erases to `Number`.

For multiple bounds:

```java
class Box<T extends Number & Comparable<T>> {
}
```

`T` erases to the leftmost bound, here `Number`. If there is a class bound, it must appear before interface bounds.

## 7. Why Java uses erasure

Generics were added after Java already had many libraries and applications using raw collections. Erasure allowed generic code to stay compatible with older bytecode and APIs.

The benefit was evolutionary compatibility. The cost is that generic type arguments are usually not available on object instances at runtime.

Consequences:

- `List<String>` and `List<Integer>` have the same runtime class;
- `instanceof List<String>` is not valid;
- `List<String>.class` is not valid;
- `new T()` is not allowed;
- `new T[]` is not safe;
- frameworks need type tokens for nested generic types.

## 8. Raw types

A raw type is a generic type used without type arguments:

```java
List raw = new ArrayList();
```

Raw types exist for compatibility with old Java code. They should be avoided in new code because they disable generic checks.

```java
List<String> names = new ArrayList<>();
List raw = names;
raw.add(42);

String name = names.get(0); // may fail later
```

The compiler usually reports unchecked warnings around raw types. Treat those warnings as important feedback. If an unchecked cast is unavoidable, keep it at the smallest boundary and document the invariant.

## 9. Invariance of generics

Even though `String` is a subtype of `Object`, `List<String>` is not a subtype of `List<Object>`.

If this were allowed, code could break type safety:

```java
List<String> strings = new ArrayList<>();
List<Object> objects = strings; // not allowed
objects.add(123);
```

Later, code reading from `strings` would expect only strings. Java prevents the problem at compile time by making generic types invariant.

## 10. Array covariance

Arrays are different:

```java
String[] strings = new String[1];
Object[] objects = strings;
objects[0] = 123; // ArrayStoreException
```

Arrays are covariant, so `String[]` can be assigned to `Object[]`. They are also reified, meaning the runtime knows the component type and can reject an invalid store.

Generics are erased, so the JVM cannot do the same runtime element-type check for `List<String>`. That is why generic invariance is enforced by the compiler.

## 11. Wildcards

Wildcards give controlled flexibility where plain invariant generic types would be too strict.

### `? extends T`

`List<? extends Number>` means a list of `Number` or some subtype of `Number`.

You can safely read values as `Number`:

```java
void printAll(List<? extends Number> values) {
    for (Number value : values) {
        System.out.println(value);
    }
}
```

But you cannot safely add a concrete `Number` or `Integer`, because the actual list might be `List<Double>` or `List<BigDecimal>`.

`? extends T` is useful for producers: inputs you only read from.

### `? super T`

`List<? super Integer>` means a list of `Integer` or some supertype of `Integer`, such as `Number` or `Object`.

You can safely add `Integer` values:

```java
void addOne(List<? super Integer> values) {
    values.add(1);
}
```

But reading is only safe as `Object`, because the exact element type is unknown.

`? super T` is useful for consumers: destinations you write into.

## 12. PECS

PECS means Producer Extends, Consumer Super.

Use `? extends T` when a parameter produces `T` values for your method:

```java
double sum(List<? extends Number> source) {
    double result = 0;
    for (Number value : source) {
        result += value.doubleValue();
    }
    return result;
}
```

Use `? super T` when a parameter consumes `T` values from your method:

```java
void addDefaults(List<? super String> target) {
    target.add("default");
}
```

A copy method shows both sides:

```java
static <T> void copy(List<? super T> target, List<? extends T> source) {
    for (T value : source) {
        target.add(value);
    }
}
```

The source produces values, so it uses `extends`. The target consumes values, so it uses `super`.

## 13. Bounds

An upper bound restricts which type arguments are allowed:

```java
class NumericBox<T extends Number> {
    private T value;
}
```

Now `NumericBox<Integer>` is valid, but `NumericBox<String>` is not.

Bounds let generic code call methods available on the bound:

```java
static <T extends Number> double asDouble(T value) {
    return value.doubleValue();
}
```

Multiple bounds express several required capabilities:

```java
static <T extends AutoCloseable & Runnable> void runAndClose(T value) throws Exception {
    try {
        value.run();
    } finally {
        value.close();
    }
}
```

## 14. Restrictions caused by erasure

Because type parameters are erased, some operations are not allowed:

```java
new T();                  // cannot know constructor
new T[10];                // cannot know array component type
obj instanceof List<T>;   // T is erased
List<String>.class;       // no separate class object
```

Use alternatives:

- pass `Class<T>` when a real runtime class is enough;
- pass `Supplier<T>` or a factory for construction;
- pass `IntFunction<T[]>` for array creation;
- use type tokens such as Jackson `TypeReference<List<UserDto>>` for nested generic metadata.

## 15. Primitives and generics

Generic type arguments must be reference types:

```java
List<Integer> values = new ArrayList<>();
// List<int> is not allowed
```

Java uses boxing and unboxing for primitives in generic collections. This is convenient, but it can create overhead and null-related bugs:

```java
Integer value = null;
int unboxed = value; // NullPointerException
```

In hot numeric code, consider primitive arrays, primitive streams or specialized collections.

## 16. Method overloading and erasure

Methods cannot be overloaded only by different generic type arguments:

```java
void handle(List<String> values) {}
void handle(List<Integer> values) {} // same erased signature
```

Both erase to a method accepting `List`, so the compiler rejects the overload.

Use distinct method names, different parameter counts or non-erased parameter types.

## 17. Interview summary

A strong generics answer usually connects these ideas:

- generics move many type errors from runtime to compile time;
- `T`, `E`, `K`, `V` are type parameters by convention;
- Java generics use type erasure for backward compatibility;
- unbounded parameters erase to `Object`, bounded parameters erase to their bound;
- raw types bypass type safety and should be isolated;
- generic types are invariant, unlike arrays;
- wildcards provide controlled variance;
- PECS means `extends` for producers and `super` for consumers;
- erasure explains restrictions such as no `new T()`, no `List<String>.class`, and no overloads that differ only by generic arguments.
