

> **LTS → versions → version conflicts → NVM → `.nvmrc` → ASDF → plugins → production usage**

---

# 1. First: What does LTS mean?

**LTS = Long-Term Support**

When a software project releases versions, some versions are supported for a longer period.

For example, imagine Node.js has:

```text
Node 20
Node 21
Node 22
Node 23
Node 24
```

They are not necessarily all supported in the same way.

An **LTS version** is a version that the project promises to maintain for a longer period with things like:

* security fixes
* important bug fixes
* stability
* compatibility updates

So:

```text
Latest version
      ↓
May contain newest features
      ↓
May change more frequently

LTS version
      ↓
Long-term maintenance
      ↓
Preferred for many production applications
```

### Why do projects care about LTS?

Suppose your company has an application:

```text
My Banking Application
        ↓
Node.js 22
        ↓
Express
        ↓
PostgreSQL
```

The application might run perfectly today.

You don't want someone to suddenly upgrade Node.js tomorrow and discover:

```text
Application
    ↓
Node 24
    ↓
Some dependency breaks
    ↓
Application doesn't work
```

Therefore companies often choose a supported, stable version and keep it for a planned period.

### Important distinction

**LTS does NOT mean "the newest version."**

It means:

> "This particular version has a longer support lifecycle."

---

# 2. Why does a project need a specific version?

This is the problem that leads us to NVM.

Imagine you work on two projects.

### Project A

```text
Project A
Node.js 20
```

### Project B

```text
Project B
Node.js 24
```

Your computer cannot simply assume:

```text
node = whatever version I installed
```

because Project A may depend on Node 20 while Project B requires Node 24.

You might have:

```text
Project A
package.json
    ↓
requires Node 20

Project B
package.json
    ↓
requires Node 24
```

Now you have a **version management problem**.

---

# 3. What happens if you don't manage versions?

Suppose you install Node.js directly.

```text
Windows
   ↓
Node.js 24
```

You start Project A:

```text
Project A
requires Node 20
```

But your machine has:

```text
Node 24
```

You may get:

```text
unsupported engine
dependency problems
build failures
runtime problems
```

Then someone tells you:

> "Hey, our project uses Node 20."

You install Node 20.

Now Project A works.

But Project B requires Node 24.

So you install Node 24.

Then another project requires Node 18.

Now you're thinking:

> "How am I supposed to keep switching Node versions?"

That's where **NVM** comes in.

---

# 4. What is NVM?

**NVM = Node Version Manager**

The important word is:

> **Version Manager**

NVM allows you to install and switch between multiple Node.js versions on the same machine.

For example:

```text
NVM
 │
 ├── Node 18
 ├── Node 20
 ├── Node 22
 └── Node 24
```

You can switch between them.

For example:

```bash
nvm use 20
```

Now:

```bash
node --version
```

might show:

```text
v20.x.x
```

Then:

```bash
nvm use 24
```

Now:

```bash
node --version
```

shows:

```text
v24.x.x
```

So NVM is basically giving you:

> **One computer → multiple Node.js installations → easy switching**

---

# 5. Why was NVM created?

Think about a developer's machine.

Without NVM:

```text
Computer
   │
   └── Node 24
```

With NVM:

```text
Computer
   │
   └── NVM
        │
        ├── Node 18
        ├── Node 20
        ├── Node 22
        └── Node 24
```

NVM manages those Node installations.

So instead of manually uninstalling:

```text
Node 24
```

and installing:

```text
Node 20
```

you can simply say:

```bash
nvm use 20
```

---

# 6. Is NVM used in production?

This is an important question.

### Usually:

**NVM is primarily a developer-machine/version-management tool.**

For example:

```text
Developer laptop
       ↓
      NVM
       ↓
Node 20
```

It helps developers reproduce the project's required Node version.

But production servers generally don't need developers manually doing:

```bash
nvm use 20
```

every time.

Production usually uses something more controlled.

For example:

```text
CI/CD
   ↓
Docker image
   ↓
Node.js 20
   ↓
Application
```

or:

