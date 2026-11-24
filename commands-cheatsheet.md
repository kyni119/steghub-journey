# Commands cheat sheet

The commands I used in the projects so far (LAMP, LEMP, MERN), grouped by topic. For each command: what it does, and an example taken from my own projects. I add to this file as the course goes on. Values between `< >` are placeholders.

## Files and folders

| Command | What it does | Example |
|---|---|---|
| `pwd` | shows the current folder | `pwd` gives `/home/ubuntu` |
| `ls -l` | lists files with permissions, owner, group and size | `ls -l /var/www/config-lemp/` gives `-rw-r----- 1 root www-data 105 Nov 19 15:29 db.php` |
| `cd <folder>` | goes into a folder (`cd ..` goes up, `cd ~` goes home) | `cd ~/Todo/client` |
| `mkdir <folder>` | creates a folder | `mkdir models routes` |
| `cat <file>` | displays a file | `cat vite.config.js` |
| `nano <file>` | edits a file | `nano index.js`, then `Ctrl+O` Enter to save and `Ctrl+X` to quit |
| `cp <source> <dest>` | copies a file | `cp index.html index.html.bak` |
| `rm <file>` | deletes a file (no trash: it is final) | `sudo rm /var/www/projectLEMP/info.php` |
| `ln -s <target> <link>` | creates a symbolic link | `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/` |
| `unlink <link>` | removes a link, not the target | `sudo unlink /etc/nginx/sites-enabled/default` |

## Permissions and owners

| Command | What it does | Example |
|---|---|---|
| `chmod 640 <file>` | sets rights as numbers: owner 6 (read+write), group 4 (read), others 0 | `sudo chmod 640 /var/www/config/db.php` |
| `chmod 600 <file>` | only the owner can read and write | `chmod 600 .env` |
| `chown <owner>:<group> <file>` | changes the owner and the group | `sudo chown root:www-data /var/www/config/db.php` |
| `sudo <command>` | runs a command as administrator | `sudo apt update` |
| `sudo -u <user> <command>` | runs a command as another user | `sudo -u www-data php /var/www/projectlamp/notes.php` |

Number code: read = 4, write = 2, execute = 1. Rights are given in this order: owner, group, others.

## SSH and AWS connection

| Command | What it does | Example |
|---|---|---|
| `ssh -i <key>.pem ubuntu@<ip>` | connects to the instance with a key file | `ssh -i lamp-key.pem ubuntu@<public-ip-address>` |
| `icacls <key>.pem /inheritance:r` | Windows: removes the inherited rights on the key | `icacls lamp-key.pem /inheritance:r` |
| `icacls <key>.pem /grant:r "$($env:USERNAME):R"` | Windows: read-only for my user | `icacls lamp-key.pem /grant:r "$($env:USERNAME):R"` |
| `Test-NetConnection <ip> -Port 22` | Windows: is the port reachable? | `TcpTestSucceeded : True` means SSH can connect |
| `curl -s https://checkip.amazonaws.com` | the public IP seen from the Internet | on the server, it gives the IP that Atlas sees |

- `ssh -i` uses a key to log in. `ssh -L` creates a tunnel (local port forwarding): it is a different option.
- The public IP of an instance changes when I stop and start it.
- `Connection refused` or a timeout: my own IP probably changed, so I update the SSH rule of the security group (source: *My IP*).

## Packages and services

| Command | What it does | Example |
|---|---|---|
| `sudo apt update` | refreshes the list of available packages | `sudo apt update` |
| `sudo apt upgrade -y` | installs the updates (`-y` answers "yes") | `sudo apt update && sudo apt upgrade -y` |
| `sudo apt install <package>` | installs a package | `sudo apt install apache2` |
| `sudo systemctl status <service>` | shows the state of a service | `sudo systemctl status nginx` shows `active (running)` |
| `sudo systemctl start\|stop\|restart <service>` | starts, stops or restarts a service | `sudo systemctl restart php8.3-fpm` |
| `sudo systemctl reload <service>` | reloads the configuration without stopping | `sudo systemctl reload nginx` |
| `sudo systemctl enable <service>` | starts the service at boot | `sudo systemctl enable nginx` |
| `curl http://localhost` | tests a web server from the machine itself | `curl http://localhost` returns the HTML of the default page |

## Apache

| Command | What it does | Example |
|---|---|---|
| `sudo a2ensite <site>` | enables a virtual host | `sudo a2ensite projectlamp` |
| `sudo a2dissite <site>` | disables a virtual host | `sudo a2dissite 000-default` |
| `sudo apache2ctl configtest` | checks the configuration before reloading | answers `Syntax OK` |
| `sudo systemctl reload apache2` | applies the new configuration | after `a2ensite` |

## Nginx

| Command | What it does | Example |
|---|---|---|
| `sudo ln -s sites-available/<site> sites-enabled/` | enables a server block | `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/` |
| `sudo unlink /etc/nginx/sites-enabled/default` | disables the default server block | the default Nginx page is no longer served |
| `sudo nginx -t` | checks the configuration (needs `sudo`) | `syntax is ok` and `test is successful` |
| `sudo systemctl reload nginx` | applies the new configuration | after `nginx -t` succeeds |

Without `sudo`, `nginx -t` fails with `open() "/run/nginx.pid" failed (13: Permission denied)`.

## MySQL

Connect with `sudo mysql -p` (as root) or `mysql -u <user> -p` (as another user).

