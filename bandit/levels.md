# Bandit — Level Writeups

---

## Level 0 → 1

**Goal:** Log in via SSH and read the `readme` file.

**Connect:**
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
# password: bandit0
```

**Solution:**
```bash
ls
cat readme
```

**What I learned:**
- How to connect to a remote machine via SSH
- Basic `ls` and `cat` commands

**Password:** `[REDACTED]`

---

## Level 1 → 2

**Goal:** Read a file named `-` (a dash).

**Connect:**
```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

Running `cat -` doesn't work because `-` is interpreted by bash as stdin, not a filename.

```bash
cat -      # ❌ waits for keyboard input — wrong
```

**Solution:**

Prefix the filename with `./` to tell bash it's a file path:

```bash
cat ./-    # ✅
```

**What I learned:**
- Files starting with `-` confuse shell commands because `-` signals a flag
- Using `./` forces bash to treat it as a relative file path

**Password:** `[REDACTED]`

---

## Level 2 → 3

**Goal:** Read a file called `--spaces in this filename--`.

**Connect:**
```bash
ssh bandit2@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

Spaces in filenames break shell commands — bash treats each word as a separate argument.

```bash
cat --spaces in this filename--   # ❌ broken
```

**Solution:**

Either escape the spaces with `\` or wrap the name in quotes:

```bash
cat ./--spaces\ in\ this\ filename--    # ✅ escape spaces
cat "./--spaces in this filename--"     # ✅ quotes
```

**What I learned:**
- Spaces in filenames must be escaped or quoted
- Tab completion (`Tab` key) handles this automatically

**Password:** `[REDACTED]`

---

## Level 3 → 4

**Goal:** Find a hidden file inside the `inhere` directory.

**Connect:**
```bash
ssh bandit3@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

`ls` shows nothing in `inhere/` — the file is hidden.

**Solution:**

Hidden files in Linux start with `.` — use `ls -la` to see them:

```bash
cd inhere/
ls -la
cat ...Hiding-From-You
```

**What I learned:**
- Files starting with `.` are hidden from regular `ls`
- `ls -la` shows all files including hidden ones
- `-l` = long format, `-a` = all files

**Password:** `[REDACTED]`

---

## Level 4 → 5

**Goal:** Find the only human-readable file in `inhere/` among 10 files named `-file00` to `-file09`.

**Connect:**
```bash
ssh bandit4@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

Most files contain binary garbage. We need to find the one with ASCII text.

**Solution:**

Use the `file` command to check the type of each file:

```bash
cd inhere/
file ./-file*
```

Output showed `-file07` as **ASCII text** while all others were binary `data`.

```bash
cat ./-file07
```

**What I learned:**
- `file` command identifies the type of a file by reading its magic bytes
- Not all files contain readable text — binary files look like garbage
- Globbing with `*` applies a command to multiple files at once

**Password:** `[REDACTED]`

---

## Level 5 → 6

**Goal:** Find a file in `inhere/` that is human-readable, exactly 1033 bytes, and not executable.

**Connect:**
```bash
ssh bandit5@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

There are 20 subdirectories each with multiple files — manual searching isn't practical.

**Solution:**

Use `find` with specific flags matching the description:

```bash
find inhere -type f -size 1033c ! -executable
```

| Flag | Meaning |
|------|---------|
| `-type f` | files only |
| `-size 1033c` | exactly 1033 bytes (`c` = bytes) |
| `! -executable` | not executable |

Result: `inhere/maybehere07/.file2`

```bash
cat inhere/maybehere07/.file2
```

**What I learned:**
- `find` is extremely powerful for locating files by properties
- `!` negates a condition in `find`
- File sizes can be specified in bytes (`c`), kilobytes (`k`), megabytes (`M`)

**Password:** `[REDACTED]`

---

## Level 6 → 7

**Goal:** Find a file anywhere on the server owned by user `bandit7`, group `bandit6`, and exactly 33 bytes.

**Connect:**
```bash
ssh bandit6@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

The file could be anywhere on the entire filesystem — not just home directory.

**Solution:**

Search from root `/` with ownership filters, redirect errors to `/dev/null`:

```bash
find / -type f -size 33c -user bandit7 -group bandit6 2>/dev/null
```

| Flag | Meaning |
|------|---------|
| `/` | search entire filesystem |
| `-user bandit7` | owned by bandit7 |
| `-group bandit6` | group is bandit6 |
| `2>/dev/null` | hide permission denied errors |

Result: `/var/lib/dpkg/info/bandit7.password`

```bash
cat /var/lib/dpkg/info/bandit7.password
```

**What I learned:**
- `2>/dev/null` suppresses stderr — essential when searching filesystem as non-root
- Files can be found by owner and group using `-user` and `-group`
- Passwords and sensitive files are often hidden in non-obvious system directories

**Password:** `[REDACTED]`

---

## Level 7 → 8

**Goal:** Find the password next to the word `millionth` in `data.txt`.

**Connect:**
```bash
ssh bandit7@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

