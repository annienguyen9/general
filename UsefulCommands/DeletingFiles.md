# **Useful Commands for Deleting Files in the Sherlock Terminal:**

### Basic File Deletion

To delete a single file, use:
```
rm filename
```

To delete multiple files at once, list them separated by spaces:
```
rm file1 file2 file3
```

To delete files matching a pattern, you can use wildcards:

* matches any number of characters.
? matches a single character.
For example, to delete all .gtf files in the current directory:
```
rm *.gtf
```

### Recursive Deletion
To delete a directory and its contents, use the -r (recursive) option:
```
rm -r directoryname
```
To delete a directory named example_dir and all its contents without any prompts:
```
rm -rf example_dir
```

Safety Precautions:
Be Cautious: The rm command permanently deletes files. Always double-check the file names and paths before executing the command.
Avoid Root: Avoid using rm as the root user unless absolutely necessary, as it can lead to accidental system damage.
