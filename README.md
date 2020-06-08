# unison-memo

unison-memo is a tiny [Unison](https://unisonweb.org) memoization library.

## Installation

Install in Unison with the following ucm command:
```
pull https://github.com/anovstrup/unison-memo:releases._v1 .external.anovstrup.memo.v1
```

## Usage

Use the `Memo.memo : '{e} a ->{e, Memo} a` function to memoize a computation. For example, here is a memoized recursive implementation of the Fibonacci function:

```
memoFib : Nat ->{Memo} Nat
memoFib n =
  Memo.memo 'let
    if n < 2 then n
    else memoFib (drop n 1) + memoFib (drop n 2))
```

A memoized computation must be evaluated in a context that provides the `Memo` ability. The `Memo.run : '{e, Memo} a ->{e} a` function provides one such evaluation context:

```
fib : Nat -> Nat
fib n = Memo.run '(memoFib n)

> fib 30
  ⧩
  832040
```

`Memo.run` is backed by the `Map` data structure from the [Unison base library](https://github.com/unisonweb/base), but alternative caching strategies can be used by implementing an alternative `Memo` handler. The `Memo.run` implementation ensures that the result of each (sub-)computation memoized with `Memo.memo` is evaluated only once (within an enclosing `Memo.run thunk` call). If such a (sub-)computation performs any effects using other abilities, those effects are also performed only once.
