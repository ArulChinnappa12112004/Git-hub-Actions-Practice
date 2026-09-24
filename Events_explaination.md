

> **An event is something that happens that tells GitHub Actions: "Now is the time to start this workflow."**

For example:

```text
Developer pushes code
        ↓
     Event
        ↓
Workflow starts
        ↓
Job
        ↓
Steps
```

The event is therefore the **trigger** for the workflow.

---

# 1. Where do we define an event?

Inside the workflow YAML file, we use:

```yaml
on:
```

For example:

```yaml
name: My CI

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Running tests"
```

Here:

```yaml
on:
  push:
```

means:

> **Whenever a push event happens, start this workflow.**

---

# 2. The basic GitHub Actions flow

Keep this picture in your mind:

```text
              SOMETHING HAPPENS
                     │
                     ↓
                  EVENT
                     │
                     ↓
              GitHub checks
                workflow
                     │
                     ↓
             Workflow starts
                     │
                     ↓
                   Job
                     │
                     ↓
                  Steps
```

For example:

```text
git push
   ↓
push event
   ↓
GitHub Actions workflow
   ↓
Job
   ↓
npm test
```

---

# 3. Three important categories of events

The categories you mentioned are very useful for learning:

```text
Events
   │
   ├── 1. Repository events
   │
   ├── 2. Manual triggers
   │
   └── 3. Scheduled events
```

Let's understand each one.

---

# 4. Repository events

A **repository event** happens because something happens to your GitHub repository.

Examples:

```text
push
pull_request
issues
release
workflow_run
```

The most common ones you'll use initially are:

```text
push
pull_request
```

---

# 5. `push` event

A `push` event happens when code is pushed to the repository.

For example:

```bash
git add .
git commit -m "Add login"
git push
```

This causes:

```text
git push
   ↓
GitHub repository
   ↓
push event
   ↓
GitHub Actions
   ↓
workflow starts
```

Example:

```yaml
name: CI

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Test
        run: echo "Running tests"
```

Every time a push occurs, the workflow can run.

---

# 6. `push` on a specific branch

You usually don't want the workflow to run for every branch.

For example:

```yaml
on:
  push:
    branches:
      - main
```

Now:

```text
push to main
     ↓
Workflow runs ✅
```

But:

```text
push to feature/login
     ↓
Workflow does not run ❌
```

So you can control **which pushes trigger the workflow**.

---

# 7. Real example

Suppose your repository has:

```text
main
feature/login
feature/payment
```

You configure:

```yaml
on:
  push:
    branches:
      - main
```

Then:

```text
Developer
   │
   ├── push → feature/login
   │              ↓
   │           No trigger
   │
   └── push → main
                  ↓
             push event
                  ↓
             CI workflow
```

This is useful when you want certain automation only after code reaches `main`.

---

# 8. `pull_request` event

Another very important repository event is:

```yaml
on:
  pull_request:
```

A pull request is created when someone proposes merging changes into another branch.

For example:

```text
feature/login
       │
       │ Pull Request
       ↓
     main
```

GitHub can trigger your workflow.

```yaml
name: Pull Request CI

on:
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Testing pull request"
```

Flow:

```text
Developer
   ↓
Create Pull Request
   ↓
pull_request event
   ↓
GitHub Actions
   ↓
Run tests
```

This is extremely useful for checking code **before merging it**.

---

# 9. `push` vs `pull_request`

This distinction is important.

### `push`

```text
Code is pushed
      ↓
push event
```

### `pull_request`

```text
Pull Request activity
      ↓
pull_request event
```

You can use both:

```yaml
on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main
```

Meaning:

> Run the workflow when code is pushed to `main` OR when a pull request targets `main`.

---

# 10. Other repository events

GitHub supports many events.

For example:

```yaml
on:
  issues:
```

runs when certain issue activity occurs.

Another:

```yaml
on:
  release:
```

can trigger when a release-related event occurs.

