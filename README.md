# all-about-the-linux
A place to put together useful material and tips relating to working with Linux.

### Table of Contents:
1. [Linux Architecture](#linux-architecture)
1. [Using the shell](#using-the-shell)
1. [Modifying the shell](#modifying-the-shell)
1. [Navigating in the shell](#navigating-in-the-shell)
1. [Streams and pipes](#streams-and-pipes)
1. [Shell scripting](#shell-scripting)
1. [Shell tools](#shell-tools)
1. [SSH and GitHub](#ssh-and-github)
1. [SLURM and HPC jobs](#slurm-and-hpc-jobs)
1. [Useful tutorials](#useful-tutorials)

## Linux basics

### Linux Architecture

__Kernel__ − The kernel is the heart of the operating system. It interacts with the hardware and most of the tasks like memory management, task scheduling and file management.

__Shell__ − The shell is the utility that processes your requests. When you type in a command at your terminal, the shell interprets the command and calls the program that you want. The shell uses standard syntax for all commands. C Shell, Bourne Shell and Korn Shell are the most famous shells which are available with most of the Unix variants.

__Commands and Utilities__ − There are various commands and utilities which you can make use of in your day to day activities. `cp`, `mv`, `cat` and `grep`, etc. are few examples of commands and utilities. There are over [100 standard commands](https://en.wikipedia.org/wiki/List_of_Unix_commands) plus numerous others provided through 3rd party software. All the commands come along with various options.

__Files and Directories__ − All the data of Unix is organized into files. All files are then organized into directories. These directories are further organized into a tree-like structure called the filesystem.

![alt text](https://www.tutorialspoint.com/unix/images/unix_architecture.jpg "linux-architecture")

To know more follow the [source](https://www.tutorialspoint.com/unix/unix-getting-started.htm).

### Using the shell

- Home directory and `~`

In most cases, your home directory will look something like this:

```bash
[username@pc-name ~]$
```
The `~` character indicates that you are in the home directory, whilst `&` tells that you are not the root user. At the prompt, you can type a command, i.e `date`, which will be interpreted by the shell:

```bash
[username@pc-name ~]$ date
Tue May 11 17:15:18 EDT 2021
[username@pc-name ~]$
```

You can pass certain arguments to a shell command, such as `echo`, which prints outs a passed argument:

```bash
[username@pc-name ~]$ echo Hello Linux!
Hello Linux!
[username@pc-name ~]$
```

- `echo $PATH`, `which`

But how does the shell know how to find the `date` or `echo` programs? When you run commands in your shell, you are really writing a small bit of code that your shell interprets. If the shell is asked to execute a command that doesn’t match one of its programming keywords, it consults an environment variable called `$PATH` that lists which directories the shell should search for programs when it is given a command:

```bash
[username@pc-name ~]$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
[username@pc-name ~]$ which echo
/bin/echo
[username@pc-name ~]$ /bin/echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

When we run the `echo` command, the shell sees that it should execute the program `echo`, and then searches through the :-separated list of directories in `$PATH` for a file by that name. When it finds it, it runs it (assuming the file is executable). We can find out which file is executed for a given program name using the `which` program. We can also bypass `$PATH` entirely by giving the path to the file we want to execute.

- contents of the shell

For the next command, let's type `ls -lh` to list the contents of a folder. For instance, you can observe here the contents of my home directory:

```
-rw-r--r-- 1 username usrgrp 3.5K May 11 10:34 environment.yml
drwxr-xr-x 3 username usrgrp    3 Apr 24 16:46 github/
lrwxrwxrwx 1 username usrgrp   24 Jan 26 09:03 mydata -> /apps/data/
drwxr-xr-x 3 username usrgrp    3 Jan 28 10:06 scikit_learn_data/
drwxr-xr-x 2 username usrgrp    4 Nov 30 09:21 scripts/
drwxr-xr-x 3 username usrgrp    3 Nov  6  2020 workshops/
```

To understand what does it mean, check the following example:

```
-rwxrw-r--    10    root   root 2048    Jan 13 07:11 afile.exe
?UUUGGGOOOS   00  UUUUUU GGGGGG ####    ^-- date stamp and file name are obvious ;-)
^ ^  ^  ^ ^    ^      ^      ^    ^
| |  |  | |    |      |      |    \--- File Size
| |  |  | |    |      |      \-------- Group Name (for example, Users, Administrators, etc)
| |  |  | |    |      \--------------- Owner Account
| |  |  | |    \---------------------- Link count (what constitutes a "link" here varies)
| |  |  | \--------------------------- Alternative Access (blank means none defined, anything else varies)
| \--\--\----------------------------- Read, Write and Special access modes for [U]ser, [G]roup, and [O]thers (everyone else)
\------------------------------------- File type flag
```

In the order of ouput:

- File permissions (`-rwxrw-r--`),
- Number of (hard) links (`1`),
- Owner name (`root`),
- Owner group (`root`),
- File size in bytes (`2048`),
- Time of last modification (`Jan 13 07:11`)
- File/directory name (`afile.exe`)

File permissions is displayed as following;

- First character is most often `-`, `l` or `d`. A `d` indicates a directory, a `-` represents a regular file, `l` is a symlink (or soft link) and other letters are used for other types of special files.
- Three sets of characters, three times, indicating permissions for owner, group and other:
        `r` = readable
        `w` = writable
        `x` = executable (for files) or accessible (for directories)
- This may be followed by some other character of there are extended permissions, like e.g. Linux ACL that are marked with a `+`.

In our example `-rwxrw-r--`, this means the line displayed is:

- A regular file (displayed as `-`)
- Readable, writable and executable by owner (`rwx`)
- Readable, writable, but not executable by group (`rw-`)
- Readable but not writable or executable by other (`r--`)

The number of hard links means the number of names the inode has, i.e. links created with `ln` without the `-s` option.

For more information, check this [link](https://unix.stackexchange.com/questions/103114/what-do-the-fields-in-ls-al-output-mean).

### Navigating in the shell

- `pwd`

Print name of current/working directory.

```bash
[username@pc-name ~]$ pwd
/home/username
```
- `cd`

Change a directory.

```bash
[username@pc-name ~]$ cd /home
[username@pc-name ~]$ pwd
/home
[username@pc-name ~]$ cd ..
[username@pc-name ~]$ pwd
/
[username@pc-name ~]$ cd ./home
[username@pc-name ~]$ pwd
/home
[username@pc-name ~]$ cd
[username@pc-name ~]$ pwd
/home/username
[username@pc-name ~]$ cd ../../
[username@pc-name ~]$ pwd
/
[username@pc-name ~]$ cd ~/
[username@pc-name ~]$ pwd
/home/username
```

A path on the shell is a delimited list of directories; separated by `/` on Linux and macOS and `\` on Windows. On Linux and macOS, the path `/` is the “root” of the file system, under which all directories and files lie, whereas on Windows there is one root for each disk partition (e.g., `C:\`). We will generally assume that you are using a Linux filesystem in this class. A path that starts with `/` is called an absolute path. Any other path is a relative path. Relative paths are relative to the current working directory, which we can see with the pwd command and change with the `cd` command. In a path, `.` refers to the current directory, and `..` to its parent directory

For more information on what can you find the root `/` directory, check this [YouTube video](https://www.youtube.com/watch?v=42iQKuQodW4).

- `ls`

List the contents of the folder. Some useful flags:

```
  -a, --all                  do not ignore entries starting with .
  -d, --directory            list directories themselves, not their contents
  -h, --human-readable       with -l, print sizes in human readable format
                               (e.g., 1K 234M 2G)
```

For more options, type `ls --help`.

- `chmod`

You can change permissions, by typing the following commands:

```
chmod +rwx filename                     to add permissions.
chmod -rwx directoryname                to remove permissions.
chmod +x filename                       to allow executable permissions.
chmod -wx filename                      to take out write and executable permissions
```

To be more specific, use `u` for users, `u` for group, `o` for others, and `ugo` or `a` (for all):

```
chmod ugo+rwx foldername                to give read, write, and execute to everyone.
chmod a=r foldername                    to give only read permission for everyone.
```

To change permissions in numeric code in Linux, you use numbers instead of `r`, `w`, or `x`.

```
0 = No Permission
1 = Execute
2 = Write
4 = Read
```

Basically, you add up the numbers depending on the level of permission you want to give. Permission numbers are:

```
0 = ---
1 = --x
2 = -w-
3 = -wx
4 = r-
5 = r-x
6 = rw-
7 = rwx
```

For example:

```
chmod 777 foldername                    will give read, write, and execute permissions for everyone.
chmod 700 foldername                    will give read, write, and execute permissions for the user only.
chmod 327 foldername                    will give write and execute (3) permission for the user, w (2) for the group, and read, write, and execute for the users.
```

Other tools, such as `chgrps` and `chown` allow to change groups of files and ownership, respectively.

For more about permissions, follow the [link](https://en.wikipedia.org/wiki/File-system_permissions).

- `whoami`

Print a current effective userid:

```bash
[username@pc-name ~]$ whoami
your-username
```

- `touch`

Create a single empty file.

- `cp`

Copy a file from one location to another. To copy folders, you need to add `-r` flag to perform a recursive copy.

- `mv`

Move a folder to a different location or rename a file in place.

- `mkdir`

Create a folder.

- `rm`

Remove a file. To remove a folder, you need to add a recursive `-r` flag. Sometimes, you might need to force the removal, so add `-f` flag. But be careful, every operation with `rm` is irreversable!

- Tab autocompletion and right/middle mouse button copy.

Use `tab` button to autocomplete paths, command names and search for file names. In addition, use right/middle mouse (depending on the system) button for quick copy paste actions.

- See the command history.

Press `up` and `down` buttons to cycle through your command history. If you have trully spammed a lot of commands, perhaps an easier way would be to read `~/.bash_history` file. Or press `ctrl + r` for recursive command history search.

- Display the contents of a file.

Commands like `cat`, `less` or `more` are useful. Run the separately and observe the behaviour. For more information read the `man` pages.

- Display only the first or last 5 lines.

Commands like `head` and `tail` together with the flag `-5` would do the job. Figure out which one does what.

### Modifying the shell

- `.bashrc` and `.bash_profile`

`.bash_profile` is executed for login shells, while `.bashrc` is executed for interactive non-login shells.

When you login (type username and password) via console, either sitting at the machine, or remotely via `ssh`: `.bash_profile` is executed to configure your shell before the initial command prompt.

But, if you’ve already logged into your machine and open a new terminal window (xterm) then `.bashrc` is executed before the window command prompt. `.bashrc` is also run when you start a new bash instance by typing `/bin/bash` in a terminal.

Let's take a look at the contents of `.bash_profile`:

```bash
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/.local/bin:$HOME/bin

export PATH

```

The `if` statement checks whether `.bashrc` exists and sources it. In addition, the `.bash_profile` exports, using `export` funtion, the path to the shell.

And now at the `.bashrc`:

```bash
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

# Uncomment the following line if you don't like systemctl's auto-paging feature:
# export SYSTEMD_PAGER=

# User specific aliases and functions
module load gcc

if [ -e $HOME/.bash_aliases ]; then
    source $HOME/.bash_aliases
fi

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/apps/2018/anaconda3/v5.0.1b.app/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/apps/2018/anaconda3/v5.0.1b.app/etc/profile.d/conda.sh" ]; then
        . "/apps/2018/anaconda3/v5.0.1b.app/etc/profile.d/conda.sh"
    else
        export PATH="/apps/2018/anaconda3/v5.0.1b.app/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<

```

The `if` statement checks whether `.bashrc` exists in the `/etc` directory and sources it. In addition, the file loads a few modules, sources `.bash_aliases` and initializes a `conda` environment. You can comment out each block to stop the sourcing and initialization. 

Finally, let's see the `.bash_aliases` file:

```bash
# User defined aliases

# Listing files and directories
alias ls='ls -F --color=auto'
alias l='ls -lh --color=auto'

# Copying files with a progress bar
alias cpv='rsync -ah --info=progress2'

# SLURM
alias sfeatures='sinfo -N --format="%N %f" | sort -u'
alias sfreecores="sinfo -o %P:%b:%C | column -t -s ':/'"
alias sfreenodes="sinfo -o %P:%b:%A | column -t -s ':/'"
alias sfreetotal="sinfo -o %P:%C | column -t -s ':/'"

```

In either `.bashrc` or `.bash_profile`, you can added custom paths to directing to a directory with a set of executables:

```bash
PATH=$PATH:~/my/dir/bin
```
or
```bash
PATH=~/my/dir/bin:$PATH
```

depending on whether you want to add `~/my/dir/bin` at the end (to be searched after all other directories, in case there is a program by the same name in multiple directories) or at the beginning (to be searched before all other directories).

- `alias`

`alias` keyword allows to set a "shortcut" to a specific command that you would like to use more often, but cannot be bothered to type it out everytime.

### Streams and pipes

My first ever example about streams and pipes that motivated me to pick up Linux:

For the start, lets download a file using `wget` command and inspect its contents:

```bash
wget 'www.pdb.org/pdb/files/1PNK.pdb'
cat 1PNK.pdb
```

The PDB files contain a description of a protein structure and its surrounding water, if it is a crystal structure. Sometimes, we want to extract only protein coordinates and leave the water out.


Let's select lines using `grep` corresponding to protein coordinates. The help options tell me that `grep` works as: 

```
grep [OPTION]... PATTERN [FILE]...
```

Thus, we write:

```bash
grep ATOM 1PNK.pdb
```

However, how we can create a file out of what was selected? Run this:

```bash
grep ATOM 1PNK.pdb > protein_only.pdb
```

But we have lost part of header! Instead let's search for lines corresponding to water:

```bash
grep HOH 1PNK.pdb 
```

Good, but we are not selecting lines of interest! Use `-v` flag (invert match) with `grep` command:

```bash
grep -v HOH 1PNK.pdb 
``` 

Voila! And now stream it into the file:

```bash
grep ATOM 1PNK.pdb > protein_only.pdb
```

Can we use `grep` twice in an oneliner? Use a pipe `|`:

```bash
grep ATOM protein_only.pdb | grep HIS
```

### Shell scripting

Shell scripting is a powerful way to perform a variety of actions, including submitting cluster jobs, modifying files or running a certain software.

Let's investigate a script that allows to rename a set of files:

```bash
#!/bin/bash

if [ $# -ne 2 ]
then
echo "Incorrect number of arguments..."
echo "Usage: rename <pattern> <target>"
exit 1
fi

# Define variables
pattern=$1
target=$2

# Rename each file with a particular pattern in a directory
for f in $pattern*
        do mv "$f" $(echo "$f" | sed "s/^$pattern/$target/g")
done
```

In the beginning, one can see the shebang `#!`, which is a special character sequence in a script file that specifies which program should be called to run the script. The shebang is always on the first line of the file, and is composed of the characters `#!` followed by the path to the interpreter program. 

After, `if` statement checks how many arguments were passed to the script. If only one or three or more variables were passed, it will output an error saying `Incorrect number of arguments..."`

Then, we define the passed variable names. Once a variable is defined, it is called again with an `$`.

In the `for` loop, we are iterating over each file containg the query pattern and at each iteration we perform a renaming using `mv` and `sed` commands. More magic with `sed` @ [Batch Renaming Using sed](http://www.guyrutenberg.com/2009/01/12/batch-renaming-using-sed/).

For this script to work, you need to create a file `rename.sh` (keep in mind the ending `.sh`), which denotes that it is a `bash` script:

```bash
touch rename.sh
```

For the script to be executable, run the `chmod` command:

```bash
chmod 711 rename.sh
```

And populate the script using `vim`.

Once completed, run the script:

```bash
./rename.sh pattern1 pattern2
```

If it is lengthy process, put it into background by appending `&`:

```bash
./lengthy_process.sh &
```

Sometimes you want to run to scripts one after another, so just add `&&` between the scripts:

```bash
./script1.sh && ./script2.sh
```

### Shell tools

- `ln`

A link in UNIX is a pointer to a file. Creating links is a kind of shortcuts to access a file. Links allow more than one file name to refer to the same file, elsewhere. There are two types of links, soft (symbolic) and hard links.

These links behave differently when the source of the link (what is being linked to) is moved or removed. Symbolic links are not updated (they merely contain a string which is the path name of its target); hard links always refer to the source, even if moved or removed. 

To create a hard link:

```
ln  [original path to a filename or a directory] [link name]
```

To create a soft link:

```
ln  -s [original path to a filename or a directory] [link name]
```

- `grep`

Is able to find query matches in a filename. Please see above in _Section 4. Streams and pipes_.

- `find`

Search for files in a directory hierarchy, for instance search for a specific file by use of its name, but looking for those that are 100 megabytes or larger:

```bash
find $HOME -iname '*.ogg' -size +100M
```

- `man`

See the manual page of a given command. If you want to get a quick and short help, try to add `-h` or `--help` flags.

```bash
man ls
```

- `curl` and `wget`

Allows you to download files. Extremely useful with multiple large files. Can connect to `ftp` repositories. For instance, large sequence databases from [UniProt](https://ftp.uniprot.org/pub/databases/uniprot/uniref/uniref100/) can be downloaded to a remote server.

```bash
wget ftp://ftp.uniprot.org/pub/databases/uniprot/uniref/uniref100/uniref100.fasta.gz
```

- `tree`

Allows to show a complete directory structure.

- `screen` and `tmux`

Allow to perform screen multiplexing. Useful when running long processes on the cluster and a stable connection is needed.

For more information, [screen cheat sheet](https://kapeli.com/cheat_sheets/screen.docset/Contents/Resources/Documents/index).

- `scp`

Securely copy files from your local machine to the server:

```bash
scp filename username@servername:/path/to/a/directory
```

Instead of the `server`, you could have an IP address.

Want to download a file from a server?

```bash
scp username@servername:/path/to/a/directory/filename .
```

- `rsync`

Same as `scp`, however has advantage over `scp` when downloading multiple files with a shaky connection. Once the internet connection is down, you will need to re-download all the files or download only those that have not been downloaded. Doing this task with `scp` is cumborsome, as you need to know the correct filenames. However, `rsync` keeps the track of the files that were or needs to be downloaded:

For instance, send over a set of files to a cluster:

```bash
rsync --progress --partial -R * username@servername:/path/to/desired/location/ --append --verbose
```

Where:

```
--progress              show progress during transfer
--partial               keep partially transferred files
--append                append data onto shorter files
--verbose               increase verbosity
```

`--partial` just tells the receiving end to keep partially transferred files if the sending end disappears as though they were completely transferred. Later you can complete the transfer by running `rsync` again with either `--append`. For a more in-depth discussion, follow this [link](https://unix.stackexchange.com/questions/48298/can-rsync-resume-after-being-interrupted).

- `du`

The `du` command is a standard Linux/Unix command that allows a user to gain disk usage information quickly. It is best applied to specific directories:

```bash
du -h /directory
```

- `df`

The `df` command (short for disk free) is used to show the amount of free disk space available on Linux and other Unix-like systems and to understand the filesystems that have been mounted. `df` displays the amount of free space on the file system containing each file name argument.

```bash
df -h
```

- `diff`

Compare the contents of two files:

```bash
diff filename1 filename2
```

- Text editors for Linux/Unix command line:

Check out `vi`, `vim`, `emacs`, `nano` etc.

### SSH and GitHub

SSH (Secure Shell) is a network protocol that enables secure remote connections between two systems. System admins use SSH utilities to manage machines, copy, or move files between systems.

The SSH settings are located in the `.ssh` folder:

```
drwxr-xr-x    1 username UsersGrp         0 Mar  4 14:02 .
drwxr-xr-x    1 username UsersGrp         0 May 12 22:15 ..
-rw-r--r--    1 username UsersGrp       220 Feb 16 10:44 config                 # Configuration file
-rw-r--r--    1 username UsersGrp      1679 Apr  1 14:25 id_rsa                 # Private key
-rw-r--r--    1 username UsersGrp       404 Apr  1 14:25 id_rsa.pub             # Public key
-rw-r--r--    1 username UsersGrp      2004 Apr  1 14:59 known_hosts            # Previously made connections
```

In your home directory, create a unique keys for yourself:

```bash
ssh-keygen -t rsa
```

To connect to GitHub, provide your public key to the GitHub SSH settings to be able to pull or push to repositories.

The `config` file can be further modified further to contain the following information:

```
Host *
        ForwardX11 yes
        ForwardX11Trusted yes
        ServerAliveInterval 30
        ServerAliveCountMax 3

Host servername
        User username
        hostname server.address.com

```

The `Host *` applies settings to all connections. The settings, the `ForwardX11` options allow you to run graphics tools remotely on a cluster. The `ServerAlive` options keep your connection alive for a while, whilst reconnecting if the internet is down.

Can't be bothered with writing login names everytime you `ssh`? Define a `Host` name, which allows me to do simply:

```bash
ssh servername
```

Instead of running:

```bash
ssh username@server.address.com
```

Same with `scp` or `rsync` commands.

### SLURM and HPC jobs

To run a job on the HPC, you need to create a script `slurm_job.sh`:

```bash
#!/bin/bash
#SBATCH --job-name=title
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --cpus-per-task=16
#SBATCH --mem-per-cpu=9GB
#SBATCH --partition=up-gpu
#SBATCH --gres=gpu:1
#SBATCH --time=1-00:00:00
#SBATCH -e error.e
#SBATCH -o output.o

# Activate environment and set path
module load anaconda3
source activate env-name

# Run a script
echo "Hello wanderer!"
```

And running using `sbatch` command:

```bash
sbatch slurm_job.sh
```

To check whether your job is running, run `sinfo` command:

```bash
sinfo -u userid
```

To cancel a job, run `scancel` command:

```bash
scancel jobid
```

### Useful tutorials

- [The missing CS lecture](https://missing.csail.mit.edu/2020/).

## Fin

As always, it is only a tip of the iceberg. Keep exploring!
