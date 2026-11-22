# Git mini exo: my answers

Replace `USERNAME` with your GitHub username, and `Your Name` / `you@example.com` with your own info.

---

## Part 1: Setup & config

1. Git is installed (it comes with most Linux/WSL setups). If not: `sudo apt-get install -y git`
2. Check the version:
   ```bash
   git --version
   ```
3. Configure username and email:
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```

## Part 2: Create your first repository

```bash
mkdir my-first-repo      # create the folder
cd my-first-repo         # go into it
git init                 # initialize a Git repository
touch readme.txt         # create the file
```

## Part 3: Your first commit

1. Current state of the repo: `git status`
2. Stage `readme.txt`: `git add readme.txt`
3. Commit: `git commit -m "Add readme file"`
4. Commit history: `git log`

## Part 4: Make changes

1. Edit the file and add a line:
   ```bash
   echo "Hello, this is my first repo" >> readme.txt
   ```
2. `git status` now shows that `readme.txt` is **modified** but **not staged for commit**. Git sees the file changed since the last commit, but I haven't told it yet to include that change in the next commit.
3. Stage and commit:
   ```bash
   git add readme.txt
   git commit -m "Add first line to readme"
   ```
4. I now have **2 commits**.

## Part 5: Exploration

- `git diff`: shows line by line what changed between my working files and the last staged/committed version (the lines added appear with `+`, removed with `-`). It shows only changes that are **not staged yet**.
- `git log --oneline`: shows the commit history in a compact way, one line per commit (short hash + message).

## Part 6: Working with branches

1. List all branches: `git branch`
2. Create `feature-script`: `git branch feature-script`
3. Switch to it: `git switch feature-script` (older way: `git checkout feature-script`)
4. Create **and** switch to `dev` in one command: `git switch -c dev` (older way: `git checkout -b dev`)
5. Switch back to `feature-script`: `git switch feature-script`
6. Verify the current branch: `git branch` (the `*` marks the current branch) or `git status` (first line says "On branch feature-script")

## Part 7: Create a bash script on a branch

1. Make sure I'm on `feature-script` (`git branch`).
2. Create the file: `touch install.sh`
3. Content of `install.sh`:
   ```bash
   #!/usr/bin/env bash
   echo "Starting installation..."
   sudo apt-get update
   sudo apt-get install -y htop
   echo "Installation complete!"
   ```
4. Make it executable: `chmod +x install.sh`
5. Stage and commit:
   ```bash
   git add install.sh
   git commit -m "Add install script"
   ```
6. Commit history on this branch: `git log --oneline` (I see 3 commits: the script one, plus the 2 earlier ones).

## Part 8: Merge branches

1. Switch back to main: `git switch main`
2. `ls`: **`install.sh` is not there.** The script was committed on `feature-script` only, and `main` hasn't received that commit yet. Each branch has its own version of the files.
3. Merge: `git merge feature-script`
4. `ls` again: **`install.sh` now appears** in the folder.
5. `git log --oneline`: the "Add install script" commit is now in the history of `main`. There is no extra "merge commit" because `main` had no new commits of its own, so Git just moved `main` forward (this is called a **fast-forward** merge). The history stays a straight line.
6. Delete the merged branch: `git branch -d feature-script`

## Part 9: Push to GitHub

1. On github.com: New repository, name it `my-first-repo`.
2. **Do not** initialize it with a README (otherwise the histories conflict).
3. Link the local repo to GitHub:
   ```bash
   git remote add origin https://github.com/USERNAME/my-first-repo.git
   ```
4. Push:
   ```bash
   git branch -M main           # make sure the branch is called main
   git push -u origin main
   ```
5. After refreshing the GitHub page, I see my files (`readme.txt`, `install.sh`) and my commits.

## Part 10: Delete and clone

1. Leave the project folder: `cd ..`
2. Delete the local repository folder: `rm -rf my-first-repo`
3. Clone it from GitHub: `git clone https://github.com/USERNAME/my-first-repo.git`
4. Go in and verify:
   ```bash
   cd my-first-repo
   ls
   ```
   The files are back, and the history too: `git log --oneline`.

## Part 11: Full workflow practice

```bash
cp my-first-repo/install.sh ~/install.sh
```

Then:

```bash
# 1. delete the local folder
rm -rf my-first-repo

# 2. delete the repo on GitHub
#    GitHub > my-first-repo > Settings > Danger Zone > Delete this repository

# 3. new folder
mkdir bash-installer && cd bash-installer

# 4. init Git (directly on a branch called main)
git init -b main

# 5. README on main
echo "# bash-installer" > README.md
echo "A small project with a bash script that installs packages." >> README.md

# 6. commit the README
git add README.md
git commit -m "Add README"

# 7. create the branch
git switch -c feature-install

# 8. bring the previous script into this repo
cp ~/install.sh .

# 9. commit the script
git add install.sh
git commit -m "Add install script"

# 10. back to main
git switch main

# 11. merge
git merge feature-install

# 12. delete the feature branch
git branch -d feature-install

# 13. create a new (empty) repo called bash-installer on GitHub

# 14. push
git remote add origin https://github.com/USERNAME/bash-installer.git
git push -u origin main
```

---

## Reflection questions

1. **Why is version control useful?** It keeps the full history of a project, lets me go back to an older version if I break something, allows several people to work on the same code, and acts as a backup.
2. **Staging vs committing?** Staging (`git add`) is choosing which changes will go in the next snapshot. Committing (`git commit`) actually saves that snapshot in the history.
3. **When should I commit?** When I finish a small, coherent piece of work that makes sense on its own (a feature, a fix, a file added). Small and often.
4. **`git init` vs `git clone`?** `git init` creates a brand new empty repository in the current folder. `git clone` copies an existing repository (for example from GitHub) with all its history.
5. **Why write good commit messages?** So that me and my teammates understand what changed and why, find bugs faster, and read the history without opening every commit. "Add install script" is useful, "update" or "asdf" is not.
6. **Purpose of branches?** To work on something separately (a feature, a fix, an experiment) without touching the stable code on `main`, and merge it only when it's ready.
7. **When to create a branch instead of working on main?** For any new feature, a bug fix, or a risky test, especially when working with other people or when `main` must always stay working.