Another:

```yaml
on:
  workflow_run:
```

can trigger a workflow based on the completion of another workflow.

You don't need to memorize all events initially.

Start with:

```text
push
pull_request
```

They are fundamental to CI/CD.

---

# 11. Second category: Manual trigger

Sometimes you don't want GitHub Actions to wait for:

```text
push
```

or:

```text
pull_request
```

You may want a person to say:

> **"Run this workflow now."**

That's a **manual trigger**.

GitHub provides:

```yaml
workflow_dispatch:
```

Example:

```yaml
name: Manual Deployment

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy
        run: echo "Deploying application"
```

Now the workflow can be manually started from GitHub.

---

# 12. Why is manual triggering useful?

Imagine your application is already built.

You don't necessarily want:

```text
Every git push
       ↓
Deploy production
```

That could be dangerous.

Instead:

```text
Code
 ↓
Test
 ↓
Build
 ↓
Someone decides:
"Deploy now"
 ↓
Manual trigger
 ↓
Production deployment
```

So manual triggers provide **human control**.

---

# 13. Example: Manual production deployment

Imagine:

```yaml
name: Deploy Production

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy
        run: echo "Deploying to production"
```

A developer can go to GitHub:

```text
Repository
   ↓
Actions
   ↓
Deploy Production
   ↓
Run workflow
```

Then GitHub starts the workflow.

Conceptually:

```text
Human
  ↓
"Run workflow"
  ↓
workflow_dispatch event
  ↓
Job
  ↓
Deployment
```

---

# 14. Manual trigger can accept inputs

You can even ask the person what environment to deploy to.

Example:

```yaml
name: Deploy

on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Environment"
        required: true
        type: choice
        options:
          - staging
          - production

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy
        run: echo "Deploying to selected environment"
```

Now the person can select:

```text
Environment:

○ staging
○ production
```

This makes manual workflows much more flexible.

---

# 15. Third category: Scheduled events

Sometimes you don't want an event caused by a developer.

You want:

> **"Run this workflow automatically at a particular time."**

For this we use:

```yaml
schedule:
```

It uses a **cron expression**.

Example:

```yaml
name: Daily Job

on:
  schedule:
    - cron: "0 0 * * *"

jobs:
  daily:
    runs-on: ubuntu-latest

    steps:
      - name: Daily task
        run: echo "Running daily task"
```

This tells GitHub to run the workflow according to the schedule.

---

# 16. What is cron?

Cron is a standard way of expressing a schedule.

This:

```text
0 0 * * *
```

means:

```text
minute  hour  day  month  weekday
  0      0     *     *       *
```

So conceptually:

```text
Every day
at 00:00 UTC
```

Important:

> GitHub Actions schedules use **UTC**, so convert the time if you're thinking in IST.

---

# 17. Example: Every day

```yaml
on:
  schedule:
    - cron: "0 0 * * *"
```

Conceptually:

```text
Monday    00:00 UTC → Run
Tuesday   00:00 UTC → Run
Wednesday 00:00 UTC → Run
Thursday  00:00 UTC → Run
...
```

This is useful for tasks that don't depend on a developer pushing code.

---

# 18. What can scheduled workflows be used for?

For example:

### Daily dependency check

```text
Every night
    ↓
Check dependencies
    ↓
Run security scan
```

### Database backup

```text
Every day
    ↓
Run backup script
```

### Report generation

```text
Every Monday
    ↓
Generate report
```

### Cleanup

```text
Every night
    ↓
Clean old artifacts
```

---

# 19. One important limitation of schedule

A scheduled workflow is not necessarily an exact real-time scheduler.

GitHub notes that scheduled workflows can be delayed during periods of high load, particularly around the start of an hour.

So don't think:

```text
00:00:00 exactly
```

is guaranteed.

For highly precise scheduling requirements, dedicated scheduling infrastructure may be more appropriate.

---

# 20. Combining events

