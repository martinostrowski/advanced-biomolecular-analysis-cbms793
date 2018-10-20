---
title: "Assessing Read Quality"
teaching: 30
exercises: 20
questions:
- "How can I describe the quality of my data?"
objectives:
- "Explain how a FASTQ file encodes per-base quality scores."
- "Interpret a FastQC plot summarizing per-base quality across all reads."
- "Use `for` loops to automate operations on multiple files."
keypoints:
- "Quality encodings vary across sequencing platforms."
- "`for` loops let you perform the same set of operations on multiple files with a single command."
---

# Bioinformatics workflows

When working with high-throughput sequencing data, the raw reads you get off of the sequencer will need to pass
through a number of  different tools in order to generate your final desired output. The execution of this set of
tools in a specified order is commonly referred to as a *workflow* or a *pipeline*.

An example of the workflow we will be using for our metagenome assembly, binning and annotation exercises is given below

![workflow](../fig/workflow.png)


1. Quality control - Assessing quality using FastQC
2. Quality control - Trimming and/or filtering reads (if necessary)
3. Assemble a Metagenome/Genome or Align reads to reference genome
4. Perform post-alignment processes such as binning, gene calling and annotation



# Week 1 review: Quality Control

The first step in the Metagenome assembly and annotation workflow is to take the FASTQ files received from the sequencing facility
and assess the quality of the sequence reads.

## Details on the FASTQ format

