# **Useful Commands for Checking Disk Usage in Sherlock:**

### Check Disk Usage

Use the du command to check the disk usage of files and directories. This can help you identify large files or directories.

Check Disk Usage of the Current Directory
```
du -sh *
```

### List Files Sorted by Size
Use the ls command with sorting options to list files by size:
```
ls -lhS
```
To list files in a specific directory, include the directory path:
```
ls -lhS /path/to/directory
```

### Find Large Files
Find Files Larger Than a Specific Size:

For example, to find files larger than 100MB:
```
find /path/to/directory -type f -size +100M -exec ls -lh {} \; | awk '{ print $NF ": " $5 }'
```

### Check Quota Usage
Find Files Larger Than a Specific Size:

Use the myquota command to check your quota usage:
```
myquota
```
