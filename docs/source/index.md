# Ten Essays on Fizz Buzz ![APL logo](img/apl-logo.png)

![Ten Essays on Fizz Buzz cover](img/fizzbuzz-cover.png)

[*Ten Essays on Fizz Buzz*](https://joelgrus.com/2020/06/06/ten-essays-on-fizz-buzz/) is a book by Joel Grus that uses the children’s game Fizz Buzz to explore programming concepts. The examples are in Python. 

!!! detail "Fizz Buzz"

	Print the numbers from 1 to 100, replacing multiples of 3 with *fizz*, multiples of 5 with *buzz*, and multiples of both with *fizzbuzz*. For example:

	    1
	    2
	    fizz
	    3
	    buzz
	    fizz
	    ..

This repo contains corresponding examples in [Dyalog APL](https://dyalog.com), with commentary.

## Grus’ book

Grus’ book serves new Python coders as an introduction to some basic algorithms, and to how experienced coders think. 

It also serves experienced coders in any language as a meditation on the implications of the variety of solutions that can be devised to even so simple a problem as Fizz Buzz.

FIXME: 
Is the reader assumed to have read Grus’ book?
I.e., is this work an APL version of the original, or a commentary upon it?

## APL

The examples here are in APL, not Python. It serves

-   as a study guide for new APL programmers
-   a comparison between APL and Python

APL expressions are generally more terse than Python. 
This suits some coders. 
Perhaps you are one of them. 

To aid comparison, we shall in each example first emulate the Python fairly closely, then – in some cases – rewrite in a style more natural to APL coders. 

You will get most out of the material by installing Dyalog APL and the code from this repo, experimenting with the examples to see how they work, and trying out variants of your own. 

## Examples code

The folder `APLSource` contains files used by [Link](https://dyalog.github.io/link/) to create a workspace, within which are namespaces corresponding to the book chapters; also a namespace `u` containing general utility functions.

Within each chapter namespace, solutions are named consecutively as upper-case letters. So the first solution is `#.ch1.A`. 

The classic Fizz Buzz problem is to print the numbers from 1 to 100, substituting `fizz` for multiples of 3, `buzz` for multiples of 5, and `fizzbuzz` for multiples of both. To make testing easier, the solutions return a list of strings (text vectors). The function `#.print` will display them in the session as if printed to stdout, e.g.

```apl
      print #.ch1.A
1
2
fizz
4
buzz
fizz
..
```

The classic problem specifies the first hundred natural numbers. The solutions print the first `#.N` numbers. `N` is set to 20 by default; setting it to 100 produces the classic result. 

## Requires

-   Dyalog 19.0 with Link enabled, or Dyalog 18.2 with Tatin and Link 4.0 installed.

## Installation

1.  Clone or download the repo to (say) `~/path/to/teofb`.

2.  Launch Dyalog.

3.  `]Link # ~/path/to/teofb/APLSource`

## Usage

```apl
      )CS ch1
      A
 1  2  fizz  4  buzz  fizz  7  8  fizz  buzz  11  fiz
      z  13  14  fizzbuzz  16  17  fizz  19  buzz 
      E
 1  2  fzz  4  bzz  fzz  7  8  fzz  bzz  11  fzz  13  14
        fzzbzz  16  17  fzz  19  bzz 
      F
 1  2  efervescencia  4  zumbido  efervescencia  7  
      8  efervescencia  zumbido  11  efervescencia  
      13  14  efervescenciazumbido  16  17  efervesc
      encia  19  zumbido 
```

## Refer to

-   [Dyalog Documentation Centre](FIXME)
-   [Link User Guide](FIXME)
-   [*Ten Essays on Fizz Buzz*](https://joelgrus.com/2020/06/06/ten-essays-on-fizz-buzz/)