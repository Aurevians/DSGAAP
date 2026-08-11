🏆 DSGAAP Milestone — Git & GitHub Foundations
Completed today:
GitHub → local repository with git clone
git status
git restore
git add -u
git commit
git pull
git push
Git configuration hierarchy
Personal vs. work Git identities
Conditional Git configuration
Repository-local configuration
macOS Keychain / osxkeychain
credential.useHttpPath
Fine-grained GitHub PAT
Least-privilege repository access
Successful authentication to Aurevians/DSGAAP


Git identity
      ↓
Git configuration hierarchy
      ↓
Conditional configuration
      ↓
Repository-local configuration
      ↓
Credential helper
      ↓
macOS Keychain
      ↓
HTTPS authentication
      ↓
Fine-grained PAT
      ↓
Least-privilege access
      ↓
Successful GitHub push




🌱 Today's Git lessons

1. git clone
git clone https://github.com/Aurevians/DSGAAP.git

→ Create a local working copy from GitHub.

2. git status
git status

→ Your Git "health check." It tells you whether you're clean, ahead, behind, or have changes.

3. git restore
git restore ANACONDA_JUPYTER_SETUP.md

→ Restore a tracked file back to the version Git knows about.
And you actually experienced why this is useful. You restored the deleted file, confirmed the working tree became clean, and then deliberately deleted it again. That's much better than simply reading about git restore. 😄

4. git add -u
git add -u

→ Stage modifications/deletions to already-tracked files.

5. git commit
git commit -m "Removing duplicate ANACONDA_JUPYTER_SETUP.md file"

→ Record the change in your local Git history.
And you got your first encounter with the TruffleHog pre-commit hook. 🐷🔑

6. git pull
You also learned why:
git pull

can refuse to run when you have unstaged changes:
error: cannot pull with rebase: You have unstaged changes.
That was a very useful real-world lesson.

7. git push
And finally:
git push

→ Send your local commit to GitHub.
Which initially failed with:
403 Permission denied
and then we diagnosed the entire authentication chain and fixed it. 🔐

🎓 And there was a bigger lesson hidden inside all of this

You experienced the difference between:

Working Directory
      ↓
git add
      ↓
Staging Area
      ↓
git commit
      ↓
Local Repository
      ↓
git push
      ↓
Remote Repository (GitHub)

And git restore operates primarily around the working tree/staging state, while git pull brings changes from the remote into your local repository.

That's a foundation we'll build on later when we cover branching, merging, rebasing, pull requests, conflicts, and eventually more advanced Git workflows.
So we count today as a DSGAAP Git milestone. 🏆

Milestone Earned — Git & GitHub Foundations 🌱
Clone → Status → Restore → Stage → Commit → Pull → Push → Authentication → PAT → Multi-account configuration


