## Linux user with limited permissions

# The idea and the purpose

In this article I want to answer this question: *How can I restrict the normal user to run only limited set of commands*.

So I describe *step-by-step* the method to create a linux user able to access to the console of a linux server but with limited permission to run commands.
This method is useful when, for example, you want to give access to one of your collaborators without risking that he can launch commands as an administrator.

The idea is to use the [restricted shell](https://www.gnu.org/software/bash/manual/html_node/The-Restricted-Shell.html) `rbash` and declare which commands the user are able to run.

<hr>

# The step-by-step guide


1. create the new user
```bash
adduser <username> 
```
compile all the requested fields. The most important is the password that will be used to access the server (for example in SSH).

1. set the `rbash` as shell for the new user
```bash
chsh -s /bin/rbash <username>
```

1. create a bin directory under the user home directory
```bash
mkdir /home/<username>/bin
chmod 755 /home/<username>/bin/
```

1. change the user's default PATH to the bin directory
```bash
echo "PATH=$HOME/bin" >> /home/<username>/.bashrc
echo "export PATH" >> /home/<username>/.bashrc
```

1. create symlinks of the command(s) that the user require to bin directory under the user home directory
```bash
ln -s /bin/ping /home/<username>/bin/
ln -s /bin/traceroute /home/<username>/bin/
ln -s /bin/htop /home/<username>/bin/
ln -s /bin/top /home/<username>/bin/
ln -s /bin/df /home/<username>/bin/
ln -s /bin/bmon /home/<username>/bin/
ln -s /bin/ps /home/<username>/bin/
ln -s /bin/date /home/<username>/bin/
ln -s /bin/netstat /home/<username>/bin/
ln -s /bin/free /home/<username>/bin/
```

1. restrict the user from modifying `~/.bashrc`
```bash
chattr +i /home/<username>/.bashrc
```

These are the basic steps to achieve the purpose.
However, I want to add one more of non-core but stylistic step: print the list of available commands when the user login in console.
For this purpose I user [motd](https://en.wikipedia.org/wiki/Message_of_the_day).

1. create motd `99-available-commands` 
```bash
touch /etc/update-motd.d/99-available-commands
```

1. add the list of available commands
```bash
cat << EOF >> /etc/update-motd.d/99-available-commands
#!/bin/sh
export TERM=xterm-256color
echo "$(tput setaf 4)-------------------------------------------------------------------------------
-- Available commands:
-------------------------------------------------------------------------------$(tput setaf 7)
  bmon          - Bandwidth monitor and rate estimator
  date          - Display the current time or system date
  df            - Report file system disk space usage
  free          - Display amount of free and used memory in the system
  htop          - Interactive process viewer
  netstat       - Display ethernet and protocol info and statistics
  ping          - Verify IP level connectivity
  ps            - Display information about running processes
  top           - Display Linux processes
  traceroute    - Trace the path/hop from one network to another
$(tput setaf 4)-------------------------------------------------------------------------------$(tput setaf 7)
"
EOF
```

1. set the right permission to the file
```bash
chmod 755 /etc/update-motd.d/99-available-commands
```

Now the user when logging in will see a list of enabled commands like this
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662106238212/yOB8bWzGl.png align="left")

<hr>

# Thanks to
- [Victor Wong](https://stackoverflow.com/users/7323888/victor-wong) for you comment in [this StackOverflow discussion](https://stackoverflow.com/questions/21498667/how-to-limit-user-commands-in-linux)
- my [evoseed](https://evoseed.io/) team
