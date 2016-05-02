# haskell-style-guide

This is forked from
[Chris Done's style guide](https://github.com/chrisdone/haskell-style-guide).

## Indentation and Alignment

Indentation should be two spaces. Do **not** use tabulator characters.

```haskell
case x of
  foo -> bar
  quux -> quuz
```

`do`-blocks should be lined up like this:

```haskell
j = do foo bar baz
       quux jyty arouo
       return ()
```

If the left-hand-side (LHS) is reasonably long, as will usually be the
case, do this instead:

```haskell
someObscenelyLongFunctionName =
  do foo bar baz
     quux jyty arouo
     return ()
```

Do **not** line up arrows like this:

```haskell
do foo     <- bar baz quux
   Tim bob <- quuz
   return foo
```

or like this

```haskell
case x of
  Left foo  -> 1
  Right bar -> 2
```

It ends up polluting diffs.

## Naming functions

Functions should be named using the `camelCaseConvention`. Do not use
`snake_case`.

## Grammar of binary operators

English is a Subject-Verb-Object (SVO) language. If you are writing a
binary operator, it needs to make sense as the verb. If the binary
operator is used in prefix format, it should be VSO, or
Verb-Subject-Object.

```haskell
verb :: Subject -> Object -> Result
```

That way, if the operator is used in infix form, we'd have

```haskell
subject `verb` object
```

#### Examples

1. In the simplest case, let's take a verb that takes an indeterminate
   direct object.

   ```haskell
   shot :: Person -> Person -> Death
   ```

   The SVO rule states that it has to make sense (in English) to write

   ```haskell
   i `shot` derek
   ```

   In this case, `i` is the subject, `shot` is the verb, and `derek` is the
   direct object. This makes no sense in English:

   ```haskell
   derek `shot` i
   ```
   That is an example of Object-Verb-Subject grammar. English is an SVO
   language, so write your binary operators so they make sense in VSO or
   SVO format.

   Haskell supports prefix and infix operators. It does not support
   suffix operators. If there ever comes a time where Haskell does
   support suffix operators, please write your binary operators in SOV
   form.

2. If your operator has a preposition in it, or if either the subject or
   the object is determinate, this should be even more obvious.

   ```haskell
   wentTo :: Subject -> Destination -> Result
   ```

   This makes sense:

   ```haskell
   i `wentTo` theStore
   ```

   This does not

   ```haskell
   theStore `wentTo` i
   ```
## Line length

Prefer 80 columns, but don't waste your time re-working code just to fit
within it. There, 120 is acceptable too. Don't try to shoehorn code that
shouldn't be broken up (like long strings) onto multiple lines. Let them
be.

## Modules

Always document the module header:

``` haskell
-- | What this module does.
module Foo where
```

Put the `where` on the same line unless there are exports.

### Exports

If there are exports then write them like this, with the `where`
coming on the line after.

``` haskell
module Foo
       ( a
       , b
       , c
       ) where
```

## Imports

Put imports one empty line after the module header. Always put imports
on separate lines, and sort them alphabetically:

``` haskell
module Foo where

import X
import Y
```

Always group import lists starting with your own project-local imports
first, because they are more important and fewer in number:

``` haskell
module Foo.Bar where

import Foo.Zot
import Foo.Bob

import Control.Monad
import Data.Text
import Data.List
import Data.Maybe
import System.IO
import System.Process
```

If you have qualified imports, align the rest of your imports accordingly.

``` haskell
module Foo.Bar where

import Foo.Zot
import Foo.Bob

import           Control.Monad
import qualified Data.Text as T
import           Data.List
import           Data.Maybe
import           System.IO
import           System.Process
```

Write import lists like this:

``` haskell
import X (foo, bar)
```

And if they don't fit on one line, like this:

``` haskell
import X ( foo
         , bar
         )
```

If you have many imports, prefer explicit qualification:

``` haskell
import qualified X as Z
```

Prefer to import types unqualified:

``` haskell
import           Data.Text (Text)
import qualified Data.Text as T
```

Unless they overlap, in which case it will be `T.Text`.

## Declarations

All declarations should be surrounded by a blank line. Declarations
should not have blank lines in them. If your code is so complicated,
split it up into names.

Always add type-signatures to top-level functions once they are
written, and document them:

``` haskell
-- | Main entry point.
main :: IO ()
main = foo bar

-- | Pretty print a name.
foo :: String -> IO ()
foo = bob

-- | Default dummy string.
bar :: String
bar = zot
```

## Functions

Prefer shorter functions to longer functions:

``` haskell
main =
  do foo bar (z * zz) …
     zot bob (z * zz) …
```

(Imagine longer code.)

This is improved by:

``` haskell
main =
  do openConnection
     makeCake
  where
    openConnection = foo bar y …
    makeCake = zot bob y …
    y = z * zz
```

This better documents what the function is doing.

When the code is larger, this is further improved by:

``` haskell
main =
  do openConnection y
     makeCake y
  where y = z * zz

openConnection y = foo bar y …

makeCake y = zot bob y …
```

Because decoupling allows for more code-reuse and easier to understand
context.

## Data types

Always layout sum types so that the `=` and `|` line up. Always
document data types. Always put parentheses around `deriving`s.

``` haskell
-- | Lorem ipsum amet patate.
data Foo = X
         | Y
         | Z
  deriving (A)
```

Always layout non-sum record types like this, and document the fields.

``` haskell
-- | Some record type.
data Foo = Foo { fooBar :: X -- ^ Bar stuff.
               , fooMu  :: Y -- ^ Mu stuff.
               , fooZot :: Z -- ^ Zot stuff.
               }
  deriving (B)
```

## Expressions

Always indent the parent before the children:

``` haskell
parent child1 child2
```

Or:

``` haskell
parent child1
       child2
```

Never mix and match single-line versus multi-line:

``` haskell
parent child1 child2
       child3
       child4
```

Never dangle children undearneath and behind the parent:

``` haskell
  parent
child1
child2
```

The `=`, `->` syntactic sugar are exceptions to the rule because they
denote separation from the actual parent, not a parent of their own:

``` haskell
foo =
  bar

case x of
  Foo bar mu ->
    a b c

case y of
  X a
    | p x ->
        bob foo
    | otherwise ->
        gogo gadget

\x y ->
  z y
```

## Do-notation

Always treat the `do` as the parent:

``` haskell
len = do foo
         bar
         mu
```

or

``` haskell
len =
  do foo
     bar
     mu
```

or

``` haskell
$(do runIO (putStrLn "Hello, World!")
     return [])
```

There is also the extreme space-saving layout following the general
parent-child layout guide:

``` haskell
len =
  do
    foo
    bar
    mu
```

Never use `(>>)` where `do` will do:

``` haskell
main = do bar
          mu
```

Not

``` haskell
main = bar >> mu
```

## Composition

Prefer to use composition where functions are expected, not
intermediate expressions:

``` haskell
foo = (foo . bar . mu) zot bob
```

This is okay.

``` haskell
foo = meaning zot bob
  where meaning = foo . bar . mu
```

This is better.

## Where clauses

Always indent where clauses two spaces:

``` haskell
main =
  do hello
     world
  where 
    go = print "Hello!"
    blah = x go
    ...
```

If you only have one function in the `where`, then something like this
is fine

``` haskell
main =
  do hello
     world
  where go = print "Hello!"
```

## Salvaging space

First, write your code on the same line as the parent:

``` haskell
foobarMu = parent child1
                  child2
```

But prefer to bring right-hand-sides (of `=`, `->`) down. Bring the
whole expression down:

``` haskell
fooBarMu =
  parent child1
         child2
```

If you need to save space, bring the children down:

``` haskell
fooBarMu =
  parent
    child1
    child2
```

## Collections

Write tuples, lists and records with spaces:

``` haskell
(a, b, c)

[a, b, c]
```

Note that if your list contains operators, try to group the elements
such that the operator does not have surrounding space:

```haskell
{x=a, y=b, z=c}
```

Layout multi-line tuples, lists and records with prefix comma:

``` haskell
( a
, b
, c
)

[ a
, b
, c
]

{ x = a
, y = b
, z = c
}
```

# If

Add two spaces for the `then` and `else` clauses

``` haskell
if x
  then y
  else x
```
