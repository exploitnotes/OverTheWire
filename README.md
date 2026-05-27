# Bandit — Level Writeups

---

## Level 0 → 1

Log in via SSH and read the readme file.

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
# password: bandit0

ls
cat readme
```

Pretty straightforward. Password is in the readme.

**Password:** `[REDACTED]`

---

## Level 1 → 2

Read a file named `-` (a dash).

```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

The file is literally called `-`. If you try `cat -` it just waits for input (stdin). The dash is a special character in bash. Solution is to use `./` to force it to treat it as a filename:

```bash
cat ./-
```

**Password:** `[REDACTED]`

---

## Level 2 → 3

Read a file called `--spaces in this filename--`.

```bash
ssh bandit2@bandit.labs.overthewire.org -p 2220
```

Spaces break the command — bash treats each word separately. Tried `cat --spaces in this filename--` but it just breaks. Escape the spaces or quote it:

```bash
cat ./--spaces\ in\ this\ filename--
# or
cat "./--spaces in this filename--"
```

Tab completion handles this automatically, which is nice.

**Password:** `[REDACTED]`

---

## Level 3 → 4

Find a hidden file in the `inhere` directory.

```bash
ssh bandit3@bandit.labs.overthewire.org -p 2220

cd inhere/
ls
```

Nothing shows up. Files starting with `.` are hidden by default. Use `-a` flag:

```bash
ls -la
cat ...Hiding-From-You
```

**Password:** `[REDACTED]`

---

## Level 4 → 5

Find the human-readable file among 10 binary files.

```bash
ssh bandit4@bandit.labs.overthewire.org -p 2220

cd inhere/
ls
```

There are 10 files: `-file00` through `-file09`. Most are binary gibberish. Use `file` command to check each:

```bash
file ./-file*
```

Shows `-file07` is ASCII text while the rest are binary data. That's the one:

```bash
cat ./-file07
```

**Password:** `[REDACTED]`

---

## Level 5 → 6

Find a file that is exactly 1033 bytes and not executable.

```bash
ssh bandit5@bandit.labs.overthewire.org -p 2220

ls -la
```

20 subdirectories with multiple files each. Manual searching would take forever. `find` command with specific filters:

```bash
find inhere -type f -size 1033c ! -executable
```

Gets us directly to `inhere/maybehere07/.file2`:

```bash
cat inhere/maybehere07/.file2
```

**Password:** `[REDACTED]`

---

## Level 6 → 7

Find a file owned by bandit7, group bandit6, exactly 33 bytes.

```bash
ssh bandit6@bandit.labs.overthewire.org -p 2220
```

The file could be anywhere on the system. Start from root:

```bash
find / -type f -size 33c -user bandit7 -group bandit6 2>/dev/null
```

The `2>/dev/null` suppresses permission denied errors. Finds `/var/lib/dpkg/info/bandit7.password`:

```bash
cat /var/lib/dpkg/info/bandit7.password
```

**Password:** `[REDACTED]`

---

## Level 7 → 8

Find the password next to the word `millionth`.

```bash
ssh bandit7@bandit.labs.overthewire.org -p 2220

cat data.txt
```

Huge file with thousands of lines. Can't read it manually. Just grep for the keyword:

```bash
grep millionth data.txt
```

**Password:** `[REDACTED]`

---

## Level 8 → 9

Find the only line that appears exactly once.

```bash
ssh bandit8@bandit.labs.overthewire.org -p 2220

cat data.txt | sort | uniq -c
```

Lots of duplicates. Most lines appear multiple times. Need to find the one that appears only once. `uniq` removes adjacent duplicates, so sort first:

```bash
sort data.txt | uniq -u
```

The `-u` flag shows only lines appearing exactly once.

**Password:** `[REDACTED]`

---

## Level 9 → 10

Find the password in a binary file, preceded by `=` characters.

```bash
ssh bandit9@bandit.labs.overthewire.org -p 2220

cat data.txt
```

Mostly binary garbage. Tried to read it directly but nothing useful. Use `strings` to extract readable text:

```bash
strings data.txt | grep '==='
```

**Password:** `[REDACTED]`

---

## Level 10 → 11

Decode a base64 encoded file.

```bash
ssh bandit10@bandit.labs.overthewire.org -p 2220

cat data.txt
```

Looks like base64 (random alphanumeric with `+` and `/`). Decode it:

```bash
cat data.txt | base64 -d
```

**Password:** `[REDACTED]`

---

## Level 11 → 12

Decode a ROT13 encoded file.

```bash
ssh bandit11@bandit.labs.overthewire.org -p 2220

cat data.txt
```

Looks like gibberish but it's ROT13 (every letter shifted by 13). Use `tr` to translate:

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

This shifts A→N, B→O, etc. Works for both upper and lowercase.

**Password:** `[REDACTED]`

---

## Level 12 → 13

Recover a password from a hexdump of a repeatedly compressed file.

```bash
ssh bandit12@bandit.labs.overthewire.org -p 2220

cat data.txt
```

