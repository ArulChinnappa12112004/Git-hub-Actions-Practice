
The easiest way to understand it is:

> **GitHub Actions is an automation system built into GitHub that can automatically perform tasks when something happens in your repository.**

For example:

```text
Developer
   ↓
git push
   ↓
GitHub Repository
   ↓
GitHub Actions
   ↓
Build → Test → Scan → Package → Deploy
```

---

# 1. First: What problem does GitHub Actions solve?

Imagine you are developing an application.

You write code:

```text
Application
   ↓
Developer writes code
```

Then every time you make changes, you might manually do:

```bash
npm install
npm test
npm run build
```

Then perhaps:

```text
Run SonarQube
Build Docker image
Push Docker image
Deploy to Kubernetes
```

Imagine doing this manually every time.

It becomes:

```text
Developer
   ↓
Write code
   ↓
Run tests manually
   ↓
Build manually
   ↓
Run security scan manually
   ↓
Build Docker manually
   ↓
Push Docker manually
   ↓
Deploy manually
```

This is slow and error-prone.

### GitHub Actions automates this.

You can tell GitHub:

> "Whenever I push code, automatically test and build my application."

Then:

```text
Developer
   ↓
git push
   ↓
GitHub
   ↓
GitHub Actions
   ↓
npm install
   ↓
npm test
   ↓
npm build
```

No need for the developer to manually execute those commands every time.

---

# 2. What exactly is GitHub Actions?

GitHub Actions is a **CI/CD and workflow automation platform** provided by GitHub.

Let's break that down.

### CI

**CI = Continuous Integration**

It means automatically checking code whenever developers make changes.

For example:

```text
Developer A → push
Developer B → push
Developer C → push
          ↓
      GitHub Actions
          ↓
     Run automated tests
```

The goal is to find problems early.

---

### CD

CD commonly means **Continuous Delivery** or **Continuous Deployment**, depending on the workflow.

For example:

```text
Code
 ↓
Build
 ↓
Test
 ↓
Docker image
 ↓
Deploy
```

GitHub Actions can automate these processes.

---

# 3. A real-world example

Suppose you have a Node.js application.

Your repository:

```text
my-app/
│
├── src/
├── package.json
└── README.md
```

You push your code:

```bash
git push
```

GitHub Actions can automatically:

```text
1. Download your repository
2. Install Node.js
3. Install dependencies
4. Run tests
5. Build application
6. Run SonarQube
7. Build Docker image
8. Push image to Docker Hub
9. Deploy to Kubernetes
```

So instead of you manually doing everything:

```text
Developer
    ↓
git push
    ↓
Automation
    ↓
Build
    ↓
Test
    ↓
Scan
    ↓
Package
    ↓
Deploy
```

---

# 4. Where do we define GitHub Actions?

This brings us to:

```text
.github/workflows/
```

GitHub expects workflow files in this location.

For example:

```text
my-project/
│
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── deploy.yml
│
├── src/
├── package.json
└── README.md
```

The important path is:

```text
.github/workflows/
```

---

# 5. Why `.github`?

`.github` is a special directory commonly used by GitHub for repository-specific GitHub configuration.

For example:

```text
.github/
│
├── workflows/
├── ISSUE_TEMPLATE/
├── pull_request_template.md
└── ...
```

So `.github` basically means:

> "GitHub-related configuration for this repository."

---

# 6. Why `workflows`?

Inside `.github` we have:

```text
.github/workflows/
```

because we are defining **workflows**.

Think:

```text
.github
   ↓
GitHub configuration
   ↓
workflows
   ↓
Automation instructions
```

For example:

```text
.github/workflows/ci.yml
```

means:

> "Here is one GitHub Actions workflow."

---

# 7. What is a workflow?

A **workflow** is an automated process.

For example:

```text
CI Workflow

Push code
   ↓
Install dependencies
   ↓
Run tests
   ↓
Build application
```

Another workflow might be:

```text
Deployment Workflow

Push to main
   ↓
Build Docker image
   ↓
Push image
   ↓
Deploy to Kubernetes
```

You can have multiple workflows.

```text
.github/workflows/

ci.yml
deploy.yml
security.yml
```

---

# 8. Why do we use YAML?

GitHub Actions needs a way for you to describe:

* When should the workflow run?
* What should it do?
* What commands should it execute?
* Which operating system should it use?
* What dependencies should it install?
* What jobs should run?

GitHub chose **YAML** as the configuration format.

So you write:

```yaml
name: CI

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run tests
        run: npm test
```

This YAML describes the automation.

You can think of YAML as:

> **Instructions/configuration describing your workflow.**

---

# 9. YAML is not a programming language

This is an important distinction.

YAML is primarily a **data/configuration language**.

For example:

```yaml
name: CI

runs-on: ubuntu-latest
```

You are not writing a programming algorithm here.

You are configuring GitHub Actions.

The actual commands can be shell commands:

```yaml
run: npm test
```

or:

```yaml
run: |
  npm install
  npm test
  npm run build
```

So:

```text
YAML
 ↓
describes the workflow

Shell / Python / JavaScript etc.
 ↓
performs actual work
```

---

# 10. What is `.yml`?

You will often see:

```text
ci.yml
```

or:

```text
ci.yaml
```

Both are YAML files.

`.yml` and `.yaml` are two commonly used filename extensions for YAML.

There is **no fundamental difference in the YAML language**.

For example:

```text
ci.yml
```

and:

```text
ci.yaml
```

can contain exactly the same YAML.

---

# 11. Why do people use `.yml`?

Mostly convention.

You may see:

```text
.github/workflows/ci.yml
```

very frequently.

But:

```text
.github/workflows/ci.yaml
```

is also valid for GitHub Actions.

The important thing is that the file is placed under:

```text
.github/workflows/
```

---

# 12. Understand the complete file structure

Consider:

```text
my-project/
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── src/
├── package.json
└── README.md
```

Let's understand each part:

```text
my-project
```

Your Git repository.

```text
.github
```

GitHub-specific configuration.

```text
workflows
```

Directory containing GitHub Actions workflow definitions.

```text
ci.yml
```

The actual workflow configuration.

---

# 13. Now let's understand the YAML structure

Example:

```yaml
name: Node CI

on:
  push:
  pull_request:

jobs:
  test:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test
```

There are several important concepts here.

---

# 14. `name`

```yaml
name: Node CI
```

This is the workflow's name.

GitHub may show:

```text
Node CI
```

in the Actions UI.

It's basically a human-readable name.

---

# 15. `on`

```yaml
on:
  push:
  pull_request:
```

`on` tells GitHub:

> **When should this workflow run?**

For example:

```yaml
on:
  push:
```

means:

> Run when code is pushed.

Or:

```yaml
on:
  pull_request:
```

means:

> Run when a pull request event occurs.

You can have:

```yaml
on:
  push:
  pull_request:
```

meaning the workflow responds to both.

---

# 16. `jobs`

```yaml
jobs:
```

A workflow contains one or more **jobs**.

For example:

```yaml
jobs:

  test:
    ...

  build:
    ...
```

Think:

```text
Workflow
   │
   ├── Job 1
   │
   └── Job 2
```

---

# 17. What is a job?

A job is a collection of steps that execute on a runner.

For example:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Install
        run: npm install

      - name: Test
        run: npm test
```

Think:

```text
Job
 │
 ├── Step 1
 ├── Step 2
 └── Step 3
```

---

# 18. What is `runs-on`?

```yaml
runs-on: ubuntu-latest
```

This tells GitHub:

> **Run this job on an Ubuntu runner.**

The runner is the machine/environment where your commands execute.

For example:

```text
GitHub Actions Job
        ↓
Ubuntu runner
        ↓
npm install
        ↓
npm test
```

This brings us to your important question.

---

# 19. Does GitHub Actions have its own cloud?

### Yes — GitHub provides hosted runners.

When you use:

```yaml
runs-on: ubuntu-latest
```

GitHub can provide a **GitHub-hosted runner** for your job.

Conceptually:

```text
Your computer
     │
     │ git push
     ↓
GitHub
     │
     ↓
GitHub Actions
     │
     ↓
GitHub-hosted runner
     │
     ├── checkout code
     ├── npm install
     ├── npm test
     └── npm build
