# libtn93

This is a C library to compute the Tamura-Nei (1993) genetic distance between genetic sequences. It is essentially a refactoring of [tn93](http://github.com/veg/tn93) by @spond and @stevenweaver intended to make it easier to wrap with Python etc., and to include in pipelines. Differences include:

- Compiles under C rather than C++
- A shared library, rather than an executable
- Avoids use of global variables
- Simplified interface, only exposing a single function


## C library

### API

```C
double tn93(const char * s1, const char * s2,  const unsigned long L, const char matchMode, const long minOverlap);
```

where:

- `s1`: sequence 1, comprised of [IUPAC nucleotide characters](https://en.wikipedia.org/wiki/Nucleotide#Abbreviation_codes_for_degenerate_bases), upper or lower case
- `s2`: sequence 2
- `L`: length of sequences (assumed to be equal)
- `matchMode`:
  - `0` or `RESOLVE`: resolve ambiguities to minimize distance (e.g. R matches A)
  - `1` or `AVERAGE`: average ambiguities (e.g.R-A is 0.5 A-A and 0.5 G-A)
  - `2` or `SKIP`: do not include sites with ambiguous nucleotides in distance calculations
  - `3` or `GAPMM`:  a gap ('-') matched to anything other than another gap is like matching an N (4-fold ambig) to it
- `minOverlap`: will return -1 if there is less than `minOverlap` non-gap characters

### Building

```bash
make
```

This will build a shared library in `/src`.

### Testing

```bash
cd test
make
./test
```

Output:

```bash
Distance (ambig mode 0) = 0.045156
Distance (ambig mode 1) = 0.045156
Distance (ambig mode 2) = 0.045156
Distance (ambig mode 3) = 0.045156
Distance (ambig mode 4) = 0.045156
```

## Python interface

```bash
cd python
python setup.py build_ext --inplace
```

This will build a `tn93` module, which can be used as follows, assuming `python3`.

```python
from pytn import tn93
s1 = "CCCATTAGCCCTATTGAGACTGTACCAGTAAAATTAAAGCCAGGAATGGATGGCCCAAAAGTTAAACAATGGCCATTGACAGAAGAAAAAATAAAAGCATTAGTAGAAATTTGTACAGAGATGGAAAAGGAAGGGAAAATTTCAAAAATTGGGCCTGAAAATCCATACAATACTCCAGTATTTGCCATAAAGAAAAAAGACAGTACTAAATGGAGAAAATTAGTAGATTTCAGAGAACTTAATAAGAGAACTCAAGACTTCTGGGAAGTTCAATTAGGAATACCACATCCCGCAGGGTTAAAAAAGAAAAAATCAGTAACAGTACTGGATGTGGGTGATGCATATTTTTCAGTTCCCTTAGATGAAGACTTCAGGAAGTATACTGCATTTACCATACCTAGTATAAACAATGAGACACCAGGGATTAGATATCAGTACAATGTGCTTCCACAGGGATGGAAAGGATCACCAGCAATATTCCAAAGTAGCATGACAAAAATCTTAGAGCCTTTTAGAAAACAAAATCCAGACATAGTTATCTATCAATACATGGATGATTTGTATGTAGGATCTGACTTAGAAATAGGGCAGCATAGAACAAAAATAGAGGAGCTGAGACAACATCTGTTGAGGTGGGGACTTACCACACCAGACAAAAAACATCAGAAAGAACCTCCATTCCTTTGGATGGGTTATGAACTCCATCCTGATAAATGGACAGTACAGCCTATAGTGCTGCCAGAAAAAGACAGCTGGACTGTCAATGACATACAGAAGTTAGTGGGGAAATTGAATTGGGCAAGTCAGATTTACCCAGGGATTAAAGTAAGGCAATTATGTAAACTCCTTAGAGGAACCAAAGCACTAACAGAAGTAATACCACTAACAGAAGAAGCAGAGCTAGAACTGGCAGAAAACAGAGAGATTCTAAAAGAACCAGTACATGGAGTGTATTATGACCCATCAAAAGACTTAATAGCAGAAATACAGAAGCAGGGGCAAGGCCAATGGACATATCAAATTTATCAAGAGCCATTTAAAAATCTGAAAACAGGAAAATATGCAAGAATGAGGGGTGCCCACACTAATGATGTAAAACAATTAACAGAGGCAGTGCAAAAAATAACCACAGAAAGCATAGTAATATGGGGAAAGACTCCTAAATTTAAACTGCCCATACAAAAGGAAACATGGGAAACATGGTGGACAGAGTATTGGCAAGCCACCTGGATTCCTGAGTGGGAGTTTGTTAATACCCCTCCCTTAGTGAAATTATGGTACCAGTTAGAGAAAGAACCCATAGTAGGAGCAGAAACCTTC"
s2 = "CCCATTAGTCCTATTGAAACTGTACCAGTAAAATTAAAGCCAGGAATGGATGGCCCAAAAGTTAAACAATGGCCATTGACAGAGGAAAAAATAAAAGCATTGGTAGAAATTTGTACAGAAATGGAAAAGGAAGGAAAAATTTCCAAAATTGGGCCTGAAAATCCATACAATACTCCAGTATTTGCCATAAAGAAAAAAGACAGTACTAAATGGAGAAAATTAGTAGATTTCAGAGAACTTAATAAGAGAACTCAAGACTTCTGGGAAGTTCAGTTAGGAATACCACATCCTGCAGGGTTAAAAAAGAAGAAATCAGTAACAGTATTGGATGTGGGTGATGCATATTTTTCAGTTCCCTTAGATAAAGAGTTCAGGAAGTATACTGCATTTACCATACCTAGTATAAACAATGAAACACCACGGATTAGATATCAGTACAATGTGCTTCCACAAGGGTGGAAAGGATCACCAGCAATATTCCAAAGTAGTATGACAAAAATCTTAGAGCCTTTTAAAAAACAAAATCCAGAAATAGTTATCTATCAATACATGGATGATTTGTATGTAGGATCTGATTTAGAAATAGGGCAGCATAGAATAAAAATAGAGGAACTGAGAGAACATCTGTTAAAGTGGGGGTTTACCACACCGGACAAGAAACATCAGAAAGAACCTCCATTTCTTTGGATGGGTTATGAACTCCATCCTGATAAATGGACAGTACAGCCTATAGTGCTGCCAGAAAAAGACAGCTGGACTGTCAATGACATACAGAAGTTAGTGGGAAAATTGAATTGGGCAAGTCAGATTTATGCAGGGATTAAAGTAAAGCAATTATGTAAACTCCTTAGGGGAACCAAAGCACTAACAGAAGTAGTACAACTAACAAAAGAAGCAGAGCTAGAACTGGCAGAAAATAGGGAGATTCTAAAAGAACCAGTACATGGAGTGTATTATGACCCATCAAAAGACTTAATAGCAGAAATACAGAAGCAGGGGCAAGGCCAATGGACATACCAAATTTATCAAGAGCCATTTAAAAACCTGAAAACAGGAAAGTATGCAAGAATGAGGGGTGCCCACACTAATGATGTAAAACAATTAACAGAGGCAGTACAAAAAGTAGCCACAGAAAGCATAGTAATATGGGGAAAGACTCCTAAATTTAAACTACCCATACAAAAAGAAACATGGGAGGCATGGTGGACAGAGTATTGGCAAGCCACCTGGATTCCTGAGTGGGAGTTTGTCAATACCCCTCCCTTAGTAAAATTGTGGTACCAGTTAGAAAAAGAACCCATAATAGGAGCAGAAACTTTC"
for k in range (4):
    dist = tn93(s1,s2,len(s1),k,100)
    print("Distance (ambig mode %d) = %g " % (k, dist))
```

Running:

```bash
python3 test.py
```

should give the same output as the C test file.