`data.txt` is huge — thousands of lines. Can't read it manually.

**Solution:**

Use `grep` to find the line containing `millionth`:

```bash
grep millionth data.txt
```

**What I learned:**
- `grep` searches for patterns inside files
- Piping `cat` into `grep` is equivalent to `grep pattern file`
- For large files, always use search tools — never read manually

**Password:** `[REDACTED]`

---

## Level 8 → 9

**Goal:** Find the only line that appears exactly once in `data.txt`.

**Connect:**
```bash
ssh bandit8@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

`data.txt` has thousands of duplicate lines — the password appears only once.

**Solution:**

Sort the file first (so duplicates are adjacent), then use `uniq -u` to show only unique lines:

```bash
sort data.txt | uniq -u
```

| Command | Meaning |
|---------|---------|
| `sort` | sorts lines alphabetically |
| `uniq -u` | shows only lines that appear exactly once |

**What I learned:**
- `uniq` only works on **adjacent** duplicates — always `sort` first
- `-u` flag shows unique lines, `-d` shows duplicates, `-c` counts occurrences
- Piping commands together is a core Linux skill

**Password:** `[REDACTED]`

---

## Level 9 → 10

**Goal:** Find the password in `data.txt` — a binary file — preceded by several `=` characters.

**Connect:**
```bash
ssh bandit9@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

`data.txt` is mostly binary data. `cat` produces garbage. We need the human-readable strings.

**Solution:**

Use `strings` to extract readable text, then `grep` for `=`:

```bash
strings data.txt | grep '==='
```

**What I learned:**
- `strings` extracts printable ASCII sequences from binary files
- Binary files can contain hidden readable content
- Combining `strings` and `grep` is a common forensics technique

**Password:** `[REDACTED]`

---

## Level 10 → 11

**Goal:** Decode a base64 encoded file to find the password.

**Connect:**
```bash
ssh bandit10@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

`data.txt` contains base64 encoded data — looks like gibberish when read directly.

**Solution:**

Pipe the file through `base64 -d` to decode it:

```bash
cat data.txt | base64 -d
```

**What I learned:**
- Base64 is an encoding scheme — not encryption. It's easily reversible.
- `base64 -d` decodes, `base64` (no flag) encodes
- Base64 is commonly used to transfer binary data over text-based protocols

**Password:** `[REDACTED]`

---

## Level 11 → 12

**Goal:** Decode a ROT13 encoded file.

**Connect:**
```bash
ssh bandit11@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

All letters in `data.txt` have been rotated by 13 positions — this is ROT13.

**Solution:**

Use `tr` to translate characters — rotate A-Z by 13 and a-z by 13:

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

**Breaking down the `tr` command:**

| Part | Meaning |
|------|---------|
| `A-Za-z` | input: all uppercase and lowercase letters |
| `N-ZA-Mn-za-m` | output: shift each by 13 positions |

ROT13 is self-reversing — applying it twice gives back the original text.

**What I learned:**
- `tr` translates characters one-to-one
- ROT13 is a simple substitution cipher — shift every letter by 13
- Since the alphabet has 26 letters, ROT13 applied twice = original text

**Password:** `[REDACTED]`

---

## Level 12 → 13

**Goal:** Recover the password from `data.txt` — a hexdump of a file that has been repeatedly compressed.

**Connect:**
```bash
ssh bandit12@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

`data.txt` is a hexdump. Reversing it gives a binary file that has been compressed multiple times using different formats — gzip, bzip2, and tar — nested inside each other.

**Solution:**

Since the home directory is read-only, first create a working directory in `/tmp`:

```bash
mkdir /tmp/b12
cd /tmp/b12
cp ~/data.txt .
```

Convert the hexdump back to binary:

```bash
xxd -r data.txt > data.bin
```

Then repeatedly check the file type and decompress accordingly:

```bash
file data.bin          # gzip
mv data.bin data.gz && gunzip data.gz

file data              # bzip2
mv data data.bz2 && bunzip2 data.bz2

file data              # gzip again
mv data data.gz && gunzip data.gz

file data              # tar
mv data data.tar && tar -xvf data.tar      # extracts data5.bin

file data5.bin         # tar again
mv data5.bin data.tar && tar -xf data.tar  # extracts data6.bin

file data6.bin         # bzip2
mv data6.bin data.bz2 && bunzip2 data.bz2

file data              # tar again
mv data data.tar && tar -xf data.tar       # extracts data8.bin

