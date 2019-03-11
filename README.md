# Swift on the JVM

**Swift on the JVM**, _swift4jvm_ for short, is a project that aims to bring the syntax and semantics of Apple's Swift programming language to the Java Virtual Machine.

The project consists of two parts:

1. A JVM-compatible implementation of the Swift **Standard Library**, which aims to mimic semantics exactly yet perform as fast as native Java using wrapperless mappings.
2. The Swift to JVM Bytecode **Compiler**, a tool that generates JVM-loadable `.class` files from `.swift`sources, and supports seemless bindings for existing Java libraries into Swift sources.

This reference book consists of three chapters, each of which will guide in the implementation of the above parts.

1. [Swift to Java Translations](swift-to-java-translations/) This chapter defines the various mappings for Swift language constructs to equivalent Java code. These translations are meant to roughly represent the transformations done by the compiler. It covers all syntactic elements defined in the [Swift Language Reference](https://docs.swift.org/swift-book/ReferenceManual/AboutTheLanguageReference.html), in order of occurrence to allow a side-by-side comparison.
2. Java to Swift Translations To allow the use of Java libraries and APIs in Swift source files, the compiler needs to know how to reconstruct Swift APIs from classes, fields, methods, and other declarative Java constructs defined as bytecode. It is not a goal of the chapter to define a complete decompiler, as method body level constructs like statements and expressions do not need to be translated.
3. Standard Library Implementation The JVM implementation of the Swift Standard Library needs to be defined in terms of class mappings, method implementations, boundaries as imposed by the target platform, and performance requirements. The third chapter covers these questions.

