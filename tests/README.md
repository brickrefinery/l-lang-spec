# L Test Files

This folder contains a colletion of test files that can be used to test an implementation of L.

Each file should be in a pair:

* Test File
  * `<some test>.ldr` : this file can be read as an L file (or opened in stud.io for review) that demonstrates some specific property of the language.
* Output File
  * `output\<some test>.ldr` : this is the expected output of the above test file.

| Test         | Use     | Rough code equivalent |
|--------------|-----------|------------|
| print.ldr | Simple print command      | x='hello'; print x |
| print_args.ldr   | Print command line args | x='hello'; print x; print(args) |
