# Scope and Closures

## Scope

### TL;DR
- JavaScript uses variables to store and retrieve values (also referred to as _state_)
- The set of rules that dictate how we store variables and find those variables at a later time is called _scope_. 

### Compiler Theory
JavaScript is a **compiled language**. What does that mean?

**Compilation** 
- refers to the (typically three) steps a chunk of source code goes through during the traditional compiled-language process
- **Step 1: Tokenizing/Lexing**: break up a string of characters into meangingfulchunks called "tokens".
	- `var a=2;` becomes `var`, `a`, `=`, `2`, `1=;`
- **Step 2: Parsing**: takes the array of tokens and turns it into a tree of nested elements (Abstract Syntax Tree (AST)).
	
![Example Abstract Syntax Tree](/images/AST.jpg)
