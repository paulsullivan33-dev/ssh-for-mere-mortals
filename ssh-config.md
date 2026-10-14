# The SSH Config File

Typing `ssh sam@192.0.2.1 -p 2222 -i ~/.ssh/work_key` every time
gets old fast. The config file, `~/.ssh/config`, turns that
into `ssh work`.

## Your first Host block

```sshconfig
Host work
    HostName 192.0.2.1
    User sam
    Port 2222
    IdentityFile ~/.ssh/work_key
```

Now `ssh work` means: connect to 192.0.2.1, as sam, on port
2222, using that key. Each line:

- **Host** — the nickname *you* type. It can be anything.
- **HostName** — the real address. Optional if the nickname is
  already the address.
- **User** — who to log in as. Skips the `sam@` prefix.
- **Port** — for servers not on the default port 22.
- **IdentityFile** — which private key to offer. SSH tries the
  default keys first, then this one.

Indentation is cosmetic; the keyword order doesn't matter. What
matters: settings apply to the `Host` block they're under, and
the *first* value found wins if a name matches multiple blocks.

## Wildcards and defaults

```sshconfig
# These apply to every host without its own block
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
    IdentitiesOnly yes
```

- **ServerAliveInterval 60** — send a keepalive every 60
  seconds so idle connections don't get dropped by routers.
- **IdentitiesOnly yes** — only offer the keys named by
  `IdentityFile`, instead of trying every key in your agent.
  (Prevents the "too many authentication failures" error in the
  troubleshooting chapter.)

Patterns work too: `Host *.example.com` matches that whole
domain, `Host 192.0.2.*` matches a subnet.

## Proving what SSH will do: `ssh -G`

The config file is code, and code deserves a test. `ssh -G`
prints the *effective* settings for a host without connecting:

```console
$ ssh -G work | grep -E "^(user|hostname|port|identityfile) "
user sam
hostname 192.0.2.1
port 2222
identityfile ~/.ssh/work_key
```

(The `grep -E` just trims the output — `ssh -G` prints every
setting SSH knows, over a hundred lines, in its own fixed
order.) If the nickname doesn't behave the way you expect,
`ssh -G` shows you exactly what SSH *thinks* you asked for.
All four lines above were verified against a real OpenSSH
9.6p1 client with the config block shown.

## A realistic example

```sshconfig
Host home
    HostName 192.0.2.10
    User sam

Host work
    HostName 192.0.2.1
    User sam
    Port 2222
    IdentityFile ~/.ssh/work_key

Host gitbox
    HostName git.example.com
    User git
    IdentityFile ~/.ssh/git_key

Host *
    ServerAliveInterval 60
    IdentitiesOnly yes
```

Three nicknames, one shared safety net. `ssh home`, `ssh work`,
`git clone gitbox:org/repo.git` — the last one works because
Git speaks SSH under the hood and reads the same config file.

## Check

- After adding a block, `ssh -G <nickname> | grep -E
  "^(hostname|user|port)"` shows the values you wrote.
- `~/.ssh/config` has permissions `600` (`chmod 600
  ~/.ssh/config`) — SSH warns about a config file others can
  read.
