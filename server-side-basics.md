# Server-Side Basics

Sooner or later you'll be the one running `sshd`. This chapter
is the minimum worth knowing; the
[Cybersecurity for Mere Mortals](https://paulsullivan33-dev.github.io/cybersecurity-for-mere-mortals/)
SSH-hardening chapter goes deeper.

## The one file

Server settings live in `/etc/ssh/sshd_config`. After editing,
validate before restarting — a bad config can lock you out:

```console
$ sudo sshd -T -f /etc/ssh/sshd_config
```

`sshd -T` prints the effective configuration (or an error
pointing at the offending line). Only restart the service
after it passes. (This validation was run for real during
writing — every directive below was accepted by OpenSSH 9.6p1.)

## The sane baseline

```sshdconfig
# No password logins: keys only
PasswordAuthentication no

# Nobody logs in as root directly
PermitRootLogin no

# Only these users may connect at all
AllowUsers sam

# Idle sessions get a nudge, then dropped
ClientAliveInterval 300
ClientAliveCountMax 2
```

What each buys you:

- **PasswordAuthentication no** — kills password guessing
  entirely. (Set this up *after* your key works, or you'll
  lock yourself out. Test a new terminal window before
  closing the old one.)
- **PermitRootLogin no** — attackers love the root account
  because it always exists. Make them guess a username too.
  You can still `sudo` once logged in as yourself.
- **AllowUsers** — a short guest list. Everyone else is
  refused before authentication even starts.
- **ClientAlive\*** — dead sessions don't linger forever.

## fail2ban: the bouncer

Even with keys only, bots will knock thousands of times a
day. **fail2ban** watches the logs and temporarily bans IPs
that fail too often. It's one package and mostly works out of
the box for SSH:

```console
$ sudo apt install fail2ban
```

(Illustrative — not installed during writing. The SSH jail is
enabled by default on Debian/Ubuntu packaging.)

## Changing the port: honesty

You'll hear "move SSH off port 22 to cut down on bot noise."
It's true that it quiets the logs — bots scan the default
port hardest. It's also true that it's not real security: a
targeted attacker port-scans. Do it to reduce noise if you
like; don't do it *instead* of keys and the settings above.

## Check

- `sudo sshd -T | grep -Ei "^(passwordauthentication|permitrootlogin|allowusers)"`
  prints your effective values → confirms what the server
  will actually enforce.
- From another machine, `ssh root@yourserver` fails →
  root login is really off.
- After `PasswordAuthentication no`, a password-only login
  attempt fails immediately → keys are the only way in.
