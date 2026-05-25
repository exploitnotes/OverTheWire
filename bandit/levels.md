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
cat -      #  waits for keyboard input — wrong
```

**Solution:**

Prefix the filename with `./` to tell bash it's a file path:

```bash
cat ./-    
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
cat --spaces in this filename--   #  broken
```

**Solution:**

Either escape the spaces with `\` or wrap the name in quotes:

```bash
cat ./--spaces\ in\ this\ filename--    #  escape spaces
cat "./--spaces in this filename--"     #  quotes
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
cat data.txt | grep millionth
```

Or more directly:
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

*---*

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
mkdir /tmp/b11
cd /tmp/b11
cp ~/data.txt .
```

Convert the hexdump back to binary:

```bash
xxd -r data.txt > data.bin
```

Then repeatedly check the file type and decompress accordingly:

```bash
file data.bin          # gzip
mv data.bin data.gz
gunzip data.gz

file data              # bzip2
mv data data.bz2
bunzip2 data.bz2

file data              # gzip again
mv data data.gz
gunzip data.gz

file data              # tar
mv data data.tar
tar -xvf data.tar      # extracts data5.bin

file data5.bin         # tar again
mv data5.bin data.tar
tar -xvf data.tar      # extracts data6.bin

file data6.bin         # bzip2
mv data6.bin data.bz2
bunzip2 data.bz2

file data              # tar again
mv data data.tar
tar -xf data.tar       # extracts data8.bin

file data8.bin         # gzip
mv data8.bin data.gz
gunzip data.gz

file data              # ASCII text 
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
                  → ASCII text 
```

**Commands Used:**

| Command | Purpose |
|---------|---------|
| `xxd -r` | reverse hexdump → binary |
| `file` | identify file type |
| `gunzip` | decompress gzip |
| `bunzip2` | decompress bzip2 |
| `tar -xvf` | extract tar archive |

**What I learned:**
- Always check file type with `file` before trying to open — extension doesn't matter
- `/tmp` is the writable scratch space on shared systems
- `xxd -r` reverses a hexdump back to binary
- Files can be compressed multiple times with different formats

**Password:** `[REDACTED]`

---

*More levels coming soon...*