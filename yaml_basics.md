
# 1. What is YAML?

**YAML** stands for:

> **YAML Ain't Markup Language**

It is a human-readable format used to represent structured data and configuration.

For example:

```yaml
name: My Application
version: 1.0
language: Python
```

YAML is commonly used for:

* GitHub Actions
* Kubernetes
* Docker Compose
* Ansible
* CI/CD tools
* Configuration files

So YAML itself is **not a programming language**.

It is mainly a way of saying:

> "Here is my configuration and its structure."

---

# 2. Why does GitHub Actions use YAML?

GitHub Actions needs to know:

> **When should I run? What should I run? Where should I run it?**

For example:

```text
Developer pushes code
        ↓
GitHub detects push
        ↓
GitHub Actions starts
        ↓
Create a machine
        ↓
Download repository
        ↓
Install dependencies
        ↓
Run tests
        ↓
Build application
        ↓
Deploy
```

You describe this process in a YAML file.

The file is normally placed here:

```text
your-project/
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── src/
├── package.json
└── README.md
```

GitHub automatically looks inside:

```text
.github/workflows/
```

for workflow YAML files.

---

# 3. The most important GitHub Actions concepts

Before writing YAML, understand these terms:

```text
Workflow
   │
   ├── Trigger
   │
   ├── Job
   │    │
   │    ├── Runner
   │    │
   │    └── Steps
   │         ├── Action
   │         ├── Command
   │         └── Command
   │
   └── Job
```

These are the concepts you should learn first.

---

# 4. Workflow

A **workflow** is the complete automation process.

Example:

```yaml
name: CI Pipeline
```

A workflow might do:

```text
Build → Test → Docker Build → Deploy
```

One YAML file represents a workflow.

For example:

```text
.github/workflows/ci.yml
```

---

# 5. `name`

The `name` gives your workflow a readable name.

```yaml
name: CI Pipeline
```

GitHub will show:

```text
CI Pipeline
```

in the **Actions** tab.

It is just a name.

---

# 6. `on` — When should the workflow run?

This is one of the most important concepts.

```yaml
on:
  push:
    branches:
      - main
```

Meaning:

> Run this workflow whenever someone pushes code to the `main` branch.

For example:

```text
Developer
    │
    │ git push
    ▼
 GitHub
    │
    │ push event
    ▼
GitHub Actions
    │
    ▼
Workflow starts
```

You can also use:

```yaml
on:
  pull_request:
```

Meaning:

> Run when a pull request is created/updated.

Or:

```yaml
on:
  workflow_dispatch:
```

Meaning:

> Allow a user to manually start the workflow from GitHub.

---

# 7. Multiple triggers

You can have:

```yaml
on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  workflow_dispatch:
```

Now the workflow can run when:

```text
Push to main
      OR
Pull request to main
      OR
Manual execution
```

---

# 8. `jobs`

A workflow contains one or more **jobs**.

```yaml
jobs:
  build:
    ...
```

Think of a job as:

> **One major unit of work.**

For example:

```text
Workflow
│
├── build
│
├── test
│
└── deploy
```

You can have:

```yaml
jobs:
  build:
    ...

  test:
    ...

  deploy:
    ...
```

---

# 9. `runs-on`

Every job needs a machine on which it will execute.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

This means:

> Run the `build` job on a GitHub-hosted Ubuntu machine.

Other common runners include:

```yaml
runs-on: windows-latest
```

and:

```yaml
runs-on: macos-latest
```

So:

```text
Job
 │
 └── runs-on
       │
       ▼
 GitHub Runner
       │
       ▼
 Ubuntu / Windows / macOS
```

---

# 10. `steps`

A job contains **steps**.

Example:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Step 1
        run: echo "Hello"

      - name: Step 2
        run: echo "Building application"
```

Think of it as:

```text
Job
 │
 ├── Step 1
 │
 ├── Step 2
 │
 └── Step 3
```

Steps normally execute sequentially within a job.

---

# 11. `run`

`run` executes a shell command on the runner.

For example:

```yaml
steps:
  - name: Check Python version
    run: python --version
```

Or:

```yaml
- name: Install dependencies
  run: pip install -r requirements.txt
```

Or:

```yaml
- name: Run tests
  run: pytest
```

So:

```yaml
run: pytest
```

basically means:

> Open the runner's terminal and execute `pytest`.

---

# 12. `uses`

This is another extremely important concept.

`uses` allows you to use an existing **GitHub Action**.

For example:

```yaml
- name: Checkout repository
  uses: actions/checkout@v4
```

This means:

> Use the existing `actions/checkout` action, version 4.

Why do we need checkout?

When GitHub creates a runner, the runner doesn't automatically contain your repository files.

So:

```text
GitHub Repository
       │
       │ checkout
       ▼