```

You don't need to create an Ubuntu VM yourself for the basic case.

---

# 20. Is GitHub Actions the same thing as AWS?

No.

GitHub Actions is an automation platform integrated with GitHub.

AWS is a cloud platform providing infrastructure and services.

For example:

```text
AWS
 ├── EC2
 ├── S3
 ├── RDS
 ├── Lambda
 └── EKS
```

GitHub Actions can **use AWS**.

For example:

```text
GitHub
   ↓
GitHub Actions
   ↓
Build application
   ↓
Deploy
   ↓
AWS
```

So GitHub Actions can act as the automation engine that interacts with AWS.

---

# 21. Where does the runner actually fit?

This is probably the most important architecture to understand.

```text
                 GitHub
                   │
                   │
          GitHub Actions
                   │
                   ↓
              Workflow
                   │
                   ↓
                Job
                   │
                   ↓
             Runner
                   │
          ┌────────┼────────┐
          ↓        ↓        ↓
       checkout   test     build
```

The **runner** is the environment that actually executes your commands.

---

# 22. Example

You write:

```yaml
- name: Run tests
  run: npm test
```

GitHub Actions doesn't magically execute `npm test` in the GitHub website.

Instead:

```text
Workflow
   ↓
Job
   ↓
Runner
   ↓
Shell
   ↓
npm test
```

The runner executes the command.

---

# 23. What is a GitHub-hosted runner?

GitHub can create/manage the runner environment for you.

For example:

```yaml
runs-on: ubuntu-latest
```

means approximately:

```text
GitHub
 ↓
Provide Ubuntu runner
 ↓
Start job
 ↓
Execute workflow
 ↓
Finish job
 ↓
Runner environment is typically discarded
```

This is very convenient for CI.

---

# 24. Can we use our own machine instead?

Yes.

GitHub Actions also supports **self-hosted runners**.

For example:

```text
GitHub
   ↓
GitHub Actions
   ↓
Your company's server
   ↓
Self-hosted runner
```

Your company might have:

```text
On-premise server
       ↓
Self-hosted GitHub Actions runner
```

This is useful when you need:

* internal network access
* special software
* private infrastructure
* custom hardware
* internal databases

---

# 25. GitHub-hosted vs self-hosted

### GitHub-hosted

```text
GitHub
   ↓
Provides runner
```

You don't manage the underlying runner machine.

### Self-hosted

```text
Your company
     ↓
Provides machine
     ↓
Installs GitHub runner
```

You are responsible for that machine.

---

# 26. What problem is GitHub Actions REALLY solving?

The bigger problem is:

> **Automating repetitive software-development processes.**

Without automation:

```text
Developer
   ↓
Write code
   ↓
Test manually
   ↓
Build manually
   ↓
Scan manually
   ↓
Package manually
   ↓
Deploy manually
```

With GitHub Actions:

```text
Developer
   ↓
git push
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Checkout
   ↓
Install
   ↓
Test
   ↓
Scan
   ↓
Build
   ↓
Deploy
```

The developer doesn't have to manually perform every step.

---

# 27. A real CI/CD example

Imagine your project:

```text
Node.js application
```

Your workflow:

```yaml
name: CI

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:

      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build
```

The flow is:

```text
Developer
    │
    │ git push
    ↓
GitHub repository
    │
    ↓
Workflow triggered
    │
    ↓
Job starts
    │
    ↓
Ubuntu runner
    │
    ├── Checkout code
    │
    ├── Install Node 20
    │
    ├── npm install
    │
    ├── npm test
    │
    └── npm run build
    │
    ↓
Success / Failure
```

---

# 28. What does `uses:` mean?

You will see:

```yaml
uses: actions/checkout@v4
```

This means:

> Use an existing GitHub Action.

Someone has already created reusable functionality for checking out code.

Instead of writing all the Git commands yourself, you use:

```yaml
uses: actions/checkout@v4
```

Similarly:

```yaml
uses: actions/setup-node@v4
```

sets up Node.js.

So there are two important ways to execute things:

### `run`

You write commands:

```yaml
run: npm test
```

### `uses`

You use an existing reusable action:

```yaml
uses: actions/checkout@v4
```

---

# 29. `run` vs `uses`

Remember it like this:

```text
run
 ↓
"Run this command"

uses
 ↓
"Use this pre-built action"
```

Example:

```yaml
- name: Checkout
  uses: actions/checkout@v4

