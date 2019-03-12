---
description: 'https://docs.swift.org/swift-book/ReferenceManual/Declarations.html'
---

# Declarations

### Top-Level Code

### Code Blocks

### Import Declaration

### Constant Declaration

### Variable Declaration

#### Stored Variables and Stored Variable Properties

#### Computed Variables and Computed Properties

#### Stored Variable Observers and Property Observers

#### Type Variable Properties

### Type Alias Declaration

### Function Declaration

#### Parameter Names

#### In-Out Parameters

#### Special Kinds of Parameters

#### Special Kinds of Methods

#### Throwing Functions and Methods

#### Rethrowing Functions and Methods

#### Functions that Never Return

### Enumeration Declaration

#### Enumerations with Cases of Any Type

**Enumerations with Indirection**

#### Enumerations with Cases of a Raw-Value Type

#### Accessing Enumeration Cases

### Structure Declaration

### Class Declaration

### Protocol Declaration

#### Protocol Property Declaration

#### Protocol Method Declaration

#### Protocol Initializer Declaration

#### Protocol Subscript Declaration

#### Protocol Associated Type Declaration

### Initializer Declaration

#### Failable Initializers

### Deinitializer Declaration

### Extension Declaration

{% hint style="info" %}
An example method that can be used to find the extension proxy class for a given object and protocol type is shown below. It assumes the proxy class uses the qualified name `mod.b.Ext$B$mod_p_P`, where `mod.b.B` and `mod.p.P` are the qualified names of `B` and `P`, respectively.

```java
static Class findExtensionProxyClass(Object o, Class protocolType) {
    Class objType = o.getClass();
    String protocolSuffix = "$" + protocolType.getName().replace('.', '_')
    for (Class superType : /* each super-class of o.getClass */) {
        String searchPath = /* superType.getName() with Ext$ inserted */;
        if (searchPath.startsWith("java.") {
            // extensions to java classes are placed in package _java.
            searchPath = "_" + searchPath;
        }
        searchPath += protocolSuffix;
        try {
            return Class.forName(searchPath);
        }
        catch (ClassNotFoundException ignored) {
            continue;
        }
    }
    return null;
}
```
{% endhint %}

#### Conditional Conformance

**Overridden Requirements Aren’t Used in Some Generic Contexts**

#### Protocol Conformance Must Not Be Redundant

**Resolving Explicit Redundancy**

**Resolving Implicit Redundancy**

### Subscript Declaration

### Operator Declaration

### Precedence Group Declaration

### Declaration Modifiers

#### Access Control Levels

