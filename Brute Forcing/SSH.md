# Brute Force SSH 

- Assuming you got the users `id_rsa`
- Convert this to a format that john can understand
  `/usr/share/john/ssh2john.py id_rsa > key_for_john`

- Crack the key with john to get the passphrase
  `john key_for_john --wordlist=PATH TO LIST`