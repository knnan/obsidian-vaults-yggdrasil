##  [AGE](https://github.com/FiloSottile/age)

1. Creating age keys
	```bash
	$ age-keygen -o key.txt
	Public key: age160p3xkjzyhccn4e0ewwszvzqctl3zy30kh0fqk0kxl6t63w6j4hqxx4zwk
	
	$ cat key.txt
	
	# created: 2022-12-04T18:34:36+03:00
	# public key: age160p3xkjzyhccn4e0ewwszvzqctl3zy30kh0fqk0kxl6t63w6j4hqxx4zwk
	AGE-SECRET-KEY-1W2259L9GJ99E28FFQQSFNJMEARKA7LXRP3E927WF0TARNDJ9L87SZCC5ND
	```

2. Encryption
```bash
age -o secret.txt.age -r age160p3xkjzyhccn4e0ewwszvzqctl3zy30kh0fqk0kxl6t63w6j4hqxx4zwk secret.txt
```

## 2.1 Encryption with SSH Keys[](https://tech.serhatteker.com/post/2022-12/encrypt-and-decrypt-files-with-ssh-part-4/#21-encryption-with-ssh-keys)

As mentioned before, `age` also supports encrypting to `ssh-rsa` and `ssh-ed25519` SSH public keys, and decrypting with the respective private key file. To encrypt:

```bash
$ age -R ~/.ssh/id_ed25519.pub secret.txt > secret.txt.age
```

**3. Using passphrases**
*	**Encrypting**:
```bash	
$ age -p plain.txt > secret.txt.age
```

* **Decryption**:
```bash
	age -d secret.txt.age > plain.txt
```


### Different modes of encryption 


## Encryption:

````markdown
# Encrypting Files with `age`

## Generating a Key Pair
```bash
age-keygen -o key.txt
````

This creates:

- **Private key** inside `key.txt` (line starting with `AGE-SECRET-KEY-...`)
    
- **Public key** shown in the comment line:
       ```
    # public key: age1abcd...xyz
    ```
    

---

## Option 1: Encrypt with Public Key (Asymmetric)

### Encrypt a file

```bash
age -r age1abcd... -o archive.tar.gz.age archive.tar.gz
```

### Decrypt a file

```bash
age -d -i key.txt -o archive.tar.gz archive.tar.gz.age
```

### One-liner: Tar, gzip, and encrypt a folder

```bash
tar -cz folder_to_encrypt | age -r age1abcd... -o folder.tar.gz.age
```

### One-liner: Decrypt and extract directly

```bash
age -d -i key.txt folder.tar.gz.age | tar -xz
```

✅ Recommended for sharing with others.  
✅ Keep your private key secret; only publish the public key.

---

## Option 2: Encrypt with Identity File (Symmetric using Private Key)

### Encrypt a file

```bash
age --encrypt -i key.txt -o archive.tar.gz.age archive.tar.gz
```

### Decrypt a file

```bash
age --decrypt -i key.txt -o archive.tar.gz archive.tar.gz.age
```

### One-liner: Tar, gzip, and encrypt a folder

```bash
tar -cz folder_to_encrypt | age --encrypt -i key.txt -o folder.tar.gz.age
```

### One-liner: Decrypt and extract directly

```bash
age --decrypt -i key.txt folder.tar.gz.age | tar -xz
```

✅ Good for personal backups.  
❌ Not suitable for sharing (requires giving away your private key).  
⚠️ If you lose `key.txt`, you cannot decrypt.

---

## Option 3: Password-based Encryption

### Encrypt a file

```bash
age -p -o archive.tar.gz.age archive.tar.gz
```

### Decrypt a file

```bash
age -d -o archive.tar.gz archive.tar.gz.age
```

### One-liner: Tar, gzip, and encrypt a folder

```bash
tar -cz folder_to_encrypt | age -p -o folder.tar.gz.age
```

### One-liner: Decrypt and extract directly

```bash
age -d folder.tar.gz.age | tar -xz
```

✅ Simple and doesn’t need key files.  
❌ Security depends on the strength of the password.

---

## 🔑 Summary: Public vs Private vs Password

|Method|Encrypts With|Decrypts With|Best Use Case|
|---|---|---|---|
|Public Key (`-r pubkey`)|Recipient’s pubkey|Private key|Sharing with others securely|
|Identity File (`-i key.txt` with `--encrypt`)|Your private key|Same private key|Personal self-encryption/backups|
|Password (`-p`)|A password|Same password|Quick/simple encryption|

```

---

This way you can **encrypt/decrypt without ever writing the tarball to disk** 🚀  

Do you also want me to add **examples with multiple recipients** (encrypting to more than one public key at once)?
```