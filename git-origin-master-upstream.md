# Git: `origin`, `master`, and `-u` Explained

> Focus notes on the three things that confused me: what `origin` and `master` actually are, why `push` sometimes needs `-u`, and how to point Git at a different destination.

---

## 1. `origin` — the alias of a remote repository

`origin` is **not** a built-in server or a magic keyword. It is just a **nickname (别名)** that your local Git stores for a long remote URL.

```bash
git remote add origin https://github.com/user/repo.git
```

The moment you run this, Git quietly remembers the URL under the name `origin`. From then on, `git push origin master` is shorthand for "push to that saved URL". You could have named it anything (`my-github`, `school`, `tom`); `origin` is simply the conventional default chosen by `git clone` / `git remote add`.

**Did I ever configure it?** Almost certainly yes — the first `git clone` or `git remote add` set it automatically. Check what your machine remembers:

```bash
git remote -v
```

If a destination was never configured, `git push` fails outright with `fatal: No configured push destination.` Git cannot guess where to send your code.

---

## 2. `master` — the default branch (main line of development)

`master` is a **branch name (分支名)**, not a remote location. Think of coding like writing an essay:

- The very first, canonical draft is the `master` branch. Every `git commit` is appended along this line.
- When you experiment, you copy the draft into a separate branch so the original stays safe, then merge it back or throw it away.

`git push origin master` means: "push my local branch named `master` into the remote called `origin`."

> Note: modern Git often defaults this first branch to `main` instead of `master`. They are the same thing — only the name differs.

---

## 3. Why `push` sometimes needs `-u`

`-u` is short for `--set-upstream` (设置上游 / 建立跟踪关系).

```bash
git push -u origin master
```

- **Without `-u`**: Git pushes this one time. Next time it has to ask "pull from where? push to where?" and you must repeat `git push origin master` / `git pull origin master` every time.
- **With `-u`**: Git remembers that this local `master` tracks the remote `origin/master`. After that, plain `git push` and `git pull` are enough.

So `-u` is normally needed **only the first time** you push a new branch.

The command decodes as:

| Part | Meaning |
|---|---|
| `git push` | upload local commits to a remote |
| `-u` | remember the upstream tracking relationship |
| `origin` | which remote (the stored alias) to push to |
| `master` | which local branch to push |

---

## 4. Changing / adding the destination

### A. Switch to a different repository (e.g. GitHub → Gitee / school server)

```bash
git remote -v          # see the current origin
git remote rm origin   # unbind the old one
git remote add origin <new-url>
git push -u origin master
```

### B. Keep several destinations at once

```bash
git remote add github https://github.com/user/repo.git
git remote add school git@server:user/repo.git

git push github master   # send to GitHub
git push school master   # send to the school server
```

---

## 5. The relationship in one line

| Concept | What it really is | Common misconception |
|---|---|---|
| `origin` | an alias for a remote URL stored in your local config | "a fixed server" — no, it is just a name you configured |
| `master` / `main` | a branch name, the default main line of development | "a remote place" — it lives both locally and remotely |
| `upstream` (`-u`) | the tracking link between a local branch and a remote branch | "a specific repository" — it is the paired remote branch, not a repo |

**Bottom line:** `git add` / `git commit` / `git push` work identically on the school Linux server and on Windows PowerShell. Git does not care about your OS or terminal; it only cares about the branch you are on (`master`), the remote alias you target (`origin`), and the upstream link you optionally set (`-u`).

---