Although it looks complicated (and it is), it's easy to understand the
[fastq](https://en.wikipedia.org/wiki/FASTQ_format) format with a little decoding. Some rules about the format
include...

|Line|Description|
|----|-----------|
|1|Always begins with '@' and then information about the read|
|2|The actual DNA sequence|
|3|Always begins with a '+' and sometimes the same info in line 1|
|4|Has a string of characters which represent the quality scores; must have same number of characters as line 2|

We can view the first complete read in one of the files our dataset by using `head` to look at
the first four lines.

~~~
$ head -n 4 57971_1_PE_480bp_MM_AGRF_H5VJHBCX2_AAGAGGCA-GTAAGGAG_L001_R1.fastq


~~~
{: .bash}

~~~
@D00626:337:H5VJHBCX2:1:1106:1162:2154 1:N:0:AAGAGGCA+GTAAGGAG
GACCTAAGATACAAATCTTAGAATCAAGATCATGCTCATCTTTTAGCTTTGTTTGGATATTATAACTTTGAGCAGGACTATCCGCTCCTGTGCAAACACCAGTTGTCAGAGCAATCGCTGATGATCTTCTTCCCATGAC
+
DDDDDIIHIIIIGIIIIIIIIIGHIIIIIIIIHIIGHIHIHHHIHGHIHIIHIHHGIHIIIIIIIIIIIIIIIIIIHIIIIIIIIIIIIIEFHHHHIIIIFIIEHHHFEHHFHHIEHIHIIIHIIIIHIHHIGFHI?HG

~~~
{: .output}

All of the nucleotides in this read are known (i.e. they are not `N`). This is a pretty good read!

Line 4 shows the quality for each nucleotide in the read. Quality is interpreted as the
probability of an incorrect base call (e.g. 1 in 10) or, equivalently, the base call
accuracy (eg 90%). To make it possible to line up each individual nucleotide with its quality
score, the numerical score is converted into a code where each individual character
represents the numerical quality score for an individual nucleotide. For example, in the line
above, the quality score line is:

~~~
DDDDDIIHIIIIGIIIIIIIIIGHIIIIIIIIHIIGHIHIHHHIHGHIHIIHIHHGIHIIIIIIIIIIIIIIIIIIHIIIIIIIIIIIIIEFHHHHIIIIFIIEHHHFEHHFHHIEHIHIIIHIIIIHIHHIGFHI?HG
~~~
{: .output}

Each of these characters represent the encoded quality for an
individual nucleotide. The numerical value assigned to each of these characters depends on the
sequencing platform that generated the reads. The sequencing machine used to generate our data
uses the standard Sanger quality PHRED score encoding, using by Illumina version 1.8 onwards.
Each character is assigned a quality score between 0 and 40 as shown in the chart below.

~~~
Quality encoding: !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHI
                  |         |         |         |         |
Quality score:    0........10........20........30........40
~~~
{: .output}


Let us compare the quality of a reverse read from the _R2.fastq file

~~~
head -n 4 57971_1_PE_480bp_MM_AGRF_H5VJHBCX2_AAGAGGCA-GTAAGGAG_L001_R2.fastq
~~~
{: .bash}

~~~
@D00626:337:H5VJHBCX2:1:1106:1162:2154 2:N:0:AAGAGGCA+GTAAGGAG
GCTTCAAGTCATGATCGAAACTTTATTATTGAAGTCATGGGAAGAAGATCATCAGCGATTGCTCTGACAACTGNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNTNNNNNNNCNNNNNNNNANGATNNNN
+
@BADDIIIIIIIIIIIIIIIGHIIIIIIIHIII?GHIHIIIHIHIHHHHHIHGHHIIIIIIHIIIIIIHIGII################################-#######-########8#-8-####
~~~
{: .output}


Towards the 3' end of the read we can now see that there are many bases that are not called.
They are given a quality code  of ! or #, corresponding to a quality score of < 2. The end of this R2
read a poor quality, a characteristic of R2 sequences which are sequenced last of all during an illumina run.

> ## Exercise
>
> What is the last read in you `*R2.fq.` file? How confident
> are you in this read?
>
>> ## Solution
>> ~~~
>> $ tail -n4 *R2.fq
>> ~~~
>> {: .bash}
>>
>> ~~~
>> ~~~
>> @D00626:337:H5VJHBCX2:1:1106:1162:2154 2:N:0:AAGAGGCA+GTAAGGAG
>> GCTTCAAGTCATGATCGAAACTTTATTATTGAAGTCATGGGAAGAAGATCATCAGCGATTGCTCTGACAACTGGTGNAGATNTNNTNNNNCNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNTNNNNNNNCNNNNNNNNANGATNNNN
>> +
>> @BADDIIIIIIIIIIIIIIIGHIIIIIIIHIII?GHIHIIIHIHIHHHHHIHGHHIIIIIIHIIIIIIHIGIIIHI#<<E/#/##<####8#########################################-#######-########8#-8-####
>> ~~~
>> {: .output}
>>
>>
> {: .solution}
{: .challenge}

> ## Quality Encodings Vary
>
> Although we've used a particular quality encoding system to demonstrate interpretation of
> read quality, different sequencing machines use different encoding systems. This means that,
> depending on which sequencer you use to generate your data, a `#` may not be an indicator of
> a poor quality base call.

> This mainly relates to older Solexa/Illumina data,
> but it's essential that you know which sequencing platform was
> used to generate your data, so that you can tell your quality control program which encoding
> to use. If you choose the wrong encoding, you run the risk of throwing away good reads or
> (even worse) not throwing away bad reads!
{: .callout}

## Assessing Quality using FastQC
In real life, you won't be assessing the quality of your reads by visually inspecting your
FASTQ files. Rather, you'll be using a software program to assess read quality and
filter out poor quality reads. We'll first use a program called [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) to visualize the quality of our reads.
Later in our workflow, we'll use another program to filter out poor quality reads.

FastQC has a number of features which can give you a  quick impression of any problems your
data may have, so you can take these issues into consideration before moving forward with your
analyses. Rather than looking at quality scores for each individual read, FastQC looks at
quality collectively across all reads within a sample. The image below shows a FastQC-generated plot that indicates
a very high quality sample:

![good_quality](../fig/good_quality1.8.png)

The x-axis displays the base position in the read, and the y-axis shows quality scores. In this
example, the sample contains reads that are 40 bp long. For each position, there is a
box-and-whisker plot showing the distribution of quality scores for all reads at that position.
The horizontal red line indicates the median quality score and the yellow box shows the 2nd to
3rd quartile range. This means that 50% of reads have a quality score that falls within the
range of the yellow box at that position. The whiskers show the range to the 1st and 4th
quartile.

For each position in this sample, the quality values do not drop much lower than 32. This
is a high quality score. The plot background is also color-coded to identify good (green),
acceptable (yellow), and bad (red) quality scores.

Now let's take a look at a quality plot on the other end of the spectrum.

![bad_quality](../fig/bad_quality1.8.png)

Here, we see positions within the read in which the boxes span a much wider range. Also, quality scores drop quite low into the "bad" range, particularly on the tail end of the reads. The FastQC tool produces several other diagnostic plots to assess sample quality, in addition to the one plotted above.

## Running FastQC


~~~
$ mv ~/.dc_sampledata_lite/untrimmed_fastq/ ~/dc_workshop/data/
~~~
{: .bash}

Navigate to your FASTQ dataset:

~~~
$ cd
~~~
{: .bash}


~~~
$ fastqc *.fq
~~~
{: .bash}

You will see an automatically updating output message telling you the
progress of the analysis. It will start like something like this:

~~~
Started analysis of XXXX.fastq
Approx 5% complete for SRR097977.fastq
Approx 10% complete for SRR097977.fastq
Approx 15% complete for SRR097977.fastq
Approx 20% complete for SRR097977.fastq
Approx 25% complete for SRR097977.fastq
Approx 30% complete for SRR097977.fastq
Approx 35% complete for SRR097977.fastq
Approx 40% complete for SRR097977.fastq
Approx 45% complete for SRR097977.fastq
Approx 50% complete for SRR097977.fastq
~~~
{: .output}



The FastQC program has created several new files within our
`~/` directory.

~~~
$ ls
~~~
{: .bash}

~~~

~~~
{: .output}

For each input FASTQ file, FastQC has created a `.zip` file and a
`.html` file. The `.zip` file extension indicates that this is
actually a compressed set of multiple output files. We'll be working
with these output files soon. The `.html` file is a stable webpage
displaying the summary report for each of our samples.

We want to keep our data files and our results files separate, so we
will move these
output files into a new directory within our `results/` directory.

~~~
$ mkdir ~/dc_workshop/results/fastqc_untrimmed_reads
$ mv *.zip ~/dc_workshop/results/fastqc_untrimmed_reads/
$ mv *.html ~/dc_workshop/results/fastqc_untrimmed_reads/
~~~
{: .bash}

Now we can navigate into this results directory and do some closer
inspection of our output files.

~~~
$ cd ~/dc_workshop/results/fastqc_untrimmed_reads/
~~~
{: .bash}

<embed height="900" width="900" src="../fig/MAI20160210d0_R2_001_fastqc.html">

## Viewing HTML files

If we were working on our local computers, we'd be able to display each of these
HTML files as a webpage:

~~~
$ open SRR097977_fastqc.html
~~~
{: .bash}

However, if you try this on our sever, you'll get an error:

~~~
Couldn't get a file descriptor referring to the console
~~~
{: .output}

This is because we have not setup the requirements to export X11 windows from our server, and that is a topic for another day.
Instead, we want to look at the webpage summary reports on our own computer, so
let's transfer them to our local computers (i.e. your laptop).

If you are using mobaxterm the file transfer panel is on the left hand side of your mobaxterm console.


If you are using a mac then read on. To transfer a file from a remote server to our own machines, we will
use a variant of the `cp` command called `scp`. The `s` stands for
"secure" - this is a secure version of copying.

For the `cp` command, the syntax is:

~~~
$ cp my_file new_location
~~~
{: .bash}

The syntax for `scp` is the same, but now `my_file` and
`new_location` are on separate computers, so we need to give an
absolute path, including the name of our remote computer. First we
will make a new directory on our computer to store the HTML files
we're transfering. Let's put it on our desktop for now. Open a new
tab in your terminal program (you can use the pull down menu at the
top of your screen or the Cmd+t keyboard shortcut) and type:

~~~
$ mkdir ~/Desktop/fastqc_html
~~~
{: .bash}

Now we can transfer our HTML files to our local computer using `scp`.

~~~
$ scp user@jen.science.mq.edu.au:~/*.html ~/Desktop/fastqc_html
~~~
{: .bash}

This looks really complicated, so let's break it down. The first part
of the command `user@jen.science.mq.edu.au` is
the address for your remote computer. Make sure you replace your usernames
for `user`.

The second part starts with a `:` and then gives the absolute path
of the files you want to transfer from your remote computer. Don't
forget the `:`. We used a wildcard (`*.html`) to indicate that we want all of
the HTML files.

The third part of the command gives the absolute path of the location
you want to put the files. This is on your local computer and is the
directory we just created `~/Desktop/fastqc_html`.


The stutus of your transfer will be reported to the screen. Now we can go to our new directory and open the HTML files.

~~~
$ cd ~/Desktop/fastqc_html/
$ open *.html
~~~
{: .bash}

Your computer will open each of the HTML files in your default web
browser. Depending on how many fq files you analysed, this might be as a number of separate
tabs in a single window.

> ## Exercise
>
> Discuss your results with a neighbor. Which sample(s) looks the best
> in terms of per base sequence quality? Which sample(s) look the
> worst?
>
>> ## Solution
>>
> {: .solution}
{: .challenge}



The `.html` files and the uncompressed `.zip` files are still present,
but now we also have a new directory for each of our samples. We can
see for sure that it's a directory if we use the `-F` flag for `ls`.

~~~
$ ls -F
~~~
{: .bash}

~~~
SRR097977_fastqc/      SRR098027_fastqc/      SRR098281_fastqc/
SRR097977_fastqc.html  SRR098027_fastqc.html  SRR098281_fastqc.html
SRR097977_fastqc.zip   SRR098027_fastqc.zip   SRR098281_fastqc.zip
SRR098026_fastqc/      SRR098028_fastqc/      SRR098283_fastqc/
SRR098026_fastqc.html  SRR098028_fastqc.html  SRR098283_fastqc.html
SRR098026_fastqc.zip   SRR098028_fastqc.zip   SRR098283_fastqc.zip
~~~
{: .output}

Let's see what files are present within one of these output directories.

~~~
$ ls -F SRR097977_fastqc/
~~~
{: .bash}

~~~
fastqc_data.txt  fastqc.fo  fastqc_report.html	Icons/	Images/  summary.txt
~~~
{: .output}

Use `less` to preview the `summary.txt` file for this sample.


## Documenting Our Work

We can make a record of the results we obtained for all our samples
by concatenating all of our `summary.txt` files into a single file
using the `cat` command. We'll call this `full_report.txt` and move
it to `~/dc_workshop/docs`.

~~~
$ cat */summary.txt > ~/dc_workshop/docs/fastqc_summaries.txt
~~~
{: .bash}






