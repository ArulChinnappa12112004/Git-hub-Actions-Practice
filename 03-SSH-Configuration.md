

# GitHub SSH Setup — Step by Step

## First, understand what we're building

You currently have:

```text
Your Windows PC                         GitHub
┌──────────────────┐                 ┌──────────────┐
│                  │                 │              │
│ Private Key 🔐   │ ◄── SSH ──────► │ Public Key 🔓│
│                  │                 │              │
└──────────────────┘                 └──────────────┘
```

The basic idea is:

* **Private key** stays on your computer.
* **Public key** is added to GitHub.
* GitHub uses the public key to verify that your computer has the corresponding private key.
* Once verified, GitHub allows Git operations.

---

# Step 1 — Check whether SSH is installed

Open PowerShell:

```powershell
ssh -V
```

You should get something similar to:

```text
OpenSSH_for_Windows_9.x
```

If you see a version number, SSH is installed.

### Why?

Windows needs an SSH client to communicate with GitHub using SSH.

---

# Step 2 — Check whether you already have an SSH key

Run:

```powershell
ls ~/.ssh
```

You may see:

```text
id_ed25519
id_ed25519.pub
```

These are your two keys:

```text
id_ed25519
     ↓
Private key 🔐
NEVER SHARE


id_ed25519.pub
     ↓
Public key 🔓
Add this to GitHub
```

### Why two keys?

SSH uses **public-key cryptography**.

The private key proves:

> "I am the person/machine that owns this key."

The public key allows GitHub to verify that proof.

---

# Step 3 — Generate the SSH key

If you don't already have one, run:

```powershell
ssh-keygen -t ed25519 -C "arulchin2004@gmail.com"
```

You'll see:

```text
Enter file in which to save the key
(C:\Users\DELL/.ssh/id_ed25519):
```

### What should you type?

**Press Enter.**

Don't type:

```text
C:\Users\Arul\.ssh
```

because that's a folder, not the filename.

Accept the default:

```text
C:\Users\DELL\.ssh\id_ed25519
```

Then you'll see:

```text
Enter passphrase (empty for no passphrase):
```

You can either:

### Option A — No passphrase

Press:

```text
Enter
```

Then press Enter again.

### Option B — Use a passphrase

Type a passphrase and confirm it.

For learning purposes, you can use **Option A**.

---

# Step 4 — Understand what was created

After successful generation:

```text
C:\Users\DELL\.ssh\
│
├── id_ed25519
│
└── id_ed25519.pub
```

### `id_ed25519`

```text
🔐 PRIVATE KEY
```

Keep this on your computer.

**Never paste this into GitHub.**

Never send it to someone.

Never commit it to Git.

---

### `id_ed25519.pub`

```text
🔓 PUBLIC KEY
```

This is what we give to GitHub.

---

# Step 5 — Copy your public key

Run:

```powershell
Get-Content ~/.ssh/id_ed25519.pub | Set-Clipboard
```

This copies your public key to the Windows clipboard.

You can also see it using:

```powershell
Get-Content ~/.ssh/id_ed25519.pub
```

It will look approximately like:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI........ arulchin2004@gmail.com
```

It is normally **one long line**.

---

# Step 6 — Add the public key to GitHub

Go to GitHub.

Then:

```text
GitHub
   ↓
Profile picture
   ↓
Settings
   ↓
SSH and GPG keys
   ↓
New SSH key
```

You'll see something like:

```text
Title:
```

Enter something meaningful:

```text
DELL Windows Laptop
```

Then:

```text
Key type:
Authentication Key
```

And in the key field, paste:

```text
ssh-ed25519 AAAAC3...
```

Then click:

**Add SSH key**

---

# Step 7 — What did we just accomplish?

Now your computer has:

```text
Private key 🔐
```

And GitHub has:

```text
Public key 🔓
```

So:

```text
YOUR PC                         GITHUB
─────────                       ──────

Private key 🔐                  Public key 🔓
     │                               │
     │                               │
     └──────── SSH ──────────────────┘
                    │
                    ▼
              Authentication
                    │
                    ▼
                    ✅
```

---

# Step 8 — Test the SSH connection

Run:

```powershell
ssh -T git@github.com
```

The first time, you might get:

```text
The authenticity of host 'github.com' can't be established.

