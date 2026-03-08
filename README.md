# Git Professional Workflow
## SSH Setup · Branching Strategy · Commit Conventions

![Git](https://img.shields.io/badge/Git-F05032?style=flat&logo=git&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)
![SSH](https://img.shields.io/badge/SSH-4EAA25?style=flat&logo=gnubash&logoColor=white)
![Terminal](https://img.shields.io/badge/Terminal-000000?style=flat&logo=windowsterminal&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat)

> **Team onboarding reference** — covers SSH authentication, branch naming, Conventional Commits, and a full feature-branch workflow from clone to merge.

---

## Table of Contents

1. [SSH Authentication Setup](#1-ssh-authentication-setup)
2. [Branching Strategy](#2-branching-strategy)
3. [Conventional Commits Reference Card](#3-conventional-commits-reference-card)
4. [Complete Workflow Example](#4-complete-workflow-example)
5. [Useful Git Aliases](#5-useful-git-aliases)

---

## 1. SSH Authentication Setup

SSH authentication avoids typing your password on every push/pull and is the standard for team environments.

### Generate an ED25519 key (recommended over RSA)

```bash
# Generate key — replace with your GitHub email
ssh-keygen -t ed25519 -C "your@email.com"

# Accept default path (~/.ssh/id_ed25519) or specify one
# Set a strong passphrase when prompted
```

### Add the key to the SSH agent

```bash
# Start the agent
eval "$(ssh-agent -s)"

# Add your private key
ssh-add ~/.ssh/id_ed25519
```

### Add the public key to GitHub

```bash
# Copy the public key to clipboard
cat ~/.ssh/id_ed25519.pub
# paste the output into: GitHub Settings > SSH and GPG keys > New SSH key
```

### Verify the connection

```bash
ssh -T git@github.com
# Expected: Hi username! You've successfully authenticated...
```

### Configure Git identity (one-time)

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global init.defaultBranch main
```

---

## 2. Branching Strategy

```
main
|  Production-ready code only. Never commit directly.
|
+-- develop
|   Integration branch. Merge features here before promoting to main.
|
+-- feature/description-in-kebab-case
|   New features. Branch from develop, merge back to develop via PR.
|   Example: feature/add-hebergement-card-component
|
+-- fix/description
|   Bug fixes on develop or main.
|   Example: fix/responsive-card-overflow-768px
|
+-- hotfix/description
    Critical fixes branched directly from main.
    Example: hotfix/broken-nav-link-production
```

| Branch | Branches from | Merges into | Direct push |
|---|---|---|---|
| main | — | — | Never |
| develop | main | main | Never |
| feature/* | develop | develop | Author only |
| fix/* | develop | develop | Author only |
| hotfix/* | main | main + develop | Author only |

---

## 3. Conventional Commits Reference Card

Format: `<type>(<scope>): <imperative summary>`
Max 50 characters for the title. Body optional, wrapped at 72 chars.

| Type | When to use | Example |
|---|---|---|
| feat | New feature or page | `feat(projet3): add responsive filter buttons` |
| fix | Bug correction | `fix(nav): correct hamburger menu z-index on iOS` |
| docs | README, comments, changelogs | `docs(readme): add screenshot and live demo link` |
| style | CSS tweaks, no logic change | `style(cards): align hotel card heights in grid` |
| refactor | Restructuring without behavior change | `refactor(layout): extract header into component` |
| test | Add or update tests | `test(filters): add unit test for aria-pressed toggle` |
| chore | Tooling, config, file moves | `chore: add .gitignore and .editorconfig` |
| perf | Performance improvements | `perf(images): compress hero jpg from 800KB to 120KB` |

### Full commit message example

```
feat(projet3): add responsive hebergement card grid

- CSS Grid layout replacing float-based approach
- Cards reflow at 768px (2 cols) and 1200px (3 cols)
- aria-label added to each card link for accessibility

Closes #12
```

---

## 4. Complete Workflow Example

From first clone to merged PR — real commands, zero magic.

```bash
# 0. Clone the repository (SSH)
git clone git@github.com:KinSushi/OpenClassroomsProject.git
cd OpenClassroomsProject

# 1. Always start from an up-to-date develop
git checkout develop
git pull origin develop

# 2. Create your feature branch
git checkout -b feature/add-activity-section

# 3. Work, then stage selectively
git add index.html styles.css

# 4. Commit with Conventional Commits
git commit -m "feat(projet3): add activities section with mosaic layout"

# 5. Push the feature branch
git push origin feature/add-activity-section

# 6. Open a Pull Request on GitHub
#    base: develop  <--  compare: feature/add-activity-section

# 7. After PR approval — merge and clean up
git checkout develop
git pull origin develop
git branch -d feature/add-activity-section
git push origin --delete feature/add-activity-section

# 8. Promote develop to main (release)
git checkout main
git pull origin main
git merge --no-ff develop -m "chore(release): merge develop into main v1.1.0"
git push origin main
```

### Resolving a merge conflict

```bash
git status                    # see conflicting files
# Edit files — remove <<<<<<< HEAD markers
git add resolved-file.css
git commit -m "fix: resolve merge conflict in styles.css"
```

---

## 5. Useful Git Aliases

Add to `~/.gitconfig` under `[alias]`:

```ini
[alias]
  st  = status -sb
  lg  = log --oneline --graph --decorate --all
  ac  = !git add -A && git commit -m
  sw  = switch
  pp  = !git push -u origin HEAD
  undo = reset --soft HEAD~1
  bra = branch -v --sort=-committerdate
```

```bash
git lg                           # visual branch history
git ac "fix(nav): correct link"  # add all + commit
git pp                           # push + set upstream
```

---

## Quick Reference

```bash
git init                          # initialize repo
git clone git@github.com:...      # clone via SSH
git status -sb                    # compact status
git add -p                        # stage interactively
git diff --staged                 # review staged changes
git commit --amend --no-edit      # fix last commit (before push only)
git stash / git stash pop         # shelve and restore changes
git log --oneline -10             # last 10 commits
git tag v1.0.0 -m "First release" # annotated tag
git push origin --tags            # push tags
```

---

## License

MIT — see [LICENSE](LICENSE)

---

> Built while completing **OpenClassrooms Course 7162856 — Gerez du code avec Git et GitHub**
> Skills: git init · branch · checkout · merge · push/pull · conflict resolution · SSH keygen · GitHub authentication · Conventional Commits
