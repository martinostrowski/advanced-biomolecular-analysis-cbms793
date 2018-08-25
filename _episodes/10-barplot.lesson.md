---
source: Rmd
title: "Barplot lesson"
teaching: 10
exercises: 20
questions:
- "This barplot example is workedin base R?"
objectives:
- "Use this exercise to gain experience formatting markdown for jekyll."
- "Describe the purpose and implementation of formative assessments."
keypoints:
- "Lessons are design in four stages: conceptual, summative, formative, and connective."
---


This is an [R Markdown](http://rmarkdown.rstudio.com) Notebook. When you execute code within the notebook, the results appear beneath the code.

Try executing this chunk by clicking the *Run* button within the chunk or by placing your cursor inside it and pressing *Cmd+Shift+Enter*.

`
plot(cars)
`

Add a new chunk by clicking the *Insert Chunk* button on the toolbar or by pressing *Cmd+Option+I*.

When you save the notebook, an HTML file containing the code and output will be saved alongside it (click the *Preview* button or press *Cmd+Shift+K* to preview the HTML file).

##Working with data and basic plotting in R:
###A species abundance barplot

The aims of this lesson are to read in a species abundance table, practice viewing and subsetting the data in different forms (i.e. as a vector, data frame and a matrix) , and produce an annotated, coloured barplot as a graphical representation of the data

Requirements for this lesson:

A working version of RStudio
Already completed athe Starting with data exercise (e.g. datacarpentry.org)

resources: The data is available on the following [dropbox](move to github) link
download the files x,y,z and place them in a directory called Rbarplot

###The Species data we will be plotting today

We are studying different genotypes of marine cyanobacteria that are abundant in the oceans. Distinct ecological types "ecotypes" have adapted to differnt ecological conditions and we can estimate their abundance using metagenome sequencing. The samples were collected along an oceanographic transect on a voyage from Darwin to Cairns in October 2012. The processes to generate the genotype abundance data are covered in detail [here](github link).



~~~
source("../bin/chunk-options.R")
~~~
{: .language-r}

The rest of the lesson should be written as a normal RMarkdown file. You can
include chunk for codes, just like you'd normally do.


1. Import the data

~~~
species.data<-read.table("~/Dropbox/Introduction_to_R/species.data.txt", h=T, row.names=1, sep='\t')
~~~
{: .language-r}

Q: what does the data look like?

In addition to the details presented in the environment window we can access information about the data in a number of ways


> ## Challenge: Can you do it?
>
> What is the output of this command?
>
>
> ~~~
> paste("This", "new", "template", "looks", "good")
> ~~~
> {: .language-r}
>
> > ## Solution
> >
> >
> > ~~~
> > [1] "This new template looks good"
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}


> ## Indentation of code within a for loop
> Note that it is common practice to indent the line(s) of code within a for loop.
> The only purpose is to make the code easier to read -- it is not required for the loop to run.
{: .callout}


Q:What does this tell us about the data?

Q: How many rows are in the data frame?

Q: How many columns are in the data frame?

```r
ncol(species.data)
```

```r
nrow(species.data)
```

we can send these details to a variable and use them later. How might this be useful?

The number of species in our table

```r
num.species<-nrow(species.data)
```

The number of samples in our table

```r
num.sites<-ncol(species.data)
```

Another method to display the structure and useful information about our data is the str function

```r
str(species.data)
```

Remember that help for a command or function can be accessed at any time

```r
?str
```


####Plotting the data

Lets try plotting the data using the barplot function

```r
barplot(species.data)
```
```r
barplot(as.matrix(species.data))
```

Looks pretty boring. Lets add some colour

```r
barplot(as.matrix(species.data), col=rainbow(num.species))
```
And fix the labels




```r
barplot(as.matrix(species.data), col=rainbow(num.species), las=2, cex.names=0.6)
```


### Extension
### Colouring the plot according to a pre-defined colour scheme

Whenever we publish new work on cyanobacterial ecotypes we would like to keep the colours consistent, for example the sub-tropical type IIa is coloured red and the temperate type Ia is dark blue.

We can import our colour scheme

Optional: Breakout and learn how to mix colours using RColorBrewer, or 'iwanthue' 10 min


```r
colcodes<-read.table("~/Dropbox/SS2012/genome_taxonomy_synpro.txt.txt", h=T, sep='\t', row.names=1)
```


