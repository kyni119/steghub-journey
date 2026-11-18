# Step 5: read MySQL data with PHP (lesson 107)

Last test of the LEMP stack: a PHP page connects to MySQL and displays data. I start by preparing the database, a dedicated user and a table.

## Database and dedicated user

```sql
CREATE DATABASE test_db;
CREATE USER 'test_user'@'localhost' IDENTIFIED WITH mysql_native_password BY '<my-password>';
GRANT ALL ON test_db.* TO 'test_user'@'localhost';
```

- I create a dedicated user instead of using `root`, so the application only has access to its own database (least privilege).
- `GRANT ALL ON test_db.*` gives all rights on the `test_db` database only. `ALL` and `ALL PRIVILEGES` are the same command.
- `'localhost'`: this user can only connect from the server itself.
- The password must follow the MEDIUM rule of the validation plugin (8 characters or more, uppercase, lowercase, digit, symbol), otherwise MySQL answers `ERROR 1819 ... does not satisfy the current policy requirements`.
- I never write the real password in this file.

## The todo_list table

I connect with the dedicated user:

```bash
mysql -u test_user -p
```

```sql
CREATE TABLE test_db.todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY (item_id)
);
INSERT INTO test_db.todo_list (content) VALUES ('My first task');
SELECT * FROM test_db.todo_list;
```

- `item_id INT AUTO_INCREMENT`: a number that increases by itself.
- `content VARCHAR(255)`: the text of the task (255 characters at most).
- `PRIMARY KEY (item_id)`: identifies each row in a unique way.
- `INSERT INTO ... VALUES` adds a row, `SELECT *` displays all the rows.

![Creating the table and adding a row](images/todo-1-table.png)

## The PHP script

```bash
nano /var/www/projectLEMP/todo_list.php
```

```php
<?php
$user = "test_user";
$password = "<my-password>";
$database = "test_db";
$table = "todo_list";

try {
    $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
    echo "<h2>TODO</h2><ol>";
    foreach ($db->query("SELECT content FROM $table") as $row) {
        echo "<li>" . htmlspecialchars($row['content']) . "</li>";
    }
    echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```

- The four variables group the user, the password, the database and the table.
- `new PDO(...)` opens the connection to MySQL, with the dedicated user.
- `$db->query("SELECT content ...")` reads the tasks, and the `foreach` displays them in a numbered list.
- `htmlspecialchars` neutralizes any HTML characters in the text of the tasks.
- The `try/catch` displays an error if the connection fails.

In the screenshot, the password is hidden.

![The todo_list.php script in Nano](images/todo-2-script.png)

## Result in the browser

With the public IP of the instance and `/todo_list.php`, the page displays the task read from MySQL:

![todo_list.php page](images/todo-3-navigateur.png)

The whole LEMP stack works: Nginx receives the request, PHP-FPM runs the script, PHP reads the table in MySQL, and the result goes back to the browser.

## Why the browser does not ask for a password

The password is in the PHP script, which runs **on the server**: it is PHP that connects to MySQL, not the visitor. The browser only receives the HTML that is produced (the list), never the code or the password. This also means the page is public: anyone with the address can see the list, without any authentication.

Good practice for a real project: don't leave the password in a file of the web folder. It goes in a configuration file outside the web root (or in an environment variable), so it is not exposed if Nginx one day stops running PHP files.
