# Shell Upgrade

- This gives us an interactive shell (assumes python is installed on the target)

` python -c 'import pty;pty.spawn("/bin/bash")'`

`SHELL=/bin/bash script -q /dev/null`

