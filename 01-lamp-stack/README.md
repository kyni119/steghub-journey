# 01. Web Stack Implementation (LAMP) in AWS

## Summary

I built a LAMP stack on an Ubuntu 24.04 EC2 instance (Stockholm region): Apache, MySQL, PHP, a `projectlamp` virtual host and a PHP test page.

## Notes

- Self study (SDLC, LAMP, TCP/UDP, ports): [101-self-study.md](101-self-study.md)
- Linux permissions (`chmod`, `chown`): [101-permissions.md](101-permissions.md)
- AWS account, budget alert, EC2 instance and SSH connection: [102-aws-prerequisites.md](102-aws-prerequisites.md)
- Installing Apache (lesson 103): [103-apache.md](103-apache.md)
- Installing MySQL (lesson 104): [104-mysql.md](104-mysql.md)
- Installing PHP (lessons 105 and 107): [105-php.md](105-php.md)
- Apache virtual host (lesson 106): [106-virtualhost.md](106-virtualhost.md)

## Errors and fixes

| Error | Fix |
|---|---|
| `%username%`: no mapping between account names and security IDs (icacls) | In PowerShell the variable is written `$env:USERNAME` (`%username%` is cmd syntax) |
| `ssh: connect to host ... port 22: Connection refused` (and `Test-NetConnection` timing out) | My IP had changed: the SSH rule of the security group only allowed the old one. I check my IP on checkip.amazonaws.com and set the SSH source to *My IP* |
| MFA and console access not available for the IAM user | Limit of the new AWS experience. I don't enable the advanced features (they remove the spending limit). I enabled MFA on my AWS Builder ID instead (profile > Security > Register a device) |

