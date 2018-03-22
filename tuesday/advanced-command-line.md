Advanced Command-Line
=======================

**1\.** Let's take a closer look at the 'sed' command. sed is a command that allows you to manipulate character data in various ways. One useful thing it can do is substitution. First, make a directory called "advanced" in your home directory and go into it.

    cd
    mkdir advanced
    cd advanced

Let's copy over a simple file to work on:

    cp /usr/share/common-licenses/BSD .

Take a look at the file:

    cat BSD

Now, let's change every occurence of the word "Redistribution" into "Mangling":

    cat BSD | sed 's/Redistribution/Mangling/gi'

Let's break down the argument to sed (within the single quotes)... The "s" means "substitute", the word between the 1st and 2nd forward slashes (i.e. /) is the word the substitute for, the word between the 2nd and 3rd slashes is the word to substitute with, and finally the "gi" at the end are flags for global substitution (i.e. substituting along an entire line instead of just the first occurence on a line), and for case insenstivity (i.e. it will ignore the case of the letters when doing the substitution).

Note that this **doesn't** change the file itself, it is simply piping the output of the cat command to sed and outputting to the screen. If you wanted to change the file itself, you could use the "-i" option to sed:

    cat BSD
    sed -i 's/Redistribution/Mangling/gi' BSD

Now if you look at the file, the lines have changed.

    cat BSD

Another useful use of sed is for capturing certain lines from a file. You can select certain lines from a file:

    sed '4q;d' BSD

This will just select the 4th line from the file.

---

**2\.** Now, let's delve into pipes a little more. Pipes are a very powerful way to look at and manipulate complex data using a series of simple programs. First take a look at the contents of the "/home" directory:

    ls /home

These are all the home directories on the system. Now let's say we wanted to find out how many directory names begin with each letter. First we cut out the first letter of the directories:

    ls /home | cut -c1

In order to do the counting, we first need to sort the data and then send it to the "uniq" command to keep only the unique occurences of a letter. The "-c" option counts up the number of occurences:

    ls /home | cut -c1 | sort | uniq -c

You'll notice there might be a letter missing... why would that be?

Now let's look at some fastq files. Link a few files into the advanced directory:

    ln -s /share/biocore-archive/Leveau_J_UCD/RNASeq_Arabidopsis_2016/00-RawData/C61_S67_L006/C61_S67_L006_R*_001.fastq.gz .
    
Since the files are gzipped files we need to use "zcat" to look at them. zcat is just like cat except for gzipped files:

    zcat C61_S67_L006_R1_001.fastq.gz | head

Notice that each header line has the barcode for that read at the end of the line. Let's count the number of each barcode. In order to do that we need to just capture the header lines from this file. We can use "sed" to do that:

    zcat C61_S67_L006_R1_001.fastq.gz | sed -n '1~4p' | head

By default sed prints every line. In this case we are giving the "-n" option to sed which will **not** print every line. Instead, we are giving it the argument "1~4p", which means to print the first line, then skip 4 lines and print again, and then continue to do that.

Now that we have a way to get just the headers, we need to isolate the part of the header that is the barcode. There are multiple ways to do this... we will use the cut command:

    zcat C61_S67_L006_R1_001.fastq.gz | sed -n '1~4p' | cut -d: -f10 | head

So we are using the "-d" option to cut with ":" as the argument to that option, meaning that we will be using the delimiter ":" to split the input. Then we use the "-f" option with argument "10", meaning that we want the 10th field after the split. In this case, that is the barcode.

Finally, as before, we need to sort the data and then use "uniq -c" to count. Then put it all together and run it on the entire dataset (This will take about a minute to run):

    zcat C61_S67_L006_R1_001.fastq.gz | sed -n '1~4p' | cut -d: -f10 | sort | uniq -c

Now you have a list of how many reads were categorized into each barcode. Here is a [sed tutorial](https://www.digitalocean.com/community/tutorials/the-basics-of-using-the-sed-stream-editor-to-manipulate-text-in-linux) for more exercises.

---

**3\.** Next, we will cover process substitution. Process substitution is a way of using the output of some software as the input file to another software without having to create intermediate files. Let's use the sickle program we compiled earlier (and should already be in our PATH). We want to do adapter trimming on one of our fastq.gz files, but we need to give sickle an uncompressed file as input. In order to do that, we use the "gunzip" command with the "-c" option. This unzips the file and sends the output to STDOUT, instead of unzipping the file in place which is the default (This will take a few minutes to run):

    sickle se -f <(gunzip -c C61_S67_L006_R1_001.fastq.gz) -t sanger -o trimmed.fa

So we are putting the gunzip command inside parentheses with a less-than symbol like so: <(COMMAND). When we do this, the output of the COMMAND gets manipulated by the shell so that sickle thinks it is a file. Sickle then uses this "file" as the input file. Take a look at the output file:

    less trimmed.fa

---

**4\.** for loops

    for x in /share/biocore-archive/Leveau_J_UCD/RNASeq_Arabidopsis_2016/00-RawData/*/*; do basename $x; done
    for x in /share/biocore-archive/Leveau_J_UCD/RNASeq_Arabidopsis_2016/00-RawData/*/*; do NAME=`basename $x`; echo $NAME is a file; done

Loops
-------

Loops are useful for quickly telling the shell to perform one operation after another, in series. For example:

    for i in {1..21}; do echo $i >> a; done  # put multiple lines of code on one line, each line terminated by ';'
    cat a
    # <1 through 21 on separate lines>

The general form is:

    for name in {list}; do
        commands
    done

The list can be a sequence of numbers or letters, or a group of files specified with wildcard characters:

    for i in {3,2,1,liftoff}; do echo $i; done  # needs more excitement!
    for i in {3,2,1,"liftoff!"}; do echo $i; done  # exclamation point will confuse the shell unless quoted
    # Now imagine you have 20 sequence files, in a 'fastqs' directory:
    bwa index reference.fa
    for sample in fastqs/*.fastq; do
        bwa mem reference.fa $sample 1> $sample.sam 2> $sample.err
    done
    # this would produce, for example, ./fastqs/sample1.fastq.sam and ./fastqs/sample1.fastq.err, etc.

Sometimes a "while" loop is more convenient than a "for" loop ... if you don't readily know how many iterations of the loop you want:

    while {condition}; do
        commands
    done

Or, imagining a file that contains the filenames (one per line) of samples' sequence data:

    cat file-of-filenames.txt | while read sample; do
        bwa mem reference.fa $sample 1> $sample.sam 2> $sample.err
    done

---

**5\.** find

    find /share/biocore/joshi/projects/genomes -name "*.fa"
    find /share/biocore/joshi/projects/genomes -name "*.fa" -exec ls -l {} \;

---

**6\.** xargs

    find /share/biocore/joshi/projects/genomes -name "*.fa" | xargs ls -lh

---

**7\.** Installing software and git

    git clone https://github.com/najoshi/sickle.git
    cd sickle
    cat Makefile
    make

Now you need to edit your PATH.

---

**8\.** .bashrc/.bash_profile and aliases setup & the PATH variable

MORE BASH
MORE GREP
AWK
PROMPTS
nohup
using "-" for input file in pipes

---

**9\.** Intro to perl

---

**10\.** Intro to mysql

    wget https://ucdavis-bioinformatics-training.github.io/2017-June-RNA-Seq-Workshop/monday/db.sqlite3
    sqlite3 db.sqlite3

---

**11\.** Intro to python


