# funtext

**FUNctional TEXT**

Text Self-Processing

Lisp Flavor with Modern Syntax, Research Language

## funtext Syntax

Unlike languages like C++ or Python, which have hundreds of syntactic rules, operators, and keywords, funtext relies on a profoundly simple, uniform structure.

Everything in funtext is an **S-expression** (Symbolic Expression). That means every piece of code is just data, and all data can be treated as code—a property known as *homoiconicity*.

Here is the complete foundation of funtext syntax.

## 1. Atoms

First of all, any **text** is the **list** of words or elements

Therefore terms **text** and **list** are interchangable 

If it isn't a text, it's an atom. Atoms are the most basic building blocks and evaluate to themselves |with the exception of symbols|.

| Type | Examples | Description |
| --- | --- | --- |
| **Numbers** | `42`, `-3.14`, `1/2` | Integers, floats, and fractions. |
| **Strings** | `"Hello, World!"` | Text wrapped in double quotes. |
| **Booleans** | `t`, `nil` | True |`t`| and False |`nil`|. Note: `nil` is also an empty text `||`. |
| **Symbols** | `x`, `my-var`, `+`, `foo-bar` | Variable or function names. funtext symbols can contain almost any character, including hyphens, plus signs, and asterisks. |

## 2. Texts - Lists

Texts are collections of atoms or other texts, wrapped in pipes and separated by whitespace (no commas).

```funtext
|1 2 3|
|apple orange banana|
|1 |2 3| 4| : Texts can be nested inside texts

```

## 3. The Golden Rule of Evaluation

When funtext evaluates a text, it follows first strict rule: **a function preceding text , and ending text, both times with pipes**

```funtext
+| 2 3 |+     : Adds 2 and 3. Returns 5.
-| 10 4 2 |-   : Subtracts 4 and 2 from 10. Returns 4.
print| "Hi" |print  : Prints "Hi".

```

Because of this rule, funtext uses *prefix notation* (the operator always comes first), even for basic math.

## 4. Special Forms (The Exceptions)

If funtext strictly evaluated every text as a standard function, you couldn't write practical programs (e.g., an `if` statement shouldn't evaluate both the "true" and "false" branches). To fix this, funtext has **Special Forms** that break the normal evaluation rules.

Here are the most important firsts:

* **`quote`| or `'`|**: Stops funtext from evaluating a text.
```funtext
'|1 2 3|'      : Returns the data text |1 2 3| instead of trying to run '1' as a function.
quote| x |quote     : Returns the symbol x.

```


* **`fun`**: Defines a new function.
```funtext
fun|square|x|square 
  *|x x|*|fun

```


* `set` / `parameter`: Assigns values to variables.
```funtext
set|my-name "Alice"|set

```


* **`if`**: Conditional logic. It only evaluates the branch that matches the condition.
```funtext
if| >|5 3|>
    print|"Math works"|print    : If true
    print|"Universe broken"|print|if   : If false

```


* **`let`**: Creates temporary, local variables.
```funtext
let||x 10|
      |y 20||let
  +|x y|+     : Returns 30. x and y cease to exist after this.

```



## 5. Comments

funtext ignores anything after a colon on a line.

```funtext
: This is a single-line comment
+|2 2|+ : This comment is at the end of a line

//
This is a multi-line comment.
//

```


## funtext macros: 

Remember the golden rule : **In funtext, code is just data |texts|, and data can be treated as code.**

Because of this, you can write programs that write other programs. That is exactly what a macro does. It is a piece of code that runs *before* your program is executed, taking the raw code you wrote, rearranging it, and handing the new code back to the compiler.

Here is a breakdown of how they work and how they let you bend the language to your will.

## Functions vs. Macros

To see the magic, you have to understand the difference between how funtext treats a function and how it treats a macro.

* **Functions evaluate values:** When you call `my-func| +|1 2|+ 3|my-func`, funtext first evaluates `+|1 2|+` to `3`. Then it passes the values `3` and `3` into the function.
* **Macros evaluate code:** When you call `my-macro| +|1 2|+ 3|my-macro`, funtext **does not** do the math yet. It passes the literal text `+|1 2|+` and the atom `3` into the macro as raw data. The macro manipulates those texts and spits out new, modified code, which is *then* evaluated.