It's a hexdump. Home directory is read-only, so create a temp workspace:

```bash
mkdir /tmp/work
cd /tmp/work
cp ~/data.txt .
xxd -r data.txt > data.bin
```

Now repeatedly check file type and decompress:

```bash
file data.bin          # gzip
mv data.bin data.gz && gunzip data.gz
file data              # bzip2
mv data data.bz2 && bunzip2 data.bz2
file data              # gzip again
mv data data.gz && gunzip data.gz
file data              # tar
mv data data.tar && tar -xf data.tar
file data5.bin         # tar again
mv data5.bin data.tar && tar -xf data.tar
file data6.bin         # bzip2
mv data6.bin data.bz2 && bunzip2 data.bz2
file data              # tar again
mv data data.tar && tar -xf data.tar
file data8.bin         # gzip
mv data8.bin data.gz && gunzip data.gz
file data              # finally text!
cat data
```

**Password:** `[REDACTED]`

---

## Level 13 → 14

Use an SSH private key to log in.

```bash
ssh bandit13@bandit.labs.overthewire.org -p 2220

ls -la
cat sshkey.private
```

Instead of a password, we get a private key. Save it and use it to SSH:

```bash
cat sshkey.private > key.pem
chmod 600 key.pem
ssh -i key.pem bandit14@bandit.labs.overthewire.org -p 2220
cat /etc/bandit_pass/bandit14
```

**Password:** `[REDACTED]`

---

## Level 14 → 15

Submit the bandit14 password to localhost port 30000.

```bash
ssh bandit14@bandit.labs.overthewire.org -p 2220

cat /etc/bandit_pass/bandit14
```

There's a service listening on port 30000. Use netcat to connect and send the password:

```bash
nc localhost 30000
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

Server responds with "Correct!" and sends the next password.

**Password:** `[REDACTED]`

---

## Level 15 → 16

Submit the bandit15 password to port 30001 using SSL/TLS.

```bash
ssh bandit15@bandit.labs.overthewire.org -p 2220

cat /etc/bandit_pass/bandit15
```

Port 30001 requires SSL/TLS, so `nc` won't work. Use `openssl s_client`:

```bash
openssl s_client -connect localhost:30001
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

Server validates and returns the next password.

**Password:** `[REDACTED]`

---

## Level 16 → 17

Find the correct SSL/TLS port in range 31000-32000.

```bash
ssh bandit16@bandit.labs.overthewire.org -p 2220
```

Multiple ports listening. Most just echo back what you send. Need to find the one that processes credentials correctly. Use nmap to scan:

```bash
nmap -sCV -p 31000-32000 localhost
```

Finds multiple ports. Port 31790 shows SSL/TLS with certificate. Test it:

```bash
openssl s_client -connect localhost:31790 -quiet
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

Gets the private key. Save and use it:

```bash
chmod 600 key.pem
ssh -i key.pem bandit17@bandit.labs.overthewire.org -p 2220
```

**Password:** `[REDACTED]`

---

## Level 17 → 18

Find the line that changed between two files.

```bash
ssh bandit17@bandit.labs.overthewire.org -p 2220

ls -la
```

Two files: `passwords.old` and `passwords.new`. Both have 100+ lines. Use `diff`:

```bash
diff passwords.old passwords.new
```

Shows one line changed. The new password is marked with `>`.

**Password:** `[REDACTED]`

---

## Level 18 → 19

Read the readme file when logging in logs you out immediately.

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220
```

Logs you out instantly. The `.bashrc` has a logout command. Instead of an interactive shell, execute a command directly:

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat ~/readme"
```

Reads the file before `.bashrc` logout happens.

**Password:** `[REDACTED]`

---

## Level 19 → 20

Use a SETUID binary to execute a command as another user.

```bash
ssh bandit19@bandit.labs.overthewire.org -p 2220

ls -la
```

There's `bandit20-do` with an `s` in the permissions. That's the SETUID bit — when you run it, it executes as the owner (bandit20). Use it to read the password:

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

**Password:** `[REDACTED]`

---

## Level 20 → 21

Use a SETUID binary to connect to a port and exchange passwords.

```bash
ssh bandit20@bandit.labs.overthewire.org -p 2220

ls -la
```

The `suconnect` binary connects to a port and validates the password. Set up a listener in one terminal:

```bash
nc -lnvp 4444
```

And connect with the binary in another:

```bash
./suconnect 4444
```

When it connects, send the bandit20 password. It validates and sends back the next password.

**Password:** `[REDACTED]`

---

## Level 21 → 22

Find the password by examining a cron job.

```bash
ssh bandit21@bandit.labs.overthewire.org -p 2220

cat /etc/cron.d/cronjob_bandit22
```

Cron job runs a script as bandit22. Check the script:

```bash
cat /usr/bin/cronjob_bandit22.sh
```

It writes the password to `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv`. Just read it:

```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

**Password:** `[REDACTED]`

---

## Level 22 → 23

Figure out the filename a cron job uses and read that file.

