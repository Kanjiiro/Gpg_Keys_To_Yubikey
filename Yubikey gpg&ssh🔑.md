# Yubikey Gpg & Ssh 🔑

## Table of Contents
=================

* [Yubikey Gpg &amp; Ssh <g-emoji class="g-emoji" alias="key" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f511.png">🔑</g-emoji>](#yubikey-gpg--ssh-)
   * [1 PreConf:](#1-preconf)
      * [1.1 Init](#11-init)
      * [1.2 Generate 3 Keys (S E C)](#12-generate-3-keys-s-e-c)
      * [1.3 Export Keys &amp; Gen Revoke Key](#13-export-keys--gen-revoke-key)
      * [1.4 Export Public Keys](#14-export-public-keys)
   * [2 PostConf](#2-postconf)
      * [2.1 Require Touch:](#21-require-touch)
      * [2.2 Add to conf.fish for Ssh:](#22-add-to-conffish-for-ssh)

<!-- Created by https://github.com/ekalinin/github-markdown-toc -->
## 1 PreConf:

### 1.1 Init

```bash
$ mkdir Desktop/my_keys

$ openssl rand -base64 24 > Desktop/my_keys/passwd.txt

$ cat Desktop/my_keys/passwd.txt

$ gpg --expert --full-generate-key
```

```input
8
S
E
Q

4096

0
y

YourUsername

YourEmail
```

---

### 1.2 Generate 3 Keys (S E C)

```bash
$ gpg --expert --edit-key YourEmail

$ gpg>addkey
```

```input
4

3y

addkey

6

3y

addkey 

8

s
e
a

3y

save
```

---

### 1.3 Export Keys & Gen Revoke Key

```bash
$ gpg --armor --export-secret-keys YourEmail> Desktop/my_keys/master.key

$ gpg --gen-revoke YourEmail > Desktop/my_keys/revoke.asc
```

```input
y
0
y
```

---

### 1.4 Export Public Keys

```bash
$ gpg --export --armor YourEmail > Desktop/my_keys/pub.key

$ gpg --export-ssh YourEmail > Desktop/my_keys/ssh_pub.key

$ gpg --export --export-secret-subkeys YourEmail > Desktop/my_keys/sub.key

$ gpg --fingerprint --fingerprint YourEmail > Desktop/my_keys/fingerprint.txt
```

```bash
$ mv Desktop/my_keys/ .gnupg/

$ gpg --list-secret-keys

$ tar -czf gpg-datadir.tar.gz .gnupg/

$ mv gpg-datadir.tar.gz Desktop/
```

---

## 2 PostConf

### 2.1 Require Touch:

```bash
Authentication:

$ ykman openpgp keys set-touch aut on

Signing:

$ ykman openpgp keys set-touch sig on

Encryption:

$ ykman openpgp keys set-touch enc on
```

### 2.2 Add to conf.fish for Ssh:

Changes who handles the authentication from ssh keyring to gpg

``` bash
set -x GPG_TTY (tty)
set -x SSH_AUTH_SOCK (gpgconf --list-dirs agent-ssh-socket)
gpgconf --launch gpg-agent
```