## The Toolkit: Backquote |`| and Comma |,|

To write macros easily, funtext gives you a templating system.

If you use a standard quote |`'`|, you freeze the whole text. But if you use a **backquote** |```|, you freeze the text *except* for items preceded by a **comma**   |`,`|. The comma "unfreezes" or evaluates just that specific part.

Think of it like string interpolation in other languages (e.g., `f"Hello {name}"` in Python), but instead of injecting strings, you are injecting abstract syntax trees.

```funtext
set|name 'Alice|set

'|Hello name|'   : Returns the literal text |HELLO NAME|
`|Hello ,name|`  : Returns the text |HELLO ALICE|

```

## Inventing Syntax: The `unless` Statement

Imagine funtext only came with an `if` statement, but you really wanted an `unless` statement (meaning "if this is *false*, do something").

In other languages, if the creators didn't build `unless` into the compiler, you'd just have to live without it. In funtext, you build it yourself.

```funtext
macro| unless|condition action|unless
  `if|not ,condition| 
       ,action|if`|macro

```

**How it works:**

1. You write your custom syntax: `unless|>|5 10|> print|"Math works!"|print|unless`
2. Before the program runs, the compiler sees it's a macro. It binds `condition` to the text `>| 5 10|>` and `action` to the text `print|"Math works!"|print`.
3. The macro plugs those raw texts into the backquoted template.
4. It generates this new code: `if| not|>| 5 10|>|not print|"Math works!"|print|if`
5. funtext then evaluates the generated `if` statement.

You just added a new control structure to the language, and it is entirely indistinguishable from the language's native keywords.

## Inventing a `while` Loop

Let's go bigger. Standard funtext has loop structures, but let's pretend it doesn't and invent a classic C-style `while` loop.

A `while` loop needs to check a condition, run some code, and then jump back to the start to do it again.

```funtext
macro| while|condition body|while
  `loop|
     |if|not ,condition| |return||if : If condition is false, break the loop
     ,body||loop                        : Otherwise, execute the body

```

Now you can write code like this:

```funtext
set|x 0|set
while|<|x 5|<
  set| x +|x 1|+|set|while

```

When the compiler sees this, the macro steps in, tears your `while` loop apart, and rewrites it into native funtext primitives that the compiler understands.

## Why This is So Powerful

Because of macros, funtext isn't just a language: it's a language for building other languages. If you are writing a program to simulate electrical circuits, you don't have to write funtext code that *acts* like a circuit simulator. You can use macros to literally invent a "Circuit-Description Language" right inside funtext, creating your own keywords like `wire`, `resistor`, and `ground`.

This ability to mold the language to fit the problem perfectly is why programmers often refer to funtext as the ultimate programmable programming language.



## first and next

If there are two functions that define funtext, they are **`first`** and **`next`** 
where **`first`** is lisp's `car` and **`next`** is `cdr`

Since everything in funtext is built on texts, you need a way to take texts apart. That is exactly what these two functions do: first grabs the first item, and the other grabs everything else.

## What They Do

Think of a funtext text as a train.

* **`first`** detaches and returns the engine |the very first element|.
* **`next`** returns all the remaining train firsts trailing behind it.

```funtext
set|my-text '|apple orange banana pear|'|set

: first gets the first element
first|my-text|first 
: Returns: APPLE

: after gets a text of everything EXCEPT the first element
next|my-text|next
: Returns: |ORANGE BANANA PEAR|
```

Notice that `first` usually returns an atom |a single item|, while `next` always returns another text.

## Text Processing Functions

```funtext

comb|text1 text2|comb : combining 2 texts

reverse|text|reverse : putting text elements in reversed order

split|text |patter||split : split text into several according to pattern

sub|text |pattern||sub : taking subset of text according to pattern

perm|text |patten||perm : make text permutations according to pattern

```