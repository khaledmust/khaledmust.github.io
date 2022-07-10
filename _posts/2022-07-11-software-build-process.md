---
title: "Software Build Process"
layout: post
categories: Embedded-Systems
---

# Table of Contents

1.  [Introduction](#org0e9bb8d)
2.  [Overview of the build process](#orgd8a996e)
3.  [Preprocessor](#org95296c5)
    1.  [Input](#orgc1b719e)
    2.  [Objectives](#org72a2d36)
    3.  [Output](#org2e33c34)
4.  [Compiler](#orgf4f6e71)
    1.  [Input](#org7d024a7)
    2.  [Objectives](#org3ccb0e3)
        1.  [Symbol table](#org9af8472)
        2.  [Front-end processes of the compiler](#orgc6b257e)
        3.  [Back-end processes of the compiler](#org6f89512)
    3.  [Output](#org893d287)
    4.  [Why don't we burn (write) the generated binary file from the compiler into the target machine?](#org4200408)
5.  [Linker](#orgf1f06dc)
    1.  [Input](#org5be9db9)
    2.  [Objectives](#org60c48a3)
        1.  [Linkage of different files into a single file](#orgcb1cf19)
        2.  [Resolution](#org98c0677)
        3.  [Section merging](#org4bab9e9)
    3.  [Output](#orgfb47630)
6.  [Locator](#orgf0a65b5)
    1.  [Input](#org5af7431)
    2.  [Objective](#orgad28e0c)
    3.  [Output](#org0d044ec)



<a id="org0e9bb8d"></a>

# Introduction

In this article, we are going to have a deep dive into the different processes that occurs during the generation of the executable of our source file.
Nevertheless, we are not going to cover every detail as it's impossible to be done in one article, but will have an overview grasp on the underlying beauty that occurs when we compile our code.


<a id="orgd8a996e"></a>

# Overview of the build process

![img](./assets/software-build-process.png)
For every process we are going into its **input**, **objective** and its **output**.


<a id="org95296c5"></a>

# Preprocessor


<a id="orgc1b719e"></a>

## Input

-   The process takes *a single source file* written in high-level language.
-   It can only operate on a single file at a time.


<a id="org72a2d36"></a>

## Objectives

-   The preprocessing phase performs *textual transformation* on the source code file, and this includes the following:
    1.  Merging of multiple lines statement that is broken by the `\` operator.
    2.  Inclusion of the header file (if specified).
    3.  Replacement of the user comments with a single space.
    4.  Macro expansion.
    5.  Evaluation of conditions such as `#IF`.
    6.  Perform diagnostics such `#error` and `#warning`.


<a id="org2e33c34"></a>

## Output

The output is a file called *intermediate file or translation unit* such that for every source code file as an input a corresponding intermediate file exists.


<a id="orgf4f6e71"></a>

# Compiler

-   On an abstract level, a compiler translates the high-level language source file into native low-level object code file.
-   It also groups the different symbols into their respected sections, such that code is placed in *text section*, initialized global variables in *data section* and uninitialized global variables in *.bss section*.


<a id="org7d024a7"></a>

## Input

-   It takes the intermediate file (translation unit) from the previous step.
-   Similar to the preprocessor, the compiler operates only on a single translation unit at a time.


<a id="org3ccb0e3"></a>

## Objectives

-   Before getting into the operations of a compiler, let's have an overview of the different processes that makes up a compiler:
    ![img](./assets/internal-compiler-process.png)
-   The compiler's operation is broken down into **front-end** and **back-end**.
-   The front-end processes are:
    1.  Lexical analysis (Scanning)
    2.  Syntax analysis (Parsing)
    3.  Semantic analysis
-   And, the back-end processes are:
    1.  Intermediate code generation
    2.  IR code optimization
    3.  Target code generation
-   Beside these processes, the compiler maintain a table known as the *symbol table*.


<a id="org9af8472"></a>

### Symbol table

-   The symbol table is a *data structure* that is maintained by the compiler.
-   This data structure is filled and maintained during the different phases of the compilation process.
-   At the end it contains details about every symbol in the translation unit we are operating on, including its name, value, scope, and other different attributes.


<a id="orgc6b257e"></a>

### Front-end processes of the compiler

1.  Lexical analysis (Scanner)

    -   This process involves the scanning of the C source code and identifying the different tokens in the source file.
    -   There are six different tokens in C language, and they are:
        1.  Keywords
        2.  Identifiers
        3.  Constants
        4.  Strings
        5.  Special symbols
        6.  Operators

2.  Syntax analysis (Parser)

    -   This process involves checking the syntax of the code against C rules.
    -   The output of this process is a *parse tree*.
        Parse tree retains all the information on the input, including whitespace.

3.  Semantic analyzer

    -   This process is considered the first logical operation that the source code undertakes.
    -   A statement could be syntactically correct but semantically incorrect.
        For example, consider a variable `foo` that has a literal string assigned to it, we then try to perform a mathematical operation on this variable, although it's syntactically correct `<Identifier> <Operator> <Identifier>` it's semantically incorrect as it's illogical to perform a mathematical operation on a string literal.
    -   The output of this process is what is known as *Abstract Syntax Tree (AST)*.
        There are different representations that the compiler could produce other than AST.


<a id="org6f89512"></a>

### Back-end processes of the compiler

What happens at the back-end of the compiler is the generation of an *intermediate representation (Intermediate code)* that is then optimized and then the binary for the target machine is generated.

1.  Intermediate representation (IR)

    -   Why do we need an intermediate code, why don't we just translate the source code into machine language?
        -   The reason for having such an intermediate representation is for optimization and portability purposes.
        -   Instead of having different intermediate code generators and different optimizers, we would only have different code generators and one single optimizers.
            ![img](./assets/intermediate-representation.png)


<a id="org893d287"></a>

## Output

-   So at the end of the compilation process an object code is generated (a single object code for every input translation unit). - - - This object code is formatted according to a standard, this could be COFF or ELF (Executable and Linkable Format) depending on the compiler.


<a id="org4200408"></a>

## Why don't we burn (write) the generated binary file from the compiler into the target machine?

Although the compiled code (object code) contains instructions for the target machine, but we can't write to it as it could be missing some unresolved symbols that the compiler wasn't able to resolve it.
And the main reason is that the compiler doesn't assign the code or the symbol any physical addresses but rather logical addresses (an offset), so the linking process is essential.


<a id="orgf1f06dc"></a>

# Linker


<a id="org5be9db9"></a>

## Input

The linker takes two inputs:

-   The library file(s) that are needed to be linked to the source code.
-   The object code(s) generated from the previous compilation process.
-   Unlike the compiler, the linker operates on all the files in our program (including library files).


<a id="org60c48a3"></a>

## Objectives


<a id="orgcb1cf19"></a>

### Linkage of different files into a single file

-   The linker as the name suggests, links all the object files and the library files needed to produce a single output binary file.
    Note that the linker operates on *selective linking* meaning that it doesn't include the
    whole library, but rather only the needed parts.


<a id="org98c0677"></a>

### Resolution

-   The linker also performs resolution for the unresolved symbols in the different source
    files that the compiler wasn't able to resolve.
    Take for example the unresolved symbol `foo`, the linker searches across the different
    source files and when it finds a symbol declaration with the same name and links it to the
    unresolved variable `foo` such that at the end it contains an address for the declaration
    of the resolved symbol.


<a id="org4bab9e9"></a>

### Section merging

-   Since every object file generated by the compiler contains its own memory sections, the linker merges those different sections into one section such that the output file contains only single text section, single data section and single .bss section.


<a id="orgfb47630"></a>

## Output

-   The linker produce a single object file that all its symbol has been resolved and linked to all the required libraries, this object file is known as *relocatable file*.
-   This object file is formatted in the same format as the object file generated from the compilation process.
-   This object file is not ready to be burned to the target, as it is still missing the physical addresses for the symbols and the code.


<a id="orgf0a65b5"></a>

# Locator


<a id="org5af7431"></a>

## Input

It takes two input files:

1.  The relocatable file of the previous process.
2.  A *linker script* containing the memory layout of the target machine.
    The linker script could be thought of as a map describing the layout of the memory of the target machine, that starting and the ending address of every section.


<a id="orgad28e0c"></a>

## Objective

The assignment of physical address to every opcode and symbol in the binary file generated.
Thus ready to be burned on the target machine.


<a id="org0d044ec"></a>

## Output

A complete binary file that could be burned (uploaded) on the target machine.

