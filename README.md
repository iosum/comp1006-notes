# COMP1006 - Intro to Web Programming
> Here's the notes from the class

> Professor : Rich Freeman

## Table of Contents

- [Week 2](#week-2)
- [Week 3](#week-3)
- [Week 4](#week-4)
- [Week 5](#week-5)


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

---

## week 3

### week2 quiz


Questions | Answers
-|-
Why is `POST` used more than `GET`? | POST is more secure as `GET` displays the form input values as parameters in the url address bar (while `POST` does not).
What is the default method of an HTML form in the code below? (`<form action="save.php"> </form>`) | Although `POST` is much more commonly used, `GET` is the default method and will execute when no specific method attribute is given within an HTML form tag.
What is the purpose of the PDO library? | PDO stands for PHP Data Objects and it is a library used to connect from PHP to a database. It includes functions such as connecting to a database, executing SQL commands against a database, and reading query results. It replaces the older my_sqli library that could only connect to MySQL databases
How do we refer to the collection of inputs submitted from an HTML form post? | `$_POST` is one of several built-in collections / arrays within the PHP library. Others include `$_SESSION`, `$_SERVER` and `$_COOKIE`.
What attribute is included in an HTML form tag that indicates which page should load after the form is submitted? | The `action` attribute value determines which page loads after a form submission. The code to process the form inputs goes on this page.

### Query-exercise

`bindParam()`?

- in order to save the person, we need to pass in the name and the email, so we use the `bindParam()` to send the name and email
- see the `$sql` statement, if there is no parameter in the `$sql` statement, we don't use the `bindParam()`
- `select` query doesn't need any extra value to run, so there is no `bindParam()` in this exercise

```php
<?php

// 1. Connect to the AWS db
$db = new PDO("mysql:host=$servername;dbname=w20", $username, $password);

// 2. Write the SQL Query to read all the records from the persons table
$sql = "SELECT * FROM persons";

// 3. Create a Command variable $cmd then use it to run the SQL Query
$cmd = $db->prepare($sql);
$cmd->execute();

// 4. Use the fetch() method of the PDO Command variable to store the data into a variable called $persons
$persons = $cmd->fetchAll();

// 4a. Before we start the loop to show the data, create an HTML table
echo '<table border="1"><thead><th>ID</th><th>Name</th><th>Email</th></thead>';

// 5. Use a foreach loop to iterate (cycle) through all the values in the $persons variable.  
// Inside this loop, use an echo command to display the name of each person.
foreach ($persons as $value) {
    // for each record, create a new row & print each value from the db in a separate table cell
    echo '<tr><td>' . $value['personId'] . '</td><td>' . $value['name'] . "</td><td>" . $value['email'] . "</td></tr>";
}

// 5a. now close the HTML table
echo '</table>';

// 6. Disconnect from the database
$db = null;

?>
```

### dropdown-list-exercise

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<?php
// connect
$db = new PDO("mysql:host=dbhostname;dbname=your_db_name", username, password);


// set up the sql query to fetch the list of names from the db
$sql = "SELECT name FROM persons";

// execute the query
$cmd = $db->prepare($sql);
$cmd->execute();
$persons = $cmd->fetchAll();

// create an HTML dropdown list
echo '<select name="name">';

// inside the dropdown, add each name to the list persons[0]
foreach($persons as $value) {
    echo '<option>' . $value['name'] . '</option>';
}

// close the dropdown tag
echo '</select>';

// disconnect
$db = null;
?>

</body>
</html>
```

### Lab1

```php
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Lab1 - 200428479</title>
</head>

<body>
    <?php
    // connect to the db
    $db = new PDO("mysql:host=server_name;dbname=your_db_name", "username", "password");

    // set up and execute the query
    $sql = "SELECT * FROM teams";
    $cmd = $db->prepare($sql);
    $cmd->execute();
    $teams = $cmd->fetchAll();

    // loop through each rows and display it on the table
    echo "<table border=\"1\"><thead><th>City</th><th>Nickname</th><th>Division</th></thead>";

    foreach ($teams as $team) {
        echo "<tr><td> {$team['city']} </td><td> {$team['nickname']} </td><td> {$team['division']} </td></tr>";
    }

    echo "</table>";

    // disconnect
    $db = null;
    ?>
</body>

</html>
```



## week 4

### week3 quiz


Questions | Answers
-|-
What method of the PDO command object is typically used to retrieve a list of results from a database query? | `fetchAll()` is used when we expect a query to potentially return multiple records, which we then loop through to process or display.
In the code below, what does `$b` represent?  `foreach ($beers as $b) {}` | the current record in the loop as the code cycles through all of the beers. In a PHP foreach loop, the first variable represents the entire list and the second variable represents the current item as the loop executes.|
Which is a benefit of using Bound Parameters to pass values into an SQL command? | Special characters are automatically handled, SQL Injection attacks may be prevented, Parameter values have their data types validated 
What tag is used for each item in a dropdown list?| `option`. The `select` tag **creates** the list and the `option` tag **wraps** each individual item inside the list. |
On a page where we are displaying content from the database, when should we disconnect? | As soon as we no longer need the database connection we should disconnect to free up the connection for another http request.

### week 4 lesson

decide our project through the semester, which is a music website.
 
- `musician-details.php` – form to input a new musician

- `save-musician.php` – page to save a new record from the form inputs to the musicians table in the db

- `musicians.php` – page that lists all the musicians in the database in an HTML table

---

script for the html form

```sql 
CREATE TABLE musicians

(musicianId INT NOT NULL AUTO_INCREMENT PRIMARY KEY,

name VARCHAR(100) NOT NULL,

recordLabel VARCHAR(50),

ranking INT,

solo BIT,

photo VARCHAR(100),

city VARCHAR(50));
```

---

`musicians-details.php`

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Musician Details</title>
</head>
<body>
<form action="save-musician.php" method="post">
    <div>
        <label for="name">Name :</label>
        <input type="text" id="name" name="name" required>
    </div>
    <div>
        <label for="recordLabel">Record Label :</label>
        <input type="text" id="recordLabel" name="recordLabel" required>
    </div>
    <div>
        <label for="ranking">Ranking :</label>
        <input type="text" id="ranking" name="ranking" required>
    </div>
    <div>
        <label for="solo">Solo :</label>
        <input type="checkbox" id="solo" name="solo">
    </div>
    <div>
        <label for="photo">Photo :</label>
        <input type="file" id="photo" name="photo">
    </div>
    <div>
        <label for="city">City :</label>
        <input type="city" id="city" name="city" required>
    </div>
    <button type="submit">Save</button>
</form>
</body>
</html>
```

---

`save-musician.php`

```php
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Save musician</title>
</head>
<body>
<a href="musicians.php">View musicians</a>
<?php
// get the data from the form
$name = $_POST['name'];
$recordLabel = $_POST['recordLabel'];
$ranking = $_POST['ranking'];
$solo = $_POST['solo'];
$photo = $_POST['photo'];
$city = $_POST['city'];

// data validation
$valid = true;

if (empty($name)) {
    echo "Please fill out the name";
    $valid = false;
}
if (empty($recordLabel)) {
    echo "Please fill out the record label";
    $valid = false;
}
if (empty($ranking)) {
    echo "Please fill out the ranking";
    $valid = false;
}
if (isset($solo)) {
    echo "Please check the checkbox";
    $valid = false;
}

if (empty($city)) {
    echo "Please fill out the city";
    $valid = false;
}

if ($valid) {
    // connect
    $db = new PDO('mysql:host=your_host;dbname=your_db_name', "username", "password");

    // set up
    $solo = (bool)$solo;
    $sql = "INSERT INTO musicians (name, recordLabel, ranking, solo, photo, city) VALUES (:name, :recordLabel, :ranking, :solo, :photo, :city)";

    // prepare the data
    $cmd = $db->prepare($sql);

    // bindparam
    // no need to set the size of the integer and bool
    $cmd->bindParam(":name", $name, PDO::PARAM_STR, 100);
    $cmd->bindParam(":recordLabel", $recordLabel, PDO::PARAM_STR, 50);
    $cmd->bindParam(":ranking", $ranking, PDO::PARAM_INT);
    $cmd->bindParam(":solo", $solo, PDO::PARAM_BOOL);
    $cmd->bindParam(":photo", $photo, PDO::PARAM_STR, 100)
    $cmd->bindParam(":city", $city, PDO::PARAM_STR, 50);

    // execute
    $cmd->execute();

    // echo successfully
    echo "<br> save successfully";

    // disconnect
    $db = null;
}


?>
</body>
</html>
```

---

`musicians.php`

```php
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Musicians</title>
</head>
<body>
<a href="musician-details.php">Add musician</a>
<?php
// connect
$db = new PDO('mysql:host=your_host;dbname=your_db_name', "username", "password");

// set up & execute
$sql = "SELECT * FROM musicians";
$cmd = $db->prepare($sql);
$cmd->execute();
$musicians = $cmd->fetchAll();

// print the table

echo "<table border=\"1\"><thead><th>Name</th><th>Record Label</th><th>Ranking</th><th>Solo</th><th>Photo</th><th>City</th></thead>";



foreach ($musicians as $musician) {
    echo "<tr><td>{$musician['name']}</td>
    <td>{$musician['recordLabel']}</td>
    <td>{$musician['ranking']}</td>";

    if($musician['solo'] == 1) {
        echo "<td>Solo</td>";
    }
    else {
        echo "<td>Band</td>";
    }


    //<td>{$musician['solo']}</td>
    echo "<td>{$musician['photo']}</td>
    <td>{$musician['city']}</td></tr>";

}

echo "</table>";

// disconnect
$db = null;

?>


</body>
</html>
```


## week 5


### week 3 quiz


Questions | Answers
-|-
What method of the PDO command object is typically used to retrieve a list of results from a database query? | `fetchAll()` is used when we expect a query to potentially return multiple records, which we then loop through to process or display.
In the code below,doeb` represent?  `foreach ($beers as $b) {}` | the current record in the loop as the code cycles through all of the beers. In a PHP foreach loop, the first variable represents the entire list and the second variable represents the current item as the loop executes.|
Which is a benefit of using Bound Parameters to pass values into an SQL command? | Special characters are automatically handled, SQL Injection attacks may be prevented, Parameter values have their data types validated 
What tag is used for each item in a dropdown list?| `option`. The `select` tag **creates** the list and the `option` tag **wraps** each individual item inside the list. |
On a page where we are displaying content from the database, when should we disconnect? | after we have displayed all the data. As soon as we no longer need the database connection we should disconnect to free up the connection for another http request.

### week 5 lesson

#### Input Validation 

- what is it and why is it important?
- List 3 types of input validation you might see in a web application

[From Validation from MDN](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation)

- We want to get the right data, in the right format. Our applications won't work properly if our users' data is stored in the wrong format, is incorrect, or is omitted altogether.

- We want to protect our users' data. Forcing our users to enter secure passwords makes it easier to protect their account information.

- We want to protect ourselves. There are many ways that malicious users can misuse unprotected forms to damage the application


`required`: Specifies whether a form field needs to be filled in before the form can be submitted.

`minlength` and `maxlength`: Specifies the minimum and maximum length of textual data (strings)

`min` and `max`: Specifies the minimum and maximum values of numerical input types
type: Specifies whether the data needs to be a number, an email address, or some other specific preset type. 

`pattern`: Specifies a regular expression that defines a pattern the entered data needs to follow.


---

- Input Validation - what is it and why is it important?

  - Ensuring user inputs are valid
  - Preventing multiple spellings of the same value (e.g. province: ON / ONT / Ontario) -> Standardization
  - Simplifies data collection
  - Security - preventing code inputs for hacking attempts
  - Checking data against the database (e.g. no duplicate usernames)


- List 3 types of input validation you might see in a web application

  - Required fields

  - Format - email / password / credit card #'s / dates / postal-zip codes / phone #'s 

  - Length
   
  - Validate against database

  - Comparison (captcha / password  / dates / numeric ranges)



---


**Warning: Never trust data passed to your server from the client. Even if your form is validating correctly and preventing malformed input on the client-side, a malicious user can still alter the network request.**

---

validation in musicians form : 

- all of the inputs are required.
- name cannot be more than 100 characters.
- the data type of ranking has to be integer and is a positive number

> To test if a variable is a number or a numeric string (such as form input, which is always a string), you must use `is_numeric()`.

---

At the html validation page :

- The min attribute specifies the minimum value for an `<input>` element.

- put the `required` in `inputs` tag

- Add a `*` to specify it is a required field


---

#### add bootstrap 

cdn (content delivery network) | local copy
-|-
|bootstrap cdn went down, the website still have the formatting
doesn't inspect the css style in the IDE | IDE will detect the css file locally

- `table striped`

- `table hover`

- grid system 
	- `col-md-2` : medium 2 column wide
	- `offset-md-2`
- navbar
- other css frameworks :
	- [material design](https://material.io/design/)
	- [pure css](https://purecss.io/)

#### version controls

- What is git ?
	- archiving system
	

- why is it important?
	- prevent from damaging files
	- keep different versions of the code, it's easy to collaborate with others
	
	
- github
	- one portal that is related to git
	- git repo web-based hosting service
	- free offsite backup
	- employeers mostly require the github account

- commit 
	- new version
	- every commit has a message or description to describe what work got added or changed in the version

- branch
	- different branch can divide the work and have their own branch
	- have their own copy while working on the same project
	- when all of the branch is done, we can merge all branches to 	master branch

- README.md
	- the description of the project 
	
	
- `git init` 
	- initialize `git` in this folder, so git will start watching for changes 
	- only to do once

- `git add .`
	- whatever in this folder, add it into this version
	- everytime I want to save the new version 
	
- `git commit -m "your_message"`
	- `commit` : new version
	- `-m` : message
	- `"your_message"` : what do we do in this version of the project
	
- `git remote add origin the_url`
	- link the folder to this particular url(repo)
	- connect the local folder and remote folder 
	
- `git push origin master`
	- `origin` : your local files
	- `master` : master branch
	- copy all the files in this folder up to github

- `git pull`
	- download the latest version of project to the local machine
	- it also directly integrates it into your current working copy files
	- merge conflicts can happen
	
- `git fetch`	
	- download the latest version of project to the local machine
	- but it doesn't integrate any of this new data into your working files







