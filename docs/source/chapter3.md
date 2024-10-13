# The cycle of 15

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

      {((⎕IO+15|⍵)⊃CYCLE_OF_15)or ⍵}¨⍳20
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

## None as a sentinel

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
      DICT_OF_15∘{(⍺ get 15|⍵)or¨⍕¨⍵}⍳20
┌─┬─┬────┬─┬────┬────┬─┬─┬────┬────┬──┬────┬──┬──┬────────┬──┬──┬────┬──┬────┐
│1│2│fizz│4│buzz│fizz│7│8│fizz│buzz│11│fizz│13│14│fizzbuzz│16│17│fizz│19│buzz│
└─┴─┴────┴─┴────┴────┴─┴─┴────┴────┴──┴────┴──┴──┴────────┴──┴──┴────┴──┴────┘
```