```text
Kubernetes
    ↓
Container image
    ↓
node:20
    ↓
Application
```

So don't think:

> "NVM is a production deployment tool."

Think:

> **NVM manages Node versions primarily on development machines and sometimes in build/server environments, while production commonly pins the runtime through containers, images, or managed infrastructure.**

---

# 7. Then why does a production team care about NVM?

Because development and production must agree about the Node version.

Imagine:

```text
Developer laptop
Node 20

CI server
Node 20

Production
Node 20
```

Everything is consistent.

But imagine:

```text
Developer
Node 20

CI
Node 22

Production
Node 24
```

Now you have:

```text
"It works on my machine."
```

This is exactly the type of problem version management tries to prevent.

---

# 8. Now comes `.nvmrc`

Suppose your project requires:

```text
Node 20
```

You could tell every developer:

> "Before working on this project, run `nvm use 20`."

But humans forget.

So the project can contain a file:

```text
.nvmrc
```

Inside:

```text
20
```

Now the project structure could look like:

```text
my-project/
│
├── package.json
├── package-lock.json
├── src/
├── README.md
└── .nvmrc
```

`.nvmrc` says:

> **This project expects this Node.js version.**

---

# 9. How `.nvmrc` works

Suppose:

```text
.nvmrc

20
```

You enter:

```bash
cd my-project
```

Then:

```bash
nvm use
```

NVM reads:

```text
.nvmrc
   ↓
20
   ↓
select Node 20
```

Then:

```bash
node --version
```

You get something like:

```text
v20.x.x
```

That's the whole purpose.

---

# 10. `.nvmrc` does NOT install Node automatically

This is a very common misunderstanding.

Suppose `.nvmrc` contains:

```text
20
```

but Node 20 isn't installed.

Running:

```bash
nvm use
```

may tell you that the requested version isn't installed.

Then you need:

```bash
nvm install 20
```

and then:

```bash
nvm use 20
```

So:

```text
.nvmrc
   ↓
tells NVM which version the project wants

NVM
   ↓
checks whether that version exists

If missing
   ↓
install it
```

---

# 11. Now let's introduce ASDF

Now you understand the problem with Node.

But think bigger.

A modern application may use:

```text
Node.js
Python
Ruby
Java
Terraform
kubectl
PostgreSQL
```

Now imagine managing every tool separately.

You could have:

```text
NVM
  ↓
Node.js

pyenv
  ↓
Python

rbenv
  ↓
Ruby

SDKMAN
  ↓
Java

asdf
  ↓
???
```

This becomes complicated.

So another approach is:

> **Use one version manager capable of managing many different tools.**

That's where **ASDF** comes in.

---

# 12. What is ASDF?

ASDF is commonly expanded as:

> **Another Version Manager**

The key idea is:

> **One version-management framework for many runtimes and tools.**

Instead of having:

```text
NVM → Node
pyenv → Python
rbenv → Ruby
```

you can use:

```text
ASDF
 │
 ├── Node.js
 ├── Python
 ├── Ruby
 ├── Java
 ├── Terraform
 └── other supported tools
```

---

# 13. Why would someone need Python version management?

Imagine you're working on:

```text
Project A
Python 3.10
```

and another project:

```text
Project B
Python 3.12
```

And another:

```text
Project C
Python 3.13
```

If your operating system only has:

```text
Python 3.13
```

you can have compatibility problems.

Some old applications may depend on:

```text
Python 3.10
```

while a newer application uses:

```text
Python 3.13
```

So you need:

```text
Python 3.10
Python 3.12
Python 3.13
```

and a way to select the correct one.

That's version management.

---

# 14. Why Ruby?

Same concept.

Imagine:

```text
Application A
Ruby 3.1

Application B
Ruby 3.3
```

You don't want to constantly uninstall/install Ruby.

You want:

```text
ASDF
 │
 ├── Ruby 3.1
 └── Ruby 3.3
```

Then select the version required by the project.

---

# 15. But why PostgreSQL?

This is where things become slightly different.

PostgreSQL is not a programming language/runtime like Node or Python.

