# if / elif/ elif / else

!!! important "In your session"

    ```apl
          )CS #.ch2
          assert←assert      ⍝ local alias
          FIZZ_BUZZ←FIZZ_BUZZ  ⍝ local copy
    ```

It is easy to translate the “prototypical correct solution”

```python
def fizz_buzz(n: int) -> str:
    if n % 15 == 0:
        return 'fizzbuzz'
    elif n % 5 == 0:
        return 'buzz'
    elif n % 3 == 0:
        return 'fizz'
    else:
        return str(n)

# Joel Grus. fizzbuzz (p. 23). Kindle Edition. 
```

into APL that looks quite similar:

```apl
 ∇ z←fizz_buzz_trad x
   ⍝ close translation of the canonical Python solution
   :If 0=15|x
       z←'fizzbuzz'
   :ElseIf 0=3|x
       z←'fizz'
   :ElseIf 0=5|x
       z←'buzz'
   :Else
       z←,⍕x
   :EndIf
 ∇
```

but most people who like APL prefer to dispense with the ‘ceremony’ of control structures. 

```apl
fizz_buzz_commented←{
  ⍝ * The If/ElseIf/Else in d-fn form
    0=15|⍵:'fizzbuzz'  ⍝ if
    0=3|⍵:'fizz'       ⍝ elseif
    0=5|⍵:'buzz'       ⍝ elseif
    ⍕⍵                 ⍝ else
}
```

which need be no more than:

```apl
fizz_buzz←{
	0=15|⍵:'fizzbuzz'
	0= 3|⍵:'fizz'
	0= 5|⍵:'buzz'
	⍕⍵
}
```

## Mind the order

Grus notes that testing 15 first is crucial. Otherwise its multiples map to either *fizz* or *buzz*. 

An APL version of his incorrect version:

```apl
incorrect_fizz_buzz←{
    0= 3|⍵:'fizz'
    0= 5|⍵:'buzz'
    0=15|⍵:'fizzbuzz'
    ⍕⍵
}
```
```apl
      assert 'fizz' ≡ incorrect_fizz_buzz 30
```

He offers an order-free if/else solution, which we can follow as a d-fn:

```apl
order_free_fizz_buzz←{
    1 1≡×3 5|⍵: ⍕⍵
    0 1≡×3 15|⍵:'fizz'
    0 1≡×5 15|⍵:'buzz'
    0=15|⍵:'fizzbuzz'
    'Impossible to get here (I hope)'⎕SIGNAL 11
}
```


## Divisibility and modulus

Grus notes that the solution relies heavily on the Modulus function `|` and illustrates for anyone unfamiliar with it:

```python
for i in range(1, 10):
    print(f"The remainder when dividing {i} by 3 is {i % 3}.")

# The remainder when dividing 1 by 3 is 1.
# The remainder when dividing 2 by 3 is 2.
# The remainder when dividing 3 by 3 is 0.
# The remainder when dividing 4 by 3 is 1.
# The remainder when dividing 5 by 3 is 2.
# The remainder when dividing 6 by 3 is 0.
# The remainder when dividing 7 by 3 is 1.
# The remainder when dividing 8 by 3 is 2.
# The remainder when dividing 9 by 3 is 0.

# Joel Grus. fizzbuzz (p. 25). Kindle Edition. 
```

The same illustration in APL is not only shorter, it arguably makes the point more clearly that the result cycles through 0, 1 and 2.

```apl
      3|⍳10
1 2 0 1 2 0 1 2 0 1
```

He observes that even if you do not know Modulus, there are other ways to test for divisibility by N, for example by repeatedly subtracting N until the result is less than N and seeing if it is also 0.

```apl
      +divisible_pairs←(0 3)(3 3)(6 3)(9 3)(0 5)(5 5)(10 5)(100 5)(30 15)(150 15)
┌───┬───┬───┬───┬───┬───┬────┬─────┬─────┬──────┐
│0 3│3 3│6 3│9 3│0 5│5 5│10 5│100 5│30 15│150 15│
└───┴───┴───┴───┴───┴───┴────┴─────┴─────┴──────┘
      +not_divisible_pairs←(1 3)(5 3)(10 3)(1 5)(2 5)(3 5)(4 5)(14 15)(16 15)
┌───┬───┬────┬───┬───┬───┬───┬─────┬─────┐
│1 3│5 3│10 3│1 5│2 5│3 5│4 5│14 15│16 15│
└───┴───┴────┴───┴───┴───┴───┴─────┴─────┘

      is_divisible_subtract←{</⍵:0=⊃⍵ ⋄ ∇⍵-⌽0 1×⍵}

      assert ∧/is_divisible_subtract¨divisible_pairs
      assert ~∨/is_divisible_subtract¨not_divisible_pairs
```

