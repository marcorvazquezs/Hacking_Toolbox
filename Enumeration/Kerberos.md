# Enumerating Kerberos

## Kerbrute

- Download [Kerbrute](https://github.com/ropnop/kerbrute/releases)

### Installation
- Rename `kerbrute_linux_amd64` to `kerbrute`
- `chmod +x kerbrute`

### Enumerating Users
- Download the wordlist to enumerate [here](https://github.com/Cryilllic/Active-Directory-Wordlists/blob/master/User.txt)
- `./kerbrute userenum -dc CONTROLLER.local -d CONTROLLER.local User.txt`
  - This brute forces user accounts from a domain controller using the supplied wordlist