```bash
ssh bandit22@bandit.labs.overthewire.org -p 2220

cat /usr/bin/cronjob_bandit23.sh
```

The script uses md5sum to generate a filename:

```bash
myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

Calculate what the filename is for bandit23:

```bash
echo 'I am user bandit23' | md5sum | cut -d ' ' -f 1
# 8ca319486bfbbc3663ea0fbe81326349

cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

**Password:** `[REDACTED]`

---

## Level 23 → 24

Exploit a cron job that executes scripts from a spool directory.

```bash
ssh bandit23@bandit.labs.overthewire.org -p 2220

cat /usr/bin/cronjob_bandit24.sh
```

The script executes files from `/var/spool/bandit24/foo/` that are owned by bandit23. Create a malicious script:

```bash
cat > /tmp/script.sh << 'SCRIPT'
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/b24pass
chmod 666 /tmp/b24pass
SCRIPT

chmod +x /tmp/script.sh
cp /tmp/script.sh /var/spool/bandit24/foo/
```

Wait a minute for the cron job to run it, then read:

```bash
cat /tmp/b24pass
```

**Password:** `[REDACTED]`

---

## Level 24 → 25

Brute-force a 4-digit PIN combined with the password.

```bash
ssh bandit24@bandit.labs.overthewire.org -p 2220
```

A daemon on port 30002 wants `password PIN` (separated by space). The PIN is 4 digits (0000-9999). Generate all combinations and send them:

```bash
for i in $(seq -w 0 9999); do
  echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i"
done | nc localhost 30002 | grep -v "Wrong"
```

The `seq -w` generates zero-padded numbers. Filters out all the "Wrong" responses to find the correct one.

**Password:** `[REDACTED]`

---

## Level 25 → 26

Log in with a private key but escape the restricted shell.

```bash
ssh bandit25@bandit.labs.overthewire.org -p 2220

cat bandit26.sshkey > key.pem
chmod 600 key.pem
ssh -i key.pem bandit26@bandit.labs.overthewire.org -p 2220
```

Gets kicked out immediately. Check what shell is being used:

```bash
cat /etc/passwd | grep bandit26
# bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

It's using `/usr/bin/showtext` which runs `more` on a text file. If the terminal is small enough, `more` will paginate. Resize your terminal window to be very small, then SSH in again. When `more` shows the text, press `v` to open vim:

```
:set shell=/bin/bash
:shell
```

Now you have a real bash shell!

**Password:** `[REDACTED]`

---

## Level 26 → 27

Use the SETUID binary to read bandit27 password.

```bash
ssh -i key.pem bandit26@bandit.labs.overthewire.org -p 2220

ls -la
```

There's `bandit27-do`. Use it:

```bash
./bandit27-do cat /etc/bandit_pass/bandit27
```

**Password:** `[REDACTED]`

---

## Level 27 → 28

Clone the git repo and check the README.

```bash
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
cd repo
cat README
```

Password is right there.

**Password:** `[REDACTED]`

---

## Level 28 → 29

Check git history to find redacted passwords.

```bash
git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
cd repo
cat README.md
```

Password is redacted with `xxxxxxxxxx`. Check git history:

```bash
git log
git show 00daa614aac60bd2981c381484191eb7bc4dcfd9
```

The diff shows the original password before it was redacted.

**Password:** `[REDACTED]`

---

## Level 29 → 30

Check different branches for passwords.

```bash
git clone ssh://bandit29-git@bandit.labs.overthewire.org:2220/home/bandit29-git/repo
cd repo
cat README.md
```

Master branch has nothing. List all branches:

```bash
git branch -a
```

Check the `dev` branch:

```bash
git checkout remotes/origin/dev
cat README.md
```

Password is there.

**Password:** `[REDACTED]`

---

## Level 30 → 31

Look for git tags.

```bash
git clone ssh://bandit30-git@bandit.labs.overthewire.org:2220/home/bandit30-git/repo
cd repo
git tag
```

There's a `secret` tag:

```bash
git show secret
```

Password is in the tag.

**Password:** `[REDACTED]`

---

## Level 31 → 32

Push a file to the repository.

```bash
git clone ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
cd repo
cat README.md
cat .gitignore
```

README wants a `key.txt` file with content "May I come in?" pushed to master. `.gitignore` blocks `.txt` files, but force add it anyway:

```bash
echo 'May I come in?' > key.txt
git add -f key.txt
git commit -m 'add key'
git push origin master
```

Server validates and returns the password.

**Password:** `[REDACTED]`

---

## Level 32 → 33

Escape the uppercase shell using `$0`.

```bash
ssh bandit32@bandit.labs.overthewire.org -p 2220
```

You're in an uppercase shell. All input gets converted to uppercase, so normal commands fail. Use `$0` to escape:

```
>> $0
$ whoami
bandit33
$ cat /etc/bandit_pass/bandit33
```

`$0` refers to the shell itself — it's an escape to the parent shell.

**Password:** `[REDACTED]`

---

*All 33 levels completed!*