GitHub Runner
       │
       ▼
Your source code available
```

---

# 13. `uses` vs `run`

This distinction is very important.

### `run`

You execute a command:

```yaml
- run: npm install
```

Meaning:

> Execute `npm install`.

### `uses`

You use an existing action:

```yaml
- uses: actions/checkout@v4
```

Meaning:

> Use the pre-built checkout action.

Think:

```text
run
 ↓
"Execute this command."


uses
 ↓
"Use this reusable action."
```

---

# 14. A complete simple workflow

Now let's combine everything:

```yaml
name: Node.js CI

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test
```

Let's understand the entire thing.

---

# 15. Read the YAML from top to bottom

### Step 1

```yaml
name: Node.js CI
```

Workflow name:

```text
Node.js CI
```

---

### Step 2

```yaml
on:
  push:
    branches:
      - main
```

Trigger:

> Start when code is pushed to `main`.

---

### Step 3

```yaml
jobs:
```

We're defining jobs.

---

### Step 4

```yaml
build:
```

Job name:

```text
build
```

---

### Step 5

```yaml
runs-on: ubuntu-latest
```

GitHub creates an Ubuntu runner.

---

### Step 6

```yaml
- name: Checkout repository
  uses: actions/checkout@v4
```

Download/check out the repository into the runner.

---

### Step 7

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: 20
```

Install/configure Node.js 20.

Notice something new:

```yaml
with:
```

`with` provides **inputs/configuration to an action**.

---

### Step 8

```yaml
- name: Install dependencies
  run: npm install
```

Run:

```bash
npm install
```

---

### Step 9

```yaml
- name: Run tests
  run: npm test
```

Run:

```bash
npm test
```

---

# 16. YAML indentation is VERY important

YAML uses indentation to understand hierarchy.

Correct:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

Here:

```text
jobs
 └── build
      └── runs-on
```

But this is wrong:

```yaml
jobs:
build:
runs-on: ubuntu-latest
```

YAML won't understand the intended structure.

Think of indentation like a tree:

```text
jobs:
│
└── build:
     │
     └── runs-on:
```

Usually use **2 spaces** for indentation.

Don't use random indentation.

---

# 17. YAML key-value structure

A basic YAML statement looks like:

```yaml
name: Arul
```

Meaning:

```text
key   = name
value = Arul
```

Another:

```yaml
runs-on: ubuntu-latest
```

means:

```text
key   = runs-on
value = ubuntu-latest
```

---

# 18. Lists in YAML

You will frequently see:

```yaml
branches:
  - main
  - develop
```

This means:

```text
branches
 ├── main
 └── develop
```

Another example:

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4

  - name: Test
    run: npm test
```

There are two items in the `steps` list.

---

# 19. `with`

`with` provides parameters to an action.

Example:

```yaml
- name: Setup Node
  uses: actions/setup-node@v4
  with:
    node-version: 20
```

Here:

```text
Action:
actions/setup-node@v4

Input:
node-version = 20
```

Another example:

```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 0
```

---

# 20. Environment variables — `env`

GitHub Actions allows environment variables.

For example:

```yaml
env:
  APP_NAME: BMW-SPAREHUB
```

Then:

```yaml
steps:
  - run: echo $APP_NAME
```

You can define them at different levels.

### Workflow level

```yaml
env:
  APP_NAME: BMW-SPAREHUB
```

Available throughout the workflow.

### Job level

```yaml
jobs:
  build:
    env:
      ENVIRONMENT: production
```

Available within that job.

### Step level

```yaml
- name: Build
  env:
    MODE: production
  run: echo $MODE
```

Available only to that step.

---

# 21. GitHub Secrets

This is extremely important for real projects.

Suppose you have:

```text
AWS_ACCESS_KEY
AWS_SECRET_KEY
DATABASE_PASSWORD
```

You should **not put them directly in YAML**.

Bad:

```yaml
env:
  PASSWORD: mypassword123
```

Instead, GitHub provides **Secrets**.

For example:

```yaml
env:
  PASSWORD: ${{ secrets.DB_PASSWORD }}
```

GitHub retrieves the secret securely.

The syntax:

```text
${{ ... }}
```

is GitHub Actions **expression syntax**.

---

# 22. Expressions — `${{ }}`

You'll see this everywhere in GitHub Actions.

Example:

```yaml
${{ github.ref }}
```

This means:

> Get the current GitHub reference/branch information.

Another:

```yaml
${{ secrets.DB_PASSWORD }}
```

means:

> Get the secret named `DB_PASSWORD`.

Another:

```yaml
${{ github.repository }}
```

means:

> Get the current repository name.

---

# 23. `needs` — Job dependencies

Suppose you have:

```text
Build
  ↓
