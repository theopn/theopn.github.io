+++
title = "Use-mention Distinction, Quasi-quotation, and Functional Programming Languages"
date = "2023-10-17"
description = "How a Computer Scientist Became a Philosopher"
tags = ["Philosophy", "Logic"]
+++

## Use-mention Distinction

Use-mention distinction is one of the first things you learn when you are studying analytical philosophy.
It is a very simple concept. Consider the following two sentences:

> 1. Socrates is my friend
> 2. 'Socrates' is an eight-letter word.

In the first sentence, "Socrates" was *used* to describe the influential Greek philosopher.
In the second sentence, "Socrates" was *mentioned* to describe the English name of the influential Greek philosopher.

Here is a trickier example.

> What *is* means is and therefore differs from *is*, for 'is is' would be nonsense.
> -- Bertrand Russell, My Philosophical Development, p. 63

- Q: Can you highlight which words were *mentioned*?
- A: What **_is_** means is and therefore differs from **_is_**, for '**is** **is**' would be nonsense.

By now, you probably figured out that philosophers like to use single quotes to denote mentions.
You can fancy other ways to denote the mentioning of a word (e.g., double quotes, italics), but philosophers and logicians like to use single quotes (double quotes are just punctuations).

Use-mention distinction is a crucial topic in concepts like [identity](https://en.wikipedia.org/wiki/Identity_(philosophy)) and [set theory](https://en.wikipedia.org/wiki/Set_theory).

> { 2, 1 + 1, 4 - 2 }

The cardinality of the set is one since members in the notation have the same values after the arithmetic evaluation.

(More specifically, the set above forms a bijection between the set { 2 } and thus the same cardinality 1.
I was going to make a Star Wars reference with `{Darth Vader, Anakin Skywalker, Luke Skywalker's father}`, but I realized that some readers would be slightly skeptical about it.)

> { '2' '1 + 1', '4 - 2'}

The cardinality of the set is three because the set contains three distinctive strings consisting of Arabic numbers and arithmetic symbols.

## Quasi-quotation

Logicians like nitty-gritty details (I mean, what mathematician doesn't?).
They were studying propositional logic and one day realized, "The way we explain the definitions of object language symbols is wrong!"

Before we get into this, let's clarify some terms.

- Object language: The language we study. In logic and most fields of mathematics, they are formal languages with specific formation rules. In our case, it is the language of propositional logic
- Metalanguage: The language we use to describe the object language. In our case, it is slightly extended English.
- Metavariable: A symbol of the metalanguage that stands for a general element of the object language.
    For example, in the metalanguage sentence "let A be the well-formed formula (wff) of the object language L", A is the metavariable that stands for some general wff of L.
- You cannot *use* object language symbols in the metalanguage.
    For example, "~p v q contains the disjunction symbol" is an incorrect sentence, just like "I drank 우유 today" is an incorrect English sentence.
    "'~p v q' is a wff of L" is a correct sentence the same way "'우유' means milk in Korean" is a grammatically correct English sentence.

Now, back to our problem.
Following is the definition of the object language symbol '~' in our object language L:

> 1. Any lowercase Roman letter is a well-formed formula (wff) of L.
> 2. If φ is a wff of L, then ~φ is a wff of L.
> 3. Nothing else is a wff of L.

To summarize the intended definition: If 'p' is a wff of L, then so is '~p', '~~p', '~~~p', ...

(Note that we are purely concerned about the syntax of the object language and not semantics.
Forget what you know about the negation sign.
'~p' is nothing but a symbol attached to another symbol that happens to be the valid wff of L.)

However, the definition is problematic in terms of use-mention distinction: φ is a metavariable of the metalanguage, whereas '~' is a symbol of object language; you cannot mix those two!

Would the following definition be correct?

> 2.1 If φ is a well-formed formula (wff) of L, then '~φ' is a well-formed formula (wff) of L.

No. Again, there was a language mix-up here, and interpreting this literally, we are saying that: "If, say, 'p' is a wff of L, then so is '~φ'. If, say '~~~~~p' is a wff of L, then so is '~φ'..."

So the correct definition is:

> 2.2 If φ is a well-formed formula (wff) of L, then the result of concatenating '~' and φ (in that order, from left to right) is a well-formed formula (wff) of L.

Hmm, okay.
But our life would be much easier if there was a symbol denoting the result of concatenating an object language symbol '~' and wff *denoted by the metavariable 'φ'*.
Good news: there is, and it is called Quasi-quatation!

> 2.3 If φ is a well-formed formula (wff) of L, then ⌜~φ⌝ is a well-formed formula (wff) of L.

Quasi-quotes are lifesavers in more complex definitions with more object language symbols such as disjunction:

> 1. Any lowercase Roman letter (with or without subscripts) is a well-formed formula (wff) of L.
> 2. If φ and ψ are wff of L, then the result of concatenating '(', φ, 'v', ψ, and ')' (in that order, from left to right) is a wff of L.
> 3. Nothing else is a wff of L.

That is one hell of a statement.
With Quasi-quotation:

> 2.1. If φ and ψ are well-formed formulas (wff) of L, then ⌜(φ v ψ)⌝  is a well-formed formula (wff) of L.

That looks much better.

## Quasiquote in Lisp

Okay, cool story Mr. Philosopher.
You are basically making up problems here.
I will go ahead and collect my sweet SWE salary.
Well, wait, Quasi-quotation is a concept in computer science too!

In Lisp (in Common Lisp, Scheme, Racket), we have quasiquote, the adoption of philosophical quasi-quotation in metaprogramming.

```scheme
'(1 2 x)   ;  ==> (list 1 2 x)
```

In the example above, the quoted (`'`, you can also use the quote function directly: `(quote (1 2 x))`) list are returned literally.
The resultant list contains numbers 1 and 2 and the symbol `x`.

Sometimes, you want some elements in the list to be evaluated, just like how you wanted a metavariable in quasi-quotations to be interpreted as object language symbols.
In Lisp, you can use `quasiquote` (```) and `unquote` (`,`) operators to partially evaluate a list.

```scheme
(define x 3)

'(1 2 ,x (+ 2 2)) ;  ==> (list 1 2 ,x (+ 2 2))
`(1 2 ,x (+ 2 2)) ;  ==> (list 1 2 3 4)
```

Quasiquote is also present in other functional programming languages like Scala, Haskell, etc.

And this is one of many examples of why you should not overlook philosophy and logic as a computer scientist.

## References

- Metalogic: An Introduction to the Metatheory of Standard First Order Logic by Geoffrey Hunter
- U of Washington CSE341 website on Scheme quoting: https://courses.cs.washington.edu/courses/cse341/04wi/lectures/14-scheme-quote.html
- Wikipedia Quasi-quotation: https://en.wikipedia.org/wiki/Quasi-quotation
- Wikipedia Lisp self-evaluating form: https://en.wikipedia.org/wiki/Lisp_(programming_language)#Self-evaluating_forms_and_quoting

