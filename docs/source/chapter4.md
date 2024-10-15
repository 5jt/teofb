# Euclid’s solution

Start by converting Grus’ enigmatic solution

```python
def fizz_buzz(n: int) -> str:
    hi, lo = max(n, 15), min(n, 15)

    while hi % lo > 0:
        hi, lo = lo, hi % lo

    return {1: str(n), 3: "fizz", 5: "buzz", 15: "fizzbuzz"}[lo]

# Joel Grus. fizzbuzz (p. 50). Kindle Edition. 
```

into APL.

```apl
fizz_buzz←{
    ⍝ Euclid's solution
     lo hi←{0≥nxt←|/⍵:⍵ ⋄ ∇ nxt,⊃⍵}(15⌊⍵)(15⌈⍵)
     lo=1:⍕⍵
     (3 5 15⍳lo)⊃'fizz' 'buzz' 'fizzbuzz'
}

      fizz_buzz¨⍳20
┌─┬─┬────┬─┬────┬────┬─┬─┬────┬────┬──┬────┬──┬──┬────────┬──┬──┬────┬──┬────┐
│1│2│fizz│4│buzz│fizz│7│8│fizz│buzz│11│fizz│13│14│fizzbuzz│16│17│fizz│19│buzz│
└─┴─┴────┴─┴────┴────┴─┴─┴────┴────┴──┴────┴──┴──┴────────┴──┴──┴────┴──┴────┘
```

The rest of the chapter explores prime factorisation. 
It also introduces timing and profiling functions.

Grus begins with a simple implementation of 

> n is prime if it's at least 2 and if it’s not divisible by any smaller number (other than 1)

```python
def is_prime(n: int) -> bool:

    return (
        n >= 2 and 
        all(n % d > 0 for d in range(2, n))
    )

assert all(is_prime(n) for n in [2, 3, 7, 11, 83, 89, 97])
assert not any(is_prime(n) for n in [4, 50, 91])

# Joel Grus. fizzbuzz (p. 51). Kindle Edition. 
```

In APL:

```apl
      is_prime←{⍵<2:0 ⋄ ∧/×⍵|⍨1+⍳⍵-2}

      assert ∧/is_prime¨2 3 7 11 83 89 97
      assert ~∨/is_prime¨0 1 4 50 91
```

This is infamously inefficient. 
There is no need to check for factors of N larger than its square root.

```apl
      is_prime2←{⍵<2:0 ⋄ ∧/×⍵|⍨(1+⍵=2)+⍳⌊⍵*÷2}

      assert ∧/is_prime2¨2 3 7 11 83 89 97
      assert ~∨/is_prime2¨0 1 4 50 91
```

The crudest way to find all the primes up to N is to test them.

```apl
      primes_up_to←{c/⍨is_prime2¨c←1↓⍳⍵}
      ]RunTime 'primes_up_to 10000'

* Benchmarking "primes_up_to 10000"
             (ms) 
 CPU (avg):    27 
 Elapsed:      26 
```

Grus observes that once a prime has been identified, its multiples are no longer candidates, and can be eliminated.
(This is the Sieve of Erastosthenes, a classic algorithm for finding primes.)

```apl
∇ primes←primes_up_to n;c;p
  primes←0⍴c←1↓⍳n      ⍝ candidates
 :While ×≢c
     primes,←p←⊃c      ⍝ next prime
     c←c/⍨×p|c         ⍝ remove multiples
 :EndWhile
∇

      ]RunTime 'primes_up_to 10000'

* Benchmarking "primes_up_to 10000"
             (ms) 
 CPU (avg):     6 
 Elapsed:       6 
```

Performance has improved 4×. :smile:

The same algorithm in Python actually runs *slower* than the first.

```python
def primes_up_to(n: int) -> List[int]:
    candidates = range(2, n + 1)
    primes = []

    while candidates:
        # The smallest remaining number must be prime, 
        # because it wasn't divisible by any smaller prime.
        p = candidates[0]

        primes.append(p)

        # Remove further multiples of p as not-prime
        candidates = [n for n in candidates if n % p > 0]

    return primes

# Joel Grus. fizzbuzz (pp. 53-54). Kindle Edition. 
```

