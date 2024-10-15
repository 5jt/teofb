# The cycle of 15

!!! important "In your session"

    ```apl
          )CS #.ch3
          assert←assert      ⍝ local alias
          FIZZ_BUZZ←FIZZ_BUZZ  ⍝ local copy
    ```

Grus indexes an array in this elegant solution.

```python
CYCLE_OF_15 = ['fizzbuzz', None, None, 'fizz', 
               None, 'buzz', 'fizz', None, 
               None, 'fizz', 'buzz', None, 
               'fizz', None, None]

def fizz_buzz(n: int) -> str:
    return CYCLE_OF_15[n % 15] or str(n)

# Joel Grus. fizzbuzz (p. 40). Kindle Edition. 
```

The fizzes and buzzes repeat every 15 numbers.
Modulo-15 of `n` yields either a string or a null.
The `or` operator replaces a null with `n` as a string.

We can do this in APL with empty strings.

```apl
      CYCLE_OF_15←'fizzbuzz' '' '' 'fizz' '' 'buzz' 'fizz' '' '' 'fizz' 'buzz'
         '' 'fizz' '' ''
      or←{×≢⍺:⍺ ⋄ ⍕⍵}

      CYCLE_OF_15∘{((⎕IO+15|⍵)⊃⍺)or ⍵}¨⍳20
┌─┬─┬────┬─┬────┬────┬─┬─┬────┬────┬──┬────┬──┬──┬────────┬──┬──┬────┬──┬────┐
│1│2│fizz│4│buzz│fizz│7│8│fizz│buzz│11│fizz│13│14│fizzbuzz│16│17│fizz│19│buzz│
└─┴─┴────┴─┴────┴────┴─┴─┴────┴────┴──┴────┴──┴──┴────────┴──┴──┴────┴──┴────┘
```

Using `⎕IO` instead of 1 emphasises that the increment is to shift the modulus from the range from 0–14 to 1–15.

## Equivalence classes

If you think of an array as a mapping from its indices to its items, we see that `FIZZ_BUZZ` is a mapping from the numbers 1–100. 
But, apart from the multiples of 3 and 5, all the items are (strings of) themselves. 

We can rewrite `fizz_buzz` in terms of *equivalence classes* determined by `15|`.

```apl
 fizz_buzz←{
     ec←15|⍵  ⍝ equivalence class

     ec∊3 6 9 12:'fizz'
     ec∊5 10:'buzz'
     ec=0:'fizzbuzz'
     ⍕⍵
 }
```

As a general rule in APL, exploiting implicit iteration is more efficient than specifying a loop with a control strucure or the Each operator `¨`.

So a vector solution would use the iteration implicit in Modulus to determine all the equivalence classes.

```apl
      +eq←(4 2 1 1/⍳4)[3 6 9 12 5 10 0⍳15|⍳20] ⍝ equivalence classes
4 4 1 4 2 1 4 4 1 2 4 1 4 4 3 4 4 1 4 2
      'fizz' 'buzz' 'fizzbuzz' '' [eq]
┌┬┬────┬┬────┬────┬┬┬────┬────┬┬────┬┬┬────────┬┬┬────┬┬────┐
│││fizz││buzz│fizz│││fizz│buzz││fizz│││fizzbuzz│││fizz││buzz│
└┴┴────┴┴────┴────┴┴┴────┴────┴┴────┴┴┴────────┴┴┴────┴┴────┘
      +i←⍸eq=4 ⍝ where the numbers go
1 2 4 7 8 11 13 14 16 17 19
      (⍕¨i)@i⊢'fizz' 'buzz' 'fizzbuzz' '' [eq]
┌─┬─┬────┬─┬────┬────┬─┬─┬────┬────┬──┬────┬──┬──┬────────┬──┬──┬────┬──┬────┐
│1│2│fizz│4│buzz│fizz│7│8│fizz│buzz│11│fizz│13│14│fizzbuzz│16│17│fizz│19│buzz│
└─┴─┴────┴─┴────┴────┴─┴─┴────┴────┴──┴────┴──┴──┴────────┴──┴──┴────┴──┴────┘
```
```apl
fizz_buzz_ec_vec←{
    ⍝ Fizz Buzz by equivalence class (vector)
    ⍝ eg fizz_buzz_eq_vec 100
    eq←(4 2 1 1/⍳4)[3 6 9 12 5 10 0⍳15|⍳⍵]  ⍝ equivalence classes
    i←⍸eq=4                                 ⍝ where numbers go
    (⍕¨i)@i⊢'fizz' 'buzz' 'fizzbuzz' ''[eq] ⍝ insert numbers
}
```
```apl
      assert FIZZ_BUZZ ≡ fizz_buzz_ec_vec 100
```




## `None` as a sentinel

In the `CYCLE_OF_15` the Python code used `None` values (non-values?) as ‘sentinels’ to mark where no replacement is to be made.
The APL code used the empty string `''` for the same purpose.

