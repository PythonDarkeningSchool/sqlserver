# SQL

## Setup

### Create a new instance

In command prompt type the following command

```bash
sqllocaldb create "<instanceName>"
sqllocaldb start "<instanceName>"
```

### Open the instance into Microsoft SQL Server Management Studio 2017

Once you open `Microsft SQL Server Management Studio 2017` the following window will appear

![open_a_instance](img/open_a_instance.JPG)

The important fields are:

- Server name: `LocalDb`\\<instanceName>
- Windows Authentication

### Create a new database

In the `Object Explorer` (right side), right click to see the `New Database` option

## ![create_new_database](img/create_new_database.JPG)

## Definition

SQL stands for `Structured Query Language` is a special-purpose programming language

## Purpose

- To manipulate sets of data
- Typically from a relational database
- ANSI and ISO standards

## Working with Tables

### Create a new table

Click on the `New Query` button and then insert the following

```sql
create table Users(
	email varchar(50)
);
```

> Where
>
> - create table => means literally to create a new table
> - Users = > the table's name
>   - email => the column name
>     - varchar => that the content of the row will be of type character
>       - 50 = the row's length

Once you type the code, click on execute button, then right click over `Tables` folder to refresh the tables due to `SQL Server Management Studio` does not refresh automatically

To see the table created go to next path

`Databases` => *<*dataBaseName*>* => Tables 

![database_create_columns](img/database_create_columns.JPG)

### Delete a table

To drop a table type the following content into a `New Query`

```sql
drop table Users;
```

> Where
>
> - Users => is the table's name

Same as above, refresh in order to update the content

## Primary Keys

### Integer Primary Key

`Primary key` gives an unique id in the table, e.g:

```sql
create table Users(
	Id Integer primary key
);
```

> Where
>
> - Id => is the name of the column
>   - Integer => is the type of data that will be in each row
>   - primary key => specifies explicitly that it would be a `primary key`

output:

![primary_key](img/primary_key.JPG)

### GUID Primary Keys

```sql
create table Users(
	Id uniqueidentifier primary key
);
```

output

![uniqueidentifier](img/uniqueidentifier.JPG)

### Incrementing primary key

The use of this is to increase the `Id` column into 1 when a new data would be inserted in the table

```sql
create table Users(
	Id integer primary key identity(1,1),
    email varchar(50)
);
```

> where:
>
> - identity -> the first `1` means that it will start from that number, the second `1` it means that the increment would be of `1`

### Incrementing GUID Primary Keys

```sql
create table Users(
	Id uniqueidentifier primary key default newid(),
    email varchar(50)
);
```



>  where:
>
> - default newid() => it's a built-in function that will return a new GUID like `75sdsad1-245dsadsa ...`

### Tangent: Choosing a key

- GUID cons
  - it's quite large
  - will take a space
- Integer cons
  - is limited to 2147483647 integers



The better options for big database is the GUID

### Composite Primary Keys

The following structure is know as `many to many relationships`

```sql
create table Users(
	Id integer primary key identity(1,1),
    email varchar(50) not null
);
create table Users_Roles(
	UserId integer,
    RoleId integer,
    primary key(UserId, RoleId)
);
create table Roles(
	Id integer primary key identity(1,1),
    Name varchar(50)
);
```

> Where:
>
> primary key(UserId, RoleId) => is to avoid duplicates in the table `Users_Roles`

## Defining columns

```sql
create table Users(
	Id integer primary key identity(1,1) not null,
    Email varchar(25) not null unique,
    MoneySpent decimal(10,2),
    CreateAt datetime not null,
    First varchar(25),
    Last varchar(25),
    Bio varchar(max)
);
```

> Where:
>
> not null => means that this fields is required and cannot be empty
>
> unique => means that this value cannot be repeated in the table
>
> decimal(10,2) => means that it will accept a maximum of 10 as length with 2 decimals (this will rounded any decimal)
>
> CreateAt datetime => You must specified a timestamp `00:00:00 00:00:00.000`
>
> Bio varchar(max)=> it will accept the maximum allowed length for characters in this cell

## Columns defaults

```sql
create table Users(
	Id integer primary key identity(1,1) not null,
    Email varchar(25) not null unique,
    MoneySpent decimal(10,2) default 0,
    CreateAt datetime not null default getdate(),
    First varchar(25),
    Last varchar(25),
    Bio varchar(max)
);
```

> Where:
>
> default 0 => means that if the user does not specify any value for this cell the default value will be 0
>
> default getdate() => means that if the user does not specify any date the default value will be the current date

 # Working with Data

## Inserting Data

```sql
INSERT INTO Users(Email, CreateAt, First, Last, Bio)
VALUES('test@test.com', getdate(), 'Test', 'User', 'Some person');
```

> Where:
>
> INSERT INTO <tableName>(<columnName1>,<columnName1>)
>
> VALUES => must be follow the order from above

Note: `SQL-Server` is not case sensitive, and it uses *single quotes* for `varchar`

To consult the data create type the following command:

```sql
SELECT * FROM Users;
```

Output:

![select_from_users_output](img/select_from_users_output.JPG)



## Bulk Inserts

To Demonstrate how to perform a `bulk insert` in a database we will use `Chinook Database`

### Chinook Database

Chinook is a sample database available for SQL Server, Oracle, MySQL, etc. It can be created by running a single SQL script. 

#### Setup

