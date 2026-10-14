# Troubleshooting

Symptom → fix. Work top to bottom; most SSH problems are one
of these six.

## "Permission denied (publickey)"

The server rejected all your keys. Check, in order:

1. **Is the public key on the server?** On the server,
   `~/.ssh/authorized_keys` must contain your `.pub` line.
   Compare fingerprints: `ssh-keygen -l -f ~/.ssh/id_ed25519.pub`
   locally vs. `ssh-keygen -l -f` on each line server-side.
2. **Are the permissions right?** `~/.ssh` must be `700`,
   `authorized_keys` `600` — see chapter 4. Wrong permissions
   fail *silently* by design.
3. **Is the server even allowing keys?** `PubkeyAuthentication`
   defaults to yes; a hardened config might say otherwise.

## "Too many authentication failures"

Your client offered too many keys before the right one. The
server gives up after a handful (default 6). Fix: tell SSH to
offer only the configured key:

```sshconfig
Host myserver
    IdentitiesOnly yes
    IdentityFile ~/.ssh/myserver_key
```

## "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED"

The server's host key differs from `~/.ssh/known_hosts`.
**Stop and think before overriding.** Legitimate causes:
the server was reinstalled, or its keys were regenerated.
Illegitimate cause: someone is intercepting you.

If you *know* the server was rebuilt, remove the old entry:

```console
$ ssh-keygen -R 192.0.2.1
```

then reconnect and verify the new fingerprint. If you did
*not* expect a change, investigate before proceeding.

## "Connection timed out" / "Connection refused"

- **Timed out** — nothing answered. Wrong address, server
  down, or a firewall eating the packets.
- **Refused** — something answered and said no. `sshd`
  isn't running, or it's on a different port (`-p 2222`).

`ssh -v` (one v) shows the handshake step by step; `-vvv`
shows everything. Read it bottom-up: the *last* lines say
where it died.

## It worked yesterday

- **Did your IP change?** Home IPs rotate; a firewall rule
  allowing your old address won't match the new one.
- **Did the server's disk fill?** SSH needs to write to
  `~/.ssh/known_hosts`-adjacent files and logs; a full disk
  produces bizarre failures.
- **Key expired?** Certificates (not plain keys) expire —
  `ssh-keygen -L -f key-cert.pub` shows validity.

## The permission checklist, one more time

```console
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/id_ed25519 ~/.ssh/authorized_keys ~/.ssh/config
$ ls -la ~/.ssh
```

If key login fails and everything *looks* right, run through
this list literally. Nine times out of ten, it's permissions.

## Check

- `ssh -G myserver` → config parses; the problem is
  elsewhere.
- `ssh -v myserver` → the debug output names the failing
  step (auth, host key, connection).
- Server-side `/var/log/auth.log` (Debian/Ubuntu) or
  `/var/log/secure` (RHEL) → the server's own account of
  why it said no.
