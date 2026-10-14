# Passwords vs Keys

Question 2 of every SSH connection: *are you allowed in?* There
are two ways to answer it. One is fine; the other is how the
pros do it.

## Passwords: simple, weak

Typing a password is the default. It works everywhere and needs
no setup. Its problems:

- **Humans pick guessable passwords.** Attackers know this and
  try thousands of common ones automatically.
- **It travels every time.** Each login sends proof of the
  secret again. (Encrypted by SSH, but still: every login is
  another chance to intercept.)
- **It can't be automated.** Scripts can't type passwords
  without storing them somewhere — which defeats the purpose.

Passwords are fine for your first connection. They shouldn't be
your last.

## Keys: a lock and a key

Key authentication uses a **pair** of mathematically linked
files:

- The **private key** stays on your machine, never leaves it.
  Guard it like a password.
- The **public key** goes on every server you want to reach.
  It's safe to share — it's called "public" because it reveals
  nothing useful to an attacker.

When you connect, the server sends a challenge that only the
private key can answer. Your secret never crosses the network at
all — not even encrypted. That's the big win over passwords.

```
your machine                          the server
─────────────                         ──────────
private key  ── proves identity ──▶  public key
   (never                                (says "this
    leaves)                               key may enter")
```

## The mental model

Think of it like a padlock:

- You make a padlock (the public key) and hand out copies to
  every server. Anyone can *have* the padlock.
- You keep the only key (the private key). Only it opens the
  padlocks.
- A thief who steals a padlock gets nothing — there's no key
  inside it.

## What changes in practice

|                    | Password            | Key                        |
|--------------------|---------------------|----------------------------|
| Setup              | None                | One-time per machine       |
| Typing at login    | Every time          | Never (or one passphrase)  |
| Phishable          | Yes                 | No                         |
| Script-friendly    | No                  | Yes                        |
| Revoking access    | Change the password | Delete one line server-side|

The next chapter makes your first key pair. It takes about a
minute.
