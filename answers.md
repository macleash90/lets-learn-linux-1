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
