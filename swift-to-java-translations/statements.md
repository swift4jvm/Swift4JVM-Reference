---
description: 'https://docs.swift.org/swift-book/ReferenceManual/Statements.html'
---

# Statements

### Loop Statements

#### For-In Statement

For-in statements are translated primarily to Java's for-each loop. If patterns are used, the appropriate destructuring is performed as specified in [Assignment Operator](expressions.md#assignment-operator) and [Patterns](patterns.md). A trailing where clause translates to an if statement around the loop body. If the collection expression is a range operator, the loop is translated to a C-style loop as an optimization.

{% tabs %}
{% tab title="Swift" %}
```swift
for item in collection {
    /* ... */
}
for i in 0..<10 where i != 5 {
    /* ... */
}
for (a, b) in tuples {
    /* ... */
}
```
{% endtab %}

{% tab title="Java" %}
```java
for (Object item : collection) {
    /* ... */
}
for (int i = 0; i < 10; i++) if (i != 5) {
    /* ... */
}
for (Tuple t1 : tuples) { Object a = t1._1; Object b = t1._2
    /* ... */
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

