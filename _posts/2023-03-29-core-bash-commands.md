---
layout: post
basic-link: https://www.tjhsst.edu/~dhyatt/superap/unixcmd.html
tlcl-link: https://linuxcommand.org/tlcl.php

---

Something about how this is my obligatory linux command line reference. Work in a bash terminal in an almost daily basis. Here is a selection of the most common commands in my bash history. Due to there frequency I would consider them to be core and worth compiling here.

In no particular order here the core bash commands broken into categories.

Many of these core commands are referenced in the linux guides that populate the internet. Two of my favorite resources are:

- [Some Basic Unix Commands]({{page.basic-link}})
- [The Linux Command Line, 5th Internet Edition]({{page.tlcl-link}})

## Getting help

```
man     # manual help about a command
apropos # search manual for help
```

## Interacting with directories

```
pwd      # print working directory
ls:      # list files in current directory 
ls -alF  # list files in long format
cd       # change directory
cd ..    # move up one directory
pushd    # push directory
popd     # pop directory
mkdir    # make a directory
mkdir -p # make a directory and parents
rmdir    # remove an empty directory
rmdir -p # remove empty directory and parents
```

## Interacting with files

```
cp             # copy a file or directory
rm             # remove a file
rm -rf         # recursively remove files and directories
mv             # move a file or directory
less           # page through a file
cat            # print whole file
zip -r <dir>   # zip a directory of files
unzip <.zip>   # unzip files into current directory
gzip -kr <dir> # gzip a directory of files
gunzip <.gz>   # unzip into current directory
curl -O <url>  # download a file from URL and save as root
```
## Finding and searching

```
history                  # print terminal command history
grep <str> <files>       # search files for text
find <dir> -name <file>  # search directory for file
```

## Environment and arguements

```
env    # look at environment variables
export # set a new environment variable
echo   # print arguements
xargs  # execute arguements:
```

## Permissions and execution

```
sudo             # super user do
source <file>    # execute commands from file in current shell 
chmod 644 <file> # change file permissions to read only
chmod 755 <file> # change file permissions to executable
chomd u+x <file> # make file executable for user
chomd a-x <file> # remove file executable for user
```

## System and processes

```
exit           # exit shell
df             # print free disk space
du             # print directory disk usage
top            # print system usage and processes
ps aux         # list all running process with ID
kill -9 <id>   # kill process with ID
apt update     # download updates for all installed packages
apt upgrade    # install updates for all installed packages
apt list       # list available packages
apt install    # install package
apt remove     # uninstall package
apt purge      # uninstall package and configuration
apt autoremove # uninstall unused dependancies
```