The definition above of `is_divisible_subtract` seems a *little* terse. Did we miss anything? Let’s compare to the Python function.

```python
def is_divisible_subtract(n: int, d: int) -> bool:
    assert d > 0 and n >= 0

    # Keep subtracting until n < d.
    while n >= d:
        n -= d

    # If we reached zero, n was divisible by d.
    return n == 0

# Joel Grus. fizzbuzz (p. 26). Kindle Edition. 
```

The Python function specifies the types of its result and arguments. 
It checks the initial arguments are in range. 
And it includes comments. 
It also uses a While-loop, where the APL function recurses.

But we’re good. 

!!! question "FIXME: APL alternative to Python‘s hypothesis package?"


## “We’re adders, we need logs to multiply”

Without logarithms, `largest_power(d, n)` returns the largest power of `d` less than `n`.

```python
def largest_power(d: int, n: int) -> int:
    """
    Finds the largest d ** k with d ** k <= n.
    """
    assert 1 < d <= n, "this only works if 1 < d <= n"

    power = d

    # Keep multiplying by d until we get something too big.
    while power * d <= n:
        power *= d
    
    return power
```

And, as always, we’ll write a few assert-tests: 

```python
assert largest_power(3, 8) == 3
assert largest_power(3, 9) == 9
assert largest_power(3, 26) == 9
assert largest_power(3, 27) == 27

# Joel Grus. fizzbuzz (pp. 30-31). Kindle Edition. 
```

Once again, APL allows us to emulate the Python closely.

```apl
∇ power←d largest_power_trad n
  assert(1<d)∧d≤n
  power←d
 :While n>d×power
     power×←d
 :EndWhile
∇
```

Or we can use a tail-end recursion.

```apl
largest_power_mult←{
  ⍝  largest power of ⍺ that is ≤⍵
   ⍺ ⍵{d n←⍺ ⋄ n<r←d×⍵:r ⋄ ⍺∇r}1
}
```
```apl
      assert 3 = 3 largest_power_mult 8
      assert 9 = 3 largest_power_mult 9
      assert 9 = 3 largest_power_mult 26
      assert 27 = 3 largest_power_mult 27
```

Turning from multiplication to logarithms takes us into APL’s origins in mathematics

```apl
     largest_power←{⍺*⌊⍺⍟⍵}  ⍝ largest power of ⍺ that is ≤⍵
```

and on to the fast version of `is_divisible_subtract`.
We can follow the control structures of the Python, or use APL’s fast tail recursion.

```apl
is_divisible_subtract_fast←{
    ⍝ whether ⍺ is divisible by ⍵
     ⍵=1:1
     0=⍺{⍺<⍵:⍺ ⋄ (⍺ largest_power_subtract_fast ⍵) ∇ ⍵}⍵
}
```
```apl
      assert ∧/is_divisible_subtract_fast/↑divisible_pairs
```

!!! question "Exercise: rewrite the d-fn as a tradfn with control structures"

    Which version do you prefer?


## Some other checks

> Another way to check that `n` is divisible by `d` is to perform the (float) division and check that the result is an integer. For example, 5 is not divisible by 2, and 5/2 is 2.5. Whereas 6 is divisible by 2, and 6/2 is 3.
> 
> Joel Grus. fizzbuzz (p. 33). Kindle Edition. 

```apl
      is_int←{x=⌊x←⍺÷⍵}
      is_divisible_try_dividing←{is_int ⍺÷⍵}
```

We could substitute for `is_int` a function train that compares the results of applying Floor `⌊` and Identity `⊢`.

```apl
      is_int ← ⊢=⌊
      is_int 2 2.3
1 0
```

Or we can just write `is_divisible_try_dividing` as a function train.

```apl
      is_divisible_try_dividing ← (⊢=⌊)÷
      assert ∧/is_divisible_try_dividing/ ↑divisible_pairs
      assert ∧/~is_divisible_try_dividing/ ↑not_divisible_pairs
```

