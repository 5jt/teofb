# 100 print statements

!!! important "In your session"

    ```apl
          )CS #.ch1
          assert←#.u.assert      ⍝ local alias
          FIZZ_BUZZ←#.FIZZ_BUZZ  ⍝ local copy
    ```

APL doesn’t really need a `print` function.
It prints result of an evaluation to the session by default. 

But we’ll make a `print` function anyway that works for either a string (character vector) or a list of strings.

## Just print

Now the first solution is almost identical to Python:

```apl
print'1'
print'2'
print'fizz'
print'4'
print'buzz'
print'fizz'
..
```

## Print in a loop

This variation defines an array and loops through it.

```python
FIZZ_BUZZ = [
    '1', '2', 'fizz', '4', 'buzz', 'fizz', '7', '8', 'fizz',
    'buzz', '11', 'fizz', '13', '14', 'fizzbuzz', '16', '17',
    'fizz', '19', 'buzz', 'fizz', '22', '23', 'fizz', 'buzz',
    '26', 'fizz', '28', '29', 'fizzbuzz', '31', '32', 'fizz',
    '34', 'buzz', 'fizz', '37', '38', 'fizz', 'buzz', '41',
    'fizz', '43', '44', 'fizzbuzz', '46', '47', 'fizz', '49',
    'buzz', 'fizz', '52', '53', 'fizz', 'buzz', '56', 'fizz',
    '58', '59', 'fizzbuzz', '61', '62', 'fizz', '64', 'buzz',
    'fizz', '67', '68', 'fizz', 'buzz', '71', 'fizz', '73',
    '74', 'fizzbuzz', '76', '77', 'fizz', '79', 'buzz',
    'fizz', '82', '83', 'fizz', 'buzz', '86', 'fizz', '88',
    '89', 'fizzbuzz', '91', '92', 'fizz', '94', 'buzz',
    'fizz', '97', '98', 'fizz', 'buzz'
]

for fizz_buzz in FIZZ_BUZZ:
    print(fizz_buzz)

# Joel Grus. fizzbuzz (pp. 14-15). Kindle Edition. 
```

APL’s array literals simplify the definition of `FIZZ_BUZZ`.
And we can use Format `⍕` to factor out a lot of fiddly quotes.
(Format on a string is a no-op.)

```apl
FIZZ_BUZZ←⍕¨1 2 'fizz' 4 'buzz' 'fizz' 7 8 'fizz'
    'buzz' 11 'fizz' 13 14 'fizzbuzz' 16 17
    'fizz' 19 'buzz' 'fizz' 22 23 'fizz' 'buzz'
    26 'fizz' 28 29 'fizzbuzz' 31 32 'fizz'
    34 'buzz' 'fizz' 37 38 'fizz' 'buzz' 41
    'fizz' 43 44 'fizzbuzz' 46 47 'fizz' 49
    'buzz' 'fizz' 52 53 'fizz' 'buzz' 56 'fizz'
    58 59 'fizzbuzz' 61 62 'fizz' 64 'buzz'
    'fizz' 67 68 'fizz' 'buzz' 71 'fizz' 73
    74 'fizzbuzz' 76 77 'fizz' 79 'buzz'
    'fizz' 82 83 'fizz' 'buzz' 86 'fizz' 88
    89 'fizzbuzz' 91 92 'fizz' 94 'buzz'
    'fizz' 97 98 'fizz' 'buzz'
```

Now the second solution is also very similar to the Python:

```apl
print¨FIZZ_BUZZ
```

except the Python loop shrinks in APL to the Each operator `¨`.

But even this is too much, for `print` already takes as argument either a string or a list of strings:

```apl
∇ print s                              
:If 1=≡s            ⍝ string         
    ⎕←s                              
:ElseIf 2=≡s        ⍝ list of strings
    ⎕←,[1.5] s                    
:Else                                
    ⎕SIGNAL 11      ⍝ domain error
:EndIf                               
∇
```

and `FIZZ_BUZZ` is a list of strings.

```apl
      print FIZZ_BUZZ
1
2
fizz
4
buzz
fizz
7
..
```

??? quesion "Why traditional?"

    Above `print` is defined as a traditional function (or ‘tradfn’).

    How might you define it as a d-function (i.e. a lambda within curly braces)? Is there a reason to prefer a tradfn for t?

## Upper case

Python strings have an `upper` method. 

```python
for fizz_buzz in FIZZ_BUZZ:
    print(fizz_buzz.upper())

# Joel Grus. fizzbuzz (p. 16). Kindle Edition. 
```

APL system function `⎕C` is atomic: it shifts case at *all* depths of nesting. So the next variant – Fizz Buzz in upper case – also takes advantage of implicit iteration.