But **different PostgreSQL server/client versions can matter for development and testing**.

For example:

```text
Application
    ↓
PostgreSQL 14
```

Another project:

```text
Application
    ↓
PostgreSQL 16
```

You may need to test against a particular PostgreSQL version because:

* SQL behavior can change
* extensions may have version requirements
* migrations may behave differently
* client/server compatibility matters
* production might use a specific major version

However, in real production environments, PostgreSQL is often managed using:

```text
Docker
AWS RDS
Azure Database
Kubernetes operators
VM packages
managed database services
```

rather than relying on ASDF to run the production database.

So ASDF can be useful for **development/test tool versions**, but it isn't the standard production database-management solution.

---

# 16. Now the BIG difference: NVM vs ASDF

This is the most important part.

### NVM

NVM has a narrow purpose:

```text
NVM
 ↓
Node.js
```

It specializes in Node.js.

### ASDF

ASDF provides a framework:

```text
ASDF
 │
 ├── Node.js plugin
 ├── Python plugin
 ├── Ruby plugin
 ├── Java plugin
 ├── Terraform plugin
 └── ...
```

So:

```text
NVM = Node-focused

ASDF = multi-tool version manager
```

---

# 17. What is ASDF's plugin architecture?

This is the interesting part.

ASDF itself doesn't need to know every detail about every programming language.

Instead, it uses **plugins**.

Think about ASDF as a manager.

```text
                 ASDF
                  │
       ┌──────────┼──────────┐
       │          │          │
    Node.js     Python      Ruby
       │          │          │
    plugin      plugin     plugin
```

The plugin knows how to work with that particular tool.

For example, conceptually:

```text
ASDF
 ↓
Node plugin
 ↓
download/install Node
 ↓
select Node version
```

And:

```text
ASDF
 ↓
Python plugin
 ↓
download/install Python
 ↓
select Python version
```

---

# 18. Why is the plugin architecture useful?

Imagine ASDF didn't have plugins.

ASDF developers would need to understand and maintain installation logic for:

```text
Node
Python
Ruby
Java
Go
Terraform
etc.
```

That's difficult.

Instead:

```text
ASDF
 ↓
provides common version-management framework

Plugins
 ↓
provide tool-specific installation/version logic
```

So the architecture is:

```text
             ASDF CORE
                 │
       ┌─────────┼─────────┐
       │         │         │
     Plugin    Plugin    Plugin
       │         │         │
      Node     Python     Ruby
```

That's the major architectural difference from NVM.

---

# 19. NVM does not need this kind of plugin architecture

NVM was designed specifically around Node.js.

Its world is basically:

```text
NVM
 ↓
Node.js
 ↓
versions
```

ASDF's world is:

```text
ASDF
 ↓
plugin system
 ↓
many different tools
```

Therefore:

| Feature              | NVM                | ASDF                   |
| -------------------- | ------------------ | ---------------------- |
| Main purpose         | Node.js versions   | Multiple tool versions |
| Node.js              | ✅                  | ✅                      |
| Python               | ❌                  | ✅                      |
| Ruby                 | ❌                  | ✅                      |
| Java                 | ❌                  | ✅                      |
| Terraform            | ❌                  | ✅                      |
| Plugin architecture  | Not its core model | Core concept           |
| Project version file | `.nvmrc`           | `.tool-versions`       |

---

# 20. ASDF has a file similar to `.nvmrc`

This is another important connection.

NVM:

```text
.nvmrc
```

Example:

```text
20
```

ASDF:

```text
.tool-versions
```

Example:

```text
nodejs 20.19.0
python 3.12.5
ruby 3.3.4
terraform 1.9.5
```

Now you can see why ASDF is powerful.

A project could say:

```text
.tool-versions

nodejs 20.19.0
python 3.12.5
terraform 1.9.5
```

Meaning:

> "These are the tool versions expected by this project."

---

# 21. Now imagine a real project

Suppose you're working on a DevOps application.

Your project uses:

```text
Node.js
Python
Terraform
```

Your project requires:

```text
Node.js     20
Python      3.12
Terraform   1.9
```