This is subject to the same precision limits as Python.

```apl
      36028797018964073 is_divisible_try_dividing 2
1
```

==FIXME==
No simple equivalent in APL?


## Some well-known tricks

> A number is divisible by 5 if and only if its last digit is either 5 or 0.

We could use Python’s trick of casting a number to a string.

```apl
      is_divisible_by_5_fmt←{(⊃⌽⍕⍵)∊'05'}
      is_divisible_by_5_fmt¨3 15 29 5
0 1 0 1
```

Or we could use Encode `⊤` with a number base of 10.

```apl
      10⊤3 15 29 1000
3 5 9 5
      is_divisible_by_5←{(10⊤⍵)∊0 5}
      is_divisible_by_5 3 15 29 1000
0 1 0 1
```

Further

> a number is divisible by 3 if and only if the sum of its digits is divisible by 3

We don’t need Python’s trick of casting digits to characters and back. 
Encode `⊤` will partition a decimal number.

```apl
      10 10 10 10 10 10⊤314159
3 1 4 1 5 9
      +/10 10 10 10 10 10⊤314159
23
      sum_of_digits←{+/⍵⊤⍨10⍴⍨⌈10⍟⍵}
      sum_of_digits 314159
23
```

Grus’ function 

```python
def is_divisible_by_3_wrong(n: int) -> bool:
    return is_divisible_by_3_wrong(sum_of_digits(n))

# Joel Grus. fizzbuzz (p. 36). Kindle Edition. 
```

illustrates recursion but fails to terminate. 
Our APL version follows the logic of his corrected version.

```python
def is_divisible_by_3(n: int) -> bool:
    while n >= 10:
        n = sum_of_digits(n)

    # Now n is a single-digit number, 
    # and we know what to do for those:
    return n in [0, 3, 6, 9]

# Joel Grus. fizzbuzz (p. 37). Kindle Edition. 
```

```apl
is_divisible_by_3←{
    ⍵≥10:∇ sum_of_digits ⍵
    ⍵∊0 3 6 9
}
```

Finally, the modulus-free version.

```python
def fizz_buzz(n: int) -> str:
    by3 = is_divisible_by_3(n)
    by5 = is_divisible_by_5(n)

    if by3 and by5:
        return "fizzbuzz"
    elif by5:
        return "buzz"
    elif by3:
        return "fizz"
    else:
        return str(n)

# Joel Grus. fizzbuzz (pp. 37-38). Kindle Edition. 
```

Or, in APL

```apl
fizz_buzz←{
  by3←is_divisible_by_3 ⍵
  by5←is_divisible_by_5 ⍵

  by3∧by5:'fizzbuzz'
  by3:'fizz'
  by5:'buzz'
  ⍕⍵
}
```

## Eliminating `elif`

Grus shows how to avoid the four-branched `if` statement with a dictionary lookup.

```python
def fizz_buzz(n: int) -> str:
    by3 = is_divisible_by_3(n)
    by5 = is_divisible_by_5(n)

    return {
        (True, True): 'fizzbuzz',
        (True, False): 'fizz',
        (False, True): 'buzz',
        (False, False): str(n)
    }[by3, by5]

# Joel Grus. fizzbuzz (p. 38). Kindle Edition. 
```

We can do something similar.
Generate all the true/false combinations.

```apl
      1 0∘.,1 0
┌───┬───┐
│1 1│1 0│
├───┼───┤
│0 1│0 0│
└───┴───┘

      ,∘.,⍨1 0  ⍝ and ravel them
┌───┬───┬───┬───┐
│1 1│1 0│0 1│0 0│
└───┴───┴───┴───┘

fizz_buzz←{
    by3←is_divisible_by_3 ⍵
    by5←is_divisible_by_5 ⍵

    ((,∘.,⍨1 0)⍳⊂by3 by5)⊃'fizzbuzz' 'fizz' 'buzz',⊂⍕⍵
}
```

Or, returning, as Grus does, to Modulus to test divisibility:

```apl
      fb←{(0⍳⍨15 5 3|⍵)⊃'fizzbuzz' 'buzz' 'fizz',⊂⍕⍵}  ⍝ Fizz Buzz
      assert FIZZ_BUZZ ≡ fb¨⍳100
```