```apl
      print 1 ⎕C FIZZ_BUZZ
1       
2       
FIZZ    
4       
BUZZ    
FIZZ    
7       
..
```

## Reversed

Python function `reversed()` takes an array argument.

```python
for fizz_buzz in reversed(FIZZ_BUZZ):
    print(fizz_buzz)

# Joel Grus. fizzbuzz (p. 16). Kindle Edition. 
```

APL function Reverse `⌽` does the same.

```apl
      print ⌽FIZZ_BUZZ
buzz
fizz
98
97
..
```


## Without vowels

The Python Regular Expressions library provides string search-and-replace.

```python
import re

for fizz_buzz in FIZZ_BUZZ:
    print(re.sub("[aeiouAEIOU]", "", fizz_buzz))

# Joel Grus. fizzbuzz (p. 16). Kindle Edition. 
```

If you think string search-and-replace with regular expressions a bit of overkill, you might prefer using APL’s Without `~` function.

The Without function is not atomic, so we loop with Each.

```apl
      +VOWELS←∊1 ¯1 ⎕C¨⊂'aeiou'
AEIOUaeiou
      (20↑FIZZ_BUZZ)~¨⊂VOWELS
┌─┬─┬───┬─┬───┬───┬─┬─┬───┬───┬──┬───┬──┬──┬──────┬──┬──┬───┬──┬───┐
│1│2│fzz│4│bzz│fzz│7│8│fzz│bzz│11│fzz│13│14│fzzbzz│16│17│fzz│19│bzz│
└─┴─┴───┴─┴───┴───┴─┴─┴───┴───┴──┴───┴──┴──┴──────┴──┴──┴───┴──┴───┘
```

!!! question "Each and Enclose"

    Can you explain why, above, the Enclose function `⊂` is applied to `'aeiou'` and then to `VOWELS`?

## In Spanish

```python
for fizz_buzz in FIZZ_BUZZ:
    print(fizz_buzz
          .replace("fizz", "efervescencia")
          .replace("buzz", "zumbido"))

# Joel Grus. fizzbuzz (p. 16). Kindle Edition. 
```

String search-and-replace is again perhaps a bit of overkill, though it neatly handles `fizzbuzz` as well as `fizz` and `buzz`. 

In APL we might simply test for string matches.

```apl
       replace←{⍺[2]@(⍸⍵∊⍺)⊢⍵}
      'brown' 'red' replace 'quick' 'brown' 'fox'
┌─────┬───┬───┐
│quick│red│fox│
└─────┴───┴───┘
```

!!! detail "Overcomputing"

    Above, the expression `⍵∊⍺` flags words in the left argument that match either *brown* or *red*, when actually we only need to locate instances of *brown*. 
    Such unnecessary calculation is sometimes called “overcomputing”.

    Explain why here the overcomputing is harmless. 
    Rewrite the expression so it finds only matches for *brown* and not for *red*.

    Why might the overcomputing be preferable?

Where Python chains the `replace` invocations, APL can use Reduce `/` to reduce a list of left arguments.

```apl
      ⊃replace/('quick' 'fast')('brown' 'red')('quick' 'brown' 'fox')
┌────┬───┬───┐
│fast│red│fox│
└────┴───┴───┘

      pairs←('fizz' 'efervescencia')('buzz' 'zumbido')
      ⊃replace/pairs,(⊃,¨/pairs)(20↑FIZZ_BUZZ)
┌─┬─┬─────────────┬─┬───────┬─────────────┬─┬─┬─────────────┬───────┬
│1│2│efervescencia│4│zumbido│efervescencia│7│8│efervescencia│zumbido│
└─┴─┴─────────────┴─┴───────┴─────────────┴─┴─┴─────────────┴───────┴
      ──┬─────────────┬──┬──┬────────────────────┬──┬──┬─────────────
      11│efervescencia│13│14│efervescenciazumbido│16│17│efervescencia
      ──┴─────────────┴──┴──┴────────────────────┴──┴──┴─────────────
      ┬──┬───────┐
      │19│zumbido│
      ┴──┴───────┘
```

??? tip "Peeking into the Reduce"

    To see the intermediate results as Reduce iterates itargument function,
    try passing the function’s result through Evaluated Output `⎕`:

    ```apl
    replace←{⎕←⍺[2]@(⍸⍵∊⍺)⊢⍵}
    ```


## Testability and Fizz Buzz

Python `assert` statements test an assertion and raise an `AssertionError` exception if it is false.

```python
assert "iz" in "FizzBuzz"
```

We can easily emulate this in APL.

```apl
      assert←{'ASSERTION FAILED' ⎕SIGNAL 666/⍨~⍵}

      assert 4=2+2
      assert 5=2+2
ASSERTION FAILED
      assert 5=2+2
      ∧
```

