# SSH for Mere Mortals

**SSH** (Secure Shell) is the encrypted pipe between your
computer and every server you'll ever touch. This guide teaches
you to *use* it confidently: connecting, keys, the config file,
jump hosts, tunnels, moving files, and fixing it when it
breaks.

No CS degree required. Ideas first, then real commands against
a real OpenSSH.

## What this guide covers

- **What SSH is** — the encrypted-pipe idea, and the three
  questions every connection answers
- **Your first connection** — `ssh user@host`, host keys, and
  the first-run leap of faith
- **Passwords vs keys** — why keys win, and the padlock
  mental model
- **Your first key** — `ssh-keygen`, `ssh-copy-id`, and the
  permission rules
- **The SSH config file** — nicknames, ports, and per-host
  settings, verified with `ssh -G`
- **Jump hosts** — reaching private networks through one
  command (`ProxyJump`)
- **The SSH agent** — type your passphrase once; plus the
  honest caveats on forwarding
- **Connection multiplexing** — pay the handshake once
- **Tunneling** — `-L`, `-R`, and `-D`, each with a concrete
  use case
- **Moving files** — `scp`, `sftp`, and `rsync` over SSH
- **Server-side basics** — the sane `sshd_config`, validated
  before restart (deeper hardening lives in the
  [Cybersecurity guide](https://paulsullivan33-dev.github.io/cybersecurity-for-mere-mortals/))
- **Troubleshooting** — symptom → fix, six ways

## What it doesn't cover

SSH certificates (the enterprise way to manage keys),
`sshd` advanced auth (Kerberos, 2FA modules), and running
public-facing SSH at scale — the Cybersecurity guide covers
the hardening side. This guide is about *using* SSH well.

## How to use this guide

Every command was checked against the real OpenSSH 9.6p1
client before shipping — flags verified, config examples
parsed with `ssh -G`, keys generated for real, and a loopback
server used for the connection chapters. **Check:** blocks
show verified output. Anything labeled illustrative says so.

## Chapters

1. [What Is SSH?](what-is-ssh.md)
2. [Your First Connection](your-first-connection.md)
3. [Passwords vs Keys](passwords-vs-keys.md)
4. [Your First Key](your-first-key.md)
5. [The SSH Config File](ssh-config.md)
6. [Jump Hosts](jump-hosts.md)
7. [The SSH Agent](the-ssh-agent.md)
8. [Connection Multiplexing](multiplexing.md)
9. [Tunneling](tunneling.md)
10. [Moving Files](moving-files.md)
11. [Server-Side Basics](server-side-basics.md)
12. [Troubleshooting](troubleshooting.md)
13. [Glossary](glossary.md)

*Written to the [nine-point writing standard](criteria.md) shared by
every guide in this series.*
