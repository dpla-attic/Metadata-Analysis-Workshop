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

#### Shell tricks (i.e. tab autocomplete, up + down arrows for previous commands)

### Moving around files & directories:

#### ls, ls -A, ls -R, ls -alsh

#### cd, cd /, cd ./wherever, cd ../.., cd ~

#### pwd

#### mkdir, mkdir -p

#### rmdir, rmdir -p, rm, rm -rf

#### cp, cp -r, mv

### Finding & Opening Things
    - find, find -name -type, find -iname, find -not
    - nano, vi, open
    - head -n, tail -n, tail -f, more, less
    - grep, grep -c, grep -o, grep -i, grep -oP, grep -v

### Other Commands
    - wc, wc -l, wc -w, wc -m
    - sort, sort -f, sort -n, sort -r, sort -c
    - uniq, uniq -c, uniq -u, uniq -d
    - sed, sed s//, sed s:/

### Pipelines & pipes to create a sequence with output passed over

### Handling input and output from files (i.e. <, >, >>)

## Further Resources

- [Introducing the Shell / Software Carpentry](https://swcarpentry.github.io/shell-novice/01-intro/)
- [In the Beginning... Was the Command Line / Patrick Hochstenbach & Johann Rolschewski](http://jorol.de/2016-ELAG-Bootcamp/slides/#1)