- name: Test
  run: npm test
```

---

# 30. What happens when you push?

Let's put everything together.

You run:

```bash
git add .
git commit -m "Add login"
git push
```

Then:

```text
1. Git push
      ↓
2. GitHub receives commit
      ↓
3. GitHub sees workflow
      ↓
4. `on: push` matches
      ↓
5. Workflow starts
      ↓
6. Job starts
      ↓
7. Runner starts
      ↓
8. Steps execute
      ↓
9. Tests/build/scan/etc.
      ↓
10. Job succeeds or fails
```

That's GitHub Actions.

---

# 31. Why is the workflow stored inside Git?

This is another very important design idea.

You might wonder:

> "Why not configure everything in the GitHub website?"

Instead, GitHub Actions configuration is normally stored with your code:

```text
repository
   │
   ├── application code
   │
   └── .github/workflows/ci.yml
```

This means the automation itself is **version controlled**.

For example:

```text
Commit 1
   ↓
ci.yml version 1

Commit 2
   ↓
ci.yml version 2

Commit 3
   ↓
ci.yml version 3
```

You can review changes, create pull requests, and revert them.

This is extremely useful for teams.

---

# 32. Think of GitHub Actions as an employee

Here's a simple analogy.

Imagine your team has a robot employee called:

> **GitHub Actions**

You give it instructions:

```text
When code is pushed:

1. Get the code
2. Install Node
3. Install dependencies
4. Run tests
5. Build application
6. Deploy
```

You write those instructions in:

```text
.github/workflows/ci.yml
```

GitHub reads the instructions and provides a runner to execute them.

So:

```text
.github/workflows/ci.yml
          ↓
     Instructions
          ↓
   GitHub Actions
          ↓
       Runner
          ↓
    Execute tasks
```

---

# 33. Final architecture

This is the mental model I recommend remembering:

```text
                    GITHUB REPOSITORY
                           │
                           │ git push
                           ↓
                  ┌─────────────────┐
                  │ GitHub Actions  │
                  └────────┬────────┘
                           │
                    reads workflow
                           │
                           ↓
                .github/workflows/ci.yml
                           │
                           ↓
                         Job
                           │
                           ↓
                        Runner
                           │
             ┌─────────────┼─────────────┐
             ↓             ↓             ↓
          Checkout        Test          Build
             │             │             │
             └─────────────┼─────────────┘
                           ↓
                    Success / Failure
```

And if deployment is included:

```text
Developer
   │
   │ git push
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Runner
   ↓
Checkout
   ↓
Install dependencies
   ↓
Test
   ↓
SonarQube
   ↓
Docker build
   ↓
Docker push
   ↓
Deploy
   ↓
AWS / Kubernetes / Server
```

---

# 34. Quick revision table

| Concept                  | Meaning                                         |
| ------------------------ | ----------------------------------------------- |
| **GitHub Actions**       | GitHub's automation/CI/CD platform              |
| **Workflow**             | Automated process                               |
| **YAML**                 | Configuration language used to define workflows |
| **`.yml` / `.yaml`**     | YAML file extensions                            |
| **`.github`**            | GitHub-specific repository configuration        |
| **`workflows`**          | Directory containing workflow files             |
| **Job**                  | Group of steps                                  |
| **Step**                 | Individual task inside a job                    |
| **Runner**               | Machine/environment that executes the job       |
| **`run:`**               | Execute shell commands                          |
| **`uses:`**              | Use a reusable GitHub Action                    |
| **`on:`**                | Defines when workflow starts                    |
| **GitHub-hosted runner** | Runner provided/managed by GitHub               |
| **Self-hosted runner**   | Runner machine managed by your organization     |
| **CI**                   | Automatically build/test code changes           |
| **CD**                   | Automate delivery/deployment                    |

### The most important relationship

```text
.github/workflows/ci.yml
          ↓
      Workflow
          ↓
         Job
          ↓
        Steps
          ↓
       Runner
          ↓
    Actual commands
          ↓
      Result
```

**In one sentence:** GitHub Actions lets you store automation instructions as YAML inside your GitHub repository, trigger those instructions from events such as pushes or pull requests, and execute the jobs on GitHub-hosted or self-hosted runners.
