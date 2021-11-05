# voiceseq

Simple voice sequencer.

## Syntax

    voiceseq [output] [map] [source] [text]

`[output]` is the path to the WAV file to generate.

`[map]` is the path to the mapping file for the source.  See below for specifics.

`[source]` is the path to the WAV file to read as the source voice.

`[text]` is the actual text string to read.  Every character of this string must have an entry in the mapping file.  A space character is only allowed if the space character has an entry in the map.  Parentheses can enclose a sequence of multiple characters that will be looked up as a single entity in the mapping file.  Enclosing a single character in parentheses is the same as just using the single character by itself.

## Mapping file

The mapping file specifies the locations of each character within the source WAV file.  It is a [Shastina](http://www.purl.org/canidtech/r/shastina) text file that has the following format:

    %voiceseq-map;

    "a" 0.7 1.2 def
    "b" 1.85 2.35 def
    "c" 3.0 3.5 def

    |;

The file must begin with the `%voiceseq-map` metacommand signature and end with the `|;` EOF marker.  Between those, only three entities are allowed:

1. Double-quoted string with no prefix
2. Floating-point value
3. `def` operator

Strings must consist of at least one and at most 15 characters.  All characters must be US-ASCII printing characters, including the space character.  Character keys are case sensitive.

Floating-point values must be a sequence of zero or more decimal digits, optionally followed by a period and another sequence of zero or more decimal digits, with the additional restriction that there must be at least one decimal digit somewhere.

Both string and floating-point entities have the effect of pushing their value on top of the Shastina interpreter stack.

The `def` operator pops two floating-point values and a string value off the stack, as suggested in the example above.  The string value is the character key to assign.  Each character key must be unique, but they do not need to be sorted in any order.  The floating-point value that is lower in the interpreter stack is the starting time in seconds, and the floating-point value that is on top of the stack is the ending time in seconds, such that the ending time subtracted by the starting time is the duration in seconds of the sound.  The ending time must be greater than the starting time, and all time ranges must be within the source WAV file.

## Compilation

The following dependencies are required:

- [libshastina](http://www.purl.org/canidtech/r/shastina) version 0.9.2 beta or compatible
- [libktwav](http://www.purl.org/canidtech/r/libktwav)

The math library may also be required with `-lm`

Sample GCC build command (all on one line), assuming that the header files for Shastina and `libktwav` are in `include/dir` and the library files for Shastina and `libktwav` are in `lib/dir`:

    gcc -O2 -o voiceseq
      -I/include/dir
      -L/lib/dir
      voiceseq.c
      -lshastina
      -lktwav
      -lm