Test
  ↓
Deploy
```

You can express that:

```yaml
jobs:
  build:
    ...

  test:
    needs: build
    ...

  deploy:
    needs: test
    ...
```

Meaning:

```text
build
  ↓
must finish successfully
  ↓
test
  ↓
must finish successfully
  ↓
deploy
```

Without `needs`, independent jobs can potentially run in parallel.

---

# 24. `if` — Conditional execution

You can conditionally run jobs or steps.

For example:

```yaml
- name: Deploy
  if: github.ref == 'refs/heads/main'
  run: ./deploy.sh
```

Meaning:

> Only run deployment if the current branch is `main`.

This is very useful in CI/CD.

---

# 25. Artifacts

Sometimes your workflow generates files:

```text
test-results/
build/
reports/
```

You may want to save them after the job finishes.

You can use:

```yaml
- name: Upload test results
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: test-results/
```

Conceptually:

```text
Runner
 │
 │ generates files
 ▼
test-results/
 │
 │ upload artifact
 ▼
GitHub Actions
 │
 ▼
Stored artifact
```

---

# 26. Matrix strategy

This is a more advanced but important concept.

Suppose you want to test your application on:

```text
Node 18
Node 20
Node 22
```

Instead of writing three jobs, you can use a matrix:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18, 20, 22]

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}

      - run: npm install
      - run: npm test
```

GitHub creates multiple job runs:

```text
             test
              │
      ┌───────┼────────┐
      ▼       ▼        ▼
   Node 18  Node 20  Node 22
```

---

# 27. `permissions`

For real-world GitHub Actions, you'll eventually encounter:

```yaml
permissions:
  contents: read
```

This controls what the workflow's GitHub token is allowed to do.

For example:

```yaml
permissions:
  contents: read
```

means the workflow gets read access to repository contents.

This becomes especially important when you start working with:

* deployments
* GitHub Packages
* pull requests
* releases
* GitHub API
* cloud deployments

---

# 28. A real CI/CD structure

Eventually, your workflow might look like:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:

  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: npm install

      - name: Build
        run: npm run build


  test:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: npm install

      - name: Test
        run: npm test


  deploy:
    needs: test
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Deploy
        run: ./deploy.sh
```

Conceptually:

```text
                 Git Push
                    │
                    ▼
              GitHub Actions
                    │
                    ▼
                 BUILD
                    │
                    │ success
                    ▼
                  TEST
                    │
                    │ success
                    ▼
                DEPLOY
```

That's the fundamental idea behind a CI/CD pipeline.

---

# 29. The YAML concepts you should learn

Don't try to memorize everything at once.

For your current GitHub Actions learning, learn these in this order:

### Level 1 — Must know

```text
YAML syntax
│
├── key/value
├── indentation
├── lists (-)
└── comments (#)
```

### Level 2 — GitHub Actions fundamentals

```text
Workflow
│
├── name
├── on
├── jobs
├── runs-on
└── steps
```

### Level 3 — Steps

```text
Step
│
├── name
├── run
├── uses
└── with
```

### Level 4 — Real CI/CD

```text
├── env
├── secrets
├── expressions ${{ }}
├── needs
├── if
└── artifacts
```

### Level 5 — Advanced

```text
├── matrix
├── permissions
├── reusable workflows
├── workflow_call
├── environments
├── concurrency
├── caching
└── self-hosted runners
```

---

# 30. The most important mental model

When you look at a GitHub Actions YAML file, read it like this:

```text
name
 ↓
"What is this automation called?"

on
 ↓
"When should it start?"

jobs
 ↓
"What work needs to happen?"

runs-on
 ↓
"Which machine should perform the work?"

steps
 ↓
"What should that machine do?"

uses
 ↓
"Use an existing action."

run
 ↓
"Execute this command."

with
 ↓
"Give configuration to this action."

env
 ↓
"Set environment variables."

secrets
 ↓
"Use sensitive values securely."

needs
 ↓
"Which job must finish first?"

if
 ↓
"Under what condition should this run?"
```

Once you understand this hierarchy, GitHub Actions YAML becomes much easier to read:

```text
WORKFLOW
│
├── name
│
├── on ─────────────── When?
│
└── jobs ───────────── What work?
      │
      ├── build
      │    │
      │    ├── runs-on ─── Where?
      │    │
      │    └── steps ───── How?
      │         ├── uses
      │         ├── run
      │         └── with
      │
      ├── test
      │
      └── deploy
           │
           └── needs: test
```

**If you understand `name → on → jobs → runs-on → steps → uses/run → with`, you already understand the core structure needed to start writing GitHub Actions workflows.**
