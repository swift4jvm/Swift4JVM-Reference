---
description: 'https://docs.swift.org/swift-book/ReferenceManual/Statements.html'
---

# Statements

### Loop Statements

#### For-In Statement

For-in statements are translated primarily to Java's for-each loop. If patterns are used, the appropriate destructuring is performed as specified in [Assignment Operator](expressions.md#assignment-operator) and [Patterns](patterns.md). A trailing `where` clause translates to an if statement around the loop body. If the collection expression is a range operator \(`...` or `..<`\) or a call to `stride(from:to:by:)` or `stride(from:through:by:)`, the loop is translated to a C-style loop as an optimization.

{% tabs %}
{% tab title="Swift" %}
```swift
for item in collection { /* ... */ }
for item in collection where condition { /* ... */ }
for (a, b) in tuples { /* ... */ }

for item in start ... end { /* ... */ }
for item in start ..< end { /* ... */ }
for item in stride(from: start; to: end; by: interval) { /* ... */ }
for item in stride(from: start; through: end; by: interval) { /* ... */ }
```
{% endtab %}

{% tab title="Java" %}
```java
for (Object item : collection) { /* ... */ }
for (Object item : collection) if (condition) { /* ... */ }
for (Tuple t1 : tuples) { Object a = t1._1; Object b = t1._2; /* ... */ }

for (int item = start; item < end; item++) { /* ... */ }
for (int item = start; item <= end; item++) { /* ... */ }
for (int item = start; item < end; item += interval) { /* ... */ }
for (int item = start; item <= end; item += interval) { /* ... */ }
```
{% endtab %}
{% endtabs %}

Examples from the [Language Guide](https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html):

{% tabs %}
{% tab title="Swift" %}
```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names {
    // ...
}

let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {

    // ...
}

for index in 1...5 {
    // ...
}

let base = 3
let power = 10
var answer = 1
for _ in 1...power {
    answer *= base
}

let minutes = 60
for tickMark in 0..<minutes {
    // ...
}

let minuteInterval = 5
for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
    // ...
}

let hours = 12
let hourInterval = 3
for tickMark in stride(from: 3, through: hours, by: hourInterval) {
    // ...
}
```
{% endtab %}

{% tab title="Java" %}
```java
final String[] names = {"Anna", "Alex", "Brian", "Jack"};
for (String name : names) {
    // ...
}

final Map<String, Integer> numberOfLegs = /* ["spider": 8, "ant": 6, "cat": 4] */;
for (Map.Entry<String, Integer> entry : numberOfLegs) {
    final String animalName = entry.getKey(); final int legCount = entry.getValue(); 
    // ...
}

for (int index = 1; index <= 5; index++) {
    // ...
}

final int base = 3
final int power = 10
int answer = 1
for (int t1 = 1; t1 <= power; t1++) {
    answer *= base
}

final int minutes = 60
for (int tickMark = 0; tickMark < minutes; tickMark++) {
    // ...
}

final int minuteInterval = 5
for (int tickMark = 0; tickMark < minutes; tickMark += minuteInterval) {
    // ...
}

final int hours = 12
final int hourInterval = 3
for (int tickMark = 3; tickMark <= hours; tickMark += hourInterval) {
    // ...
}
```
{% endtab %}
{% endtabs %}

#### While Statement

A while statement in its basic form can be taken as-is. Multiple boolean conditions, separated by commas, can be translated to a chain of `&&` operators. If patterns are used, the appropriate destructuring is performed as specified in [Assignment Operator](expressions.md#assignment-operator) and [Patterns](patterns.md). Optional binding conditions behave as in an [if statement](statements.md#if-statement), which is described below.

{% tabs %}
{% tab title="Swift" %}
```swift
while condition {
    /* ... */
}
while condition1, condition2, condition3 {
    /* ... */
}
while let it = someMethod() {
    /* ... */
}
```
{% endtab %}

{% tab title="Java" %}
```java
while (condition) {
    /* ... */
}
while (condition1 && condition2 && condition3) {
    /* ... */
}
Integer t1; while ((t1 = someMethod) != null) { int it = t1.intValue();
    /* ... */
}
```
{% endtab %}
{% endtabs %}

#### Repeat-While Statement

Repeat-while statements directly translate to do-while statements.

{% tabs %}
{% tab title="Swift" %}
```swift
repeat {
    /* ... */
} while condition
```
{% endtab %}

{% tab title="Java" %}
```java
do {
    /* ... */
} while (condition);
```
{% endtab %}
{% endtabs %}

### Branch Statements

#### If Statement

#### Guard Statement

#### Switch Statement

**Switch Statements Must Be Exhaustive**

**Switching over Future Enumeration Cases**

**Execution Does Not Fall Through Cases Implicitly**

### Labeled Statement

### Control Transfer Statements

#### Break Statement

#### Continue Statement

#### Fallthrough Statement

#### Return Statement

#### Throw Statement

### Defer Statement

### Do Statement

### Compiler Control Statements

#### Conditional Compilation Block

#### Line Control Statement

#### Compile-Time Diagnostic Statement

### Availability Condition

