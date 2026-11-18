# File and directory permissions

- `chown`: changes the owner and the group of a file or directory.
- `chmod`: changes the permissions of a file or directory.

```bash
chown [OPTIONS] [OWNER]:[GROUP] FILE
chmod [OPTIONS] MODE FILE
```

`-R`: recursive (the directory and everything inside it).

---

## Reading permissions with `ls -l`

```
-rwxr-xr-- 1 kadio kadio 120 Oct 14 18:00 run_all.sh
│└┬┘└┬┘└┬┘
│ │  │  └─ others
│ │  └──── group
│ └─────── owner (user)
└───────── type (- file, d directory)
```

| Letter | Permission | Value |
|---|---|---|
| `r` | Read | 4 |
| `w` | Write | 2 |
| `x` | Execute | 1 |

Values add up: `7 = rwx`, `6 = rw-`, `5 = r-x`, `4 = r--`, `0 = ---`.
A 3-digit mode is read in this order: owner, group, others.

## Changing permissions with `chmod`

Two ways to write it:

- **Symbolic**: `u` (user), `g` (group), `o` (others), `a` (all), then `+` (add), `-` (remove), then `r`, `w`, `x`.
  Examples: `u+x` (the owner can execute), `g-w` (the group can no longer write), `o+r` (others can read).
- **Numeric**: `chmod 755 file`.

| Command | Effect |
|---|---|
| `chmod u+x script.sh` | Adds execute for the owner |
| `chmod +x script.sh` | Adds execute for everyone |
| `chmod 755 script.sh` | `rwxr-xr-x`: the owner can do everything, others read and execute |
| `chmod 644 file.txt` | `rw-r--r--`: a normal file |
| `chmod 600 ~/.ssh/id_rsa` | Read/write for the owner only (private key) |
| `chmod 400 my-key.pem` | Read-only for the owner (AWS key: SSH refuses it if it is too open) |

Without the `x` permission, `./script.sh` returns `Permission denied`.

---

## Owner and group

```bash
ls -l file                         # owner and group
sudo chown kadio file              # change the owner
sudo chown kadio:kadio file        # owner and group
sudo chown :devs file              # group only
sudo chown -R kadio:kadio folder/  # recursive
sudo chgrp developers file         # change the group
```

### Users and groups

| Command | Purpose |
|---|---|
| `whoami` | Current user |
| `id` | UID, GID and groups |
| `groups` | Groups of the user |
| `sudo adduser name` | Create a user |
| `sudo groupadd name` | Create a group |
| `sudo usermod -aG group name` | Add a user to a group (`-a` is essential, otherwise the user leaves the other groups) |
| `su - name` | Switch to another user |
| `sudo deluser name` | Delete a user (their files stay) |
| `sudo delgroup name` | Delete a group |

Useful files: `/etc/passwd` (users), `/etc/group` (groups), `/etc/shadow` (hashed passwords, readable by root only).

### `umask`

Sets the default permissions of new files. With `umask 022`, a file is created as `644` and a directory as `755`.

---

## Special permissions (overview)

| Bit | Effect | Risk |
|---|---|---|
| SUID (`s` instead of the owner's `x`, `4xxx`) | The program runs with the owner's rights | A vulnerability can lead to privilege escalation |
| SGID (`2xxx`) | Same with the group | Same |
| Sticky bit (`t`, `1xxx`, e.g. `/tmp`) | In a **directory**, only a file's owner can delete it | None |

```bash
find / -perm -4000 -type f 2>/dev/null   # find SUID files (security audit)
```

---

## Exercise

Create a file, change its owner and its permissions, then check with `ls -l`.

### Step 1: a user, a file, permissions

```bash
sudo adduser intern                              # create the user "intern"
touch my_file.txt                                # create a file
sudo chown intern:intern my_file.txt             # change the owner and the group
sudo chmod 640 my_file.txt                       # rw-r-----: owner reads/writes, group reads, others nothing
ls -l my_file.txt                                # check
```

### Step 2: a `devs` group and a directory with the sticky bit

```bash
sudo groupadd devs                               # create the group devs
sudo usermod -aG devs intern                     # add intern to the group
sudo chown :devs my_file.txt                     # the file's group becomes devs
```

Goal: only the owner can delete their file. The sticky bit goes on the **directory** that contains the file, not on the file:

```bash
mkdir shared
sudo chown :devs shared
sudo chmod 1770 shared                           # rwxrwx--T: the devs group works, everyone deletes only their own files
sudo mv my_file.txt shared/
ls -ld shared                                    # the T (or t) at the end confirms the sticky bit
```

Why not `chmod 1777` or `4777` on the file?
- `1777` on a file does nothing (the sticky bit only works on directories) and gives all rights to everyone.
- `4777` sets SUID: the file would run with the owner's rights. It is a security hole, and it does not answer the question.

### Step 3: cleanup

```bash
sudo deluser intern                              # delete the user (their home directory stays)
sudo delgroup devs                               # delete the group
id intern                                        # must answer "no such user"
ls -l shared/my_file.txt                         # the file is still there, but the owner is shown as a number (UID)
```

`ps -u intern` only lists a user's processes: it is not the right command to check that the user is gone. `id` is more direct.

To delete everything: `sudo rm -r shared my_file.txt`.

---
