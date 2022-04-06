# L language specification

[![CC BY-NC-SA 4.0][cc-by-nc-sa-shield]][cc-by-nc-sa]

General specification for the L language, leveraging LEGO CAD programs (like [stud.io](https://www.bricklink.com/v3/studio/download.page)) as a programming language.

## Overview

> *A [toy language](https://en.wikipedia.org/wiki/Esoteric_programming_language), using toys.*

L is a language that takes the standard file format of LEGO CAD programs and should be able to interpret the file not as a LEGO model, but instead as programming code, often outputting a new LEGO model.

While nothing in here should be taken seriously, the language itself takes itself seriously, and actual programs (within limits) can be made using it.

This language spec makes some assumptions that you have some coding background, but as much as possible is trying to limit how much technical depth you need to catch it all.

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

**Practical example:** Minifigure heads (![minifigure head](images/3626ap0125.png)) used as variable names since their "brains" are what hold the information.

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

A ldraw file is written as a series of lines, each line is one ldraw command. They fall into one of six variations, with the first character of each line noting what it is:

* 0: Comment or META Command
* 1: Sub-file reference
* 2: Line
* 3: Triangle
* 4: Quadrilateral
* 5: Optional Line

Commands 2-4 are used to create the actual shapes of blocks and such. We're not going to address them in the standard here, and instead use pre-defined blocks for our code. These blocks, in turn, use several of those 2-4 commands to create each block, but that's all handled behind the scenes.

The comments and META commands have several official options that can be found in the file spec, linked above. For our programs we're going to largely limit ourselves to the defaults. A simple ldraw file might then look like this:

```l-lang
0 Name: Sample Program
0 Author: Herbie Kay
1 133 -190 160 0 1 0 0 0 1 0 -0 0 1 3004.dat
1 15 -190 160 20 1 0 0 0 1 0 -0 0 1 3004.dat
1 4 -190 152 10 1 0 0 0 1 0 -0 0 1 3068b.dat
1 14 -190 184 10 1 0 0 0 1 0 -0 0 1 3022.dat
```

The lines starting with a 0 are META/comments (in this case the file and author name), and the lines starting with a 1 are the four blocks used to make up this model. Let's look at one line as an example:

```l-lang
1 133 -190 160 0 1 0 0 0 1 0 -0 0 1 3004.dat
```

For the purpose of L, we're going to choose to ignore most of this line, allowing the user to put this part anywhere in the model without affecting how it will be interpreted, but the line can be broken down like this:

```l-lang
1  133      -190 160 0           1 0 0 0 1 0 -0 0 1             3004.dat
1 <color> <x y z {coordinates}> <a b c d e f g h i {rotation}> <part>
```

To make this language as flexible as possible for making models that can both be compiled and actually make something, the color, coordinates, and rotation are all ignored by the interpreter.

This leaves us with pile of parts, then, to make our language.

A META command that's going to be very useful for us is one that helps organize those parts together. It's a "STEP" command and the line is just this:

```l-lang
0 STEP
```

In models, these steps map to steps in an instruction booklet to help someone build the model.

We can also have callouts to other "files" which are submodels (for instance, building a tree as its own little thing before being added to the larger model of a house). These can be within the same file, or a pointer to an external file, allowing scripts to be able to call other scripts. How this works is described in the file standard, and below in the *functions* section.

## Tokens

Tokens represent commands and keywords in a language. This is how we'll give meaning to our code. For example to code something like "x = 10" we need to know how to represent a variable (x), an assignment operator (=) and have some way to deal with values (10).

The end of each command (like the x = 10, above) should end in a step command:

```l-lang
0 STEP
```

And since every token or block is also a separate line, a command like our x = 10 will ultimately have to look closer to this:

```l-lang
x
=
10
0 STEP
```

Though, obviously, we'll want to represent those peices with, well, LEGO pieces. This section will describe just how we are doing that.

### Variables

* Variables are all global in scope.
* Variables are all untyped.

All minifigure heads are variables. Head accessories are left undefined, so you can dress up your minifigure heads in the model however you like, though the head part number itself (the face design) is what is used to "name" an track the variable.

![variables](images/variables.png)
> *Facial expressions can make great ways to imply what sort of information a given variable holds...*

#### Special variables

A few variables are set aside as special and should have additional consideration when used.

| Variable         | Use     | Notes |
|--------------|-----------|------------|
| Basic smiley head, `3626ap01` (![smiley head](images/3626ap0125.png)) | Command line args      | Additional command line args passed into the script will be put here        |
| Basic smiley head, female, `3626cpb0633`  (![smiley head, female](images/3626cpb063325.png))   | Function args | Any arguments when calling a function will be placed into this variable. This variable can also be used to store return values from functions.      |

### Constants

#### Numbers

Numbers are the standard number blocks 0-9:

![number blocks](images/numbers.png)

The part numbers for these are `3005pt0`, `3005pt1`, `3005pt2`, ... `3005pt9`.

#### Letters

Letters use the letter tiles:

![letter tiles](images/letters.png)

The part numbers for these are `3070bpb009` (letter a), `3070bpb010` (letter b), `3070bpb011` (letter c), ... `3070bpb034` (letter z)

### Assignments

There isn't a fun piece that looks like an equal sign, so instead we're going to clip parts together, with a LEGO clip, part `4085a` (![a LEGO clip](images/4085a25.png)). An assignment will look like "Clip \<variable\> \<value to assign\>". To end the command, mark it with a STEP command.

So, to look back up to our original assignment question of "x = 10", let's create that now:

```l-lang
1 1 -110 -8 -30 1 0 0 0 1 0 0 0 1 4085a.dat
1 4 -70 -24 -50 1 0 0 0 1 0 0 0 1 3626cpb3.dat
1 15 -30 -24 -50 1 0 0 0 1 0 0 0 1 3005pt1.dat
1 15 -10 -24 -50 1 0 0 0 1 0 0 0 1 3005pt0.dat
0 STEP
```

Which would create something that looks like this:

![an example assignment](images/assignment.png)

Because positions and color are ignored, when building your boat (or house, or dragon, ...) these parts can be put anywhere in the model.

### Math

### Strings

### Comparison

### Loops

### Additional functions

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

This TOKEN command will then add the part `3001`, a generic 2x4 brick (![a 2x4 brick](images/300125.png)), to whatever token `2412` is assigned to (`2412` is that grill piece, so in this case it's making it an 'if' command).

Multiple assignments can be made like this, just using any original token value to map to any others. These commands, for instance, create a few new variable options:

```l-lang
0 !TOKEN 3626ap01=4738a
0 !TOKEN 3626ap01=18742
0 !TOKEN 3626ap01=92926
```

In this case, `3626ap01` is a minifure head (![minifigure head](images/3626ap0125.png)), and `4738a`, `18742`, and `92926` are a treasure chest (![a treasure chest](images/4738a25.png)), a bucket (![a bucket](images/1874225.png)), and a trash can (![a trash can](images/9292625.png)). With this line, all of which are now usable as variables just the same as any minifigure head. In this example, any existing variable (any minifigure head) could have been used to create the mapping.

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
