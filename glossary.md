# Glossary

**Agent (SSH agent)**
: A background program that holds your unlocked keys and
  answers authentication challenges for you. See chapter 7.

**Agent forwarding**
: Letting a remote server use your local agent for further
  connections (`-A`). Convenient; risky on machines you don't
  fully trust.

**Authorized keys (`~/.ssh/authorized_keys`)**
: The list of public keys allowed to log in as a user, one
  per line, on the server.

**Bastion host**
: See *jump host*.

**ControlMaster / multiplexing**
: Sharing one SSH connection among several sessions, so
  later connections skip the handshake. See chapter 8.

**Daemon (`sshd`)**
: The SSH server program, running in the background on the
  remote machine, listening for connections.

**Fingerprint**
: A short hash identifying a key — for host keys ("is this
  the right server?") and user keys ("which key is this?").

**Forwarding**
: See *tunnel*.

**Host key**
: The server's identity key. Your client remembers its
  fingerprint in `known_hosts` and warns if it changes.

**Jump host**
: A reachable server you hop through to reach machines on a
  private network. See chapter 6.

**Key pair**
: Two mathematically linked files: the private key (stays
  with you, never shared) and the public key (installed on
  servers). See chapters 3–4.

**Known hosts (`~/.ssh/known_hosts`)**
: Your client's memory of server fingerprints, checked on
  every connection.

**Local / remote / dynamic forward**
: The three tunnel directions: `-L` (your port → remote
  network), `-R` (remote port → your machine), `-D` (SOCKS
  proxy through the server). See chapter 9.

**Multiplexing**
: See *ControlMaster*.

**Passphrase**
: A password protecting your *private key* on disk —
  distinct from the login password on the server.

**Private key**
: Your half of the key pair. Never leaves your machine,
  never shared, ideally passphrase-protected.

**ProxyJump**
: The `-J` option / config keyword for hopping through a
  jump host in one command.

**Public key**
: The shareable half of the key pair, installed on servers
  in `authorized_keys`.

**SOCKS proxy**
: A generic proxy protocol. `ssh -D` gives you one,
  routing program traffic through the server.

**StrictModes**
: The server-side check that refuses key login when
  `~/.ssh` permissions are too loose.

**Tunnel**
: Routing arbitrary TCP traffic through the SSH
  connection. See chapter 9.
