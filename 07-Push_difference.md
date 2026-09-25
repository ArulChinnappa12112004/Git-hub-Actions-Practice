

### 1. Short form

```yaml
on: push
```

### 2. Expanded form

```yaml
on:
  push:
```

Both mean:

> **Run this workflow when a `push` event happens.**

---

## Why does YAML allow both?

Because YAML supports different ways of writing the same structure.

Think of it like this:

```yaml
on: push
```

is the **short/simple form**.

Whereas:

```yaml
on:
  push:
```

is the **expanded form**, which gives you a place to add configuration under `push`.

---

# The important difference appears when you need options

Suppose you only want the workflow to run when someone pushes to the `main` branch.

You cannot simply do:

```yaml
on: push
```

because you need to provide configuration.

Instead:

```yaml
on:
  push:
    branches:
      - main
```

Now the structure is:

```text
on
└── push
    └── branches
        └── main
```

Meaning:

> When a push happens **to the main branch**, start the workflow.

---

## Compare these three

### Option 1 — Simple push

```yaml
on: push
```

Means:

```text
Any push
   ↓
Start workflow
```

---

### Option 2 — Expanded push

```yaml
on:
  push:
```

Means exactly the same:

```text
Any push
   ↓
Start workflow
```

There is no additional configuration.

---

### Option 3 — Push with configuration

```yaml
on:
  push:
    branches:
      - main
```

Means:

```text
Push
 ↓
Is it to main?
 ↓
Yes → Start workflow
No  → Don't start
```

---

# Why do tutorials usually use this?

You'll often see:

```yaml
on:
  push:
    branches:
      - main
```

instead of:

```yaml
on: push
```

because the expanded format allows you to configure the event.

For example:

```yaml
on:
  push:
    branches:
      - main
      - develop
```

Or:

```yaml
on:
  push:
    branches:
      - main
    paths:
      - "src/**"
```

Now you are saying:

> Run when there is a push to `main`, and the changed files are under `src/`.

---

# You can also have multiple events

This is another reason the expanded structure is useful:

```yaml
on:
  push:
  pull_request:
  workflow_dispatch:
```

This means:

```text
              ┌── push
              │
on ───────────┼── pull_request
              │
              └── workflow_dispatch
```

The workflow can start because of **any of these events**.

---

# Very simple analogy

Think about:

```yaml
on: push
```

as saying:

> **When a push happens, run me.**

And:

```yaml
on:
  push:
    branches:
      - main
```

as saying:

> **When a push happens, let me specify some conditions about that push.**

---

## ⭐ Remember this

| Syntax                        | Meaning                        |
| ----------------------------- | ------------------------------ |
| `on: push`                    | Run on any push                |
| `on:` → `push:`               | Same: run on any push          |
| `on:` → `push:` → `branches:` | Run on selected branches       |
| `on:` → `push:` → `paths:`    | Run when selected files change |

So **`on: push` and `on: push:` are not different events**. The second form is mainly useful because it gives you a structure where you can add **configuration/options** for the `push` event.
