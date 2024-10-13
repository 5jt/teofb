# Ten Essays on Fizz Buzz – ![APL logo](img/apl-logo.png)

![Ten Essays on Fizz Buzz cover](img/fizzbuzz-cover.png)

[*Ten Essays on Fizz Buzz*](https://joelgrus.com/2020/06/06/ten-essays-on-fizz-buzz/) is a book by Joel Grus that uses the children’s game Fizz Buzz to explore programming concepts. The examples are in Python. 

This repo contains corresponding examples in [Dyalog APL](https://dyalog.com), with commentary.

![Dyalog logo](img/dyalog-logo.png)

The repo contains files used by [Link](https://dyalog.github.io/link/) to create a workspace, within which are namespaces corresponding to the book chapters; also a namespace `u` containing general utility functions.

Within each chapter namespace, solutions are named consecutively as upper-case letters. So the first solution is `#.ch1.A`. 

The classic Fizz Buzz problem is to print the numbers from 1 to 100, substituting `fizz` for multiples of 3, `buzz` for multiples of 5, and `fizzbuzz` for multiples of both. To make testing easier, the solutions return a list of strings (text vectors). The function `print` in Chapter 1 will display them in the session as if printed to stdout, e.g.

```apl
      )CS ch1
#.ch1
      print A
1
2
fizz
4
buzz
fizz
..
```


## Requires

-   Dyalog 19.0 with Link enabled, or Dyalog 18.2 with Tatin and Link 4.0 installed.

## Installation

1.  Clone or download the repo to (say) `~/path/to/teofb`.

2.  Launch Dyalog.

3.  `]Link # ~/path/to/teofb`

## Usage

```apl
      )CS ch1
      A
 1  2  fizz  4  buzz  fizz  7  8  fizz  buzz  11  fiz
      z  13  14  fizzbuzz  16  17  fizz  19  buzz 
```

## To do

- [x] draft ch1 code in DWS
- [x] use Link: convert code to text
- [ ] include Tester2
- [ ] include ADoc
- [x] MkDocs instance in `docs/`
- [x] configure Material for MkDocs
- [ ] CI/CD through GH Pages
- [x] chapter 1
- [x] chapter 2
- [x] chapter 3
- [ ] chapter 4
- [ ] chapter 5
- [ ] chapter 6
- [ ] chapter 7
- [ ] chapter 8
- [ ] chapter 9
- [ ] chapter 10
- [ ] [embed font in MkDocs](https://squidfunk.github.io/mkdocs-material/setup/changing-the-fonts/): APL386 Unicode, Hack
- [ ] Install TeX template for Tufte adapted to Vector Sigil page size
- [ ] Convert MDs to TeX
- [ ] Shell script to compile PDF
