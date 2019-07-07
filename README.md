# git-graph

`git-graph` takes several refs, and shows the smallest (complete) picture which
involves those refs.

## Installation

Put the script somewhere in your path
```
curl 'https://raw.githubusercontent.com/cdosborn/git-graph/master/git-graph' \ 
    -o /usr/local/bin/git-graph
chmod u+x /usr/local/bin/git-graph
```
Source the bash completion in your startup files, so refs are auto-completed.
**Note:** requires existing git bash completion.
```
. <(git-graph --bash-completion)
```

## Usage
```
Print the shared history of git refs

git graph <ref> <ref> ...     # Pass several refs to graph
git graph ... -- <path>       # Filter commits which affected <path
git graph --single-parent ... # Pass options know to git log
git graph --echo ...          # Just print the underlying git command
git graph --bash-completion   # Print bash completion code
git graph --help              # Print this message
```


## Motivation

When inspecting a feature branch it's useful to visualize the history, you
might run:
```
git log --oneline --graph master feature
```

However, this will show the entire history leading back to the first commit.

You might try and hide the shared history by excluding any shared ancestors via
their merge base.
```
git log --oneline --graph $(git merge-base master feature)^! master feature
```

This doesn't always paint the complete picture.

Consider the following scenario:
```
         A <- B <- C <- D
        /         /       feature
... <- E <- F <- G
                   master
```
`G` is the merge base in this case (or the first common ancestor). By excluding its ancestors via `^!`, git log will graph:
```
   C <- D
  /       feature
 G
   master
```
However, this cleaves off the history including `B` and `A`. `git-graph` searches for `E` which is the common ancestor of all paths from master and feature.
```
  A <- B <- C <- D
 /         /       feature
E <- F <- G
            master
```

## Caveats
This is some gnarly bash
