---
layout: post
---

When I first installed Git I did not really know what it was for besides interacting with Github. I learned the basics and got back to whatever project I was working on. Beyond cloning, committing, and pushing my Git skillset did not advance much over time.

Recenty I fell into a challening development situation that prompted me to learn more about Git and version control workflows. I read through the excellent and free [Pro Git](https://git-scm.com/book/en/v2) book. This text covers the full spectrum of Git, from the basics of branching through Git's internal state.

One of the actionable learnings is how to integrate Git with Bash to smooth out workflows and increase project awareness. Below are some notes on how to:

1. Enable Git command tab completion
2. Add Git status info the the prompt
3. Set aliases to streamline commands

## Get source code

In order to set up tab completion and decorate the prompt we need two scripts packaged with the Git source code:

1. Clone a copy of Git source from [Github](https://github.com/git/git)
2. Change into the cloned Git directory
3. Checkout the tag corresponding to the installed Git version
4. Copy `git-completion.bash` to home 
5. Copy `git-prompt.sh` to home 

```bash
mlr@pop-os:~/Desktop$ cd git
```

```bash
mlr@pop-os:~/Desktop/git $ git version
git version 2.34.1
```

```bash
mlr@pop-os:~/Desktop/git $ git checkout tags/v2.34.1
HEAD is now at e9d7761bb9 Git 2.34.1
```

```bash
mlr@pop-os:~/Desktop/git $ cp contrib/completion/git-completion.bash ~/
```

```bash
mlr@pop-os:~/Desktop/git $ cp contrib/completion/git-prompt.sh ~/
```

## Add tab completion

To set up tab completion the `git-completion.bash` script needs to be referenced in the `.bashrc`:

```bash
# git completion
. ~/git-completion.bash
```

This line can be added where ever it makes sense. I added the reference to the bottom of the configuration file.

Now source the `.bashrc` and confirm tab completion:

```bash
mlr@pop-os:~$ source .bashrc
```

```bash
mlr@pop-os:~$ git cl<tab>
clean   clone
```

## Decorate prompt

To decorate the shell prompt the `git-prompt.sh` script also needs to be referenced in the `.bashrc`:

```bash
# git prompt
. ~/git-prompt.sh
```

After the script reference set an environment variable to add status markers to the prompt:

```bash
# set status marker
export GIT_PS1_SHOWDIRTYSTATE=1
```

Next locate the `PS1` prompt string in the `.bashrc`, move it to the end of the file, and append the Git prompt format string to it:

```bash
# set PS1 
export PS1='$<current PS1>$(__git_ps1 " (%s)")\$ '
```

This part may require some trial and error to clearly append Git information to the current prompt.

Checkout the comments in `git-prompt.sh` for more environment variables and general notes.

Now `source` the `.bashrc` and change into a directory with a repository. Confirm that a current branch shown with some sort of marker:

```bash
mlr@pop-os:~/Desktop/rckwzrd.github.io (gh-pages *)$ 
```

## Set Aliases

Finally, aliases can be set with `git config` to shorten or combine commands. 

The status command can be shorted with:

```bash
git config --global alias.st status
```

A log command that displays the last 10 commit messages can be created with:

```bash
git config --global alias.ol log '--pretty=oneline -10'
```




