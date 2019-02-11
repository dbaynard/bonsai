---
title:  Reviewing PRs on github  
date:   11 Feb 2019  

description: |
  A short script to fetch PR branches into a local repository of a github project.
#cover:
#  src: /assets/2019-01-29-haggis-scoticus.svg
#  alt: An outline of a wild haggis.
#  caption: |

categories:
- programmes
- programming
...

```zsh
#!/usr/bin/env zsh
#
# Review github PRs.
#
# This script makes reviewing github PRs simple. Supply the requester's branch
# in the form it is presented at the top of the PR on github (i.e. user:branch).
# This adds:
#
# - a new remote corresponding to the user, with only the PR branch
# - a new local branch (unless a branch with that name exists already, in
#   which case it refuses to check out a branch).
#
# Supplying an optional argument that is the name you wish to give your branch
# will force that branch to be used, even if you already have a branch with that name.
#
# Use: git-review user:their-branch [my-branch]
#
# or add an alias to gitconfig to use as 'git review'
#
# > git config alias.review '!git-review'

user="${1%:*}"
their_branch="${1#*:}"

local_repo="$(git rev-parse --show-toplevel 2> /dev/null)"
repo="${local_repo:t}"

add_remote()
{
    # git fetch "git@github.com:$user/$repo" "$branch" && git checkout -b "$branch" FETCH_HEAD
    echo "Adding remote github branch $their_branch of repo $repo belonging to user $user."
    git remote add -t "$their_branch" "$user" "git@github.com:$user/$repo" || git remote set-branches --add "$user" "$their_branch"
    echo "Fetching remote branch $user/$their_branch (and any other already added $user branches)."
    git fetch "$user"
}

create_their()
{
    add_remote
    echo "Creating local branch $their_branch"
    git branch "$their_branch" "$user/$their_branch" && checkout "$their_branch"
}

create_my()
{
    add_remote
    echo "Creating local branch $my_branch"
    git branch -f "$my_branch" "$user/$their_branch"
    checkout "$my_branch"
}

checkout()
{
    echo "Checking out local branch $1"
    git checkout "$1"
}

case "$#" in
    1)
        create_their
        ;;
    2)
        my_branch="$2"
        create_my
        ;;
    *)
        echo "Invalid syntax. Use 'git-review user:their_branch [my_branch]'"
        exit 3
esac

echo "Done"
```
