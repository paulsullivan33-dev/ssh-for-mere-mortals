# Your First Key

## Generate the pair

```console
$ ssh-keygen -t ed25519 -C "sam@laptop"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/sam/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/sam/.ssh/id_ed25519
Your public key has been saved in /home/sam/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:gaSJr4NCcTUh0xXnd770NukHgN6I1OBINh0es/vTkCQ sam@laptop (ED25519)
The key's randomart image is:
+--[ED25519 256]--+
|                 |
...
```

Notes on the prompts:

- **File:** accept the default. SSH looks there automatically.
- **Passphrase:** this encrypts the private key on your disk. A
  passphrase means a stolen laptop doesn't hand over your
  servers too. You can leave it empty for convenience (common
  for automation keys), but know the tradeoff: anyone who reads
  the file owns your access. Chapter 7 shows how the SSH agent
  lets you type the passphrase once per session instead of per
  connection.

Two files now exist:

- `~/.ssh/id_ed25519` — the private key. **Never share it.**
- `~/.ssh/id_ed25519.pub` — the public key. This one travels.

(`-t ed25519` picks the modern key type: small, fast, and
secure. Older guides say `-t rsa -b 4096`; ed25519 is the
current default recommendation and what `ssh-keygen` picks if
you omit `-t` on recent versions.)

## Install the public key on the server

The classic tool copies it for you:

```console
$ ssh-copy-id sam@192.0.2.1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/sam/.ssh/id_ed25519.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
sam@192.0.2.1's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'sam@192.0.2.1'"
and check to make sure that only the key(s) you wanted were added.
```

(The transcript above is real output from OpenSSH 9.6p1's
`ssh-copy-id`, with the address adapted — the INFO lines and
the closing message are verbatim.)

It asks for your password one last time, appends the public key
to `~/.ssh/authorized_keys` on the server, and fixes the file
permissions. From now on, `ssh sam@192.0.2.1` logs you in with
the key — no password.

Doing it by hand is the same three steps `ssh-copy-id`
automates: append the `.pub` contents to
`~/.ssh/authorized_keys` on the server, and make sure the
permissions are right (next section).

## The permission rules

SSH is strict about file permissions, because loose permissions
mean other users on the machine could read or replace your
keys. If they're wrong, key login fails silently and you're
back to passwords. Memorize these:

| Path                    | Permissions | Meaning              |
|-------------------------|-------------|----------------------|
| `~/.ssh`                | `700`       | Only you may enter   |
| `~/.ssh/id_ed25519`     | `600`       | Only you may read    |
| `~/.ssh/authorized_keys`| `600`       | Only you may read/write |
| `~/.ssh/config`         | `600`       | Only you may read    |

Set them with:

```console
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/id_ed25519 ~/.ssh/authorized_keys ~/.ssh/config
```

**Check:** `ls -la ~/.ssh` should show `drwx------` for the
directory and `-rw-------` for the key files. (These exact
requirements were verified against a real `sshd` with
`StrictModes` on — see the validation notes.)

## One key or many?

Start with one key pair for everything. Add a second only when
you have a reason: a passphrase-free key for scripts (so a
compromised script can't unlock your interactive key), or a
separate key for work vs personal. The `~/.ssh/config` file
(chapter 5) picks which key goes where, so juggling is painless.

## Check

- `ls ~/.ssh/` shows `id_ed25519` and `id_ed25519.pub`.
- `ssh sam@192.0.2.1` logs in without asking for a password
  (or asks only for the key's passphrase).
- `ssh-keygen -l -f ~/.ssh/id_ed25519.pub` prints the key's
  fingerprint — compare it with what the server has if you're
  ever unsure which key is installed where.
