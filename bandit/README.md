# Bandit — OverTheWire

**Category:** Linux Fundamentals  
**Levels:** 34  
**Access:** SSH  

---

## What is Bandit?

Bandit is designed for absolute beginners. Each level teaches a new Linux concept by making you find a password hidden somewhere on the system. That password is then used to SSH into the next level.

```bash
# General connection format
ssh bandit<LEVEL>@bandit.labs.overthewire.org -p 2220
```

---

## Concepts Covered

| Levels | Concept |
|--------|---------|
| 0 → 1 | SSH, basic file reading |
| 1 → 2 | Files with special names (`-`) |
| 2 → 3 | Files with spaces in name |
| 3 → 4 | Hidden files (`ls -la`) |
| 4 → 5 | File types (`file` command) |
| 5 → 6 | `find` with size and permissions |
| 6 → 7 | `find` across entire filesystem |
| 7 → 8 | `grep` through large files |
| 8 → 9 | `sort` + `uniq` |
| 9 → 10 | `strings` command |
| 10 → 11 | Base64 decoding |
| 11 → 12 | ROT13 / `tr` command |
| 12 → 13 | Hexdump reversal + recursive decompression |

---

## Level Writeups

→ [All Levels](./levels.md)

---