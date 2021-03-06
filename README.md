## ay\PDO

Extended PDO with strict-type parameter binding, deffered database connection, query and prepared statement logging (including the parameters used to execute the statement), and debugging features.

### Prepared statement chaining and inline strict-type parameter binding

#### The native PDO implementation

```PHP
$sth = $db
	->prepare("SELECT 1 FROM `foo` WHERE `bar_id` = :bar_id AND `baz` = :baz;");

$sth->bindValue('bar_id', 1, PDO::PARAM_INT);
$sth->bindValue('baz', 'qux');

$sth->execute();

$sth->fetch(PDO::FETCH_ASSOC);
```

#### The ay\PDO implementation

```PHP
$db
	->prepare("SELECT 1 FROM `foo` WHERE `bar_id` = i:bar_id AND `baz` = s:baz;")
	->execute(['bar_id' => 1, 'baz' => 'qux'])
	->fetch(PDO::FETCH_ASSOC);
```

Native PDO implementation returns boolean value indicating either success or failure. `ay\PDO` will return `false` in case of a failure and instance of the `PDOStatement` otherwise.

Instead of using `bindValue` to define the parameter type, you can prefix the placeholder with either parameter type single-character reference, e.g. `i` for integer, `s` for string, etc.

### Inserting data

#### The native PDO implementation

```PHP
$db
	->prepare("INSERT INTO `foo` SET `bar` = :bar;")
	->execute(['bar' => 'qux']);

$foo_id = $db->lastInsertId();
```

#### The ay\PDO implementation

```PHP
$foo_id = $db
	->prepare("INSERT INTO `foo` SET `bar` = s:bar;")
	->execute(['bar' => 'qux']);
```

This is done by checking if the executed query starts with "INSERT" string. It does not catter for "REPLACE" scenarion.

## ay\pdo\log\PDO

Enables logging of all the queries, including prepared statement and the respective parameters. Queries can be retrieved as an array using ay\PDO `getQueryLog` method.

## ay\pdo\debug\PDO

Utilises `ay\pdo\log\PDO` extension to log the queries and MySQL profiling (http://dev.mysql.com/doc/refman/5.5/en/show-profile.html) to produce a breakdown of every query execution time.

## ay\pdo\deferred\PDO

Used when majority of the requests are handled from the cache-database. `ay\pdo\deferred\PDO` does not establish connection to the database until at least one query is executed.