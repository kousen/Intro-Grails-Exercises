The Quest for the Holy Grails
-----------------------------

1. *Declare the JDBC driver dependency*

    a. Open the `BuildConfig.groovy` file in the `conf` folder

    b. Uncomment the line that declares the MySQL JDBC driver dependency (the version number may be different but doesn't matter here)

        dependencies {
            runtime 'mysql:mysql-connector-java:5.1.20'
        }

2. *Change the data source settings*

    a. Open the file `DataSource.groovy` in the `conf` folder

    b. In the `dataSource` block, update settings (other settings can remain as they are now):

        dataSource {
            driverClassName = 'com.jdbc.mysql.Driver'
            username = 'grails'
            password = 'groovy'
        }

    c. In the same block, declare the Hibernate dialect

        dataSource {
            // ... as above ...
            dialect = com.hibernate.dialect.MySql5InnoDBDialect
        }

    (Note: can use an import statement for the package if you want)

    d. Change the JDBC URL in the development environment

        environments {
            development {
                dataSource {
                    dbCreate = 'create-drop'
                    url = 'jdbc:mysql://localhost:3306/holygrails'
                }
            }
        }

    e. Do the same for the `url` in the `test` environment

    environments {
        test {
            dataSource {
                dbCreate = 'create-drop'
                url = 'jdbc:mysql://localhost:3306/holygrails'
            }
        }
    }

3. *Create the database and user in MySQL*

    a. Use whatever client tool you prefer (here a command prompt) to log into MySQL with root access

        > mysql -u root -p
        Password: *******

    b. Create the holygrails database (don't forget the semicolon)

        mysql> create database holygrails;

    c. Create a user called 'grails' with password 'groovy' for that database only. This assumes you are accessing a MySQL instance on your local machine -- adjust the address accordingly if you are connecting to a remote instance

        mysql> grant all on holygrails.* to grails@localhost identified by 'groovy';

4. *Access the new database*

    a. Use the new database. There shouldn't be any tables yet.

        mysql> use holygrails;
        mysql> show tables;

    b. Restart the application. After it starts, there should be 4 tables.

        mysql> show tables;
        +----------------------+
        | Tables_in_holygrails |
        +----------------------+
        | castle               |
        | knight               |
        | quest                |
        | task                 |
        +----------------------+
        4 rows in set (0.00 sec)

    c. Describe a table and check its contents

        mysql> desc castle;
        +-----------+--------------+------+-----+---------+----------------+
        | Field     | Type         | Null | Key | Default | Extra          |
        +-----------+--------------+------+-----+---------+----------------+
        | id        | bigint(20)   | NO   | PRI | NULL    | auto_increment |
        | version   | bigint(20)   | NO   |     | NULL    |                |
        | city      | varchar(255) | NO   |     | NULL    |                |
        | latitude  | double       | NO   |     | NULL    |                |
        | longitude | double       | NO   |     | NULL    |                |
        | name      | varchar(255) | NO   |     | NULL    |                |
        | state     | varchar(255) | NO   |     | NULL    |                |
        +-----------+--------------+------+-----+---------+----------------+
        7 rows in set (0.02 sec)

        mysql> select * from castle;
        +----+---------+--------+------------+-------------+---------+-------+
        | id | version | city   | latitude   | longitude   | name    | state |
        +----+---------+--------+------------+-------------+---------+-------+
        |  1 |       1 | Boston | 42.3584308 | -71.0597732 | Camelot | MA    |
        +----+---------+--------+------------+-------------+---------+-------+
        1 row in set (0.00 sec)
