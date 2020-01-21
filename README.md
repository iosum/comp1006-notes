## week 2


### week 1 review

- Use the . operator to concatenate strings (NOT the + symbol)
- PHP code must be enclosed inside opening & closing PHP tags
- opening tag is ```" <?php"```
- closing tag is ```"?>"  ```
- PHP cannot run without a web server
- Open-source (free)
- Includes built-in functions we can use (e.g. phpinfo();)
- PHP is **loosely-typed**: variables do not have a specific data type and their values can change 
- from 1 type to another
- Very possibly to be replaced by JavaScript in the future
- Large database support for all types of databases
- Widely-used for content management systems including WordPress

### week 2 

single quote : php treats as a literal string

double quote : php treats php variables as php variables

double quote + html attributes (single quote)

- common errors

  - undefined index : the name attributes inside html must be the same inside php file ```['name attribute']```

  - always disconnect the db

    **it will let the user wait to connect to the website if there is no connection available.**

---


#### html form

- a set of `<form></form>` tags

- within the opening `<form>` tag, a method attribute with a value set to post:  

```php
method="POST"
```




- also within the opening `<form>` tag, a target attribute that tells the server which page to load next.  This page will process the form inputs: 

```php
<form action="save-person.php" method="POST">
```

- This 2nd page must have a `.php` extension in order to process the form inputs

```php
action="save-person.php"
```

- User input controls for each field we want from the user

```php
 <input name="name" id="name">
```

- A submit button to send the form values to the server for processing

```php
<button type="submit">Submit</button>
```

---

```html
    <form action="save-person.php" method="POST">
        <fieldset>
            <label for="name">Name :</label>
            <input name="name" id="name">
        </fieldset>
        <fieldset>
            <label for="email">Email :</label>
            <input name="email" id="email">
        </fieldset>
        <button type="submit">Submit</button>
    </form>
```

---


#### inside php
- Capture the form inputs

```php
$variableName = $_POST[‘html_input_name’];

$name = $_POST['name'];


```

> All of the form input values are automatically added by the PHP processor to a collection or array called `$_POST`

- Connect to the database

```php

$db = new PDO('mysql:host=localhost;dbname=your_db_name', 'username', 'password');)

```

- Set up and prepare the SQL INSERT command

```php

$sql = "WHAT_SQL_COMMAND_DO_YOU_NEED"

$sql = "INSERT INTO persons (name, email) VALUES(:name, :email)";

$cmd = $db->prepare($sql);


```

- Add each input value as a parameter to the INSERT command

```php

$cmd->bindParam(":name", $name, PDO::PARAM_STR, 50);
$cmd->bindParam(":email", $email, PDO::PARAM_STR, 50);

```


- Execute the save operation

```php
$cmd->execute();
```

- Disconnect from the database

```php
$db = null;
```

- Show a confirmation to the user

---

![](saving_data_schema.jpg)

---

#### connection exercise

```php
<!DOCTYPE html>
<html>
<head>
<title>Database Connection</title>
</head>
<body>
<?php
$db = new PDO('mysql:host=172.31.22.43;dbname=username-here', 'username-here', 'password-here');
if (!$db)  {
               echo 'could not connect';
}
else {
    echo 'connected to the database';
}
$db = null;
?> 

</body>
</html>
```
