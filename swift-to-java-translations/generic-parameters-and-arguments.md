---
description: >-
  https://docs.swift.org/swift-book/ReferenceManual/GenericParametersAndArguments.html
---

# Generic Parameters and Arguments

### Generic Parameter Clause

Generic parameter clauses are translated mostly as-is. Type parameters with multiple bounds are translated according to the rules of [Protocol Composition Types](types.md#protocol-composition-type).

{% tabs %}
{% tab title="Swift" %}
```swift
func simpleMax<T: Comparable>(_ x: T, _ y: T) -> T
func foo<T: A & B>() -> T
```
{% endtab %}

{% tab title="Java" %}
```java
<T extends Comparable> T simpleMax(T x, T y)
<T extends A & B> T foo()
```
{% endtab %}
{% endtabs %}

#### Generic Where Clauses

The compiler will attempt to transform requirements into constraints as well as possible. In other cases, special annotations will be added to preserve information about the requirements.

{% hint style="info" %}
This is contrary to the behaviour of the Swift compiler, which treats constraints as syntactic sugar for requirements.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
func simpleMax<T>(_ x: T, _ y: T) where T: Comparable -> T
func foo<T>() where T: A, T: B -> T
func bar<S: Sequence>() where S.Iterator.Element: Comparable -> S.Iterator.Element
func baz<S1: Sequence, S2: Sequence>() where S1.Iterator.Element == S2.Iterator.Element -> S1.Iterator.Element
```
{% endtab %}

{% tab title="Java" %}
```java
<T extends Comparable> T simpleMax(T x, T y)
<T extends A & B> T foo()
<S extends Sequence<E>, E extends Comparable> E bar()
<S1 extends Sequence<E>, S2 extends Sequence<E>, E> baz()
```
{% endtab %}
{% endtabs %}

### Generic Argument Clause

Generic argument clauses are translated into Java as-is, unless the type mapping reorders, adds, or omits generic parameters.

