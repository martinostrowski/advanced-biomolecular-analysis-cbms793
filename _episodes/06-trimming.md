---
title: "Trimming and Filtering"
teaching: 30
exercises: 25
questions:
- "How can I get rid of sequence data that doesn't meet my quality standards?"
objectives:
- "Clean FASTQ reads using Trimmommatic."
- "Select and set multiple options for command-line bioinformatics tools."
- "Write `for` loops with two variables."
keypoints:
- "The options you set for the command-line tools you use are important!"
- "Data cleaning is an essential step in a genomics workflow."
---

# Cleaning Reads

In the previous episode, we took a high-level look at the quality
of each of our samples using FastQC. We vizualized per-base quality
graphs showing the distribution of read quality at each base across
all reads in a sample and extracted information about which samples
fail which quality checks. We know that all of our samples failed at
least one of the quality metrics used by FastQC. This doesn't mean,
though, that our samples should be thrown out! It's very common to
have some reads within a sample,
or some positions (near the begining or end of reads) across all
reads that are low
quality and should be discarded. We will use a program called
[Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) to
filter poor quality reads and trim poor quality bases from our samples.

## Trimmomatic Options

Trimmomatic is a program written in the Java programming language.
You don't need to learn Java to use Trimmomatic (FastQC is also
written in Java), but the fact that it's a Java program helps
explain the syntax that is used to run Trimmomatic. The basic
command to run Trimmomatic starts like this:

~~~
$ java -jar /usr/local/Trimmomatic-0.38/trimmomatic-0.38.jar
~~~
{: .bash}

`java` tells our computer that we're running a Java program. `-jar`
is an option specifying that we're going to specify the location of
the Java program we want to run. The Java program itself will have
a `.jar` file extension.

That's just the basic command, however. Trimmomatic has a variety of
options and parameters. We will need to specify what options we want
to use for our analysis. Here are some of the options:


| option    | meaning |
| ------- | ---------- |
| `-threads` | Specify the number of processors you want Trimmomatic to use. |
|  `SE` or `PE`   | Specify whether your reads are single or paired end. |
|  `-phred33` or `-phred64` | Specify the encoding system for your quality scores. |

In addition to these options, there are various trimming steps
available:

| step   | meaning |
| ------- | ---------- |
| `SLIDINGWINDOW` | Perform sliding window trimming, cutting once the average quality within the window falls below a threshold. |
| `LEADING`  | Cut bases off the start of a read, if below a threshold quality.  |
|  `TRAILING` |  Cut bases off the end of a read, if below a threshold quality. |
| `CROP`  |  Cut the read to a specified length. |
|  `HEADCROP` |  Cut the specified number of bases from the start of the read. |
| `MINLEN`  |  Drop an entire read if it is below a specified length. |
|  `TOPHRED33` | Convert quality scores to Phred-33.  |
|  `TOPHRED64` |  Convert quality scores to Phred-64. |

We will use only a few of these options and trimming steps in our
analysis. It is important to understand the steps you are using to
clean your data. For more information about the Trimmomatic arguments
and options, see [the Trimmomatic manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf).

We said above that a basic command for Trimmomatic looks like this:

~~~
java -jar /usr/local/Trimmomatic-0.38/trimmomatic-0.38.jar
~~~
{: .bash}


*For future classes we will start with decompressed files*

However, a complete command for Trimmomatic will look something like this:

~~~
java -jar /usr/local/Trimmomatic-0.38/trimmomatic-0.38.jar PE -threads 4 \
 35880_1_PE_700bp_MM_UNSW_HM35MBCXY_GCGTAGTA-GAGCCTTA_S8_L001_R1_001.fastq.gz 35880_1_PE_700bp_MM_UNSW_HM35MBCXY_GCGTAGTA-GAGCCTTA_S8_L001_R2_001.fastq.gz \
 -baseout MySample.Filtered.fq.gz \
 ILLUMINACLIP:/usr/local/Trimmomatic-0.38/adapters/NexteraPE-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
~~~
{: .bash}

In this example, we've told Trimmomatic:

| code   | meaning |
| ------- | ---------- |
| `PE` | that it will be taking a single end file as input |
| `-threads 4` | to use four computing threads to run (this will spead up our run) |
| `35880_1_PE_700bp_MM_UNSW_HM35MBCXY_GCGTAGTA-GAGCCTTA_S8_L001_R1_001.fastq.gz ` | the R1 input file name |
| `35880_1_PE_700bp_MM_UNSW_HM35MBCXY_GCGTAGTA-GAGCCTTA_S8_L001_R2_001.fastq.gz ` | the R1 input file name |
|  `35880_L001.trim.fastq.gz` | the base output file name to create |
| `ILLUMINACLIP:/usr/local/Trimmomatic-0.38/adapters/NexteraPE-PE.fa:2:30:10`| to clip the Illumina adapters from the input file using the adapter sequences listed in `NexteraPE-PE.fa` |
| `SLIDINGWINDOW:4:15` | to use a sliding window of size 4 that will remove bases if their phred score is below 20 |

