# SUID Search

- Search for binaries with SUID bit set

`find / -perm +6000 2>/dev/null | grep '/bin/'`