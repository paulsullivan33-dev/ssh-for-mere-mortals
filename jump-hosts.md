# Jump Hosts

Some machines aren't reachable directly — they're on a private
network behind a server that *is* reachable. The reachable one
is the **jump host** (also called a bastion host): you hop
through it.

The old way was two manual logins: SSH to the jump host, then
SSH again from there. The modern way does it in one command.

## ProxyJump: one command, two hops

```console
$ ssh -J sam@jump.example.com sam@192.0.2.50
```

`-J` says "reach 192.0.2.50 *through* jump.example.com." You
authenticate to each hop — typically the jump host with your
key, then the inner machine — and end up with a terminal on
the inner machine. Your keystrokes travel through the whole
chain encrypted.

In the config file it reads even better:

```sshconfig
Host db
    HostName 192.0.2.50
    User sam
    ProxyJump sam@jump.example.com
```

Now `ssh db` just works. Multiple hops chain with commas:

```sshconfig
    ProxyJump hop1.example.com,hop2.example.com
```

## What the jump host sees

The jump host forwards your encrypted traffic but can't read
it — the encryption is end-to-end between *your* machine and
the final destination. It does learn *metadata*: which inner
machine you connected to, when, and for how long. That's
unavoidable and worth knowing.

## Jump host vs VPN vs Tailscale

All three solve "reach the private network," differently:

|               | Jump host              | VPN / tailnet              |
|---------------|------------------------|----------------------------|
| Granularity   | Per SSH connection     | Whole machine joins        |
| Setup         | One config line        | Software on every machine  |
| Other traffic | SSH only               | Everything                 |

If SSH is all you need, a jump host is the lighter tool. If you
want *every* program to reach the private network, see
[Tailscale for Mere Mortals](https://paulsullivan33-dev.github.io/tailscale-for-mere-mortals/).

## Check

- `ssh -J user@jump user@inner` drops you on the inner
  machine's prompt.
- `ssh -G db | grep proxyjump` confirms the config took
  effect — without needing the hosts to exist.
