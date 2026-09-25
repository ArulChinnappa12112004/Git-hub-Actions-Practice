

```yaml
name: My NPM package workflow

on: push

jobs:
  node-20-release:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/setup-node@v3
        with:
          node-version: 20

      - run: npm ci

      - run: npm test

      - run: npm publish ...
```

I'll explain it from top to bottom, then focus deeply on **Actions, `uses`, `with`, and `run`**.

---

# 1. What is this workflow trying to do?

The workflow is basically saying:

> "Whenever somebody pushes code to this repository, create an Ubuntu machine, set up Node.js 20, install the NPM dependencies, run tests, and publish the package."

The overall flow is:

```text
git push
   ↓
GitHub detects push event
   ↓
GitHub Actions starts workflow
   ↓
Ubuntu runner
   ↓
Set up Node.js 20
   ↓
npm ci
   ↓
npm test
   ↓
npm publish
```

---

# 2. `name`

```yaml
name: My NPM package workflow
```

This is simply the **name of the workflow**.

You'll see it in:

```text
GitHub
  ↓
Actions
  ↓
My NPM package workflow
```

It doesn't control the execution.

---

# 3. `on: push`

```yaml
on: push
```

This says:

> Start this workflow whenever a `push` event happens.

For example:

```powershell
git push
```

causes:

```text
git push
   ↓
GitHub
   ↓
push event
   ↓
Workflow starts
```

---

# 4. `jobs`

```yaml
jobs:
```

A workflow contains one or more jobs.

Here we have:

```yaml
jobs:
  node-20-release:
```

`node-20-release` is the **job ID**.

You can think of it as the name of this particular job.

---

# 5. `runs-on`

```yaml
runs-on: ubuntu-latest
```

This tells GitHub:

> Give this job an Ubuntu runner.

Conceptually:

```text
GitHub
   ↓
GitHub Actions
   ↓
Ubuntu machine
   ↓
Execute our job
```

---

# 6. `steps`

```yaml
steps:
```

A job contains steps.

Your job has four main steps:

```text
Step 1 → Set up Node.js 20
Step 2 → npm ci
Step 3 → npm test
Step 4 → npm publish
```

Now we reach the important part.

---

# 7. What is `uses`?

You have:

```yaml
- uses: actions/setup-node@v3
```

`uses` means:

> **Use a pre-built GitHub Action.**

Instead of writing all the commands necessary to install/configure Node.js yourself, you're telling GitHub:

> "Use this existing action that knows how to set up Node.js."

---

# 8. What is an Action?

A **GitHub Action** is a reusable piece of automation.

Someone has already created code that performs a particular task.

For example:

```text
Action
 ├── Set up Node.js
 ├── Checkout repository
 ├── Configure AWS
 ├── Upload artifacts
 ├── Create releases
 └── etc.
```

GitHub provides many commonly used Actions, and there are also community/third-party Actions.

For example:

```yaml
uses: actions/checkout@v4
```

means:

> Use the `checkout` Action provided by GitHub's `actions` organization.

And:

```yaml
uses: actions/setup-node@v4
```

means:

> Use the `setup-node` Action provided by GitHub's `actions` organization.

---

# 9. Understand this notation

Look at:

```yaml
uses: actions/setup-node@v3
```

Break it into:

```text
actions / setup-node @ v3
   │          │        │
   │          │        └── version/tag
   │          │
   │          └────────── Action name
   │
   └──────────────────── GitHub organization
```

So:

```text
actions
```

is the organization/repository owner,

```text
setup-node
```

is the Action,

and:

```text
@v3
```

specifies the version/tag being used.

---

# 10. What does `actions/setup-node` actually do?

It helps configure Node.js on the GitHub runner.

Without it, you would need to manually deal with things like:

```text
Which Node version?
Where should it come from?
How should it be installed?
How should it be added to PATH?
```

The Action handles that for you.

You simply say:

```yaml
uses: actions/setup-node@v3
```

---

# 11. Now what is `with`?

This is the next important concept.

You have:

```yaml
- uses: actions/setup-node@v3
  with:
    node-version: 20
```

`uses` says:

> **Which Action should I use?**

`with` says:

> **What configuration/options should I give to that Action?**

Think:

```text
uses
 ↓
Which tool/action?

with
 ↓
How should that action behave?
```

---

# 12. Your example

You have:

```yaml
uses: actions/setup-node@v3
```

You're saying:

> Use the Node setup Action.

Then:

```yaml
with:
  node-version: 20
```

You're saying:

> Configure that Action to install/use Node.js version 20.

So together:

```yaml
- uses: actions/setup-node@v3
  with:
    node-version: 20
```

means:

> **Use the `setup-node` Action, and configure it to use Node.js 20.**

---

# 13. Why can't we just write `node-version: 20`?

Because:

```yaml
node-version: 20
```

doesn't mean anything by itself to GitHub Actions.

GitHub needs to know:

> "Which Action should receive this configuration?"

That's why:

```yaml
uses:
```

comes first.

For example:

```yaml
uses: actions/setup-node@v3
with:
  node-version: 20
```

The relationship is:

```text
        Action
          ↓
actions/setup-node
          ↑
          |
       with:
          |
          ↓
 node-version: 20
```

---

# 14. Think of `uses` + `with` like installing software

Imagine you have an application called:

```text
Node Setup Tool
```

You tell it:

```text
Use Node Setup Tool
Version = 20
```

In GitHub Actions:

```yaml
uses: actions/setup-node@v3
with:
  node-version: 20
```

Same idea.

---

# 15. What is `run`?

Now look at:

```yaml
- run: npm ci
```

`run` means:

> **Execute a shell command on the runner.**

The Ubuntu runner executes:

```bash
npm ci
```

Similarly:

```yaml
- run: npm test
```

executes:

```bash
npm test
```

And:

```yaml
- run: npm publish
```

executes:

```bash
npm publish
```

---

# 16. So `uses` vs `run`

This is one of the most important things to remember.

### `uses`

```yaml
uses: actions/setup-node@v3
```

Means:

> **Use a pre-built GitHub Action.**

### `run`

```yaml
run: npm test
```

Means:

> **Run this shell command yourself.**

So:

```text
uses
 ↓
Use existing reusable automation

run
 ↓
Execute a command
```

---

# 17. Why don't we use shell scripts for everything?

You absolutely **can** use shell commands/scripts.

For example, instead of:

```yaml
- uses: actions/setup-node@v3
  with:
    node-version: 20
```

you could theoretically write your own commands to download/install Node.js.

But that would mean **you are responsible for implementing and maintaining all of that logic**.

For example:

```yaml
- run: |
    # download Node
    # extract Node
    # configure PATH
    # verify version
    # etc.
```

That's unnecessary when a maintained Action already exists.

---

# 18. Actions save you from reinventing things

Imagine you need to checkout your repository.

You could manually write Git commands:

```yaml
- run: git clone ...
```

But GitHub provides:

```yaml
- uses: actions/checkout@v4
```

This Action handles the checkout process for the workflow.

Similarly, Node:

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
```

Instead of writing your own Node installation logic.

---

# 19. Why use Actions instead of shell scripts?

There are several reasons.

### 1. Reusability

Someone has already implemented the common task.

```text
Need Node?
   ↓
setup-node Action
```

You don't need to build it yourself.

---

### 2. Less code

Instead of a large shell script:

```yaml
- run: |
    download Node
    extract Node
    configure PATH
    ...
```

you can write:

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
```

Much simpler.

---

### 3. Standardized

Many projects use standard Actions such as:

```yaml
actions/checkout
actions/setup-node
```

Developers recognize them immediately.

---

### 4. Configuration

Actions often provide useful options through `with`.

For example:

