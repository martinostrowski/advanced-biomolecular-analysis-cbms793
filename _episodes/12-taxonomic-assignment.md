---
source: Rmd
title: "Taxonomic assignment using LCA. Week 3-4"
teaching: 5
exercises: 40
questions:
- "How can I assign my sequences to the nearest taxonomic lineage?"
objectives:
- "Identify databases and tools that could serve as sources of taxonomic information for your sequences"
- "Learn how to repeat commands and processes tens, hundreds or thousands of files using loops"
- "Extract functional information from sequence database searches"
- "Practice writing loops"
keypoints:
- "Loops are very useful for repeating commands over multiple files"
---

## BAsic Sequence Taxonomy Annotation

[BASTA](https://github.com/timkahlke/BASTA/wiki) is a command-line tool written in python that was developed to enable a basic taxonomy annotation based on a Last Common Ancestor algorithm. If you like BASTA and use it for publications please cite it as "Kahlke T and Ralph PJ (2018), BASTA–Taxonomic classification of sequences and sequence bins using Last Common Ancestor estimations. Meth. Ecol. Evol. doi:10.1111/2041‐210X.13095"


We can use BASTA to determine the most likely source of each bin, contig and sequence. We will use BASTA to evaluate the specificity of the MetaBat binning by comparing the taxonomic composition of the overall assembly, the consensus LCA estimate for each entire bin and the composition protein sequences in each bin.

Our input analyses will be a diamond (blastp) sequence search of the predicted protein sequences from PROKKA.

cd to your metabat.bins directory and rename your protein sequence predictions from prokka (.faa) while copying to a new directory

~~~
mkdir prokka.faa

for subdir in *prokka.out; do cp $subdir/*faa prokka.faa/$subdir.faa; done;
~~~
{: .language-bash}

cd to the new directory and check the contents are what you expect

~~~
cd prokka.faa

ls
~~~
{: .language-bash}

There are probably a large number of bins to process so we should think about constructing a loop. Putting things together piece by piece.

In the example we'll start with the predicted protein sequences in bin.10 and do a sequence search (blastp) against the NCBI non-redundant database using [diamond](https://github.com/bbuchfink/diamond). DIAMOND is a sequence aligner for protein and translated DNA searches, designed for high performance analysis of big sequence data.

The help file is always a good place to start
~~~
diamond help
~~~
{: .language-bash}

~~~
mostrowski@jen:~$ diamond help
diamond v0.9.21.122 | by Benjamin Buchfink <buchfink@gmail.com>
Licensed under the GNU AGPL <https://www.gnu.org/licenses/agpl.txt>
Check http://github.com/bbuchfink/diamond for updates.

Syntax: diamond COMMAND [OPTIONS]

Commands:
makedb	Build DIAMOND database from a FASTA file
blastp	Align amino acid query sequences against a protein reference database
blastx	Align DNA query sequences against a protein reference database
view	View DIAMOND alignment archive (DAA) formatted file
help	Produce help message
version	Display version information
getseq	Retrieve sequences from a DIAMOND database file
dbinfo	Print information about a DIAMOND database file

General options:
--threads (-p)         number of CPU threads
--db (-d)              database file
--out (-o)             output file
--outfmt (-f)          output format
	0   = BLAST pairwise
	5   = BLAST XML
	6   = BLAST tabular
	100 = DIAMOND alignment archive (DAA)
	101 = SAM

	Value 6 may be followed by a space-separated list of these keywords:

	qseqid means Query Seq - id
	qlen means Query sequence length
	sseqid means Subject Seq - id
	sallseqid means All subject Seq - id(s), separated by a ';'
	slen means Subject sequence length
	qstart means Start of alignment in query
	qend means End of alignment in query
	sstart means Start of alignment in subject
	send means End of alignment in subject
	qseq means Aligned part of query sequence
	sseq means Aligned part of subject sequence
	evalue means Expect value
	bitscore means Bit score
	score means Raw score
	length means Alignment length
	pident means Percentage of identical matches
	nident means Number of identical matches
	mismatch means Number of mismatches
	positive means Number of positive - scoring matches
	gapopen means Number of gap openings
	gaps means Total number of gaps
	ppos means Percentage of positive - scoring matches
	qframe means Query frame
	btop means Blast traceback operations(BTOP)
	staxids means unique Subject Taxonomy ID(s), separated by a ';' (in numerical order)
	stitle means Subject Title
	salltitles means All Subject Title(s), separated by a '<>'
	qcovhsp means Query Coverage Per HSP
	qtitle means Query title

	Default: qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore
--verbose (-v)         verbose console output
--log                  enable debug log
--quiet                disable console output

Makedb options:
--in                   input reference file in FASTA format

... truncated
~~~
{: .output}


And here is an example command for a diamond blastp search of the protein sequences in bin 10 against the NCBI nr database (from October 2018)
~~~
diamond blastp -q bin.10.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.10.prokka.dvnr.out
~~~
{: .bash}


If you have many bins it is time to start thinking about an efficient way to process them all. One approach is to create a batch file to run a diamond blastp on every bin. The batch file will utilise a loop to create the list of commands. We will save them in a file called `do-diamond.sh`

~~~
for i in *faa; do echo "diamond blastp -q $i -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o $i.dvnr.out"; done > do-diamond.sh
~~~
{: .output}


check that the contents look sane
~~~
less do-diamond.sh
~~~
{: .bash}

suggest running one of these commands to make sure everything executes correctly

if it is OK then make the file executable (i.e. a shell script) with the following

we need to change the permissions to make this file execuatble
~~~
chmod a+x do-diamond.sh
~~~
{: .bash}

#then run the batch, and wait

~~~
./do-diamond.sh
~~~
{: .bash}

Next we'll extract taxonomic associations from the accession numbers of the diamond blastp output using BASTA

~~~
basta sequence -b BEST_HIT -d /disks/jen/scratch-ssd/db/.basta/taxonomy/ ju.bin14.dvnr.out ju.bin14.basta.out prot
~~~
{: .bash}


Have a look at the results using bash tools

~~~


~~~
{: .bash}


~~~


~~~
{: .output}


then send the results to krona plot

~~~
cat ju.bin14.basta.out | cut -f 1,3 > ju.bin14.basta.bh.out

/usr/local/anaconda2/bin/basta2krona.py  krona.test.html
~~~
{: .bash}

> ## Questions:
>
> Write a loop to process all of the diamond blastp output files
>
> >
> > for i in *.prokka.out.basta.out;
> > do
> > name=$(basename $i .prokka.out.basta.out)
> > echo "cat $i | cut -f 1,3 > nd.bin.$name.basta.bh.out
> > done > do-bastatrim.sh
> {: .solution}
{: .challenge}



~~~
for i in *.prokka.out.basta.out;
do
name=$(basename $i .prokka.out.basta.out)
echo "cat $i | cut -f 1,3 > nd.bin.$name.basta.bh.out
done > do-bastatrim.sh
~~~
{: .bash}

~~~
/usr/local/anaconda2/bin/basta2krona.py nd.bin10.basta.bh.out,nd.bin12.basta.bh.out,nd.bin.bin.11.basta.bh.out,nd.bin.bin.13.basta.bh.out,nd.bin.bin.15.basta.bh.out,nd.bin.bin.1.basta.bh.out,nd.bin.bin.3.basta.bh.out,nd.bin11.basta.bh.out,nd.bin.bin.10.basta.bh.out,nd.bin.bin.12.basta.bh.out,nd.bin.bin.14.basta.bh.out,nd.bin.bin.16.basta.bh.out,nd.bin.bin.2.basta.bh.out,nd.bin.bin.4.basta.bh.out  krona.test.html
~~~
{: .bash}

> ## Questions:
>
> How does the results of taxonomic assignment sequences vary between the different  methods of assignment, top-hit versus LCA?
>
{: .challenge}

Krona provides an excellent interactive viewer to begin to explore hierarchal data, such as taxonomic lineages and protein functional categories (hint!). Look at the structure of the Super-focus output based on the SEED hierarchy of functional categories.



Here is a composite taxonomic assignemnt of all of the contigs generated by the class this year. `student.contigs.LCA` refers to predicted protein sequences assigned using the LCA approach as implemented by BASTA. In contrast `student.contigs.bh` was generated with the best-hit results for each protein sequence.

<embed width="1000" height="1000" src="../fig/krona.LCAvsbh.html"></embed>




> ## Questions:
>
>
>
>
>
>
>
> {: .language-bash}
>
> > ## Solutions
> >
> >
> > ~~~
> >
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}





