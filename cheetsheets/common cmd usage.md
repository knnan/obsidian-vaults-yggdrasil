Here is a **clean, copy-friendly Markdown cheatsheet** containing **everything you've learned so far**:

- `echo`
    
- `sh -c`
    
- null-terminated tricks
    
- `sed` (insert lines)
    
- `awk` vs `sed`
    
- `jq` basics
    

Ready for Obsidian.  
No fluff, no commentary — just pure reference.

---

# 🧰 **Linux CLI Essentials — Cheatsheet**

## ----------------------------------------------------

# **echo**

### Basic usage

```bash
echo "Hello World"
```

### Options

|Option|Meaning|
|---|---|
|`-n`|no trailing newline|
|`-e`|enable escapes (`\n`, `\t`, etc.)|
|`-E`|disable escapes (default)|

### Common escapes

```bash
echo -e "Line1\nLine2"
echo -e "Name:\tAlice"
```

### Variables

```bash
echo "Hello $USER"
```

### Redirect to file

```bash
echo "log entry" >> file.txt
```

---

## ----------------------------------------------------

# **sh -c**

### What it does

Runs a command string in a new POSIX shell:

```bash
sh -c "echo hello"
```

### Why use it?

- commands with pipes, redirection, `$(...)`
    
- when a program accepts _only one_ command string (systemd, Docker, cron)
    
- when using `find -exec`, `xargs`, or `sudo`
    

### `$0`, `$1`, `$2` rules

Inside `sh -c`:

- The first argument after the script becomes `$0`
    
- The next arguments become `$1`, `$2`, …
    

Example:

```bash
sh -c 'echo "$0 $1 $2"' name arg1 arg2
```

### Safe use (avoid injection)

```bash
sh -c 'echo "$1"' _ "$user_input"
```

### With sudo

```bash
sudo sh -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'
```

### With find

```bash
find . -name "*.log" -exec sh -c 'echo "$1"' _ {} \;
```

---

## ----------------------------------------------------

# **Convert to null-terminated**

### Replace spaces with NUL

```bash
echo "1 2 3" | tr ' ' '\0'
```

### Expand tokens and print null terminated

```bash
echo "1 2 3" | xargs -n1 printf "%s\0"
```

---

## ----------------------------------------------------

# **sed (insert lines)**

### Insert **before** pattern

```bash
sed '/PATTERN/i New line here' file
```

### Insert **after** pattern

```bash
sed '/PATTERN/a New line here' file
```

### Insert using line numbers

```bash
sed '5i Insert before line 5'
sed '5a Insert after line 5'
```

### Multiple lines

```bash
sed '/PATTERN/i\
Line 1\
Line 2\
Line 3
' file
```

### In-place edit

```bash
sed -i '/PATTERN/a Inserted line' file
```

---

## ----------------------------------------------------

# **awk vs sed**

### When to use **sed**

- simple, line-based edits
    
- substitutions (`s/old/new/`)
    
- delete or insert lines
    
- in-place file editing
    
- super fast for streaming text
    

### When to use **awk**

- working with **columns/fields**
    
- doing math, sums, grouping
    
- complex conditions
    
- formatting output
    
- building reports
    
- multi-line logic/state
    

### Common `awk` examples

Extract column:

```bash
awk '{print $3}' file
```

Filter:

```bash
awk '$2 > 10'
```

Sum:

```bash
awk '{sum+=$2} END{print sum}'
```

Number lines:

```bash
awk '{print NR, $0}'
```

---

## ----------------------------------------------------

# **jq Basics**

### Pretty print JSON

```bash
jq .
```

### Access fields

```bash
jq '.name'
jq '.user.age'
```

### Arrays

```bash
jq '.[0]'      # first element
jq '.[]'       # iterate
```

### Filtering

```bash
jq '.[] | select(.age > 30)'
```

### Extract specific fields

```bash
jq '.[] | .name'
jq '.[] | select(.age>30) | .name'
```

### Modify JSON

```bash
jq '.age = 30'
jq '. + {country:"US"}'
jq 'del(.password)'
```

### Output raw strings

```bash
jq -r '.name'
```

### Build objects

```bash
jq '{user:.name, years:.age}'
```

### Math

```bash
jq '.x + .y'
```

---

# ✔️ **End of Cheatsheet**

If you'd like, I can also bundle this into:

- a **downloadable Markdown file**
    
- a **print-ready PDF**
    
- an Obsidian **canvas** version
    

Just tell me!