## Running Trimmomatic

Now we will run Trimmomatic on our data. To begin, navigate to your metagenome data directory:


Notice that we needed to give the absolute path to our copy of the
Trimmomatic program.

~~~
$java -jar /usr/local/Trimmomatic-0.38/trimmomatic-0.38.jar PE -threads 12  35880_1_PE_700bp_MM_UNSW_HM35MBCXY_GCGTAGTA-GAGCCTTA_S8_L001_R1_001.fastq.gz 35880_1_PE_700bp_MM_UNSW_HM35MBCXY_GCGTAGTA-GAGCCTTA_S8_L001_R2_001.fastq.gz  -baseout ~/MySample.Filtered.fq.gz  ILLUMINACLIP:/usr/local/Trimmomatic-0.38/adapters/NexteraPE-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
TrimmomaticPE: Started with arguments:
 -threads 12 35880_1_PE_700bp_MM_UNSW_HM35MBCXY_GCGTAGTA-GAGCCTTA_S8_L001_R1_001.fastq.gz 35880_1_PE_700bp_MM_UNSW_HM35MBCXY_GCGTAGTA-GAGCCTTA_S8_L001_R2_001.fastq.gz -baseout /home/mostrowski/MySample.Filtered.fq.gz ILLUMINACLIP:/usr/local/Trimmomatic-0.38/adapters/NexteraPE-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
Using templated Output files: /home/mostrowski/MySample.Filtered_1P.fq.gz /home/mostrowski/MySample.Filtered_1U.fq.gz /home/mostrowski/MySample.Filtered_2P.fq.gz /home/mostrowski/MySample.Filtered_2U.fq.gz
Using PrefixPair: 'AGATGTGTATAAGAGACAG' and 'AGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'GTCTCGTGGGCTCGGAGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'TCGTCGGCAGCGTCAGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'CTGTCTCTTATACACATCTCCGAGCCCACGAGAC'
Using Long Clipping Sequence: 'CTGTCTCTTATACACATCTGACGCTGCCGACGA'
ILLUMINACLIP: Using 1 prefix pairs, 4 forward/reverse sequences, 0 forward only sequences, 0 reverse only sequences
Quality encoding detected as phred33


~~~
{: .output}

> ## Exercise
>
> Use the output from your Trimmomatic command to answer the
> following questions.
>
> 1) What percent of reads did we discard from our sample?
> 2) What percent of reads did we keep?
>
>> ## Solution
>> 1) 21.02%
>> 2) 78.98%
> {: .solution}
{: .challenge}

You may have noticed that Trimmomatic automatically detected the
quality encoding of our sample. It is always a good idea to
double-check this or to enter the quality encoding manually.

We can confirm that we have our output files:

~~~
$ ls MySample.Filtered*
~~~
{: .bash}

~~~
MySample.Filtered_1P.fq.gz  MySample.Filtered_2P.fq.gz  MySample.Filtered_1U.fq.gz  MySample.Filtered_2U.fq.gz
~~~
{: .output}

The output files are also compressed FASTQ files. It should be smaller than our
input file because we've removed reads. We can confirm this:

~~~
$ ls 35880* -l -h
$ ls MySample.Filtered* -l -h
~~~
{: .bash}

~~~

~~~
{: .output}


We've just successfully run Trimmomatic on our FASTQ files!

> ## Exercise
> Earlier we looked at the last read in our `_R2` file and likely
> saw that it was very poor quality at the end.
>
> ~~~
> $ head -n4 SRR098026.fastq
> ~~~
> {: .bash}
>
> ~~~
> @SRR098026.1 HWUSI-EAS1599_1:2:1:0:968 length=35
> NNNNNNNNNNNNNNNNCNNNNNNNNNNNNNNNNNN
> +SRR098026.1 HWUSI-EAS1599_1:2:1:0:968 length=35
> !!!!!!!!!!!!!!!!#!!!!!!!!!!!!!!!!!!
> ~~~
> {: .output}
>
> After filtering out bad reads, what is the first remaining read for
> this sample? What does its quality look like?
>
>> ## Solution
>> ~~~
>> $ head -n4 SRR098026.fastq_trim.fastq
>> ~~~
>> {: .bash}
>>
>> ~~~
>> @SRR098026.342 HWUSI-EAS1599_1:2:1:3:655 length=35
>> GGATNGGCCTTGTATTTATGATTCTCNGAGTCTGT
>> +SRR098026.342 HWUSI-EAS1599_1:2:1:3:655 length=35
>> BB@B!B@AACBBABCCCCBBBBBB@@!B?B<ABB@
>> ~~~
>> {: .output}
>>
>> Comparing this with our quality scale:
>>
>> ~~~
>> Quality encoding: !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHI
>>                   |         |         |         |         |
>> Quality score:    0........10........20........30........40
>> ~~~
>> {: .output}
>>
>> We can see that the scores are mostly in the 30+ range. This is
>> pretty good.
> {: .solution}
{: .challenge}

We've now completed the trimming and filtering steps of our quality
control process!