Grus’ analysis discovers the function is spending almost all of its time executing `primes.append(p)`. 
Python does have array methods, but perhaps they’ve been ‘bolted on’ to the core language? In contrast, APL is all about arrays.


## Performance optimisation

Grus uses the `cProfile` tool to investigate where his function spends its time.
The (edited) output shows where the time goes.

```
ncalls  tottime  function
1       0.078    <ipython-input-18-7da95fe8b6cd>:1(primes_up_to)
9592    2.138    <ipython-input-18-7da95fe8b6cd>:12(<listcomp>)
1       0.000    <string>:1(<module>)
1       0.000    {built-in method builtins.exec}
9592    0.000    {method 'append' of 'list' objects}
1       0.000    {method 'disable' of '_lsprof.Profiler' objects}

Joel Grus. fizzbuzz (p. 55). Kindle Edition. 
```

Dyalog APL’s `]Profile` user command will do a similar job for us.

```apl
      ]Profile summary -lines -expr='p←primes_up_to 10000'
 Total time: 7.0 msec                     
                                          
  Element             msec      %  Calls  
  #.primes_up_to[4]    3.2   45.8   1229  
  #.primes_up_to[3]    1.0   14.5   1229  
  #.primes_up_to[2]    0.9   13.1   1230  
  #.primes_up_to[5]    0.9   12.7   1229  
  #.primes_up_to[1]    0.0    0.2      1  
  #.primes_up_to[0]    0.0    0.0      1  
```

Our function spends half its time finding and removing multiples of the found primes, looping round on:

```apl
c←c/⍨×p|c         ⍝ remove multiples
```

Two operations draw my attention. The list `c` of candidates gets redefined in each loop. And there is the calculation of `p|c`. 

Eratosthenes didn’t use Modulus in his sieve. To remove multiples of (say) 7 from the candidates, he simply struck out every seventh candidate. No Modulus, just counting. Can we do something similar?

```apl
∇   primes←primes_up_to n;c;lmt;p
	primes←,2
	c←0@1⊢n⍴1 0        ⍝ candidates: odd numbers 2 < c ≤ n
	lmt←n*÷2
   :Repeat
		primes,←p←c⍳1  ⍝ next prime
		c∧←n⍴~(-p)↑1   ⍝ remove its multiples
   :Until p≥lmt        ⍝ candidates have no factors <√n
	primes,←⍸c
∇
```

Now sieving for primes below 10,000 is too fast to count in milliseconds

	* Benchmarking "p←primes_up_to 10000"
	             (ms) 
	 CPU (avg):     0 
	 Elapsed:       0 

but profiling is still useful.

	      ]Profile summary -lines -expr='p←primes_up_to 10000'
	 Total time: 0.1 msec                     
	                                          
	  Element             msec      %  Calls  
	  #.primes_up_to[6]    0.0   43.9     25  
	  #.primes_up_to[2]    0.0   16.8      1  
	  #.primes_up_to[5]    0.0   15.9     25  
	  <unnamed dfn>[0]     0.0    8.4      1  
	  #.primes_up_to[7]    0.0    8.4     25  
	  #.primes_up_to[8]    0.0    5.6      1  
	  #.primes_up_to[3]    0.0    3.7      1  
	  #.primes_up_to[1]    0.0    1.9      1  
	  #.primes_up_to[4]    0.0    0.9      1  
	  #.primes_up_to[0]    0.0    0.0      1  

Run time has dropped 70×! Some of this may have come from replacing the `p|c` with `c∨←`.
But probably more important is that the calls have dropped from 1229 to 25.
Stopping when `p>lmt` means we stoppd testing when we had found the 25 primes below 100, i.e. the square root of `n`.

For comparison, here is the revised Python code:

