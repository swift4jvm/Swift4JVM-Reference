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

TBD

**Capture Lists**

TBD

#### Implicit Member Expression

Implicit member expressions are syntactic sugar, so their translation depends on their qualified form. See [Explicit Member Expression](expressions.md#explicit-member-expression).

#### Parenthesized Expression

Parenthesized expressions behave the same in both languages.

#### Tuple Expression

TBD

#### Wildcard Expression

Wildcard expressions can only be used as the target of an assignment or destructuring. See [Assignment Operator](expressions.md#assignment-operator).

#### Key-Path Expression

TBD

#### Selector Expression

TBD

#### Key-Path String Expression

TBD

### Postfix Expressions

#### Function Call Expression

Function call expressions are technically a call applied to a member reference. As such, the translation is the translation of the member access according to [Explicit Member Expressions](expressions.md#explicit-member-expression), and a call to the relevant functional method of the resulting function type. In practice, the optimizer should take care of the lambda creation followed by the functional method call.

Trailing closures behave as if they were the last argument within the argument list. The special rule for operators is translated using lambda expressions \(see [Closure Expressions](expressions.md#closure-expression)\).

{% tabs %}
{% tab title="Swift" %}
```swift
someFunction(x: x, f: {$0 == 13})
someFunction(x: x) {$0 == 13}
myData.someMethod() {$0 == 13}
myData.someMethod {$0 == 13}
someClosure()
someFunction(op: +)
```
{% endtab %}

{% tab title="Java" %}
```java
someFunction(x, $0 -> $0 == 13)
someFunction(x, $0 -> $0 == 13)
myData.someMethod($0 -> $0 == 13)
myData.someMethod($0 -> $0 == 13)
someClosure.apply()
someFunction((t1, t2) -> t1 + t2)
```
{% endtab %}
{% endtabs %}

#### Initializer Expression

Initializer expressions behave like a reference to an initializer, not a call, and are a special version of the [explicit member expressions](expressions.md#explicit-member-expression). As such, a translation strategy uses lambda expressions or constructor references \(see `f1`, `f2` and `f3` in the example\). It is up to the optimizer to detect a call to a functional method of a new lambda expression \(`s1`, `s2`, `s3`\).

{% tabs %}
{% tab title="Swift" %}
```swift
let f1: (Int) -> String = String.init
let s1 = SomeType.init(data: 3)
let f2 = SomeType.init(data:)
let s2 = SomeType(data: 1)
let s3 = type(of: someValue).init(data: 7)
let f3 = type(of: someValue).init(data:)

```
{% endtab %}

{% tab title="Java" %}
```java
Function<Integer, String> f1= String::new;
SomeType s1 = new SomeType(3);
Function<Integer, SomeType> f2 = SomeType::new;
SomeType s2 = new SomeType(1);
SomeType s3 = Runtime.newInstance(someValue.getClass(), 7);
Class t1 = someValue.getClass;
Function<Integer, SomeType> f3 = t2 -> Runtime.newInstance(t1, t2);
```
{% endtab %}
{% endtabs %}

#### Explicit Member Expression

Explicit member expressions can refer to many different things with wildly different Java equivalents.

If the identifier refers to a submodule or class, it translates as-is.

{% tabs %}
{% tab title="Swift" %}
```swift
SomeModule.SomeSubModule.SomeClass
```
{% endtab %}

{% tab title="Java" %}
```java
somemodule.somesubmodule.SomeClass
```
{% endtab %}
{% endtabs %}

If it refers to a property, a call to the getter is generated. Assignments to the expression translate to a setter call. This also applies to tuple access or modification.

{% tabs %}
{% tab title="Swift" %}
```swift
let c = SomeClass()
let y = c.someProperty
c.someProperty = 3
var t = (10, 20, 30)
t.0 = t.1
```
{% endtab %}

{% tab title="Java" %}
```java
final SomeClass c = new SomeClass();
final int y = c.getSomeProperty();
c.setSomeProperty(3);
Tuple<Integer, Integer, Integer> t = Tuple.of(10, 20, 30);
t.set0(t.get1());
```
{% endtab %}
{% endtabs %}

Explicit member expressions referring to methods, like [initializer expressions](expressions.md#initializer-expression), become method references.

{% tabs %}
{% tab title="Swift" %}
```swift
let b = instance.someMethod(x:y:)
let d: (Int, Bool) -> Void  = instance.overloadedMethod(x:y:)
```
{% endtab %}

{% tab title="Java" %}
```java
final BiFunction<Integer, Integer, Void> b = instance::someMethodXY;
final BiFunction<Integer, Integer, Void> d = instance::overloadedMethodXY;
```
{% endtab %}
{% endtabs %}

#### Postfix Self Expression

To translate the expression variant of this, all that needs to be done is removing the `.self`. The type variant is equivalent to the `.class` suffix in Java.

{% tabs %}
{% tab title="Swift" %}
```swift
x.self
x.self = y
SomeType.self
```
{% endtab %}

{% tab title="Java" %}
```java
x
x = y
SomeType.class
```
{% endtab %}
{% endtabs %}

#### Subscript Expression

A subscript expression translates to a call to `get`, or `put` when it is assigned to.

{% tabs %}
{% tab title="Swift" %}
```swift
foo[bar]
foo[bar, baz]
foo[bar] = baz
```
{% endtab %}

{% tab title="Java" %}
```java
foo.get(bar)
foo.get(bar, baz)
foo.put(bar, baz)
```
{% endtab %}
{% endtabs %}

#### Forced-Value Expression

The force unwrap operator can be translated by omission, but is more useful to insert code that triggers a `NullPointerException` at its use site instead of later. When the forced-value expression is immediately followed by a virtual method call, the helper code is not necessary as the call triggers the exception if the receiver is `null`.

{% tabs %}
{% tab title="Swift" %}
```swift
let a = b!
b!.someMethod()
var x: Int? = 0
x! += 1
```
{% endtab %}

{% tab title="Java" %}
```java
b.getClass(); final String a = b;
b.someMethod();
Integer x = Integer.valueOf(0)
x = Integer.valueOf(x.intValue() + 1)
```
{% endtab %}
{% endtabs %}

#### Optional-Chaining Expression

TODO

