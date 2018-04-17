---
date: "2014-06-12T23:45:00Z"
tags:
- linux
title: Two interesting linux command.
---

Get two interesting linux command from this [article](http://jianshu.io/p/5ca890e5bdbf).

1. strings

		NAME
		       strings - find the printable strings in a object, or other binary, file

		SYNOPSIS
		       strings [ - ] [ -a ] [ -o ] [ -t format ] [ -number ]  [  -n  number  ]
		       [--] [file ...]

		DESCRIPTION
		       Strings  looks  for  ASCII  strings in a binary file or standard input.
		       Strings is useful for identifying random object files  and  many  other
		       things.   A  string is any sequence of 4 (the default) or more printing
		       characters ending with a newline or a  null.   Unless  the  -  flag  is
		       given,  strings  looks  in  all sections of the object files except the
		       (__TEXT,__text) section.  If no files are specified standard  input  is
		       read.

2. od

		NAME
		     od -- octal, decimal, hex, ASCII dump

		SYNOPSIS
		     od [-aBbcDdeFfHhIiLlOosvXx] [-A base] [-j skip] [-N length] [-t type]
		        [[+]offset[.][Bb]] [file ...]

		DESCRIPTION
		     The od utility is a filter which displays the specified files, or standard
		     input if no files are specified, in a user specified format.
