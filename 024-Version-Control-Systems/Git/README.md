# GIT

![Git work areas](images/18333fig0106-tn.png)

---

**WORKING AREA** -> **STAGING AREA** -> **HEAD** -> **REMOTE**

---

* **WORKING AREA** : Area where changes are made
* **STAGING AREA** : Area where changes are staged for commit (git add)
* **HEAD** : Final area where changes are commited to (`**master**`` branch, by default)
* **REMOTE** : Remote Git repository (also denoted as `**origin**`)

>NOTE:
> *HEAD: The local branch to which the commits are going to
> This can be checked using:
>   # git branch
>   # cat .git/HEAD
---
| Command |  Operation |
|----------| ---------- |
| git init | Initialize a Git repo |
| git status | Check the status of the repo |
| git add | Add files to be tracked in Staging area |
| git diff | Show changes between working area and staging area |
| git diff --staged | Show changes between staged and committed area |
| git diff HEAD | Show changes between working area and HEAD |
| git remote add origin https://github.com/my_code | Set the origin/remote to github |
| git push -u origin master | Push the `master` to `origin` |
