

## What is an SSH key?

An **SSH key** allows your computer to securely prove to GitHub:

> **“This computer is authorized to access this GitHub account/repository.”**

Instead of repeatedly giving your GitHub username and password, Git uses an **SSH key pair**.

There are two keys:

```text
Your computer
     │
     ├── 🔑 Private key
     │      (stays on your computer)
     │
     └── 🔓 Public key
            (added to GitHub)
```

The important rule is:

> **Private key = NEVER share it.**
> **Public key = safe to add to GitHub.**

---

# Why do we need it?

Imagine you have a GitHub repository:

```text
github.com/yourname/BMW-SPAREHUB
```

You want to clone it:

```bash
git clone git@github.com:yourname/BMW-SPAREHUB.git
```

GitHub needs to verify:

> "Is this computer actually authorized to access this repository?"

SSH provides that authentication.

The process is roughly:

```text
Your computer                         GitHub
     │                                  │
     │  "I want to access repository"   │
     │ ────────────────────────────────> │
     │                                  │
     │       Authentication challenge   │
     │ <────────────────────────────────│
     │                                  │
     │ Uses PRIVATE KEY                 │
     │ to prove identity                │
     │                                  │
     │ ────────────────────────────────> │
     │                                  │
     │        Authentication ✅         │
     │                                  │
```

GitHub has your **public key**, while your computer has the **private key**.

GitHub can verify that the computer possesses the corresponding private key without receiving the private key itself.

---

# 1. How do we create SSH keys?

You can generate them with:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

This creates something similar to:

```text
~/.ssh/
│
├── id_ed25519
└── id_ed25519.pub
```

### `id_ed25519`

This is your:

🔐 **Private key**

Do NOT upload it to GitHub or send it to anyone.

### `id_ed25519.pub`

This is your:

🔓 **Public key**

This is the one you add to GitHub.

---

# 2. What happens when you add the key to GitHub?

You copy your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

You'll see something like:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... your-email@example.com
```

Then add it to:

**GitHub → Settings → SSH and GPG keys → New SSH key**

Now GitHub basically has:

```text
GitHub
   │
   └── Public key 🔓
```

Your computer has:

```text
Computer
   │
   └── Private key 🔐
```

The two keys work together.

---

# 3. SSH vs HTTPS

You may have seen these two Git clone URLs.

### HTTPS

```bash
git clone https://github.com/user/project.git
```

### SSH

```bash
git clone git@github.com:user/project.git
```

Both can be used to work with GitHub.

The difference is primarily **how Git authenticates you**.

### HTTPS

```text
Git
 │
 ▼
GitHub
 │
 ▼
Authentication using HTTPS credentials/token
```

### SSH

```text
Git
 │
 ▼
SSH
 │
 ▼
Private key 🔐
 │
 ▼
GitHub
 │
 ▼
Public key 🔓
```

---

# 4. Why developers commonly use SSH

Suppose you're working every day.

You might repeatedly do:

```bash
git pull
git push
```

With SSH configured, Git can authenticate using your SSH key.

For example:

```bash
git push origin main
```

GitHub recognizes your SSH key and authenticates the computer.

You don't have to manually enter a GitHub credential every time.

---

# 5. Is the SSH key connected to your GitHub password?

No.

This is an important distinction.

Your GitHub account has things like:

```text
GitHub account
│
├── Username
├── Password
├── Email
├── SSH keys
├── Personal access tokens
└── Repositories
```

The SSH key is **another authentication mechanism**.

It doesn't replace your GitHub password for logging into the website.

Instead, it is primarily used when Git communicates with GitHub over SSH.

---

# 6. Why is the private key kept on your computer?

Suppose you give someone your private key:

```text
id_ed25519
```

That person could potentially authenticate as your computer/key.

Therefore:

### Public key

```text
id_ed25519.pub
```

✅ Can be uploaded to GitHub.

### Private key

```text
id_ed25519
```

❌ Never share it.

Think of it like:

```text
Public key = lock 🔒
Private key = key 🔑
```

You can give someone the **lock**, but you don't give them the **key**.

---

# 7. One computer can have multiple SSH keys

For example:

```text
Your laptop
│
├── GitHub personal account
│      └── SSH key A
│
└── GitHub company account
       └── SSH key B
```

This is useful when you have:

```text
Personal GitHub
        +
Company GitHub
```

You can configure SSH to use different keys for different accounts.

---

# 8. How do you test whether SSH works?

After adding your public key to GitHub:

```bash
ssh -T git@github.com
```

If authentication succeeds, GitHub will respond with a message indicating that you've authenticated successfully.

Then you can clone using:

```bash
git clone git@github.com:username/repository.git
```

and later:

```bash
git add .
git commit -m "Added feature"
git push origin main
```

---

# The whole concept in one picture

```text
                 YOUR COMPUTER
              ┌─────────────────┐
              │                 │
              │ Private Key 🔐  │
              │                 │
              └────────┬────────┘
                       │
                       │ SSH
                       ▼
              ┌─────────────────┐
              │     GitHub      │
              │                 │
              │ Public Key 🔓   │
              │                 │
              └────────┬────────┘
                       │
                       ▼
                Authentication
                       │
                       ▼
              Repository Access
                       │
                       ▼
              git pull / push
