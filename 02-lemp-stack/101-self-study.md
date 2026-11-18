# Self study (module 02: LEMP)

## 1. SQL basics

SQL (*Structured Query Language*) is the language used to talk to a relational database such as MySQL. Data is stored in tables (rows and columns), which are themselves stored in databases.

### The 4 families of commands

| Family | Role | Examples |
|---|---|---|
| DQL (queries) | Read data | `SELECT` |
| DML (manipulation) | Change data | `INSERT`, `UPDATE`, `DELETE` |
| DDL (definition) | Create or change the structure | `CREATE`, `ALTER`, `DROP` |
| DCL (control) | Manage rights | `GRANT`, `REVOKE` |

### Commands to remember

```sql
CREATE DATABASE my_db;                          -- create a database
SHOW DATABASES;                                 -- list the databases (MySQL command)
USE my_db;                                      -- choose the working database
CREATE TABLE tasks (id INT AUTO_INCREMENT PRIMARY KEY, title VARCHAR(100));
INSERT INTO tasks (title) VALUES ('Learn SQL');
SELECT id, title FROM tasks WHERE id = 1 ORDER BY title LIMIT 10;
UPDATE tasks SET title = 'Master SQL' WHERE id = 1;
DELETE FROM tasks WHERE id = 1;
DROP TABLE tasks;                               -- delete the table and its data
```

### Syntax rules

- Each statement ends with a `;`.
- Keywords are not case-sensitive (`SELECT` = `select`), but data can be.
- Comments: `-- one line` or `/* several lines */`.
- An `UPDATE` or a `DELETE` without `WHERE` changes or deletes all the rows of the table.

### For the next steps

In the LEMP project, I create a database and a task table (`todo_list`), insert rows with `INSERT`, then display them with `SELECT` from a PHP page.

## 2. The Nano editor

Nano is a text editor for the terminal, simpler than Vim: it has no modes, you just type, and the shortcuts are shown at the bottom of the screen (`^` means `Ctrl`, `M-` means `Alt`).

| Action | Shortcut |
|---|---|
| Open or create a file | `nano file-name` |
| Save | `Ctrl + O`, then Enter to confirm the name |
| Quit | `Ctrl + X` (it offers to save if there are changes) |
| Cut a line | `Ctrl + K` |
| Paste | `Ctrl + U` |
| Copy a line | `Alt + 6` |
| Search | `Ctrl + W` |
| Search and replace | `Ctrl + \` |
| Undo / Redo | `Alt + U` / `Alt + E` |
| Help | `Ctrl + G` |

### Nano or Vim?

- Nano: no modes, quicker to learn, handy to edit a configuration file.
- Vim: more powerful, but you need to know its modes (`i`, `Esc`, `:wq`).

In this project, the Nginx configuration files are edited with Nano.

## Exercise

On the server, I open a file with Nano, write a few lines, cut and paste a line, search a word, then save and check with `cat`.

```bash
nano test-nano.txt
cat test-nano.txt
```