```yaml
uses: actions/setup-node@v4
with:
  node-version: 20
  cache: npm
```

Now the Action can also configure npm caching.

---

### 5. Complex tasks

Some automation is complicated to implement correctly using a few shell commands.

For example:

```text
AWS authentication
Docker login
Artifact upload
Code scanning
Cloud deployment
```

A reusable Action can package that complexity.

---

# 20. But should we use Actions for everything?

No.

Use `run` when you need to execute your project's commands.

For example:

```yaml
- run: npm ci
- run: npm test
- run: npm run build
```

These are your project's commands.

You don't necessarily need an Action for them.

Think:

```text
GitHub/platform setup
        ↓
      uses

Your application commands
        ↓
       run
```

That's not an absolute rule, but it's a very useful beginner mental model.

---

# 21. Your workflow explained visually

Your workflow:

```yaml
name: My NPM package workflow

on: push

jobs:
  node-20-release:
    runs-on: ubuntu-latest

    steps:

      - uses: actions/setup-node@v3
        with:
          node-version: 20

      - run: npm ci

      - run: npm test

      - run: npm publish
```

Think of it like this:

```text
                PUSH
                 ↓
        GitHub Actions
                 ↓
        Ubuntu Runner
                 ↓
       ┌─────────────────┐
       │ uses             │
       │ setup-node       │
       │                  │
       │ with             │
       │ node-version: 20 │
       └─────────────────┘
                 ↓
          Node.js 20 ready
                 ↓
             npm ci
                 ↓
          Dependencies
            installed
                 ↓
            npm test
                 ↓
             Tests
                 ↓
           npm publish
                 ↓
        Package published
```

---

# 22. One more important distinction

Don't confuse:

```text
GitHub Actions
```

with:

```text
an Action
```

### GitHub Actions

The **automation platform** provided by GitHub.

It gives you:

```text
Workflows
Jobs
Steps
Runners
Events
Actions
```

### An Action

A **reusable automation component** used inside a workflow.

For example:

```yaml
uses: actions/setup-node@v4
```

`setup-node` is an Action.

---

# 23. Simple analogy

Imagine you're cooking.

### GitHub Actions = kitchen

The kitchen provides the environment where you perform the work.

### `run` = you perform a task yourself

```text
run: npm test
```

Like:

> "I will cook this myself."

### `uses` = use a prepared tool

```text
uses: actions/setup-node@v4
```

Like:

> "Use this ready-made tool to do this particular task."

### `with` = configure the tool

```yaml
with:
  node-version: 20
```

Like:

> "Use this tool with this particular setting."

---

# 24. The three things you should remember

### `run`

```yaml
run: npm test
```

**Execute a command.**

---

### `uses`

```yaml
uses: actions/setup-node@v4
```

**Use a reusable GitHub Action.**

---

### `with`

```yaml
with:
  node-version: 20
```

**Provide configuration/input to the Action.**

So the relationship is:

```text
uses
 ↓
Which Action?
 ↓
actions/setup-node@v4
 ↓
with
 ↓
How should it be configured?
 ↓
node-version: 20
```

And then:

```text
run
 ↓
Execute my command
 ↓
npm ci
npm test
npm publish
```

---

## ⭐ Final mental model

When you see a GitHub Actions workflow, ask these questions:

```text
on:
 ↓
WHEN should it run?

jobs:
 ↓
WHAT major work should happen?

runs-on:
 ↓
WHERE should it run?

uses:
 ↓
WHICH reusable Action should I use?

with:
 ↓
HOW should I configure that Action?

run:
 ↓
WHICH shell command should I execute?
```

For your example:

```text
push
 ↓
Start workflow
 ↓
Ubuntu runner
 ↓
uses → setup-node Action
 ↓
with → Node 20
 ↓
run → npm ci
 ↓
run → npm test
 ↓
run → npm publish
```

That is the core difference between **`uses` + `with`** and **`run`** in GitHub Actions.
