# What

Just another simple ORM for PHP. You can use it, but don't ask why I made it. Right? :-)

# How

## Includes

Include the `torm.php` to include the other classes needed: `Connection`,
`Model` and `Collection`. 

## Connection

### PDO and drivers

We use PDO to create the connections. Create and send your connection (on this
example, stored on the `$con` var) and set the name of the database driver:

    <?php
        TORM\Connection::setConnection($con);
        TORM\Connection::setDriver("sqlite");
    ?>

The database driver will be used if needed some specific feature of the database. Drivers can be set for each environment (see below).

### Current supported databases

1. SQLite 
2. Oracle (still working on it, soon will be ready, still needing autoincrement and limit)

### Environments

There are three environments where connections and drivers can be set: *development*, *test* and production.

You can send the connection enviroment **after** the PDO connection object:

    <?php
        TORM\Connection::setConnection($con,"test");
    ?>

The current environment can be set with the `TORM_ENV` enviroment variable.

Or, with Apache, to select the current enviroment, for example, to production, on an Apache server 
with `.htaccess` allowed, insert into the `.htaccess` file:

   SetEnv TORM_ENV production

## Models

Define your models where you want like

    <?php
        class User extends TORM\Model {};
        User::$order = "name";
    ?>

include them and use like

    <?php
        // this will search for user with id 1
        $user = User::find(1);

        // this will create a new user
        $user = new User();
        $user->name  = "John Doe";
        $user->email = "john@doe.com";
        $user->level = 1
        $user->save();
    ?>

### Models methods

1. **setTableName** - The default behaviour is use the name of the current class
   with a 's' on the end (all right, lacks a lot of pluralization but we're
   starting simple). Change to the table name you want.
2. **order** - Default sort order.
3. **pk** - Primary key column. Defaults to `id`.
4. **ignorecase** - Process all columns as lower case. Defaults to `true`.

### Validations

Some validations are provided:

1. **Presence** (not empty): `User::validates("name" ,array("presence"=>true))`
2. **Regular expression**: `User::validates("email",array("format"  =>"^[_a-z0-9-]+(\.[_a-z0-9-]+)*@[a-z0-9-]+(\.[a-z0-9-]+)*(\.[a-z]{2,4})$"))`
3. **Uniqueness**: `User::validates("email",array("uniqueness"=>true))`
4. **Numericality**: `User::validates("id",array("numericality"=>true))`

You can check for an valid object using `$user->isValid()`.

### Collections

Some methods, like `where`, returns `Collection` objects, which acts like an iterator, using the `next` method or, with PHP 5.4 and above, the `foreach` loop:

    <?php
        $users = User::where(array("level"=>1));
        var_dump($users->next();
        
        $users = User::where(array("level"=>1));
        foreach($users as $user)
            var_dump($user);        
    ?>
    
### Fluent collections

With methods that returns `Collection`, we can use a fluent operation like this one:

    <?php
        $users = User::where(array("level"=>1)).limit(5).order("name desc");
    ?>    

The `order` clause overwrite the default one.

# Log

You can enable log messages with:

    <?php
        TORM\Log::enable(true);
    ?>
    
# Features

1. It uses prepared statements to run queries. This way, all values are automatically converted to safe ones on the database.
2. As prepared statements can be some more expensive than running direct queries, there is a cache for each prepared statement created.
3. Code is very small. And I'll try to keep it this way. :-)

# Test

Go to the `test` directory and use `PHPUnit` to test it like this:

    $export TORM_ENV=test
    $phpunit torm.php
