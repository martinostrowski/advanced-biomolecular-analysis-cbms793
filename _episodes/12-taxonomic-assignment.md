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

Then check the output. What is represented in each column?

~~~
EKHPIGLK_00001  WP_040495630.1  76.9    316     73      0       9       324     95      410     1.3e-143        518.8
EKHPIGLK_00001  BAN01327.1      75.6    316     77      0       9       324     95      410     2.5e-142        514.6
EKHPIGLK_00001  GBL20536.1      74.4    316     81      0       9       324     94      409     1.2e-139        505.8
EKHPIGLK_00001  PHX93058.1      74.4    316     81      0       9       324     94      409     1.5e-139        505.4
EKHPIGLK_00001  KRO44663.1      74.1    316     82      0       9       324     82      397     5.8e-139        503.4
EKHPIGLK_00001  KRO36171.1      75.6    315     77      0       9       323     94      408     1.7e-138        501.9
EKHPIGLK_00001  KRO48561.1      73.7    316     83      0       9       324     61      376     4.9e-138        500.4
EKHPIGLK_00001  KRO53220.1      73.7    316     83      0       9       324     94      409     4.9e-138        500.4
EKHPIGLK_00001  WP_116999185.1  73.7    316     83      0       9       324     100     415     7.8e-136        493.0
EKHPIGLK_00001  KGA09915.1      72.0    314     88      0       9       322     95      408     4.3e-134        487.3
EKHPIGLK_00001  WP_052601171.1  68.8    317     99      0       9       325     95      411     1.1e-126        462.6
EKHPIGLK_00001  RIK04981.1      63.1    317     116     1       9       324     95      411     2.3e-111        411.8
EKHPIGLK_00001  OYV59647.1      60.4    316     117     3       9       322     32      341     1.3e-101        379.4
EKHPIGLK_00001  OYV65711.1      57.4    317     130     3       9       324     95      407     3.1e-100        374.8
~~~
{: .output}

If you have many bins it is time to start thinking about an efficient way to process them all. One approach is to create a batch file to run a diamond blastp on every bin. The batch file will utilise a loop to create the list of commands. We will save them in a file called `do-diamond.sh`

~~~
for i in *faa; do echo "diamond blastp -q $i -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o $i.dvnr.out"; done > do-diamond.sh
~~~
{: .bash}


check that the contents look sane
~~~
less do-diamond.sh
~~~
{: .bash}

~~~
diamond blastp -q bin.10.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.10.prokka.out.faa.dvnr.out
diamond blastp -q bin.11.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.11.prokka.out.faa.dvnr.out
diamond blastp -q bin.12.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.12.prokka.out.faa.dvnr.out
diamond blastp -q bin.13.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.13.prokka.out.faa.dvnr.out
diamond blastp -q bin.14.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.14.prokka.out.faa.dvnr.out
diamond blastp -q bin.15.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.15.prokka.out.faa.dvnr.out
diamond blastp -q bin.16.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.16.prokka.out.faa.dvnr.out
diamond blastp -q bin.17.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.17.prokka.out.faa.dvnr.out
diamond blastp -q bin.18.prokka.out.faa -d /disks/jen/scratch-ssd/db/nr_oct2018.dmnd -p 10 -f 6 -o bin.18.prokka.out.faa.dvnr.out
~~~
{: .output}


suggest running one of these commands to make sure everything executes correctly

if it is OK then make the file executable (i.e. a shell script) with the following

we need to change the permissions to make this file execuatble
~~~
chmod a+x do-diamond.sh
~~~
{: .bash}

then run the batch, and wait

~~~
./do-diamond.sh
~~~
{: .bash}

Next we'll extract taxonomic associations from the accession numbers of the diamond blastp output using BASTA. You will need to ensure that you are in the right directory and edit the input and output names to match your files.

~~~
basta sequence -b BEST_HIT -d /disks/jen/scratch-ssd/db/.basta/taxonomy/ ***.bin14.dvnr.out ***.bin14.basta.out prot
~~~
{: .bash}


Have a look at the results using bash tools. Here are some examples

~~~
less ***.bin14.basta.out
~~~
{: .bash}


~~~
EKHPIGLK_00014  Unknown Bacteria;Actinobacteria;Acidimicrobiia;Acidimicrobiales;Ilumatobacteraceae;Ilumatobacter;Ilumatobacter_coccineus;
EKHPIGLK_00023  Unknown Bacteria;Actinobacteria;Acidimicrobiia;Acidimicrobiales;Ilumatobacteraceae;Ilumatobacter;Ilumatobacter_nonamiensis;
EKHPIGLK_00043  Bacteria;Actinobacteria;Acidimicrobiia;Acidimicrobiales;        Bacteria;Actinobacteria;Acidimicrobiia;Acidimicrobiales;Ilumatobacteraceae;Desertimonas;Desertimonas_flava;
EKHPIGLK_00076  Bacteria;       Bacteria;Candidatus_Tectomicrobia;unknown;unknown;unknown;Candidatus_Entotheonella;Candidatus_Entotheonella_palauensis;
EKHPIGLK_00085  Bacteria;Actinobacteria;        Bacteria;Actinobacteria;Acidimicrobiia;unknown;unknown;unknown;Acidimicrobiia_bacterium_BACL6_MAG-120924-bin43;
EKHPIGLK_00095  Unknown Bacteria;Actinobacteria;Acidimicrobiia;Acidimicrobiales;Ilumatobacteraceae;Ilumatobacter;Ilumatobacter_coccineus;
EKHPIGLK_00100  Unknown Archaea;Euryarchaeota;unknown;unknown;unknown;unknown;Euryarchaeota_archaeon;
~~~
{: .output}

What is the output in each column?

then send the results to krona plot

~~~
cat ***.bin14.basta.out | cut -f 1,3 > ***.bin14.basta.bh.out
~~~
{: .bash}
~~~
/usr/local/anaconda2/bin/basta2krona.py ju.bin14.basta.bh.out krona.test.html
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
> > echo "cat $i | cut -f 1,3 > nd.bin.$name.basta.bh.out"
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