```python
def primes_up_to(n: int) -> List[int]:
    #             0      1         2, ..., n
    candidates = [False, False] + [True] * (n - 1)

    for p in range(2, int(math.sqrt(n))):
        # if we haven't already eliminated p as a prime
        if candidates[p]:
            # eliminate all multiples of p, starting at p ** 2
            for m in range(p * p, n + 1, p):
                candidates[m] = False
    
    # return the indices that weren't eliminated
    return [n for n, prime in enumerate(candidates) if prime]

# Joel Grus. fizzbuzz (p. 56). Kindle Edition. 
```

and its performance:

	In [28]: %timeit primes = primes_up_to(10000)
	467 μs ± 873 ns per loop (mean ± std. dev. of 7 runs, 1,000 loops each)



## Factorisation

```apl
∇ factors←factorize n;primes;p
  primes←primes_up_to n
  factors←⍬

 :For p :In primes
     :While 0=p|n
         factors,←p  ⍝ got one
         n←⌊n÷p      ⍝ residue to factorise
     :EndWhile
     :If n=1         ⍝ no more factors to find
         :Leave
     :EndIf
 :EndFor
∇
```
```apl
      assert 3 5 ≡ factorize 15
      assert 2 3 5 5 ≡ factorize 150
      assert (,7) ≡ factorize 7
```

## What factorisation has to do with Fizz Buzz

Having the prime factors enables a very legible version of `fizz_buzz`.

```apl
fizz_buzz_factorization←{
    by3 by5←3 5∊factorize ⍵
    by3∧by5:'fizzbuzz'
    by3:'fizz'
    by5:'buzz'
    ⍕⍵
}
```

Or, replacing the tests with vector operations

```apl
fizz_buzz_factorization←{
    (1+2⊥3 5∊factorize ⍵)⊃(⊂⍕⍵),'buzz' 'fizz' 'fizzbuzz'
}
```
```apl
      fizz_buzz_factorization¨⍳20
┌─┬─┬────┬─┬────┬────┬─┬─┬────┬────┬──┬────┬──┬──┬────────┬──┬──┬────┬──┬────┐
│1│2│fizz│4│buzz│fizz│7│8│fizz│buzz│11│fizz│13│14│fizzbuzz│16│17│fizz│19│buzz│
└─┴─┴────┴─┴────┴────┴─┴─┴────┴────┴──┴────┴──┴──┴────────┴──┴──┴────┴──┴────┘
```

Pause for a moment and consider a version of `fizz_buzz_factorization` that takes a vector argument. 
Leave aside possible improvements in factorising the list, and accept that we’ll iterate `factorize` over each number, all the other primitives iterate implicitly. Seems a shame – and less efficient? –to use Each to iterate them.

```apl
      {2⊥⍉↑3 5∘∊¨factorize¨⍵}⍳20
0 0 2 0 1 2 0 0 2 1 0 2 0 0 3 0 0 2 0 1
```

Starting with `⍕¨⍵` we can insert where the above list is positive.

```apl
      {i←2⊥⍉↑3 5∘∊¨factorize¨⍵ ⋄ (⊂'***')@(⍸×i)⍕¨⍵}⍳20
┌─┬─┬───┬─┬───┬───┬─┬─┬───┬───┬──┬───┬──┬──┬───┬──┬──┬───┬──┬───┐
│1│2│***│4│***│***│7│8│***│***│11│***│13│14│***│16│17│***│19│***│
└─┴─┴───┴─┴───┴───┴─┴─┴───┴───┴──┴───┴──┴──┴───┴──┴──┴───┴──┴───┘
```

And use the positive items to pick the inserts.

```apl
fizz_buzz_factorization_vec←{
    i←2⊥⍉↑3 5∘∊¨factorize¨⍵
    'buzz' 'fizz' 'fizzbuzz'[i~0]@(⍸×i)⍕¨⍵
}
```
```apl
      assert FIZZ_BUZZ ≡ fizz_buzz_factorization_vec ⍳100
```

## Greatest Common Divisors and Least Common Multiples



