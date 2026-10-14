# Moving Files

The encrypted pipe moves files as easily as keystrokes. Three
tools, three temperaments.

## `scp`: the quick copy

```console
$ scp report.pdf sam@192.0.2.1:/home/sam/docs/
$ scp sam@192.0.2.1:/home/sam/docs/report.pdf ./
$ scp -r myproject/ sam@192.0.2.1:/home/sam/
```

Local to remote, remote to local, whole directories with
`-r`. The syntax mirrors `cp`, with `user@host:` marking the
remote side.

(Modern `scp` — OpenSSH 9.0 and later — runs over the SFTP
protocol under the hood. Same command, safer machinery. If
you're on an older client talking to an ancient server, `-O`
selects the legacy protocol.)

## `sftp`: the interactive session

```console
$ sftp sam@192.0.2.1
Connected to 192.0.2.1.
sftp> ls
sftp> get report.pdf
sftp> put notes.txt /home/sam/docs/
sftp> bye
```

An FTP-style prompt over the SSH pipe: browse, `get`, `put`,
`ls`, `cd`. Best when you're moving several files and want
to look around first.

## `rsync`: the smart sync

```console
$ rsync -avz myproject/ sam@192.0.2.1:/home/sam/myproject/
```

`rsync` only transfers what changed, so the second run is
nearly instant. The flags: `-a` (archive: preserve
permissions, timestamps, recurse), `-v` (verbose), `-z`
(compress in flight).

Two details that bite everyone once:

- **The trailing slash matters.** `myproject/` copies the
  *contents* into the destination; `myproject` (no slash)
  copies the directory *itself*. When in doubt, use the
  slash and `--dry-run` first.
- **It runs over SSH by default** on modern systems (older
  ones need `-e ssh` spelled out).

For backups and deploys, `rsync` is the right tool.

## Which to reach for

| Tool    | Best when                                      |
|---------|------------------------------------------------|
| `scp`   | One file, one shot, done                       |
| `sftp`  | Browsing around and moving several files       |
| `rsync` | Repeated syncs, big trees, backups, deploys    |

## Check

- `scp file user@host:/tmp/` then `ssh user@host "ls -l
  /tmp/file"` → the file is there, same size.
- Run the `rsync` command twice → the second run transfers
  almost nothing ("speedup" in the summary).
