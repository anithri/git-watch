# watch-git
A set of scripts and config to privde a git status summary in i3blocks or similar tool.

### watch-git
watch-git is a shell script that handles setting and viewing the currently watched git repo.

set ```ENV WATCH_GIT_REPO_FILE=<path>``` to overide the default file of ```~/.watch_git_repo```

```console
$ pwd
/home/anithri/workspace/watch-git

$ watch-git help
USAGE:
  watch-git                    # => same as $cmd get all
  watch-git get all
  watch-git get name
  watch-git get repo
  watch-git set <path>         # => name defaults to basename <path>
  watch-git set <name> <path>

$ watch-git set .
$ watch-git get all
watch-git
/home/anithri/workspace/watch-git

$ watch-git get name
watch-git

$ watch-git get repo
/home/anithri/workspace/watch-git

$ cd /
$ watch-git set playspace/another_project
$ watch-git get all
another_project
/home/anithri/playspace/watch-git

$ watch-git set favorite /home/anithri/batcave
$ watch-git get all
favorite
/home/anithri/batcave
```

### watch-git-status
watch-git-status is a ruby script that formats the result of a ```git status --porcelain``` in the repo stored
in the repo file.  I did it in ruby because of the string processing I use, feel free to reimplement in the language of your choice.

The default format uses '+' for added and added/modifed. '~' for modified, '?' for untracked, and '-' for deleted.

```console
$ watch-git set uber-project .
$ watch-git-status
uber-project +3 ~4 ?3 -0
```

You can use the name of any of the format variables on it's own to return only that variable

```
$ watch-git-status name
uber-project
$ watch-git-status added
3
$ watch-git-status modified
4
$ watch-git-status untracked
3
$ watch-git-status delted
0
```

You can pass it a format string to return the result of the format.
The format variables are: name, added, modified, untracked, deleted.
use them in a string by surrounding the var name with
````'%{}'```: ```%{name} has %{added} new files!```

```
$ watch-git-status "%{name}: +%{added} ~%{modified} ?%{untracked} -%{deleted}"
uber-project +3 ~4 ?3 -0
```

### i3blocks
Here is a simple configuration to use the scripts to track your repo

```ini
[git_repo]
command=~/bin/watch-git-status
interval=30
```

And here is a more complicated one to use different colors for each

```ini
[git_repo]
command=~/bin/watch-git get name
interval=15
separator_block_width=3
separator=false

[git_added]
interval=15
command=~/bin/watch-git-status "+%{added}"
color=#00FF00
separator_block_width=3
separator=false

[git_modified]
interval=15
command=~/bin/watch-git-status "~%{modified}"
color=#00ffff
separator_block_width=3
separator=false

[git_untracked]
interval=15
command=~/bin/watch-git-status "?%{untracked}"
color=#FFFF00
separator=false

[git_deleted]
interval=15
command=~/bin/watch-git-status "-%{deleted}"
color=#FF0000
separator_block_width=3
```
