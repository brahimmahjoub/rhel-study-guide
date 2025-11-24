# RHCSA EX200 (RHEL 10) Complete Study Guide

**For Red Hat Certified System Administrator Examination**

---

## Table of Contents

1. [Installing Red Hat Enterprise Linux 10](#chapter-1-installing-rhel-10)
2. [Using Essential Tools](#chapter-2-using-essential-tools)
3. [Essential File Management Tools](#chapter-3-essential-file-management-tools)
4. [Working with Text Files](#chapter-4-working-with-text-files)
5. [Connecting to RHEL 10](#chapter-5-connecting-to-rhel-10)
6. [User and Group Management](#chapter-6-user-and-group-management)
7. [Permissions Management](#chapter-7-permissions-management)
8. [Configuring Network](#chapter-8-configuring-network)
9. [Managing Software](#chapter-9-managing-software)
10. [Managing Processes](#chapter-10-managing-processes)
11. [Working with Systemd](#chapter-11-working-with-systemd)
12. [Scheduling Tasks](#chapter-12-scheduling-tasks)
13. [Configuring Logging](#chapter-13-configuring-logging)
14. [Managing Storage](#chapter-14-managing-storage)
15. [Managing Advanced Storage](#chapter-15-managing-advanced-storage)
16. [Basic Kernel Management](#chapter-16-basic-kernel-management)
17. [Managing and Understanding the Boot Procedure](#chapter-17-managing-and-understanding-the-boot-procedure)
18. [Troubleshooting Skills](#chapter-18-troubleshooting-skills)
19. [Automation with Bash Shell Script](#chapter-19-automation-with-bash-shell-script)
20. [Configuring SSH](#chapter-20-configuring-ssh)
21. [Managing Apache HTTP Service](#chapter-21-managing-apache-http-service)
22. [SELinux](#chapter-22-selinux)
23. [Configuring a Firewall](#chapter-23-configuring-a-firewall)
24. [Accessing Network Storage](#chapter-24-accessing-network-storage)
25. [Configuring Time Services](#chapter-25-configuring-time-services)
26. [Managing Containers](#chapter-26-managing-containers)

---

# Chapter 1: Installing RHEL 10

## Exam-Relevant Objectives

- Understand RHEL 10 architecture and system requirements
- Comprehend RHEL derivatives and alternatives
- Understand the deployment options available in RHEL 10

## Key Concepts

**Linux**: An open-source, Unix-like operating system kernel created by Linus Torvalds in 1991. Foundation of multiple distributions, known for security, stability, and flexibility.

**RHEL 10**: The latest major release of Red Hat Enterprise Linux, powered by Linux kernel 6.12.0. Available in server and desktop editions with enterprise support.

**RHEL Derivatives**: AlmaLinux and Rocky Linux are free, open-source alternatives to RHEL that use RHEL source code with all Red Hat licensing removed.

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Kernel Version** | 5.14.x | 6.12.0 (major improvements) |
| **Architecture Support** | x86-64-v2 baseline | **x86-64-v3 baseline** (requires newer CPUs) |
| **AI Integration** | None | **Lightspeed AI assistant** for system management |
| **Deployment Model** | Traditional OS installs | **Image-based deployment** (immutable OS) |
| **Post-Quantum Security** | Traditional FIPS | **Post-Quantum FIPS** (ML-KEM, ML-DSA) |
| **DNS Security** | Standard DNS | **DNS-over-HTTPS (DoH) & DNS-over-TLS (DoT)** |
| **Subscription Management** | subscription-manager | **rhc (Red Hat Connect)** for Hybrid Cloud Console |
| **Application Streams** | RPM modularity (dnf modules) | **RPM direct or Flatpak** (modularity removed) |
| **Default Firewall** | nftables 1.0.x | **nftables 1.1.1** (improved) |
| **Network Interface Naming** | Supports legacy eth0 naming | **net.ifnames=0 deprecated, only modern names** |
| **Container Runtime** | Podman with CRI-o | **Enhanced Podman & Buildah support** |
| **Deprecated/Removed** | Some legacy features | **Teamd, ifcfg, SysV init, Cgroup v1, Firefox package** |

## Installation Considerations for RHEL 10

- **Minimum CPU Requirements**: RHEL 10 requires x86-64-v3 CPU baseline. Older systems (pre-2015) may not be compatible.
- **Lightspeed Integration**: Registered systems with active subscriptions can leverage AI-powered troubleshooting at the command line.
- **Image-based Deployment**: RHEL 10 introduces support for immutable OS deployments where core files are read-only.
- **Subscription Management**: Use `rhc connect` instead of `subscription-manager register` for simplified registration to Red Hat's Hybrid Cloud Console.

## Critical Notes

- RHEL 10 is enterprise-focused with emphasis on hybrid cloud, edge, and container-native deployments.
- Always verify CPU architecture compatibility before attempting upgrades from RHEL 9 to RHEL 10.
- New kernel 6.12 provides better workload scheduling, memory management, and hardware compatibility.

---

# Chapter 2: Using Essential Tools

## Exam-Relevant Objectives

- Access a shell prompt and issue commands with correct syntax
- Use input-output redirection (>, >>, |, 2>, etc.)
- Use grep and regular expressions to analyze text
- Create and edit text files using Vim
- Locate, read, and use system documentation (man, info, /usr/share/doc)
- Archive, compress, unpack, and uncompress files using tar, gzip, and bzip2

## Shell Basics

**Shell**: The default working environment for a Linux administrator. Bash is the most common shell on RHEL 10.

```bash
# View current shell
echo $SHELL

# List available shells
cat /etc/shells
```

## Executing Commands

### Command Anatomy
```bash
ls -l /etc
# ls: command
# -l and /etc: arguments
# -l: option that modifies behavior
```

### Types of Commands

- **Aliases**: User-defined shortcuts, executed before anything else
  ```bash
  alias ll='ls -l --color=auto'
  ```

- **Internal Commands**: Part of the shell itself (e.g., cd, echo)
  ```bash
  type pwd  # Check if command is internal
  ```

- **External Commands**: Executable files on disk
  ```bash
  which ls  # Show where command is located
  /usr/bin/time command  # Time an external command
  ```

## I/O Redirection

| Redirector | Explanation |
|-----------|-------------|
| `> (1>)` | Redirects STDOUT; overwrites file |
| `>> (1>>)` | Redirects STDOUT; appends to file |
| `2>` | Redirects STDERR |
| `2>&1` | Redirects STDERR to STDOUT destination |
| `< (0<)` | Redirects STDIN |
| `\|` (pipe) | Sends output of one command to input of another |
| `&>` | Redirects both STDOUT and STDERR (RHEL 10 preferred) |

### Practical Examples
```bash
# Redirect both output and errors to a file
ls valid_file non_existent_file > combined_output.log 2>&1
ls valid_file non_existent_file &> combined_output.log

# Pipe output through grep
ls -R / | grep "pattern"

# Redirect to null device (discard output)
ls nothing > /dev/null 2>&1

# Use file as input
cat < filename.txt
```

## Command History

```bash
# Display command history (default: 1000 commands)
history

# Display last 20 commands
history 20

# Execute command with specific number
!number

# Search backward through history
Ctrl+R  # Opens backward search prompt

# Clear in-memory history
history -c

# Clear both history and .bash_history file
history -c && history -w

# Remove specific command from history
history -d number

# Save history to file
history -w
```

## Vim Editor

### Vim Modes
- **Command Mode** (default): Navigate and execute commands
- **Insert Mode**: Type and edit text (press `i`, `a`, `o`)
- **Command-line Mode**: Execute operations (press `:`)

### Essential Vim Commands
```bash
# Enter Vim
vim filename.txt

# Insert modes
i       # Insert before cursor
a       # Append after cursor
o       # Open new line below
O       # Open new line above

# Navigation
gg      # Go to beginning of file
G       # Go to end of file
^       # Go to beginning of line
$       # Go to end of line
/text   # Search forward for text
?text   # Search backward for text

# Editing
dd      # Delete line
yy      # Yank (copy) line
p       # Paste after cursor
u       # Undo
Ctrl+r  # Redo

# Global find and replace
:%s/old/new/g

# Exit Vim
:wq     # Save and quit
:q!     # Quit without saving
:w      # Save
```

## Shell Environment

### Environment Variables
```bash
# Display all environment variables
env

# Display specific variable
echo $LANG
echo $PATH

# Set variable (temporary)
export MYVAR="value"
```

### Shell Configuration Files

| File | Purpose |
|------|---------|
| `/etc/profile` | Generic login shell initialization (all users) |
| `/etc/bashrc` | Generic subshell initialization |
| `~/.bash_profile` | User-specific login shell variables |
| `~/.bashrc` | User-specific subshell variables |
| `/etc/motd` | Message of the day (displayed after login) |
| `/etc/issue` | Display before login |

**File Loading Order**:
1. `/etc/profile`
2. `~/.bash_profile`
3. `/etc/bashrc`
4. `~/.bashrc`

### Shell Types
- **Login Shell**: First shell opened after user login
- **Subshell**: Any shell started from existing shell

## Finding Help

### Using --help
```bash
ls --help
```

### Man Pages
```bash
# View manual page
man command

# Search man pages by keyword
man -k keyword
man -k password | grep 5  # Filter by section

# View specific section
man 5 passwd  # File format (section 5)
man 8 useradd  # System admin commands (section 8)

# Show short description
man -f command
```

### Man Page Sections
- **1**: Executable programs or shell commands
- **5**: File formats and conventions
- **8**: System administration commands

### Info Pages
```bash
# View info documentation (hierarchical format)
info command

# User-friendly info viewer
pinfo command
```

### Additional Documentation
```bash
# Browse system documentation
ls /usr/share/doc/

# Specific package documentation
ls /usr/share/doc/rsyslog/
cat /usr/share/doc/rsyslog/README
```

## Archive and Compression

### tar Command
```bash
# Create archive
tar cvf file.tar /home
tar cvf file.tar file1 file2 file3

# Extract archive
tar xvf file.tar

# List archive contents
tar tvf file.tar

# Compression options
tar cvzf file.tar.gz /home    # gzip compression
tar cvjf file.tar.bz2 /home   # bzip2 compression
tar cvJf file.tar.xz /home    # xz compression

# Extract compressed archives
tar xvzf file.tar.gz
tar xvjf file.tar.bz2
tar xvJf file.tar.xz
```

### Compression Utilities
```bash
# gzip (fastest, lower compression)
gzip file
gunzip file.gz
gzip -d file.gz

# bzip2 (slower, higher compression)
bzip2 file
bunzip2 file.bz2
bzip2 -d file.bz2

# xz (slowest, highest compression)
xz file
unxz file.xz
xz -d file.xz
```

## Regular Expressions and Text Processing

### grep and Patterns
```bash
# Search for pattern
grep "pattern" file.txt

# Case-insensitive search
grep -i "pattern" file.txt

# Show lines NOT matching pattern
grep -v "pattern" file.txt

# Count matching lines
grep -c "pattern" file.txt

# Show line numbers
grep -n "pattern" file.txt

# Extended regex
grep -E "pattern1|pattern2" file.txt
egrep "pattern1|pattern2" file.txt
```

### Regular Expression Basics

| Character | Meaning |
|-----------|---------|
| `.` | Any single character |
| `*` | Zero or more of previous character |
| `^` | Start of line |
| `$` | End of line |
| `[abc]` | Any one character in brackets |
| `[a-z]` | Any character in range |
| `[^abc]` | Any character NOT in brackets |

### Practical Examples
```bash
# Find lines starting with 'root'
grep "^root" /etc/passwd

# Find lines ending with 'sh'
grep "sh$" /etc/passwd

# Find any digit
grep "[0-9]" /etc/passwd

# Find lines with at least one digit
grep -E "[0-9]+" /etc/passwd
```

## Key Commands Summary

| Command | Purpose |
|---------|---------|
| `type CMD` | Determine command type (alias, internal, external) |
| `which CMD` | Locate external command |
| `man -k KEYWORD` | Search man pages by keyword |
| `info COMMAND` | View hierarchical documentation |
| `tar cvf archive.tar FILES` | Create tar archive |
| `tar xvf archive.tar` | Extract tar archive |
| `gzip file` | Compress file |
| `grep PATTERN file` | Search file for pattern |

## Tips and What to Remember

- **Pipeline Efficiency**: Use pipes effectively to process data without creating temporary files.
- **Error Redirection**: Always test redirection: `2>&1` redirects STDERR to STDOUT's destination.
- **Command History**: Use `Ctrl+R` for faster command history searches instead of `history | grep`.
- **Vim Navigation**: In large files, use `Ctrl+F` (forward) and `Ctrl+B` (backward) for page navigation.
- **Compression Choice**: Use gzip for speed, bzip2 for compression ratio, xz for best compression.
- **Man Page Sections**: Always specify section number when section matters (e.g., `man 5 passwd` for file format).
- **grep Tips**: Use `-i` for case-insensitive, `-v` for inverted matches, `-c` to count occurrences.

## Critical Comments

- The shell environment is crucial for system administration. Understanding command execution order and redirection is fundamental to RHCSA exam success.
- Regular expressions appear throughout RHEL system configuration files and log analysis—mastery is essential.
- Man pages are your best resource on the exam; practice navigating them efficiently.

---

# Chapter 3: Essential File Management Tools

## Exam-Relevant Objectives

- Create, delete, copy, and move files and directories
- Create hard and soft (symbolic) links
- List and identify file types and permissions
- Archive and compress files
- Work with file system hierarchy and mounting concepts

## File System Hierarchy Standard (FHS)

### Key Definitions

**FHS**: Defines standard directory structure and contents across Linux and Unix systems. Ensures consistent file locations and organization.

**File System**: System for organizing and managing files on storage devices.

**Device File**: Special file acting as interface to device driver, allowing interaction with hardware.

**Mounting**: Process of connecting a storage device to a directory (mount point) in the file system, making device contents accessible.

**Inode**: Data structure storing administrative information about files (data blocks, dates, permissions, owners) but NOT the filename.

### Directory Structure Overview

| Directory | Purpose |
|-----------|---------|
| `/` | Root directory |
| `/bin` | Essential user commands (hard links to /usr/bin) |
| `/sbin` | Essential system administration commands |
| `/usr/bin` | User commands |
| `/usr/sbin` | System administration commands |
| `/usr/local/bin` | Locally compiled programs |
| `/etc` | System configuration files |
| `/home` | User home directories |
| `/root` | Root user home directory |
| `/var` | Variable data (logs, spools, caches) |
| `/var/log` | System log files |
| `/tmp` | Temporary files (cleared on reboot) |
| `/boot` | Kernel and bootloader files |
| `/lib` | Essential shared libraries |
| `/usr/share/doc` | Package documentation |

## Viewing File System Hierarchy

```bash
# Show current mounts (overwhelming output)
mount

# Show mounts in human-readable format with file system type
df -Th

# Show mount relationships (better output)
findmnt

# Show detailed mount information
findmnt -D

# Show mounts in tree format
findmnt --tree
```

## File Listing and Wildcards

```bash
# List files starting with 'f'
ls f*

# List files matching pattern with single character wildcard
ls f?le1     # Matches: file1, fale1, etc.

# List files in range
ls file[1-5]     # Matches: file1, file2, file3, file4, file5
ls file[1,3,5]   # Matches: file1, file3, file5

# Case-insensitive globbing (if shopt -s nocaseglob is set)
ls [A-Z]*
```

## File Operations

### Copy Files and Directories

```bash
# Copy file to directory
cp /etc/myfile /mydir

# Best practice: Always use trailing slash to ensure target is directory
cp /etc/myfile /mydir/

# Copy directory recursively
cp -R /etc /tmp

# Copy directory contents (preserves structure)
cp -a /mydir/ .

# Copy all files from directory (omit parent directory)
cp -a /mydir/. .

# Preserve all attributes (archive mode)
cp -a source destination
```

### Remove Files and Directories

```bash
# Remove file (typically aliased to 'rm -i' for safety)
rm filename

# Remove directory and contents
rm -rf directory

# Remove empty directory
rmdir directory

# Force remove without confirmation
rm -f filename
```

### Move and Rename Files

```bash
# Move/rename file
mv oldname newname

# Move file to directory
mv file /destination/

# Move multiple files
mv file1 file2 file3 /destination/

# Force move without confirmation
mv -f source destination
```

## Working with Links

### Hard Links

**Characteristics**:
- Points to same inode as original file
- Cannot span file systems
- Cannot link to directories
- Deletion of original doesn't affect link
- Both have equal status (same inode number)

```bash
# Create hard link
ln /etc/hosts .

# Create hard link with different name
ln /etc/hosts myhosts

# List inode numbers to verify hard link
ls -i

# Remove hard link (just removes one link, data persists if other links exist)
rm link
```

**Hard Link Use Cases**:
- Backup files with minimal disk space
- Maintain multiple references to same data
- Prevent accidental data loss
- Share configuration files between applications

### Symbolic (Soft) Links

**Characteristics**:
- Points to filename (path), not inode
- Can span file systems
- Can link to directories
- Points to inode of target file
- Deletion of original breaks link (dangling symlink)

```bash
# Create symbolic link to file
ln -s /etc/hosts .

# Create symbolic link to directory
ln -s /etc /tmp/etclink

# Create relative symbolic link
ln -sr ../../files/target.txt shortcut.txt

# Verify symbolic link
ls -l    # Shows link arrow (->)
file link    # Shows "symbolic link to..."

# Remove symbolic link (doesn't affect target)
rm symlink

# Remove symlink directory (symlink removed, target directory persists)
rm -f linkdir
```

**Symbolic Link Use Cases**:
- Create shortcuts to deeply nested files
- Link configuration files for easier access
- Switch between versions (e.g., /app/current -> /app/v1.0)
- Access external drives as local directories
- Deploy multiple configurations

## File System Operations

```bash
# List block devices and partitions
lsblk

# Show file system usage
df -h

# Show file system usage with type
df -Th

# Show inode usage
df -i

# Display mount information with relationships
findmnt

# Verify file system (check for errors)
fsck /dev/sda1

# Mount file system
mount /dev/sda1 /mnt

# Unmount file system
umount /mnt
umount /dev/sda1

# Mount all file systems in /etc/fstab
mount -a

# Verify /etc/fstab syntax
findmnt --verify
```

## Searching for Files

```bash
# Find files by name
find / -name "filename"

# Find files by type (f: file, d: directory, l: symlink)
find / -type f -name "*.conf"
find / -type d -name "config"

# Find by user
find / -user bob

# Find by group
find / -group users

# Find by permission
find / -perm 644

# Find recently modified (within last day)
find / -mtime -1

# Find larger than certain size
find / -size +100M

# Execute command on found files
find / -name "*.tmp" -exec rm {} \;

# Combine conditions (AND)
find / -type f -size +100M -mtime -1
```

## Working with File Paths

```bash
# Get full path
pwd    # Print working directory

# List files with full path
ls -d /etc/*/

# Create parent directories as needed
mkdir -p /deep/nested/directory/path

# Remove directory and all contents
rm -rf /directory/
```

## Tips and What to Remember

- **Trailing Slashes**: Always use trailing slashes (`/`) when copying/moving to directories to avoid ambiguity.
- **Hard vs Soft Links**: Hard links for backup and data protection; symbolic links for shortcuts and flexibility.
- **inode Awareness**: Understanding inodes helps troubleshoot storage issues and link behavior.
- **Globbing Patterns**: Master `*`, `?`, and `[range]` for efficient file operations.
- **Link Verification**: Use `ls -i` for inodes and `ls -l` to identify link types.
- **Find Command**: Practice find syntax; it's powerful for locating and managing files across the system.

## Critical Notes

- Hard links cannot be created for directories; always use symbolic links for directory references.
- Symbolic links that reference deleted files become "dangling symlinks" and should be cleaned up.
- The inode number is unique within a file system, not across file systems—relevant when copying between systems.

---

# Chapter 4: Working with Text Files

## Exam-Relevant Objectives

- Use grep and regular expressions to analyze text
- Cut, sort, and manipulate text from files
- Count lines, words, and characters in files
- Process text using common utilities

## Text Processing Utilities

### grep Command

```bash
# Search for pattern in file
grep "pattern" file.txt

# Case-insensitive search
grep -i "pattern" file.txt

# Invert match (show lines NOT matching)
grep -v "pattern" file.txt

# Count matching lines
grep -c "pattern" file.txt

# Show line numbers
grep -n "pattern" file.txt

# Show only matching part
grep -o "pattern" file.txt

# Extended regex
grep -E "pattern1|pattern2" file.txt

# Fixed string (no regex interpretation)
grep -F "literal.string" file.txt

# Recursive search in directory
grep -r "pattern" /etc/

# Show context (lines before and after)
grep -B2 -A2 "pattern" file.txt
```

### cut Command

```bash
# Extract first field (colon-delimited)
cut -f 1 -d : /etc/passwd

# Extract characters 1-5
cut -c 1-5 file.txt

# Extract fields 3 and 5
cut -f 3,5 -d : /etc/passwd

# Extract from field 2 onward
cut -f 2- -d : /etc/passwd

# Exclude delimiter lines
cut -d : -f 1 /etc/passwd | sort
```

### head and tail Commands

```bash
# Show first 5 lines
head -n 5 /etc/passwd

# Show first 10 lines (default)
head /etc/passwd

# Show last 2 lines
tail -n 2 /etc/passwd

# Show last 10 lines (default)
tail /etc/passwd

# Follow file as it grows (monitor logs)
tail -f /var/log/messages

# Combine head and tail
head -n 10 /etc/passwd | tail -n 5
```

### sort Command

```bash
# Sort alphabetically
sort file.txt

# Sort by third field (colon-delimited)
sort -k3 -t : /etc/passwd

# Sort numerically
sort -n file.txt

# Reverse sort
sort -r file.txt

# Sort and remove duplicates
sort -u file.txt

# Multiple sort keys
sort -k3,3n -k1,1 file.txt
```

### wc Command (Word Count)

```bash
# Count lines, words, and characters
wc file.txt

# Count lines only
wc -l file.txt

# Count words only
wc -w file.txt

# Count characters
wc -m file.txt
wc -c file.txt

# Count lines in multiple files
wc -l /var/log/*
```

### sed Command (Stream Editor)

```bash
# Delete specific line
sed -i '25d' ~/.ssh/known_hosts

# Replace text
sed 's/old/new/' file.txt
sed 's/old/new/g' file.txt    # Global replacement

# Delete lines matching pattern
sed -i '/pattern/d' file.txt

# Print only lines matching pattern
sed -n '/pattern/p' file.txt
```

## Regular Expressions

### Anchors

| Pattern | Meaning |
|---------|---------|
| `^pattern` | Pattern at start of line |
| `pattern$` | Pattern at end of line |
| `^$` | Empty line |

### Wildcards and Quantifiers

| Pattern | Meaning |
|---------|---------|
| `.` | Any single character |
| `*` | Zero or more of previous character |
| `+` | One or more of previous character (extended regex) |
| `?` | Zero or one of previous character (extended regex) |
| `{n}` | Exactly n occurrences |
| `{n,}` | n or more occurrences |
| `{n,m}` | Between n and m occurrences |

### Character Classes

| Pattern | Meaning |
|---------|---------|
| `[abc]` | Any one character in brackets |
| `[a-z]` | Any character in range (lowercase) |
| `[A-Z]` | Any character in range (uppercase) |
| `[0-9]` | Any digit |
| `[^abc]` | Any character NOT in brackets |

### Predefined Character Classes

| Pattern | Meaning |
|---------|---------|
| `[[:alpha:]]` | Any letter |
| `[[:digit:]]` | Any digit |
| `[[:alnum:]]` | Any letter or digit |
| `[[:space:]]` | Any whitespace |
| `[[:upper:]]` | Any uppercase letter |
| `[[:lower:]]` | Any lowercase letter |

### Practical Examples

```bash
# Find lines starting with 'root'
grep "^root" /etc/passwd

# Find lines ending with 'sh'
grep "sh$" /etc/passwd

# Find UID greater than 999
grep ":[0-9]{4}:" /etc/passwd

# Find any line with digits
grep -E "[0-9]" /etc/passwd

# Find consecutive spaces
grep "  " file.txt
```

## Tips and What to Remember

- **cut vs grep**: Use `cut` for structured data extraction; use `grep` for pattern matching.
- **sort Context**: Always know delimiter when sorting structured data (e.g., `/etc/passwd` uses `:`).
- **Pipeline Efficiency**: Chain commands with pipes: `head | grep | cut | sort | uniq`.
- **grep Flags**: `-i` for case-insensitive, `-v` for inverse, `-c` for count, `-n` for line numbers.
- **Regular Expression Scope**: Basic regex uses `\`, extended regex (`-E` or `egrep`) uses `+?{}|` without escaping.

---

# Chapter 5: Connecting to RHEL 10

## Exam-Relevant Objectives

- Log in and switch users in multiuser targets
- Access remote systems using SSH
- Securely transfer files between systems
- Boot, reboot, and shut down a system normally

## Switching Users

### Using su (Switch User)

```bash
# Start root subshell (without login environment)
su

# Open subshell as another user (without login environment)
su linda

# Start root login shell (with full login environment)
su -

# Open login shell as another user (with full login environment)
su - linda

# Execute single command as another user
su - linda -c "command"
```

**Best Practice**: Use `su -` instead of `su` to get full login environment and proper initialization.

### Using sudo

```bash
# Execute command with sudo privileges
sudo command

# Start root login shell
sudo -i

# Execute command as another user
sudo -u username command

# List available sudo commands
sudo -l

# Edit sudoers safely
sudo visudo

# Disable password prompt for command
sudo NOPASSWD: /usr/bin/ls
```

## Virtual Terminals and TTY Switching

| Key Combination | Terminal | Purpose |
|---|---|---|
| `Ctrl+Alt+F2` | TTY2 | Graphical Environment (GNOME/GDM) |
| `Ctrl+Alt+F3` | TTY3 | Text-based virtual terminal |
| `Ctrl+Alt+F4` | TTY4 | Text-based virtual terminal |
| `Ctrl+Alt+F5` | TTY5 | Text-based virtual terminal |
| `Ctrl+Alt+F6` | TTY6 | Text-based virtual terminal |

**Important**: The graphical session typically runs on TTY2 by default; text terminals are TTY3-TTY6.

## Rebooting and Shutting Down

```bash
# Reboot system
systemctl reboot

# Halt system (stop without powering off)
systemctl halt

# Power off system
systemctl poweroff

# Suspend system (standby)
systemctl suspend

# Hibernate system
systemctl hibernate

# Emergency reboot (no synchronization)
echo b > /proc/sysrq-trigger
```

**Use Cases**:
- `reboot`: Normal restart
- `poweroff`: Power off machine
- `halt`: Stop without power (useful for virtual machines)
- Emergency restart: Only when system is unresponsive

## SSH (Secure Shell)

### Basics

**SSH**: Secure method to gain remote access to other machines over network.

**SSHD**: Service (server process) that must be running on remote server at default port 22.

```bash
# Connect to remote server
ssh user@server

# Connect to specific port
ssh -p 2022 user@server

# Verbose mode (troubleshooting)
ssh -v user@server

# Enable X11 forwarding (for GUI applications)
ssh -X user@server    # More secure
ssh -Y user@server    # Trusted servers

# Execute remote command and return
ssh user@server "command"

# Check SSH connectivity
ssh -v user@server    # Shows detailed connection process
```

### SSH Configuration Files

**Client Configuration**: `/etc/ssh/ssh_config`

```bash
# Enable X11 forwarding globally
ForwardX11 yes

# Additional client options
ServerAliveInterval 60        # Send keepalive every 60 seconds
ServerAliveCountMax 3         # Close if no response after 3 keepalives
StrictHostKeyChecking no      # Auto-accept new host keys
User username                 # Default user
Port 22                       # Default port
```

### Removing Known Hosts

```bash
# Remove specific line from known_hosts
sed -i -e '25d' ~/.ssh/known_hosts

# Remove all known hosts for fresh start
rm ~/.ssh/known_hosts
```

## Secure File Transfer

### SCP (Secure Copy)

```bash
# Copy file from local to remote
scp /etc/hosts student@192.168.204.129:/tmp

# Copy file from remote to local
scp student@192.168.204.129:/etc/hosts /tmp

# Copy directory recursively
scp -r student@192.168.204.129:/etc /tmp

# Specify non-default port
scp -P 2022 file user@server:/destination

# Copy from one remote to another (via local)
scp user1@server1:/file user2@server2:/destination
```

### SFTP (SSH File Transfer Protocol)

```bash
# Open SFTP session
sftp student@server

# SFTP commands (from sftp> prompt)
ls              # List remote files
pwd             # Show remote working directory
lpwd            # Show local working directory
lcd /tmp        # Change local directory
cd /tmp         # Change remote directory

# Upload files
put /etc/hosts              # Upload to remote home
put /etc/hosts /tmp         # Upload to specific remote directory
put -r directory            # Upload directory recursively

# Download files
get /etc/hosts              # Download to local directory
get -r directory            # Download directory recursively

# Create remote directory
mkdir remotedir

# Remove remote file
rm remotefile

# Exit SFTP
exit
quit
bye
```

## SSH Key-Based Authentication

### Generating Keys

```bash
# Generate public/private key pair
ssh-keygen

# Generate with specific parameters
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N "passphrase"

# Generate ED25519 keys (preferred in RHEL 10)
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519
```

**Default Key Locations**:
- Private key: `~/.ssh/id_rsa` (or `id_ed25519`)
- Public key: `~/.ssh/id_rsa.pub` (or `id_ed25519.pub`)

### Copying and Using Keys

```bash
# Copy public key to remote server
ssh-copy-id user@server
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server

# Connect without password (if key copied)
ssh server2

# Keys are stored in remote ~/.ssh/authorized_keys
# Public key is written to ~/.ssh/authorized_keys on remote
```

### SSH Agent and Passphrase Caching

```bash
# Start SSH agent for current bash shell
ssh-agent /bin/bash

# Add passphrase to agent (cached for session)
ssh-add

# Add specific key
ssh-add ~/.ssh/id_ed25519

# List cached keys
ssh-add -l

# Remove all cached keys
ssh-add -D

# Remove specific key
ssh-add -d ~/.ssh/id_rsa
```

## rsync (Remote Synchronization)

```bash
# Copy file between directories
rsync localDir/src/file1.txt localDir/dest/

# Synchronize local directories
rsync -r localDir/src/ localDir/dest/

# Synchronize local to remote
rsync -r localDir/src/ user@server:remoteDir/dest/

# Synchronize remote to local
rsync -r user@server:remoteDir/src/ localDir/dest/

# Archive mode (preserves permissions, timestamps, symlinks)
rsync -a localDir/src/ user@server:remoteDir/dest/

# Compression enabled
rsync -az localDir/src/ user@server:remoteDir/dest/

# Show progress during transfer
rsync -a --progress localDir/src/ user@server:remoteDir/dest/

# Delete files in destination not in source
rsync -a --delete localDir/src/ user@server:remoteDir/dest/

# Dry run (test without copying)
rsync -an localDir/src/ user@server:remoteDir/dest/

# Preserve hard links
rsync -H source destination

# Preserve ACLs and extended attributes
rsync -AX source destination

# Full archive with all options
rsync -aHAX localDir/src/ user@server:remoteDir/dest/

# Show statistics after transfer
rsync -a --stats source destination
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **SSH Version** | 8.x | **9.9** (stricter host-key permissions) |
| **SSH Key Group** | ssh_keys group | **SUID-based ssh-keysign** |
| **Preferred Key Type** | RSA (3072-bit) | **ED25519** (default generated) |
| **SSH Configuration** | /etc/ssh/ssh_config | Same location |
| **TTY Session Management** | Same | Same |
| **System Shutdown** | systemctl | Same |
| **File Transfer** | scp, sftp, rsync | Enhanced rsync options |

## Tips and What to Remember

- **SSH Security**: Always use SSH keys instead of passwords; enable `PasswordAuthentication no` in sshd_config on servers.
- **Key Management**: Generate ED25519 keys in RHEL 10 (smaller, faster, more secure than RSA).
- **SSH Agent**: Use ssh-agent to cache passphrases for session duration; improves workflow without sacrificing security.
- **rsync Efficiency**: Use `rsync -a --delete` for mirroring; use `--progress` to monitor large transfers.
- **Port Numbers**: Default SSH is 22; use non-standard ports (>1024) for additional obscurity (not security).

---

# Chapter 6: User and Group Management

## Exam-Relevant Objectives

- Create, delete, and modify local user accounts
- Create, delete, and modify local groups and group memberships
- Change passwords and adjust password aging policies
- Configure privileged access using sudo and su
- Manage user and group defaults

## User Account Basics

### User Account Files

**/etc/passwd**: User account information

```
username:password:UID:GID:GECOS:home_directory:shell
root:x:0:0:root:/root:/bin/bash
student:x:1000:1000:Student User:/home/student:/bin/bash
```

**Fields**:
- **username**: Account name (up to 32 characters)
- **password**: `x` (password stored in /etc/shadow), `*` (account locked)
- **UID**: User ID (0=root, 1-999=system accounts, 1000+=regular users)
- **GID**: Primary group ID (see /etc/group)
- **GECOS**: Comment field (full name, contact info)
- **home_directory**: User's home directory
- **shell**: User's login shell (/bin/bash, /sbin/nologin, etc.)

**/etc/shadow**: Password and aging information

```
username:password:last_change:min_age:max_age:warning:inactive:expire:reserved
student:$6$...hash...:19000:0:99999:7:::
```

**Fields**:
- **username**: Account name (must match /etc/passwd)
- **password**: Hashed password (or `!` if locked, `!!` if never set)
- **last_change**: Days since epoch (1970-01-01) when password changed
- **min_age**: Minimum days between password changes
- **max_age**: Maximum days until password expires
- **warning**: Days before expiration to warn user
- **inactive**: Days after expiration account remains inactive
- **expire**: Account expiration date (days since epoch)
- **reserved**: Reserved field (unused)

### User Types

- **Normal Users**: UIDs ≥ 1000, for people
- **System Users**: UIDs 1-999, for services

## Creating and Managing Users

### Using useradd

```bash
# Create user with home directory
useradd -m username

# Create user with specific UID
useradd -m -u 1201 username

# Create user with specific shell
useradd -m -s /sbin/nologin username

# Create user in specific groups (primary + secondary)
useradd -m -u 1201 -g primary_group -G secondary_group1,secondary_group2 linda

# Create user with custom home directory
useradd -m -d /home/custom/path username

# Create system user (no home directory)
useradd -r -s /sbin/nologin systemuser
```

### Setting User Passwords

```bash
# Set password interactively
passwd username

# Set password non-interactively (scripting)
echo "password" | passwd --stdin username

# Lock account
passwd -l username

# Unlock account
passwd -u username

# Disable password login (allow key-based only)
passwd -d username
```

### Managing User Properties

```bash
# Modify user
usermod -m -d /new/home/path username    # Change home directory
usermod -s /sbin/nologin username        # Change shell
usermod -L username                      # Lock account
usermod -U username                      # Unlock account
usermod -aG group1,group2 username       # Add to secondary groups (-a = append)
usermod -G group1,group2 username        # Replace all groups (overwrites)

# Change user comment (GECOS field)
usermod -c "New Comment" username

# Change user ID
usermod -u 1500 username
```

### Password Aging

```bash
# Set password policy
passwd -n 30 -w 3 -x 90 username
# -n: minimum age (30 days)
# -w: warning (3 days before expiry)
# -x: maximum age (90 days)

# Using chage (more flexible)
chage -l username                      # List password settings
chage -d 0 username                    # Force password change on next login
chage -E 2025-12-31 username           # Set expiration date
chage -M 90 username                   # Max days before password expires
chage -m 1 username                    # Min days before password can be changed
chage -W 7 username                    # Warning days before expiration
chage -I 3 username                    # Inactive days after expiration
```

### Viewing User Information

```bash
# Display user info
id username
id linda    # Shows: uid=1201(linda) gid=1000(sales) groups=1000(sales),1001(ops)

# List groups user belongs to
groups username

# Change effective primary group (for current session)
newgrp groupname
# Use exit to reset to original primary group

# Show all users
cut -d: -f1 /etc/passwd

# Show users with specific UID range
awk -F: '$3 >= 1000 && $3 <= 1005 {print $1}' /etc/passwd
```

## Deleting Users

```bash
# Delete user account (keep home directory)
userdel username

# Delete user and home directory
userdel -r username

# Find files owned by deleted user
find / -uid 1201
```

## User Defaults Configuration

### /etc/login.defs

Default values for user creation; applied when useradd runs:

```bash
# Password policy defaults
PASS_MAX_DAYS 99999      # Default max days
PASS_MIN_DAYS 0          # Default min days
PASS_WARN_AGE 7          # Default warning days

# User ID ranges
UID_MIN 1000             # Start of regular user UIDs
UID_MAX 60000            # End of regular user UIDs
SYS_UID_MIN 201          # Start of system UIDs
SYS_UID_MAX 999          # End of system UIDs

# Group ID ranges
GID_MIN 1000
GID_MAX 60000
SYS_GID_MIN 201
SYS_GID_MAX 999

# Home directory template
CREATE_HOME yes
USERGROUPS_ENAB yes      # Create private group for each user
```

### /etc/default/useradd

Additional useradd defaults:

```bash
GROUP=1000              # Default group for users
HOME=/home              # Home directory location
SHELL=/bin/bash         # Default shell
SKEL=/etc/skel          # Skeleton directory (contents copied to new home)
```

### /etc/skel/

Skeleton directory: contents copied to each new user's home directory.

```bash
# Common skeleton files
/etc/skel/.bashrc
/etc/skel/.bash_profile
/etc/skel/.bash_logout
```

## Group Management

### Understanding Groups

Groups provide:
- Primary group: Listed in GID field of /etc/passwd
- Secondary groups: User belongs to multiple groups

### Group Files

**/etc/group**: Group definitions

```
groupname:group_password:GID:members
wheel:x:10:student,linda
sales:x:1001:bob,mary,jane
```

**/etc/gshadow**: Group password information

```
groupname:password:admins:members
```

### Creating and Managing Groups

```bash
# Create group
groupadd groupname

# Create group with specific GID
groupadd -g 2001 groupname

# Modify group
groupmod -n newname oldname              # Rename group
groupmod -g 2001 groupname               # Change GID
groupmod -g 2001 -o groupname            # Allow duplicate GID (-o)

# Delete group
groupdel groupname

# Add user to group
usermod -aG groupname username

# Add user to multiple groups
usermod -aG group1,group2,group3 username

# Remove user from group (usermod removes all groups, then re-adds)
usermod -G group1,group3 username        # User removed from group2
```

### Querying Group Membership

```bash
# Show groups user belongs to
groups username

# Show all users in specific group
getent group groupname

# List groups for user (secondary groups only)
id -G username

# Using lid (if installed)
lid username                    # Shows groups for user
lid -g groupname                # Shows users in group

# Using groupmems
groupmems -g groupname -l       # List members of group as secondary members
```

## Privileged Access

### sudo Configuration

**File**: `/etc/sudoers` (edit with visudo only)

```bash
# Allow user to run all commands
linda ALL=(ALL) ALL

# Allow user to run specific commands
linda ALL=/usr/bin/useradd, /usr/bin/passwd

# Allow commands without password
%wheel ALL=(ALL) NOPASSWD: ALL

# Prevent root password change
linda ALL=/usr/bin/passwd, !/usr/bin/passwd root

# Allow group to run commands
%group_name ALL=/usr/bin/command

# Set password timeout
Defaults timestamp_timeout=240

# Best practice: Create separate files in /etc/sudoers.d/
sudo sh -c 'echo "bob ALL=(ALL) ALL" > /etc/sudoers.d/bob'
chmod 440 /etc/sudoers.d/bob
```

### sudo Usage

```bash
# Run command with sudo
sudo command

# Start interactive root shell
sudo -i

# List available sudo commands
sudo -l

# Run command as another user
sudo -u username command

# Reset sudo timeout (requires password again)
sudo -k

# Use pipe with sudo (special syntax required)
sudo sh -c "rpm -qa | grep ssh"
```

### PolicyKit (Fallback)

When sudo is not available:

```bash
# Use pkexec as alternative
pkexec visudo    # Edit sudoers if sudo is broken
```

## User Environment

### Login Shell Initialization

When user logs in, these files load in order:

1. `/etc/profile` (for all users)
2. `~/.bash_profile` (user-specific)
3. `/etc/bashrc` (subshells)
4. `~/.bashrc` (user-specific subshells)

### Environment Variables

```bash
# Display all environment variables
env

# Export variable for current session
export MYVAR="value"

# Set variable permanently (add to ~/.bashrc or ~/.bash_profile)
echo 'export MYVAR="value"' >> ~/.bashrc

# Set login shell initial values
# Add to ~/.bash_profile:
# PATH=$PATH:$HOME/bin
# export PATH
```

### Shell Startup Files

**~/.bash_profile** (login shell):
```bash
# Executed for login shells
# Usually sources ~/.bashrc
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
```

**~/.bashrc** (interactive non-login shells):
```bash
# Executed for subshells and interactive non-login shells
# Set aliases, functions, prompt settings
alias ll='ls -l'
export HISTSIZE=1000
PS1='[\u@\h \W]\$ '
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **sudo Package** | Standard | Enhanced security roles |
| **Default Shell** | bash (5.x) | bash (5.2+) |
| **Home Directory Template** | /etc/skel | Same |
| **User ID Defaults** | 1000+ | Same |
| **System Users** | 201-999 | Same |
| **Password Hashing** | SHA-512 | SHA-512 (with improvements) |
| **New sudo Roles** | None | **Ansible System Roles for sudo** |

## Tips and What to Remember

- **Always use visudo**: Never edit /etc/sudoers directly; use `visudo` which validates syntax.
- **Best Practice for sudoers.d**: Add custom sudo rules to `/etc/sudoers.d/` instead of modifying main file.
- **Home Directory Ownership**: Verify `chown username:groupname /home/username` after manual creation.
- **Password Aging**: Use `chage -d 0 username` to force password change on next login.
- **Group Membership**: Remember `-aG` to append; `-G` alone replaces all groups.
- **UID Ranges**: System accounts (1-999), regular users (1000+); respecting ranges avoids conflicts.

---

# Chapter 7: Permissions Management

## Exam-Relevant Objectives

- List, set, and change standard ugo/rwx permissions
- Create hard and soft links
- Manage default file permissions with umask
- Understand and apply advanced permissions (SUID, SGID, sticky bit)
- Manage extended attributes and ACLs

## File Ownership

### Displaying Ownership

```bash
# Show file ownership
ls -l /etc/hosts
# -rw-r--r--. 1 root root 371 Nov 27 10:30 /etc/hosts
#             ^           ^^^^
#         permissions   owner group

# Show inode number (for hard links)
ls -i /etc/hosts

# Show all attributes including extended
ls -la file.txt
```

### Changing Ownership

```bash
# Change owner only
chown bob file.txt

# Change owner and group
chown bob:users file.txt
chown bob.users file.txt

# Change group only
chown :users file.txt
chown .users file.txt

# Change group (alternative)
chgrp users file.txt

# Recursive change
chown -R bob /home/account
chgrp -R users /home/account

# Preserve links when changing
chown -h user:group symlink
```

### Finding Files by Ownership

```bash
# Find files owned by user
find / -user bob

# Find files owned by group
find / -group users

# Find files not owned by specific user
find / -not -user bob
```

## Basic Permissions

### Permission Overview

| Permission | Files | Directories |
|-----------|-------|-------------|
| **Read (r=4)** | View file contents | List directory contents |
| **Write (w=2)** | Modify file contents | Create/delete files in directory |
| **Execute (x=1)** | Run file as program | Change into directory (cd) |

### Permission Notation

```bash
# Absolute mode (numeric)
chmod 755 file          # rwxr-xr-x
chmod 644 file          # rw-r--r--
chmod 700 directory     # rwx------

# Relative mode (symbolic)
chmod u+x file          # Add execute for owner
chmod g-w file          # Remove write for group
chmod o-r file          # Remove read for others
chmod a+x file          # Add execute for all

# Relative mode with operators
# +: add permission
# -: remove permission
# =: set exactly these permissions

chmod u=rwx,g=rx,o=rx file     # Set permissions exactly
chmod u+x,g-w,o-r file         # Multiple changes

# Recursive change
chmod -R 755 /directory
chmod -R a+X /directory         # Add execute only to directories
```

### Setting Default Permissions with umask

**Default Permissions**:
- Files: 666 (rw-rw-rw-)
- Directories: 777 (rwxrwxrwx)

**umask** (subtracted from defaults):

```bash
# Show current umask
umask

# Set umask temporarily
umask 022               # Typical: files=644, directories=755

# Set umask permanently
# In /etc/profile (all users):
umask 022

# In ~/.bashrc (user-specific):
echo 'umask 022' >> ~/.bashrc

# Create umask script in /etc/profile.d/
cat > /etc/profile.d/umask.sh << 'EOF'
umask 022
EOF
chmod 644 /etc/profile.d/umask.sh
```

**Calculation**:
- File: 666 - 022 = 644 (rw-r--r--)
- Directory: 777 - 022 = 755 (rwxr-xr-x)

## Advanced Permissions

### Set User ID (SUID)

**Purpose**: File executes with permissions of file owner (not executor).

**Typical Use**: Allow non-root users to run privileged commands (e.g., `sudo`, `passwd`).

```bash
# Add SUID
chmod u+s /path/to/file
chmod 4755 /path/to/file      # Absolute mode

# Display SUID
ls -l /usr/bin/sudo
# -rwsr-xr-x  1 root  root  ...
#  ^
# SUID bit set (s in owner execute position)

# Remove SUID
chmod u-s /path/to/file
```

### Set Group ID (SGID)

**On Files**: Execute with permissions of group owner.

**On Directories**: New files inherit parent directory's group; new directories inherit and propagate SGID.

```bash
# Add SGID (file)
chmod g+s /path/to/file
chmod 2755 /path/to/file

# Add SGID (directory)
chmod g+s /data/shared
chmod 2755 /data/shared

# Display SGID
ls -l /data/shared
# drwxr-sr-x  2 root  users  ...
#        ^
# SGID bit set (s in group execute position)

# All files created in /data/shared inherit 'users' group
touch /data/shared/newfile
ls -l /data/shared/newfile
# -rw-r--r--. 1 user users  ...  <- group is 'users'
```

### Sticky Bit

**Purpose**: Only owner (or root) can delete file; prevents users from deleting others' files.

**Typical Use**: `/tmp`, `/var/tmp` directories.

```bash
# Add sticky bit
chmod +t /tmp
chmod 1777 /tmp

# Display sticky bit
ls -ld /tmp
# drwxrwxrwt  10 root  root  ...
#         ^
# Sticky bit set (t in others execute position)

# Remove sticky bit
chmod -t /tmp
```

### Special Permissions Summary

| Permission | Numeric | File Effect | Directory Effect |
|-----------|---------|-------------|------------------|
| **SUID** | 4000 | Execute as owner | Ignored |
| **SGID** | 2000 | Execute as group | New files inherit group; SGID propagates |
| **Sticky** | 1000 | Ignored | Only owner can delete files |

### Practical Example: Shared Directory

```bash
# Create shared directory with proper permissions
mkdir /data/sales
chown root:sales /data/sales
chmod 2770 /data/sales          # drwxrws---
# SGID (2) + User(7) + Group(7) + Others(0)

# Verify
ls -ld /data/sales
# drwxrws---. 2 root sales 6 Nov 27 10:30 /data/sales

# Now when users in 'sales' group create files:
cd /data/sales
touch myfile
ls -l myfile
# -rw-r-----. 1 user sales  0 Nov 27 10:31 myfile
# Group is 'sales' due to SGID on directory
```

## Extended Attributes

### File Attributes (chattr/lsattr)

```bash
# Common attributes:
# i: immutable (cannot be modified or deleted)
# a: append-only (can only append to file)
# A: no atime updates (improves performance)
# c: compress (file is automatically compressed)
# d: no dump (not backed up by dump command)

# Set immutable
chattr +i /root/myfile

# Verify attribute
lsattr /root/myfile
# ----i----------- /root/myfile

# Remove attribute
chattr -i /root/myfile

# Set multiple attributes
chattr +ia file.txt
```

## Permissions Troubleshooting

### Fixing Common Permission Issues

```bash
# Check permission of file
stat file.txt

# Check permission of file and parent directories
namei -l /path/to/file

# Diagnose "Permission denied" errors
# 1. Check file permissions: ls -l
# 2. Check user group membership: id username
# 3. Check parent directory permissions: ls -ld /parent/directory

# Correct common issues
chmod 644 /etc/hosts                    # Regular file (owner can write)
chmod 755 /etc/ssh                      # Directory (readable, searchable)
chmod 600 ~/.ssh/id_rsa                 # Private key (owner only)
chmod 644 ~/.ssh/authorized_keys        # Public keys (readable by SSH)
chmod 700 ~/.ssh                        # SSH directory (owner only)
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **umask Defaults** | 0022 | Same |
| **SUID/SGID/Sticky** | Standard | Same |
| **ACL Support** | Standard | Enhanced |
| **Extended Attributes** | Standard | Same |
| **Default Permissions** | Same | Same |

## Tips and What to Remember

- **Absolute vs Relative Mode**: Use relative mode for clarity (`chmod u+x`); use absolute for scripts (`chmod 755`).
- **Recursive Changes**: Use `chmod -R` carefully; use `chmod -R a+X` to add execute only to directories.
- **SUID/SGID Risks**: Minimize use; audit regularly. Check: `find / -perm /4000`
- **Sticky Bit**: Essential for `/tmp`; prevents accidental deletion of others' files.
- **Permission Priority**: User > Group > Others; most restrictive permission applies.
- **Private Key Permissions**: Always `chmod 600 ~/.ssh/id_rsa`; SSH refuses to use if group/others readable.

---

# Chapter 8: Configuring Network

## Exam-Relevant Objectives

- Configure IPv4 and IPv6 addresses
- Configure hostname and hostname resolution
- Configure network services to start automatically at boot
- Validate network configuration

## Network Management in RHEL 10

**NetworkManager**: Primary network management service in RHEL 10.

```bash
# Check NetworkManager status
systemctl status NetworkManager

# Network configuration location
/etc/NetworkManager/system-connections/
```

## Network Interface Naming

### RHEL 10 Changes (Deprecated Legacy Naming)

In RHEL 10, legacy `eth0`, `eth1` naming is deprecated. All systems use predictable interface names.

**Naming Scheme**:
- **en**: Ethernet
- **wl**: WLAN
- **ww**: WWAN

**Examples**:
- `ens3`: Onboard Ethernet
- `enp3s0`: PCI slot 3, function 0
- `enx001122334455`: MAC address-based

**RHEL 9 vs RHEL 10**:

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Legacy eth0 support** | Supported (net.ifnames=0) | **Deprecated (no longer available)** |
| **Predictable Names** | Default | **Enforced** |
| **Network Config Files** | /etc/sysconfig/network-scripts/ (deprecated) | /etc/NetworkManager/system-connections/ |

## Validating Network Configuration

### Checking IP Addresses

```bash
# Show all interfaces and addresses
ip addr show
ip a s
ip a

# Show specific interface
ip addr show ens160

# Show only IPv4 addresses
ip -4 addr show

# Show only IPv6 addresses
ip -6 addr show

# Show link state
ip link show
ip link show ens160

# Show statistics
ip -s link show
```

### Checking Routing

```bash
# Show routing table
ip route show
ip r

# Show IPv6 routes
ip -6 route show

# Show default gateway
ip route show | grep default
```

### Checking Ports and Services

```bash
# Show listening ports (TCP)
ss -lt
ss -tln    # Numeric (no name resolution)

# Show listening ports (UDP)
ss -ul

# Show all listening ports (TCP + UDP)
ss -tul

# Show connection state
ss -s      # Summary

# Alternative (netstat)
netstat -tul
```

## Temporary Network Configuration

### Using ip Command

```bash
# Add IP address temporarily
ip addr add 10.0.0.10/24 dev ens160

# Remove IP address
ip addr del 10.0.0.10/24 dev ens160

# Bring interface up
ip link set dev ens160 up

# Bring interface down
ip link set dev ens160 down

# Set MTU
ip link set dev ens160 mtu 1500

# Note: All changes revert on reboot; use NetworkManager for persistent config
```

## Persistent Network Configuration with nmcli

### Connection Management

```bash
# List all connections
nmcli conn show

# Show details of specific connection
nmcli conn show ens160

# Show active devices
nmcli dev status

# List all devices
nmcli dev

# Bring connection up
nmcli conn up ens160

# Bring connection down
nmcli conn down ens160

# Reload connection files
nmcli conn reload
```

### Creating Connections

```bash
# Create DHCP connection
nmcli conn add con-name dhcp type ethernet ifname ens160 ipv4.method auto

# Create static IP connection
nmcli conn add con-name static ifname ens160 \
  type ethernet \
  ipv4.method manual \
  ipv4.addresses 10.0.0.10/24 \
  ipv4.gateway 10.0.0.1 \
  autoconnect no

# Activate connection
nmcli conn up static
```

### Modifying Connections

```bash
# Modify DNS servers
nmcli conn mod static ipv4.dns "10.0.0.10"

# Add additional DNS server
nmcli conn mod static +ipv4.dns "8.8.8.8"

# Change IP address
nmcli conn mod static ipv4.addresses "10.0.0.100/24"

# Add additional IP address
nmcli conn mod static +ipv4.addresses "10.0.0.40/24"

# Set automatic connection
nmcli conn mod static connection.autoconnect yes

# Prevent automatic connection
nmcli conn mod static connection.autoconnect no

# Apply changes
nmcli conn reload
nmcli conn up static
```

## Persistent Network Configuration with nmtui

```bash
# Launch interactive network configuration
nmtui

# Options in nmtui:
# 1. Edit a connection (add/modify/delete)
# 2. Activate a connection (bring up/down)
# 3. Set system hostname
```

## Network Configuration Files

### Connection Files Location

```bash
# Directory containing connection files
/etc/NetworkManager/system-connections/

# Example connection file
/etc/NetworkManager/system-connections/ens160.nmconnection

# File format (INI-style)
[connection]
id=ens160
type=802-3-ethernet
interface-name=ens160

[ipv4]
method=manual
addresses=10.0.0.10/24
gateway=10.0.0.1
dns=10.0.0.10
```

**RHEL 10 Changes**: Moved from legacy `/etc/sysconfig/network-scripts/` to NetworkManager's keyfile format in `/etc/NetworkManager/system-connections/`.

## Hostname Configuration

### Setting Hostname

```bash
# Show current hostname
hostnamectl status

# Set hostname permanently
hostnamectl set-hostname myhost.example.com

# Verify change (may require new shell)
hostnamectl status

# Manual method (edit file)
vim /etc/hostname
# Add: myhost.example.com

# Old method (still works)
nmtui    # Choose "Set system hostname"
```

### Hostname Resolution

#### /etc/hosts File

```bash
# Add static hostname mappings
cat >> /etc/hosts << EOF
10.0.0.10 server1.example.com server1
10.0.0.20 server2.example.com server2
EOF

# Format: IP hostname FQDN aliases
# FQDN must be second field (after IP)
```

#### DNS Configuration

```bash
# Configure DNS with nmcli
nmcli conn mod ens160 ipv4.dns "8.8.8.8 1.1.1.1"
nmcli conn mod ens160 ipv4.ignore-auto-dns yes

# View DNS resolution order
getent hosts server.example.com

# Test DNS resolution
nslookup server.example.com
dig server.example.com

# DNS resolution configuration
cat /etc/resolv.conf   # Generated by NetworkManager
cat /etc/nsswitch.conf # Resolution order: files dns myhostname
```

## Network Validation

```bash
# Test connectivity
ping -c 3 server.example.com

# Trace route to destination
traceroute server.example.com

# Check DNS resolution
nslookup server.example.com
dig server.example.com

# Test TCP connectivity to port
nc -zv server.example.com 22

# Check listening ports
ss -tul

# View ARP table
ip neigh show

# Show MAC addresses
ip link show
```

## Troubleshooting Network Issues

```bash
# Check interface status
nmcli device status

# Show connection details
nmcli connection show ens160

# Check for errors
journalctl -u NetworkManager

# Restart NetworkManager
systemctl restart NetworkManager

# Reload connections without restart
nmcli conn reload

# Reset interface
nmcli device disconnect ens160
nmcli device connect ens160
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Default Network Manager** | NetworkManager | Same |
| **Legacy eth0 Naming** | Supported | **Deprecated** |
| **ifcfg Configuration** | /etc/sysconfig/network-scripts/ | /etc/NetworkManager/system-connections/ (keyfile format) |
| **Teamd Networking** | Supported | **Removed** |
| **nftables Version** | 1.0.x | **1.1.1** (improved) |
| **Predictable Names** | Default | Enforced |
| **DNS-over-HTTPS** | No | **Yes (DoH/DoT)** |

## Tips and What to Remember

- **nmcli vs nmtui**: Use `nmtui` for learning; use `nmcli` for scripting.
- **Persistent Configuration**: Always use nmcli/nmtui; never edit NetworkManager files directly.
- **DNS Configuration**: Set with nmcli to persist; avoid editing `/etc/resolv.conf` directly.
- **Hostname Format**: Use FQDN (server.example.com) instead of short names.
- **Network Validation**: Always test with `ping`, `ss`, and `ip addr show` after changes.
- **RHEL 10 Migration**: Expect to configure networking differently than RHEL 9 if upgrading.

---

# Chapter 9: Managing Software

## Exam-Relevant Objectives

- Configure access to RPM repositories
- Install and remove RPM software packages
- Update software packages
- Work with package groups
- Understand package modules (RHEL 9) and Flatpak packages (RHEL 10)

## Repository Management in RHEL 10

### Registering with Red Hat (New Method in RHEL 10)

```bash
# New RHEL 10 method: Red Hat Connect (rhc)
rhc connect

# Follow prompts to enter Red Hat account
# System connects to Red Hat Hybrid Cloud Console
# Subscriptions automatically available

# Verify connection status
rhc status
```

### Traditional Registration Method (Still Supported)

```bash
# Register system
subscription-manager register

# List available subscriptions
subscription-manager list --available

# Auto-attach subscriptions
subscription-manager attach --auto

# List consumed subscriptions
subscription-manager list --consumed

# Unregister system
subscription-manager unregister
```

### Repository Configuration

```bash
# List enabled repositories
dnf repolist

# List all repositories (including disabled)
dnf repolist all

# Enable repository
dnf config-manager --set-enabled repository-name
dnf config-manager --enable repository-name

# Disable repository
dnf config-manager --set-disabled repository-name
dnf config-manager --disable repository-name

# Add custom repository
dnf config-manager --add-repo=http://reposerver.example.com/BaseOS

# Repository files
ls /etc/yum.repos.d/

# Manual repository file creation
cat > /etc/yum.repos.d/custom.repo << 'EOF'
[custom-repo]
name=Custom Repository
baseurl=http://reposerver.example.com/repos
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
EOF
```

## Using dnf (Package Manager)

### Package Search and Information

```bash
# Search for packages
dnf search nmap

# Search all fields (name, summary, description)
dnf search all nmap

# Find package providing specific file
dnf provides /usr/bin/python3
dnf whatprovides /usr/bin/python3

# Get package information
dnf info nmap

# Show package dependencies
dnf depends nmap
dnf repoquery --depends nmap
```

### Installing and Removing Packages

```bash
# Install package
dnf install nmap

# Install multiple packages
dnf install nmap git vim

# Install without confirmation
dnf install -y nmap

# Install package group
dnf group install "Basic Web Server"

# Remove package
dnf remove nmap

# Autoremove unused dependencies
dnf autoremove

# Remove package and dependents
dnf remove -y nmap

# Check what would be installed without installing
dnf install --setopt=tsflags=test nmap
```

### Updating Packages

```bash
# Update specific package
dnf update nmap

# Update all packages
dnf update

# Check for available updates
dnf check-update

# Update with security fixes only
dnf update --security

# Kernel updates (new version installed, old kept)
dnf update kernel
```

### Package Information and History

```bash
# List installed packages
dnf list installed

# List available packages
dnf list available

# Check if specific package is installed
dnf list nmap

# Show package history
dnf history

# Undo last transaction
dnf history undo last

# Redo transaction
dnf history redo transaction-id

# Show transaction details
dnf history info transaction-id
```

## Working with Package Groups

```bash
# List all package groups
dnf group list

# List available groups
dnf group list available

# Show group details
dnf group info "Basic Web Server"

# Install group
dnf group install "Basic Web Server"

# Remove group
dnf group remove "Basic Web Server"

# List groups with packages
dnf group list --verbose
```

## RHEL 10 Changes: Application Streams

### RHEL 9 vs RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Application Streams** | dnf modules (RPM modularity) | **RPM direct OR Flatpak** |
| **Module Syntax** | `dnf module enable` | **Removed** |
| **Available Versions** | Via dnf modules | Via separate software collections or Flatpak |
| **Python Version** | 3.9, 3.11 (via modules) | **3.12** (direct RPM) |
| **Perl Version** | 5.32 (via modules) | **5.40** (direct RPM) |

**RHEL 10 Approach**: Instead of modules, applications are available as:
1. Direct RPM packages (modern versions)
2. Software Collections (if backwards compatibility needed)
3. Flatpak containers (sandboxed, distribution-independent)

### Checking Application Versions

```bash
# RHEL 10: Direct version check
python3 --version

# List available versions
dnf list python3*

# Install specific version if multiple available
dnf install python3.11    # If 3.11 still available
```

## Working with Flatpak

```bash
# Install Flatpak support
dnf install flatpak

# Search for Flatpak apps
flatpak search firefox

# Install Flatpak application
flatpak install flathub org.mozilla.firefox

# List installed Flatpaks
flatpak list --app

# Run Flatpak application
flatpak run org.mozilla.firefox

# Remove Flatpak application
flatpak uninstall org.mozilla.firefox

# Update Flatpak applications
flatpak update
```

## RPM Package Management (Low-level)

### Querying RPM Database

```bash
# Query installed packages
rpm -qa                          # All packages
rpm -qa | grep nmap              # Search for specific
rpm -q nmap                      # Query specific package

# List files in package
rpm -ql nmap

# Show package information
rpm -qi nmap

# Search which package provides file
rpm -qf /usr/bin/nmap

# Check for differences between installed and package
rpm -V nmap
```

### Installing/Removing RPM Files

```bash
# Install local RPM file
rpm -i package.rpm
rpm -ivh package.rpm    # Verbose, with hash marks

# Upgrade package
rpm -U package.rpm

# Freshen (upgrade if installed)
rpm -F package.rpm

# Remove package
rpm -e package-name

# Force remove (not recommended)
rpm -e --nodeps package-name
```

## Repository Security

### GPG Key Management

```bash
# Import GPG key
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

# List imported keys
rpm -qa gpg-pubkey*

# Verify package signature
rpm -K package.rpm

# Check if package is signed
rpm --checksig package.rpm
```

### Disabling GPG Check (Not Recommended)

```bash
# Temporarily disable GPG check
dnf install --setopt=gpgcheck=0 package

# Disable for specific repository
echo 'gpgcheck=0' >> /etc/yum.repos.d/custom.repo
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **dnf Version** | 4.x | **5.x** |
| **Application Streams** | dnf modules | **RPM direct or Flatpak** |
| **Subscription Manager** | subscription-manager | **rhc (Red Hat Connect)** |
| **Python** | 3.9, 3.11 (via modules) | **3.12** (direct) |
| **Perl** | 5.32 | **5.40** |
| **Go** | 1.18, 1.19 | **1.23** |
| **GCC** | 11.x, 12.x | **14.2** |
| **Flatpak Support** | Limited | **Full support** |
| **Firefox Package** | Available | **Only via Flatpak** |

## Tips and What to Remember

- **dnf vs rpm**: Use `dnf` for dependency resolution; use `rpm` for low-level package queries.
- **Caching**: dnf caches metadata; use `dnf clean all` if repositories change.
- **Group Installation**: Groups provide convenient multi-package installation; check contents first with `dnf group info`.
- **Update Strategy**: Regular `dnf update` is safe; always test in non-production first.
- **RHEL 10 Migration**: Expect different application availability; Flatpak becomes more important for GUI apps.

---

# Chapter 10: Managing Processes

## Exam-Relevant Objectives

- Identify CPU/memory-intensive processes and kill processes
- Adjust process scheduling and priorities
- Manage tuning profiles
- Use top and other process monitoring tools

## Viewing Running Processes

### ps Command

```bash
# Show processes for current user
ps

# Show all processes (BSD format)
ps aux
# USER      PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
# root        1  0.0  0.1 225140  9248 ?        Ss   10:30   0:01 /usr/lib/systemd/systemd

# Show process tree (hierarchical)
ps forest
ps -ef --forest

# Show all processes (System V format)
ps -ef

# Show long format
ps -l

# Show threads
ps -L

# Filter by user
ps -u linda

# Filter by process name
ps -C apache

# Show processes except specific user
ps -U root -u root -N
```

### top Command (Interactive)

```bash
# Start top (interactive monitoring)
top

# Top commands (while running):
q            # Quit top
h            # Help
M            # Sort by memory usage
P            # Sort by CPU usage
T            # Sort by running time
u            # Show processes for specific user
k            # Kill process (prompts for PID and signal)
r            # Renice process (change priority)
```

### Process Listing Examples

```bash
# Show CPU-intensive processes
ps aux --sort=-%cpu | head

# Show memory-intensive processes
ps aux --sort=-%mem | head

# Find specific process
pgrep -a httpd

# Find PID of process
pidof httpd

# Show process details
ps -o pid,ppid,cmd,nice,stat -p PID
```

## Process Priority and Scheduling

### Understanding Priority

**Nice Value**: -20 (highest priority) to 19 (lowest priority)
- Lower nice value = higher priority
- Default: 0

**Priority (PR)**: Internal kernel priority (read-only)

```bash
# Show nice values
ps -o pid,nice,comm
ps lax

# Start process with specific nice value
nice -n 10 command          # Nicer (lower priority)
nice -n -5 command          # Higher priority (needs root)

# Change priority of running process
renice -n 5 -p PID          # Increase nice value (lower priority)
renice -n -5 -p PID         # Decrease nice value (higher priority)

# Change priority for specific user
renice -n 10 -u username

# Change priority for group
renice -n 10 -g groupname
```

## Signals and Process Termination

### Common Signals

| Signal | Number | Action | Use Case |
|--------|--------|--------|----------|
| **SIGHUP** | 1 | Hang up | Reload configuration |
| **SIGINT** | 2 | Interrupt | Ctrl+C equivalent |
| **SIGKILL** | 9 | Kill (unstoppable) | Force kill (last resort) |
| **SIGTERM** | 15 | Terminate (default) | Graceful shutdown |
| **SIGSTOP** | 19 | Stop | Pause process |
| **SIGCONT** | 18 | Continue | Resume process |
| **SIGUSR1** | 10 | User defined | Service-specific |
| **SIGUSR2** | 12 | User defined | Service-specific |

### Sending Signals

```bash
# Send SIGTERM (graceful shutdown)
kill PID
kill -15 PID
kill -TERM PID

# Send SIGKILL (force kill)
kill -9 PID
kill -KILL PID

# Send other signals
kill -1 PID                 # SIGHUP (reload)
kill -2 PID                 # SIGINT (interrupt)
kill -19 PID                # SIGSTOP (suspend)
kill -18 PID                # SIGCONT (resume)

# Kill by process name
killall httpd

# Kill with specific signal
killall -9 httpd
```

## Process States and Zombies

### Process States

| State | Symbol | Meaning |
|-------|--------|---------|
| **Running** | R | Actively running or ready to run |
| **Sleeping** | S | Sleeping (waiting for I/O) |
| **Disk Sleep** | D | Uninterruptible sleep (I/O wait) |
| **Stopped** | T | Stopped by signal (SIGSTOP) |
| **Zombie** | Z | Process terminated but parent hasn't reaped |

### Handling Zombie Processes

```bash
# Identify zombie processes
ps aux | grep Z

# Zombie info (shows parent process)
ps -o pid,ppid,stat,cmd | grep Z

# Kill parent process (if safe)
kill -9 parentPID

# If parent is already init (PID 1), zombie is benign
# System will eventually clean it up
```

## Process Monitoring Tools

### Using top

```bash
# Start top with sorted output
top -o %CPU                # Sort by CPU
top -o %MEM                # Sort by memory

# Monitor specific process
top -p PID1,PID2,PID3

# Batch mode (for scripts)
top -b -n 1

# Change update interval
top -d 2                   # Update every 2 seconds

# Show only specific user
top -u username
```

### Using htop (if installed)

```bash
# Start htop (enhanced top)
htop

# Filter processes
F4                          # Filter by name
```

### Using systemd-cgtop

```bash
# Show CPU and memory by cgroup
systemd-cgtop

# Monitor specific service
systemd-cgtop -p httpd.service
```

## Process Tuning with tuned

### Managing Tuning Profiles

```bash
# Show available profiles
tuned-adm list

# Show active profile
tuned-adm active

# Verify daemon running
systemctl status tuned

# Set profile
tuned-adm profile powersave      # Power saving
tuned-adm profile performance    # Maximum performance
tuned-adm profile balanced       # Balanced (default)
tuned-adm profile throughput-performance
tuned-adm profile latency-performance

# Show profile details
tuned-adm profile_info performance

# Recommend profile
tuned-adm recommend

# Enable tuned daemon
systemctl enable --now tuned
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **ps Command** | Standard | Same |
| **top Command** | Standard | Same |
| **tuned Profiles** | Standard set | **Expanded profiles** |
| **Process Priority** | -20 to 19 | Same |
| **systemd-cgtop** | Available | Same |
| **Real-time Priority** | Available (FIFO, RR) | Same |

## Tips and What to Remember

- **Priority Values**: Remember -20 (high) to 19 (low); use `renice` to adjust running process.
- **Process Termination**: Try SIGTERM first; use SIGKILL only if necessary.
- **Zombie Cleanup**: Zombies are harmless if not numerous; check parent process.
- **tuned Profiles**: Always use appropriate profile for your workload; "performance" uses more power.
- **CPU vs Memory**: Monitor both; high CPU doesn't mean high memory usage.
- **top Sorting**: Press `Shift+M` for memory, `Shift+P` for CPU during interactive session.

---

# Chapter 11: Working with Systemd

## Exam-Relevant Objectives

- Start, stop, and enable/disable services
- Check service status and view logs
- Understand systemd units and targets
- Boot systems into different targets
- Create basic systemd services

## Systemd Basics

**systemd**: System manager and service launcher in RHEL 10.

```bash
# Show systemd version
systemctl --version

# Check system status
systemctl status

# Show systemd version
systemd --version
```

## Systemd Units

### Unit Types

| Type | File Extension | Purpose |
|------|----------------|---------|
| **Service** | .service | System service (daemon) |
| **Socket** | .socket | Socket for service activation |
| **Device** | .device | Hardware device |
| **Mount** | .mount | File system mount |
| **Automount** | .automount | Automatic mount point |
| **Target** | .target | Group of units (like runlevel) |
| **Timer** | .timer | Timer for scheduled tasks |
| **Snapshot** | .snapshot | System state snapshot |
| **Scope** | .scope | External process group |
| **Slice** | .slice | Resource control group |
| **Path** | .path | File system path monitoring |

### Unit Locations

```bash
# System units (Red Hat provided)
/usr/lib/systemd/system/

# System units (local customizations)
/etc/systemd/system/

# User units
~/.config/systemd/user/

# Temporary units
/run/systemd/system/
```

## Service Management

### Starting and Stopping Services

```bash
# Start service
systemctl start httpd

# Stop service
systemctl stop httpd

# Restart service
systemctl restart httpd

# Reload configuration (without stopping)
systemctl reload httpd

# Restart if running (start if not)
systemctl try-restart httpd

# Check if service is running
systemctl is-active httpd

# Show service status
systemctl status httpd
```

### Enabling and Disabling Services

```bash
# Enable service (start on boot)
systemctl enable httpd

# Disable service (don't start on boot)
systemctl disable httpd

# Enable and start immediately
systemctl enable --now httpd

# Disable and stop immediately
systemctl disable --now httpd

# Check if enabled
systemctl is-enabled httpd
```

### Service Status and Logs

```bash
# Show service status
systemctl status httpd

# Show service dependencies
systemctl list-dependencies httpd

# Show service details
systemctl show httpd

# Show specific property
systemctl show -p ExecStart httpd

# View service configuration
systemctl cat httpd
```

## Systemd Targets

### Understanding Targets

**Target**: Collection of units that boot together (replaces runlevel concept).

### Common Targets

| Target | Purpose | Equivalent Runlevel |
|--------|---------|-------------------|
| **emergency.target** | Emergency shell (minimal) | Emergency |
| **rescue.target** | Rescue shell (single-user) | 1 |
| **multi-user.target** | Multi-user (no GUI) | 3 |
| **graphical.target** | Multi-user with GUI | 5 |
| **reboot.target** | Reboot system | 6 |
| **poweroff.target** | Power off system | 0 |

### Managing Targets

```bash
# Show default target
systemctl get-default

# Set default target
systemctl set-default multi-user.target
systemctl set-default graphical.target

# Show current target
systemctl list-units --type target --active

# Show all targets
systemctl list-units --type target --all

# Isolate target (switch to it immediately)
systemctl isolate rescue.target
systemctl isolate emergency.target
systemctl isolate multi-user.target

# List targets with descriptions
systemctl list-unit-files --type target

# Show target details
systemctl show multi-user.target
```

## Creating Custom Systemd Services

### Service File Format

```bash
# Service file location
/etc/systemd/system/myservice.service

# Example service file
cat > /etc/systemd/system/myapp.service << 'EOF'
[Unit]
Description=My Custom Application
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/myapp
Restart=always
RestartSec=5
User=myuser
Group=mygroup

[Install]
WantedBy=multi-user.target
EOF

# Reload systemd to recognize new unit
systemctl daemon-reload

# Enable and start service
systemctl enable --now myapp.service

# Check status
systemctl status myapp.service
```

### Service File Sections

**[Unit]**:
- Description: Service description
- After: Start after these units
- Before: Start before these units
- Wants: Weak dependency
- Requires: Strong dependency
- Conflicts: Cannot run with these units

**[Service]**:
- Type: simple, forking, oneshot, notify
- ExecStart: Command to run
- ExecStop: Stop command
- ExecReload: Reload command
- Restart: always, on-failure, on-success
- RestartSec: Seconds between restarts
- User: Run as user
- Group: Run as group
- WorkingDirectory: Working directory
- Environment: Set variables
- StandardOutput: inherit, journal, syslog, kmsg, journal+console, null
- StandardError: Same options as StandardOutput

**[Install]**:
- WantedBy: Target to install into (multi-user.target, graphical.target, etc.)
- RequiredBy: Required for targets
- Alias: Alternative names

### Service Examples

```bash
# Simple service (application stays running)
[Service]
Type=simple
ExecStart=/usr/bin/myapp

# Forking service (launches daemon that forks)
[Service]
Type=forking
ExecStart=/usr/sbin/httpd -D FOREGROUND

# One-shot service (runs once and exits)
[Service]
Type=oneshot
ExecStart=/usr/local/bin/setup.sh
```

## Systemd Sockets

### Socket-Activated Services

```bash
# Socket file example
cat > /etc/systemd/system/myservice.socket << 'EOF'
[Unit]
Description=My Service Socket

[Socket]
ListenStream=9999
Accept=true

[Install]
WantedBy=sockets.target
EOF

# Service template
cat > /etc/systemd/system/myservice@.service << 'EOF'
[Unit]
Description=My Service Instance
After=network.target

[Service]
ExecStart=/usr/bin/myapp
StandardInput=socket
StandardOutput=socket
StandardError=socket
EOF

# Enable and start
systemctl enable --now myservice.socket
systemctl status myservice.socket
```

## Systemd Mount Units

### Creating Mount Units

```bash
# Mount unit example
cat > /etc/systemd/system/mnt-data.mount << 'EOF'
[Unit]
Description=/mnt/data Mount

[Mount]
What=/dev/sdc1
Where=/mnt/data
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
EOF

# Reload and enable
systemctl daemon-reload
systemctl enable mnt-data.mount
systemctl start mnt-data.mount

# Verify
systemctl status mnt-data.mount
mount | grep mnt-data
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **systemd Version** | 249.x | **254.x** (latest) |
| **Target Management** | Standard | Same |
| **Socket Activation** | Supported | Same |
| **Service Creation** | Standard | Same |
| **New Features** | None significant | Enhanced timer reliability |

## Tips and What to Remember

- **reload vs restart**: Use `reload` for configuration changes that don't need restart; use `restart` for full restart.
- **enable --now**: Use to enable and start simultaneously; saves two commands.
- **Target Switching**: Use `isolate` to switch targets; verify no critical services stop first.
- **Service Dependencies**: Use `After=` and `Before=` in [Unit] to control startup order.
- **Automatic Restart**: Use `Restart=always` with `RestartSec=` to auto-restart failed services.

---

# Chapter 12: Scheduling Tasks

## Exam-Relevant Objectives

- Schedule tasks using at, cron, and systemd timer units
- Create and manage scheduled jobs
- Ensure scheduled tasks persist after reboot

## Systemd Timer Units (Preferred in RHEL 10)

### Creating Timer-Based Tasks

```bash
# Timer unit example
cat > /etc/systemd/system/backup.timer << 'EOF'
[Unit]
Description=Daily Backup Timer
Requires=backup.service

[Timer]
OnBootSec=10min
OnUnitActiveSec=1d
Persistent=true

[Install]
WantedBy=timers.target
EOF

# Corresponding service
cat > /etc/systemd/system/backup.service << 'EOF'
[Unit]
Description=Daily Backup Service
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
EOF

# Enable and start
systemctl daemon-reload
systemctl enable --now backup.timer

# Check timer status
systemctl status backup.timer
systemctl list-timers

# Show when next run is scheduled
systemctl list-timers backup.timer

# Manually trigger timer
systemctl start backup.service
```

### Timer Directives

| Directive | Meaning |
|-----------|---------|
| **OnBootSec=** | Run N time after system boot |
| **OnUnitActiveSec=** | Run N time after service last completed |
| **OnCalendar=** | Run at specific time (cron-like format) |
| **Persistent=true** | Run missed scheduled time if system was down |

### OnCalendar Examples

```bash
# Daily at 2:00 AM
OnCalendar=*-*-* 02:00:00

# Every Monday at 10:00 AM
OnCalendar=Mon *-*-* 10:00:00

# Every 6 hours
OnCalendar=*-*-* 00,06,12,18:00:00

# On the 1st of each month at midnight
OnCalendar=*-*-01 00:00:00
```

## Cron (Traditional Scheduler)

### Cron Basics

```bash
# Edit user's cron jobs
crontab -e

# List user's cron jobs
crontab -l

# Remove all cron jobs
crontab -r

# Edit root's cron jobs
sudo crontab -e

# View cron logs (if configured)
journalctl -u crond
tail -f /var/log/cron
```

### Cron Job Format

```bash
# Cron format:
# Minute Hour Day Month DayOfWeek Command
# 0-59   0-23  1-31  1-12   0-6 (0=Sun, 6=Sat)

# Examples in crontab
0 2 * * * /usr/local/bin/backup.sh              # 2:00 AM daily
30 9 * * 1-5 /usr/bin/check-updates.sh          # 9:30 AM weekdays
0 */6 * * * /usr/local/bin/cleanup.sh           # Every 6 hours
0 0 1 * * /usr/local/bin/monthly-report.sh      # 1st of month at midnight
*/5 * * * * /usr/local/bin/monitor.sh           # Every 5 minutes
0 9-17 * * * /usr/local/bin/work-hours.sh       # Every hour 9-5 PM
```

### Cron Special Characters

| Symbol | Meaning |
|--------|---------|
| `*` | Any value in field |
| `,` | Multiple specific values (1,3,5) |
| `-` | Range of values (1-5) |
| `/` | Step values (*/5 = every 5) |

### System Cron Jobs

```bash
# System cron directory
/etc/cron.d/              # System cron jobs
/etc/cron.daily/          # Daily jobs
/etc/cron.weekly/         # Weekly jobs
/etc/cron.monthly/        # Monthly jobs
/etc/cron.hourly/         # Hourly jobs

# Run cron job immediately (not scheduled)
run-parts /etc/cron.daily

# View job output (if configured)
sudo tail -f /var/log/cron
```

## at Command (One-Time Scheduling)

### Scheduling One-Time Jobs

```bash
# Schedule command to run once
at 10:30 AM tomorrow
at> /usr/local/bin/backup.sh
at> <Ctrl+D>

# Schedule for specific date/time
at 10:30 AM Dec 25
at> /usr/bin/sendmail user@example.com
at> <Ctrl+D>

# Schedule for specific number of days
at now + 5 days
at> /usr/local/bin/task.sh
at> <Ctrl+D>

# Read commands from file
at 10:00 AM < /path/to/commands.txt

# List scheduled at jobs
atq

# Show details of specific job
at -c job-id

# Remove scheduled job
atrm job-id
```

### at Time Formats

```bash
# Various time formats
at 10:30 AM              # Today at 10:30 AM
at 14:30                 # Today at 2:30 PM (24-hour)
at 10:30 AM tomorrow     # Tomorrow at 10:30 AM
at 10:30 AM next Friday  # Next Friday at 10:30 AM
at 10:30 AM 12/25/2025   # December 25, 2025 at 10:30 AM
at now + 1 hour          # 1 hour from now
at now + 2 days          # 2 days from now
at midnight              # Midnight tonight
at noon                  # Noon today
```

## Cron Access Control

### /etc/cron.allow and /etc/cron.deny

```bash
# Whitelist users who can use cron
echo "bob" >> /etc/cron.allow

# Blacklist users who cannot use cron
echo "bob" >> /etc/cron.deny

# Only /etc/cron.allow is checked (deny is ignored if allow exists)
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Systemd Timers** | Supported | Same (preferred) |
| **cron Service** | crond | Same |
| **at Command** | Supported | Same |
| **Timer Enhancements** | Standard | **Improved reliability** |
| **Cron Log Location** | /var/log/cron | Same |

## Tips and What to Remember

- **Timers vs Cron**: Systemd timers are preferred for RHEL 10; cron still works for compatibility.
- **Output Redirection**: Add `>> /path/to/logfile 2>&1` to capture output in cron jobs.
- **Environment**: Cron jobs run in minimal environment; use full paths or set `PATH=`.
- **at Service**: Enable `atd` service: `systemctl enable --now atd`
- **Persistent Timers**: Set `Persistent=true` in timer units to run missed scheduled times.
- **Cron Minute Accuracy**: Cron runs at minute intervals; for more frequent tasks, use systemd timers.

---

# Chapter 13: Configuring Logging

## Exam-Relevant Objectives

- Locate and interpret system log files and journals
- Preserve system journals
- Configure local logging
- Understand rsyslog and journal configuration

## Systemd Journal (journald)

### Viewing Journal Logs

```bash
# Show all journal entries
journalctl

# Show journal entries since system boot
journalctl -b

# Show previous boot's logs (if persistent)
journalctl -b -1

# Show journal entries from specific service
journalctl -u httpd

# Show journal entries for specific process
journalctl _PID=1234

# Follow journal in real-time (like tail -f)
journalctl -f

# Show last 20 entries
journalctl -n 20

# Show since specific time
journalctl --since "2025-01-01"
journalctl --since "1 hour ago"
journalctl --until "2 hours ago"

# Show entries between times
journalctl --since "1 day ago" --until "now"

# Filter by priority (emerg, alert, crit, err, warning, notice, info, debug)
journalctl -p err              # Errors and higher
journalctl -p info             # Info and higher
journalctl -p debug            # Debug and higher
```

### Journal Output Formats

```bash
# Default short format
journalctl

# Verbose format (all fields)
journalctl -o verbose

# JSON format
journalctl -o json

# Short timestamp format
journalctl -o short

# Catalog format (with explanations)
journalctl -o cat

# Show entry count
journalctl --no-pager | wc -l
```

## Making Journal Persistent

### Enable Persistent Storage

```bash
# Create journal directory
sudo mkdir -p /var/log/journal

# Set ownership and permissions
sudo chown root:systemd-journal /var/log/journal
sudo chmod 2755 /var/log/journal

# Restart journal service
sudo systemctl restart systemd-journald

# Flush journal to disk
sudo journalctl --flush

# Verify persistence
ls -la /var/log/journal/
sudo journalctl -b -1    # Should show previous boot logs

# Force journal retention
cat > /etc/systemd/journald.conf << 'EOF'
[Journal]
Storage=persistent
SystemMaxUse=4G
RuntimeMaxUse=100M
EOF

# Apply changes
sudo systemctl restart systemd-journald
```

## Rsyslog Configuration

### Rsyslog Basics

**rsyslog**: Traditional syslog daemon for centralized logging.

```bash
# Check rsyslog status
systemctl status rsyslog

# Main configuration file
/etc/rsyslog.conf

# Additional configuration directory
/etc/rsyslog.d/
```

### Rsyslog Configuration

```bash
# View rsyslog configuration
cat /etc/rsyslog.conf | grep -v "^#"

# Basic format
# Facility.Priority  Destination
# daemon.info       /var/log/daemon.log

# Create custom rsyslog rule
cat > /etc/rsyslog.d/custom.conf << 'EOF'
# Log HTTP server to custom file
:programname, isequal, "httpd" /var/log/httpd/access.log
& stop

# Send critical messages to file
*.crit /var/log/critical.log

# Send to remote syslog server
*.* @logserver.example.com:514      # UDP
*.* @@logserver.example.com:514     # TCP
EOF

# Restart rsyslog to apply changes
sudo systemctl restart rsyslog

# Test rsyslog
logger "Test message"
tail -f /var/log/messages
```

### Rsyslog Facilities

| Facility | Code | Use |
|----------|------|-----|
| auth | 4 | Authentication |
| authpriv | 10 | Private authentication |
| daemon | 24 | System daemons |
| kern | 0 | Kernel messages |
| local0-7 | 16-23 | Local use (custom) |
| lpr | 6 | Printer subsystem |
| mail | 2 | Mail system |
| syslog | 5 | Messages from syslogd |
| user | 1 | User processes |

### Rsyslog Priorities

| Priority | Severity |
|----------|----------|
| **0 - emerg** | System unusable |
| **1 - alert** | Action must be taken immediately |
| **2 - crit** | Critical conditions |
| **3 - err** | Error conditions |
| **4 - warning** | Warning conditions |
| **5 - notice** | Normal but significant |
| **6 - info** | Informational |
| **7 - debug** | Debug-level messages |

## System Log Files

### Common Log Files

| Log File | Purpose |
|----------|---------|
| `/var/log/messages` | General system log |
| `/var/log/secure` | Authentication and authorization |
| `/var/log/audit/audit.log` | Audit framework logs |
| `/var/log/dmesg` | Kernel messages (boot) |
| `/var/log/boot.log` | Boot messages |
| `/var/log/cron` | Cron job logs |
| `/var/log/maillog` | Mail server logs |
| `/var/log/httpd/access_log` | Apache access logs |
| `/var/log/httpd/error_log` | Apache error logs |

### Viewing Logs

```bash
# View log file
cat /var/log/messages

# Follow log in real-time
tail -f /var/log/messages

# View last N lines
tail -n 50 /var/log/messages

# View with timestamps
ls -l /var/log/messages

# Search logs for pattern
grep "error" /var/log/messages

# View compressed logs (if rotated)
zcat /var/log/messages-20250101.gz

# Count specific events
grep "Failed password" /var/log/secure | wc -l
```

## Log Rotation

### logrotate Configuration

```bash
# Main configuration
/etc/logrotate.conf

# Configuration directory (application-specific)
/etc/logrotate.d/

# Example Apache logrotate config
cat > /etc/logrotate.d/httpd << 'EOF'
/var/log/httpd/*log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 644 root root
    sharedscripts
    postrotate
        /bin/kill -HUP `cat /var/run/syslogd.pid 2>/dev/null` 2>/dev/null || true
    endscript
}
EOF

# Test logrotate configuration (dry run)
logrotate -d /etc/logrotate.conf

# Manually rotate logs
logrotate -f /etc/logrotate.conf

# Force rotate specific log
logrotate -f /etc/logrotate.d/httpd
```

### Logrotate Options

| Option | Meaning |
|--------|---------|
| `daily` | Rotate daily |
| `weekly` | Rotate weekly |
| `monthly` | Rotate monthly |
| `rotate N` | Keep N rotated logs |
| `compress` | Compress old logs with gzip |
| `delaycompress` | Compress on next rotation |
| `notifempty` | Don't rotate empty files |
| `missingok` | Don't error if file missing |
| `create MODE USER GROUP` | Create new file with permissions |
| `sharedscripts` | Run postrotate script once |
| `postrotate` | Script to run after rotation |

## Kernel Logging

### dmesg Command

```bash
# Show kernel ring buffer
dmesg

# Show last 20 kernel messages
dmesg | tail -20

# Follow kernel messages
dmesg -w

# Clear kernel buffer (root only)
dmesg -c

# Show with timestamps
dmesg -T

# Show with human-readable times
dmesg -H

# Search for specific messages
dmesg | grep -i error

# Monitor in real-time
watch dmesg
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **journald** | Primary | Same |
| **Persistent Journal** | Optional | Same |
| **rsyslog** | Supported | Same |
| **Log Retention** | Configurable | Same |
| **Journal Size Limits** | 4GB default | Same |
| **Compression** | gzip | Same |

## Tips and What to Remember

- **journalctl Priority**: Remember `-p err` for errors and higher; `-p debug` includes all.
- **Persistent Journal**: Enable for troubleshooting; verify `/var/log/journal/` is writable.
- **Real-time Monitoring**: Use `journalctl -f` or `tail -f` for active troubleshooting.
- **Log Rotation**: Customize `/etc/logrotate.d/` for application-specific needs.
- **Storage Considerations**: Monitor `/var/log/` disk usage; set limits in `journald.conf`.
- **Authentication Logs**: Check `/var/log/secure` for failed SSH attempts and sudo usage.

---

# Chapter 14: Managing Storage

## Exam-Relevant Objectives

- List, create, delete partitions on GPT disks
- Create and mount file systems
- Configure systems to mount file systems at boot by UUID or label
- Add new partitions and swap non-destructively

## Partitioning Basics

### Partition Schemes

**MBR (Master Boot Record)**:
- Older partitioning scheme
- Supports 4 primary partitions (or 3 primary + 1 extended containing logical partitions)
- Maximum partition size: 2 TiB
- Maximum disk size: 2 TiB

**GPT (GUID Partition Table)**:
- Modern replacement for MBR
- Supports up to 128 partitions
- Partition size: up to 8 ZiB
- Disk size: up to 8 ZiB
- **Recommended for RHEL 10**

## Viewing Disk and Partition Information

```bash
# List block devices
lsblk

# Show detailed block device information
lsblk -a

# Show disk partitions (legacy)
fdisk -l

# Show UUID and labels
blkid

# Show partition table type
parted -l

# Show disk size
df -h

# Show inode usage
df -i

# Show all mounts
mount | grep -i ext
```

## GPT Partitioning with gdisk

### Using gdisk

```bash
# Interactive gdisk session
sudo gdisk /dev/sdb

# Common commands in gdisk:
# p: print partition table
# n: create new partition
# d: delete partition
# t: change partition type
# w: write changes to disk
# q: quit without saving

# Example session
sudo gdisk /dev/sdb

GPT fdisk (gdisk) version 1.0.3

Command (? for help): p        # Show partition table
Command (? for help): n        # Create new partition
Partition number (1-128): 1
First sector: (press Enter for default)
Last sector: +1G              # 1 GB partition
Hex code or GUID: 8300        # Linux filesystem

Command (? for help): w        # Write changes
Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING DATA!

# Non-interactive gdisk (for scripting)
echo -e "n\n\n\n+1G\n8300\nw\nY" | gdisk /dev/sdb
```

## MBR Partitioning with fdisk

### Using fdisk

```bash
# Interactive fdisk session
sudo fdisk /dev/sda

# Common commands in fdisk:
# p: print partition table
# n: create new partition
# d: delete partition
# t: change partition type
# w: write changes
# q: quit without saving

# Example session
sudo fdisk /dev/sda

Welcome to fdisk. Changes will remain in memory only, until you decide to write them.
Command (m for help): p        # Show partition table

Disk /dev/sda: 1000 cylinders, 64 heads, 32 sectors/track
Units = cylinders of 1024*512 bytes...

Command (m for help): n        # Create new partition
Select (default p): p          # Primary
Partition number (1-4): 1
First cylinder (1-1000): 1
Last cylinder or +size (1-1000): +1G
Command (m for help): t        # Change type
Partition number (1-4): 1
Hex code (type L to list codes): 83   # Linux
Command (m for help): w        # Write changes
```

## Parted (Partition Editor)

### Using parted

```bash
# Interactive parted session
sudo parted /dev/sdb

# Create GPT partition table
(parted) mklabel gpt

# Create partition
(parted) mkpart primary ext4 0% 50%    # 50% of disk
(parted) mkpart primary ext4 50% 100%  # Remaining 50%

# Print partitions
(parted) print

# Exit
(parted) quit

# Non-interactive commands
sudo parted -s /dev/sdb mklabel gpt
sudo parted -s /dev/sdb mkpart primary ext4 0% 100%
```

## Creating File Systems

### File System Types in RHEL 10

| File System | Features | Use Case |
|------------|----------|----------|
| **XFS** | Default, excellent performance, size-only increase | General purpose (default) |
| **ext4** | Mature, supports reducing size, reliable | Legacy or when reduce needed |
| **VFAT** | Compatible with Windows, no permissions | USB drives, external media |

### Creating File Systems

```bash
# Create XFS (default in RHEL 10)
sudo mkfs.xfs /dev/sdb1

# Create ext4
sudo mkfs.ext4 /dev/sdb1

# Create VFAT
sudo mkfs.vfat /dev/sdb1

# Create with label
sudo mkfs.xfs -L data /dev/sdb1

# Verify file system
sudo xfs_info /dev/sdb1       # XFS
sudo tune2fs -l /dev/sdb1     # ext4

# Check file system type
blkid

# Resize file system (ext4 only; XFS can only grow)
sudo resize2fs /dev/sdb1 5G   # ext4
sudo xfs_growfs /mnt/data     # XFS (use mount point, not device)
```

## Mounting File Systems

### Manual Mounting

```bash
# Mount file system
sudo mount /dev/sdb1 /mnt

# Mount with specific type
sudo mount -t ext4 /dev/sdb1 /mnt

# Mount with options
sudo mount -o defaults,noatime /dev/sdb1 /mnt

# Show mounted file systems
mount

# Unmount file system
sudo umount /mnt

# Verify mount
df -h | grep mnt
```

### Permanent Mounting with /etc/fstab

```bash
# View /etc/fstab
cat /etc/fstab

# Format: Device MountPoint Type Options Dump fsck
UUID=abc123def456 /mnt ext4 defaults 0 0
LABEL=data       /data xfs  defaults 0 0
/dev/sdb1        /mnt2 ext4 defaults 0 0

# Add mount to /etc/fstab
echo 'UUID=abc123def456 /mnt ext4 defaults 0 0' >> /etc/fstab

# Find UUID
blkid /dev/sdb1
# Output: /dev/sdb1: UUID="abc123def456" TYPE="ext4"

# Find LABEL
e2label /dev/sdb1           # ext4
xfs_admin -l /dev/sdb1      # XFS

# Test /etc/fstab syntax
sudo mount -a                    # Mount all unmounted file systems
sudo findmnt --verify           # Verify /etc/fstab
sudo findmnt --verify --verbose # Verbose verification

# Mount all file systems in /etc/fstab
sudo mount -a

# Remount file system
sudo mount -o remount,noatime /mnt
```

### Mount Options

| Option | Purpose |
|--------|---------|
| **defaults** | rw, suid, dev, exec, auto, nouser, async |
| **ro** | Read-only |
| **rw** | Read-write |
| **noatime** | Don't update access time (improves performance) |
| **relatime** | Update access time relative to modification (compromise) |
| **noexec** | Prevent execution of binaries |
| **nosuid** | Disable SUID/SGID bits |
| **nodev** | Prevent device file access |
| **user** | Allow non-root users to mount |
| **nouser** | Don't allow non-root users to mount |
| **auto** | Mount automatically at boot |
| **noauto** | Don't mount automatically |

## Adding Swap

### Create Swap Partition

```bash
# Create partition with fdisk/gdisk
sudo fdisk /dev/sda
# Change type to swap (82 for MBR, 8200 for GPT)

# Format as swap
sudo mkswap /dev/sda1

# Enable swap
sudo swapon /dev/sda1

# Verify swap
free -h
swapon -s    # Show swap usage

# Add to /etc/fstab for persistence
echo '/dev/sda1 none swap sw 0 0' >> /etc/fstab

# Disable swap
sudo swapoff /dev/sda1
```

### Create Swap File

```bash
# Create 1 GB swap file
sudo dd if=/dev/zero of=/swapfile bs=1M count=1024

# Set permissions
sudo chmod 600 /swapfile

# Format as swap
sudo mkswap /swapfile

# Enable swap
sudo swapon /swapfile

# Verify
free -h

# Add to /etc/fstab
echo '/swapfile none swap sw 0 0' >> /etc/fstab

# Disable swap
sudo swapoff /swapfile
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Default File System** | XFS | Same |
| **gdisk Available** | Yes | Same |
| **fdisk Compatibility** | MBR support | Same |
| **parted Tool** | Available | Same |
| **Mount Points** | Standard | Same |
| **UUID Usage** | Recommended | Same |
| **fstab Verification** | findmnt | Same |

## Tips and What to Remember

- **Always Use UUID or Label**: Don't rely on device names (/dev/sdb1) which can change.
- **Verify fstab Syntax**: Always run `findmnt --verify` before rebooting after /etc/fstab changes.
- **Mount All First**: Always test with `mount -a` before reboot to catch errors.
- **XFS Performance**: Default in RHEL 10; use when possible for performance.
- **ext4 for Reducing**: Only use ext4 if you need to shrink file systems.
- **Swap Sizing**: General rule: swap = RAM size (for traditional systems); modern systems need less.

---

# Chapter 15: Managing Advanced Storage

## Exam-Relevant Objectives

- Create and remove physical volumes
- Assign physical volumes to volume groups
- Create and delete logical volumes
- Extend and reduce logical volumes
- Understand LVM architecture
- Configure Stratis storage (RHEL 10 enhancement)

## LVM Architecture

**LVM (Logical Volume Manager)** provides flexible disk storage allocation:

- **Physical Volume (PV)**: Physical disk or partition
- **Volume Group (VG)**: Collection of PVs (pool of storage)
- **Logical Volume (LV)**: Virtual disk from VG

**Benefits**:
- Flexible resizing without unmounting
- Snapshots for backup
- Combine multiple disks into single volume

## Creating LVM

### Step 1: Create Physical Volume

```bash
# Prepare partition (GPT/MBR)
sudo gdisk /dev/sdb
# Create partition, type 8e00 (LVM)

# or

sudo fdisk /dev/sdb
# Create partition, type 8e (LVM)

# Create physical volume
sudo pvcreate /dev/sdb1

# Verify
sudo pvs
# PV         VG   Fmt  Attr PSize  PFree
# /dev/sdb1       lvm2 a--  10.00g 10.00g

# Show detailed PV info
sudo pvdisplay /dev/sdb1
```

### Step 2: Create Volume Group

```bash
# Create volume group with PV
sudo vgcreate vgdata /dev/sdb1

# Add multiple PVs to VG
sudo vgcreate vgdata /dev/sdb1 /dev/sdc1

# Verify
sudo vgs
# VG     #PV #LV #SN Attr   VSize  VFree
# vgdata   1   0   0 wz--n- 10.00g 10.00g

# Show detailed VG info
sudo vgdisplay vgdata
```

### Step 3: Create Logical Volume

```bash
# Create LV with specific size
sudo lvcreate -L 5G -n lvdata vgdata
# -L: Size
# -n: LV name
# Last argument: VG name

# Create LV using percentage of VG
sudo lvcreate -l 50%VG -n lvdata1 vgdata   # 50% of VG
sudo lvcreate -l 75%FREE -n lvdata2 vgdata # 75% of free space

# Verify
sudo lvs
# LV     VG     Attr       LSize
# lvdata vgdata -wi-a----- 5.00g

# Show detailed LV info
sudo lvdisplay /dev/vgdata/lvdata

# Create file system on LV
sudo mkfs.xfs /dev/vgdata/lvdata

# Mount LV
sudo mkdir -p /data
sudo mount /dev/vgdata/lvdata /data

# Verify
df -h | grep data
```

## Resizing LVM

### Extending Logical Volume

```bash
# Extend LV by 2 GB
sudo lvextend -L +2G /dev/vgdata/lvdata
# -L +2G: Add 2 GB

# Extend to absolute size (10 GB total)
sudo lvextend -L 10G /dev/vgdata/lvdata

# Extend LV and file system together
sudo lvextend -L +2G -r /dev/vgdata/lvdata
# -r: Resize file system automatically

# Extend using percentage
sudo lvextend -l +50%VG /dev/vgdata/lvdata
sudo lvextend -l 75%VG /dev/vgdata/lvdata     # Extend to 75% of VG

# Grow file system (if -r not used)
sudo xfs_growfs /data              # XFS
sudo resize2fs /dev/vgdata/lvdata  # ext4

# Verify size
df -h /data
```

### Reducing Logical Volume

**WARNING: Reducing LV can cause data loss. Always backup first.**

```bash
# Reduce file system first (ext4 only; XFS can't reduce)
sudo e2fsck -f /dev/vgdata/lvdata
sudo resize2fs /dev/vgdata/lvdata 4G    # Reduce to 4 GB

# Then reduce LV
sudo lvreduce -L 4G /dev/vgdata/lvdata

# Or reduce in one step
sudo lvreduce -L -1G -r /dev/vgdata/lvdata    # Remove 1 GB
# -r: Resize file system first

# Verify
df -h /data
```

### Extending Volume Group

```bash
# Create new PV and add to VG
sudo pvcreate /dev/sdc1

# Extend VG with new PV
sudo vgextend vgdata /dev/sdc1

# Verify
sudo vgs vgdata

# Now can extend LV using new space
sudo lvextend -L +5G /dev/vgdata/lvdata
```

### Reducing Volume Group

```bash
# Move LV data away from PV to remove
sudo pvmove /dev/sdb1

# Remove PV from VG
sudo vgreduce vgdata /dev/sdb1

# Remove PV entirely
sudo pvremove /dev/sdb1

# Verify
sudo vgs
sudo pvs
```

## LVM Snapshots

```bash
# Create snapshot of LV
sudo lvcreate -L 2G -s -n lvdata_snap /dev/vgdata/lvdata

# List snapshots
sudo lvs

# Mount snapshot (read-only)
sudo mkdir -p /mnt/snap
sudo mount -o ro /dev/vgdata/lvdata_snap /mnt/snap

# Use snapshot for backup
sudo tar czf backup.tar.gz -C /mnt/snap .

# Remove snapshot
sudo lvremove /dev/vgdata/lvdata_snap
```

## Stratis Storage (RHEL 10 Enhancement)

**Stratis**: Simplified storage management built on LVM and file systems.

### Stratis Setup

```bash
# Install Stratis packages
sudo dnf install -y stratisd stratis-cli

# Enable and start Stratis daemon
sudo systemctl enable --now stratisd

# Verify daemon running
sudo systemctl status stratisd
```

### Stratis Operations

```bash
# Create Stratis pool from block device
sudo stratis pool create mypool /dev/sdc

# List pools
sudo stratis pool list

# Show pool details
sudo stratis pool show mypool

# Create Stratis file system (no size needed)
sudo stratis fs create mypool myfs1

# List file systems
sudo stratis fs list mypool

# Mount Stratis file system
sudo mkdir -p /stratis/myfs1
sudo mount /stratis/mypool/myfs1 /stratis/myfs1

# Verify
df -h /stratis/myfs1

# Add device to pool (expand)
sudo stratis pool add-data mypool /dev/sdd

# Remove file system
sudo stratis fs destroy mypool myfs1

# Remove pool
sudo stratis pool destroy mypool

# Snapshot (keep current state)
sudo stratis fs snapshot mypool myfs1 myfs1-snap
```

### Permanent Stratis Mount

```bash
# Get UUID of Stratis file system
blkid | grep /dev/stratis

# Add to /etc/fstab
UUID=xxx /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0

# Test mount
sudo mount -a

# Verify
mount | grep stratis
```

## LVM Best Practices

```bash
# Check LVM status
sudo pvs
sudo vgs
sudo lvs

# Monitor LVM activity
sudo lvmdiskscan

# Backup LVM metadata
sudo vgcfgbackup -f /root/vgdata.cfg vgdata

# Restore LVM metadata
sudo vgcfgrestore -f /root/vgdata.cfg vgdata

# Remove LVM entirely
sudo lvremove /dev/vgdata/lvdata
sudo vgremove vgdata
sudo pvremove /dev/sdb1
```

## Key Differences: RHEL 9 → RHEL 10

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **LVM Version** | 2.03.x | Same |
| **Stratis** | Supported | **Enhanced** |
| **LVM Snapshots** | Standard | Same |
| **LVM Thin Pools** | Supported | Same |
| **VG/LV Limits** | Unchanged | Same |
| **New Stratis Features** | None | **Improved reliability** |

## Tips and What to Remember

- **Always Backup Before Reducing**: Reducing LVs can cause data loss.
- **XFS Limitation**: XFS can only grow, not shrink; use ext4 if reduction needed.
- **PV Naming**: Physical volumes can be partitions or entire disks.
- **VG Size**: VG automatically uses available space from PVs.
- **Snapshots**: Use for backups; snapshots consume space as changes occur.
- **Stratis**: Simpler than LVM; use when simplicity matters more than advanced features.

---

# Chapter 16: Basic Kernel Management

## Exam-Relevant Objectives

- List kernel modules and investigate their use
- Manage kernel drivers and modular kernel loading
- Update the kernel package
- Understand hardware initialization through udev
- Use kernel boot parameters

## Key Concepts

**Kernel Module**: A piece of code that can be dynamically loaded into the kernel to add functionality without recompiling the entire kernel. Modules allow drivers to be loaded only when needed.

**udev (User Device)**: The device manager that dynamically detects hardware at boot and during runtime. It reads rules files and loads appropriate drivers via kernel modules.

**Kernel Threads**: Operating system tasks implemented by kernel threads; visible in `ps aux` with square brackets (e.g., `[kworker]`, `[kswapd]`).

## Working with Kernel Modules

### Listing and Viewing Modules

```bash
# List currently loaded kernel modules
lsmod

# Show module details and parameters
modinfo <module_name>
modinfo cdrom

# List kernel modules with more details
lsmod | head -20

# Show kernel module dependencies
modinfo -d <module>  # Description
modinfo -p <module>  # Parameters
```

### Loading Modules

```bash
# Load a kernel module and its dependencies
modprobe <module_name>
modprobe cdrom

# Load module with parameters
modprobe cdrom debug=1

# Check if module is available before loading
modprobe --show-depends <module>
```

### Removing Modules

```bash
# Unload a kernel module (if not in use)
modprobe -r <module_name>
modprobe -r cdrom

# Force remove (careful - can destabilize system)
modprobe -r --force <module>
```

### Checking Hardware and Drivers

```bash
# Show PCI devices and associated kernel drivers
lspci -k

# Show first 20 lines with modules
lspci -k | head -20

# Show USB devices
lsusb

# Show kernel module status in sysfs
ls /sys/module/
```

## Hardware Initialization Process

### Boot Sequence for Hardware

1. **POST (Power-On-Self-Test)**: BIOS/UEFI firmware initializes hardware
2. **Hardware Probing**: Kernel detects available hardware
3. **Module Loading**: systemd-udevd loads appropriate drivers
4. **Rules Processing**:
   - Reads system rules from `/usr/lib/udev/rules.d/` (do not modify)
   - Reads custom rules from `/etc/udev/rules.d/`
   - Rules define how to name devices and which modules to load

### udev Configuration

```bash
# Monitor udev events in real-time
udevadm monitor

# Monitor specific events
udevadm monitor --subsystem-match=usb

# Show device information
udevadm info /dev/sda

# Reload udev rules and restart daemon
udevctl reload
systemctl restart systemd-udevd

# List udev rules
ls /usr/lib/udev/rules.d/
ls /etc/udev/rules.d/
```

### Kernel Ring Buffer and Logging

```bash
# View kernel messages (system boot and driver loading)
dmesg

# Show last 20 kernel messages
dmesg | tail -20

# Clear kernel ring buffer (root only)
dmesg -c

# Show timestamps with dmesg
dmesg -T

# Follow kernel logs via journal
journalctl -k

# Follow kernel logs in real-time
journalctl -k -f

# Show kernel info
hostnamectl status
```

## Managing Kernel Packages

### Kernel Upgrades

```bash
# Check current kernel version
uname -r

# Show all installed kernels
rpm -qa | grep kernel

# List available kernel updates
dnf check-update kernel

# Upgrade kernel to latest version
sudo dnf upgrade kernel

# Install specific kernel version
sudo dnf install kernel-6.12.0

# Verify new kernel installed without removing old
ls -la /boot/vmlinuz-*
```

### Kernel Modules in /etc/modprobe.d

```bash
# Permanent module options
cat > /etc/modprobe.d/custom.conf << 'EOF'
options cdrom debug=1
alias foo bar
EOF

# Blacklist a module (prevent loading)
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf

# Rebuild initramfs to apply changes
sudo dracut --force
```

## Tainted Kernel

**Tainted Kernel**: Kernel that contains proprietary/closed-source drivers. Reduces support from Red Hat.

```bash
# Check if kernel is tainted
cat /proc/sys/kernel/tainted

# View taint status
dmesg | grep -i taint
```

## Kernel-Related System Files

| File/Directory | Purpose |
|---|---|
| `/sys` | Virtual filesystem with kernel module and hardware info |
| `/proc/sys/kernel` | Kernel parameters |
| `/lib/modules/$(uname -r)` | Installed kernel modules |
| `/etc/modprobe.d/` | Kernel module configuration |
| `/boot/vmlinuz-*` | Kernel images |
| `/boot/initramfs-*` | Ramdisk with boot drivers |

## Tips and What to Remember

- **lsmod First**: Always check if module is loaded before trying to load it again
- **Module Dependencies**: modprobe automatically resolves dependencies; manual insmod does not
- **udev Rules**: Custom rules go in `/etc/udev/rules.d/`, NOT `/usr/lib/udev/rules.d/`
- **Hardware Issues**: Check dmesg and journald for hardware detection failures
- **Kernel Tainting**: Avoid proprietary drivers if possible; use open-source alternatives

## Critical Comments

- Kernel modules are critical for hardware support; always verify correct module is loaded for your hardware
- Changes to modprobe.d require dracut rebuild to persist
- Removing wrong module can cause immediate system instability

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Kernel Version** | 5.14.x | **6.12.0** (major version) |
| **udev Handling** | Standard | **Enhanced hardware detection** |
| **Module Auto-loading** | Supported | **Improved reliability** |
| **Kernel Tainting** | Standard | Same |
| **Dracut Tool** | Standard | **Better initramfs generation** |
| **Hardware Probing** | During boot | **During boot (improved)** |

---

# Chapter 17: Managing and Understanding the Boot Procedure

## Exam-Relevant Objectives

- Understand the RHEL 10 boot process
- Work with GRUB2 bootloader
- Boot into different systemd targets
- Pass boot parameters for troubleshooting
- Modify bootloader configuration

## Boot Procedure Overview

### RHEL 10 Boot Process

1. **POST (Power-On-Self-Test)**: BIOS/UEFI firmware checks hardware
2. **Bootable Device Selection**: Firmware locates bootable device
3. **GRUB2 Loading**: Boot loader (GRUB2) is loaded from boot sector
4. **Kernel Loading**: Kernel and initramfs (with minimal drivers) are loaded
5. **Init Process**: systemd (PID 1) becomes first process
6. **initrd.target Processing**: systemd executes units from initrd.target
7. **Root Filesystem Switch**: System switches from initramfs to real root filesystem
8. **Default Target Loading**: systemd loads default target (multi-user or graphical)

## Systemd Targets

### Understanding Targets

**Target**: Collection of units (services, mounts, sockets) that define a system state. Replaces legacy runlevels.

| Target | Purpose | Equivalent Runlevel | Boots To |
|--------|---------|-------------------|----------|
| **emergency.target** | Emergency shell (minimal, no networking) | Emergency | Root shell only |
| **rescue.target** | Single-user mode with basic services | 1 | Root shell with basic networking |
| **multi-user.target** | Multi-user mode without GUI | 3 | Command line for all users |
| **graphical.target** | Multi-user with desktop GUI | 5 | Desktop environment |
| **poweroff.target** | Power off the system | 0 | System shutdown |
| **reboot.target** | Reboot the system | 6 | System restart |

### Managing Targets

```bash
# Show default boot target
systemctl get-default

# Set default target (persistent)
systemctl set-default multi-user.target
systemctl set-default graphical.target

# Show currently active target
systemctl list-units --type target --active

# Switch to target immediately
systemctl isolate rescue.target
systemctl isolate emergency.target
systemctl isolate multi-user.target

# List all available targets
systemctl list-unit-files --type target

# Verify target configuration
systemctl cat multi-user.target

# Show target dependencies
systemctl list-dependencies multi-user.target
```

### Target Units

```bash
# Each target has two parts:
# 1. Unit file (e.g., /usr/lib/systemd/system/multi-user.target)
# 2. .wants directory with symlinks to required units
#    (e.g., /etc/systemd/system/multi-user.target.wants/)

# Show target unit content
cat /usr/lib/systemd/system/multi-user.target

# Enable a service for target (creates symlink)
systemctl enable httpd
# Creates: /etc/systemd/system/multi-user.target.wants/httpd.service

# View service symlinks in target.wants
ls /etc/systemd/system/multi-user.target.wants/
```

## GRUB2 Bootloader

### GRUB2 Basics

**GRUB2 (Grand Unified Bootloader version 2)**: Modern bootloader that loads kernel and initramfs.

**GRUB Configuration Flow**:
1. Edit `/etc/default/grub` (user-modifiable)
2. Run `grub2-mkconfig` to regenerate main config
3. Main config locations depend on firmware:
   - **BIOS**: `/boot/grub2/grub.cfg`
   - **UEFI**: `/boot/efi/EFI/redhat/grub.cfg`

### Editing GRUB Configuration

```bash
# View GRUB default configuration
cat /etc/default/grub

# Key GRUB variables
GRUB_DEFAULT=0                    # Boot first entry by default
GRUB_TIMEOUT=5                    # Wait 5 seconds for user input
GRUB_CMDLINE_LINUX="quiet"        # Boot parameters passed to kernel
GRUB_CMDLINE_LINUX_DEFAULT=""     # Additional parameters

# Edit GRUB defaults
vim /etc/default/grub
# Modify GRUB_CMDLINE_LINUX to add parameters
# Example: GRUB_CMDLINE_LINUX="quiet rhgb"

# Regenerate GRUB configuration from /etc/default/grub
grub2-mkconfig -o /boot/grub2/grub.cfg

# For UEFI systems
grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
```

### Boot Parameters

**At Boot Time**: Press `e` at GRUB menu to edit boot parameters

```bash
# Common boot parameters

rd.break                          # Stop in emergency shell (initramfs stage)
systemd.unit=rescue.target        # Boot into rescue mode
systemd.unit=emergency.target     # Boot into emergency mode
rhgb                              # Redhat Graphical Boot (splash screen)
quiet                             # Suppress kernel messages

# Disable SELinux at boot
selinux=0

# Reduce verbosity
rd.loglevel=1

# Root filesystem read-write (for recovery)
rw

# Enable kernel debugging
debug
```

### Recovering with Boot Parameters

```bash
# At GRUB menu, press 'e' to edit

# Add 'rd.break' to stop in initramfs stage
# Add 'enforcing=0' to disable SELinux
# Add 'init=/bin/sh' for shell directly

# Example boot line with parameters:
linux /vmlinuz-6.12.0-10.el10.x86_64 root=UUID=abc123 ro quiet rd.break

# Press Ctrl+X to boot
```

### GRUB2 Installation

```bash
# Install GRUB2 to MBR
sudo grub2-install /dev/sda

# Install GRUB2 for UEFI
sudo grub2-install --efi-directory=/boot/efi

# Verify installation
ls -la /boot/grub2/grub.cfg
ls -la /boot/efi/EFI/redhat/grub.cfg
```

## Kernel Command-Line Interface

### Accessing Boot Menu

- Press **`e`** during GRUB boot: Edit boot parameters
- Press **`c`** during GRUB boot: Enter GRUB command mode
- Press **`Esc`**: Back to GRUB menu

### Modifying Boot Parameters

```bash
# Remove quiet and rhgb to see boot messages
# Original: linux /vmlinuz-6.12.0 quiet rhgb
# Modified: linux /vmlinuz-6.12.0

# Add rescue target
# Modified: linux /vmlinuz-6.12.0 systemd.unit=rescue.target

# Ctrl+X: Boot with modified parameters
# Changes are temporary (not saved)
```

## Tips and What to Remember

- **Always Regenerate GRUB**: After editing `/etc/default/grub`, run `grub2-mkconfig`
- **Boot Parameter Syntax**: Add parameters after kernel name on boot line
- **Persistent vs Temporary**: Boot menu edits are temporary; modify `/etc/default/grub` for permanent changes
- **Target Isolation**: Use `systemctl isolate` to switch targets without rebooting
- **UEFI vs BIOS**: Verify firmware type and use correct GRUB config location

## Critical Comments

- GRUB configuration errors can render system unbootable; always test boot before moving to production
- Boot parameters with `rd.break` are essential for emergency recovery
- Never edit `/boot/grub2/grub.cfg` directly; edit `/etc/default/grub` instead

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Kernel Version** | 5.14.x | **6.12.0** |
| **Boot Process** | Standard systemd | Same |
| **GRUB2** | Standard | Same |
| **Target Management** | Standard | Same |
| **Boot Parameter Options** | Standard | **Expanded for new hardware** |
| **Emergency Mode** | rescue.target, emergency.target | Same |

---

# Chapter 18: Troubleshooting Skills

## Exam-Relevant Objectives

- Boot into troubleshooting targets
- Use emergency and rescue modes
- Reset root password
- Repair filesystem issues
- Rebuild initramfs
- Diagnose boot failures

## RHEL 10 Boot Procedure (for Troubleshooting Context)

1. Power-on self-test (POST)
2. Boot device selection
3. GRUB2 loads kernel and initramfs
4. Kernel mounts initramfs as root
5. systemd processes initrd.target
6. Root filesystem is mounted
7. systemd executes default target

## Rescue and Emergency Modes

### Emergency Target

**emergency.target**: Minimal environment with root shell, no networking, minimal services.

```bash
# Boot into emergency mode from GRUB menu
# Press 'e' and add: systemd.unit=emergency.target

# From running system
systemctl isolate emergency.target

# At emergency prompt
# Only / and /proc are mounted
# Manually mount filesystems as needed
mount -o remount,rw /        # Remount root as read-write
```

### Rescue Target

**rescue.target**: Single-user mode with more services than emergency, basic networking available.

```bash
# Boot into rescue mode from GRUB menu
# Press 'e' and add: systemd.unit=rescue.target

# From running system
systemctl isolate rescue.target

# Rescue mode includes:
# - Basic networking
# - System logging
# - More services than emergency
# - Root shell (no password required)
```

## Kernel Boot Arguments for Troubleshooting

```bash
# Stop in initramfs stage (before root mount)
rd.break

# Use shell directly instead of systemd
init=/bin/sh

# Disable SELinux
selinux=0

# Single-user mode (old style)
single

# Verbose boot (show all messages)
systemd.log_level=debug
```

## Password Reset Procedure

### Using rd.break

```bash
# 1. At GRUB menu, press 'e'
# 2. Add 'rd.break' to kernel line
# 3. Press Ctrl+X to boot

# 4. At emergency initramfs shell:
mount -o remount,rw /sysroot
chroot /sysroot
passwd
exit

# 5. Relabel files if SELinux is enforcing
touch /.autorelabel

# 6. Reboot
reboot
```

### Using Single-User Mode

```bash
# 1. At GRUB menu, press 'e'
# 2. Add 'single' or 'systemd.unit=rescue.target'
# 3. Press Ctrl+X

# 4. At root prompt (no password needed in rescue mode)
passwd

# 5. Exit and reboot
exit
```

## Filesystem Repairs

### Checking and Repairing Filesystems

```bash
# Check filesystem for errors (no repair)
sudo fsck -n /dev/sda1

# Check and repair (interactive)
sudo fsck /dev/sda1

# Force repair without prompts
sudo fsck -y /dev/sda1

# For ext4 specifically
sudo e2fsck /dev/sda1
sudo e2fsck -p /dev/sda1    # -p: auto-repair

# For XFS specifically
sudo xfs_repair /dev/sda1   # XFS is usually self-healing

# Check for filesystem errors in logs
dmesg | grep -i error
```

### Mounting Root Filesystem Read-Write

```bash
# If root is mounted read-only, remount as read-write
mount -o remount,rw /

# Or from emergency/rescue shell with chroot
mount -o remount,rw /sysroot
chroot /sysroot
mount -o remount,rw /
```

## Initramfs Rebuild

### When Rebuild is Needed

- After changing kernel modules
- If initramfs is corrupted
- After driver or firmware updates
- After modifying modprobe configuration

### Rebuilding Initramfs

```bash
# Rebuild initramfs for current kernel
sudo dracut --force

# Rebuild for specific kernel version
sudo dracut --force /boot/initramfs-6.12.0.img 6.12.0

# Verify rebuild
ls -la /boot/initramfs-*
file /boot/initramfs-*
```

## Boot Failure Diagnosis

### Checking Boot Logs

```bash
# Show systemd journal from last boot
journalctl -b

# Show journal from previous boot (if available)
journalctl -b -1

# Filter for errors
journalctl -b -p err

# Show service startup failures
systemctl list-units --state=failed

# Check for failed services
systemctl status

# View kernel messages from boot
journalctl -b -k

# Show boot performance
systemd-analyze

# Show time spent in each service
systemd-analyze blame

# Visualize boot sequence
systemd-analyze plot > boot.svg
```

### Using rescue Media

If system does not boot at all:

1. Boot from RHEL 10 installation media
2. Select "Troubleshooting" menu
3. Choose "Rescue a RHEL system"
4. Provides shell with access to filesystems
5. Mount filesystem: `mount /dev/sda1 /mnt`
6. Perform repairs in `/mnt`

### Fixing GRUB

```bash
# From rescue shell with chroot
mount /dev/sda1 /mnt
chroot /mnt

# Reinstall GRUB
grub2-install /dev/sda

# Regenerate config
grub2-mkconfig -o /boot/grub2/grub.cfg

# Exit and reboot
exit
exit
reboot
```

## Common Issues and Fixes

### Issue: "No space left on device"

```bash
# Find large files or directories
du -sh /*

# Clean journal logs (if full)
journalctl --vacuum=500M

# Remove old kernel versions
dnf remove kernel-<old-version>

# Clean package cache
dnf clean all
```

### Issue: Filesystem Read-Only

```bash
# Remount as read-write
mount -o remount,rw /

# If stuck, use single-user mode and remount
```

### Issue: Corrupted Boot Files

```bash
# From rescue media:
mount /dev/sda1 /mnt
chroot /mnt
grub2-install /dev/sda
dracut --force
exit
exit
reboot
```

## Tips and What to Remember

- **rd.break**: Most reliable for emergency access; stops before root mount
- **SELinux Relabeling**: Use `touch /.autorelabel` after password changes
- **Backup GRUB**: Never edit grub.cfg directly; use grub2-mkconfig
- **Journal Persistence**: Enable persistent journal (`/var/log/journal`) for troubleshooting

## Critical Comments

- Troubleshooting is essential exam skill; practice boot recovery scenarios
- Always keep rescue media handy for unbootable systems
- Journal analysis is key to diagnosing boot and service failures

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Boot Process** | Standard | Same |
| **Emergency Mode** | Available | Same |
| **Rescue Mode** | Available | Same |
| **dracut Tool** | Standard | **Improved** |
| **Journal Availability** | Standard | Same |
| **AI Assistance** | None | **Lightspeed available (Red Hat subscription)** |
| **Boot Parameters** | Standard set | **Expanded options** |

---

# Chapter 19: Automation with Bash Shell Script

## Exam-Relevant Objectives

- Write simple bash scripts
- Use variables, positional parameters, and environment
- Conditionals: if statements and test command
- Loops: for, while, until
- Case statements and command substitution
- Script debugging

## Shell Script Basics

### Shebang and Execution

```bash
#!/bin/bash

# Always include shebang at top of script
# Specifies interpreter (bash, sh, python, etc.)

# Make script executable
chmod +x script.sh

# Run script
./script.sh

# Run with bash explicitly
bash script.sh

# Check syntax without running
bash -n script.sh
```

### Variables

```bash
#!/bin/bash

# Define variables (no spaces around =)
USERNAME="john"
AGE=25
PATH_TO_FILE="/etc/hosts"

# Use variables with $
echo $USERNAME
echo "Username is $USERNAME"

# Positional parameters (script arguments)
./script.sh arg1 arg2 arg3
# $0 = script name
# $1 = first argument (arg1)
# $2 = second argument (arg2)
# $3 = third argument (arg3)
# $@ = all arguments
# $# = number of arguments

# Example script using parameters
#!/bin/bash
echo "Script: $0"
echo "First argument: $1"
echo "All arguments: $@"
echo "Argument count: $#"
```

### Environment Variables

```bash
# Access environment variables
echo $HOME
echo $PATH
echo $SHELL
echo $USER

# Set variable for current session
export MYVAR="value"

# Check if variable is set
if [ -z "$MYVAR" ]; then
    echo "Variable not set"
fi

# Use default value if not set
${MYVAR:-defaultvalue}
```

## Conditionals

### if Statement

```bash
# Basic if structure
if [ condition ]; then
    # commands if condition is true
fi

# if-else
if [ condition ]; then
    # commands if true
else
    # commands if false
fi

# if-elif-else
if [ condition1 ]; then
    # commands if condition1 true
elif [ condition2 ]; then
    # commands if condition2 true
else
    # commands if all conditions false
fi
```

### test Command and [ ]

**test** and **[ ]** are equivalent ([ ] is syntactic sugar for test)

```bash
# File tests
[ -f file.txt ]        # True if file exists
[ -d directory ]       # True if directory exists
[ -e file ]            # True if file or directory exists
[ -r file ]            # True if file is readable
[ -w file ]            # True if file is writable
[ -x file ]            # True if file is executable
[ -s file ]            # True if file has size > 0

# String tests
[ "$var" = "value" ]       # String equals
[ "$var" != "value" ]      # String not equals
[ -z "$var" ]              # String is empty
[ -n "$var" ]              # String is not empty
[ "$var" ]                 # String is not empty (shorthand)

# Numeric tests
[ $num -eq 10 ]        # Equal
[ $num -ne 10 ]        # Not equal
[ $num -lt 10 ]        # Less than
[ $num -le 10 ]        # Less than or equal
[ $num -gt 10 ]        # Greater than
[ $num -ge 10 ]        # Greater than or equal

# Logical operators
[ condition1 ] && [ condition2 ]    # AND
[ condition1 ] || [ condition2 ]    # OR
[ ! condition ]                     # NOT
```

### Example if Scripts

```bash
#!/bin/bash
# Check if file exists
if [ -f "$1" ]; then
    echo "File $1 exists"
else
    echo "File not found"
fi

# Check if user is root
if [ $UID -eq 0 ]; then
    echo "Running as root"
else
    echo "Not root user"
fi

# Check string comparison
if [ "$USER" = "root" ]; then
    echo "You are root"
elif [ "$USER" = "admin" ]; then
    echo "You are admin"
else
    echo "You are regular user"
fi
```

## Loops

### for Loop

```bash
# Loop through list
for item in one two three; do
    echo $item
done

# Loop through array
for i in ${array[@]}; do
    echo $i
done

# Loop through files
for file in *.txt; do
    echo "Processing $file"
done

# Loop with range
for i in {1..5}; do
    echo $i
done

# C-style for loop
for ((i=1; i<=5; i++)); do
    echo $i
done
```

### while Loop

```bash
# Basic while
count=1
while [ $count -le 5 ]; do
    echo "Count: $count"
    ((count++))
done

# while reading file
while read line; do
    echo "$line"
done < file.txt

# while with user input
while true; do
    read -p "Enter command (q to quit): " cmd
    if [ "$cmd" = "q" ]; then
        break
    fi
    echo "You entered: $cmd"
done
```

### until Loop

```bash
# Loop until condition becomes true
count=1
until [ $count -gt 5 ]; do
    echo "Count: $count"
    ((count++))
done

# until with condition
until [ -f /tmp/stop.flag ]; do
    echo "Waiting for stop flag..."
    sleep 1
done
```

### break and continue

```bash
# break: exit loop immediately
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        break    # Exit for loop
    fi
    echo $i
done

# continue: skip to next iteration
for i in {1..5}; do
    if [ $i -eq 3 ]; then
        continue    # Skip this iteration
    fi
    echo $i
done
```

## Case Statement

```bash
# Case statement structure
case $variable in
    pattern1)
        # commands if variable matches pattern1
        ;;
    pattern2)
        # commands if variable matches pattern2
        ;;
    *)
        # default case if no pattern matches
        ;;
esac

# Example
#!/bin/bash
case "$1" in
    start)
        echo "Starting service"
        ;;
    stop)
        echo "Stopping service"
        ;;
    restart)
        echo "Restarting service"
        ;;
    status)
        echo "Checking status"
        ;;
    *)
        echo "Usage: $0 {start|stop|restart|status}"
        exit 1
        ;;
esac
```

## Command Substitution

```bash
# Capture command output into variable
result=$(command)
result=`command`    # Old syntax (not recommended)

# Example
current_date=$(date +%Y-%m-%d)
echo "Today is $current_date"

# Use in conditionals
if [ $(whoami) = "root" ]; then
    echo "Running as root"
fi

# Nested command substitution
users_count=$(cat /etc/passwd | wc -l)
echo "System has $users_count users"
```

## Script Examples

### Example 1: Check if user exists

```bash
#!/bin/bash
# Check if user provided
if [ -z "$1" ]; then
    echo "Usage: $0 <username>"
    exit 1
fi

# Check if user exists
if id "$1" &>/dev/null; then
    echo "User $1 exists"
    id "$1"
else
    echo "User $1 does not exist"
    exit 1
fi
```

### Example 2: Backup script with timestamp

```bash
#!/bin/bash
# Simple backup script

SOURCE_DIR="/home/user/documents"
BACKUP_DIR="/backup"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/backup_$TIMESTAMP.tar.gz"

if [ ! -d "$SOURCE_DIR" ]; then
    echo "Error: Source directory not found"
    exit 1
fi

echo "Creating backup..."
tar czf "$BACKUP_FILE" "$SOURCE_DIR"

if [ $? -eq 0 ]; then
    echo "Backup created: $BACKUP_FILE"
else
    echo "Backup failed"
    exit 1
fi
```

### Example 3: Loop through processes

```bash
#!/bin/bash
# List processes and count them

process_count=0
while read line; do
    if [ -n "$line" ]; then
        ((process_count++))
    fi
done < <(ps aux)

echo "Total processes: $process_count"
```

## Script Debugging

```bash
# Run script in debug mode (shows commands as executed)
bash -x script.sh

# Enable debugging inside script
#!/bin/bash
set -x    # Enable debugging
commands here
set +x    # Disable debugging

# Show verbose output
bash -v script.sh

# Trace function calls
set -c

# Check syntax without running
bash -n script.sh

# Debug single line
bash -x -c 'echo $HOME'
```

## Script Exit Status

```bash
# Check previous command exit status
./script.sh
echo $?    # 0 = success, 1-255 = error

# In script, set exit status
exit 0     # Success
exit 1     # Error

# Chain commands with &&, ||
command1 && command2    # command2 runs only if command1 succeeds
command1 || command2    # command2 runs only if command1 fails
```

## Tips and What to Remember

- **Always Quote Variables**: Use `"$var"` instead of `$var` to prevent word splitting
- **Shebang Required**: Always include `#!/bin/bash` at script top
- **Test Carefully**: Use `bash -n` before running new scripts
- **Error Checking**: Check command exit status ($?) for error handling
- **Documentation**: Add comments explaining script purpose and parameters

## Critical Comments

- Shell scripting is essential for system administration
- Scripts must be tested in non-production before deployment
- Always make scripts readable with comments and proper formatting

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Bash Version** | 5.x | **5.2+** |
| **Syntax** | Standard | Same |
| **Script Execution** | Standard | Same |
| **Error Handling** | Standard | Same |
| **New Features** | None significant | **Improved POSIX compliance** |

---

# Chapter 20: Configuring SSH

## Exam-Relevant Objectives

- Configure sshd server options
- Restrict SSH access by user and host
- Set up key-based authentication
- Manage SSH ports and SELinux contexts
- Secure SSH configuration

## SSH Basics

**SSH (Secure Shell)**: Protocol for secure remote administration and file transfer over unsecured network.

**SSHD (SSH Daemon)**: Server process running on remote system, listening on port 22 (default).

### SSH Server Installation

```bash
# SSH is typically pre-installed
# Install if needed
sudo dnf install openssh-server openssh-clients

# Check SSHD status
systemctl status sshd
sudo systemctl restart sshd
sudo systemctl enable sshd
```

## SSH Server Configuration

### Main Configuration File

```bash
# Main SSHD configuration
/etc/ssh/sshd_config

# Reload configuration after changes
sudo systemctl reload sshd

# Verify configuration syntax
sudo sshd -t
```

### Key SSHD Configuration Options

```bash
# Port configuration
Port 22                          # Default port
Port 2222                        # Alternative port (add multiple lines for multiple ports)

# Root login control
PermitRootLogin no              # Disable root login
PermitRootLogin without-password # Allow only with key
PermitRootLogin prohibit-password # Prohibit password (key only)

# Authentication
PubkeyAuthentication yes        # Allow key-based auth (default)
PasswordAuthentication yes      # Allow password auth
PermitEmptyPasswords no         # Disallow empty passwords

# User and group restrictions
AllowUsers user1 user2          # Only these users can login
AllowGroups sshusers wheel      # Only these groups can login
DenyUsers admin root            # These users cannot login
DenyGroups noaccess             # These groups cannot login

# X11 forwarding
X11Forwarding yes              # Allow GUI app forwarding
X11DisplayOffset 10

# Other options
MaxAuthTries 3                 # Max login attempts
MaxSessions 2                  # Max concurrent sessions
Compression delayed            # Compress after auth
ClientAliveInterval 60         # Send keepalive every 60 seconds
ClientAliveCountMax 3          # Close if no response after 3 keepalives
```

### Configuring SSHD

```bash
# Edit configuration
sudo vim /etc/ssh/sshd_config

# Example changes
# Change port to 2222
# Port 2222
# 
# Disable root login
# PermitRootLogin no
#
# Allow specific users
# AllowUsers bob alice

# Verify syntax
sudo sshd -t

# Reload SSHD
sudo systemctl reload sshd

# Verify changes took effect
ss -tln | grep :2222    # For new port
```

## Restricting SSH Access

### By User

```bash
# Allow only specific users
AllowUsers bob alice john

# Allow user from specific host
AllowUsers bob@192.168.1.10 alice@192.168.1.20

# Deny specific users
DenyUsers admin root testuser
```

### By Group

```bash
# Allow specific groups
AllowGroups sshusers wheel developers

# Deny specific groups
DenyGroups noaccess nologin
```

### Order of Evaluation

1. `DenyUsers` - Deny this user first
2. `AllowUsers` - If set, only these users
3. `DenyGroups` - Deny this group
4. `AllowGroups` - If set, only these groups

## Key-Based Authentication

### Generating SSH Keys

```bash
# Generate key pair (interactive)
ssh-keygen

# Generate with specific parameters
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519

# Generate RSA key with 4096 bits
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa

# Keys generated:
# Private key: ~/.ssh/id_ed25519 (keep secret, 0600 permissions)
# Public key: ~/.ssh/id_ed25519.pub (share with servers)
```

### Default Key Locations

```bash
~/.ssh/id_ed25519      # ED25519 private key (preferred in RHEL 10)
~/.ssh/id_ed25519.pub  # ED25519 public key
~/.ssh/id_rsa          # RSA private key
~/.ssh/id_rsa.pub      # RSA public key
~/.ssh/authorized_keys # Public keys authorized for login
```

### Deploying Public Keys

```bash
# Automated deployment
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server
ssh-copy-id user@server  # Uses default key

# Manual deployment
cat ~/.ssh/id_ed25519.pub | ssh user@server "cat >> ~/.ssh/authorized_keys"

# On server side, verify
ls -la ~/.ssh/authorized_keys
# Should have permissions: 0600
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

### SSH Key Permissions

```bash
# Correct permissions are critical
chmod 700 ~/.ssh              # Home directory for SSH
chmod 600 ~/.ssh/id_ed25519   # Private key
chmod 644 ~/.ssh/id_ed25519.pub  # Public key
chmod 600 ~/.ssh/authorized_keys # Authorized keys
```

### Using SSH Keys

```bash
# Connect using key
ssh -i ~/.ssh/id_ed25519 user@server

# Use default key (no -i needed if in ~/.ssh)
ssh user@server

# List available keys
ssh-add -l

# Add key to SSH agent (cache passphrase)
ssh-add ~/.ssh/id_ed25519

# Remove key from agent
ssh-add -d ~/.ssh/id_ed25519

# Remove all keys from agent
ssh-add -D
```

## SSH Configuration for Security

### Recommended Secure Configuration

```bash
# Edit /etc/ssh/sshd_config
Port 22
AddressFamily inet               # IPv4 only
ListenAddress 0.0.0.0

# Authentication
PubkeyAuthentication yes
PasswordAuthentication no        # Disable password auth
PermitEmptyPasswords no
PermitRootLogin no              # Never allow root login
AllowUsers bob alice

# Security
PermitUserEnvironment no
IgnoreRhosts yes
HostbasedAuthentication no
MaxAuthTries 3
MaxSessions 2
Compression delayed

# Logging
SyslogFacility AUTHPRIV
LogLevel INFO

# Timeouts
ClientAliveInterval 300
ClientAliveCountMax 2

# Reload
systemctl reload sshd
```

## SSH and SELinux

### SSH Port Configuration with SELinux

```bash
# Change SSHD port to non-standard (e.g., 2222)
# 1. Edit /etc/ssh/sshd_config
sudo vim /etc/ssh/sshd_config
# Add: Port 2222

# 2. Update SELinux to allow port
sudo semanage port -a -t ssh_port_t -p tcp 2222

# 3. Verify
sudo semanage port -l | grep ssh_port_t

# 4. Reload SSHD
sudo systemctl reload sshd

# 5. Open firewall
sudo firewall-cmd --add-port=2222/tcp --permanent
sudo firewall-cmd --reload
```

## Testing SSH Configuration

```bash
# Check SSHD configuration syntax
sudo sshd -t

# Connect with verbose output (troubleshooting)
ssh -v user@server
ssh -vvv user@server    # Very verbose

# Test key-based auth
ssh -i ~/.ssh/id_ed25519 user@server

# Test with password
ssh user@server

# Connect to specific port
ssh -p 2222 user@server

# Check remote services listening
ss -tln | grep -E ':22|:2222'
```

## SSH Client Configuration

### ~/.ssh/config

```bash
# Client-side configuration file
# ~/.ssh/config

Host server1
    HostName 192.168.1.100
    User bob
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    
Host server2
    HostName server2.example.com
    User alice
    IdentityFile ~/.ssh/id_rsa

# Use with:
ssh server1  # Connects as bob to 192.168.1.100:2222
ssh server2  # Connects as alice to server2.example.com
```

## Tips and What to Remember

- **ED25519 Keys**: Use in RHEL 10 (smaller, faster than RSA)
- **Key Permissions**: Always 0600 for private key; SSH refuses to use if wrong
- **Never Root SSH**: Always disable root SSH and use sudo
- **Restart vs Reload**: Use `reload` for config changes (no connection drop)
- **Test Before Deploying**: Verify `sshd -t` before reload

## Critical Comments

- SSH security is fundamental; misconfiguration exposes systems
- Always keep backup shell access (console) in case SSH is misconfigured
- Key-based auth is more secure than passwords; always use on production systems

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **OpenSSH Version** | 8.x | **9.9** |
| **ED25519 Support** | Yes | **Default** |
| **RSA Support** | Yes | Same |
| **Key Permissions** | Checked | **Stricter checks** |
| **sshd-keysign** | Standard | **SUID-based changes** |
| **Security Defaults** | Relaxed | **Stricter** |

---

# Chapter 21: Managing Apache HTTP Service

## Exam-Relevant Objectives

- Install and configure Apache HTTP Server (httpd)
- Start, stop, and enable httpd service
- Configure ports and SELinux contexts
- Create virtual hosts
- Manage firewall rules for HTTP/HTTPS

## Apache HTTP Server Installation

### Installing HTTPD

```bash
# Install Apache HTTP Server
sudo dnf install httpd

# Start HTTPD service
sudo systemctl start httpd

# Enable to start on boot
sudo systemctl enable httpd

# Verify status
sudo systemctl status httpd
sudo systemctl is-active httpd

# Check if listening on port 80
ss -tln | grep :80
```

## HTTPD Configuration Files

### Directory Structure

| Path | Purpose |
|------|---------|
| `/etc/httpd/conf/httpd.conf` | Main config file (do not modify directly) |
| `/etc/httpd/conf.d/` | Additional config files |
| `/etc/httpd/conf.modules.d/` | Module configuration |
| `/var/www/html/` | Default document root |
| `/var/log/httpd/` | Log files (access.log, error.log) |

### Main Configuration File

```bash
# Main config
/etc/httpd/conf/httpd.conf

# Key directives
Listen 80                       # Listen port
ServerAdmin admin@example.com   # Admin email
DocumentRoot /var/www/html     # Default content directory
```

### Editing HTTPD Configuration

```bash
# Edit main config
sudo vim /etc/httpd/conf/httpd.conf

# Example: Change port from 80 to 8080
# Find: Listen 80
# Change to: Listen 8080

# Create separate config in conf.d
sudo vim /etc/httpd/conf.d/mysite.conf

# Test configuration syntax
sudo httpd -t
apachectl configtest

# Reload configuration
sudo systemctl reload httpd

# Restart service (breaks connections)
sudo systemctl restart httpd
```

## Virtual Hosts

### Creating Virtual Hosts

```bash
# Create virtual host config file
sudo vim /etc/httpd/conf.d/site1.conf

# Example virtual host configuration
<VirtualHost *:80>
    ServerName site1.example.com
    ServerAlias www.site1.example.com
    DocumentRoot /var/www/site1
    <Directory /var/www/site1>
        Require all granted
    </Directory>
</VirtualHost>

# Create document root
sudo mkdir -p /var/www/site1
sudo chown apache:apache /var/www/site1
sudo chmod 755 /var/www/site1

# Add content
echo "Welcome to site1" | sudo tee /var/www/site1/index.html

# Set SELinux context
sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/site1(/.*)?"
sudo restorecon -Rv /var/www/site1

# Test and reload
sudo httpd -t
sudo systemctl reload httpd
```

### Name-Based Virtual Hosts

```bash
# Enable NameVirtualHost (usually default in RHEL 10)
# In /etc/httpd/conf/httpd.conf or conf.d/

# Multiple sites on same IP with different hostnames
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html/example.com
</VirtualHost>

<VirtualHost *:80>
    ServerName blog.example.com
    DocumentRoot /var/www/html/blog
</VirtualHost>
```

## Port Configuration

### Changing HTTPD Port

```bash
# Change port in /etc/httpd/conf/httpd.conf
sudo vim /etc/httpd/conf/httpd.conf
# Change: Listen 80
# To: Listen 8080

# Update SELinux to allow port
sudo semanage port -a -t http_port_t -p tcp 8080

# Verify
sudo semanage port -l | grep http_port_t

# Test configuration
sudo httpd -t

# Reload
sudo systemctl reload httpd

# Open firewall
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

## SELinux and HTTPD

### SELinux File Contexts

```bash
# Default HTTPD contexts
# /var/www/html files should be httpd_sys_content_t
# /var/www/cgi-bin should be httpd_sys_script_exec_t

# Check context
ls -Z /var/www/html/

# Fix context for custom directories
sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/mysite(/.*)?"
sudo restorecon -Rv /var/www/mysite

# Fix write-able directories (for uploads)
sudo semanage fcontext -a -t httpd_sys_rw_content_t "/var/www/uploads(/.*)?"
sudo restorecon -Rv /var/www/uploads
```

### Troubleshooting SELinux with HTTPD

```bash
# Check for SELinux denials
sudo grep httpd /var/log/audit/audit.log

# Audit specific errors
sudo ausearch -m avc -ts today | grep httpd

# If permission denied, check context
ls -Z /var/www/html
ps -Z | grep httpd

# Temporarily set permissive for testing
sudo setenforce 0

# Re-enable enforcing
sudo setenforce 1

# Generate policy module for custom violations
sudo audit2allow -a -M httpd_custom
sudo semodule -i httpd_custom.pp
```

## Firewall Configuration for HTTPD

```bash
# Add HTTP service to firewall
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=https --permanent

# Or add specific ports
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent

# Apply changes
sudo firewall-cmd --reload

# Verify
sudo firewall-cmd --list-all
```

## Testing HTTPD Configuration

```bash
# Syntax check
sudo httpd -t
apachectl configtest

# Check listening ports
sudo ss -tln | grep httpd
sudo netstat -tln | grep :80

# Test connection locally
curl http://localhost
curl http://localhost:8080

# Test remote connection
curl http://server.example.com

# Check logs for errors
sudo tail -f /var/log/httpd/error_log
sudo tail -f /var/log/httpd/access_log
```

## HTTPD Service Management

```bash
# Start service
sudo systemctl start httpd

# Stop service
sudo systemctl stop httpd

# Restart (breaks connections)
sudo systemctl restart httpd

# Reload config (keeps connections)
sudo systemctl reload httpd

# Check status
sudo systemctl status httpd

# Enable on boot
sudo systemctl enable httpd

# Disable on boot
sudo systemctl disable httpd
```

## Tips and What to Remember

- **Test Configuration**: Always run `httpd -t` before restart
- **Use reload**: Prefer `reload` over `restart` to minimize disruption
- **SELinux Contexts**: Web content must have correct context
- **Document Root Permissions**: Apache runs as 'apache' user; verify directory ownership
- **Log Analysis**: Check error_log for configuration or permission issues

## Critical Comments

- HTTPD misconfiguration is common; always test before applying
- SELinux is often the culprit if HTTPD starts but pages don't load
- Always backup config before making changes

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **HTTPD Version** | 2.4.x | Same |
| **TLS Support** | 1.2, 1.3 | **TLS 1.3 enhanced** |
| **Configuration** | Standard | Same |
| **Virtual Hosts** | Standard | Same |
| **SELinux Integration** | Standard | Same |

---

# Chapter 22: SELinux

## Exam-Relevant Objectives

- Understand SELinux modes (enforcing, permissive, disabled)
- Manage SELinux contexts (user, role, type, level)
- Configure port labels and boolean settings
- Diagnose and resolve SELinux policy violations
- Restore default file contexts

## SELinux Basics

**SELinux (Security-Enhanced Linux)**: Mandatory access control system that restricts access based on security policies beyond traditional Unix permissions.

**Three Modes**:
- **Enforcing**: SELinux policy is enforced; denied actions are blocked and logged
- **Permissive**: Policy is logged but not enforced; all actions allowed
- **Disabled**: SELinux is off; no policy enforcement or logging

### SELinux Configuration

```bash
# Check current mode
getenforce

# Check detailed status
sestatus
sestatus -v

# Persistent configuration file
/etc/selinux/config

# View policy
cat /etc/selinux/config
```

### Changing SELinux Modes

```bash
# Temporarily set permissive (session-only)
sudo setenforce 0
getenforce  # Should show Permissive

# Temporarily set enforcing
sudo setenforce 1
getenforce  # Should show Enforcing

# Persistent change (requires reboot)
sudo vim /etc/selinux/config
# Change: SELINUX=enforcing

# Or edit via command
sudo sed -i 's/SELINUX=.*/SELINUX=permissive/' /etc/selinux/config
```

### Disabling SELinux (NOT RECOMMENDED)

```bash
# Disable at boot (requires reboot)
sudo vim /etc/selinux/config
# Change: SELINUX=disabled

# Or via kernel boot parameter
# Add selinux=0 to kernel line in GRUB

# Re-enable SELinux
sudo vim /etc/selinux/config
# Change: SELINUX=enforcing
# Reboot
# System will relabel all files (takes time)
touch /.autorelabel
```

## SELinux Contexts

### Context Components

SELinux contexts have 4 parts: `user:role:type:level`

```bash
# View file context
ls -Z /etc/passwd
unconfined_u:object_r:passwd_file_t:s0

# View process context
ps -Z | grep httpd
system_u:system_r:httpd_t:s0

# Components:
# user: unconfined_u (user context)
# role: object_r (object role) or system_r (process role)
# type: passwd_file_t, httpd_t (type/domain)
# level: s0 (security level for MLS)
```

### Listing and Finding Contexts

```bash
# Find policy for specific type
semanage fcontext -l | grep httpd_sys_content_t

# Show what type a directory should have
semanage fcontext -l | grep "/var/www"

# Search for context by path
semanage fcontext -l | grep apache
```

## Managing File Contexts

### Restoring Default Contexts

```bash
# Restore context for single file
sudo restorecon /var/www/html/index.html

# Restore recursively for directory
sudo restorecon -R /var/www/html
sudo restorecon -Rv /var/www/html  # Verbose

# Restore context matching pattern
sudo restorecon -R -e /var/www/html/keep_this /var/www/html
```

### Adding Custom Contexts

```bash
# Add custom directory context
sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/mysite(/.*)?"

# Add context for writable web directory
sudo semanage fcontext -a -t httpd_sys_rw_content_t "/var/www/uploads(/.*)?"

# Apply new context
sudo restorecon -Rv /var/www/mysite
sudo restorecon -Rv /var/www/uploads

# List custom contexts
sudo semanage fcontext -l -C

# Remove custom context
sudo semanage fcontext -d "/var/www/mysite(/.*)?"
```

## Managing SELinux Ports

### Labeling Non-Standard Ports

```bash
# Show SSH port labels
sudo semanage port -l | grep ssh_port_t

# Add custom port for HTTP
sudo semanage port -a -t http_port_t -p tcp 8080

# Add custom port for HTTPS
sudo semanage port -a -t http_port_t -p tcp 8443

# Add custom port for SSH
sudo semanage port -a -t ssh_port_t -p tcp 2222

# List port labels
sudo semanage port -l | grep -E "http_port_t|ssh_port_t"

# Remove port label
sudo semanage port -d -t http_port_t -p tcp 8080

# Verify port is allowed
sudo semanage port -l | grep 8080
```

## SELinux Booleans

### Understanding Booleans

**Boolean**: Flag that modifies SELinux policy behavior without changing the policy itself.

```bash
# List all booleans
sudo getsebool -a

# Search for specific boolean
sudo getsebool -a | grep ftp

# Get specific boolean
sudo getsebool ftp_home_dir

# Set boolean temporarily (session)
sudo setsebool ftp_home_dir on
sudo setsebool ftp_home_dir off

# Set boolean permanently
sudo setsebool -P ftp_home_dir on

# View boolean description
sudo semanage boolean -l | grep ftp_home_dir
```

### Common Booleans for HTTPD

```bash
# Allow HTTPD to access home directories
sudo setsebool -P httpd_enable_homedirs=on

# Allow HTTPD to connect to database
sudo setsebool -P httpd_can_network_connect_db=on

# Allow HTTPD to connect to network
sudo setsebool -P httpd_can_network_connect=on

# Allow HTTPD to send mail
sudo setsebool -P httpd_can_sendmail=on

# View all HTTPD-related booleans
sudo semanage boolean -l | grep httpd
```

## Diagnosing SELinux Violations

### Audit Log Analysis

```bash
# Check audit logs for SELinux denials
sudo grep denied /var/log/audit/audit.log

# Filter by application
sudo grep "avc.*denied" /var/log/audit/audit.log | grep httpd

# Search by type
sudo ausearch -m avc -ts today

# Real-time monitoring
sudo tail -f /var/log/audit/audit.log | grep denied
```

### Using ausearch and audit2allow

```bash
# Search for recent denials
sudo ausearch -m avc -ts recent

# Find denials for specific user
sudo ausearch -m avc -u system_u -ts today

# Generate suggested policy module
sudo audit2allow -a    # Review suggested changes
sudo audit2allow -a -M custom
sudo semodule -i custom.pp

# Generate policy without installing
sudo audit2allow -a -M custom --no-audit

# View generated policy
cat custom.te
cat custom.fc
```

### Troubleshooting Workflow

```bash
# 1. Reproduce the problem
# 2. Check error logs
tail -f /var/log/httpd/error_log

# 3. Check SELinux audit log
sudo tail -f /var/log/audit/audit.log

# 4. Identify denied action
sudo grep "denied" /var/log/audit/audit.log | tail -5

# 5. Temporarily set permissive to test
sudo setenforce 0
# Run test
sudo setenforce 1

# 6. Generate policy if needed
sudo ausearch -m avc -ts recent | sudo audit2allow -M custom
sudo semodule -i custom.pp

# 7. Re-enable enforcing
sudo setenforce 1
```

## SELinux for Services

### Checking Service Contexts

```bash
# Check running service context
ps -Z | grep httpd
ps -Z | grep sshd
ps -Z | grep rsyslogd

# Check service domain type
ps -Z | grep -o "[a-z_]*_t" | sort -u
```

### Allowing Service-Specific Actions

```bash
# Examples for common services

# SSH - Allow home directory access
sudo setsebool -P ssh_sysadm_login=on

# FTP - Allow home directory access
sudo setsebool -P ftp_home_dir=on

# HTTPD - Allow network database connection
sudo setsebool -P httpd_can_network_connect_db=on

# Samba - Allow home shares
sudo setsebool -P samba_share_nfs=on
```

## SELinux Policy Documentation

```bash
# View SELinux policy documentation
man selinux

# Search for policy topics
man -k selinux

# Install SELinux documentation
sudo dnf install selinux-policy-doc

# View man pages for specific types
man httpd_selinux
man sshd_selinux
man crond_selinux

# Find relevant documentation
ls /usr/share/doc/selinux-policy*
```

## Tips and What to Remember

- **Always Start Permissive**: When troubleshooting, set permissive mode first
- **Check Three Things**: File context, process context, booleans (in that order)
- **Use -P for Persistent**: Remember -P flag for permanent changes
- **Audit Logs are Your Friend**: /var/log/audit/audit.log contains all violations
- **Practice audit2allow**: Learn to generate suggested policies from denials

## Critical Comments

- SELinux is complex but powerful; most service issues are SELinux-related
- Never disable SELinux on production systems; instead, set permissive or create policy
- Always verify policy before deploying to production

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **SELinux Version** | 3.x | Same |
| **Policy Improvements** | Standard | **Enhanced policies** |
| **Audit Logging** | Standard | **Improved verbosity** |
| **Boolean Management** | Standard | Same |
| **audit2allow Tool** | Standard | Same |

---

# Chapter 23: Configuring a Firewall

## Exam-Relevant Objectives

- Understand firewalld zones and services
- Manage firewall rules with firewall-cmd
- Create rich rules for advanced filtering
- Configure firewall for common services
- Make firewall changes persistent

## Firewalld Basics

**firewalld**: Dynamic firewall management tool that provides firewall rules without reloading.

**nftables**: Backend for firewalld rules (version 1.1.1 in RHEL 10).

### Firewalld Architecture

```bash
# Check firewalld status
systemctl status firewalld
sudo systemctl enable --now firewalld

# Verify firewall is active
sudo firewall-cmd --state

# Show default zone
sudo firewall-cmd --get-default-zone
```

## Firewalld Zones

### Understanding Zones

**Zone**: Set of rules that define trust level of network interface or source address.

| Zone | Purpose | Default Behavior |
|------|---------|------------------|
| **public** | Untrusted networks (default) | Drop incoming, allow outgoing |
| **trusted** | Trusted networks | Allow all |
| **drop** | Drop all incoming | Block everything |
| **block** | Block all incoming (with rejects) | Block with notification |
| **dmz** | Demilitarized zone | Allow select services |
| **external** | External networks (masquerade) | Allow select services |
| **home** | Home networks (trusted) | Allow select services |
| **internal** | Internal networks (trusted) | Allow select services |
| **work** | Work networks | Allow select services |

### Managing Zones

```bash
# List available zones
sudo firewall-cmd --get-zones

# Get default zone
sudo firewall-cmd --get-default-zone

# Set default zone
sudo firewall-cmd --set-default-zone=home

# List zones with configuration
sudo firewall-cmd --list-all --zone=public
sudo firewall-cmd --list-all --zone=work

# Show all active zones
sudo firewall-cmd --get-active-zones
```

## Firewall Rules

### Services

```bash
# List available services
sudo firewall-cmd --get-services | tr ' ' '\n' | head -20

# List services in specific zone
sudo firewall-cmd --zone=public --list-services

# Add service to zone
sudo firewall-cmd --zone=public --add-service=http
sudo firewall-cmd --zone=public --add-service=https

# Remove service
sudo firewall-cmd --zone=public --remove-service=http

# Make changes permanent
sudo firewall-cmd --zone=public --add-service=http --permanent

# Reload firewall to apply permanent changes
sudo firewall-cmd --reload
```

### Ports

```bash
# Add port temporarily
sudo firewall-cmd --zone=public --add-port=8080/tcp

# Add port permanently
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent

# Add port range
sudo firewall-cmd --zone=public --add-port=8000-8100/tcp --permanent

# Remove port
sudo firewall-cmd --zone=public --remove-port=8080/tcp --permanent

# List ports
sudo firewall-cmd --zone=public --list-ports

# Reload to apply changes
sudo firewall-cmd --reload
```

### Rich Rules

**Rich Rule**: Complex firewall rule with source/destination, port, protocol specifications.

```bash
# Basic rich rule syntax
firewall-cmd --add-rich-rule='rule family="ipv4" source address="IP" [protocol] action'

# Allow SSH from specific network
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="10.0.0.0/8" service name="ssh" accept' --permanent

# Deny HTTP from specific IP
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.1.5" service name="http" reject' --permanent

# Allow MySQL from specific port range
sudo firewall-cmd --add-rich-rule='rule family="ipv4" service name="mysql" accept' --permanent

# Log rejected packets
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="10.1.0.0/16" port protocol="tcp" port="22" log prefix="SSH-BLOCKED" level="warning" reject' --permanent

# List rich rules
sudo firewall-cmd --list-rich-rules

# Remove rich rule
sudo firewall-cmd --remove-rich-rule='rule family="ipv4" source address="10.0.0.0/8" service name="ssh" accept' --permanent

# Reload after changes
sudo firewall-cmd --reload
```

## Complete Firewall Configuration

### Standard Configuration

```bash
# Set default zone
sudo firewall-cmd --set-default-zone=public

# Add common services permanently
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=https --permanent
sudo firewall-cmd --add-service=ssh --permanent

# Add custom port
sudo firewall-cmd --add-port=2222/tcp --permanent

# Reload to apply
sudo firewall-cmd --reload

# Verify configuration
sudo firewall-cmd --list-all
```

### Firewall for Web Server

```bash
# Set zone to public
sudo firewall-cmd --set-default-zone=public

# Add web services
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=https --permanent

# Add SSH for management
sudo firewall-cmd --add-service=ssh --permanent

# Reload
sudo firewall-cmd --reload

# Verify
sudo firewall-cmd --list-all
```

### Firewall Configuration File

```bash
# Firewall configuration stored in
/etc/firewalld/zones/public.xml
/etc/firewalld/zones/work.xml
etc.

# View zone configuration
cat /etc/firewalld/zones/public.xml

# Configuration is usually managed via firewall-cmd
# Manual editing requires reload
sudo firewall-cmd --reload
```

## Firewall Troubleshooting

```bash
# Show all active rules
sudo firewall-cmd --list-all

# Show rules per zone
sudo firewall-cmd --zone=public --list-all
sudo firewall-cmd --zone=trusted --list-all

# Show which zone interface is in
sudo firewall-cmd --get-active-zones

# Assign interface to zone
sudo firewall-cmd --zone=public --change-interface=ens160 --permanent

# Check service definition
cat /usr/lib/firewalld/services/http.xml

# Test connectivity
telnet server.example.com 80
nc -zv server.example.com 80

# Monitor firewall activity
sudo tail -f /var/log/firewalld
```

## Firewall GUI (Optional)

```bash
# Install firewall GUI (not required for exam)
sudo dnf install firewall-config

# Launch GUI
firewall-config

# GUI allows visual zone and rule management
```

## Tips and What to Remember

- **--permanent Flag**: Always use for persistent changes
- **Reload Required**: After --permanent changes, run `firewall-cmd --reload`
- **Temporary vs Permanent**: Changes without --permanent are lost on reload
- **Zone Selection**: Choose appropriate zone for your use case
- **Service vs Port**: Use service when available; port when service doesn't exist

## Critical Comments

- Firewall misconfiguration can lock you out; always have console access or alternative method
- Test firewall rules before making them permanent in production
- Document all custom rules for future reference

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **firewalld Version** | Latest | Same |
| **nftables Backend** | 1.0.x | **1.1.1** |
| **Zone Management** | Standard | Same |
| **Rich Rules** | Standard | Same |
| **DDoS Protection** | Basic | **Enhanced** |

---

# Chapter 24: Accessing Network Storage

## Exam-Relevant Objectives

- Mount NFS (Network File System) shares
- Configure autofs for automatic mounting
- Set up NFS client configuration
- Manage mount options and performance

## NFS Overview

**NFS (Network File System)**: Protocol for sharing filesystems over network.

**NFS Server**: System that exports (shares) filesystems.

**NFS Client**: System that mounts (accesses) shared filesystems.

### Installing NFS Utilities

```bash
# Install NFS client utilities
sudo dnf install nfs-utils

# Start NFS service (if needed for client)
sudo systemctl start nfs-client.target

# Check NFS support
mount -t nfs

# Verify NFSv4 support
grep nfs /proc/filesystems
```

## Mounting NFS Shares

### Manual NFS Mounting

```bash
# Show NFS exports from server
showmount -e nfs_server.example.com

# Create mount point
sudo mkdir -p /mnt/nfs_share

# Mount NFS share temporarily
sudo mount -t nfs nfs_server.example.com:/exported/path /mnt/nfs_share

# Verify mount
mount | grep nfs
df -h | grep nfs

# Unmount
sudo umount /mnt/nfs_share
```

### Permanent NFS Mounting with /etc/fstab

```bash
# Get NFS export details
showmount -e nfs_server.example.com

# Add to /etc/fstab
sudo vim /etc/fstab

# Example entry
nfs_server.example.com:/data /mnt/nfs_data nfs defaults 0 0
nfs_server.example.com:/home /mnt/nfs_home nfs defaults,soft,timeo=30 0 0

# Mount options explained:
# defaults: Standard options
# soft: Return error if server unreachable
# hard: Wait forever (default)
# timeo=30: Timeout 30 deciseconds
# retrans=3: Retry 3 times
# ro: Read-only
# rw: Read-write

# Test mount
sudo mount -a

# Verify
df -h | grep nfs
```

### NFS Mount Options

| Option | Purpose |
|--------|---------|
| **soft** | Fail quickly if server unreachable |
| **hard** | Keep retrying (default, safer) |
| **timeo=N** | Timeout in deciseconds (1/10 sec) |
| **retrans=N** | Number of retransmissions |
| **ro** | Read-only mount |
| **rw** | Read-write mount |
| **async** | Asynchronous writes |
| **sync** | Synchronous writes |
| **nfsvers=4** | Use NFSv4 protocol |

## Autofs for Automatic Mounting

### Installing and Configuring autofs

```bash
# Install autofs
sudo dnf install autofs

# Start autofs service
sudo systemctl enable --now autofs

# Configuration files
/etc/auto.master        # Mount point and map file references
/etc/auto.misc          # Miscellaneous mounts
/etc/auto.nfs           # Custom NFS mount map

# Create NFS-specific map file
sudo vim /etc/auto.nfs
```

### Autofs Configuration Example

```bash
# Edit /etc/auto.master
sudo vim /etc/auto.master

# Add line for NFS automounts
/nfsdata /etc/auto.nfs

# Create map file /etc/auto.nfs
sudo vim /etc/auto.nfs

# Example entry (tab-separated)
nfsshare -rw nfs_server.example.com:/data

# The above means:
# /nfsdata/nfsshare -> mounts nfs_server.example.com:/data
# -rw: read-write options

# Multiple mounts in same map
share1 -rw server1.example.com:/export1
share2 -ro server2.example.com:/export2
home -rw server3.example.com:/home

# Reload autofs
sudo systemctl restart autofs

# Access mount point (autofs mounts automatically)
cd /nfsdata/nfsshare
ls

# Check active mounts
mount | grep autofs

# Show autofs statistics
systemctl status autofs
```

### Wildcard Autofs Configuration

```bash
# Edit /etc/auto.master
sudo vim /etc/auto.master

# Add wildcard mount point
/users /etc/auto.users

# Edit /etc/auto.users
sudo vim /etc/auto.users

# Wildcard entry (mount any user home directory)
* -rw nfs_server.example.com:/home/&

# Usage:
# Access /users/bob -> mounts nfs_server.example.com:/home/bob
# Access /users/alice -> mounts nfs_server.example.com:/home/alice
# & is replaced with the wildcard match
```

## NFS Troubleshooting

```bash
# Check NFS server exports
showmount -e server.example.com

# Check NFS mounts
mount | grep nfs

# Show mount options
df -h
cat /proc/mounts | grep nfs

# Test NFS connectivity
ping server.example.com
rpcinfo -p server.example.com    # Show RPC services

# Check NFS logs
sudo tail -f /var/log/messages | grep nfs
sudo dmesg | grep nfs

# Force unmount
sudo umount -f /mnt/nfs_share

# Lazy unmount
sudo umount -l /mnt/nfs_share

# Test NFS performance
dd if=/dev/zero of=/mnt/nfs_share/testfile bs=1M count=100
```

## Tips and What to Remember

- **Soft vs Hard**: Use soft for interactive, hard for critical; soft timeouts faster
- **showmount First**: Always check exports before mounting
- **autofs Benefits**: Auto mounts on access, unmounts when idle
- **Fstab Mounting**: Verify with `mount -a` before reboot
- **NFS Performance**: Use NFSv4 when possible for better performance

## Critical Comments

- NFS security: Use IP restrictions and consider firewalls
- NFS hangs: If server goes down, client can hang; use soft mount or NFS v4 with recovery
- Always backup NFS-mounted data; NFS is network-dependent

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **NFS Version** | 4.x | Same |
| **autofs** | Standard | Same |
| **NFSv4** | Default | Same |
| **Performance** | Good | **Improved** |
| **Mount Options** | Standard | Same |

---

# Chapter 25: Configuring Time Services

## Exam-Relevant Objectives

- Configure chrony for NTP time synchronization
- Manage timezone with timedatectl
- Ensure system time consistency
- Configure firewall for NTP access

## System Time Management

### Checking System Time

```bash
# View date and time
date

# View in specific format
date +%Y-%m-%d
date +%H:%M:%S

# View timezone
timedatectl status
timedatectl list-timezones

# Check hardware clock
sudo hwclock --show
```

## Timedatectl

### Time and Timezone Management

```bash
# Show current time and timezone
timedatectl status

# Show NTP status
timedatectl status | grep NTP

# List available timezones
timedatectl list-timezones

# List timezones matching pattern
timedatectl list-timezones | grep Europe

# Set timezone
sudo timedatectl set-timezone America/New_York
sudo timedatectl set-timezone Europe/London
sudo timedatectl set-timezone Asia/Tokyo

# Enable NTP synchronization
sudo timedatectl set-ntp true

# Disable NTP (manual time)
sudo timedatectl set-ntp false

# Set time manually (if NTP disabled)
sudo timedatectl set-time "2025-01-15 14:30:00"
```

## Chrony NTP Service

### Installing and Starting Chrony

```bash
# Install chrony
sudo dnf install chrony

# Start chrony daemon
sudo systemctl start chronyd

# Enable on boot
sudo systemctl enable chronyd

# Check status
sudo systemctl status chronyd
timedatectl status
```

### Chrony Configuration

```bash
# Main configuration file
/etc/chrony.conf

# View configuration
cat /etc/chrony.conf | grep -v "^#"

# Key configuration options
pool 2.rhel.pool.ntp.org iburst    # NTP pool
server 0.rhel.pool.ntp.org iburst  # Specific server
port 123                            # NTP port (default)
driftfile /var/lib/chrony/drift    # Frequency correction file
makestep 1.0 3                      # Make big time jumps
```

### Editing Chrony Configuration

```bash
# Edit chrony.conf
sudo vim /etc/chrony.conf

# Example configuration
# Use local NTP server
server ntp.internal.example.com iburst prefer

# Or use pool
pool ntp.ubuntu.com iburst

# Allow local clients (optional)
allow 192.168.0.0/16

# Save and restart
sudo systemctl restart chronyd

# Verify configuration
sudo chronyc sources
```

## Monitoring NTP Synchronization

### Using chronyc

```bash
# Show NTP sources
sudo chronyc sources

# Show detailed sources
sudo chronyc sources -v

# Show statistics
sudo chronyc tracking

# Show synchronization status
sudo chronyc -N tracking

# Show daemon information
sudo chronyc info

# Show server statistics
sudo chronyc serverstats

# Offline mode (no NTP adjustment)
sudo chronyc offline

# Online mode (resume NTP)
sudo chronyc online

# Manual time step (if allowed)
sudo chronyc makestep
```

### Checking Synchronization

```bash
# Show NTP status
timedatectl status | grep -E "NTP|synchronized"

# Show time difference
chronyc tracking | grep -E "System time|RMS offset"

# Monitor in real-time
watch 'chronyc tracking | grep -E "System time|RMS offset"'
```

## Firewall Configuration for NTP

```bash
# Allow NTP service through firewall
sudo firewall-cmd --add-service=ntp --permanent

# Or allow specific port
sudo firewall-cmd --add-port=123/udp --permanent

# Apply changes
sudo firewall-cmd --reload

# Verify
sudo firewall-cmd --list-ports
```

## Hardware Clock Synchronization

```bash
# View hardware clock
sudo hwclock

# Sync hardware clock to system time
sudo hwclock -w

# Sync system time to hardware clock
sudo hwclock -s

# Set hardware clock (dangerous)
sudo hwclock --set --date="2025-01-15 14:30:00"
```

## Troubleshooting NTP

```bash
# Check NTP service running
systemctl status chronyd

# Check NTP port listening
sudo ss -tlun | grep 123

# Verify time source connectivity
ping -c 1 pool.ntp.org

# Show NTP client logs
journalctl -u chronyd | tail -20

# Check firewall blocking
sudo firewall-cmd --list-all

# Force time synchronization
sudo chronyc makestep

# Restart chrony
sudo systemctl restart chronyd

# Detailed troubleshooting
sudo chronyc -N tracking
sudo chronyc sources -v
```

## Tips and What to Remember

- **Always Enable NTP**: Critical for security, authentication, logging
- **Pool vs Server**: Use pool for resilience; use server for internal NTP
- **Check Synchronization**: Verify with `timedatectl` or `chronyc tracking`
- **Timezone Matters**: Correct timezone essential for scheduled tasks and logs
- **Allow NTP Port**: Remember to open UDP 123 on firewall

## Critical Comments

- Time sync failures cause certificate validation errors, Kerberos failures
- NTP clients should ideally synchronize with multiple sources
- Keep hardware clock close to system time for boot correctness

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Chrony** | Default | Same |
| **NTP Pools** | Standard | Same |
| **timedatectl** | Standard | Same |
| **Synchronization** | Standard | **Improved reliability** |
| **Firewall Integration** | Standard | Same |

---

# Chapter 26: Managing Containers

## Exam-Relevant Objectives

- Run and manage containers using Podman
- Work with container images and registries
- Build container images with Buildah
- Configure containers as systemd services
- Understand container storage and security

## Container Basics

**Container**: Lightweight, isolated execution environment with its own filesystem, processes, and networking.

**Podman (Pod Manager)**: Container runtime compatible with Docker; can run rootless (as regular user).

**Container Image**: Read-only template containing application and dependencies.

**Container Registry**: Centralized storage for container images (Docker Hub, Quay, etc.).

## Installing Podman

### Installing Container Tools

```bash
# Install Podman and related tools
sudo dnf install -y podman buildah skopeo

# Verify installation
podman --version
buildah --version

# Check Docker compatibility
podman run hello-world

# Show system information
podman info
```

## Running Containers

### Basic Container Operations

```bash
# Search for image online
podman search nginx

# Pull image from registry
podman pull nginx

# Run container detached
podman run -d nginx
podman run -d --name webserver nginx

# Run container interactive
podman run -it alpine sh

# List running containers
podman ps

# List all containers (including stopped)
podman ps -a

# Show container details
podman inspect <container_id>

# View container logs
podman logs <container_id>
podman logs -f <container_id>    # Follow logs
```

### Container Lifecycle

```bash
# Start container
podman start <container_id>

# Stop container
podman stop <container_id>

# Restart container
podman restart <container_id>

# Pause container
podman pause <container_id>

# Unpause container
podman unpause <container_id>

# Remove container
podman rm <container_id>

# Force remove running container
podman rm -f <container_id>

# Remove all stopped containers
podman container prune

# Show container statistics
podman stats <container_id>
```

### Port Mapping

```bash
# Map host port to container port
podman run -d -p 8080:80 nginx
# Host port 8080 -> Container port 80

# Map specific host interface
podman run -d -p 127.0.0.1:8080:80 nginx

# Multiple port mappings
podman run -d -p 80:80 -p 443:443 nginx

# Dynamic port mapping
podman run -d -p 80 nginx  # Random host port
podman port <container_id>  # Show mapping

# Show listening ports
ss -tln | grep LISTEN
podman inspect <container_id> | grep -A 10 "PortBindings"
```

### Volume Mounting

```bash
# Mount host directory into container
podman run -d -v /host/path:/container/path nginx

# Mount read-only
podman run -d -v /host/path:/container/path:ro nginx

# Named volume
podman volume create myvolume
podman run -d -v myvolume:/data nginx

# List volumes
podman volume ls

# Remove volume
podman volume rm myvolume
```

## Container Images

### Working with Images

```bash
# List local images
podman images

# Show image details
podman inspect <image_id>
podman inspect <image_name>

# Remove image
podman rmi <image_id>

# Remove unused images
podman image prune

# Tag image
podman tag alpine:latest myrepo/alpine:custom

# Show image layers
podman history nginx

# Search image metadata
podman inspect --format='{{.RepoTags}}' <image_id>
```

### Image Registry Management

```bash
# Login to registry
podman login docker.io
podman login quay.io
podman login registry.example.com

# Show login credentials
cat ~/.config/containers/auth.json

# Logout
podman logout docker.io

# Push image to registry
podman tag myapp:latest quay.io/myuser/myapp:latest
podman push quay.io/myuser/myapp:latest

# Pull from specific registry
podman pull docker.io/library/nginx
podman pull quay.io/podman/stable

# List images by registry
podman images | grep docker.io
```

## Building Container Images

### Using Buildah

```bash
# Create container for building
buildah from alpine:latest

# Or interactive building
container=$(buildah from alpine:latest)

# Add content to container
buildah run $container apk add --no-cache nginx

# Set entry point
buildah config --entrypoint /usr/sbin/nginx $container

# Commit to image
buildah commit $container myapp:latest

# Build from Dockerfile
buildah bud -t myapp:latest .
buildah bud -t quay.io/myuser/myapp:v1.0 .
```

### Dockerfile Example

```bash
# Create Dockerfile
cat > Dockerfile << 'EOF'
FROM alpine:latest
RUN apk add --no-cache nginx
COPY index.html /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
EOF

# Build image
buildah bud -t mywebapp:latest .

# Run container from image
podman run -d -p 8080:80 mywebapp:latest
```

## Running Containers as Systemd Services

### Creating Systemd Service for Container

```bash
# Option 1: Use podman generate systemd
podman run -d --name myapp nginx
podman generate systemd --name myapp > /tmp/container-myapp.service

# Copy to systemd directory
sudo cp /tmp/container-myapp.service /etc/systemd/system/container-myapp.service

# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable --now container-myapp.service
sudo systemctl status container-myapp.service

# Option 2: Create service manually
sudo vim /etc/systemd/system/container-nginx.service
```

### Example Systemd Service

```bash
[Unit]
Description=Nginx Container
After=network.target

[Service]
Type=forking
ExecStart=/usr/bin/podman run -d --name nginx-web -p 80:80 nginx
ExecStop=/usr/bin/podman stop nginx-web
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

## Container Networking

### Network Management

```bash
# List networks
podman network ls

# Create custom network
podman network create mynetwork

# Run container on network
podman run -d --network mynetwork --name db alpine
podman run -d --network mynetwork --name app myapp

# Connect container to network
podman network connect mynetwork <container_id>

# Disconnect from network
podman network disconnect mynetwork <container_id>

# Inspect network
podman network inspect mynetwork
```

## Rootless Containers

### Running as Non-Root User

```bash
# Check if rootless Podman configured
podman run hello-world

# Configure rootless mode
podman system migrate

# Run as regular user (no sudo)
podman run -d nginx

# Check user namespace
podman info | grep -A 5 "rootless"

# Enable for automatic startup (user systemd)
systemctl --user enable podman
```

## Container Security

### Security Considerations

```bash
# Run with read-only filesystem
podman run -d --read-only nginx

# Run with no capabilities
podman run -d --cap-drop=all nginx

# Add specific capabilities
podman run -d --cap-add=NET_ADMIN nginx

# SELinux context
podman run -d --security-opt label=type:svirt_lxc_t nginx

# Seccomp profile
podman run -d --security-opt seccomp=unconfined nginx

# Resource limits
podman run -d -m 512m --cpus 1 nginx
```

## Container Troubleshooting

```bash
# Check container logs
podman logs <container_id>

# Show container status
podman ps -a
systemctl status container-myapp

# Inspect container
podman inspect <container_id>

# Execute command in running container
podman exec -it <container_id> sh

# Attach to container
podman attach <container_id>

# Monitor container
podman stats

# Debug Podman issues
podman info
podman system df

# Clean up unused resources
podman container prune
podman image prune
podman volume prune
podman network prune
podman system prune -a
```

## Tips and What to Remember

- **Podman is Docker-Compatible**: Most Docker commands work with Podman
- **Rootless by Default**: Podman can run as non-root (no sudo needed)
- **Systemd Integration**: Generate systemd services for auto-start
- **Registry Management**: Always specify registry; default is docker.io
- **Clean Regularly**: Use `prune` commands to free space

## Critical Comments

- Containers are not VMs; they share the kernel (lighter weight)
- Always pull images from trusted registries
- Container images should be as minimal as possible (security and size)
- Monitor container resource usage in production

## RHEL 9 → RHEL 10 Differences

| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Podman Version** | 4.x | **5.x** |
| **Buildah** | Supported | **Tightly integrated** |
| **Container Runtime** | Standard | Same |
| **Rootless Support** | Yes | **Improved** |
| **Systemd Integration** | Standard | **Better** |
| **Performance** | Good | **Enhanced** |

---

# Exam Preparation Strategy (Comprehensive)

## Study Areas by Priority - Updated for All 26 Chapters

### High Priority (45-55% of exam)
These topics appear frequently and are critical for exam success:

1. **Systemd & Services** (Chapter 11, 17, 12)
   - Service management (start, stop, enable, disable)
   - Targets and target switching (multi-user, graphical, rescue, emergency)
   - Systemd timers for scheduling
   - Service unit files and dependencies
   - **Exam Weight**: ~10-12%

2. **Storage Management** (Chapter 14, 15)
   - Partitioning (GPT, fdisk, gdisk)
   - File systems (XFS, ext4, VFAT creation and mounting)
   - LVM (physical volumes, volume groups, logical volumes, resizing)
   - Stratis storage configuration
   - /etc/fstab mounting with UUID and labels
   - **Exam Weight**: ~12-15%

3. **Networking** (Chapter 8)
   - nmcli configuration (IPv4, IPv6, DNS)
   - Hostname configuration and resolution
   - Network connection management
   - Interface persistence
   - firewalld integration
   - **Exam Weight**: ~10-12%

4. **Security (SELinux & Firewall)** (Chapter 22, 23)
   - SELinux modes (enforcing, permissive, disabled)
   - Contexts (user, role, type, level)
   - Port labeling with semanage
   - Boolean settings for services
   - File context restoration (restorecon)
   - firewalld zones, services, rich rules
   - Port management and persistence
   - **Exam Weight**: ~12-15%

5. **User & Group Management** (Chapter 6, 7)
   - User creation/modification/deletion
   - Password aging and policies
   - Group management and membership
   - sudo configuration and privileged access
   - Permissions (chmod, chown)
   - SUID, SGID, sticky bit
   - **Exam Weight**: ~8-10%

### Medium Priority (30-40% of exam)
Important skills that appear regularly:

1. **SSH Configuration** (Chapter 20)
   - sshd_config modifications (ports, users, keys)
   - Key-based authentication setup
   - User/group restrictions
   - SELinux port labeling for SSH
   - Firewall rules for SSH
   - **Exam Weight**: ~5-7%

2. **Software Management** (Chapter 9)
   - dnf package management
   - Repository configuration
   - RPM subscription (rhc in RHEL 10)
   - Application Streams (RPM direct vs modules)
   - Flatpak support
   - **Exam Weight**: ~5-7%

3. **Apache HTTP Server** (Chapter 21)
   - httpd installation and service management
   - Port configuration and SELinux contexts
   - Virtual hosts configuration
   - Firewall rules for HTTP/HTTPS
   - Document root permissions
   - **Exam Weight**: ~3-5%

4. **Process Management** (Chapter 10)
   - Identifying CPU/memory intensive processes
   - Process termination (signals)
   - Priority adjustment (nice, renice)
   - Tuning profiles (tuned-adm)
   - Top and monitoring tools
   - **Exam Weight**: ~3-5%

5. **Logging & Journaling** (Chapter 13)
   - journalctl usage and filtering
   - Persistent journal configuration
   - rsyslog configuration
   - Log file locations and rotation
   - **Exam Weight**: ~3-5%

6. **Text Processing & Scripting** (Chapter 2, 3, 4, 19)
   - grep, cut, sort, wc commands
   - Regular expressions
   - Shell scripting (variables, conditionals, loops)
   - sed and awk basics
   - **Exam Weight**: ~3-5%

7. **Network Storage** (Chapter 24)
   - NFS mounting (manual and fstab)
   - autofs configuration
   - Mount verification and troubleshooting
   - **Exam Weight**: ~2-4%

### Lower-Medium Priority (8-12% of exam)
Important but less frequently tested:

1. **Boot & Troubleshooting** (Chapter 17, 18)
   - GRUB2 configuration
   - Boot targets and parameters
   - Emergency/rescue modes
   - Password reset procedures
   - Filesystem repair (fsck)
   - **Exam Weight**: ~4-6%

2. **Time Services** (Chapter 25)
   - chrony NTP configuration
   - timedatectl timezone management
   - Hardware clock synchronization
   - **Exam Weight**: ~2-3%

3. **Kernel Management** (Chapter 16)
   - Kernel modules (lsmod, modinfo, modprobe)
   - udev and hardware initialization
   - Kernel upgrades
   - **Exam Weight**: ~1-2%

4. **Container Management** (Chapter 26)
   - Podman basic operations
   - Container image management
   - Buildah image building
   - systemd container services
   - **Exam Weight**: ~1-3%

5. **Essential Tools** (Chapter 2, 3, 5)
   - File management (ls, cp, mv, rm)
   - Links (hard and symbolic)
   - Archive/compression (tar, gzip, bzip2)
   - Remote access (SSH, SCP, rsync)
   - **Exam Weight**: ~2-3%

## Complete Exam Preparation Timeline

### Week 1-2: Foundation (Chapters 1-5)
- Linux basics and installation
- Essential tools and file management
- Command-line proficiency
- Text file creation and editing

### Week 3-4: User & Permissions (Chapters 6-7)
- User and group management
- File permissions and ownership
- Advanced permissions (SUID, SGID, sticky bit)

### Week 5-6: System Management (Chapters 11-13)
- systemd services and targets
- Systemd timers
- Logging and journaling
- Process management

### Week 7-8: Storage (Chapters 14-15)
- Partitioning and filesystems
- Mount configuration
- LVM configuration and resizing
- Stratis storage

### Week 9-10: Networking (Chapter 8, 20, 23-25)
- Network configuration with nmcli
- DNS and hostname
- SSH configuration
- Firewall configuration
- Time services

### Week 11-12: Security & Services (Chapters 21-22)
- SELinux deep dive
- Apache HTTP Server
- Port labeling
- Context management

### Week 13: Advanced Topics (Chapters 16-19, 26)
- Kernel management
- Boot troubleshooting
- Shell scripting
- Container basics

### Week 14-15: Practice & Review (All chapters)
- Practice exams
- Weak area review
- Lab exercises
- Command review

## Exam Tips & Strategies

### Time Management
- **Total Time**: 3 hours (180 minutes)
- **Task Count**: Typically 15-20 practical tasks
- **Per-Task Time**: 9-12 minutes per task (5 min for easy, 15 for complex)
- **Strategy**: 
  - Read all tasks first (2-3 minutes)
  - Start with easy tasks to build confidence
  - Save complex tasks for when you have full energy
  - Leave 10 minutes at end for verification and final check

### Persistence Requirement
All configurations must survive a system reboot. This means:
- Edit `/etc/fstab` (not just `mount`)
- Use `systemctl enable` (not just `start`)
- Add `--permanent` to firewall rules
- Use `setsebool -P` (not just `setsebool`)
- Add `-r` or rebuild initramfs for kernel module changes
- Use `/etc/sudoers.d/` for sudo rules
- Create systemd service files in `/etc/systemd/system/`

### Command Accuracy
- **Case Sensitive**: Command names and file paths are case-sensitive
- **Typos Fail**: One typo can fail entire task
- **Spacing Matters**: `/etc/fstab` format must be exact
- **No Spaces in Options**: Use `key1=value1,key2=value2` (no spaces)
- **Verification**: Always verify command succeeded before moving on

### Verification Checklist
After each task, verify:
1. **Service Status**: `systemctl status <service>`
2. **Listening Ports**: `ss -tln` or `netstat -tln`
3. **File Ownership**: `ls -la` for correct user/group
4. **Firewall Rules**: `firewall-cmd --list-all`
5. **SELinux Context**: `ls -Z` for correct context
6. **Mount Points**: `df -h` or `mount`
7. **Logs**: `journalctl` for errors
8. **DNS Resolution**: `getent hosts` for name resolution

### Common Mistakes (Study These!)

| Mistake | Impact | Fix |
|---------|--------|-----|
| Forgetting `systemctl daemon-reload` | Services don't start | Always reload after creating service files |
| Not making `/etc/fstab` changes | Mounts lost on reboot | Test with `mount -a` before reboot |
| Incorrect SSH key permissions (not 0600) | Cannot login with key | `chmod 600 ~/.ssh/id_*` |
| Wrong SELinux context on files | Services cannot access files | `restorecon -Rv /path` |
| Firewall blocking required ports | Services unreachable | Add `--permanent` and `--reload` |
| DNS not configured | Cannot reach services by name | Configure with nmcli or /etc/hosts |
| Wrong umask or permissions | Security issues or access denied | Verify with `stat` or `ls -la` |
| Not disabling NetworkManager before ifcfg | Network config conflicts | Use nmcli for all changes |
| Forgetting `--permanent` flag | Changes lost on reload | Always use `--permanent` for persistence |
| Wrong user/group ownership | Services run with wrong privileges | Verify ownership matches service user |

### Tools and Resources Available on Exam

**Always Available**:
- Man pages: `man <command>`
- Help flags: `--help`
- `/usr/share/doc/` documentation
- System utilities (grep, sed, awk, etc.)
- Text editors (vim, nano)
- All installed packages

**Should You Know**:
- Command syntax and options
- File locations and names
- Configuration file formats
- Service dependencies
- SELinux context types
- Firewall zones and services
- LVM commands

### Study Techniques

1. **Hands-on Practice**: Do NOT just read; practice every command
2. **Repetition**: Do each lab exercise 2-3 times until comfortable
3. **Speed Building**: Time yourself and try to improve
4. **Error Analysis**: Understand why commands fail
5. **Scenario Practice**: Create realistic scenarios
6. **Peer Testing**: Have someone verify your configuration
7. **Lab Environment**: Set up virtual machines for practice
8. **Cheat Sheets**: Create personal reference materials

### Pre-Exam Preparation (48 hours before)

**Do This**:
- Review weak areas one final time
- Practice one complete mock exam
- Get good sleep night before
- Eat well on exam day
- Arrive early to exam center

**Don't Do This**:
- Cram new material 24 hours before
- Stay up late studying
- Skip lunch or eat poorly
- Stress about specific topics
- Compare yourself to other candidates

---

# RHEL 9 to RHEL 10 Migration & Compatibility

## Critical Changes Requiring Attention

### 1. Architecture & CPU Requirements
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **CPU Baseline** | x86-64-v2 (2008+) | **x86-64-v3 (2015+)** |
| **Compatibility** | Older systems OK | Systems pre-2015 may not boot |
| **Check Before Upgrade** | Not needed | `grep flags /proc/cpuinfo` |

**Action**: Verify CPU supports AVX2 and other v3 features before upgrading old systems

### 2. Network Interface Naming
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Legacy eth0 Support** | Supported via `net.ifnames=0` | **Fully deprecated** |
| **Default Naming** | Predictable (ens0, enp3s0, etc.) | **Enforced modern names** |
| **Configuration Files** | `/etc/sysconfig/network-scripts/` | `/etc/NetworkManager/system-connections/` |
| **Tool** | nmcli, nmtui | Same (nmcli, nmtui) |

**Action**: Remove any `net.ifnames=0` boot parameters; scripts referencing eth0 will fail

### 3. Subscription Management
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Primary Method** | subscription-manager | **Red Hat Connect (rhc)** |
| **Legacy Support** | Standard | Still supported (deprecated) |
| **Integration** | Separate | Hybrid Cloud Console integration |
| **Automation** | subscription-manager register | `rhc connect` |

**Action**: Update automation scripts; learn `rhc` commands for new systems

### 4. Application Streams & Package Management
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Modularity** | `dnf modules` (RPM modularity) | **Removed** |
| **Python Versions** | 3.9, 3.11 (via modules) | **3.12 (direct RPM)** |
| **Perl Version** | 5.32 | **5.40** |
| **Ruby Version** | 3.0, 3.1 | **3.3** |
| **Go Version** | 1.18, 1.19 | **1.23** |
| **GCC** | 11.x, 12.x | **14.2** |
| **Alternative Method** | N/A | **Flatpak for GUI apps** |

**Action**: Review application dependencies; use Flatpak for unsupported GUI applications

### 5. SSH Security
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **OpenSSH Version** | 8.x | **9.9** |
| **Key Type Default** | RSA | **ED25519** |
| **Key Permissions Check** | Standard | **Stricter validation** |
| **sshd-keysign** | Standard | **SUID-based handling** |
| **Deprecated Algorithms** | Some | **More removed** |

**Action**: Generate ED25519 keys; ensure 0600 permissions on private keys; test SSH connectivity after upgrade

### 6. Kernel and Core
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Kernel Version** | 5.14.x | **6.12.0** |
| **Hardware Support** | Good | **Enhanced (new CPUs, devices)** |
| **Performance** | Good | **Improved scheduling, memory management** |
| **Security** | Standard | **Post-quantum cryptography prep** |

**Action**: Test kernel compatibility with custom drivers; regenerate initramfs if needed

### 7. Removed and Deprecated Features
| Feature | RHEL 9 | RHEL 10 |
|--------|--------|---------|
| **Teamd Networking** | Supported | **Removed** |
| **ifcfg Network Config** | Default | **Deprecated (use nmcli)** |
| **SysV Init Scripts** | Supported | **Removed** |
| **Cgroup v1** | Supported | **Removed (v2 only)** |
| **Firefox Package** | Available | **Only via Flatpak** |
| **PostScript Support** | Available | **Reduced** |
| **Legacy Console Drivers** | Some | **Removed** |

**Action**: Update any teamd, ifcfg, or SysV init usage; migrate to nmcli and systemd

### 8. DNS Security Enhancement
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Standard DNS** | Unencrypted | Still available |
| **DNS-over-HTTPS** | No | **Yes** |
| **DNS-over-TLS** | No | **Yes** |
| **DNSSEC Support** | Standard | Same |

**Action**: No action needed; DNS queries work as before; encrypted options available

### 9. Container & Podman
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **Podman Version** | 4.x | **5.x** |
| **Buildah Integration** | Good | **Enhanced** |
| **Rootless Support** | Yes | **Improved** |
| **Systemd Integration** | Standard | **Better** |
| **Performance** | Good | **Improved** |

**Action**: Test container workloads; ensure container images compatible; rebuild if needed

### 10. Firewall Backend
| Feature | RHEL 9 | RHEL 10 |
|---------|--------|---------|
| **nftables Version** | 1.0.x | **1.1.1** |
| **Backend Changes** | Minor | **Enhanced** |
| **Rule Syntax** | Same | Compatible |
| **Performance** | Good | **Better** |
| **DDoS Protection** | Basic | **Enhanced** |

**Action**: No immediate action; firewall rules compatible; test complex rule sets

## Complete Compatibility Checklist

- [ ] **CPU Check**: Verify x86-64-v3 support
  - `grep flags /proc/cpuinfo` should show `avx2`, `lzcnt`, `abm`, `prefetchw`
  - Old systems (pre-2015) may need hardware replacement

- [ ] **Network Configuration**:
  - [ ] Update scripts removing `net.ifnames=0` references
  - [ ] Test nmcli configuration (should work unchanged)
  - [ ] Verify DNS resolution after upgrade
  - [ ] Test all network services connectivity

- [ ] **SSH Configuration**:
  - [ ] Generate ED25519 keys for new systems
  - [ ] Test key-based authentication
  - [ ] Verify sshd_config syntax: `sshd -t`
  - [ ] Check SSH firewall rules

- [ ] **Application Streams**:
  - [ ] Document application versions in RHEL 9
  - [ ] Check availability in RHEL 10
  - [ ] Test Python, Ruby, Go, GCC compatibility
  - [ ] Plan Flatpak migration if needed

- [ ] **Subscription & Licensing**:
  - [ ] Learn `rhc` commands
  - [ ] Test subscription management
  - [ ] Verify entitlements transfer to RHEL 10
  - [ ] Update automation/scripts

- [ ] **Storage & Filesystems**:
  - [ ] Review LVM configurations
  - [ ] Test Stratis compatibility
  - [ ] Verify /etc/fstab UUIDs/labels still valid
  - [ ] Check XFS and ext4 performance

- [ ] **Systemd & Services**:
  - [ ] Test service starts and stops
  - [ ] Verify unit file compatibility
  - [ ] Check systemd-journald (persistent journal)
  - [ ] Verify cgroup v2 compatibility

- [ ] **SELinux**:
  - [ ] Verify SELinux policies compatible
  - [ ] Test booleans still work
  - [ ] Check custom policy modules
  - [ ] Test file context restoration

- [ ] **Firewall & Networking**:
  - [ ] Export firewall rules: `firewall-cmd --list-all`
  - [ ] Re-apply after upgrade
  - [ ] Test nftables integration
  - [ ] Verify port labeling

- [ ] **Kernel Modules**:
  - [ ] List loaded modules: `lsmod`
  - [ ] Check for deprecated modules
  - [ ] Rebuild custom modules if needed
  - [ ] Test hardware detection

- [ ] **Container Workloads**:
  - [ ] Test Podman container starts
  - [ ] Verify image compatibility
  - [ ] Test systemd container services
  - [ ] Check storage backend

- [ ] **Backups & Recovery**:
  - [ ] Back up critical configurations
  - [ ] Create recovery media
  - [ ] Document custom settings
  - [ ] Test backup restoration

## Migration Best Practices

### Pre-Upgrade
1. Create system snapshot/backup
2. Document current configuration
3. List installed packages
4. Back up /etc, /home, custom applications
5. Test in non-production first

### Upgrade Process
1. Ensure sufficient disk space (~5GB)
2. Read RHEL 10 release notes
3. Consider phased approach (test system first)
4. Back up bootloader/GRUB
5. Plan for downtime

### Post-Upgrade Verification
1. Reboot system successfully
2. Verify all services start
3. Test all critical applications
4. Confirm network connectivity
5. Check logs for errors
6. Verify backups still accessible
7. Confirm DNS resolution
8. Test firewall rules

### Rollback Plan (If Needed)
1. Keep RHEL 9 backup media
2. Document rollback procedure
3. Have recovery console access
4. Test rollback in non-production first
5. Plan for service impact

---

# Quick Reference: RHEL 10 Changes Summary

**Remember These Changes**:
- ✅ Use x86-64-v3 CPUs only
- ✅ Use modern network names (ens0, not eth0)
- ✅ Use `rhc` for subscription management
- ✅ Use ED25519 SSH keys
- ✅ Use nmcli for networking
- ✅ Use DNF (not yum or rpm directly)
- ✅ Use Podman 5.x for containers
- ✅ Use systemd for everything (no SysV init)
- ✅ No dnf modules (use direct RPM or Flatpak)
- ✅ Firefox available only via Flatpak

---

**End of Comprehensive Exam Preparation Strategy**