file data8.bin         # gzip
mv data8.bin data.gz && gunzip data.gz

file data              # ASCII text ✅
cat data
```

**Decompression Chain:**

```
hexdump
  → gzip
    → bzip2
      → gzip
        → tar
          → tar
            → bzip2
              → tar
                → gzip
                  → ASCII text ✅
```

**What I learned:**
- Always check file type with `file` before trying to open — extension doesn't matter
- `/tmp` is the writable scratch space on shared systems
- `xxd -r` reverses a hexdump back to binary
- Files can be compressed multiple times with different formats

**Password:** `[REDACTED]`

---

## Level 13 → 14

**Goal:** Use an SSH private key to log into the next level.

**Connect:**
```bash
ssh bandit13@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

Instead of a password, we receive `sshkey.private` — an RSA private key for bandit14.

**Solution:**

Copy the key and set correct permissions:

```bash
cat sshkey.private > key.pem
chmod 600 key.pem
```

SSH using the key:

```bash
ssh -i key.pem bandit14@bandit.labs.overthewire.org -p 2220
```

Then read the password file:

```bash
cat /etc/bandit_pass/bandit14
```

**What I learned:**
- SSH uses public-key cryptography for passwordless authentication
- Private keys must have permissions `600` (owner only) — SSH rejects if world-readable
- `-i` flag specifies the identity/private key file
- RSA is an asymmetric encryption algorithm

**Password:** `[REDACTED]`

---

## Level 14 → 15

**Goal:** Submit the bandit14 password to localhost port 30000 to get the bandit15 password.

**Connect:**
```bash
ssh bandit14@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

Port 30000 is running a service that expects the current level's password and returns the next level's password.

**Solution:**

Get the current password:

```bash
cat /etc/bandit_pass/bandit14
```

Connect to localhost:30000 using netcat:

```bash
nc localhost 30000
[paste password here]
```

The server responds with `Correct!` and returns the next level's password.

**What I learned:**
- `nc` (netcat) is the "TCP/IP swiss army knife"
- Used for reading/writing to network sockets
- Port 30000 runs a simple echo service that validates passwords
- Many wargame levels use local services on high-numbered ports

**Password:** `[REDACTED]`

---

## Level 15 → 16

**Goal:** Submit the bandit15 password to port 30001 using SSL/TLS encryption.

**Connect:**
```bash
ssh bandit15@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

Port 30001 requires SSL/TLS — `nc` won't work, need `openssl s_client`.

**Solution:**

Get current password:

```bash
cat /etc/bandit_pass/bandit15
```

Connect with SSL/TLS:

```bash
openssl s_client -connect localhost:30001
[paste password here]
```

Server responds with `Correct!` and the next level's password.

**What I learned:**
- `openssl s_client` creates an SSL/TLS client connection
- Self-signed certificates produce warnings but still work
- Verify certificate details even if warnings appear (check CN, validity dates)
- TLS encrypts the connection (password not visible in plaintext)

**Password:** `[REDACTED]`

---

## Level 16 → 17

**Goal:** Find which port in range 31000-32000 speaks SSL/TLS and returns the SSH private key for bandit17.

**Connect:**
```bash
ssh bandit16@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

The description says there are multiple ports listening, but only one returns credentials. The others echo back what you send.

**Solution:**

Use nmap to scan and identify services:

```bash
nmap -sCV -p 31000-32000 localhost
```

Output shows port 31790 has SSL/TLS with service detection. Test it:

```bash
openssl s_client -connect localhost:31790 -quiet
[paste password here]
```

The server will request the password and return the SSH private key.

Save the private key:

```bash
chmod 600 key17.pem
ssh -i key17.pem bandit17@bandit.labs.overthewire.org -p 2220
```

**What I learned:**
- `nmap -sCV` performs service version detection
- Multiple ports run echo services (plain and SSL variants)
- Only port 31790 with SSL processes credentials correctly
- Port scanning reveals service types and versions quickly

**Password:** `[REDACTED]`

---

## Level 17 → 18

**Goal:** Find the only line that has changed between `passwords.old` and `passwords.new`.

**Connect:**
```bash
chmod 600 key17.pem
ssh -i key17.pem bandit17@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

Two files with 100+ lines each. Can't manually compare.

**Solution:**

Use `diff` to show differences:

```bash
diff passwords.old passwords.new
```

Output:
```
42c42
< KxOU4IzbXM8j8HeAWPAXTd1eC77mp1qV
---
> [REDACTED]
```

The new password is the line marked with `>`.

**What I learned:**
- `diff` compares two files line-by-line
- Output shows additions (`>`), deletions (`<`), and changes (`c`)
- Useful for version control and file comparison
- Can use `diff -u` for unified format (shows context)

**Password:** `[REDACTED]`

