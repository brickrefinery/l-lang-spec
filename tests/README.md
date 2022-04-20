# L Test Files

This folder contains a collection of test files that can be used to test an implementation of L.

Each file should be in a pair:

* Test File
  * `<some test>.ldr` : this file can be read as an L file (or opened in stud.io for review) that demonstrates some specific property of the language.
* Output File
  * `output\<some test>.ldr` : this is the expected output of the above test file.

| Test         | Use     | Rough code equivalent |
|--------------|-----------|------------|
| print.ldr | Simple print command      | x='hello'; print x |
| print_args.ldr   | Print command line args | x='hello'; print x; print(args) |
| math_plus.ldr | Addition test | x=10+3; x=x+25; print x |
| math_minus.ldr | Subtraction test | x=10-3; x=x-5; print x |
| meta.ldr | Add and use tokens using the META LLANG TOKEN command | x='hello'; print x (swapping out "=" and "print" for new tokens) |
