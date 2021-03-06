My Changes
==========

Made changes on the project structure and code for my own convenience.

If you use this work please cite the TER paper below.

Build
=====

Clone this repository, then you can either open the project in Netbeans and build from there (or) if you have `ant` installed build from console as follows:

``` bash
./dist.sh
```

The compiled jar will be in the `dist` directory, including a distributable `.tgz` file.

New Usage
=========

To simply print the total TER in percent, eg. `TER: 56.56`, use as follows:

```bash
   $ java -jar tercom.jar -r <ref_file> -h <hyp_file>
```

To see the process details in console:

```bash
   $ java -jar tercom.jar -r <ref_file> -h <hyp_file> -o cmd
```


---

------------------------------------------------------------------------
                     TRANSLATION ERROR RATE (TER) 0.8.0

        Matthew Snover
        Shuguang Wang
        Spyros Matsoukas

	Copyright (C) 2007 BBN Technologies and University of Maryland      
------------------------------------------------------------------------

TER contains a Java program for computing the translation error rate,
which is an error metric for machine translation that messures the number of 
edits required to change a system output into one of the references. More details
about the translation error rate can be found at 
http://www.cs.umd.edu/~snover/tercom/

TER parses input reference and hypothesis files as SGML(NIST format), XML,
or Trans. Both reference and hypothesis files should be in the same formats.

The code has been compiled into an executable jar file: tercom.jar

The source files are also provided in the src/ directory.  ter.TER is the main executable class. 

References to TER should cite:

Matthew Snover, Bonnie Dorr, Richard Schwartz, Linnea Micciulla, and
John Makhoul, "A Study of Translation Edit Rate with Targeted Human
Annotation," Proceedings of Association for Machine Translation in the
Americas, 2006.

USAGE
=====

Currently, the following options are supported:

```
   -N normalization, optional, default is no.
   -s case sensitivity, optional, default is insensitive
   -P no punctuations, default is with punctuations.
   -r reference file path, required.
   -h hypothesis file path, required.
   -o output formats, optional, default are all formats.
      Valid formats include "cmd", "ter", "xml", "sum", "sum_nbest" and "pra".
      "cmd", print detailed processing to stdout/console
      "ter", plain text file contains four columns: chunkid, numerrs, numwrds, ter%
      "xml", XML format with detailed alignment for each word
      "pra", plain text with alignment details, simpler than that of tercom_v6b.
      "pra_more", identical to pra output as tercom_v6b.
      "sum", same summary output as that of tercom_v6b.
      "sum_nbest", same nbest summary output as that of tercom_v6b. 
   -n output name prefix, optional, no output will be generated if it is not set.
   -b beam width, optional, default is 20.
   -S translation span prefix, optional, this option only works with single reference.
   -a alternative reference path, optional, this file will be only used to compute the reference length.
   -d maximum shift distance, optional, default is 50 words.   
```

Examples
========
1. Use default values for all options and output in all formats.

```bash
   $ java -jar tercom.jar -r <ref_file> -h <hyp_file> -n <output_prefix>
```

2. Enable normalization and case sensitivity, and set the beam width
   to 10.

```bash
   $ java -jar tercom.jar -N -s -b 10 -r <ref_file> -h <hyp_file> -n <output_prefix>
```

3. Output only summary output.

```bash
   $ java -jar tercom.jar -r <ref_file> -h <hyp_file> -o xml -n <output_prefix>
```

Sample Data
===========

Sample Data and output is provided in the sample-data directory.

sample-data/hyp.txt contains the hypothesis text
sample-data/ref.txt contains the reference text
sample-data/out.txt contains the output of running the following command

cd sample-data/
java -jar ../tercom.jar -r ref.txt -h hyp.txt > out.txt

Source File Descriptions
========================

The primary classes of the tercom computation code are described below:

ter.TER - The command-line UI for the code.  Its usage is described above.

TERcalc - This does all of the work calculating TER.  All of the
methods are static, so no instantiations should ever be made.  To
calculate TER, call: TERcalc.TER(String a, String b).  This tokenizes
and then runs TER.  It returns a ter.core.Alignment object.  If the input is
pretokenized (or tokenized and then converted to another type, such as
Integer (which give better performance)), then you can call
TER(Comparable[], Comparable[]).  If you want to use a different
scoring module (see ter.core.CostFunction class), then use the functions
TER(Comparable[], Comparable[], ter.core.CostFunction) or TER(String, String,
ter.core.CostFunction).

ter.core.Shift - This is a tiny storage class, which stores the information
for indiviual shifts.

ter.core.Alignment - This contains the output of TER.  Some processing might
be needed to make it as pretty as TERcom pra output.

ter.core.CostFunction - This class is used to determine the cost of insertions,
deletions, substitutions, matches, and shifts.  The base class gives
all edits a cost of 1, and matches a cost of 0.  If a researcher wants
to experiment with alternative cost functions, or cost matrices, then
a new child class of ter.core.CostFunction should be made that calculates those cost functions. This can be passed in as the third arguement to the TER
function of TERcalc. It should be noted that this version of the code
only does word to word cost matrices (not phrasal matrices), and that
all costs must be in the range of 0.0 to 1.0.

ter.Parameters - This class is used to parse the command arguments given to ter.TER.