# Your First Connection

The basic command has three parts: the program, who you want to
be on the other machine, and the machine itself.

```console
$ ssh sam@192.0.2.1
```

- `sam` — the username on the *remote* machine. (It doesn't have
  to match your local username.)
- `192.0.2.1` — the remote machine's address. This is a
  documentation address; substitute your server's real address.
  A hostname like `server.example.com` works the same way.

If your remote username matches your local one, you can skip it:

```console
$ ssh 192.0.2.1
```

## The first-run question

The very first time you connect to a server, you'll see something
like this:

```console
The authenticity of host '192.0.2.1 (192.0.2.1)' can't be established.
ED25519 key fingerprint is SHA256:3yurdUKf1UrZhgnLM7ECGOhugKfqHfG56aYVx4gylsU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

This is question 1 from the last chapter: *is the server who it
claims to be?* The server is showing you its **host key**
fingerprint — a short ID for the server's identity.

Type `yes` (the full word — `y` alone won't do) and the
fingerprint is saved in `~/.ssh/known_hosts` on your machine.
Next time, SSH checks the saved fingerprint silently. If it ever
*changes*, SSH refuses to connect and warns you loudly — that's
the protection working.

**The honest caveat:** typing `yes` the first time is a leap of
faith. You're trusting that the machine answering is really yours
and not an impostor. On a home network, that trust is usually
fine. If you want to be rigorous, compare the fingerprint with
the one shown on the server itself:

```console
# run ON the server, once
$ ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key.pub
256 SHA256:47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU root@myserver (ED25519)
```

If the two fingerprints match, nobody is sitting in the middle.

## Logging in

After the host key check, the server asks who's knocking —
usually a password prompt:

```console
sam@192.0.2.1's password:
```

Type it (nothing appears as you type — that's normal, not
broken) and you're in. Your prompt changes to the remote
machine's, and every command you type now runs *there*.

Type `exit` (or press Ctrl+D) to close the connection and come
home.

## Running one command

You don't have to open a full session. Put a command after the
address and SSH runs it remotely, then disconnects:

```console
$ ssh sam@192.0.2.1 "uptime"
 14:32:10 up 12 days,  3:11,  1 user,  load average: 0.08, 0.03, 0.01
```

This is the building block of all SSH automation: scripts that
reach out, run something, and come back.

## Check

- `ssh -V` prints a version → the client is installed.
- First connection asks about the host key → type `yes`, and
  `~/.ssh/known_hosts` is created.
- `ssh sam@192.0.2.1 "hostname"` prints the *remote* machine's
  name, proving your command ran over there.
