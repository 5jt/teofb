# Ten Essays on Fizz Buzz – in APL

![Ten Essays on Fizz Buzz cover](img/fizzbuzz-cover.png)&nbsp;![APL logo](img/apl-logo.png)

[*Ten Essays on Fizz Buzz*](https://joelgrus.com/2020/06/06/ten-essays-on-fizz-buzz/) is a book by Joel Grus that uses the children’s game Fizz Buzz to explore programming concepts. The examples are in Python. 

This repo contains corresponding examples in [Dyalog APL](https://dyalog.com), with commentary.

![Dyalog logo](img/dyalog-logo.png)

The repo contains files used by [Link](https://dyalog.github.io/link/) to create a workspace, within which are namespaces corresponding to the book chapters; also a namespace `u` containing general utility functions.

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

3.  `]Link # ~/path/to/teofb`
