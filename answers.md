# Q1

## What does each command tell you?

### `pwd`
This commad shows my current working directory. 
It tells me exactly where I am in the filesystem so I know where files and commands will operate.
Output: '/home/fred/Desktop'

### `whoami`
Displays the username of the currently logged-in user. This is important because permissions and privileges depend on the account being used.
Output: 'fred'

### `uname -a`
It displays the operating system and kernel information including kernel version, hostname, architecture.

## Why run these first on an unknown server?

These three commands provide basic situational awareness. Before making any changes, I need to know:

- My current location in the filesystem.
- Which user account I am operating under.
- Which operating system and kernel version the server is running.

The kernel version is especially important because known kernel vulnerabilities (CVEs) often affect specific versions. Knowing the kernel version allows me to quickly assess whether the system may be vulnerable and whether security updates are required.

---

# Q2

## Five directories and their purpose

### `/bin`
Contains essential user commands such as `ls`, `cp`, and `mv`.

### `/etc`
Stores system-wide configuration files.

### `/home`
Contains user home directories and personal files.

### `/var`
Stores variable data such as logs, caches, and spool files.

### `/usr`
Contains user applications, libraries, and documentation.

## What is the Filesystem Hierarchy Standard (FHS)?

The Filesystem Hierarchy Standard (FHS) is a specification that defines the directory structure and directory contents of Linux systems. It ensures that Linux distributions organize files consistently.

## Why does it exist?

FHS provides consistency across systems. Administrators and automation tools know where configuration files, logs, binaries, and user data should be located.

## What breaks without it?

Without FHS:

- Scripts may fail because files are stored in unexpected locations.
- Monitoring tools may not find logs.
- Backup systems may miss critical data.
- New administrators would struggle to understand the server structure.

---

# Q3

## What is different between the two outputs?

The default listing only shows visible files and directories.

The detailed listing with hidden files shows:

- Hidden files eg. .config .profile
- Permissions
- Ownership
- File size
- Modification dates

## What makes a file hidden?

A file becomes hidden when its name begins with a period (`.`). It is not a special file type in Linux.

## Two common hidden files

### `.bashrc`
Contains shell configuration settings, aliases, and environment customizations.

### `.ssh`
Stores SSH keys and SSH configuration information used for secure remote access.

---

# Q4

## Which flag did you use?

I used the `-p` flag with `mkdir`.

## What does it do?

It creates parent directories automatically if they don't already exist.

## What is brace expansion?

Brace expansion is a Bash feature that allows multiple directory names to be generated from a single expression.

Example:

```bash
mkdir -p ~/projects/cyphercore/{configs,logs/{access,errors,archive},reports}
```

## How did it help?

It allowed the entire directory structure to be created in one command instead of several separate commands.

## Could you do it without brace expansion?

Yes, but it would require multiple commands and be more time-consuming.

---

# Q5

## What does the command actually do?

The `touch` command creates files if they do not already exist.

## What happens if the file already exists?

The file is not overwritten. Instead, its access and modification timestamps are updated.

## What changes before and after?

The modification time changes while the file contents remain the same.

## Why is this useful?

Many automation scripts use timestamps to determine whether a file has been updated. 
Updating timestamps without modifying content can trigger workflows or indicate activity.

---

# Q6

## What do the file sizes tell you?

The files for app.conf and db.conf are 0 because they are empty and were just created with `touch`.

## Permission breakdown

looking at app.conf we see -rw-r--r--

- `-` = regular file
- `rw-` = owner can read and write
- `r--` = group can read
- `r--` = others can read

## What does `-h` do?

The `-h` flag displays file sizes in a human-readable format such as KB, MB, or GB instead of raw bytes.
However in this case, because the files are empty we see only 0

---

# Q7

## Difference between `tree` and `ls -R`

`tree` provides a visual hierarchy showing parent-child relationships between directories and files.

`ls -R` recursively lists files but does not provide a clear visual structure.

## When would a DevSecOps engineer use `tree`?

A DevSecOps engineer might use `tree` to:

- Verify deployment structures.
- Check log locations.
- Identify unexpected files or directories.
- Validate that automation scripts created the correct structure.

---

# Q8

## Difference between `>` and `>>`

### `>`
Overwrites a file.

### `>>`
Appends data to the end of a file.

## What happened when using `>`?

The file content was replaced with only the new line that was written.

## Why is this dangerous?

Accidentally using `>` on production logs can destroy valuable troubleshooting information, audit trails, and incident evidence.

In a production log rotation script, confusing these operators could result in permanent loss of logs that may be needed for debugging or security investigations.

---

# Q9

## Difference between the two commands

### `cat`
Displays content from top to bottom.

### `tac`
Displays content from bottom to top.

## Which would I use first during an incident?

I would use `tac` because the newest log entries are usually the most relevant during troubleshooting.

## Command showing the last 10 lines

```bash
tail
```

or more explicitly:

```bash
tail -n 10 logs/access/access.log
```

---

# Q10

## 1. What is a heredoc?

A heredoc is a way to provide multiple lines of input to a command in a single operation.

It makes scripts cleaner and easier to maintain compared to using many separate `echo` statements.

## 2. Difference between `EOF` and `'EOF'`

### Unquoted (`EOF`)

Variables are expanded.

### Quoted (`'EOF'`)

Variables are treated literally.

Output:

```text
Database: $DBNAME
```

## What did I observe?

From Image Q1.2, an unquoted delimiter, Bash replaced `$DBNAME` with its value.

From Image Q1.3, a quoted delimiter, Bash treated `$DBNAME` as plain text and printed it exactly as written.

## 3. Why might someone prefer a heredoc on a compromised server?

A heredoc leaves fewer traces than opening editors like `nano` or `vim`.

Editors may create:

- Swap files
- Temporary files
- Recovery files

These can affect forensic investigations or leave evidence of activity.

---

# Q11

## Differences between `less` and `more`

### `less`

- Allows forward and backward navigation.
- Supports searching efficiently.
- Better suited for large files.

### `more`

- Primarily supports forward navigation.
- Has fewer navigation features.

## Which loads the entire file into memory first?

`less` is optimized for large files and does not require loading everything into memory before viewing.

## Which would I use on a 512MB server with a 3GB log?

I would use `less` because it handles very large files efficiently.

## Three useful `less` shortcuts

| Search | `/search_term` |
| Jump to end | `G` |
| Quit | `q` |

---

# Q12

## Difference between the two commands

### `cp`
Creates a duplicate copy of a file.

### `mv`
Renames or relocates a file.

## Does the original filename still exist after renaming?

No.

After using `mv`, the old filename disappears and only the new filename remains.

## Does copying a large file duplicate all data on disk?

Yes.

A traditional copy operation creates another copy of the file data on disk, which consumes additional storage space.

---

# Q13

## What happened when removing `logs/`?

From image 13 we got a failure prompt
The command failed because the directory was not empty.

## Difference between `rmdir` and `rm -r`

### `rmdir`
Removes only empty directories.

### `rm -r`
Removes directories and all contents recursively.

## Why is `rm -rf` dangerous?

It can permanently delete large portions of a filesystem without asking for confirmation, even the root directory.

## Important rule before using `rm -rf`

Always verify the target path carefully before pressing Enter. A small mistake can cause significant data loss.

---

# Q14

## Does the structure match Abena's original sticky note?

Yes.

The directory structure matches Abena's sticky notes.
Only difference is that with our current structure, there are some files in there that aren't 
in Abena's sticky notes

## Why does structure matter?

A clean and predictable structure helps:

- Deployment scripts find files reliably.
- Monitoring tools locate logs.
- Security tools scan expected locations.
- Team members understand the environment quickly.

Consistency reduces operational errors and simplifies automation.

---

# Q15

## What do you notice about the inode numbers?

The original file and the hard link share the same inode number ie 301852.

## What does the link count represent?

The link count represents how many filenames currently point to the same inode.

## What is an inode?

An inode is a table that stores information about a file and points to the file's location.

---

# Q16

## Is the data still there?

Yes.

The data remains accessible through the hard link.

## Why?

The hard link still points to the same inode that contains the file data.

Deleting one filename does not remove the inode if another hard link still exists.

## What must happen before the data is permanently deleted?

All hard links referencing the inode must be removed so that the link count reaches zero.

## What changed?

The link count decreased by one after deleting the original filename.

## How could an attacker use hard links?

An attacker could create additional hard links to important files so the data survives even if one filename is deleted during cleanup efforts.

---

# Q17

## What identifies a symbolic link?

The letter 'l' at the beginning of the permissions string identifies a symbolic link.

## What happened after deleting the original file?

The symbolic link could no longer find its target.

## What is this called?

This is known as a 'dangling symlink' or 'broken symlink'.

## Why can this cause deployment failures?

The link still exists and appears valid, but the target file no longer exists.

Applications or scripts depending on the link may fail unexpectedly, making troubleshooting more difficult at 2am.

---

# Q18

| Property | Hard Link | Soft Link |
|-----------|-----------|-----------|
| Shares inode with original? | Yes | No |
| Works across different filesystems? | No | Yes |
| Survives deletion of original? | Yes | No |
| Can link to a directory? | No | Yes |
| Shows as `l` in `ls -la`? | No | Yes |
| Detectable by matching inodes in `ls -li`? | Yes | No |

## Real-world DevSecOps use cases

### Hard Link

Maintaining access to important log files during log rotation while preserving the underlying data.

### Soft Link

Pointing applications to the latest deployment release directory without changing application configuration.

---

# Q19

## 1. Which content landed where and why?

Successful output was written to `stdout_output.txt` because it was standard output.

Error messages were written to `stderr_output.txt` because they were generated by standard error.

Linux separates normal output from error output using different streams.

## 2. Name all three streams

| Stream | Number | Purpose |
|----------|----------|----------|
| stdin | 0 | Input |
| stdout | 1 | Normal output |
| stderr | 2 | Error output |

## 3. What does `2>` mean?

`2>` redirects stream 2 (stderr) into a file.

## 4. What does `2>&1` do?

`2>&1` redirects stderr to the same destination currently being used by stdout.

## Real deployment use case

When running deployment or backup scripts, engineers often combine stdout and stderr into a single log file so that all output can be reviewed together after execution.

---

# Q20

## 1. Did you quote the delimiter?

For the report, I used an unquoted delimiter because variable expansion can be useful when generating reports dynamically.

## Why does it matter?

Quoted and unquoted delimiters behave differently when variables are present.

## 3. When would I use each?

### Unquoted heredoc

Useful when generating reports, scripts, or templates that should contain actual variable values.

### Quoted heredoc

Useful when creating configuration files or script templates where variables should remain untouched until a later stage.

---

# Q21

## How does everything built today relate to DevSecOps?

The directory structure mirrors how applications are organized in production environments.

The logs simulate real operational and troubleshooting data.

The archive folder represents historical log retention.

Hard and soft links are commonly used in deployments and configuration management.

The report demonstrates documentation and operational reporting practices.

Together, these tasks reflect the daily responsibilities of a DevSecOps engineer.

## Two commands that changed how I think about Linux

### `ln`

Understanding hard links helped me realize that filenames are not the actual file. Multiple names can point to the same inode and data.

### `less`

I once had a 5GB database .sql script, I wanted to read the first few lines but my applications kept crashing.
This was because I always run out of RAM when opening this file. Now I know how to use less command to do it 
without running out of RAM