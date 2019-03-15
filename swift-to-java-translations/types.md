---
description: 'https://docs.swift.org/swift-book/ReferenceManual/Types.html'
---

# Types

### Type Annotation

A type annotation is moved from behind the identifier and replaces the declarator keyword.

{% tabs %}
{% tab title="Swift" %}
```swift
let someTuple: (Double, Double) = (3.14159, 2.71828)
func someFunction(a: Int) { /* ... */ }
```
{% endtab %}

{% tab title="Java" %}
```java
final Tuple<double, double> someTuple = Tuple.of(3.14159, 2.71828);
void someFunction(int a) { /* ... */ }
```
{% endtab %}
{% endtabs %}

### Type Identifier

Type identifiers are translated as-is.

### Tuple Type

TBD

### Function Type

TBD

### Array Type

Array types, i.e. the syntactic sugar for the generic Swift type `Array<T>`, are mapped to Java's array types. See [Literal Expression](expressions.md#literal-expression) for information about the translation of array _literals_.

{% tabs %}
{% tab title="Swift" %}
```swift
let someArray: Array<String> = ["Alex", "Brian", "Dave"]
let someArray: [String] = ["Alex", "Brian", "Dave"]
var array3D: [[[Int]]] = [[[1, 2], [3, 4]], [[5, 6], [7, 8]]]
```
{% endtab %}

{% tab title="Java" %}
```java
final String[] someArray = { "Alex", "Brian", "Dave" };
final String[] someArray = { "Alex", "Brian", "Dave" };
final int[][][] array3D = {{{1, 2}, {3, 4}}, {{5, 6}, {7, 8}}};
```
{% endtab %}
{% endtabs %}

### Dictionary Type

Dictionary types, i.e. the Swift type `Dictionary<K, V>`, are translated to `Map<K, V>`. See [Literal Expression](expressions.md#literal-expression) for information about the translation of dictionary _literals_.

{% tabs %}
{% tab title="Swift" %}
```swift
let someDictionary: [String: Int] = ["Alex": 31, "Paul": 39]
let someDictionary: Dictionary<String, Int> = ["Alex": 31, "Paul": 39]
```
{% endtab %}

{% tab title="Java" %}
```java
Map<String, Integer> someDictionary = /* ... */;
Map<String, Integer> someDictionary = /* ... */;
```
{% endtab %}
{% endtabs %}

### Optional Type

Optional types are implemented using Java's `null` literal and reference types. The translation for primitive types happens as shown below:

{% tabs %}
{% tab title="Swift" %}
```swift
var optionalInteger: Int?
optionalInteger = 42
optionalInteger!
```
{% endtab %}

{% tab title="Java" %}
```java
Integer optionalInteger;
optionalInteger = Integer.valueOf(42);
optionalInteger.intValue();
```
{% endtab %}
{% endtabs %}

### Implicitly Unwrapped Optional Type

Works like the optional type, except that unwrapping is performed implicitly, i.e. the `!` operator can be omitted.

{% tabs %}
{% tab title="Swift" %}
```swift
var implicitlyUnwrappedString: String!
implicitlyUnwrappedString = "abc"
implicitlyUnwrappedString.uppercased
```
{% endtab %}

{% tab title="Java" %}
```java
String implicitlyUnwrappedString;
implicitlyUnwrappedString = "abc";
implicitlyUnwrappedString.toUpperCase();
```
{% endtab %}
{% endtabs %}

### Protocol Composition Type

This kind of type is not representable in Java in all contexts. In declarations, it will be replaced by the only class or the first interface, and casts will be used when accessing members of the remaining interfaces. Since intersection types are allowed in type parameter bounds, they will be translated as-is in this context.

{% tabs %}
{% tab title="Swift" %}
```swift
// class A, protocol B, protocol C
var it: A & B
it.methodFromB()
it as B & C
func take<T>() where T: A & B & C { /* ... */ }
```
{% endtab %}

{% tab title="Java" %}
```java
// class A, protocol B, protocol C
A it;
((B) it).methodFromB();
(B & C) it;
<T extends A & B & C> void take() { /* ... */ }
```
{% endtab %}
{% endtabs %}

### Metatype Type

Metatype types are mapped to generic instances of `java.lang.Class`.

{% tabs %}
{% tab title="Swift" %}
```swift
SomeClass.Type
SomeProtocol.Protocol
```
{% endtab %}

{% tab title="Java" %}
```java
Class<SomeClass>
Class<SomeProtocol>
```
{% endtab %}
{% endtabs %}

### Type Inheritance Clauses

Type inheritance clauses are mapped to `extends` and `implements`.

{% tabs %}
{% tab title="Swift" %}
```swift
class A : SomeClass
class B : SomeClass, SomeProtocol
class C : SomeProtocol
protocol D : SomeProtocol, SomeOtherProcotol
```
{% endtab %}

{% tab title="Java" %}
```java
class A extends SomeClass
class B extends SomeClass implements SomeProtocol
class C implements SomeProtocol
interface D extends SomeProtocol, SomeOtherProcotol
```
{% endtab %}
{% endtabs %}

### Type Inference

Type inference is performed by the compiler, firstly to provide meaningful translations for declarations.

{% tabs %}
{% tab title="Swift" %}
```swift
let e = 2.71828
let eFloat: Float = 2.71828
```
{% endtab %}

{% tab title="Java" %}
```java
final double e = 2.71828;
final float eFloat = 2.71828F;
```
{% endtab %}
{% endtabs %}

