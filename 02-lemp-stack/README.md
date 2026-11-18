# 02. Web Stack Implementation (LEMP)

- [ ] 101
- [ ] 102
- [ ] 103
- [ ] 104
- [ ] 105
- [ ] 106
- [ ] 107  (assignment)

## Summary

I built a LEMP stack on a second Ubuntu 24.04 EC2 instance: Nginx, MySQL, PHP-FPM, a `projectLEMP` server block, and a PHP page that reads a MySQL table.

## Notes

- Self study (SQL basics and the Nano editor): [101-self-study.md](101-self-study.md)
- EC2 instance `lemp-server` and SSH connection: [102-ec2-lemp.md](102-ec2-lemp.md)
- Installing Nginx (lesson 103): [103-nginx.md](103-nginx.md)
- Installing MySQL (lesson 104): [104-mysql.md](104-mysql.md)
- Installing PHP-FPM (lesson 105): [105-php-fpm.md](105-php-fpm.md)
- Nginx server block (lesson 106): [106-nginx-server-block.md](106-nginx-server-block.md)
- Reading MySQL data with PHP (lesson 107): [107-php-mysql.md](107-php-mysql.md)

## Errors and fixes

| Error | Fix |
|---|---|
| Ubuntu 26.04 image proposed by default | I choose Ubuntu 24.04 LTS, the version used by the course (PHP 8.3, MySQL 8.0) |
| `nginx -t` without `sudo`: `open() "/run/nginx.pid" failed (13: Permission denied)` | The test must write to `/run/nginx.pid`, which only `root` can do: I run `sudo nginx -t` |
| `ERROR 1819 ... Your password does not satisfy the current policy requirements` | The validation plugin (MEDIUM level) requires 8 characters or more, an uppercase letter, a lowercase letter, a digit and a symbol: my attempts had no uppercase letter |
| `ERROR 1045 ... Access denied for user 'test_user'@'localhost'` | The password was typed wrongly (nothing is displayed while typing): I reset it with `ALTER USER` and typed it again |
| `cat: nano: No such file or directory` | I had typed `cat nano /etc/...`: `cat` displays a file, `nano` edits it. The right command is `cat <path>` |

## AWS cleanup

- [ ] Resources deleted
