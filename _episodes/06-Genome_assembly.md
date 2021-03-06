---
source: Rmd
title: "Genome Assembly Workflow"
teaching: 10
exercises: 40
questions:
- "How to quality control raw sequence files for genome assembly"
objectives:
- "Describe in detail the quality measures of your raw sequences"
- "Explain the key points in the genome assembly workflow"
- "Demonstrate how to include pieces of code, figures, and challenges"
keypoints:
- "Garbage in, Garbage out! Focus on quality, and methods to improve the input data"
---


## Metagenome assembly, illumina Paired end sequences

In your directory you will find two, or more, sequence files `.fq.gz` in *fastq* format. We are going
to discuss what is in each of these files, begin to understand how they are formatted and explore different ways to quality check them.

The following notes are not intended to be a complete workflow. Where necessary you will have to fill the gaps `xxx` using
relevant filenames and tools you have already learned, or by doing some research on the web.

> ## Questions:
>
> What basic bash commands can you use to determine the size, and number of sequences in your fastq files?
>
>
> {: .language-bash}
>
> > ## Solution
> > ls -la
> >
> > ~~~
> > ls -la
> >
> >  gunzip *
> >
> >  less xxx
> >
> > fastqc xxx
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}



Explore the fastqc output

> ## Questions:
>
> How many sequences are in your files?
>
> How does the quality profile of the R1 and R2 reads compare?
>
> Do your sequences need to be trimmed because the quality issues or leftover adaptor sequences?
>
> What options do you have for viewing the output of the fastqc analyses?
>
{: .challenge}

If your sequences requires filtering we are going to use the program trimmomatic using the following code
1. trim R1 and R2 using trimmomatic, edit the input and output names to something rational

~~~
java -jar /usr/local/Trimmomatic-0.38/trimmomatic-0.38.jar PE -threads 4 YON20160503d0_R1_001.fq.gz YON20160503d0_R2_001.fq.gz -baseout mySamplefiltered.fq.gz ILLUMINACLIP:/usr/local/Trimmomatic-0.38/adapters/NexteraPE-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
~~~
{: .language-bash}

> ## Questions:
>
> How does the quantity and quality of the sequences in your filtered files compare to your raw sequence files?
>
{: .challenge}

Next, and final, task for the day is to start the metagenome assembly using a popular assembly program (spades or megahit)

It is likely that this will take sometime so we will need to setup a special terminal session that will not be interrupted when you log out.
We will use the program screen and give this session a name

2. Join the two unpaired reads files and start the spades assembly (edit the names)

~~~
screen -S Assembly

cat mySamplefiltered_*U.fq.gz > mySamplefiltered_12U.fq.gz

/usr/local/megahit/megahit -1 mySamplefiltered_1P.fq.gz -2 mySamplefiltered_2P.fq.gz -r mySamplefiltered_12U.fq.gz -t 4 -o assembly
~~~
{: .language-bash}

##That is all for this week

Produce a summary of results using quast

~~~
/usr/local/quast/quast-4.6.3/quast.py contigs.fa
~~~
{: .language-bash}

> ## Questions:
>
> How does the total size of your input data compare to size of your assembly?
>
> How long is the longest contig?
>
> Which contig is assembled from the greatest number of input sequences?
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



3. read mapping, prepare for binning and coverage assessment


~~~
bowtie2-build final.contigs.fa scaffolds

bowtie2 -x contigs -1 ../mySamplefiltered_1P.fq.gz -2 ../AmySamplefiltered_1P.fq.gz -U ../mySamplefiltered_12U.fq.gz -S contigs.reads.mapped.sam
~~~
{: .language-bash}

convert sam to bam file for MetaBat # this needs to be fixed

~~~
samtools view -bS contigs.reads.mapped.sam | samtools sort - contigs.sorted
~~~
{: .language-bash}


4. MetaBat binning


~~~
/usr/local/anaconda2/bin/runMetaBat.sh -t 4 final.contigs.fa contigs.sorted.bam
~~~
{: .language-bash}

mv bins to bins directory !!


and change its name so that we are all on the same page

~~~
mv xxxxxxxxx metabat.bins

~~~
{: .language-bash}


Analyse bins using CheckM.

~~~
checkm lineage_wf -f metabat.bins/checkm.txt -t 4 -x fa metabat.bins/ metabat.bins/scg
~~~
{: .language-bash}

When this analysis is done open and explore the results

~~~
nano metabat.bins/checkm.txt
~~~
{: .language-bash}





