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

A web repl is transpiled using GHCJS, placed under `docs/` and [hosted from this repository](http://yallop.computer/PL/repl.html).

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

## Overview

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

## Quick-facts

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

## Build

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

A built JS repl should be checked in under `docs/repl.html`. To rebuild:
```bash
script/build-js
```

### Run local build

Terminal repl:
```bash
script/pl
```

The web interface should be accessible by pointing a web browser at `/docs/repl.html`.

## Install TUI

```bash
script/install
```

You may then start a repl by calling:

```bash
pl
```

### Test

```
script/test
```

## Using
### Web
![Web REPL](https://github.com/syallop/PLRepl/blob/master/README/Web.png)
The web interface should be accessible at `docs/repl.html`.

### Terminal
Launching the `pl` executable creates a [brick](https://hackage.haskell.org/package/brick)-based
command line interface split into several panes.

![Overview](https://github.com/syallop/PLRepl/blob/master/README/Overview.svg)
- The top-left `widget` is an [PLEditor](https://github.com/syallop/PLEditor)-based editor for inputing [lispy](https://github.com/syallop/PLLispy) source code.
- The bottom-left widget is an output text area that displays the result of parsing/ type checking code entered into the editor widget. 
- The right widget displays a context of the types currently defined and available to be mentioned in the editor.  

#### Commands

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

#### Examples

Below are a couple of example functions entered into the repl.

Boolean and:
![Boolean And](https://github.com/syallop/PLRepl/blob/master/README/BooleanAnd.svg)

Subtracting two from a natural number:
![Subtract Two](https://github.com/syallop/PLRepl/blob/master/README/SubTwo.svg)

## Lispy Syntax Overview
For a full description, see the [Lispy README](https://github.com/syallop/PL/blob/master/Lispy/README.md)

### Expressions
- The following syntax may appear where an expression is expected.
- Each expression can always contain extra surrounding parenthesis.
- Expressions can always contain extra preceeding (and following) space without altering their parse.
- When printing, single or no spaces are prefered. Printing currently leans towards inserting parenthesis.
- In some cases preceeding spaces or parenthesis are required.

#### Lambdas
Lambdas are anonymous functions which abstract values over expressions.

| Parses           | Description |
| ---------------- | ----------- |
| <pre><code>λBool 0</code></pre>       | A function accepting an expression of type `Bool` and returns that expression. Types must only be annotated on the abstracted variable. Variables are referenced by an index and how many abstractions away they appear            |
| <pre><code>λBool (λNat 1)</code></pre> | Parentheses are used to nest expressions. This is a function accepting an expression of type `Bool` that returns a function that accepts an expression of type `Nat` and returns the first `Bool`-typed expression.      |

#### Binding
Refer to expressions bound by a lambda expression.

| Parses           | Description |
| ---------------- | ----------- |
| <pre><code>0</code></pre>              | The nearest expression bound by a Lambda abstraction. I.E. Inside `λBool 0` would refer to the expression typed `Bool`. |
| <pre><code>1</code></pre>              | The expression not the 0th, but the first binding away. I.E. Inside `λBool (λNat 1)` would refer to the expression typed `Bool`. |

#### Function application
Apply regular Lambdas to expressions.

| Parses           | Description |
| ---------------- | ----------- |
| <pre><code>@ 0 1</code></pre>          | Apply the expression bound 0 lambdas away to the expression bound one lambda away |
| <pre><code>@ (λBool 0) (*) </code></pre> | Apply an identity function to the zero-product |

#### Sum expressions
An sum expression has an index within some larger some type, in which it is one alternative.

| Parses            | Description |
| ----------------- | ----------- |
| <pre><code>+0 (*) (*) Nat </code></pre> | A value of a sum type. The index within the greater sum is `0`. The value is `(*)` - the empty product type. The first type in the sum is `(*)` - the type of empty products. The second type in the sum is `Nat`. |

#### Product expressions
A product expression combines many expressions into one where the order is fixed.

| Parses            | Description |
| ----------------- | ----------- |
| <pre><code>* 0 1 2</code></pre>         | A product of expressions bound 0, 1 and 2 abstractions away. |
| <pre><code>(*)</code></pre>             | The empty product |
| <pre><code>* (*) (*)</code></pre>       | A product of two empty products. |

#### Union expressions
A union expression has a type index into some larger type, in which it is one alternative. This differs from a sum type which has an order and where values are indexed by their position.

| Parses                | Description |
| --------------------- | ----------- |
| <pre><code>∪ Bool 0 Bool Nat Unit</code></pre> | A value of a Union type. The index within the greater union is the type `Bool`.  The value is `0` - a binding to an abstraction. The types that form the union are `Bool` `Nat` and `Unit`. |

#### Big Lambdas
Big Lambdas abstract over types, but still produce expressions.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>ΛKIND λ(?0) 0</code></pre>      | A function accepting a _type_ of kind `KIND` that produces a function that accepts an _expression_ with the previously bound type to produce that expression |

#### Type Binding
Refer to _types_ bound by a big lambda expression.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>?0</code></pre>                 | The nearest type bound by a Big Lambda abstraction. I.E. Inside `ΛKIND (?0)` would refer to the type kinded `KIND`. |
| <pre><code>?1</code></pre>                 | The type not the 0th, but the first binding away. I.E. Inside `ΛKINDA (ΛKINDB (?1))` would refer to the type kinded `KINDA`. |

#### Big application
Apply Big Lambdas - which abstract over types - to types to produce an expression.

| Parses                   | Description  |
| ------------------------ | ------------ |
| <pre><code># (Λ KIND λ(?0) 0) Bool</code></pre> | Apply the type `Bool` to a Big Lambda which takes types of kind `KIND`. |

### Types
This syntax may appear anywhere a type is expected. The same rule for expressions apply to types regarding parenthesis and whitespace.

#### Named
A Named type is associated with some 'externally' defined type definition. This
is 'external' in that the (current) expression/ type language provides no syntax
for creating these associations. Type names are currently an upper case
character followed by zero or more lower case characters.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>Bool</code></pre>                | The type associated with the name `Bool` |

#### Arrows
An arrow is the type of regular Lambda functions and denotes the type the
function accepts and the type of the expression produced.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>→ (*) Bool</code></pre>          | The type of a Lambda which binds the empty product and produces an expression with the named type `Bool` |

#### Sum types
Denotes the type of sum expressions as a left-to-right ordering of the constituent expression types.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>+ Bool Nat Unit</code></pre>      | The type of sum expressions that may be individually typed either `Bool`, `Nat` or `Unit` |
| <pre><code>+ Bool Bool</code></pre>          | The type of sum expressions that may be individually one of two kinds of `Bool` |

#### Product types
Denotes the type of product expressions as a left-to-right ordering

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>* Bool Nat Unit</code></pre>      | The type of product expressions that are typed `Bool`, `Nat`, `Unit` in order |
| <pre><code>*</code></pre>                  | The empty product type with no sub-expressions |

#### Union types
The type of union expressions.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>∪ Bool Nat Unit</code></pre>      | The type of union expressions that may individually be either `Bool`, `Nat` or `Unit` and are indexed by type rather than order |
| <pre><code>∪</code></pre>                  | The empty union type with no alternatives. There are no values which inhabit this type |

#### Big arrow
The type of big lambdas.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>\-> KIND Bool</code></pre>       | The type of big lambdas that accept a type with kind `KIND` and produce a value of type `Bool` |

#### Type lambda
A type level lambda corresponds is a lambda that abstracts over types to
produces types.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>Λ KIND Bool</code></pre>         | A type function that abstracts over a type with kind `KIND` and produces the type `Bool`. |
| <pre><code>Λ KIND ?0</code></pre>          | A type function that abstracts over a type with kind `KIND` and produces that type. |

#### Type application
Apply a type lambda to a type to produce a type.

| Parses                | Description |
| --------------------- | ----------- |
| <pre><code>/@ (Λ KIND ?0) Bool</code></pre>  | Apply the type `Bool` (kinded KIND) to the type lambda which returns the bound `Bool` |

### Case analysis
Expressions (and _not_ types) can be pattern matched by case analysis.

An entire case statement starts with "CASE" followed by a scrutinee expression then the
case branches. Zero or many case branches can exist, followed by a default
branch. Branches have two components, the pattern they match (which may contain
pattern bindings) and an expression in which any bindings are accessible as if
bound by lambda abstraction.

Branches are denoted by a `|` character. Abstractions are denoted by `?` character.

Example case statement:

```haskell
CASE expression ( -- Case analysis on some expression
    (|? (0))      -- The first branch matches anything and returns it.
    (|? (*))      -- The second branch will not be reached, but would
    (+)           -- The default branch will not be reached but returns the empty sum
)
```

Sums, products and unions can be pattern matched upon. Lambdas and other forms
of expression can not. Wherever an expression is allowed within a pattern, so is
a `?` which acts to bind that expression.

#### Bind
A bind matches the entire expression and binds it to be referenced as if
abstracted by a lambda. If a bind appears at the top level, the entire
expression is bound. If it appears as a sub expression, that sub-expression is
bound.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>?</code></pre>                  | Match any expression value. Sums, Products, etc |
| <pre><code>+0 ?</code></pre>               | If the sum expression is matched index 0, bind its expression |
| <pre><code>* ? ?</code></pre>              | Match both expressions in a product expression |

#### Sums
A sum pattern matches a sum expression. The index matches the sum expressions
alternatives left to right starting at 0.

Example parses:

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>+0 ?</code></pre>               | Matches when a sum expression is at index 0 within its sum type. |
| <pre><code>+0 (+1 ?)</code></pre>          | Matches when a sum expression is at index 0 within its sum type and that contained expression is at index 1 within its sum type. |

#### Products
A product pattern matches a product expression. All contained expressions must
match for the product to match as a whole.

| Parses             | Description |
| ------------------ | ----------- |
| <pre><code>* ? ?</code></pre>              | Matches any two expressions within a product type |
| <pre><code>* (+0 ?) (+1 ?)</code></pre>    | Matches only when both nested sum expression match |

#### Unions
A union pattern matches a union expression. A type is used as the index into the
union.

| Parses               | Description |
| -------------------- | ----------- |
| <pre><code>∪ Bool ?</code></pre>            | Matches when the expression has the `Bool` type within the union. |

## Developing

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

