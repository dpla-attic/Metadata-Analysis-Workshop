# Command Line Basics, DPLAFest 2017 DPLA Metadata Analysis Workshop

*1:45-2:15 PM, led by [Audrey Altman, DPLA Developer](mailto:audrey@dp.la)*

## Goals

- *nix Shell & Bash Introduction
- Navigating & Working with Files & Directories
- Pipes & Filters
- Finding Things
- Redirect Input & Output

## Preparation

Either using Git, curl, the GitHub interface or thumbdrives passed around before the session, download this Workshop GitHub Repository to your computer. We'll be using that for the session.

## Lesson Questions/Topics:

**Table of Contents**

### Preparation

Quick grab of the Workshop Repo (through whatever method), then unzip as needed.


### \*nix Shell & Bash Introduction

What is a command-line interface (CLI)? Where does bash interact with this? What is bash?

> "In this session we will introduce programming by looking at how data can be manipulated, counted, and mined using the Unix shell, a command line interface to your computer and the files to which it has access.

> The shell is one of the most productive programming environments ever created. Its syntax may be cryptic, but people who have mastered it can experiment with different commands interactively, then use what they have learned to automate their work. Command Line Interfaces like shells and tools were the primary utilities for interaction with computer systems and programs until the introduction of the Graphical User Interfaces. For many tasks shell and the command line can still excel GUI programs, however: particularly for process very large datasets and files.

> A Unix shell is a command-line interpreter that provides a user interface for the Linux operating system and for Unix-like systems (such as Mac OS).

> For Windows users, popular shells such as Cygwin or Git Bash provide a Unix-like interface. This session will cover a small number of basic commands using Git Bash for Windows users, Terminal for Mac OS. These commands constitute building blocks upon which more complex commands can be constructed to fit your data or project.

> What you can quickly learn is how to query lots of data for the information you want super fast. Using Bash or any other shell sometimes feels more like programming than like using a mouse. Commands are terse (often only a couple of characters long), their names are frequently cryptic, and their output is lines of text rather than something visual like a graph. On the other hand, with only a few keystrokes, the shell allows us to combine existing tools into powerful pipelines and handle large volumes of data automatically. This automation not only makes us more productive, but also improves the reproducibility of our workflows by allowing us to repeat them with few simple commands. Also, understanding the basics of the shell is very useful as a foundation before learning to program, since most programming languages necessitate working with the shell." (abridged from Library Carpentry)

#### Basic structure of a Command (command, options/flags, arguments)

> "Let's start by opening the shell. This likely results in seeing a black window with a cursor flashing next to a dollar sign. This is our command line, and the $ is the command prompt to show the system is ready for our input. The prompt can look somewhat different from system to system, but it usually ends with a $.

> The $ sign is used to indicate a command to be typed on the command prompt, but we never type the $ sign itself, just what follows after it." (adapted from In the Beginning ... There was the Command Line)

```bash
$ command --option(s) argument(s)
# long options
$ ls --all ~/
# short options
$ ls -a ~/
```

#### How to get help understanding a Command

Use the `man` command to invoke the manual page (documentation) for a shell command. For example,

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

displays all the flags/options available to you. Use the spacebar to navigate the manual pages, and q to quit this man output.

Note: this command is for Mac and Linux users only. It does not work directly for Windows users. If you use windows, you can search for the Shell command on http://man.he.net/, and view the associated manual page. You can also use the help switch `--help` after a command to display the help documentation. e.g. `ls --help`

#### Shell tricks

- tab autocomplete: Hitting tab at any time within the shell will prompt it to attempt to auto-complete the line based on the files or sub-directories in the current directory. Where two or more files have the same characters, the auto-complete will only fill up to the first point of difference, after which we can add more characters, and try using tab again.
- up + down arrows for previous commands

### Moving around files & directories:

#### pwd

When working in the shell, you are always somewhere in the computer's file system, in some folder (directory). We will therefore start by finding out where we are by using the `pwd` command, which you can use whenever you are unsure about where you are. It stands for 'print working directory' and the result of the command is printed to your standard output, which is the terminal.

Let's type `pwd` and hit enter to execute the command:

```bash
$ pwd
/Users/Christina
```

#### ls, ls -A, ls -R, ls -alsh

The output will be a path to your home directory. Let's check if we recognize it by listing the contents of the directory. To do that, we use the `ls` command:

```bash
$ ls
Applications             Downloads                Music                    Public                   __MACOSX                 perl5
Applications (Parallels) Dropbox                  Pictures                 Sites                    marcedit                 test.json
Desktop                  Library                  Presentations            Tools                    mashcat
Documents                Movies                   Projects                 VirtualBox VMs           nifi-0.7.0
```

We may want more information than just a list of files and directories. We can get this by specifying various flags (also known as options or switches) to go with our basic commands.

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

Now `ls -h` won't work on its own. When we want to combine two flags, we can just run them together. So, by typing `ls -lh` and hitting enter we receive an output in a human-readable format (note: the order here doesn't matter).

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

#### cd, cd /, cd ./wherever, cd ../.., cd ~

We've now spent a great deal of time in our home directory. Let's go somewhere else. We can do that through the `cd` or Change Directory command:

(Note: On Windows and Mac, by default, the case of the file/directory doesn't matter. On Linux it does.)

```bash
$ cd Desktop
```

Notice that the command didn't output anything. This means that it was carried out successfully. Let's check by using `pwd`:

```bash
$ pwd
/Users/Christina/Desktop
```

If something had gone wrong, however, the command would have told you. Let's see by trying to move into a (hopefully) non-existing directory:

```bash
$ cd "bibframe plan"
cd: The directory 'bibframe plan' does not exist
```

Notice that we surrounded the name by quotation marks. The arguments given to any shell command are separated by spaces, so a way to let them know that we mean 'one single thing called "bibframe plane"', not 'two different things', is to use (single or double) quotation marks.

We've now seen how we can do 'down' through our directory structure (as in into more nested directories). If we want to go back, we can type `cd ..` . This moves us 'up' one directory, putting us back where we started. If we ever get completely lost, the command `cd` without any arguments will bring us right back to the home directory, right where we started.

To switch back and forth between two directories use: `cd ..` .

```bash
$ cd ..
```

*Exercise - Try Exploring on Your Own*

Take 5 minutes to move around the computer, get used to moving in and out of directories, see how different file types appear in the Unix shell. Be sure to use the `pwd` and `cd` commands, and the different flags for the `ls` command you learned so far.

If you run Windows, also try typing `explorer .` to open Explorer for the current directory (the single dot means "current directory"). If you're on Mac or Linux, try `open .` instead.

#### mkdir, mkdir -p

As well as navigating directories, we can interact with files on the command line: we can read them, open them, run them, and even edit them.

We will try a few basic ways to interact with files. Let's first move into the CUL-MWG-Workshop-master directory on your desktop (if you don't have this directory, please ask for help).

```bash
$ cd
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

#### rmdir, rmdir -p, rm, rm -rf



#### cp, cp -r, mv


Answer <details>

```
blfjksdlafjdksla
```

</details


### Finding & Opening Things

#### find, find -name -type, find -iname, find -not

#### nano, vi, open

#### cat, head -n, tail -n, tail -f, more, less

If you are in firstdir, use `cd ..` to get back to the CUL-MWG-Workshop-master directory.
Here there are copies of two public domain books downloaded from Project Gutenberg along with other files we will cover later.

```bash
$ ls -lh
total 65408
-rw-r--r--@ 1 Christina  staff   3.6M Feb 20 06:13 2014-01-31_JA-africa.tsv
-rw-r--r--@ 1 Christina  staff   7.4M Feb 20 06:13 2014-01-31_JA-america.tsv
-rw-r--r--@ 1 Christina  staff   1.4M Feb 20 06:13 2014-02-02_JA-britain.tsv
-rw-r--r--@ 1 Christina  staff    13M Feb 20 06:13 2017-ecommons-CU-etds.csv
-rw-r--r--@ 1 Christina  staff   5.0M Feb 20 06:13 2017-ecommons-CUL-community.csv
-rw-r--r--@ 1 Christina  staff   582K Feb 20 06:13 33504-0.txt
-rw-r--r--@ 1 Christina  staff   598K Feb 20 06:13 829-0.txt
-rw-r--r--@ 1 Christina  staff   144K Feb 20 06:13 CUlecturetapes_metadata_ingest-ready.csv
-rw-r--r--@ 1 Christina  staff    13B Feb 20 06:13 gallic.txt
```

The files 829-0.txt and 33504-0.txt holds the content of book 829 and #3504 on Project Gutenberg. But we've forgot which books, so we try the `cat` command to read the text of the first file:

```bash
$ cat 829-0.txt
```

The terminal window erupts and the whole book cascades by (it is printed to your terminal). Great, but we can't really make any sense of that amount of text. (Hit Ctrl+C )

Often we just want a quick glimpse of the first or the last part of a file to get an idea about what the file is about. To let us do that, the Unix shell provides us with the commands `head` and `tail`.

$ head 829-0.txt
The Project Gutenberg eBook, Gulliver's Travels, by Jonathan Swift

This eBook is for the use of anyone anywhere at no cost and with
almost no restrictions whatsoever.  You may copy it, give it away or
re-use it under the terms of the Project Gutenberg License included
with this eBook or online at www.gutenberg.org

This provides a view of the first ten lines, whereas tail 829-0.txt provides a perspective on the last ten lines:

$ tail 829-0.txt

Most people start at our Web site which has the main PG search facility:
     http://www.gutenberg.org
This Web site includes information about Project Gutenberg-tm,
including how to make donations to the Project Gutenberg Literary
Archive Foundation, how to help produce our new eBooks, and how to
subscribe to our email newsletter to hear about new eBooks.

If ten lines is not enough (or too much), we would check man head to see if there exists an option to specify the number of lines to get (there is: head -n 20 will print 20 lines).
Another way to navigate files is to view the contents one screen at a time. Type less 829-0.txt to see the first screen, spacebar to see the next screen and so on, then q to quit (return to the command prompt).


$ less 829-0.txt


Like many other shell commands, the commands cat, head, tail and less can take any number of arguments (they can work with any number of files). We will see how we can get the first lines of several files at once. To save some typing, we introduce a very useful trick first.


#### grep, grep -c, grep -o, grep -i, grep -oP, grep -v

### Other Commands

#### wc, wc -l, wc -w, wc -m

#### sort, sort -f, sort -n, sort -r, sort -c

#### uniq, uniq -c, uniq -u, uniq -d

#### sed, sed s//, sed s:/

### Pipelines & pipes to create a sequence with output passed over

### Handling input and output from files (i.e. <, >, >>)

## Further Resources

- [Introducing the Shell / Software Carpentry](https://swcarpentry.github.io/shell-novice/01-intro/)
- [In the Beginning... Was the Command Line / Patrick Hochstenbach & Johann Rolschewski](http://jorol.de/2016-ELAG-Bootcamp/slides/#1)