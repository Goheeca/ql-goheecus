# CL-RTTEMPLATE

The `cl-rttemplate` is a read-time quotation system. Published in the public domain. It's a template system similar to the quasiquote & unquote facility, however, it operates during the read-time.

I've created this for a need to macroize the [commonqt](http://common-lisp.net/project/commonqt/) reader macros.

## Download

The *asdf-install*able package: [cl-rttemplate.tar.gz](http://ql-goheecus.goheeca.ga/lisp/cl-rttemplate/cl-rttemplate-20120211.tgz)

## API

* The `#"` reader macro reads from the input until the sequence `"#` is read. The character `#\"` toggles quoting and the character `#\\` is used for escaping.
* The `#@` reader macro is a helper which works like the splicing unquote `,@`. Internally, it uses multiple values so if you have an unquoted form which returns multiple values, you can use `#n@` to extract a single value, `n` denotes which value.
* The current readtable `cl-rttemplate:*rt*` is active during building the string representation which consists of writing of unquoted forms into strings.
* The `enable-syntax` function enables the reader macros and copies the current `*readtable*` into the `cl-rttemplate:*rt*`.

## Usage

Enable the syntax by `(cl-rttemplate:enable-syntax)` or `(rtt:enable-syntax)`.

Modify the readtable at your will; for example: `(setf (readtable-case rtt:*rt*) :preserve)`.

## Samples

Simple symbol building:

```
#"'begin"(expt 3 8)"end"#
```

You can use the lexical bindings:

```
(let ((lexical-value '|Hello|))
  #"'(a b " lexical-value " \"" lexical-value " world!\")"#)
```

and now change the `readtable-case`:

```
(setf (readtable-case rtt:*rt*) :preserve)
(let ((lexical-value '|Hello|))
  #"'(a b " lexical-value " \"" lexical-value " world!\")"#)
```

A splicing example:

```
#"'(a " #@'(a b c) " e)"#
```

and another one:

```
#"(* " #0@(floor 100 7) " " #1@(floor 25 13) ")"#
```
