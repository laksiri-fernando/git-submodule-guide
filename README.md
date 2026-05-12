# GIT Submodule Guide

Git submodules allow you to include one Git repository inside another Git repository.

This is useful when:

- Multiple projects share common code
- You want to keep dependencies in separate repositories
- You want independent versioning for components
- You want to reuse libraries/modules across projects

## What Is a Git Submodule

A submodule is:

> A Git repository embedded inside another Git repository.

Example:

```bash
Main Project: Super Project (git-submodule-guide)
│
├── frontend/
├── backend/
├── git-submodule-sub/   <-- Git Submodule
└── .gitmodules
```
## 1. Create the submodule (git-submodule-sub)

Create the submodule repo in github

## 2. Setup submodule for the guide
```bash
mkdir temp
cd temp
mkdir git-submodule-sub
cd git-submodule-sub

echo "# git-submodule-sub" >> README.md
echo "export const VERSION = '1.0';" > utils.js

git init
git add .
git commit -m "first commit"

git branch -M main
git remote add origin git@github.com:laksiri-fernando/git-submodule-sub.git
git push -u origin main
```

## 3. Create the main repo (git-submodule-guide)

Create the main repo in github

## 4. Setup main repo for the guide
```bash
# From lesions directory
mkdir git-submodule-guide
cd git-submodule-guide

echo "# git-submodule-guide" >> README.md

git init
git add .
git commit -m "first commit"

git branch -M main
git remote add origin git@github.com:laksiri-fernando/git-submodule-guide.git
git push -u origin main

```

## 5. Add submodule
```bash
# git submodule add <repo-url> <folder>
git submodule add git@github.com:laksiri-fernando/git-submodule-sub.git
```

Git will:

- Clone the repository
- Create .gitmodules
- Add special entry in Git index

## 6. Commit the submodule
```bash
git add .
git commit -m "Added git-submodule-sub submodule"
```

## 7. Clone a repo with submodules

Other users will clone the main repo which contain submodules. This is where many beginners get confused.

```bash
git clone --recurse-submodules git@github.com:laksiri-fernando/git-submodule-guide.git
```

This clones:

- parent repo
- all submodules

### If You Already Cloned Without Submodules
```bash
git submodule init
git submodule update

# or
git submodule update --init --recursive
```

## 8. Understanding Detached HEAD in Submodules

Inside submodule:

```bash
cd git-submodule-sub
git status
```

You may see:

>HEAD detached at a8f72d9

This is NORMAL.

Because parent repo tracks a specific commit.

## 9. Updating Submodule Content
Suppose submodule changes.

In submodule
```bash
echo "export const API = 'v2';" >> utils.js

git add .
git commit -m "Added API constant"
git push
```

## 10. Parent Repo Does NOT Auto Update

In main repo

```bash
cd git-submodule-sub
git pull origin main
```

Now submodule has newer commit.

Go back and check status:

```bash
cd ..
git status
```

You will see:

> modified:   git-submodule-sub (new commits)

This means:

- Submodule commit reference changed.

## 11. Commit Updated Submodule Reference

In main repo:

```bash
git add git-submodule-sub
git commit -m "Updated submodule version"
```

VERY IMPORTANT:

You commit the NEW submodule commit pointer.

## 12. Other Developers Pulling Changes

In main repo:

```bash
git pull
git submodule update --init --recursive
```

to move submodule to correct commit.

## 13. Common Workflow (Most Practical)

Team Updates Shared Library

```bash
cd libs/shared-utils

git checkout main
git pull
```

Make Changes

```bash
vim utils.js
```

Commit in Submodule

```bash
git add .
git commit -m "Added helper"
git push
```

> At a certain occasion users may decide to add new feature to the main package

#### Update Parent Repo Pointer

Go parent repo and commit new submodule reference:

```bash
git add libs/shared-utils
git commit -m "Updated shared-utils"
git push
```

Two commits happen:

1. Submodule repo commit
2. Parent repo pointer commit

## 14. View Submodule Status

```bash
git submodule status
```

Example output:

a8f72d9 git-submodule-sub

## 15. Initialize ALL Submodules Recursively

Useful for nested submodules.

```bash
git submodule update --init --recursive
```

## 15. Pull Latest Submodule Changes

Option 1 — Manual

```bash
cd libs/shared-utils
git pull
```

Option 2 — From Parent

```bash
git submodule update --remote
```

This fetches latest configured branch.

## 16. Track a Branch Instead of Fixed Commit

Normally submodule tracks commit.

But you can configure branch.

Edit .gitmodules:

```ini
[submodule "libs/shared-utils"]
    path = libs/shared-utils
    url = https://github.com/company/shared-utils.git
    branch = main
```

Then:

```bash
git submodule update --remote
```

## 17. Clone + Pull + Update Shortcut

Best command:

```bash
git pull --recurse-submodules
```

## 18. Nested Submodules

Possible:

```text
main-project
└── library-a
    └── library-b
```

Use recursive:

```bash
git clone --recurse-submodules
```

and:

