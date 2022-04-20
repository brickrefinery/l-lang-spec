# Print commands in L

| Part         | Operator     | Notes |
|--------------|-----------|------------|
| simple envelope, `3069bp01` (![simple envelope](images/3069bp0125.png)) | Print      | Will output the value of everything until the next STEP. Minifigures (variables) will output their stored value.        |
| envelope with formatting, `3069bpb0851` (![simple envelope](images/3069bpb085125.png)) | Print with location      | Same as print above, but instead of print deciding the next appropriate, you can set the location of the output. |


The above two commands are variations of a sort of "print" command that will create output, but work differently. The

## Print: Simple envelope, `3069bp01` (![simple envelope](images/3069bp0125.png))

Given a string, this should make some assumptions and put the letters into the output in roughly a sensible way. This should function largely like other languages "printline" command - write out the string and then future strings should be on other lines.

This code:
```l-lang
print 'hello'
print 'world'
```
should then have the result of LEGO blocks set up in the form like this:
```l-lang
hello
world
```

To do this, using the LDraw units, each letter should be spaced 40 units from the next, and each line 40 units from the previous.

As a reference, an LDraw line for a block will look like this:
```l-lang
1 <color> <x y z> <a b c d e f g h i> <block>
Example:
1 15 10 0 10 1 0 0 0 1 0 0 0 1 3005pth.dat
```

The `<a b c d e f g h i>` is the rotation of the block and out of scope for print. This can be left at `1 0 0 0 1 0 0 0 1` which creates a rotation of "straight up".

`<x y z>` is the location with `x` and `z` being the horizontal plane. 

* `x`: This should be set to `10 + (40 * <current char count>)`
* `y`: This can be left as 0 to sit the blocks directly on the "floor".
* `z`: It is easier to read lines if `z` works in the negative, starting at 10, so each line should be `10 - (40 * <current line>)` for the `z`.

When choosing which block, when printing letters, the block can be `3005pt<x>.dat` where `<x>` is the letter to print. `3005pta.dat`, `3005ptb.dat`, `3005ptcdat`, etc.

That `hello world` above would then produce output like:

```l-lang
1 15 10 0 10 1 0 0 0 1 0 0 0 1 3005pth.dat
1 15 50 0 10 1 0 0 0 1 0 0 0 1 3005pte.dat
1 15 90 0 10 1 0 0 0 1 0 0 0 1 3005ptl.dat
1 15 130 0 10 1 0 0 0 1 0 0 0 1 3005ptl.dat
1 15 170 0 10 1 0 0 0 1 0 0 0 1 3005pto.dat
1 15 10 0 -30 1 0 0 0 1 0 0 0 1 3005ptw.dat
1 15 50 0 -30 1 0 0 0 1 0 0 0 1 3005pto.dat
1 15 90 0 -30 1 0 0 0 1 0 0 0 1 3005ptr.dat
1 15 130 0 -30 1 0 0 0 1 0 0 0 1 3005ptl.dat
1 15 170 0 -30 1 0 0 0 1 0 0 0 1 3005ptd.dat
```

## Print, with location: Envelope with formatting, `3069bpb0851` (![simple envelope](images/3069bpb085125.png))