Anything that `or` recognises as False would do.


## Truthiness and logic

Truthiness and falsiness are alien to APL but easily to emulate.

A scalar is truthy unless it is 0 or a space; an array unless it has a non-zero dimension.

```apl
      truthy←{0=≢s←⍴⍵:~⍵∊0 ' ' ⋄ ~0∊s}
      or←{truthy ⍺:⍺ ⋄ ⍵}

      'this' or 'that'
this
      ' ' or 'that'
that
      '' or 'that'
that
      ⍬ or 4
4
      0 or 4
4
      3 or 4
3
```

Grus’ `average` function raises an exception on an empty list.

Lists in APL often are or become empty; we prefer to return a result.
The statistical mean (average) is an iconic APL d-fn or function train.

```apl
      avg←{(+/⍵)÷≢⍵}  ⍝ d-fn
      avg←+/÷≢        ⍝ train
```

But the sum of an empty list (`+/⍬`) is the same as its length (0), giving a ratio of 1.

```apl
      avg ⍬
1
```

If your intuition is that the average of an empty list is zero, then you can fix this.

```apl
      avg←{(×l)×(+/⍵)÷l←≢⍵}
      avg ⍬
0
```

## Dictionary

Where an array can be considered a mapping from its indices to its values, a dictionary is a mapping from its keys to its values.
(You could consider a dictionary in which the keys default to the indices.)

Dictionaries are not first-class objects in APL, but are easy to emulate as pairs of lists.
In this case, we will append a default value to the list of values.

```apl
      +DICT_OF_15←(3 6 9 12 5 10 0)(4 2 1 1/'fizz' 'buzz' 'fizzbuzz' '')
┌───────────────┬─────────────────────────────────────────┐
│3 6 9 12 5 10 0│┌────┬────┬────┬────┬────┬────┬────────┬┐│
│               ││fizz│fizz│fizz│fizz│buzz│buzz│fizzbuzz│││
│               │└────┴────┴────┴────┴────┴────┴────────┴┘│
└───────────────┴─────────────────────────────────────────┘

      get←{k v←⍺ ⋄ v[k⍳⍵]}
      DICT_OF_15 get 15|⍳20
┌┬┬────┬┬────┬────┬┬┬────┬────┬┬────┬┬┬────────┬┬┬────┬┬────┐
│││fizz││buzz│fizz│││fizz│buzz││fizz│││fizzbuzz│││fizz││buzz│
└┴┴────┴┴────┴────┴┴┴────┴────┴┴────┴┴┴────────┴┴┴────┴┴────┘
```

The implicit iteration in Find `⍳` and indexing means we do not need to specify any looping – not even an Each `¨`.
Not so for `or`.

```apl
      (DICT_OF_15∘get 15|⍳20) or¨ ⍳20
┌─┬─┬────┬─┬────┬────┬─┬─┬────┬────┬──┬────┬──┬──┬────────┬──┬──┬────┬──┬────┐
│1│2│fizz│4│buzz│fizz│7│8│fizz│buzz│11│fizz│13│14│fizzbuzz│16│17│fizz│19│buzz│
└─┴─┴────┴─┴────┴────┴─┴─┴────┴────┴──┴────┴──┴──┴────────┴──┴──┴────┴──┴────┘
```

Which looks very similar to the solution using `CYCLE_OF_15`.


## No test

The Fizz Buzz problem asks us to produce answers for the first hundred numbers.

The solutions Grus considers involve testing the numbers according to the Fizz Buzz rule and, Python being a scalar language, applying those tests in a loop.

If the problem were to do Fizz Buzz on a number or numbers given as argument, this would be essential. 
But it’s not. The problem specifies the first hundred natural numbers. We don’t need to test them for anything. 

We know that for the first N natural numbers the Cycle of 15 will simply repeat

```apl
      N←20
      N⍴⌽CYCLE_OF_15
┌┬┬────┬┬────┬────┬┬┬────┬────┬┬────┬┬┬────────┬┬┬────┬┬────┐
│││fizz││buzz│fizz│││fizz│buzz││fizz│││fizzbuzz│││fizz││buzz│
└┴┴────┴┴────┴────┴┴┴────┴────┴┴────┴┴┴────────┴┴┴────┴┴────┘
```

with numbers replacing the empty strings.
For the first N natural numbers, the numbers are the same as the indices of the empty strings they replace.

```apl
 fizz_buzz_notest←{
     x←⍵⍴⌽CYCLE_OF_15  ⍝ extend template
     i←⍸0=≢¨x          ⍝ find empty strings
     (⍕¨i)@i⊢x         ⍝ replace with numbers
 }
```
```apl
      assert FIZZ_BUZZ ≡ fizz_buzz_notest 100
```

This solution generalises nicely:

-   It already works for values other than 100.
-   It’s easy to generate cycles for other pairs – or even groups – of numbers.
-   Apply to a different range of numbers by rotating `x` according to the range start.