Grus defines two functions for testing. 
One tests the output against `FIZZ_BUZZ`.
The other takes the name of a scalar solution function – in this context, a function that for argument `N` returns `fizz`, `buzz`, `fizzbuzz`, or `N` as a string – and sees whether iterating it over 1–100 yields `FIZZ_BUZZ`. 

Testing the output against `FIZZ_BUZZ` is easy enough in APL

```apl
chk_out←FIZZ_BUZZ∘≡
```

but uninformative! We want incorrect items identified.

We shall start by following the Python example: if any errors are found, `check_output` raises an exception, and the problems are listed in `errors`.

Here is the Python:

```python
def check_output(output: List[str]) -> None:
    assert len(output) == 100, "output should have length 100"
    # Collect all the errors in a list
    # The i+1 reflects that output[0] is the output for 1,
    # output[1] is the output for 2, and so on
    errors = [
        f"({i+1}) predicted: {output[i]}, actual: {FIZZ_BUZZ[i]}"
        for i in range(100)
        if output[i] != FIZZ_BUZZ[i]
    ]
    # And assert that the list of errors is empty
    assert not errors, f"{errors}"
# Joel Grus. fizzbuzz (p. 19). Kindle Edition.
```

Our APL version closely follows the Python, then comments out those lines and follows them with something more succinct.

```apl
check_output output;i;errors
   assert =/≢¨output FIZZ_BUZZ

⍝  errors←''
⍝ :For i :In ≢output
⍝     :If output[i]≢FIZZ_BUZZ[i]
⍝         errors,←⊂(⍕i),' predicted: ',(i⊃FIZZ_BUZZ),', actual: ',i⊃output
⍝     :EndIf
⍝ :EndFor

⍝ assert 0=≢errors

  i←⍸output≢¨FIZZ_BUZZ
  errors←(⍕¨i),¨(⊂' predicted: '),¨FIZZ_BUZZ[i],¨(⊂', actual: '),¨output[i]
  assert 0=≢i
```

To test a *function* in Python:

```python
def check_function(fizz_buzz: Callable[[int], st
    """
    The type annotation says that `fizz_buzz` ne
    a function that takes a single argument (whi
    and returns a `str`.
    """
    output = [fizz_buzz(i) for i in range(1, 101
    check_output(output)
# Joel Grus. fizzbuzz (p. 20). Kindle Edition.
```

In APL:

```apl
check_function fizzbuzz
⍝ e.g. check_function '#.ch2.A' 
 check_output⍎fizzbuzz,'¨⍳100'
```

## Testability beyond Fizz Buzz

Checking two words are anagrams by comparing the sorted strings:

```python
def anagrams(s1: str, s2: str) -> bool:
    return sorted(s1) == sorted(s2)

assert anagrams("dale", "lead")
assert anagrams("time", "mite")
assert not anagrams("made", "deem")
assert not anagrams("time", "miter")
# Joel Grus. fizzbuzz (p. 20). Kindle Edition. 
```

In APL:

```apl
asc←{⍵[⍋⍵]}           ⍝ sorted ascending
anagrams←{≡/asc¨⍺ ⍵}

assert 'dale' anagrams 'lead'
assert 'time' anagrams 'mite'
assert ~ 'made' anagrams 'deem'
assert ~ 'time' anagrams 'miter'
```

## Generating the `print` statements

Returning to the very first solution (the 100 `print` statements) they can be generated in Python with `make_print-statement`, the function tested, and used as follows:

```python
def make_print_statement(fizz_buzz: str) -> str:
    return f"print('{fizz_buzz}')"

assert make_print_statement("10") == "print('10')"
assert make_print_statement("fizz") == "print('fizz')"
assert make_print_statement("buzz") == "print('buzz')"
assert make_print_statement("fizzbuzz") == "print('fizzbuzz')"

for fizz_buzz in FIZZ_BUZZ:
    print(make_print_statement(fizz_buzz))

# Joel Grus. fizzbuzz (p. 21). Kindle Edition. 
```

Emulating that in APL:

```apl
      make_print_statement←{'⎕←''',⍵,''''}
      assert '⎕←''10''' ≡ make_print_statement '10'
      assert '⎕←''fizz''' ≡ make_print_statement 'fizz'
      assert '⎕←''buzz''' ≡ make_print_statement 'buzz'
      assert '⎕←''fizzbuzz''' ≡ make_print_statement 'fizzbuzz'
```

This might all feel a bit pointless, where, given `FIZZ_BUZZ`, the entire APL solution is simply

```apl
      ,[1.5] FIZZ_BUZZ
1
2
fizz
..
```

But we can draw 

!!! info "A tentative conclusion"

    What is fairly easy in Python is often trivial in APL. 

That’s something, anyway.