### With separate tools

You might use:

```text
NVM
 ↓
Node

pyenv
 ↓
Python

some other manager
 ↓
Terraform
```

Multiple tools.

### With ASDF

```text
ASDF
 │
 ├── Node.js plugin
 ├── Python plugin
 └── Terraform plugin
```

And:

```text
.tool-versions

nodejs 20.x
python 3.12.x
terraform 1.9.x
```

One project file describes the development toolchain.

---

# 22. Why is this important for teams?

Imagine you have:

```text
Developer A
Developer B
Developer C
CI/CD
```

Without version control:

```text
Developer A → Node 20
Developer B → Node 22
Developer C → Node 24
CI           → Node 22
```

Potentially:

```text
Works for A
Fails for B
Works in CI
```

Very frustrating.

With version declaration:

```text
.tool-versions
```

everyone can use the expected versions.

```text
                 Git repository
                       │
                .tool-versions
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
     Developer A   Developer B      CI
          │            │            │
          └────────────┼────────────┘
                       ↓
              same tool versions
```

This is one reason version files are valuable.

---

# 23. Where does Git come into this?

This is an important practical point.

You usually commit:

```text
.nvmrc
```

or:

```text
.tool-versions
```

to Git.

For example:

```text
my-project/
│
├── .git/
├── .tool-versions
├── package.json
├── src/
└── README.md
```

Then another developer clones:

```bash
git clone <repository>
```

and gets the version information automatically.

So the project itself communicates:

> "These are the versions we expect."

---

# 24. Does `.nvmrc` mean production will automatically use that version?

**No.**

This is very important.

`.nvmrc` is primarily a **project/environment hint for NVM**.

It does not magically configure:

```text
AWS
Docker
Kubernetes
Production server
```

For production, you normally explicitly define the runtime.

For example, Docker:

```dockerfile
FROM node:20
```

Now the production/build environment uses Node 20.

Or a CI workflow might specify Node 20.

So you might have:

```text
Developer
   ↓
.nvmrc
   ↓
Node 20

Production
   ↓
Dockerfile
   ↓
node:20
```

Both are expressing the same desired runtime, but through different mechanisms.

---

# 25. Who actually implements/uses NVM in a company?

Not usually the **end customer**.

Let's imagine a company has:

```text
10 developers
5 QA engineers
3 DevOps engineers
1 production platform
```

### Developers

They may use:

```text
NVM
```

to switch Node versions.

For example:

```bash
nvm use
```

### Project repository

Contains:

```text
.nvmrc
```

### CI/CD

Could use:

```text
Node 20
```

through the CI configuration.

### Docker

Could use:

```dockerfile
FROM node:20
```

### Kubernetes

Runs the resulting container.

```text
Kubernetes
    ↓
container
    ↓
Node 20 application
```

So the entire chain can look like:

```text
Developer laptop
       │
       │ NVM
       ↓
   Node 20
       │
       │ git push
       ↓
   Git repository
       │
       │ CI/CD
       ↓
   Build application
       │
       │ Docker
       ↓
   node:20 image
       │
       ↓
   Kubernetes
       │
       ↓
 Production
```

---

# 26. Is NVM itself running inside Kubernetes?

Usually, **no**.

This is another common misunderstanding.

You don't normally have:

```text
Kubernetes
   ↓
NVM
   ↓
Node
```

Instead:

```text
Docker image
   ↓
Node.js
   ↓
Application
```

For example:

```dockerfile
FROM node:20

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

CMD ["npm", "start"]
```

The Docker image already contains the required Node version.

Therefore you don't need NVM inside the container just to switch versions.

---

# 27. Then why should I learn NVM if production uses Docker?

Because developers need a controlled local environment.

Suppose production uses:

```text
Node 20
```

Your local computer should ideally use:

```text
Node 20
```

NVM makes that easy.

So:

```text
Production
Node 20
      ↑
      │
Developer
Node 20
```

This reduces environment differences.

---

# 28. NVM vs Docker — don't confuse them

They solve related but different problems.

### NVM

Manages:

```text
Node versions
on your machine
```

