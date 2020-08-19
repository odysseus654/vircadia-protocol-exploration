Description of the "VARNUM" datatype
====================================

The "varnum" datatype is used here to describe an unsigned integer stored in a variable number of bytes depending on the size of the number it stores.

This is based on the UTF-8 encoding scheme, after stripping out rules that are specific to encoding of UNICODE characters.

This was chosen as:
    - Having a fixed number of bits reserved for a value is "both too small and too large" i.e. it sacrifices compactness while limiting the values that can be stored.
    - It keeps smaller, hopefully more used numbers in a more compact representation (anyone up for a Huffman encoding for ID values?)
    - Many of the same reasons cited in the original paper proposing this encoding ( http://doc.cat-v.org/plan_9/4th_edition/papers/utf )

Choosing this encoding is the biggest change from the core SDXF spec, replacing both the Field ID (originally 16 bits) and Length (originally 24 bits) fields.

Numbers less than 128 (up to 0x7F) are stored in one byte:

+---+---+---+---+---+---+---+---+
+ 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
+---+---+---+---+---+---+---+---+
+ 0 | # | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+

Numbers less than 2048 (up to 0x7FF) are stored in two bytes:

+---+---+---+---+---+---+---+---+
+ 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
+---+---+---+---+---+---+---+---+
+ 1 | 1 | 0 | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+

Numbers less than 65,536 (up to 0xFFFF) are stored in three bytes:

+---+---+---+---+---+---+---+---+
+ 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
+---+---+---+---+---+---+---+---+
+ 1 | 1 | 1 | 0 | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+

Numbers less than 2,097,152 (up to 0x1FFFFF) are stored in four bytes:

+---+---+---+---+---+---+---+---+
+ 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
+---+---+---+---+---+---+---+---+
+ 1 | 1 | 1 | 1 | 0 | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+

Numbers less than 67,108,864 (up to 0x3FFFFFF) are stored in five bytes:

+---+---+---+---+---+---+---+---+
+ 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
+---+---+---+---+---+---+---+---+
+ 1 | 1 | 1 | 1 | 1 | 0 | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+

Numbers less than 2,415,919,104 (up to 0x8FFF FFFF) are stored in six bytes:

+---+---+---+---+---+---+---+---+
+ 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
+---+---+---+---+---+---+---+---+
+ 1 | 1 | 1 | 1 | 1 | 1 | 0 | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+

Numbers less than 68,719,476,736 (up to 0xF FFFF FFFF) are stored in six bytes:

+---+---+---+---+---+---+---+---+
+ 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
+---+---+---+---+---+---+---+---+
+ 1 | 1 | 1 | 1 | 1 | 1 | 1 | 0 |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+
+ 1 | 0 | # | # | # | # | # | # |
+---+---+---+---+---+---+---+---+

Needless to say, as this exceeds the capacity of a unit64 larger numbers are unlikely to be useful