```


The important point is:

> **SSH is not required because HTTPS cannot authenticate you. HTTPS can authenticate you perfectly well. SSH is an alternative authentication method.**

Let's compare them practically.

### HTTPS

When you use:

```bash
git clone https://github.com/username/project.git
```

Git communicates with GitHub using **HTTPS**.

For operations that require authentication, GitHub can authenticate you using credentials such as a **Personal Access Token (PAT)**, often stored by a credential manager so you don't have to enter it repeatedly.

```text
Your computer
      │
      │ HTTPS
      ▼
   GitHub
      │
      │ "Who are you?"
      ▼
Credential / Token
      │
      ▼
Authentication ✅
```

So yes — **HTTPS already solves authentication.**

---

## Then why does SSH exist?

Because SSH provides **another way to authenticate**.

With SSH:

```bash
git clone git@github.com:username/project.git
```

Git uses your SSH private key:

```text
Your computer
     │
     │ SSH
     ▼
   GitHub
     │
     │ Verify SSH key
     ▼
Authentication ✅
```

So think of it as:

```text
                 GitHub
                   ▲
                   │
        ┌──────────┴──────────┐
        │                     │
      HTTPS                  SSH
        │                     │
   Token/credential       SSH key
        │                     │
        └──────────┬──────────┘
                   │
             Authentication
```

**Both accomplish the same broad goal: proving you have access.**

---

# So what's the practical difference?

Imagine you're a developer working on your project every day.

### With HTTPS

You use:

```bash
git clone https://github.com/arul/project.git
```

Then:

```bash
git pull
git push
```

Git needs authenticated access for private repositories and write operations. Modern GitHub authentication uses a token/credential mechanism rather than your GitHub account password.

Your credential manager can remember the credential, so you may not notice the authentication happening.

---

### With SSH

You configure your SSH key once:

```bash
ssh-keygen -t ed25519
```

Add the public key to GitHub.

Then:

```bash
git clone git@github.com:arul/project.git
```

After that, your SSH key can authenticate your Git operations.

---

# The biggest conceptual difference

Don't think:

> ❌ "HTTPS = authentication, SSH = authentication, so why have two?"

Think:

> ✅ **HTTPS and SSH are two different communication/authentication mechanisms that GitHub supports.**

It's similar to having two doors into the same building:

```text
                 COMPANY BUILDING
                       │
              ┌────────┴────────┐
              │                 │
          Door A             Door B
          HTTPS               SSH
              │                 │
        Credential/Token    SSH Key
              │                 │
              └────────┬────────┘
                       │
                    Access
```

You don't need to use both.

---

# Why do companies often use SSH?

This becomes particularly useful in **corporate development and DevOps**, which you'll encounter with things like GitHub Actions, servers, Kubernetes, AWS, etc.

SSH keys are convenient for **machine-to-machine authentication**.

For example:

```text
Developer laptop
       │
       │ SSH key
       ▼
GitHub
```

or:

```text
CI/CD server
       │
       │ SSH key
       ▼
GitHub
```

or:

```text
Your computer
       │
       │ SSH
       ▼
Linux server
```

There doesn't have to be a human typing a password.

---

# One more important distinction

SSH is **not necessarily "more secure than HTTPS" in a simple sense**.

Both can be securely configured.

The real difference is the authentication mechanism:

|                       | HTTPS            | SSH                  |
| --------------------- | ---------------- | -------------------- |
| Protocol              | HTTPS            | SSH                  |
| Authentication        | Token/credential | SSH key              |
| Need SSH key?         | ❌ No             | ✅ Yes                |
| GitHub supports it?   | ✅                | ✅                    |
| Clone                 | `https://...`    | `git@github.com:...` |
| Push/Pull             | ✅                | ✅                    |
| Common for developers | ✅                | ✅                    |
| Good for automation   | ✅                | ✅                    |

---

## So for you, which should you use?

If you're learning Git/GitHub, **you don't need to create an SSH key just because someone says you should.**

You can absolutely do:

```bash
git clone https://github.com/username/project.git
```

and continue using HTTPS.

If your company/project documentation says:

```bash
git clone git@github.com:company/project.git
```

then you'll generally need SSH configured.

### The simplest mental model

```text
HTTPS
   ↓
"Authenticate me using a token/credential."

SSH
   ↓
"Authenticate me using my SSH key."

Both
   ↓
"Give me access to GitHub."
```
