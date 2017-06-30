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

To test it, type the following - everything after ''PS1="'' is custom:
```
 export PS1="[\[$(tput sgr0)][\033[38;5;196m]u\[$(tput sgr0)][\033[38;5;15m\]@\h - \[$(tput sgr0)][\033[38;5;10m]w\[$(tput sgr0)][\033[38;5;15m\]]: \[$(tput sgr0)\]"
```

#### Root color

<file>
PS1="[033[38;5;196m]u\[$(tput sgr0)][\033[38;5;15m\]@\[$(tput sgr0)][\033[38;5;12m]h:\[$(tput sgr0)][\033[38;5;196m\][\w]:\[$(tput sgr0)][\033[38;5;15m\] \[$(tput sgr0)\]"
</file>

#### User color

<file>
PS1="[033[38;5;10m]u\[$(tput sgr0)][\033[38;5;15m\]@\[$(tput sgr0)][\033[38;5;12m]h:\[$(tput sgr0)][\033[38;5;10m\][\w]:\[$(tput sgr0)][\033[38;5;15m\] \[$(tput sgr0)\]"
</file>

Something wron with colors above.. try

User: export PS1="\[\033[38;5;10m\]\u\[$(tput sgr0)\]\[\033[38;5;15m\]@\[$(tput sgr0)\]\[\033[38;5;12m\]\h\[$(tput sgr0)\]\[\033[38;5;4m\]:\[$(tput sgr0)\]\[\033[38;5;10m\][\w]:\[$(tput sgr0)\]\[\033[38;5;15m\] \[$(tput sgr0)\]"

Admin: export PS1="\[\033[38;5;9m\]\u\[$(tput sgr0)\]\[\033[38;5;15m\]@\[$(tput sgr0)\]\[\033[38;5;12m\]\h\[$(tput sgr0)\]\[\033[38;5;4m\]:\[$(tput sgr0)\]\[\033[38;5;9m\][\w]:\[$(tput sgr0)\]\[\033[38;5;15m\] \[$(tput sgr0)\]"

### Do it

For both user and root:

```
nano ~/.bashrc
```

''Replace PS1''