```bash
git submodule update --init --recursive
```

## 19. Remove a Submodule (Modern Method)

Suppose removing:

`git-submodule-sub`

Step 1

```bash
git submodule deinit -f git-submodule-sub
```

Step 2

```bash
git rm -f git-submodule-sub
```

Step 3

Delete leftover metadata:

```bash
rm -rf .git/modules/git-submodule-sub
```

Step 4:

Commit

```bash
git commit -m "Removed shared-utils submodule"
```

## 20. Difference Between Submodule and Normal Folder

| Feature                      | Normal Folder | Submodule |
| ---------------------------- | ------------- | --------- |
| Separate Git history         | No            | Yes       |
| Independent versioning       | No            | Yes       |
| Separate remote repo         | No            | Yes       |
| Reusable across projects     | Limited       | Excellent |
| Commit tracked independently | No            | Yes       |


## 21. Difference Between Submodule and Monorepo

#### Monorepo

One repository:

```text
repo/
├── frontend
├── backend
└── libs
```

Single git history

#### Submodule Architecture

```text
frontend-app/
backend-app/
shared-lib/
```

All separate repositories.

## 22. Advantages of Submodules

Reusable Libraries

- Shared components across many systems.

Independent Releases

- Each module versioned separately.

Cleaner Separation

- Teams can own different repos.

Security/Permissions

- Different repo access control.

## 23. Disadvantages of Submodules

Very important.

#### Harder Workflow

Developers often forget:

```bash
git submodule update
```

#### Detached HEAD Confusion

Beginners think repo broken.

#### Two-Step Commits

Need commit in:

1. submodule
2. parent repo

#### CI/CD Complexity

Pipelines must clone recursively.

Example:

```bash
git clone --recurse-submodules
```

## 24. CI/CD Example (GitHub Actions)

```yaml
- name: Checkout
  uses: actions/checkout@v4
  with:
    submodules: recursive
```

## 25. Best Practices

#### Always Use Relative Stable Structure

Good:

`git-submodule-sub`

Avoid changing paths frequently

#### Pin Stable Commits

Avoid auto-tracking latest main in production.

#### Document Setup Clearly

README should contain:

```bash
git clone --recurse-submodules
```

#### Use Recursive Commands

```bash
git submodule update --init --recursive
```

## 26. Real-World Example

Suppose your architecture:

```text
ABCSolutions/
├── apigw/
├── hrm/
├── tms/
├── lb/
└── shared-ui/
```

You could make:

`shared-ui`

a separate repository and include as submodule in:

- HRM
- TMS
- Line Balancing

This enables:

- independent UI framework versioning
- shared Angular components
- reusable authentication modules

## 27. Typical Enterprise Workflow

#### Developer A Updates Shared Repo

```bash
cd shared-ui

git commit
git push
```

#### Developer B Updates Parent Repo

```bash
cd hrm-app/shared-ui

git pull
```

Then:

```bash
cd ..
git add shared-ui
git commit -m "Updated shared-ui version"
```

## 28. Useful Commands Cheat Sheet

#### Add Submodule
```bash
git submodule add <url> <path>
```

#### Clone With Submodules
```bash
git clone --recurse-submodules <repo>
```

#### Initialize Existing Submodules
```bash
git submodule update --init --recursive
```

#### Update Submodule to Latest
```bash
git submodule update --remote
```

#### Check Status
```bash
git submodule status
```

#### Remove Submodule
```bash
git submodule deinit -f <path>
git rm -f <path>
rm -rf .git/modules/<path>
```

## 29. MOST COMMON Beginner Mistakes

#### Mistake 1

Forgetting:

```bash
git add <submodule-folder>
```

after submodule update.

#### Mistake 2

Editing submodule without committing inside submodule repo.

#### Mistake 3

Cloning without:

```bash
--recurse-submodules
```

#### Mistake 4

Thinking submodule automatically updates to latest branch.

It does NOT.

## 30. When SHOULD You Use Submodules?

Good for:

- Shared libraries
- Shared UI components
- Common configs
- Independent microservices
- Large enterprise reusable modules

## 31. When NOT To Use Submodules?

Avoid if:

- Small team unfamiliar with Git
- Rapidly changing tightly-coupled code
- Simple projects
- You want extremely easy onboarding

Sometimes monorepo is simpler.

## 32. Alternative Approaches

Instead of submodules:

- Monorepo
- Git subtree
- npm packages
- Private package registries
- Docker images
- Maven/NuGet/PyPI packages

Modern teams often prefer package managers over submodules.

## 33. Recommended Learning Exercise

Try this locally:

#### Create Two Repositories

```bash
math-lib
school-app
```

#### Add math-lib as submodule

#### Change math-lib

#### Commit in math-lib

#### Update parent repo pointer

#### Clone from scratch

This exercise makes submodules “click”.

## Final Mental Model

Think of submodules like this:

```text
Parent Repo
   ↓
Stores POINTER to
   ↓
Specific commit in another repo
```

NOT:

`Parent repo contains copied files`

That understanding solves most confusion
