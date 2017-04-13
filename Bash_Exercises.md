# Bash Exercises

## Exercise 1: Moving around files & directories

Navigate into the `Metadata-Analysis-Workshop` directory and explore what's there.

```
pwd # Print working directory
ls # List files
ls -a # List all files (including hidden files)
ls -l # List files along with additional info
ls -lh  # List long form with unix suffixes for file size
cd # Change directory
cd .. # Go one directory 'up' the directory stucture
mkdir # Make a new directory
rmdir # Delete a directory (use with caution!)
```

### Hints:
* Use `man` to learn more about a command, e.g. `man ls`.  (Type `q` to escape).
* Use the tab key to autocomplete.

## Exercise 2: Analyzing files

Use your new skills to analyze files in `Metadata-Analysis-Workshop/harvested-metadata`

```
grep # Search within one or more files
grep -c # Number of times a string appears within one or more files
grep -i # Case-insentitive
grep -w # Look for whole words only
wc # Count lines, words, and bytes in one or more files
wc -l # Count lines
wc -w # Count words
[command A] | [command B] # "Pipe" the output of command A to command B
uniq # Get unique values
sort # Sort values lexially
```

### Hints:
* Use `*` for a wildcard matcher.
* Use the up and down arrows to look through previous commands.
