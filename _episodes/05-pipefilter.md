---
title: "Pipes and Filters"
teaching: 25
exercises: 10
questions:
- "How can I combine existing commands to do new things?"
objectives:
- "Redirect a command's output to a file."
- "Process a file instead of keyboard input using redirection."
- "Construct command pipelines with two or more stages."
- "Explain what usually happens if a program or pipeline isn't given any input to process."
- "Explain Unix's 'small pieces, loosely joined' philosophy."
keypoints:
- "`cat` displays the contents of its inputs."
- "`head` displays the first 10 lines of its input."
- "`tail` displays the last 10 lines of its input."
- "`sort` sorts its inputs."
- "`wc` counts lines, words, and characters in its inputs."
- "`command > file` redirects a command's output to a file."
- "`first | second` is a pipeline: the output of the first command is used as the input to the second."
- "The best way to use the shell is to use pipes to combine simple single-purpose programs (filters)."
---

Now that we know a few basic commands,
we can finally look at the shell's most powerful feature:
the ease with which it lets us combine existing programs in new ways.
We'll start with a directory called `genomes`
that contains five files describing a draft *Synechococcus* genome (Everyone's favourite cyanobacteria).
The `.f??` extension indicates that these files are fasta-formatted and contain, genome sequences, gene sequences or protein sequences.

~~~
$ ls genomes
~~~
{: .language-bash}

~~~
backup  HB1133_20170901.faa  HB1133_20170901.faa.v.crak.out  HB1133_20170901.ffn  HB1133_20170901.fna  HB1133_20170901.fsa  report
~~~
{: .output}

Let's go into that directory with `cd` and run the command `wc *.faa`.
`wc` is the "word count" command:
it counts the number of lines, words, and characters in files (from left to right, in that order).

The `*` in `*.faa` matches zero or more characters,
so the shell turns `*.faa` into a list of all `.faa` files in the current directory:

~~~
$ cd genomes
$ wc -l *.faa
~~~
{: .language-bash}

~~~
15787 HB1133_20170901.faa
~~~
{: .output}


But why do this one file at a time? We can use wild cards to process all of the fasta files at  once


If we run `wc -l *.f??` instead of just `wc -l *.faa`,
the output shows only the number of lines per file:

~~~
wc -l *.f??
~~~
{: .language-bash}

~~~
  15787 HB1133_20170901.faa
  39662 HB1133_20170901.ffn
  39548 HB1133_20170901.fna
  39548 HB1133_20170901.fsa
 134545 total
~~~
{: .output}

We can also use `-w` to get only the number of words,
or `-c` to get only the number of characters.

Which of these files is shortest?
It's an easy question to answer when there are only six files,
but what if there were 6000?
Our first step toward a solution is to run the command:

~~~
$ wc -l *.f?? > lengths.txt
~~~
{: .language-bash}

The greater than symbol, `>`, tells the shell to **redirect** the command's output
to a file instead of printing it to the screen. (This is why there is no screen output:
everything that `wc` would have printed has gone into the
file `lengths.txt` instead.)  The shell will create
the file if it doesn't exist. If the file exists, it will be
silently overwritten, which may lead to data loss and thus requires
some caution.
`ls lengths.txt` confirms that the file exists:

~~~
$ ls lengths.txt
~~~
{: .language-bash}

~~~
lengths.txt
~~~
{: .output}

We can now send the content of `lengths.txt` to the screen using `cat lengths.txt`.
`cat` stands for "concatenate":
it prints the contents of files one after another.
There's only one file in this case,
so `cat` just shows us what it contains:

~~~
$ cat lengths.txt
~~~
{: .language-bash}

~~~
  15787 HB1133_20170901.faa
  39662 HB1133_20170901.ffn
  39548 HB1133_20170901.fna
  39548 HB1133_20170901.fsa
 134545 total
~~~
{: .output}

> ## Output Page by Page
>
> We'll continue to use `cat` in this lesson, for convenience and consistency,
> but it has the disadvantage that it always dumps the whole file onto your screen.
> More useful in practice is the command `less`,
> which you use with `$ less lengths.txt`.
> This displays a screenful of the file, and then stops.
> You can go forward one screenful by pressing the spacebar,
> or back one by pressing `b`.  Press `q` to quit.
{: .callout}

Now let's use the `sort` command to sort its contents.

> ## What Does `sort -n` Do?
>
> If we run `sort` on a file containing the following lines:
>
> ~~~
> 10
> 2
> 19
> 22
> 6
> ~~~
> {: .source}
>
> the output is:
>
> ~~~
> 10
> 19
> 2
> 22
> 6
> ~~~
> {: .output}
>
> If we run `sort -n` on the same input, we get this instead:
>
> ~~~
> 2
> 6
> 10
> 19
> 22
> ~~~
> {: .output}
>
> Explain why `-n` has this effect.
>
> > ## Solution
> > The `-n` flag specifies a numeric sort, rather than alphabetical.
> {: .solution}
{: .challenge}

We will also use the `-n` flag to specify that the sort is
numerical instead of alphabetical.
This does *not* change the file;
instead, it sends the sorted result to the screen:

~~~
$ sort -n lengths.txt
~~~
{: .language-bash}

~~~
  15787 HB1133_20170901.faa
  39548 HB1133_20170901.fna
  39548 HB1133_20170901.fsa
  39662 HB1133_20170901.ffn
 134545 total
~~~
{: .output}

We can put the sorted list of lines in another temporary file called `sorted-lengths.txt`
by putting `> sorted-lengths.txt` after the command,
just as we used `> lengths.txt` to put the output of `wc` into `lengths.txt`.
Once we've done that,
we can run another command called `head` to get the first few lines in `sorted-lengths.txt`:

~~~
$ sort -n lengths.txt > sorted-lengths.txt
$ head -n 1 sorted-lengths.txt
~~~
{: .language-bash}

~~~
15787 HB1133_20170901.faa
~~~
{: .output}

Using `-n 1` with `head` tells it that
we only want the first line of the file;
`-n 20` would get the first 20,
and so on.
Since `sorted-lengths.txt` contains the lengths of our files ordered from least to greatest,
the output of `head` must be the file with the fewest lines.

> ## Redirecting to the same file
>
> It's a very bad idea to try redirecting
> the output of a command that operates on a file
> to the same file. For example:
>
> ~~~
> $ sort -n lengths.txt > lengths.txt
> ~~~
> {: .language-bash}
>
> Doing something like this may give you
> incorrect results and/or delete
> the contents of `lengths.txt`.
{: .callout}


If you think this is confusing,
you're in good company:
even once you understand what `wc`, `sort`, and `head` do,
all those intermediate files make it hard to follow what's going on.
We can make it easier to understand by running `sort` and `head` together:

~~~
$ sort -n lengths.txt | head -n 1
~~~
{: .language-bash}

~~~
15787 HB1133_20170901.faa
~~~
{: .output}

The vertical bar, `|`, between the two commands is called a **pipe**.
It tells the shell that we want to use
the output of the command on the left
as the input to the command on the right.
The computer might create a temporary file if it needs to,
or copy data from one program to the other in memory,
or something else entirely;
we don't have to know or care.

Nothing prevents us from chaining pipes consecutively.
That is, we can for example send the output of `wc` directly to `sort`,
and then the resulting output to `head`.
Thus we first use a pipe to send the output of `wc` to `sort`:

~~~
$ wc -l *.f?? | sort -n
~~~
{: .language-bash}

~~~
  15787 HB1133_20170901.faa
  39548 HB1133_20170901.fna
  39548 HB1133_20170901.fsa
  39662 HB1133_20170901.ffn
 134545 total
~~~
{: .output}

And now we send the output of this pipe, through another pipe, to `head`, so that the full pipeline becomes:

~~~
$ wc -l *.f?? | sort -n | head -n 1
~~~
{: .language-bash}

~~~
   15787 HB1133_20170901.faa
~~~
{: .output}

This is exactly like a mathematician nesting functions like *log(3x)*
and saying "the log of three times *x*".
In our case,
the calculation is "head of sort of line count of `*.f??`".


Let's do something useful with these commands, like count the number of sequences in each file.
To do this we need one more trick `grep`, a pattern matching function. Each sequence in a fasta file is composed of a header denoted by a `>` at the start of the line, followed the the sequence on a new line.
We can use `grep` to find only the header lines in a fasta file.

~~~
$ grep '>' HB1133_20170901.fna
~~~
{: .language-bash}

~~~
>Syn_HB1133_58
>Syn_HB1133_7
>Syn_HB1133_3
>Syn_HB1133_11
>Syn_HB1133_18
>Syn_HB1133_54
>Syn_HB1133_198
>Syn_HB1133_68
>Syn_HB1133_177
>Syn_HB1133_174

~~~
{: .output}

We can also add an option flag to `-c` to the command that will count the number of occurrences

~~~
$ grep -c '>' HB1133_20170901.fna
~~~
{: .language-bash}

~~~
10

~~~
{: .output}

> ## Retrieving counts from multiple files
>
> In our current directory, we want to find the number of sequences in each of the fasta files
>
>
> > ## Solution
> > The solution is to use a wildcard
> >
> > grep -c '>' HB1133_20170901.f??
> >
> {: .solution}
{: .challenge}




> ## Piping Commands Together
>
> In our current directory, we want to find the 3 files which have the least number of
> seqences. Which command listed below would work?
>
> 1. `grep -c '>' HB1133_20170901.f?? > sort -n > head -n 3`
> 2. `grep -c '>' HB1133_20170901.f?? | sort -n | head -n 1-3`
> 3. `grep -c '>' HB1133_20170901.f?? | head -n 3 | sort -n`
> 4. `grep -c '>' HB1133_20170901.f?? | sort -n | head -n 3`
>
> > ## Solution
> > Option 4 is the solution.
> > The pipe character `|` is used to feed the standard output from one process to
> > the standard input of another.
> > `>` is used to redirect standard output to a file.
> {: .solution}
{: .challenge}

Here's what actually happens behind the scenes when we create a pipe.
When a computer runs a program --- any program --- it creates a **process**
in memory to hold the program's software and its current state.
Every process has an input channel called **standard input**.
(By this point, you may be surprised that the name is so memorable, but don't worry:
most Unix programmers call it "stdin").
Every process also has a default output channel called **standard output**
(or "stdout"). A second output channel called **standard error** (stderr) also
exists. This channel is typically used for error or diagnostic messages, and it
allows a user to pipe the output of one program into another while still receiving
error messages in the terminal.

The shell is actually just another program.
Under normal circumstances,
whatever we type on the keyboard is sent to the shell on its standard input,
and whatever it produces on standard output is displayed on our screen.
When we tell the shell to run a program,
it creates a new process
and temporarily sends whatever we type on our keyboard to that process's standard input,
and whatever the process sends to standard output to the screen.

Here's what happens when we run `wc -l *.fna > lengths.txt`.
The shell starts by telling the computer to create a new process to run the `wc` program.
Since we've provided some filenames as arguments,
`wc` reads from them instead of from standard input.
And since we've used `>` to redirect output to a file,
the shell connects the process's standard output to that file.

If we run `wc -l *.fna | sort -n` instead,
the shell creates two processes
(one for each process in the pipe)
so that `wc` and `sort` run simultaneously.
The standard output of `wc` is fed directly to the standard input of `sort`;
since there's no redirection with `>`,
`sort`'s output goes to the screen.
And if we run `wc -l *.fna | sort -n | head -n 1`,
we get three processes with data flowing from the files,
through `wc` to `sort`,
and from `sort` through `head` to the screen.

![Redirects and Pipes](../fig/redirects-and-pipes.png)

This simple idea is why Unix has been so successful.
Instead of creating enormous programs that try to do many different things,
Unix programmers focus on creating lots of simple tools that each do one job well,
and that work well with each other.
This programming model is called "pipes and filters".
We've already seen pipes;
a **filter** is a program like `wc` or `sort`
that transforms a stream of input into a stream of output.
Almost all of the standard Unix tools can work this way:
unless told to do otherwise,
they read from standard input,
do something with what they've read,
and write to standard output.

The key is that any program that reads lines of text from standard input
and writes lines of text to standard output
can be combined with every other program that behaves this way as well.
You can *and should* write your programs this way
so that you and other people can put those programs into pipes to multiply their power.

> ## Redirecting Input
>
> As well as using `>` to redirect a program's output, we can use `<` to
> redirect its input, i.e., to read from a file instead of from standard
> input. For example, instead of writing `wc HB1133_20170901.fna`, we could write
> `wc < HB1133_20170901.fna`. In the first case, `wc` gets a command line
> argument telling it what file to open. In the second, `wc` doesn't have
> any command line arguments, so it reads from standard input, but we
> have told the shell to send the contents of `HB1133_20170901.fna` to `wc`'s
> standard input.
{: .callout}



> ## Wildcard Expressions
>
> Wildcard expressions can be very complex, but you can sometimes write
> them in ways that only use simple syntax, at the expense of being a bit
> more verbose.
> Consider the directory `genomes/` :
> the wildcard expression `*.f[sfa]a`
> matches all files ending in `.fna` or `.faa` or `.fsa`. Imagine you forgot about
> this.
>
{: .challenge}

> ## Removing Unneeded Files
>
> Suppose you want to delete your processed data files, and only keep
> your raw files and processing script to save storage.
> The raw files end in `.dat` and the processed files end in `.txt`.
> Which of the following would remove all the processed data files,
> and *only* the processed data files?
>
> 1. `rm ?.txt`
> 2. `rm *.txt`
> 3. `rm * .txt`
> 4. `rm *.*`
>
> > ## Solution
> > 1. This would remove `.txt` files with one-character names
> > 2. This is correct answer
> > 3. The shell would expand `*` to match everything in the current directory,
> > so the command would try to remove all matched files and an additional
> > file called `.txt`
> > 4. The shell would expand `*.*` to match all files with any extension,
> > so this command would delete all files
> {: .solution}
{: .challenge}
