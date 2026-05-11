# GIT Submodule Guide

## 1. Create the submodule (git-submodule-sub)

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

## 3. Create the main repo in github (git-submodule-guide)

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