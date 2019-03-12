---
description: 'https://docs.swift.org/swift-book/ReferenceManual/Expressions.html'
---

# Expressions

### Prefix Expressions

Prefix expressions, in particular prefix operators, are translated to regular method calls.

{% tabs %}
{% tab title="Swift" %}
```swift
-x
```
{% endtab %}

{% tab title="Java" %}
```java
minus(x)
```
{% endtab %}
{% endtabs %}

#### Try Operator

The regular `try` operator only serves to indicate calls that can throw within a `do` block. As such, its Java equivalent is the call itself.

{% tabs %}
{% tab title="Swift" %}
```swift
try someMethod()
```
{% endtab %}

{% tab title="Java" %}
```java
someMethod()
```
{% endtab %}
{% endtabs %}

The `try!` operator converts a checked exception to an \(unchecked\) error. This ensures it will not normally be caught by a caller.

{% tabs %}
{% tab title="Swift" %}
```swift
try! someMethod()
```
{% endtab %}

{% tab title="Java" %}
```java
try { someMethod(); } catch (Exception ex) { throw new Error(ex); }
```
{% endtab %}
{% endtabs %}

The `try?` operator discards exceptions and returns `nil` as its result when one occurs. Because try blocks can only be used as statements in Java, the compiler will transform the code to a combination of temporary local variables, try blocks, and in the worst case temporary helper methods. Note that in the example below, someMethod returns an `Int`, so the Java type of `it` is `Integer`.

{% tabs %}
{% tab title="Swift" %}
```swift
let it = try? someMethod()
```
{% endtab %}

{% tab title="Java" %}
```java
final Integer it;
try { it = someMethod(); }
catch (Exception ex) { it = null; }
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Swift" %}
```swift
if let it = try? someMethod() {
    /* ... */
}
```
{% endtab %}

{% tab title="Java" %}
```java
{
    final Integer t1;
    try { it = someMethod(); }
    catch (Exception ex) { it = null; }
    if (t1 != null) {
        final int it = t1.intValue();
        /* ... */
    }
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
The use of try blocks as expressions is made practically impossible in Java bytecode due to the discarding of stack frames at the catch label. As such, special care should be taken when a `try!` or `try?` operator occurs nested deep within an expression. A compiler implementation is advised to translate try blocks using either temporary variables or helper methods. The snippets below illustrates these two strategies.

```swift
foo(bar(), try? baz() + bal)
```

It can be translated using a temporary variable for each expression occurring before the try operator, to respect the order of evaluation:

```java
final A t1 = bar()
final B t2;
try { t2 = baz() + bal; } catch (Exception ex) { t2 = null; }
foo(t1, t2);
```

Alternatively, the try expression can be substituted by a call to a temporary method. This has the advantage of requirement fewer analysis steps to figure out where to put the temporary variables.The best way to implement this is with a lambda expression, as it captures other variables automatically. The optimiser can then detect the call to the functional method of the lambda type and replace it with a direct call. The Swift AST then looks as follows:

```swift
foo(bar(), { try? baz() + bal }())
```

This translates to Java like a regular closure:

```java
foo(bar(), ((Supplier<B>) () -> {
    try { return baz() + bal; }
    catch (Exception ex) { return null; }
}).get())
```

In bytecode, this becomes:

```java
private B lambda1(/* captures: */ C bal) {
    try { return baz() + bal; }
    catch (Exception ex) { return null; }
}
// ...
foo(bar(), LAMBDA_INDY(Object Supplier.get(), B lambda1(C), bal).get())
```

The optimiser can then detect that the creation of a lambda object followed by a call to its functional method can be simplified to a call to the lambda implementation method:

```java
private B lambda1(C bal) {
    try { return baz() + bal; }
    catch (Exception ex) { return null; }
}
// ...
foo(bar(), lambda1(bal));
```
{% endhint %}

### Binary Expressions

#### Assignment Operator

#### Ternary Conditional Operator

#### Type-Casting Operators

### Primary Expressions

#### Literal Expression

#### Self Expression

#### Superclass Expression

#### Closure Expression

**Capture Lists**

#### Implicit Member Expression

#### Parenthesized Expression

#### Tuple Expression

#### Wildcard Expression

#### Key-Path Expression

#### Selector Expression

#### Key-Path String Expression

### Postfix Expressions

#### Function Call Expression

#### Initializer Expression

#### Explicit Member Expression

#### Postfix Self Expression

#### Subscript Expression

#### Forced-Value Expression

#### Optional-Chaining Expression

