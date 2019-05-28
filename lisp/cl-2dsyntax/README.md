# CL-2DSYNTAX

The `cl-2dsyntax` is an indentation-sensitive reader system. Published in the public domain. Because lispers read the code by indentation and not by parentheses, I've decided to write a reader macro which removes the need for parentheses.

## Download

The *asdf-install*able package: [cl-2dsyntax.tar.gz](https://goheeca.github.com/ql-goheecus/lisp/cl-2dsyntax/cl-2dsyntax-20120820.tgz)

## Usage

Activate the syntax by evaluating `(cl-2dsyntax:enable-syntax)`, the reader macro is hooked on the character `#\!`.

The `#\!` reader macro reads input until the `#\Newline` character followed by a non-`#\Space` character. If the input consists of one line, it's transformed into a list and evaluated.

This form:

```
!+ 1 2 3
```

transforms into:

```
(+ 1 2 3)
```

If the first line is empty, it's skipped (mostly because of aesthetics):

```
!
* 1
  2
  3
```

A multi-line input is transformed the following way:

* Each line followed by indented lines is wrapped into a list together with the rest.
* indented one line comment can be used as a dummy
* A nested invocation of the reader macro is handled in a slightly different way -- for the purpose of determining the extent of this nested invocation, the input is cropped from the left up to the corresponding `#\!` character and then this extracted region is handled.

Some example:

```
!let !!a "Hello "
      !b "world!"
  !concatenate 'string a b
```

or equivalently:

```
!
let !a
        "Hello "
     (b "world!")
  concatenate
    'string a b
```

## Samples

A factorial function:

```
!defun fact !n
  if !zerop n
    1
    !* n !fact !1- n
```

a different version:

```
!defun fact (n)
  if (zerop n)
    1
    * n
      fact
        1-
          n
```

An implementation of the `once-only` macro:

```
!defmacro once-only !(&rest names) &body body
  let !!gensyms !loop for n in names collect !gensym
   `!let !,@!loop for g in gensyms collect `!,g !gensym
      `!let !,,@!loop for g in gensyms for n in names collect ``!,,g ,,n
         ,!let !,@!loop for n in names for g in gensyms collect `!,n ,g
            ,@body
```
