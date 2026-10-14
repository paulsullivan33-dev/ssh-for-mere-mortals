# What Is SSH?

SSH (Secure Shell) is an encrypted pipe between two computers. You
type on yours; the commands run on the other one; everything in
between is scrambled so nobody watching the network can read it.

That's the whole idea. Everything in this guide is a variation on
it: a pipe for typing, a pipe for files, a pipe for other programs
to borrow.

## The picture

```
your keyboard ──▶ [ SSH: encrypted ] ──▶ the other computer
                                            runs your commands
```

Before SSH existed, people used telnet and rsh, which sent
everything — including passwords — as plain readable text. Anyone
on the same network could watch. SSH replaced them by encrypting
the connection from the very first message.

## Two programs, two sides

SSH is always a pair:

- **The client** (`ssh`) runs on your machine. It's the part you
  type into.
- **The server** (`sshd`, the little "d" stands for *daemon*, a
  program that runs in the background) runs on the remote machine,
  listening for incoming connections.

This guide is mostly about the client — connecting *out* to
servers. The server side gets one chapter near the end, because
sooner or later you'll be the one running it.

## What SSH is not

- **Not a VPN.** A VPN encrypts *all* of a machine's network
  traffic. SSH encrypts one connection at a time, to one machine.
  (It can carry other traffic through tunnels — that's a later
  chapter — but it's still one pipe, not a whole network.)
- **Not just a terminal.** The same pipe moves files (`scp`,
  `sftp`, `rsync`) and can forward network ports for other
  programs.

## The three questions SSH answers

Every SSH connection answers three questions, in this order:

1. **Is the server who it claims to be?** (host keys — chapter 2)
2. **Are you allowed in?** (passwords and keys — chapters 3–4)
3. **What may you do?** (whatever account you logged in as may do)

Keep those three in mind. Half of SSH troubleshooting is figuring
out which question failed.

## Check

You don't need a server yet. Just confirm the client exists:

```console
$ ssh -V
OpenSSH_9.6p1 Ubuntu-3ubuntu13.19, OpenSSL 3.0.13 30 Jan 2024
```

Any OpenSSH 8.x or 9.x works for this guide. (The version string
above is from the machine this guide was tested on — yours will
differ, and that's fine.)
