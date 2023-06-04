# Git Project [![][autotest_badge]][autotest_workflow]

## Preliminaries

1. Fork this repo by clicking **Fork** in the top-right corner of the page. **Make sure you fork ALL branches, not only `main`**.
2. Clone your forked repository by:
   ```bash
   git clone https://github.com/<your-username>/<your-project-repo-name>
   ```
   Change `<your-username>` and `<your-project-repo-name>` according to your GitHub username and the name you gave to your fork. E.g. `git clone https://github.com/johndoe/GitProject`.

Let's get started...

## Part I: Branches 

Create the following git commit tree.
You can add any file you want in each commit, but the message for each commit must be exactly the same as denoted in the below graph (`c1`, `c2`,..., `c12`). 
Note the (lightweight) tags in commit `c8`. 

The parent commit of `c1` must be the last commit in branch `main` after a fresh clone of this repo (commit with message `start here`). 

```mermaid
gitGraph
       commit id: "c1"
       branch arik/bugfix1
       commit id: "c10"
       commit id: "c11"
       checkout main
       commit id: "c2"
       branch john/feature1
       checkout john/feature1
       commit id: "c3"
       checkout main
       merge arik/bugfix1 tag: "v1.0.2"
       checkout john/feature1
       branch john/feature1-test
       checkout john/feature1-test
       commit id: "c5"
       checkout main
       commit id: "c6"
       checkout john/feature1
       commit id: "c7"
       checkout main
       merge john/feature1 tag: "v1.0.3"
       checkout john/feature1-test
       commit id: "c8" tag: "john-only"
       checkout main
       commit id: "c9"
```

**Notes**:

- If you've messed up the repo, you can always checkout branch main and run `git reset --hard <commit-id>` where `<commit-id>` is the commit hash from which you need to start.
- By default, your tags aren't being pushed to remote. Make sure to push your tags using the `--tags` flag in the `git push` command.

### Test it locally

```bash
git checkout main
cd test
bash branches.sh
```

## Part II: Merge conflict

**It's highly recommended to use a conflict merge tool (like the built-in one in PyCharm or VSCode).**

Your team colleagues, John Doe and Narayan Nadella, are working together on the same task. 
Each one of them is working on his own git branch. 

- John Doe developed under `origin/feature/version1` branch.
- Narayan Nadella developed under `origin/feature/version2` branch.

Both checked out from the same `main` branch. 

You decide to create a new branch called `feature/myfeature` and merge the work of John and Narayan into your branch. When done this you encountered a conflict.

1. From `mian` branch, create and checkout `feature/myfeature` branch.
2. Merge `origin/feature/version1` into your branch, take a look on the merge changes.
3. Merge `origin/feature/version2` into your branch, and resolve the conflicts according to the below guidelines:
   - The flask webserver code under `app.py` should have a total of 8 endpoints **in the following order**: `/`, `/status`, `/blog`, `/pricing`, `/contact`, `/chat`, `/services`, `/internal`.
   - Narayan mistakenly has coded a bad port number for the service, John's branch port is correct.
   - Narayan knows better than John about the price of the service.

### Test it locally

```bash
git checkout main
cd test
bash conflict.sh
```

## Part III: Pre-commit and sensitive data 

In this repo, there is a commit which contains credentials of strong identity in AWS.
The file contains the credentials might look like:

```text
AWS_ACCESS_KEY_ID=AKIA6BJMA3TKBADSHFXZ
AWS_SECRET_ACCESS_KEY=op7N48fxIFxh06ToUwZd33emso/QKZWb/2M5fgTX
```

Your goal is to find this commit, and completely remove it from the history. 

Here is an illustration of the vulnerable commit (the true branch name is not `some_branch`):

```mermaid
gitGraph
       branch some_branch
       checkout some_branch
       commit id: "commit1"
       commit id: "VULNERABLE_COMMIT"
       commit id: "commit 2"
       commit id: "commit 3"
```

And after your fix: 

```mermaid
gitGraph
       branch some_branch
       checkout some_branch
       commit id: "commit1"
       commit id: "some other commit 2"
       commit id: "some other commit 3"
```

Note that the commits coming before the vulnerable commit should remain untouched (like `commit1`),
while commit coming after the vulnerable commit might change (like `some other commit 2` and `some other commit 3`, instead of `commit 2` and `commit 3`).

Commit-wise, you are free to do whatever you wish for the commits that are coming after the vulnerable commit, as far as **the content of the branch remain the same**. 
The branch content should be identical to what it was before your fix, except the vulnerable file that was committed in the `VULNERABLE_COMMIT` commit.  

There are many approaches to solve it, some are using `git reset --hard`, `git rebase` or `git cherry-pick`. Find your preferred way.
You should find the branch contains the vulnerable data, learn its structure and data, and remove the vulnerable commit carefully, without loosing data committed in other commits. 

Since you've changed the commit history, you may be needing to `--force`fully push your fixed branch to remote. 

In order to prevent this vulnerability in the future, integrate [pre commit](https://pre-commit.com/) into your repo, and add a plugin that blocks any commits that contains AWS credentials data.
Verify that the tool is working - try to commit the below text and make sure pre-commit is blocking you.
If you were able to commit it, `git reset` your working branch to the commit before the vulnerable commit, and try again.  

### Test it locally

```bash
git checkout main
bash test/sensitive_data.sh
```


[DevOpsTheHardWay]: https://github.com/alonitac/DevOpsTheHardWay
[onboarding_tutorial]: https://github.com/alonitac/DevOpsTheHardWay/blob/main/tutorials/onboarding.md
[autotest_badge]: ../../actions/workflows/project_auto_testing.yaml/badge.svg?event=push
[autotest_workflow]: ../../actions/workflows/project_auto_testing.yaml/
[fork_github]: https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo#forking-a-repository
[clone_pycharm]: https://www.jetbrains.com/help/pycharm/set-up-a-git-repository.html#clone-repo
[github_actions]: ../../actions
[github_repo]: ../../

