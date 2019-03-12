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
    try { t1 = someMethod(); } catch (Exception ex) { t1 = null; }
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

Binary expressions are translated to method calls like prefix expressions. See [Operators](lexical-structure.md#operators) for a table of operator symbol to name mappings.

#### Assignment Operator

The assignment operator, in its basic form, is kept as-is. Tuple destructuring translates to a sequence of assignments.

{% tabs %}
{% tab title="Swift" %}
```swift
(a, _, (b, c)) = ("test", 9.45, (12, 3))
```
{% endtab %}

{% tab title="Java" %}
```java
a = "test"; /* 9.45 (discarded) */; b = 12; c = 3;
```
{% endtab %}
{% endtabs %}

If needed, temporary variables are used to hold the tuple objects.

{% tabs %}
{% tab title="Swift" %}
```swift
(a, _, (b, c)) = someMethod()



```
{% endtab %}

{% tab title="Java" %}
```java
Tuple<String, Double, Tuple<Int, Int>> t1 = someMethod();
a = t1._0
Tuple<Int, Int> t2 = t1._2
b = t2._0; c = t2._1
```
{% endtab %}
{% endtabs %}

#### Ternary Conditional Operator

The ternary conditional operator is translated as-is.

#### Type-Casting Operators

Type-casting operators require a special translation strategy because of protocol adoption via extensions. If a class implements a protocol only due to an extension that is not also in the same module, a proxy implementation needs to be used. The following examples refer to classes `A` and `B`, protocols `P` and `Q`, and the variables `a` and `b`, as defined below.

```swift
class A: P
class B: A, Q
protocol P
protocol Q
extension B: R // in another module
var a: A
var b: B
```

The behavior of the `is` operator depends on whether the RHS is a protocol or not. If it isn't, then the check translates to `instanceof`. Protocol conformance, however, is checked with a special runtime method, which dynamically checks the existence of an extension proxy class.

{% tabs %}
{% tab title="Swift" %}
```swift
a is B
a is Q
b is Q
```
{% endtab %}

{% tab title="Java" %}
```java
a instanceof B
Runtime.instanceOf(a, Q.class)
Runtime.instanceOf(b, Q.class)
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
The `Runtime.instanceOf` method can be implemented as follows \(refer to the hint in [Extensions ](declarations.md#extension-declaration)for the definition of `findExtensionProxyClass`\):

```java
static boolean instanceOf(Object o, Class protocolType) {
    return o != null && (protocolType.isInstance(o)
           || findExtensionProxyClass(o, protocolType) != null);
}
```
{% endhint %}

The `as` operator is only used to upcast or specify an explicit type for conversions. Since it does not allow downcasts, no special handling for the aforementioned extension scenario is required. It translates directly to regular casts or the appropriate conversion.

{% tabs %}
{% tab title="Swift" %}
```swift
b as A
b as P
1.5 as Float
2 as Comparable
```
{% endtab %}

{% tab title="Java" %}
```java
(B) a
(P) b
1.5F
(Comparable) Integer.valueOf(2)
```
{% endtab %}
{% endtabs %}

The `as?` operator can be translated easily by treating it as a combination of `is` and `as!`. The translation can be done using the rules for these operators, as given above and below, after performing the following AST transformation:

```swift
expr as? Type
// =>
(expr is Type>) ? (expr as! Type) : nil
```

Temporary variables should be used to ensure `expr` is evaluated only once.

{% hint style="info" %}
A more efficient implementation when the RHS is a protocol would be

```java
Runtime.castOpt(expr, Type.class)
```

instead of

```java
Runtime.instanceOf(expr) ? Runtime.cast(expr, type.class) : null
```

because the extension proxy class needs to be searched only once. `castOpt` can be implemented like `cast`, with `return null` in place of `throw new ClassCastException()`.
{% endhint %}

The last type-casting operator is `as!`. It translates to a regular cast if possible, and uses a special runtime method in the same cases as the `is` operator.

{% tabs %}
{% tab title="Swift" %}
```swift
a as! B
a as! Q
b as! Q
```
{% endtab %}

{% tab title="Java" %}
```java
(B) a
Runtime.cast(a, Q.class)
Runtime.cast(b, Q.class)
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
The `Runtime.cast` method can be implemented as follows \(refer to the hint in [Extensions ](declarations.md#extension-declaration)for the definition of `findExtensionProxyClass`\):

```java
static <T> T cast(Object o, Class<T> protocolType) {
    if (o == null) {
        return null;
    }
    if (protocolType.isInstance(o)) {
        return protocolType.cast(o);
    }
    Class proxyClass = findExtensionProxyClass(o, protocolType);
    if (proxyClass == null) {
        throw new ClassCastException();
    }
    return /* new proxyClass(o), using reflection */;
}
```
{% endhint %}

### Primary Expressions

#### Literal Expression

Ordinary literal expressions like strings, numbers and booleans are translated according to the section in [Lexical Structure](lexical-structure.md#literals).

The `#file`, `#line`, `#column` and `#function` literals are treated as if they were string or integer literals with the appropriate value. `#dsohandle` cannot be used on the JVM and is therefore an error.

The translation of array and dictionary literals is TBD.

The playground literals `#colorLiteral`, `#fileLiteral` and `#imageLiteral` are not applicable on the JVM.

#### Self Expression

All instances of self expressions can be translated by replacing `self` or `self.init` with `this`.

{% tabs %}
{% tab title="Swift" %}
```swift
self
self.memberName
self[arguments]
self(arguments)
self.init(arguments)
```
{% endtab %}

{% tab title="Java" %}
```java
this
this.memberName
this.get(arguments)
this(arguments)
this(arguments)
```
{% endtab %}
{% endtabs %}

Assignment to self in mutating methods translates to assignment to all fields, if possible without an instance creation. Since `self =` can only occur within the struct itself, it is not necessary to use `copyTo`.

{% tabs %}
{% tab title="Swift" %}
```swift
self = somePoint
self = Point(x: 1, y: 2)
```
{% endtab %}

{% tab title="Java" %}
```java
this.x = somePoint.x; this.y = somePoint.y
this.x = 1; this.y = 2
```
{% endtab %}
{% endtabs %}

#### Superclass Expression

Superclass expressions can be translated directly, replacing `super.init` with just `super`.

{% tabs %}
{% tab title="Swift" %}
```swift
super.memberName
super[arguments]
super.init(arguments)
```
{% endtab %}

{% tab title="Java" %}
```java
super.memberName
super.get(arguments)
super(arguments)
```
{% endtab %}
{% endtabs %}

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

