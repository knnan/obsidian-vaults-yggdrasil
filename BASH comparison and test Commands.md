Got it 👍 here’s a **brief, practical cheat sheet** you can keep in mind for Bash test conditions.

---

# 📌 Bash Test Cheat Sheet — `[ ... ]` vs `[[ ... ]]`

## 🔹 Syntax

- `[ ... ]` → POSIX, portable, always quote variables!
    
- `[[ ... ]]` → Bash/Zsh/Ksh only, safer, supports regex & logical ops.
    

---

## 🔹 File Tests

```bash
[ -e file ]   # exists
[ -f file ]   # regular file
[ -d dir ]    # directory
[ -r file ]   # readable
[ -w file ]   # writable
[ -x file ]   # executable
```

---

## 🔹 String Tests

```bash
[ -z "$s" ]       # empty
[ -n "$s" ]       # not empty
[ "$a" = "$b" ]   # equal (use == inside [[ ... ]])
[ "$a" != "$b" ]  # not equal
```

Extra with `[[ ... ]]`:

```bash
[[ $a = foo* ]]          # glob match
[[ $a =~ ^foo[0-9]+$ ]]  # regex match
```

---

## 🔹 Number Tests

```bash
[ "$a" -eq "$b" ]   # equal
[ "$a" -ne "$b" ]   # not equal
[ "$a" -lt "$b" ]   # less than
[ "$a" -le "$b" ]   # ≤
[ "$a" -gt "$b" ]   # greater than
[ "$a" -ge "$b" ]   # ≥
```

---

## 🔹 Combining Conditions

With `[ ... ]`:

```bash
[ cond ] && [ cond ]   # AND
[ cond ] || [ cond ]   # OR
! [ cond ]             # NOT
```

With `[[ ... ]]`:

```bash
[[ cond && cond ]]     # AND
[[ cond || cond ]]     # OR
[[ ! cond ]]           # NOT
```

---

## 🔹 Key Differences

- `[ ... ]` → portable, requires quotes, no regex.
    
- `[[ ... ]]` → safer, allows `&&`, `||`, globbing, regex.
    

---

✅ **Rule of Thumb**:

- Use `[[ ... ]]` in Bash scripts whenever possible.
    
- Use `[ ... ]` only if you need `/bin/sh` portability.
    

---