---

## Level 18 → 19

**Goal:** Read the `readme` file when `.bashrc` logs you out immediately upon SSH login.

**Connect:**
```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

Logging in normally results in instant logout due to modified `.bashrc`.

**Solution:**

Execute a command directly via SSH without spawning an interactive shell:

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat ~/readme"
```

This executes the command before `.bashrc` logout happens and returns the password.

**What I learned:**
- SSH can execute single commands without interactive shell: `ssh user@host "command"`
- `.bashrc` is executed for non-interactive shells, but some operations bypass it
- This demonstrates why security relies on multiple layers, not just `.bashrc`

**Password:** `[REDACTED]`

---

## Level 19 → 20

**Goal:** Use a SETUID binary to execute a command as another user and read the next level's password.

**Connect:**
```bash
ssh bandit19@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

A setuid binary `bandit20-do` allows executing commands as `bandit20` user.

**Solution:**

Examine the binary:

```bash
ls -la
# -rwsr-x---   1 bandit20 bandit19 14888 Apr  3 15:17 bandit20-do
```

The `s` in permissions indicates setuid — when executed, runs as the owner (`bandit20`).

Run a command with elevated privileges:

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

**What I learned:**
- SETUID (Set User ID) bit allows a binary to run with the owner's permissions
- Permission format: `s` in owner execute position = setuid
- `chmod u+s <file>` adds setuid bit
- Important security concern — setuid binaries can be exploited if they have vulnerabilities

**Password:** `[REDACTED]`

---

## Level 20 → 21

**Goal:** Connect to a listening port using a setuid binary and establish a two-way connection to exchange passwords.

**Connect:**
```bash
ssh bandit20@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

A setuid binary `suconnect` takes a port number and connects to localhost, expecting the bandit20 password. If correct, it sends the bandit21 password back.

**Solution:**

Open two terminal sessions:

Terminal 1 - Start a netcat listener:

```bash
nc -lnvp 4444
```

Terminal 2 - Run the suconnect binary:

```bash
./suconnect 4444
```

The binary will connect to your listener on port 4444. When it connects, netcat will display the connection. Send the current password:

```bash
[send: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO]
```

The binary verifies the password and sends back the next level's password.

**What I learned:**
- Setuid binaries can enforce authentication through network connections
- `nc -lnvp` creates a listening server on a specified port
- Multi-terminal workflows are useful for testing network services
- The binary validates the password and transmits the next one

**Password:** `[REDACTED]`

---

## Level 21 → 22

**Goal:** Find the password by examining cron jobs in `/etc/cron.d/`.

**Connect:**
```bash
ssh bandit21@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

A cron job runs automatically for bandit22. Examine what it does to find the password.

**Solution:**

List cron jobs:

```bash
cat /etc/cron.d/cronjob_bandit22
```

Output shows:

```bash
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

This runs `/usr/bin/cronjob_bandit22.sh` as bandit22 every minute. Check the script:

```bash
cat /usr/bin/cronjob_bandit22.sh
```

Output:

```bash
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

The script writes bandit22's password to a temporary file. Read it:

```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

**What I learned:**
- Cron jobs are scheduled tasks that run automatically
- `/etc/cron.d/` contains system-wide cron jobs
- Scripts executed by cron run with the specified user's privileges
- Temporary files created by cron jobs are often world-readable
- Check what scripts do before running them

**Password:** `[REDACTED]`

---

## Level 22 → 23

**Goal:** Find the password by analyzing a cron job that uses md5sum to generate a filename.

**Connect:**
```bash
ssh bandit22@bandit.labs.overthewire.org -p 2220
```

**The Problem:**

A cron job for bandit23 generates a filename using md5sum of a string. We need to calculate what that filename is.

**Solution:**

Check the cron job:

```bash
cat /etc/cron.d/cronjob_bandit23
```

Output:

```bash
@reboot bandit23 /usr/bin/cronjob_bandit23.sh &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh &> /dev/null
```

Read the script:

```bash
cat /usr/bin/cronjob_bandit23.sh
```

Output:

```bash
#!/bin/bash
myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

The script:
1. Gets the current user (bandit23)
2. Creates an md5sum of "I am user bandit23"
3. Writes the password to `/tmp/[md5sum]`

Calculate the md5sum:

```bash
echo 'I am user bandit23' | md5sum | cut -d ' ' -f 1
```

Output: `8ca319486bfbbc3663ea0fbe81326349`

Read the password file:

```bash
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

**What I learned:**
- Cron jobs can be analyzed to predict their behavior
- md5sum creates a consistent hash from a string
- Understanding the logic allows us to calculate the output filename
- The script assumes the temporary directory is secure, but it's readable

**Password:** `[REDACTED]`

---