Are you sure you want to continue connecting
(yes/no/[fingerprint])?
```

Type:

```text
yes
```

You will then see something like:

```text
Warning: Permanently added 'github.com' (ED25519)
to the list of known hosts.
```

This is normal.

Then you should see:

```text
Hi ArulChinnappa4! You've successfully authenticated,
but GitHub does not provide shell access.
```

### This means:

🎉 **SSH is working.**

You already achieved this in your setup.

---

# Step 9 — What is `known_hosts`?

When you typed:

```text
yes
```

SSH saved GitHub's server identity in a file called:

```text
~/.ssh/known_hosts
```

So your `.ssh` folder might now contain:

```text
.ssh/
│
├── id_ed25519          🔐 Your private key
├── id_ed25519.pub      🔓 Your public key
└── known_hosts         🛡️ Known SSH servers
```

`known_hosts` basically tells your computer:

> "I've previously verified this server."

This helps SSH detect unexpected server identity changes.

---

# Step 10 — Understand `ssh-agent`

This is where your earlier confusion came from.

`ssh-agent` is **not the thing that creates your SSH key**.

Your key already exists:

```text
id_ed25519
```

The agent is an optional helper that can hold unlocked private keys in memory.

Think:

```text
SSH key
   │
   └── Actual authentication 🔐


ssh-agent
   │
   └── Helper that manages the key
```

If your key has a passphrase, `ssh-agent` can be convenient.

If you created your key **without a passphrase**, you generally don't need to worry about it for basic GitHub SSH usage.

And because you already successfully ran:

```powershell
ssh -T git@github.com
```

you don't need to troubleshoot `ssh-agent` just to make GitHub SSH work.

---

# Step 11 — Clone a repository using SSH

Now compare the two methods.

### HTTPS

```powershell
git clone https://github.com/ArulChinnappa4/GitHub-Actions-Practice.git
```

### SSH

```powershell
git clone git@github.com:ArulChinnappa4/GitHub-Actions-Practice.git
```

Notice the difference:

```text
HTTPS:
https://github.com/...

SSH:
git@github.com:...
```

With SSH, Git uses your SSH key to authenticate.

---

# Step 12 — If you already cloned the repository using HTTPS

Suppose you already have:

```text
G:\GitHub-Actions-Practice
```

Check the remote:

```powershell
git remote -v
```

You might see:

```text
origin  https://github.com/ArulChinnappa4/GitHub-Actions-Practice.git (fetch)
origin  https://github.com/ArulChinnappa4/GitHub-Actions-Practice.git (push)
```

That means your repository is still configured for HTTPS.

Change it to SSH:

```powershell
git remote set-url origin git@github.com:ArulChinnappa4/GitHub-Actions-Practice.git
```

Check again:

```powershell
git remote -v
```

Now:

```text
origin  git@github.com:ArulChinnappa4/GitHub-Actions-Practice.git (fetch)
origin  git@github.com:ArulChinnappa4/GitHub-Actions-Practice.git (push)
```

Now the repository uses SSH.

---

# Step 13 — Test Git operations

Try:

```powershell
git pull
```

and:

```powershell
git push
```

If the SSH configuration is correct, GitHub can authenticate your machine using your SSH key.

---

# Complete picture

This is the entire process:

```text
                 YOUR WINDOWS PC
                       │
                       │
                ssh-keygen
                       │
                       ▼
             ┌──────────────────┐
             │  SSH KEY PAIR    │
             └──────────────────┘
                 │          │
                 │          │
          Private key    Public key
             🔐             🔓
                 │          │
                 │          │
          STAY HERE     COPY TO GITHUB
                 │          │
                 │          ▼
                 │      GitHub Account
                 │          │
                 │          │
                 └──── SSH ──┘
                       │
                       ▼
                 Authentication
                       │
                       ▼
                    GitHub
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
           clone      pull      push
```

## The commands you actually need

For your Windows machine, the basic setup is:

```powershell
# 1. Generate key
ssh-keygen -t ed25519 -C "arulchin2004@gmail.com"

# 2. Copy public key
Get-Content ~/.ssh/id_ed25519.pub | Set-Clipboard

# 3. Add the copied key to GitHub
# GitHub → Settings → SSH and GPG keys → New SSH key

# 4. Test
ssh -T git@github.com

# 5. For an existing repository, change remote to SSH
git remote set-url origin git@github.com:ArulChinnappa4/GitHub-Actions-Practice.git

# 6. Verify
git remote -v

# 7. Use Git normally
git pull
git push
```

### And remember these three things

**1. Private key**

```text
id_ed25519
```

🔐 **Stays on your computer. Never share it.**

**2. Public key**

```text
id_ed25519.pub
```

🔓 **Add this to GitHub.**

**3. SSH agent**

```text
ssh-agent
```

🛠️ **Optional helper; it isn't the SSH authentication itself.**

You've already successfully completed the most important test:

```text
Hi ArulChinnappa4! You've successfully authenticated
```

So your SSH authentication is already working.
