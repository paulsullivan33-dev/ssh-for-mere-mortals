# The SSH Agent

A passphrase on your key is good security and mild annoyance:
SSH asks for it on *every* connection. The **SSH agent** fixes
the annoyance: you unlock the key once, and the agent answers
for you until you log out.

## Starting it

```console
$ eval "$(ssh-agent -s)"
Agent pid 1234
$ ssh-add ~/.ssh/id_ed25519
Enter passphrase for /home/sam/.ssh/id_ed25519:
Identity added: /home/sam/.ssh/id_ed25519 (sam@laptop)
```

- `ssh-agent -s` starts the agent and prints shell commands
  that point SSH at it; `eval` runs them.
- `ssh-add` unlocks the key into the agent. One passphrase
  typed, then every later `ssh` just works.

(On most Linux desktops and on macOS, an agent is already
running when you log in — try `ssh-add -l` before starting
your own. Windows' OpenSSH ships its own agent service too.)

Useful agent commands:

| Command          | Does                              |
|------------------|-----------------------------------|
| `ssh-add -l`     | List keys the agent holds         |
| `ssh-add -d key`| Remove one key                    |
| `ssh-add -D`     | Remove all keys (lock the agent)  |

## Agent forwarding: the convenient risk

Add `-A` (or `ForwardAgent yes` in config) and the *remote*
server can ask your *local* agent to authenticate further
connections — handy for hopping from server A to server B
without copying your private key to A.

**The caveat, stated plainly:** while forwarded, anyone with
root on the remote server can *use* your agent to authenticate
as you elsewhere. They can't steal the key itself, but they
can ride it. Rule of thumb: forward only to machines you
administer, and prefer `ProxyJump` (last chapter) for hopping
— it never exposes the agent at all.

## When the agent isn't the answer

- **Scripts and cron jobs** can't type a passphrase and usually
  can't reach your agent. Give them their own passphrase-free
  key, locked down server-side to exactly what the script
  needs.
- **"Could not open a connection to your authentication
  agent"** means the agent isn't running (or your shell doesn't
  know about it) — re-run the `eval` line.

## Check

- `ssh-add -l` lists your key's fingerprint → the agent is
  working.
- `ssh sam@192.0.2.1` connects with no passphrase prompt →
  the agent is answering.
- `ssh-add -D` then reconnect → passphrase prompt returns,
  proving the agent was the thing helping.
