# Connection Multiplexing

Every `ssh` command pays a small tax: TCP handshake, key
exchange, authentication — usually under a second, but it adds
up when a script opens dozens of connections. **Multiplexing**
pays it once and shares the pipe.

## How it works

The first connection becomes the **master**; later connections
to the same host ride along as passengers, skipping the
handshake entirely.

```sshconfig
Host *
    ControlMaster auto
    ControlPath ~/.ssh/sockets/%r@%h:%p
    ControlPersist 10m
```

- **ControlMaster auto** — the first connection becomes master
  automatically; later ones attach to it.
- **ControlPath** — where the shared socket file lives. `%r`,
  `%h`, `%p` expand to user, host, port, so each destination
  gets its own socket. (Create `~/.ssh/sockets/` first —
  `mkdir -p ~/.ssh/sockets`.)
- **ControlPersist 10m** — after the last passenger leaves,
  the master lingers 10 minutes in the background, so your
  next connection is still instant.

## What it feels like

```console
$ time ssh home "true"     # first: pays the handshake
real    0m0.842s
$ time ssh home "true"     # second: rides the master
real    0m0.041s
```

(The numbers above are illustrative — yours depend on network
distance. The *ratio* is the point: the second connection
skips authentication entirely.)

## Managing the master

```console
$ ssh -O check home     # is a master running?
$ ssh -O exit home      # shut the master down
$ ssh -O stop home      # stop accepting new passengers
```

## The tradeoff

A lingering master holds your authentication open. On a shared
or untrusted *client* machine, that's a window for someone
else at your keyboard. `ControlPersist` with a short timeout
(or `no`, so the master dies with the last session) is the
honest middle ground.

## Check

- `ssh -O check home` prints `Master running` → multiplexing
  is active.
- Two rapid `ssh home "true"` calls: the second is visibly
  faster.
