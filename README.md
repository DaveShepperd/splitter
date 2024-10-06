Edit appropriate Makefile (Makefile.linux, Makefile.msys2, Makefile.mingw, Makefile.pi32 for Linux, [Windows] Msys2, [Windows] Mingw or PiOS [32 bit or 64 bit]) if necessary/desired.
Type make -f Makefile.xxx where xxx is appropriate Makefile (or make a symlink to the appropriate Makefile on the system that supports such and just type make).

I had trouble getting the stdin/stdout thing to work correctly with Msys2 on Windows. I suspect it might have something to do with Msys2 not supporting binary input/output on stdin/stdout. I didn't research it.
Instead I just added a requirement for an input/output filename if running under Windows. YMMV. Good luck with it.

```
Usage: splitter [opts]
Where: (NOTE: For all the 'n' below, they may be expressed as hex by prefixing a 0x)
On Linux, Input is expected from stdin, output is directed to stdout. Errors are directed to stderr.
-i, --infile=path   - Optional path to input file. (Default is stdin on Linux; required parameter on Windows.)
-o, --outfile=path  - Optional path to output file. (Default is stdout on Linux; required parameter on Windows.)
-s, --inskip=n      - Set the number of bytes to skip on input. (Default is 0.
-S, --outskip=n     - Set the number of bytes to pre-fill output with fill character before beginning to copy input. (Default is 0.)
-b, --byteskip=n    - Set the number of input bytes to skip while copying to output. (Can only be 0 <= n <= 7; see NOTE below.)
-c, --count=n       - Sets max number of bytes to write to output not including any outskip. If larger than can be provided by input, it will fill
-f, --fill=n        - Sets the output fill character. Output bytes skipped will be filled. (Defaults to 0.)
-v, --verbose       - Increments verbose level (output is to stderr)
-V, --version       - Display the current version (output is to stderr)
-h,-?, --help       - This message
```

NOTE: Use byteskip to split a multi-byte per word input into a smaller one. I.e. byteskip=1 converts a 16 bit
file by outputting every other byte. Using byteskip=2 skips 2 input bytes between output bytes, etc. To select the starting
byte, use the --inskip option. I.e. with --byteskip=1 --inskip=1 (or any odd address) will output just the odd
bytes of the input.

Some examples:
```
# Will simply copy file foo to bar without changes:
splitter < foo > bar
# Will skip the first 256 bytes of foo and copy the rest to bar:
splitter --inskip=0x100 < foo > bar
# Will first fill 128 bytes of bar with the fill character, then beginning with the 0x201's byte on input, copy the
remaining odd bytes of foo to bar
splitter --inskip=0x201 --outskip=0x80 --byteskip=2 < foo > bar
# etc.
```
