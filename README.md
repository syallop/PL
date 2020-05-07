# PL - Experimental

This monorepo collects together several components into a Programming Language
which is slowly evolving according to my whims.

It is currently a [structurally typed](https://en.wikipedia.org/wiki/Structural_type_system)
[functional](https://en.wikipedia.org/wiki/Functional_programming) programming language with
anonymous functions, [sums](https://en.wikipedia.org/wiki/Tagged_union),
[products](https://en.wikipedia.org/wiki/Product_type) and [unions](https://en.wikipedia.org/wiki/Union_type). Types may be passed at the
expression level and type functions may exist at the type level. A terminal UI
[REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop)
exists to parse, reduce, type-check and print expressions.

It aspires to one day be:
- [total](https://en.wikipedia.org/wiki/Total_functional_programming): All computations must terminate or make 'progress'
- [mobile](https://en.wikipedia.org/wiki/Code_mobility): Code can be transparently moved to new locations
- [content-addressable](https://en.wikipedia.org/wiki/Content-addressable_storage): Code is immutable and identified by it's SHA rather than a moving name
- and have [distributed pattern matching](https://en.wikipedia.org/wiki/Join-calculus) semantics.

A [DHT](https://github.com/syallop/DHT) and [Join Calculus](https://github.com/syallop/Join-Language) project may be used here.

Where an off-the-shelf library could/ should have been used to solve a problem,
instead a new sub-project is spawned as learning/ fun has been prioritised over
development time and feature completeness. Some of the sub-components
are:
- [Grammar](https://github.com/syallop/PLGrammar): Reversible Grammars are used
  to generate parsers and printers from a single syntax definition.
- [Parser](https://github.com/syallop/PLParser): Applicative Parser Combinators
  with leftovers, backtracking and error collection.
- [Printer](https://github.com/syallop/PLPrinter): A pretty-printer allowing
  documents to be nested and line wrapped. 
- [Lispy](https://github.com/syallop/PLLispy): A lispy syntax for parsing and
  printing PL programs.
- [Editor](https://github.com/syallop/PLEditor): Components for building text
  editors with viewports and an underlying text-zipper data structure. 
- [Repl](https://github.com/syallop/PLRepl): Builds the terminal UI `pl` capable
  of parsing, reducing, type-checking and printing expressions.

A web repl is transpiled using GHCJS, placed under `docs/` and [hosted from this repository](http://yallop.computer/PL).

Some interesting techniques being explored across the codebase are:
- Fix point encoding of recursive types (such as expressions and types), to allow
  defining [recursion schemes](https://en.wikipedia.org/wiki/Category:Recursion_schemes)
  that abstract operations on the nested occurances. 
- [De Bruijn indexes](https://en.wikipedia.org/wiki/De_Bruijn_index) for
  reducing variables without fiddly renaming.
- Syntax descriptions as partial [isomorphisms](https://en.wikipedia.org/wiki/Isomorphism)
  to describe Grammars that can be interpreted as both Parsers and Printers to
  avoid printing syntax that can't be parsed.
- Applicative [Parser Combinators](https://en.wikipedia.org/wiki/Parser_combinator) for clean composition of parsers.
- Composition of [pretty-printed](https://en.wikipedia.org/wiki/Prettyprint) documents to isolate line-wrapping and text
  manipulation logic.
- [Terminal user interfaces](https://en.wikipedia.org/wiki/Text-based_user_interface) for quick manual testing and exploration.
- 2D [Zippers](https://en.wikipedia.org/wiki/Zipper_(data_structure)) for efficient navigation and viewport rendering in text editors
- [DSL composition](https://github.com/syallop/DSL-Compose) for combining Operational encodings of distinct, reusable DSL
  fragments.

# Overview

- [Quick facts](#Quick-facts)
- [Build](#Build)
  - [Run local build](#Run-local-build)
  - [Install](#Install)
  - [Test](#Test)
- [Using](#Using)
  - [Commands](#Commands)
  - [Examples](#Examples)
- [Lispy Syntax Overview](#Lispy-Syntax-Overview)
  - [Expressions](#Expressions)
    - [Lambdas](#Lambdas)
    - [Binding](#Binding)
    - [Function application](#Function-application)
    - [Sum expressions](#Sum-expressions)
    - [Product expressions](#Product-expressions)
    - [Union expressions](#Union-expressions)
    - [Big Lambdas](#Big-Lambdas)
    - [Type Binding](#Type-Binding)
    - [Big application](#Big-application)
  - [Types](#Types)
    - [Named](#Named)
    - [Arrows](#Arrows)
    - [Sum types](#Sum-types)
    - [Product types](#Product-types)
    - [Union types](#Union-types)
    - [Big arrow](#Big-arrow)
    - [Type lambda](#Type-lambda)
    - [Type application](#Type-application)
  - [Case analysis](#case-analysis)
    - [Bind](#Binds)
    - [Sums](#Sums)
    - [Products](#Products)
    - [Unions](#Unions)
- [Developing](#Developing)

# Quick-facts

- The core data-types currently consist of anonymous functions, sums, products
  and unions.

- Types are structural by default. I.E. if two types have the same structure,
  they can be used interchangeably.

- Functions may accept types to produce values.

- Type functions may accept types to produce types.

- Variables are referenced by counting how far away the function providing them
  appeared - they use De Bruijn indices.

- Case analysis must cover all cases and may do so by using a default value.

- There is currently no IO. The only computation that can be performed is the
  reduction of expressions.

- There are no primitive types (such as integers, bytes, strings etc). They must
  currently be built ontop of either ADTs or lambda encodings.

- One 'lispy' syntax is provided where expressions take the form of prefix
  tokens and lists of zero or many arguments/ sub-expressions.

- Components are agnostic to the syntax used for parsing/ printing and accepting
  'Grammar' definitions. This allows different syntaxes to be easily experimented
  with and possibly to interoperate.

- Grammar definitions are 'reversible' meaning they encode both how a syntax
  should be parsed and an expression should be pretty-printed.

# Build

This project is developed with Haskells [stack](https://docs.haskellstack.org/en/stable/README/) for the main dependencies and terminal interface.
The JS repl is built with [Nix](https://nixos.org/nix/) and [GHCJS](https://github.com/ghcjs/ghcjs).
Direct `cabal` support is untested.

Clone repository with all submodules:
```bash
git clone --recursive https://github.com/syallop/pl
```

To build all submodules and the terminal REPL (excluding the JS repl):
```bash
script/build
```

A built JS repl should be checked in under `docs/index.html`. To rebuild:
```bash
script/build-js
```

## Run local build

Terminal repl:
```bash
script/pl
```

The web interface should be accessible by pointing a web browser at `/docs/index.html`.

# Install TUI

```bash
script/install
```

You may then start a repl by calling:

```bash
pl
```

## Test

```
script/test
```

# Using
## Web
[![Web REPL](https://github.com/syallop/PLRepl/blob/master/README/Web.png)](http://yallop.computer/PL)
The web interface should be accessible locally at `docs/index.html`. A version is [hosted here](http://yallop.computer/PL).

## Terminal
Launching the `pl` executable creates a [brick](https://hackage.haskell.org/package/brick)-based
command line interface split into several panes.

![Overview](https://github.com/syallop/PLRepl/blob/master/README/Overview.svg)
- The top-left `widget` is an [PLEditor](https://github.com/syallop/PLEditor)-based editor for inputing [lispy](https://github.com/syallop/PLLispy) source code.
- The bottom-left widget is an output text area that displays the result of parsing/ type checking code entered into the editor widget. 
- The right widget displays a context of the types currently defined and available to be mentioned in the editor.  

### Commands

Commands are entered by pressing keys, either a single key (such as an up
arrow), or multiple (such as the up arrow while holding the control key).
Most commands will be sent to whatever `widget` we consider in `focus`. Some
commands are `global` and affect the entire repl.

Below is an overview of some of the commands the repl understands.

| Key combination       | Targets        | Action |
| --------------------- | -------------- | ------ |
| `up`                  | focused        | Move the cursor up in the focused widget | 
| `ctrl + up`           | focused        | Make the current view within a widget taller |
| `down`                | focused        | Move the cursor down in the focused widget |
| `ctrl + down`         | focused        | Make the current view within a widget shorter |
| `left`                | focused        | Move the cursor left in the focused widget |
| `ctrl + left`         | focused        | Make the current view within a widget narrower |
| `right`               | focused        | Move the cursor right in the focused widget |
| `ctrl + right`        | focused        | Make the current view within a widget wider |
| `any other character` | editor         | Insert character into the editor widget |
| `HOME`                | editor         | Insert a random code sample into the editor |
| `DELETE/ BACKSPACE`   | editor         | Delete the current character in the editor widget |
| `ENTER`               | editor         | Insert a newline in the editor widget | 
| `INSERT`              | editor, output | Grab the text in the editor and run it through the configured repl. This usually means parsing, type-checking and evaluating. Output appears in the output widget. If successful, the editor widget is cleared | 
| `PAGE-UP`             | global         | Switch focus to the next widget |
| `PAGE-DOWN`           | global         | Switch focus to the previous widget |
| `ctrl + l`            | editor         | Clear all text from the editor widget |
| `ESC`                 | global         | Exit the program |

### Examples

Below are a couple of example functions entered into the repl.

Boolean and:
![Boolean And](https://github.com/syallop/PLRepl/blob/master/README/BooleanAnd.svg)

Subtracting two from a natural number:
![Subtract Two](https://github.com/syallop/PLRepl/blob/master/README/SubTwo.svg)

# Lispy Syntax Overview
In the future a more human friendly syntax will be provided with syntax sugar
for higher level operations, less terse constructs and likely less parenthesis!

For now, a 'lispy' syntax is provided that is minimal, terse and without any
sugar.

Expressions, types and patterns are surrounded by parenthesis,
begin with a token character and then contain zero or more space separated
arguments. The number and types of argument can be looked up in reference syntax below.

Variables are numeric indexes which count the number of abstractions away a
variable should be bound.

Built in named types begin with an upper case character.

Example:
```
(λBool 0)
|| |   |
|| |   -- Second and last argument is a varible index 0
|| -- First argument is a named type
|--Token Lambda character
-- Parenthesis
```

Tokens will prefer to render with unicode but have ascii alternatives.

Spaces and newlines can be inserted freely without changing the meaning of an
expression - nothing is whitespace sensitive

Comments begin and end with a double quote and can span line. Semantically they
are attached to the following node which they should describe.

The above example is therefore equivalent to:
```
"The identity function, specialised to Booleans"
(\ (Bool)
   (0)
)
```

## Expressions
This section covers the syntax for expression literals. An expression takes
one of the following forms.

### Lambdas
Lambdas are anonymous functions which abstract values over expressions.

**Examples**:

An identity function specialised to Bool:
```
λBool 0
```
- Identified by the lambda token
- Accepts a parameter of type `Bool`
- Body is the expression `0`
- `0` refers to the variable bound 0 lambdas away - I.E. the preceeding `λBool`
- Return types can be infered and cannot be provided. The type of this
  expression is `-> Bool Bool`.
- Parenthesis are omitted as the expression can be parsed unambiguously at the
  top level.

A const function specialised to Bool and Nat:
```
λBool (λNat 1)
```
- Parenthesis are used to nest expressions.
- Lambdas only accept a single argument. A second argument is accepted by
  returning a second lambda in the body of the first.
- In a higher level syntax/ language extension this might be written `\Bool Nat 1`.
- The variable `1` refers to the outermost `λBool`.
- The type of this expression is `-> Bool (-> Nat Bool)`

### Binding
Refer to expressions bound by a lambda expression.

**Examples**:

Closest binding:
```
0
```
- The expression bound by the nearest lambda.
- Indexes begin at 0!
- Not a literal 0/ natural number!
- Are typed by the abstraction they refer to. E.G. Inside `λBool 0` would refer to the expression typed `Bool`.

Not the closest but the next binding:
```
1
```
- The expression bound not by the nearest lambda, but the next.
- Indexes begin at 0!
- In the expression `λBool (λNat 1)` would refer to the expression typed `Bool`.

### Product expressions
A product expression combines many expressions into one. The size and order is fixed. I.E. they are like tuples in some languages.

**Examples:**

Product of three expressions:
```
* 0 1 2
```
- The product of expressions bound 0, 1 and 2 abstractions away
- A products type mirrors its expression. E.G. If the bindings were typed `Nat`, `Bool` and `Unit` would have a type like `* Nat Bool Unit`.

Empty product of no expressions:
```
(*)
```
- The empty product contains no expressions and is often enclosed in parenthesis
  to reduce parsing ambiguities.
- This expression is often used for token values such as Unit, Zero or the Nil
  case in a list.
- Is typed `(*)`.

Singleton product (of empty product):
```
* (*)
```
- A product of a single value which is the empty product
- Is typed `* (*)`

### Function application
Apply regular Lambdas to expressions. 

**Examples**:

Apply the empty product to a function that returns it:
```
@ (λ(*) 0) (*)
```
- The `@` token denotes function application
- The first argument is the function and should be built from a lambda
- The second argument is the argument
- The type of function and argument must be equal
- Reduces to `*`, typed `*`

Apply a binding to another:
```
@ 0 1
```
- Apply the expression bound `0` lambdas away to the expression bound `1` lambda
  away.
- The type of the expression depends upon the result type of `0`.

### Sum expressions
A sum expression is an alternative within some larger type. It's position is
indicated by it's index.

**Examples:**

Empty product is the zeroth expression in the sum of empty Products and Bools
```
+0 (*) (*) Bool
```
- A sum expression begins with a `+` token.
- The first argument is an index denoting the position the expression takes
  within the overall sum.
  - This expression is in the `0`th position.
  - The index is required as types are allowed to appear multiple times.
  - Indexes begin at 0.
- The second argument is an expression, in this case the empty product `(*)`.
- One or many arguments follow which are an ordered list of _types_ the whole
  sum is composed of.
  - In this case the sum contains the empty sum as its first type `(*)` and a `Bool`ean as its second type.
- This expression is typed `+ (*) Bool` - the sum of empty products and booleans.
- Sums may contain one or more types. 

### Union expressions
A union expression is similar to a sum expression. The differences are that
there may not be duplicate types and the types are unordered. This means
indexes are types rather than numbers.

**Examples:**

A bound empty Product within a union of three types:
```
∪ (*) (*) Bool Nat (*)
```
- A union expression begins with a `U` token.
- The first argument is a type index denoting the type the expression has within
  the overall sum.
  - This expression is typed `(*)` - the empty product type.
  - Numerical indexes are not used as the types are not ordered semantically
- The second argument is an expression `(*)` - the empty product.
- Zero or many arguments follow which are an unordered list of unique _types_
  the whole union is composed of.
  - In this case the union contains `Bool`, `Nat` and the empty product `*`.
- This expression is typed `U Bool Nat (*)` which may be written as `U Nat (*) Bool`
  or any other equivalent permutation of types.
- Unions may contain zero or more types.

### Big Lambdas
Big lambdas are lambdas which abstract over types to produce an expression. This
is unlike lambdas which abstract over expressions to produce expressions (and
Type Lambdas which abstract over types to produce types).

**Examples:**

Identity function with a generic type:
```
ΛKIND (λ(?0) 0)
```
- The `Λ` token indicates the start of a big lambda
  - This token can be written `/\`
- The first argument is a _kind_ which is the type of types.
  - Simple types will have kind `KIND`.
  - Types which accept parameters will have kinds like `/-> KIND KIND`
- The second argument is an expression in which the bound type will be available
  to type bindings.
  - Type bindings are like expression bindings. They live in a separate context
    and are written with a preceeding `?`.
- The first argument to the nested lambda is a type-binding to the type bound 0
  abstractions away - I.E. the outer big lambda.
  - This means the nested lambda will accept and value with whatever type was
    applied.
- The expression is typed `/-> KIND (-> ?0 ?0) I.E. it takes a type of kind
  KIND then takes and returns an expression with that type.

### Big application
Apply Big Lambdas - which abstract over types - to types to produce an expression.

**Examples:**

Identity function supplied `Bool`.
```
 # (ΛKIND λ(?0) 0) Bool
```
- The '#` token indicates the start of a Big Application
- The first argument is the function and should be built from a Big Lambda
- The second argument is the argument and should be a _type_
- The kind of function and argument must be equal
- Reduces to the boolean identity function `λBool 0` typed `-> Bool Bool`.

## Types
This section covers the syntax for type literals. A type takes one of the following forms.

### Named
Named types are currently built-in and associate names to type definitions.
Named types have an associated kind, may be recursive and have a definition
which takes the form of a type.

Types are named by an upper case character followed by zero or more lower case
characters. (`U` is an exception which is the union type).

Current built in named types are:
- `Bool`
- `Nat`
- `Unit`
- `Maybe`
- `List`

Types are currently structural meaning anywhere a named type is expected, any
value compatible with the types definition is accepted.

Type definitions are displayed in `REPL`s and look like:

```
#Bool
  NonRec
  :: Kind
  = +(*) (*)
```
- The first line is the types name (not including the `#`)
- The second line describes whether the type is recursively defined or not.
  - `NonRec` types can be thought of as simple aliases. They will always reduce
    to their definition.
  - `Rec` types are aliases that may refer to themselves in some position. They
    will reduce more conservatively, sticking at each recursive instance to
    avoid expanding forever.
- The third line hints at the types `KIND`.
  - Types with `KIND` take no type parameters
  - Types with `-> KIND KIND` take a type of kind `KIND`.
- The final line gives the types definition.

Below are some built-in types whose definitions are elaborated in the following
sections. 

Unit:
```
#Unit
  NonRec
  :: Kind
  = *
```
- Unit is non-recursive
- It is equal to the empty product meaning expressions of type `Unit` can be
  cosntructed with `(*)`.

Booleans:
```
#Bool
  NonRec
  :: Kind
  = +(*) (*)
```
- Booleans are non recursive
- They are represented as a sum of two alternatives.
- The 0th alternative is an empty product `(*)` which denotes 'false'
  - False is constructed: `+0 (*) (*) (*)`
- The 1st alternative is an empty product `(*)` which denoted 'true'
  - True is constructed: `+1 (*) (*) (*)`

Nat:
```
#Nat
  Rec
  :: Kind
  = + (*) Nat
```
- Nat is the type of natural numbers
- They are represented as the sum of two alternatives
- The 0th alternative is an empty product `(*)` which denotes 'zero'
  - Zero is constructed: `+0 (*) (*) Nat`
- The 1st alternative is a recursive instance of the natural type which denotes
  the 'successor'.
  - One is the successor of zero and can be constructed: `+1 (+0 (*) (*) Nat) (*) Nat`

```
#Maybe
  NonRec
  :: -> Kind Kind
  = ΛKIND (+(*) ?0)
```
- Maybe accepts a type parameter to construct expressions that may or may not be
  present.
- It is represented as a type function which accepts a type to produce a sum of
  two alternatives.
- The first alternative is an empty product `(*)` which denotes 'nothing'.
  - Nothing of type Maybe Bool could be constructed: `+0 (*) (*) Bool`
- The second alternative is whatever type was supplied and denotes 'just'. 
  - Just zero of type Maybe Nat could be constructed: `+1 (+0 (*) (*) Nat) (*) Nat`

### Type Binding
Refer to _types_ bound by a big lambda expression or a type lambda.

**Examples:**

The nearest bound type:
```
?0
```

Not the nearest but the first binding away:
```
?1
```

### Arrows
An arrow is the type of regular Lambda functions and denotes the type the
function accepts and the type of the expression produced.

**Examples:**

Type of lambdas from empty Product to Bool:
```
→ (*) Bool
```
- Arrow types are identified by the first arrow token or `->`
- The first argument is the type of the functions argument. An expression with
  this type would abstract over the empty product type like `\(*) ...`.
- The second argument is the result type of the function.

### Sum types
The type of sum expressions give the constituent types, ordered from left-to-right.
The order is significant and will be used to construct and match on expressions.

**Examples:**

Sum of three types:
```
+ Bool Nat Unit
```
- A sum type begins with a `+` token
- What follows is one or many ordered types.
- Expressions

The sum of two types:
```
+ Bool Bool
```
- The sum of two distinct `Bool` types. 

### Product types
The type of product expressions give the constituent types, ordered from
left-to-right. The order is significant and will be used to construct and match
on expressions.

**Examples:**

Product of three types:
```
* Bool Nat Unit
```
- The product of `Bool`, `Nat` and `Unit`.
- Expressions of this type are constructed in sequence with values in the same
  order.

Empty Product type:
```
(*)
```
- The empty product contains no expressions and is often enclosed in parenthesis
  to reduce parsing ambiguities.
- This type is often used for token values such as Unit, Zero or the Nil
  case in a list.

Singleton Product type:
```
* Nat
```
- A Product of a single type Nat
- This type is _not_ particularly useful but exists because the empty and
  two-product _are_. 

### Union types
A union type is similar to a sum type . The differences are that
there may not be duplicate types and the types are unordered. This means
indexes are types rather than numbers.

**Examples:**

Union of three types:
```
∪ Bool Nat Unit
```
- The type of union expressions that may individually be either `Bool`, `Nat` or `Unit` and are indexed by type rather than order

Empty union:
```
∪
```
- The empty union type with no alternatives.

### Type lambda
Type Lambdas are type-level functions or lambdas that abstract over types to
produce types.
They are not the type of any expression.

**Examples:**

Accept a type with KIND to produce the `Bool` type:
```
Λ KIND Bool
```
- A Type Lambda begins with a `Λ` token which can also be written as `/\`
- The first argument is the kind that the applied type must have.
- The second argument is the body which may use type bindings to access the
  supplied type.

Identity function of types with kind KIND:
```
Λ KIND ?0
```
- This function accepts and returns any plain kinded type.

### Type application
Apply a type lambda to a type to produce a type.

**Examples:**

Pass `Bool` to a type identity function:
```
/@ (Λ KIND ?0) Bool
```
- Apply the type `Bool` (kinded KIND) to the type lambda, returning the bound
  `Bool`.

### Big arrow
The type of big lambdas which operate at the expression level, abstracting types
to produce expressions,

**Examples:**

Type of expressions which take a type and produce a boolean:
```
\-> KIND Bool
```
- The type of big lambdas that accept a type with kind `KIND` and produce a value of type `Bool`


Type of expressions which take a type then take and return values of that type: 
```
\-> KIND (-> ?0 ?0)
```

## Case analysis
Expressions (and _not_ types) can be pattern matched by case analysis.

An entire case statement starts with "CASE" followed by a scrutinee expression then the
case branches. Zero or many case branches can exist, followed by a default
branch. Branches have two components, the pattern they match (which may contain
pattern bindings) and an expression in which any bindings are accessible as if
bound by lambda abstraction.

Branches are denoted by a `|` character. Binds are denoted by `?` character.

Example case statement:

Subtract one:
```
"Subtract one for naturals greater than zero"
CASE 0 (
    (| (+1 ?)   0)
    (0)
)
```
- The first argument to the case statement is the scrutinee expression.
  - In this case it is a binding to a variable zero lambdas away.
- In the case statement `(| (+1 ?) 0)` is the first branch.
  - `|` denotes the start of a branch
  - The first argument `(+1 ?)` is the pattern to match
    - `+1` matches when the scrutinee expression is the 1st alternative in a sum
    - `?` binds the contained expression as if captured by a lambda abstraction
  - The second argument `0` is the body expression used when the pattern `(+1 ?)` matches.
    - Because the pattern contains a `?`, this `0` will refer to the matched expression.
- After the first branch is a default, recognisable by it's terminating position
  in the case body and the lack of preceeding `|`.
  - The default body is a `0` which will refer to the same expression as the
    scrutinee.

The default branch _could_ be replaced by a branch that explicitly matches the
'zero' case like:
```
    (| (+0 (*)) 0)
```
- Unlike the previous branch, there is no binding, only nested expressions that
  are compared for equality.


Sums, Products and Unions can be matched upon in patterns. Lambdas and other
forms of expressions can not.

Wherever an expression could occur in a pattern, so could a `?` which acts to
bind it.

### Bind
A bind matches the entire expression and binds it to be referenced as if
abstracted by a lambda. If a bind appears at the top level, the entire
expression is bound. If it appears as a sub expression, that sub-expression is
bound.

When multiple binds appear in a pattern the closest patterns are the right most.
Nesting is resolved depth-first.

**Examples:**

Match any expression value. Sums, Products, etc.
```
?
```

Match a sum alternative:
```
+ 0 ?
```
- If the sum expression is the 0th alternative, bind it's expression.

Match a product:
```
* ? ?
```
- Match both expressions in a product and bind them.

### Sums
A sum pattern matches a sum expression. The index matches the sum expressions
position within the alternative types left-to-right and starting at 0.


**Examples:**

0th Sum alternative:
```
+0 ?
```

1st Sum alternative:
```
+1 ?
```

Nested sum:
```
+0 (+1 ?)
```
- Matches when the outer expression is at index 0, and the nested sum is at
  index 1.

### Products
A product pattern matches a product expression. All contained expressions must
match for the product to match as a whole.

**Examples:**

Match any two expressions:
```
* ? ?
```

Match nested sum expressions:
```
* (+0 ?) (+1 ?)
```
- Matches only when the first product is the 0th sum and the second product is
  the 1st sum.

### Unions
A union pattern matches a union expression. Unlike a sum which matches by
position, unions match via their type.

Extract the Boolean expression:
```
∪ Bool ?
```
- Matches when the Union expression has the `Bool` type within the union.

# Developing

Several of the projects offshoot dependencies are potentially useful by
themselves and exist as separate git repositories tied together here by git
submodules. 

A useful workflow is to:

Watch for changes to any submodules and automatically rebuild and run tests:
```
script/watch-build-test
```
This allows changes to be made cutting across the dependencies, ensuring they
are up to date with each other without having to juggle SHAs.

Once changes are good:
- Commit to each of the directly effected subrepositories
  - Ensure the local `stack build` and `stack test` work
  - Update SHAs in the `stack.yaml`s as necessary to keep independant builds
    working as the monorepos build will always use the latest.
- Commit the updated submodules in the root monorepo repository
- Consider writing a script to perform the above dance as this can become
  painful.

