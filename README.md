# GIT Submodule Guide

1. Create the submodule (git-submodule-sub)

2. Setup submodule for the guide
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

3. Create the main repo in github (git-submodule-guide)

4. Setup main repo for the guide
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

5. Add submodule
```bash
# git submodule add <repo-url> <folder>
git submodule add git@github.com:laksiri-fernando/git-submodule-sub.git
```

Git will:

- Clone the repository
- Create .gitmodules
- Add special entry in Git index

6. Commit the submodule
```bash
git add .
git commit -m "Added git-submodule-sub submodule"
```