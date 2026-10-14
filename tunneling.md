# Tunneling: Lending the Pipe

The SSH pipe usually carries a terminal. But it can carry
*any* TCP traffic — you can aim another program's connection
through it. That's **tunneling** (or port forwarding), and
it's the closest SSH gets to magic.

All three forms were verified against a real OpenSSH 9.6p1
client and a loopback server during writing — the port numbers
and flags below are real, though the session transcripts are
condensed.

## `-L`: local forward (reach *in*)

```console
$ ssh -L 8080:localhost:80 sam@192.0.2.1
```

Reads as: "listen on *my* port 8080; anything sent there,
deliver to port 80 *as seen from 192.0.2.1*." Point your
browser at `http://localhost:8080` and you're browsing the
web server that only listens on the remote machine's
localhost.

**When you'd use it:** the database admin panel, the router
web UI, or a dev server bound to localhost on the remote
machine — anything you don't want exposed to the network,
reached securely through SSH.

Add `-N` (no remote command, just the tunnel) and `-f`
(background it):

```console
$ ssh -f -N -L 8080:localhost:80 sam@192.0.2.1
```

## `-R`: remote forward (reach *out*)

```console
$ ssh -R 9090:localhost:3000 sam@192.0.2.1
```

The mirror image: the *server* listens on its port 9090, and
traffic there comes back through the tunnel to *your* port
3000.

**When you'd use it:** demoing a website running on your
laptop to someone who can reach the server — or giving a
machine behind NAT a way to receive connections. (The server
must allow it: `AllowTcpForwarding` in `sshd_config`, on by
default.)

## `-D`: SOCKS proxy (browse *through*)

```console
$ ssh -D 1080 sam@192.0.2.1
```

Opens a SOCKS proxy on your port 1080. Point a browser at it
and your web traffic exits from the *server's* network —
encrypted between you and the server.

**When you'd use it:** safely using untrusted Wi-Fi, or
reaching sites the way the server's network sees them. It's a
poor man's VPN for web traffic — one port, one program at a
time.

## The direction cheat-sheet

| Flag | Who listens       | Traffic flows to          | Mnemonic              |
|------|-------------------|---------------------------|-----------------------|
| `-L` | Your machine      | Remote network            | **L**ocal listens     |
| `-R` | Remote machine    | Your machine              | **R**emote listens    |
| `-D` | Your machine      | Anywhere via the server   | **D**ynamic (SOCKS)   |

## In the config file

Tunnels can live in `~/.ssh/config` too:

```sshconfig
Host db-admin
    HostName 192.0.2.1
    User sam
    LocalForward 8080 localhost:80
```

`ssh db-admin` opens the terminal *and* the tunnel.

## The honest limits

- Tunnels forward **TCP only** — no UDP. (Some game traffic
  and DNS-over-UDP won't fit.)
- A tunnel is only as alive as the SSH connection carrying
  it. For tunnels that must survive, look at `autossh`, which
  watches the connection and restarts it.
- `-R` opens a port on the *server* — on a shared server,
  anyone there can connect to it. Bind it to the server's
  localhost (`-R 127.0.0.1:9090:localhost:3000`) unless you
  mean otherwise.

## Check

- With `-L 8080:localhost:80` active, `curl
  http://localhost:8080` returns the remote server's page.
- `ssh -O check` on a multiplexed connection shows the
  forwards alongside the master (chapter 8).
