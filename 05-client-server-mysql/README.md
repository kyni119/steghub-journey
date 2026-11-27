# 05. Client-Server Architecture with MySQL

- [ ] 101
- [ ] 102  (assignment)

## Notes

- Self study (ping, traceroute, MTR, basic SQL, plan of the module): [101-self-study.md](101-self-study.md)
- Two EC2 instances (`mysql-server`, `mysql-client`) and their security groups: [102-ec2-instances.md](102-ec2-instances.md)
- MySQL server: `bind-address`, database and user with least privilege: [103-mysql-server.md](103-mysql-server.md)
- MySQL client: network test with `nc`, remote connection: [104-mysql-client.md](104-mysql-client.md)

## What is different from the course guide

| Course guide | What I did | Why |
|---|---|---|
| Region `us-east-1`, `t2.micro` | Stockholm (`eu-north-1`), `t3.micro` | same as my other projects, free tier |
| SSH (22) open to any IP | SSH from my IP only | smaller attack surface |
| Port 3306 allowed from the IP of the client | port 3306 allowed from the **security group** of the client | the rule does not depend on a private IP |
| `bind-address = 0.0.0.0` | `bind-address` = private IP of the server | MySQL is never reachable from the Internet |
| `GRANT ALL PRIVILEGES ON *.*` | `ALL PRIVILEGES` on the database `company` only | least privilege |
| Password written in the guide | password asked at the prompt (`-p`), not written in any file | no secret in the repository |

## Errors and fixes

| Error | Fix |
|---|---|
| `mysql-client: command not found` | `mysql-client` is the name of the package. The command is `mysql` |
| `mysql -v`: `Can't connect to local MySQL server through socket` | Without `-h`, `mysql` looks for a server on the same machine. I add `-h <server-private-ip>` |
| `ERROR 1045 (28000): Access denied for user ... (using password: YES)` | the password typed at the prompt was not the right one. The second try worked |
| `ERROR 1146 (42S02): Table 'company.company' doesn't exist` | I wrote the name of the database after `FROM`. `FROM` expects a table: `employees`, or `company.employees` |
| `sudo mysql -e ...` on the client: `ERROR 2002 ... mysqld.sock` | wrong machine: MySQL runs on the **server**. I ran the command on `mysql-server` |
| `Test-NetConnection` on the private IP from my PC: `TcpTestSucceeded : False` | a private IP cannot be reached from the Internet, so the test proves nothing. I test the **public** IP instead |
| `ping` to the private IP of the server: 100% packet loss, but MySQL works | the security group does not allow ICMP. `ping` tests the machine, `nc -zv` tests the port |
| The editor of the outbound rules showed `TCP custom`, port `0` | I cancelled: the default rule (all traffic) is the one to keep |

## AWS cleanup

- [ ] Resources deleted
