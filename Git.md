# Git (advanced)

- [Search "foo" in history](#search-foo-in-history)
   * [Search for commit content (eg source code)](#search-for-commit-content-eg-source-code)
   * [Search in the commit history (eg commit messages)](#search-in-the-commit-history-eg-commit-messages)
- [Rename a branch that was already pushed to remote](#rename-a-branch-that-was-already-pushed-to-remote)
- [Squash](#squash)
- [Delete all local branches but main](#delete-all-local-branches-but-main)
- [Rewrite history](#rewrite-history)
   * [Rebase interactive](#rebase-interactive)
   * [Remove sensitive information](#remove-sensitive-information)

## Search "foo" in history

### Search for commit content (eg source code)

---

ℹ️ The search term can be a string or a regex.

---

Search for the first occurrence of a term in the working directory.

`git grep foo`

Search for all occurrences.

`git grep foo $(git rev-list --all)`

Limit to some subtree in your project (only return revisions for changes affecting the mentioned subtree, and limit grep to that subtree):

`git grep foo $(git rev-list --all -- subtree/subsubtree) -- subtree/subsubtree`

### Search in the commit history (eg commit messages)

This example command searches for the string "foo" in commits made after Jan 1st, 2009 until Jan 1st, 2010 and restricts search to the specified path. Parameters are optional.

`git log -Sfoo --since=2009.1.1 --until=2010.1.1 -- path_containing_change`

## Rename a branch that was already pushed to remote

Rename the local branch to the new name 
 
`git branch -m <old_name> <new_name>` 
  
Delete the old branch on remote - where <remote> is, for example, origin 

`git push <remote> --delete <old_name` 
 
Or shorter way to delete remote branch: 
 
`git push <remote> :<old_name>`
 
Prevent git from using the old name when pushing in the next step. Otherwise, git will use the old upstream name instead of <new_name>. 
 
`git branch --unset-upstream <new_name>` 
 
Push the new branch to remote 
`git push <remote> <new_name>` 
 
Reset the upstream branch for the new_name local branch 

`git push <remote> -u <new_name>`

## Squash

Move HEAD back x + 1 commits (HEAD will be just before the last 2 commits), undo ahead changes and re-add the changes as staged, ready to be committed again (changes are not lost).

---

⚠️ Using `reset --hard` means changes are lost.

---

`git reset --soft HEAD~2`

Alternative: Move HEAD to commit <sha>, undo ahead changes and re-add the changes as staged, ready to be committed again (changes are not lost).

`git reset --soft <sha>,HEAD`

Re-commit changes as desired

`git commit -m "new commit message"`

Push changes (if changes existed on remote, you'll need to force-push)

`git push -f`

## Delete all local branches but main

`git branch | grep -v "main" | xargs git branch -D`

## Rewrite history

---

⚠️ ***DANGER ZONE***

---

### Rebase interactive

Opens a file with all commits from the mentioned sha where you can rewrite the history.

`git rebase -i [commit-sha]`

### Remove sensitive information

Tool: bfg cleaner (needs Java installed)

Download the .jar from the [website](https://rtyley.github.io/bfg-repo-cleaner/)

Create an alias to be able to use the "bfg" command as shown in the tutorials (Linux env)

`nano ~/.bash_aliases => alias bfg='java -jar /your/download/path/bfg-1.14.0.jar'`

`source ~/.bash_aliases`

Create a file with the sensitive data to remove (ex passwords.txt). Expressions can be string or regex.
It can be a list of expressions to replace, which will be replaced by "***REMOVED***" by default, or you can specify "password123=>custom_placeholder" to overwrite the default placeholder.

In the local repository, use this command to replace all instances of expressions found in the passwords.txt file:

`bfg --replace-text /path/passwords.txt`

bfg can also be used to remove whole files from the repository history

Finally, force push your local changes

