# Configure

## Bash

Configure color to indicate route.

### Root

Start off by copying the skeleton files ''/etc/skel/.bash_profile'' and ''/etc/skel/.bashrc'' to ''/root'', then edit ''/root/.bashrc'' as desired.
```
su root

cp /etc/skel/.bash_profile /root/

cp /etc/skel/.bashrc /root/
```

### Generate colors

Generate colors here: [[http://bashrcgenerator.com/|http://bashrcgenerator.com/]]

Test it:
```
export PS1="[\[$(tput sgr0)][\033[38;5;196m]u\[$(tput sgr0)][\033[38;5;15m\]@\h - \[$(tput sgr0)][\033[38;5;10m]w\[$(tput sgr0)][\033[38;5;15m\]]: \[$(tput sgr0)\]"
```

#### Root color

```
PS1="[033[38;5;196m]u\[$(tput sgr0)][\033[38;5;15m\]@\[$(tput sgr0)][\033[38;5;12m]h:\[$(tput sgr0)][\033[38;5;196m\][\w]:\[$(tput sgr0)][\033[38;5;15m\] \[$(tput sgr0)\]"
```

#### User color

```
PS1="[033[38;5;10m]u\[$(tput sgr0)][\033[38;5;15m\]@\[$(tput sgr0)][\033[38;5;12m]h:\[$(tput sgr0)][\033[38;5;10m\][\w]:\[$(tput sgr0)][\033[38;5;15m\] \[$(tput sgr0)\]"
```

Include git branch in git folders:
```
PS1='[\[$(tput bold)\]\[$(tput sgr0)\]\[\033[38;5;81m\]\u\[$(tput sgr0)\]\[$(tput sgr0)\]\[\033[38;5;15m\]: \[$(tput sgr0)\]\[\033[38;5;81m\]\w\[$(tput sgr0)\]\[\033[38;5;15m\]]\[\033[0;32m\]$(if git rev-parse --git-dir > /dev/null 2>&1; then echo " - ["; fi)$(git branch 2>/dev/null | grep "^*" | colrm 1 2)\[\033[0;32m\]$(if git rev-parse --git-dir > /dev/null 2>&1; then echo "]"; fi)\[\033[0m\033[0;15m\] \\$\[$(tput sgr0)\] '
```

### Do it

For both user and root:

```
nano ~/.bashrc
```

''Replace PS1''

