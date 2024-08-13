##  [AGE](https://github.com/FiloSottile/age)

1. Creating age keys
```bash
$ age-keygen -o key.txt
Public key: age160p3xkjzyhccn4e0ewwszvzqctl3zy30kh0fqk0kxl6t63w6j4hqxx4zwk
```

```bash
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