### Docker

Packages:

```text
Application
+
runtime
+
dependencies
+
configuration
```

into a container image.

So:

```text
NVM
=
"What Node version should my development machine use?"

Docker
=
"What exact runtime/environment should this application run with?"
```

---

# 29. NVM vs ASDF — when would you choose which?

If you mainly work with Node.js:

```text
JavaScript/TypeScript developer
        ↓
       NVM
```

is simple and focused.

If you regularly work with:

```text
Node
Python
Ruby
Terraform
Java
Go
```

then a multi-tool manager such as:

```text
ASDF
```

can make the environment more centralized.

For example, as a DevOps engineer you might have:

```text
Node.js
Python
Terraform
kubectl
Helm
```

and want project-specific versions.

That's where the multi-tool approach becomes useful.

---

# 30. The complete picture

Now connect everything.

### Step 1 — LTS

LTS means:

```text
Long-Term Support
```

A supported/stable release line maintained for longer.

---

### Step 2 — Version problem

Different projects may require different versions:

```text
Project A → Node 20
Project B → Node 22
```

---

### Step 3 — NVM

NVM manages Node versions:

```text
NVM
 ├── Node 20
 ├── Node 22
 └── Node 24
```

---

### Step 4 — `.nvmrc`

The project tells NVM:

```text
.nvmrc
20
```

Meaning:

```text
This project expects Node 20.
```

---

### Step 5 — Multiple tools

Real projects may require:

```text
Node
Python
Ruby
Terraform
Java
```

Managing each separately can become inconvenient.

---

### Step 6 — ASDF

ASDF provides one version-management framework:

```text
ASDF
 │
 ├── Node.js
 ├── Python
 ├── Ruby
 ├── Java
 └── Terraform
```

---

### Step 7 — Plugin architecture

Each tool is handled through a plugin:

```text
ASDF
 │
 ├── Node plugin
 ├── Python plugin
 ├── Ruby plugin
 └── Terraform plugin
```

The plugin handles tool-specific installation/version behavior.

---

### Step 8 — `.tool-versions`

The project can define:

```text
.tool-versions

nodejs 20.x
python 3.12.x
terraform 1.9.x
```

So the whole development environment becomes reproducible.

---

# 31. The simplest way to remember everything

Keep this mental model:

```text
                    VERSION MANAGEMENT
                           │
             ┌─────────────┴─────────────┐
             │                           │
            NVM                         ASDF
             │                           │
       Node.js only              Multiple tools
             │                           │
             │                 ┌─────────┼─────────┐
             │                 │         │         │
             │               Node      Python    Ruby
             │
        .nvmrc
             │
        "Use Node 20"
```

And:

```text
LTS
 │
 └── A long-supported release line

NVM
 │
 └── Manages Node.js versions

.nvmrc
 │
 └── Project's Node.js version declaration

ASDF
 │
 └── Manages versions of many tools

Plugins
 │
 └── Teach ASDF how to manage each tool

.tool-versions
 │
 └── Project's versions for multiple tools
```

---

# 32. One final real-world example

Imagine you join a company.

You clone:

```bash
git clone https://github.com/company/payment-service.git
```

Inside you find:

```text
payment-service/
│
├── .tool-versions
├── package.json
├── requirements.txt
├── Dockerfile
└── src/
```

`.tool-versions` says:

```text
nodejs 20.19.0
python 3.12.5
terraform 1.9.8
```

You install ASDF and the required plugins.

Then ASDF gives you:

```text
Node 20.19.0
Python 3.12.5
Terraform 1.9.8
```

You develop the application.

Then CI builds:

```text
Dockerfile
     ↓
FROM node:20
     ↓
Application image
     ↓
Container registry
     ↓
Kubernetes
     ↓
Production
```

Notice the responsibilities:

```text
ASDF/NVM
   ↓
Developer environment

Git
   ↓
Stores project version requirements

CI/CD
   ↓
Builds/tests application

Docker
   ↓
Packages application + runtime

Kubernetes
   ↓
Runs application
```

That separation is **very important**.

---