Step 1 - Download the following files for `SQL-Server` from [DataSources](https://github.com/lerocha/chinook-database/tree/master/ChinookDatabase/DataSources)

:link: [Chinook_SqlServer.sql](https://raw.githubusercontent.com/lerocha/chinook-database/master/ChinookDatabase/DataSources/Chinook_SqlServer.sql)

:link: [Chinook_SqlServer_AutoIncrementPKs.sql](https://raw.githubusercontent.com/lerocha/chinook-database/master/ChinookDatabase/DataSources/Chinook_SqlServer_AutoIncrementPKs.sql)

Step 2 - Open them in `Microsoft SQL Server Management Studio` and execute them with `F5`

Output:

![chinook_database_example](img/chinook_database_example.JPG)

### Inserting the data

We have to use `dbo.Customer` table from `Chinook` to insert some columns into `dbo.Users` table

```sql
INSERT INTO Users(Email, First, Last)
SELECT Email, FirstName, LastName
FROM Chinook.dbo.Customer
```

Considerations

In the case that some column in the database differ in length, e.g:

| Column Name | Data Type   | VS   | Column Name | Data Type  |
| ----------- | ----------- | ---- | ----------- | ---------- |
| Email       | nvachar(40) |      | Email       | vachar(30) |

There is a problem because you cannot insert the data from the table `A` to table `B` since the length is different, in this case you have to perform the following steps in `Microsoft SQL Server Management Studio`

>  Tools > Options > Designers > Table and Database Designers > `uncheck` => "Prevent saving changes that require table re-creation"

Now, right click under the table with less length and select `Design`, then increase the `Data Type` correspondent.

Close the `Design` window and save the changes performed



Generally speaking, `Microsoft SQL Management Studio` will properly handle dependencies when you modify a table in a way that requires it to be recreated (it will do assorted tricks with temp tables behind the scenes, which you can see if you tell it to generate a change script instead of actually making the changes). However, this may involve modifications/changes to related tables or foreign keys. In other words, don't do this on a live system if you can help it, and make sure you've got backups before you do it.

## Updating Data

### Single record

Structure

```sql
UPDATE <tableName> SET
<columnName> = '09/23/2014'
WHERE <columnName> = <someId>;
```

e.g:

```sql
UPDATE Users SET
CreateAt = '09/23/2014'
WHERE Id=1;
```

### Bulk Updates

```sql
UPDATE Users SET
CreateAt = '09/23/2012'
WHERE Id <= 10;
```

## Deleting Data

### Single record

```sql
DELETE from Users
WHERE Id=1;
```

### Bulk Delete

#### With Criteria

```sql
DELETE from Users
WHERE Id <= 10;
```

#### All table

```sql
DELETE from Users;
```



# Querying Data

## Simple Select

```sql
SELECT *
from <tableName>
```

> Where:
>
> \* => this wildcard means that will select everything from the table

## Select Columns

```sql
SELECT <columnName>,<columnName>
-- SELECT *
from <tableName>
```

> Where:
>
> `-- ` => is the way to put comments in `SQL Server`

## Aliasing Columns

```sql
SELECT <columnName> as 'someName', <columnName> as 'someOtherName'
from <tableName>
```

With the above command the original columns name will be overwriting with the alias assigned

## Column Expressions

```sql
SELECT <columnName> + ' ' + <otherColumnName> as 'customColumnName'
from <tableName>
```

The above code will `concatenate` two columns in one with a custom column name, if we omitted the alias  we will have `No column name` because the concatenation of two columns will be the value instead of the column name

## Spaces into Columns Names

### Create a column with spaces

To add a column name with a space the code is the following:

```sql
ALTER TABLE <tableName> ADD [<columnName>] VARCHAR(50) NULL
```

> Where:
>
> - `VARCHAR(50)` and `NULL` as parameters and could be replaced by others parameters

Notice that `columnName` is surrounded by *braces* and this is because the column name will have spaces

### Consult a column with spaces

```sql
SELECT [column Name]
from <tableName>;
```



# Database

## Definition

- A database is
  - A container to help organize data
  - A way to efficiently store and retrieve data

## Relational model

A way to describe data and the relationships between data entities

> Database design is important, it controls the questions you can ask later
>
> `SQL` is the language you use to ask the questions



# Basic SQL Syntax

A `SQL` statement is an expression that tells a database what you want it to do

![basic_sql_syntax](img/basic_sql_syntax.JPG)

# SQL commands

## SELECT

> *SELECT* statement retrieves one or more rows from one or more tables

e.g

```sql
SELECT first_name, last_name FROM contacts;
```

Output:

| id   | first_name | last_name |
| ---- | ---------- | --------- |
| 1    | Jon        | Flanders  |

## INSERT

> Adds one or more rows into a table

e.g:

```sql
INSERT INTO contacts(first_name, last_name) VALUES("Fritz", "Onion");
```

Where `contacts` is the table to consult

Output:

| id   | first_name | last_name |
| ---- | ---------- | --------- |
| 1    | Jon        | Flanders  |
| 2    | Fritz      | Onion     |

## UPDATE

> Modifies one or more rows in a table

```sql
UPDATE contants SET last_name="Ahern" WHERE id=1;
```

Where `contacts` is the table to consult

## DELETE

> Removes one or more rows from one table

```sql
DELETE FROM contacts WHERE id = 2;
```

Where `contacts` is the table to consult