You can have multiple triggers in one workflow.

For example:

```yaml
name: CI

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  workflow_dispatch:

  schedule:
    - cron: "0 0 * * *"
```

Now the workflow can start because of **four different events**:

```text
                   CI Workflow
                        ↑
        ┌───────────────┼───────────────┐
        │               │               │
      push        pull_request    workflow_dispatch
        │               │               │
        └───────────────┼───────────────┘
                        │
                     schedule
```

Any configured event can trigger the workflow.

---

# 21. A real company-style example

Imagine your repository is:

```text
payment-service
```

You might want:

### When developer pushes to feature branch

```text
push
 ↓
Run tests
```

### When Pull Request is created

```text
pull_request
 ↓
Run tests
 ↓
Code quality scan
```

### When someone wants to deploy

```text
workflow_dispatch
 ↓
Deploy
```

### Every night

```text
schedule
 ↓
Security/dependency scan
```

One repository can therefore have workflows designed around different events.

---

# 22. Think of events like doorbells

A simple analogy:

Your workflow is a machine:

```text
Workflow
   ↓
Build
Test
Deploy
```

But the machine needs a signal telling it:

> "Start!"

Different events are different doorbells:

```text
push
   ↓
🔔 "Code changed!"

pull_request
   ↓
🔔 "Someone opened/updated a PR!"

workflow_dispatch
   ↓
🔔 "A human clicked Run workflow!"

schedule
   ↓
🔔 "The scheduled time arrived!"
```

The workflow then starts.

---

# 23. Very important: Event vs Job vs Step

Don't mix these three.

### Event

**When should the workflow start?**

```yaml
on:
  push:
```

### Job

**What major unit of work should happen?**

```yaml
jobs:
  test:
```

### Step

**What individual commands/actions should the job perform?**

```yaml
steps:
  - name: Install
    run: npm install

  - name: Test
    run: npm test
```

So:

```text
EVENT
  ↓
starts
  ↓
WORKFLOW
  ↓
contains
  ↓
JOB
  ↓
contains
  ↓
STEPS
```

---

# 24. Complete example

```yaml
name: Application CI

on:

  # Repository event
  push:
    branches:
      - main

  # Repository event
  pull_request:
    branches:
      - main

  # Manual event
  workflow_dispatch:

  # Scheduled event
  schedule:
    - cron: "0 0 * * *"

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

This means:

```text
                    Application CI
                          ↑
          ┌───────────────┼────────────────┐
          │               │                │
        push        pull_request    workflow_dispatch
          │               │                │
          └───────────────┼────────────────┘
                          │
                       schedule
                          │
                          ↓
                         Job
                          ↓
                        Steps
                     ┌────┼────┐
                     ↓    ↓    ↓
                 Checkout Install Test
```

---

# 25. Quick comparison

| Event               | What causes it?                  | Typical use                  |
| ------------------- | -------------------------------- | ---------------------------- |
| `push`              | Code pushed                      | Build/test                   |
| `pull_request`      | PR activity                      | Test/validate code           |
| `workflow_dispatch` | Human manually starts            | Deployment/manual operations |
| `schedule`          | Time reaches configured schedule | Nightly jobs/reports/scans   |

---

# 26. The most important thing to remember

When you look at:

```yaml
on:
```

read it as:

> **"WHEN should GitHub start this workflow?"**

Then:

```yaml
jobs:
```

read it as:

> **"WHAT work should GitHub perform?"**

Then:

```yaml
steps:
```

read it as:

> **"WHAT individual actions/commands should the job execute?"**

So the complete mental model is:

```text
                WHEN?
                  ↓
                EVENT
                  ↓
             WORKFLOW
                  ↓
                WHAT?
                  ↓
                 JOB
                  ↓
                HOW?
                  ↓
                STEPS
                  ↓
               RUNNER
                  ↓
             Actual work
```

That's the foundation of GitHub Actions.
