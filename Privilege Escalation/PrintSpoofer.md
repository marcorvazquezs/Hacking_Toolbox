# Print Spoofer Exploit

**Requirements**
- Attack requires the user account to have `SeImpersonatePrivilege`

**Works on**
- Windows 8.1
- Windows Server 2012 R2
- Windows 10
- Windows Server 2019

**Attack Process**
- Download [PrintSpoofer.exe](https://github.com/dievus/printspoofer)
- Upload the `PrintSpoofer.exe` using a compromised `smb` share
- Use a `reverse shell` session to navigate to the `smb` share and run the `PrintSpoofer.exe -i -c cmd.exe` command
- You should now have a shell as `nt authority\system`


