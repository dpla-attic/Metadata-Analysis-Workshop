# Command Line Basics, DPLAFest 2017 DPLA Metadata Analysis Workshop

*1:45-2:15 PM, led by [Audrey Altman, DPLA Developer](mailto:audrey@dp.la)*

## Goals

- \*nix Shell & Bash Introduction
- Navigating & Working with Files & Directories
- Pipes & Filters
- Finding Things
- Redirect Input & Output

## Preparation

Either using Git, curl, the GitHub interface or thumbdrives passed around before the session, download this Workshop GitHub Repository to your computer.

Also have the [Bash Exercises](Bash_Exercises.md) handy - that is the primary document you'll be working with.

## Possible Lesson Questions/Topics:

Nota bene: (Not All of this will be Covered; Treat this as a Reference)

**Table of Contents**

* [Preparation](#preparation-1)
* [Shell & Bash Intro](#nix-shell--bash-introduction)
    * [Basic Structure of a Command](#basic-structure-of-a-command-command-optionsflags-arguments)
    * [How to get help understanding a Command](#how-to-get-help-understanding-a-command)
    * [Shell tricks](#shell-tricks)
* [Moving around files & directories](#moving-around-files--directories)
    * [pwd](#pwd)
    * [ls, ls -A, ls -R, ls -alsh](#ls-ls--a-ls--r-ls--alsh)
    * [cd, cd /, cd ./wherever, cd ../.., cd ~](#cd-cd--cd-wherever-cd--cd-)
    * [mkdir, mkdir -p](#mkdir-mkdir--p)
    * [rmdir, rmdir -p, rm, rm -rf](#rmdir-rmdir--p-rm-rm--rf)
    * [cp, cp -r, mv](#cp-cp--r-mv)
* [Finding & Opening Things](#finding--opening-things)
    * [find, find -name -type, find -iname, find -not](#find-find--name--type-find--iname-find--not)
    * [nano, vi, open](#nano-vi-open)
    * [grep, grep -c, grep -o, grep -i, grep -oP, grep -v](#grep-grep--c-grep--o-grep--i-grep--op-grep--v)
* [Other Commands](#other-commands)
    * [wc, wc -l, wc -w, wc -m](#wc-wc--l-wc--w-wc--m)
    * [sort, sort -f, sort -n, sort -r, sort -c](#sort-sort--f-sort--n-sort--r-sort--c)
    * [uniq, uniq -c, uniq -u, uniq -d](#uniq-uniq--c-uniq--u-uniq--d)
* [Pipelines & pipes](#pipelines--pipes)
* [Handling input and output from files (i.e. <, >, >>)](#handling-input-and-output-from-files-ie---)
* [Further Resources](#further-resources)

### Preparation

Quick grab of the Workshop Repo (through whatever method), then unzip as needed.

### \*nix Shell & Bash Introduction

What is a command-line interface (CLI)? Where does bash interact with this? What is bash?

> "In this session we will introduce programming by looking at how data can be manipulated, counted, and mined using the Unix shell, a command line interface to your computer and the files to which it has access.

> The shell is one of the most productive programming environments ever created. Its syntax may be cryptic, but people who have mastered it can experiment with different commands interactively, then use what they have learned to automate their work. Command Line Interfaces like shells and tools were the primary utilities for interaction with computer systems and programs until the introduction of the Graphical User Interfaces. For many tasks shell and the command line can still excel GUI programs, however: particularly for process very large datasets and files.

> A Unix shell is a command-line interpreter that provides a user interface for the Linux operating system and for Unix-like systems (such as Mac OS).

> For Windows users, popular shells such as Cygwin or Git Bash provide a Unix-like interface. This session will cover a small number of basic commands using Git Bash for Windows users, Terminal for Mac OS. These commands constitute building blocks upon which more complex commands can be constructed to fit your data or project.

> What you can quickly learn is how to query lots of data for the information you want super fast. Using Bash or any other shell sometimes feels more like programming than like using a mouse. Commands are terse (often only a couple of characters long), their names are frequently cryptic, and their output is lines of text rather than something visual like a graph. On the other hand, with only a few keystrokes, the shell allows us to combine existing tools into powerful pipelines and handle large volumes of data automatically. This automation not only makes us more productive, but also improves the reproducibility of our workflows by allowing us to repeat them with few simple commands. Also, understanding the basics of the shell is very useful as a foundation before learning to program, since most programming languages necessitate working with the shell." (abridged from Library Carpentry)

#### Basic Structure of a Command

> "Let's start by opening the shell. This likely results in seeing a black window with a cursor flashing next to a dollar sign. This is our command line, and the $ is the command prompt to show the system is ready for our input. The prompt can look somewhat different from system to system, but it usually ends with a $. The $ sign is used to indicate a command to be typed on the command prompt, but we never type the $ sign itself, just what follows after it." (adapted from In the Beginning ... There was the Command Line)

```bash
$ command --option(s) argument(s)
# long options
$ ls --all ~/
# short options
$ ls -a ~/
```

#### Get Help Understanding a Command

Use the `man` command to invoke the manual page (documentation) for a shell command.

```bash
$ man ls
LS(1)                     BSD General Commands Manual                    LS(1)
NAME
     ls -- list directory contents
SYNOPSIS
     ls [-ABCFGHLOPRSTUW@abcdefghiklmnopqrstuwx1] [file ...]
DESCRIPTION
     For each operand that names a file of a type other than directory, ls displays its name as well as any requested, associated
     information.  For each operand that names a file of type directory, ls displays the names of files contained within that direc-
     tory, as well as any requested, associated information.
     If no operands are given, the contents of the current directory are displayed.  If more than one operand is given, non-directory
     operands are displayed first; directory and non-directory operands are sorted separately and in lexicographical order.
     The following options are available:
     -@      Display extended attribute keys and sizes in long (-l) output.
     -1      (The numeric digit ``one''.)  Force output to be one entry per line.  This is the default when output is not to a termi-
             nal.
     -A      List all entries except for . and ...  Always set for the super-user.
# more text
```

For example, the above example displays all the flags/options available to you. Use the spacebar to navigate the manual pages, and `q` to quit this man output.

**Nota bene**: this command is for Mac and Linux users only. It does not work directly for Windows users. If you use windows, you can search for the Shell command on http://man.he.net/, and view the associated manual page. You can also use the help switch `--help` after a command to display the help documentation. e.g. `ls --help`

#### Shell tricks

- **tab autocomplete**: Hitting tab at any time within the shell will prompt it to attempt to auto-complete the line based on the files or sub-directories in the current directory. Where two or more files have the same characters, the auto-complete will only fill up to the first point of difference, after which we can add more characters, and try using tab again.
- **up + down arrows for previous commands**: On a blank command prompt, hit the up arrow key and notice that the previous command you typed appears before your cursor. We can continue pressing the up arrow to cycle through your previous commands.
- `history`: Use the `history` command to see a list of all the commands you've entered during the current session. Use the space bar to navigate through pages, and `q` to quit.
- **Wildcards**: Luckily the shell supports wildcards! The ? (matches exactly one character) and * (matches zero or more characters) are probably familiar from library search systems. We can use the * wildcard to write the commands in a more compact way.

### Moving around files & directories:

#### Print Working Directory `pwd`

When working in the shell, you are always somewhere in the computer's file system, in some folder (directory). Find out where you are by using the `pwd` command, which you can use whenever you are unsure. It stands for 'print working directory' and the result of the command is printed to your standard output, which is the terminal.

Example: type `pwd` and hit enter to execute the command:

```bash
$ pwd
/Users/Christina
```

#### List Contents `ls`

For example, if you just opened your bash shell and haven't changed your directory yet, use the `ls` command to list the default space - your home directory:

```bash
$ ls
Applications             Downloads                Music                    Public                   __MACOSX                 perl5
Applications (Parallels) Dropbox                  Pictures                 Sites                    marcedit                 test.json
Desktop                  Library                  Presentations            Tools                    mashcat
Documents                Movies                   Projects                 VirtualBox VMs           nifi-0.7.0
```

If you want more information than just a list of files and directories, you can use various flags (also known as options or switches) to go with our basic commands.

If we type `ls -l` and hit enter, the computer returns a list of files that contains information similar to what we would find in our Finder (Mac) or Explorer (Windows): the size of the files in bytes, the date it was created or last modified, and the file name.

```bash
$ ls -l
total 24
drwxr-xr-x    10 Christina  staff    340 Jan 26 20:32 Applications
drwxr-xr-x@    5 Christina  staff    170 Jan 20 17:37 Applications (Parallels)
drwx------+    4 Christina  staff    136 Feb  3 10:46 Desktop
drwx------+   39 Christina  staff   1326 Feb 14 14:34 Documents
drwx------+   88 Christina  staff   2992 Feb 20 06:02 Downloads
drwx------@    8 Christina  staff    272 Feb 13 09:56 Dropbox
drwx------@   73 Christina  staff   2482 Jan  2 16:21 Library
drwx------+    5 Christina  staff    170 Dec 21  2015 Movies
drwx------+    6 Christina  staff    204 Mar  7  2016 Music
drwx------+ 1620 Christina  staff  55080 Aug 17  2016 Pictures
drwxr-xr-x     7 Christina  staff    238 May 29  2016 Presentations
drwxr-xr-x    44 Christina  staff   1496 Feb  3 10:58 Projects
drwxr-xr-x+    5 Christina  staff    170 Dec 17  2015 Public
drwxr-xr-x    14 Christina  staff    476 Feb 13 11:02 Sites
drwxr-xr-x@   45 Christina  staff   1530 Feb  7 17:06 Tools
drwx------     7 Christina  staff    238 Oct 12 11:21 VirtualBox VMs
drwxr-xr-x     4 Christina  staff    136 Mar  1  2016 __MACOSX
drwxr-xr-x    18 Christina  staff    612 Mar  7  2016 marcedit
drwxr-xr-x     3 Christina  staff    102 Jan 26  2016 mashcat
drwxr-xr-x@   20 Christina  staff    680 Nov  8 11:20 nifi-0.7.0
drwxr-xr-x     6 Christina  staff    204 Jun  6  2016 perl5
-rw-r--r--     2 Christina  staff   8232 Sep 30 12:05 test.json
```

In everyday usage we are more used to units of measurement like kilobytes, megabytes, and gigabytes. Luckily, there's another flag -h that when used with the -l option, use unit suffixes: Byte, Kilobyte, Megabyte, Gigabyte, Terabyte and Petabyte in order to reduce the number of digits to three or less using base 2 for sizes.

Now `ls -h` won't work on its own. When we want to combine two flags, we can just run them together. So, by typing `ls -lh` and hitting enter we receive an output in a human-readable format (note: the order of the flags here doesn't matter).

```bash
$ ls -lh
total 24
drwxr-xr-x    10 Christina  staff   340B Jan 26 20:32 Applications
drwxr-xr-x@    5 Christina  staff   170B Jan 20 17:37 Applications (Parallels)
drwx------+    4 Christina  staff   136B Feb  3 10:46 Desktop
drwx------+   39 Christina  staff   1.3K Feb 14 14:34 Documents
drwx------+   88 Christina  staff   2.9K Feb 20 06:02 Downloads
drwx------@    8 Christina  staff   272B Feb 13 09:56 Dropbox
drwx------@   73 Christina  staff   2.4K Jan  2 16:21 Library
drwx------+    5 Christina  staff   170B Dec 21  2015 Movies
drwx------+    6 Christina  staff   204B Mar  7  2016 Music
drwx------+ 1620 Christina  staff    54K Aug 17  2016 Pictures
drwxr-xr-x     7 Christina  staff   238B May 29  2016 Presentations
drwxr-xr-x    44 Christina  staff   1.5K Feb  3 10:58 Projects
drwxr-xr-x+    5 Christina  staff   170B Dec 17  2015 Public
drwxr-xr-x    14 Christina  staff   476B Feb 13 11:02 Sites
drwxr-xr-x@   45 Christina  staff   1.5K Feb  7 17:06 Tools
drwx------     7 Christina  staff   238B Oct 12 11:21 VirtualBox VMs
drwxr-xr-x     4 Christina  staff   136B Mar  1  2016 __MACOSX
drwxr-xr-x    18 Christina  staff   612B Mar  7  2016 marcedit
drwxr-xr-x     3 Christina  staff   102B Jan 26  2016 mashcat
drwxr-xr-x@   20 Christina  staff   680B Nov  8 11:20 nifi-0.7.0
drwxr-xr-x     6 Christina  staff   204B Jun  6  2016 perl5
-rw-r--r--     2 Christina  staff   8.0K Sep 30 12:05 test.json
```

#### Change Directory `cd`

To move from your current directory (if you've not run anything else in the exercises above, you'll still be in your home directory) you can use the `cd` or Change Directory command:

(Note: On Windows and Mac, by default, the case of the file/directory doesn't matter. On Linux it does.)

```bash
$ cd Desktop
```

Notice that the command didn't output anything. This means that it was carried out successfully. Let's check by using `pwd`:

```bash
$ pwd
/Users/Christina/Desktop
```

If something had gone wrong, however, the command will tell you. Let's see by trying to move into a (we hope) non-existing directory:

```bash
$ cd "bibframe plan"
cd: The directory 'bibframe plan' does not exist
```

Notice that we surrounded the name by quotation marks. The arguments given to any Bash shell command are separated by spaces, so a way to let them know that we mean 'one single thing called "bibframe plan"', not that we mean two different things ('bibframe' and 'plan'), is to use (single or double) quotation marks.

We've now seen how we can do 'down' through our directory structure (as in into more nested directories). If we want to go back, we can type `cd ..` . This moves us 'up' one directory, putting us back where we started. If we ever get completely lost, the command `cd` without any arguments will bring us right back to the home directory.

```bash
$ cd ..
```

If at some point you want to open where you are in your Bash shell in a GUI finder, on Mac or Linux, you can type `open .` (the single dot means your current location). On Windows, also try typing `explorer .` to open Explorer for the current directory.

#### Make Directory `mkdir`

As well as navigating directories, we can interact with files on the command line: we can read them, open them, run them, and even edit them.

We will try a few basic ways to interact with files. Let's first move into the Metadata-Analysis-Workshop directory on your desktop (if you don't have this directory, please ask for help).

```bash
# Change to our home directory
$ cd
# Change to our CUL-MWG-Workshop directory
$ cd Desktop/CUL-MWG-Workshop-master
$ pwd
/Users/Christina/Desktop/CUL-MWG-Workshop-master
```

Here, we will create a new directory and move into it. Make the directory name 'firstdir':

```bash
$ mkdir firstdir
$ cd firstdir
```

Here we used the `mkdir` command (meaning 'make directories') to create a directory named 'firstdir'. Then we moved into that directory using the `cd` command.

#### Remove Files &amp; Directories `rmdir` & `rm`

Finally, onto deleting. We won't use it now, but if you do want to delete a file, for whatever reason, the command is `rm`, or remove.

Using wildcards, we can even delete lots of files. And adding the `-r` flag we can delete folders with all their content.

Unlike deleting from within our graphical user interface, there is no warning, no recycling bin from which you can get the files back and no other undo options! For that reason, please be _very careful_ with rm and extremely careful with `rm -r`.

If you want to remove a directory, you can use `rmdir` - but it will only work if the directory is empty. If the directory is not empty, you need to remove the files (using `rm`) first.

#### Copy & Move Files `cp` & `mv`

We may also want to change the file name to something more descriptive. We can move it to a new name by using the `mv` or move command, giving it the old name as the first argument and the new name as the second argument:

```bash
$ mv 829-0.txt gulliver.txt
This is equivalent to the 'rename file' function.
```

Afterwards, when we perform a ``ls`` command, we will see that it is now gulliver.txt:

```bash
$ ls
2014-01-31_JA-africa.tsv 2014-02-02_JA-britain.tsv gulliver.txt 2014-01-31_JA-america.tsv 33504-0.txt 2014-01_JA.tsv
```

Instead of moving a file, you might want to copy a file (make a duplicate), for instance to make a backup before modifying a file using some script you're not quite sure how works. Just like the `mv` command, the `cp` command takes two arguments: the old name and the new name.

How would you make a copy of the file gulliver.txt called gulliver-backup.txt? Try it!

Click 'details' to see the answer


<detail>

```bash
$ cp gulliver.txt gulliver-backup.txt
```

<detail>


Renaming a directory works in the same way as renaming a file. Try using the mv command to rename the firstdir directory to backup.

Click 'details' to see the answer


<details>

```bash
$ mv firstdir backup
```

<details>


If the last argument you give to the mv command is a directory, not a file, the file given in the first argument will be moved to that directory. Try using the `mv` command to move the file gulliver-backup.txt into the backupfolder.

```bash
$ mv gulliver-backup.txt backup
```

This would also work:

```bash
$ mv gulliver-backup.txt backup/gulliver-backup.txt
```

### Finding & Opening Things

#### cat, head

At the top level of the Metadata-Analysis-Workshop directory, we want to take a peek at some of the harvested-metadata files. However, these can sometimes be large enough to make GUI editors not work.

Lets ry the `cat` command to open the file:

```bash
$ cat harvested-metadata/springfield.oai.qdc.xml
```

The terminal window erupts and the whole XML file cascades by (it is printed to your terminal). Great, but we can't really make any sense of that amount of text. (Hit Ctrl+C if you're stuck in the XML view)

Often we just want a quick glimpse of the first or the last part of a file to get an idea about what the file is about. To let us do that, the Unix shell provides us with the commands `head` and `tail`.

```bash
$ head harvested-metadata/springfield.oai.qdc.xml
  <?xml version="1.0" encoding="UTF-8"?><OAI-PMH xmlns="http://www.openarchives.org/OAI/2.0/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.openarchives.org/OAI/2.0/ http://www.openarchives.org/OAI/2.0/OAI-PMH.xsd"> <responseDate>2015-10-11T00:35:52Z</responseDate> <ListRecords>
  <record><header status="deleted"><identifier>oai:cdm16122.contentdm.oclc.org:p15370coll2/0</identifier><datestamp>2012-02-08</datestamp><setSpec>p15370coll2</setSpec></header>
  </record><record><header><identifier>oai:cdm16122.contentdm.oclc.org:p15370coll2/1</identifier><datestamp>2014-01-08</datestamp><setSpec>p15370coll2</setSpec></header>
  <metadata>
  <oai_qdc:qualifieddc xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:dcterms="http://purl.org/dc/terms/" xmlns:oai_qdc="http://worldcat.org/xmlschemas/qdc-1.0/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://worldcat.org/xmlschemas/qdc-1.0/ http://worldcat.org/xmlschemas/qdc/1.0/qdc-1.0.xsd http://purl.org/net/oclcterms http://worldcat.org/xmlschemas/oclcterms/1.4/oclcterms-1.4.xsd">
  <dc:title>Amos Alonzo Stagg, ca. 1891</dc:title>
  <dc:description>A photograph of Amos Alonzo Stagg, ca. 1891, while a student at the International Young Men's Christian Association Training School, now known as Springfield College. Stagg graduated  in 1891  and  served as an assistant physical education instructor at Springfield College from 1890-1892. He started the football program at Springfield College and played in one of the first public basketball game, being the only faculty member to score a &quot;basket ball goal&quot; in their 5 to 1 loss to the students. His football teams at Springfield College were known as &quot;Stagg's Eleven&quot; or the &quot;Stubby Christians&quot;. During the two years he coached and played football at Springfield College, the &quot;Stubby Christians&quot; went 10-11-1 and played in one of the first indoor football games in Madison Square Garden against the Yale Consolidated team on December 12, 1890. In a career spanning more than 50 years, Stagg came to be known as the &quot;Grand Old Man of Football&quot;. He coached football at the University of Chicago (Chicago, Ill.) from 1892-1932 and at the College of  the Pacific from 1933 until his retirement in 1946. Over his career he won 314 games. Amos Alonzo Stagg died in 1965 at the age of 102.</dc:description>
  <dc:subject>Springfield College--Students; Springfield College--Alumni and alumnae; International Young Men's Christian Association Training School (Springfield, Mass.); Springfield College;</dc:subject>
  <dc:subject>Stagg, Amos Alonzo, 1862-1965; Football; Coaching;</dc:subject>
  <dc:publisher>Springfield College</dc:publisher>
```

This provides a view of the first ten lines, whereas `tail harvested-metadata/springfield.oai.qdc.xml` provides a perspective on the last ten lines:

```bash
$ tail harvested-metadata/springfield.oai.qdc.xml
  <dcterms:extent>22 Pages</dcterms:extent>
  <dc:language>en-US</dc:language>
  <dc:relation>The Springfield Student</dc:relation>
  <dc:relation>52</dc:relation>
  <dc:relation>16</dc:relation>
  <dc:rights>Text and images are owned, held, or licensed by Springfield College and are available for personal, non-commercial, and educational use, provided that ownership is properly cited. A credit line is required and should read: Courtesy of Springfield College, Babson Library, Archives and Special Collections. Any commercial use without written permission from Springfield College is strictly prohibited. Other individuals or entities other than, and in addition to, Springfield College may also own copyrights and other propriety rights. The publishing, exhibiting, or broadcasting party assumes all responsibility for clearing reproduction rights and for any infringement of United States copyright law.</dc:rights>
  <dc:identifier>http://cdm16122.contentdm.oclc.org/cdm/ref/collection/p16122coll6/id/10117</dc:identifier></oai_qdc:qualifieddc>
  </metadata>
  </record>
  </ListRecords></OAI-PMH>
```


#### Global Regular Expression Print `grep`

Searching for something in one or more files is something we'll often need to do, so let's introduce a command for doing that: `grep` (short for global regular expression print).

Now let's try our first search:

```bash
$ grep harvest-metadata *.qdc.xml
```
Amend `grep metadata *.csv` to `grep -c metadata *.csv` and hit enter.

```bash
$ grep -c metadata *.csv
2017-ecommons-CU-etds.csv:3
2017-ecommons-CUL-community.csv:139
CUlecturetapes_metadata_ingest-ready.csv:0
```

The shell now prints the number of times the string metadata appeared in each file.
We will try another search:

```bash
$ grep -c 'Digital Archive' *.csv
2017-ecommons-CU-etds.csv:0
2017-ecommons-CUL-community.csv:3
CUlecturetapes_metadata_ingest-ready.csv:0
```

We got back the counts of the instances of the string 'application/pdf' within the files. Now, amend the above command to the below and observe how the output of each is different:

```bash
$ grep -ci 'Digital Archive' *.csv
2017-ecommons-CU-etds.csv:0
2017-ecommons-CUL-community.csv:7
CUlecturetapes_metadata_ingest-ready.csv:0
```

This repeats the query, but prints a case insensitive count (including instances of both digital archive and Digital Archive and other variants).

As before, cycling back and adding `>` results/, followed by a filename (ideally in .txt format), will save the results to a data file. Go ahead and do this on your own.

So far we have counted strings in file and printed to the shell or to file those counts. But the real power of grep comes in that you can also use it to create subsets of tabulated data (or indeed any data) from one or multiple files.

```bash
$ grep -i metadata *.csv
```

This script looks in the defined files and prints any lines containing revolution (without regard to case) to the shell.

```bash
$ grep -i metadata *.csv > results/2017-02-15_metadata-ecommons.csv
```

This saves the subsetted data to file.

Sometimes you need to capture the whole word only in that form (so revolution, but not revolutionary). The `-w` flag instructs `grep` to look for whole words only, giving us greater precision in our search.

```bash
$ grep -iw revolution *.csv > results/DATE_JAiw-revolution.csv
```

This script looks in both of the defined files and exports any lines containing the whole word revolution (without regard to case) to the specified .csv file.

We can show the difference between the files we created.

```bash
$ wc -l results/*.csv
     186 results/2017-02-15_metadata-ecommons.csv
     162 results/DATE_JAiw-revolution.csv
     348 total
```

Finally, we'll use the regular expression syntax covered earlier to search for similar words.

There is unfortunately both "basic" and "extended" regular expressions. This is a common cause of confusion. Unless you want to remember the details, just always use extended regular expressions (-E flag) when doing something more complex than searching for a plain string.

The regular expression `'fr[ae]nc[eh]'` will match "france", "french", but also "frence" and "franch". It's generally a good idea to enclose the expression in single quotation marks, since that ensures the shell sends it directly to grep without any processing (such as trying to expand the wildcard operator *).

```bash
$ grep -iwE 'fr[ae]nc[eh]' *.csv
```

The shell will print out each matching line.
We include the `-o` flag to print only the matching part of the lines e.g. (handy for isolating/checking results):

```bash
$ grep -iwEo 'fr[ae]nc[eh]' *.csv
```

### Other Commands

#### wc, wc -l, wc -w, wc -m

`wc` is the "word count" command: it counts the number of lines, words, bytes and characters in files. Since we love the wildcard operator, let's run the command `wc *.tsv` to get counts for all the .tsv files in the current directory (it takes a little time to complete):

```bash
$ wc *.csv
   12012 1804072 13923125 2017-ecommons-CU-etds.csv
   13172  452320 5233591 2017-ecommons-CUL-community.csv
     238   15220  147314 CUlecturetapes_metadata_ingest-ready.csv
   25422 2271612 19304030 total
$ wc *.*sv
   13712  511261 3773660 2014-01-31_JA-africa.tsv
   27392 1049601 7731914 2014-01-31_JA-america.tsv
    5375  196999 1453418 2014-02-02_JA-britain.tsv
   12012 1804072 13923125 2017-ecommons-CU-etds.csv
   13172  452320 5233591 2017-ecommons-CUL-community.csv
     238   15220  147314 CUlecturetapes_metadata_ingest-ready.csv
   71901 4029473 32263022 total
```

The first three columns contains the number of lines, words and bytes (to show number characters you have to use a flag).

If we only have a handful of files to compare, it might be faster or more convenient to just check with Microsoft Excel, OpenRefine or your preferred text editor, but when we have tens, hundreds or thousands of documents, the Unix shell has a clear speed advantage. The real power of the shell comes from being able to combine commands and automate tasks, though. We will touch upon this slightly.

For now, we'll see how we can build a simple pipeline to find the shortest file in terms of number of lines. We start by adding the -l flag to get only the number of lines, not the number of words and bytes:

```bash
$ wc -l *sv
   13712 2014-01-31_JA-africa.tsv
   27392 2014-01-31_JA-america.tsv
    5375 2014-02-02_JA-britain.tsv
   12012 2017-ecommons-CU-etds.csv
   13172 2017-ecommons-CUL-community.csv
     238 CUlecturetapes_metadata_ingest-ready.csv
   71901 total
```

The `wc` command itself doesn't have a flag to sort the output, but as we'll see, we can combine three different shell commands to get what we want.

First, we have the `wc -l *sv` command. We will save the output from this command in a new file. To do that, we redirect the output from the command to a file using the ‘greater than’ sign (>), like so:

`$ wc -l *sv > lengths.txt`

There's no output now since the output went into the file lengths.txt, but we can check that the output indeed ended up in the file using `cat` or `less` (or Notepad or any text editor).

```bash
$ cat lengths.txt  
   13712 2014-01-31_JA-africa.tsv
   27392 2014-01-31_JA-america.tsv
    5375 2014-02-02_JA-britain.tsv
   12012 2017-ecommons-CU-etds.csv
   13172 2017-ecommons-CUL-community.csv
     238 CUlecturetapes_metadata_ingest-ready.csv
   71901 total
```

#### sort, sort -f, sort -n, sort -r, sort -c


Next, there is the `sort` command. We'll use the -n flag to specify that we want numerical sorting, not lexical sorting, we output the results into yet another file, and we use cat to check the results:

```bash
$ sort -n lengths.txt > sorted-lengths.txt
$ cat sorted-lengths.txt
     238 CUlecturetapes_metadata_ingest-ready.csv
    5375 2014-02-02_JA-britain.tsv
   12012 2017-ecommons-CU-etds.csv
   13172 2017-ecommons-CUL-community.csv
   13712 2014-01-31_JA-africa.tsv
   27392 2014-01-31_JA-america.tsv
   71901 total
```

#### uniq, uniq -c, uniq -u, uniq -d

If you pipe something to the `uniq` command, it will filter out duplicate lines and only return unique ones. Try piping the output from the command in the last exercise to uniq and then to wc -l to count the number of unique ISSN values.

```bash
$ grep -Eo '\d{4}-\d{4}' 2014-01_JA.tsv | uniq | wc -l
```

### Pipelines & pipes

But we're really just interested in the end result, not the intermediate results now stored in lengths.txt and sorted-lengths.txt.
What if we could send the results from the first command (`wc -l *sv`) directly to the next command (`sort -n`) and then the output from that command to `head -n 1`? Luckily we can, using a concept called **pipes**. On the command line, you make a pipe with the vertical bar character |. Let's try with one pipe first:

```bash
$ wc -l *sv | sort -n
     238 CUlecturetapes_metadata_ingest-ready.csv
    5375 2014-02-02_JA-britain.tsv
   12012 2017-ecommons-CU-etds.csv
   13172 2017-ecommons-CUL-community.csv
   13712 2014-01-31_JA-africa.tsv
   27392 2014-01-31_JA-america.tsv
   71901 total
```

Notice that this is exactly the same output that ended up in our sorted-lengths.txt earlier. Let's add another pipe:

```bash
$ wc -l *sv | sort -n | head -n 1
238 CUlecturetapes_metadata_ingest-ready.csv
```

It can take some time to fully grasp pipes and use them efficiently, but it's a very powerful concept that you will find not only in the shell, but also in most programming languages.

> See http://jorol.de/2016-ELAG-Bootcamp/slides/#6 for more abbreviated and technical introduction to working with pipes.

This simple idea is why Unix has been so successful. Instead of creating enormous programs that try to do many different things, Unix programmers focus on creating lots of simple tools that each do one job well, and that work well with each other.
This programming model is called “pipes and filters”. We’ve already seen pipes; a filter is a program like wc or sort that transforms a stream of input into a stream of output. Almost all of the standard Unix tools can work this way: unless told to do otherwise, they read from standard input, do something with what they’ve read, and write to standard output.
The key is that any program that reads lines of text from standard input and writes lines of text to standard output can be combined with every other program that behaves this way as well. You can and should write your programs this way so that you and other people can put those programs into pipes to multiply their power.


We have our `wc -l *sv | sort -n | head -n 1` pipeline. What would happen if you piped this into `cat`? Try it!

Click 'detail' to get the expected results.


<detail>

The cat command just outputs whatever it gets as input, so you get exactly the same output from

```bash
$ wc -l *sv | sort -n | head -n 1
```

and

```bash
$ wc -l *sv | sort -n | head -n 1 | cat
```

</detail>


### Handling input and output from files (i.e. <, >, >>)

We will save the output from this command in a new file. To do that, we redirect the output from the command to a file using the ‘greater than’ sign (>), like so:

`$ wc -l *sv > lengths.txt`

The date command outputs the current date and time. Can you write the current date and time to a new file called logfile.txt? Then check the contents of the file.

```bash
$ date > logfile.txt
$ cat logfile.txt
```

To check the contents, you could also use less or many other commands.

Beware that `>` will happily overwrite an existing file without warning you, so please be careful. While `>` writes to a file, `>>` appends something to a file. Try to append the current date and time to the file logfile.txt?

```bash
$ date >> logfile.txt
$ cat logfile.txt
```


## Further Resources

- [Introducing the Shell / Software Carpentry](https://swcarpentry.github.io/shell-novice/01-intro/)
- [In the Beginning... Was the Command Line / Patrick Hochstenbach & Johann Rolschewski](http://jorol.de/2016-ELAG-Bootcamp/slides/#1)