| Command (SQL) | What it does | Example |
|---|---|---|
| `CREATE DATABASE <db>;` | creates a database | `CREATE DATABASE lamp_demo;` |
| `CREATE USER '<user>'@'localhost' IDENTIFIED WITH mysql_native_password BY '<password>';` | creates a user | `CREATE USER 'lamp_app'@'localhost' IDENTIFIED WITH mysql_native_password BY '<my-password>';` |
| `GRANT SELECT ON <db>.* TO '<user>'@'localhost';` | gives read access only (least privilege) | `GRANT SELECT ON lamp_demo.* TO 'lamp_app'@'localhost';` |
| `REVOKE ALL PRIVILEGES ON <db>.* FROM '<user>'@'localhost';` | removes all the rights | `REVOKE ALL PRIVILEGES ON test_db.* FROM 'test_user'@'localhost';` |
| `SHOW GRANTS FOR '<user>'@'localhost';` | shows what a user can do | shows `GRANT SELECT ON lamp_demo.* ...` |
| `ALTER USER '<user>'@'localhost' IDENTIFIED WITH mysql_native_password BY '<password>';` | changes a password | used to reset `root` and `test_user` |

- A MySQL user is a name **plus** a host: `'root'@'localhost'` and `'root'@'%'` are two different accounts.
- Proof of least privilege: connected as `lamp_app`, `INSERT INTO lamp_demo.notes ...` fails with `ERROR 1142 ... INSERT command denied`.

## PHP

| Command | What it does | Example |
|---|---|---|
| `php -l <file>.php` | checks the syntax | `php -l /var/www/projectlamp/notes.php` gives `No syntax errors detected` |
| `sudo -u www-data php <file>.php` | runs the page as the web server user | prints the record: Apache's user can read the configuration and the database |

A blank PHP page usually hides an error: check the web server log, the file permissions and the syntax.

## Processes and ports

| Command | What it does | Example |
|---|---|---|
| `sudo ss -ltnp \| grep ':3000'` | who listens on port 3000? (`-l` listening, `-t` TCP, `-n` numbers, `-p` process) | `LISTEN 0 511 *:3000 ... users:(("node",pid=1423,fd=24))`: the PID is 1423 |
| `pgrep -a node` | running Node processes, with their full command line | `1632 node .../.bin/vite` and `1726 node index.js` |
| `kill <PID>` | stops one process by its number | `kill 1423` |
| `pkill <name>` | stops every process with this name | `pkill vite` |
| `pkill -f "<text>"` | stops every process whose command line contains the text | `pkill -f "node index.js"` |
| `(<command> > <file> 2>&1 &)` | runs a command in the background and saves its messages | `(node index.js > /tmp/server.log 2>&1 &)`, then `cat /tmp/server.log` |

- Before starting a server, I check that no old copy is still running: an old process can keep answering with an outdated configuration.
- An empty answer for `ss ... ':5000'` means that nothing listens on port 5000: the backend is not running.

## HTTP requests with curl

| Command | What it does | Example |
|---|---|---|
| `curl -s <url>` | GET request (`-s` hides the progress bar) | `curl -s http://localhost:5000/api/todos` gives `[]` |
| `curl -s -X POST -H "Content-Type: application/json" -d '<json>' <url>` | POST with a JSON body | `curl -s -X POST -H "Content-Type: application/json" -d '{"action":"Learn MERN"}' http://localhost:5000/api/todos` gives `{"action":"Learn MERN","_id":"65f1...","__v":0}` |
| `curl -s -X DELETE <url>/<id>` | DELETE request | `curl -s -X DELETE http://localhost:5000/api/todos/<id>` |
| `curl -s -i <url>` | also shows the status line and the headers | an empty task gives `HTTP/1.1 400 Bad Request` |

Options: `-s` silent, `-X` HTTP method, `-H` header, `-d` body, `-i` show the response headers.

## Node.js and npm

| Command | What it does | Example |
|---|---|---|
| `node -v` and `npm -v` | show the versions | `v22.23.5` and `10.9.9` |
| `npm init -y` | creates `package.json` with the default answers | in the `Todo` folder |
| `npm install <package>` | installs a package and adds it to `package.json` | `npm install express dotenv` |
| `npm run dev` | runs the `dev` script of `package.json` | starts Vite on port 3000 |
| `node <file>.js` | runs a script | `node index.js` gives `Server running on port 5000` |

- `node_modules/` is never committed: `npm install` recreates it from `package.json`.
- Node reads the `.env` file **only at startup**: restart after changing it.

## Secrets: never in the repository

| Command | What it does | Example |
|---|---|---|
| `sed 's/\(\/\/[^:]*:\)[^@]*@/\1xxx@/' .env` | displays a connection string with the password hidden | shows `DB=mongodb+srv://todo_app:xxx@<cluster>/todo?retryWrites=true&w=majority` |
| `read -s PW; node -e 'console.log(encodeURIComponent(process.argv[1]))' "$PW"` | URL-encodes a password without showing it while I type it | `ab#12` becomes `ab%2312` |
| `chmod 600 .env` | only I can read the file | right after creating `.env` |

- The password goes in a file outside the web root (`/var/www/config`, rights `640`, group `www-data`) or in a `.env` file.
- Special characters in a connection string are encoded: `#` becomes `%23`, `@` becomes `%40`.
- The `.gitignore` blocks `*.pem`, `.env`, `*.tfstate` and `node_modules/`.
