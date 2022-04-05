# L language specification

[![CC BY-NC-SA 4.0][cc-by-nc-sa-shield]][cc-by-nc-sa]

General specification for the L language, leveraging LEGO CAD programs (like [stud.io](https://www.bricklink.com/v3/studio/download.page)) as a programming language.

## Overview

> *A [toy language](https://en.wikipedia.org/wiki/Esoteric_programming_language), using toys.*

L is a language that takes the standard file format of LEGO CAD programs and should be able to interpret the file not as a LEGO model, but instead as programming code, often outputting a new LEGO model.

While nothing in here should be taken seriously, the language itself takes itself seriously, and actual programs (within limits) can be made using it.

## Philosophy

"Why?" is often a hard question to answer, and in this case it may be harder than usual. There's little to "learn" here - learning how to make a complier is documented better than this ever will, and learning to code could be done by picking any other language - but there is joy to be found. And that, joy, is good enough.

If any of this language brings you joy, either by laughing at the standard, or by trying your hand at compiling a pirate ship into a car, then this language has done what it has set out to do.

### Goal: Turing complete

As its first goal, this language should be [Turing complete](https://en.wikipedia.org/wiki/Turing_completeness), but that's a fairly straightforward bar. Still, it feels right that we should be able to get this.

While this is a crude summary that's better tackled by the link above, we'll consider this goal complete if the language has these basic functions:

* Iteration (x + 1)
* Variables
* Comparison operatiors (if...)
* Loops

To be a functional scripting language we'll need quite a bit more than that though.

### Goal: Flexible construction

Impose as few limitations on your construction as possible to implement the language. While parts may be required to include, keep in mind construction options where possible.

**Practical example:** Part colors and locations within the model should be ignored by the interpreter and considered a "comment", allowing the user to put them wherever in the model they'd like with impact to the program being made.

### Goal: Skeuomorphic tokens

Wherever possible, choose parts for tokens that imply what they represent.

**Practical example:** Minifigure heads used as variable names since their "brains" are what hold the information.

### Goal: Coding done within an IDE (like stud.io)

While the file format is text-based, and any text editor can then edit that file, general programming should be able to be done within the bounds of a LEGO CAD program. Stud.io is considered the reference implementation for this and is what testing will be done against.

**Practical example:** Stud.io doesn't have a facility to add arbitrary META commands to the file, so while we may leverage META commands, doing basic coding shouldn't then require them.

### LDraw output

With the input of the files being LEGO CAD files (ldraw files), the output should also produce a valid ldraw file.

Note: there is no expectation that this output is itself successfully readable by L, but should be readable by any LEGO CAD program.

## File Standard

The file standard used by LEGO CAD programs is typically in the ldraw format, so that's the format we'll use for this language.

The entire file format is listed on the [ldraw.org](https://www.ldraw.org/article/218.html) site, but we're going to minimize what parts of the file we use.

Ldraw files (.ldr files) are readable by most LEGO CAD programs, and any program written for L will still be a valid Ldraw file.

## Advanced Features

Advanced features are intended as a convenience but may sometimes bend the rules and conventions established above.

### Comments

It's disappointing that comments are relegated to the "advanced" section, but sticking within the limits of the LEGO CAD as an IDE, there isn't a trivial way to add arbitray text.

To add a comment, we use a standard META command:

```l-draw
0 // <comment>
```

This form of commenting is already part of the Ldraw spec and isn't changed or supplimented further. This allows comments to be handled correctly in any program that already supports the Ldraw file format.

### Token mapping

Using META commands, we can tell the interpreter to also consider a piece to count as another piece. The practical effect of this is to allow you to *not* use a part in your construction. For instance, if you were making your program into a boat, and didn't want to use the grill piece (![A LEGO grill piece](images/2412b25.png)) as your "if" command.

There are thousands of "undefined" parts available for use, intentionally including some of the most common bricks. You can utilize these by mapping them to a specific token.

The META command to do this in your code looks like this:

```l-lang
0 !TOKEN 2412=3001
```

This TOKEN command will then add the part 3001 (a generic 2x4 brick) to whatever token 2412 is assigned to (2412 is that grill piece, so in this case it's making it an 'if' command).

Multiple assignments can be made like this, just using any original token value to map to any others. This command, for instance, creates a few new variable options:

```l-lang
0 !TOKEN 3626ap01=4738a
0 !TOKEN 3626ap01=18742
0 !TOKEN 3626ap01=92926
```

In this case, 3626ap01 is a minifure head, and 4738a, 18742, and 92926 are a treasure chest, a bucket, and a trash can. With this line, all of which are now usable as variables just the same as any minifigure head. In this example, any existing variable (any minifigure head) can be used to create the mapping.

These mappings can also be done on one line, separated with commas (but no spaces), like so:

```l-lang
0 !TOKEN 3626ap01=4738a,18742,92926
```

## License

This work is licensed under a
[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License][cc-by-nc-sa].

[![CC BY-NC-SA 4.0][cc-by-nc-sa-image]][cc-by-nc-sa]

[cc-by-nc-sa]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[cc-by-nc-sa-image]: https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png
[cc-by-nc-sa-shield]: https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg
