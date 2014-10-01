The Quest for the Holy Grails
-----------------------------

1. *Create the Sakila database*

    (Note: if you already have the Sakila database, skip this step.)

    a. Download the zip file for the Sakila database from http://downloads.mysql.com/docs/sakila-db.zip and unzip it.

    b. Log into MySQL with root access.

    c. Source the sql files. Supply the full path to the sql files, using forward slashes in the path name.

    (Note: more detailed installation instructions can be found at https://dev.mysql.com/doc/sakila/en/sakila-installation.html)

        mysql> source sakila-schema.sql;
        ...
        mysql> source sakila-data.sql;
        ...

    d. Verify the number of cities.

        mysql> use sakila;
        mysql> select count(*) from city;
        +----------+
        | count(*) |
        +----------+
        |      600 |
        +----------+
        1 row in set (0.00 sec)

2. *Create a new Grails app for Sakila*

    a. Create a Grails app

        > grails create-app sakila

    b. Change the data source to use the sakila database by editing grails-app/conf/DataSource.groovy.

    dataSource {
        driverClassName = 'com.mysql.jdbc.Driver'
        username = 'root'
        password = ''
        dialect = MySQL5InnoDBDialect  // add import for org.hibernate.dialect.*
    }

    c. Change the JDBC URL and `create-drop` value in both the development and test environments.

    environments {
        development {
            dataSource {
                dbCreate = "validate"
                url = "jdbc:mysql://localhost:3306/sakila"
            }
        }
        test {
            dataSource {
                dbCreate = "validate"
                url = "jdbc:mysql://localhost:3306/sakila"
            }
        }
        ...
    }

3. *Map the Country table*

    a. Examine the definition of the COUNTRY table:

        mysql> desc country;
       +-------------+----------------------+------+-----+-------------------+-----------------------------+
        | Field       | Type                 | Null | Key | Default           | Extra                       |
          +-------------+----------------------+------+-----+-------------------+-----------------------------+
        | country_id  | smallint(5) unsigned | NO   | PRI | NULL              | auto_increment              |
        | country     | varchar(50)          | NO   |     | NULL              |                             |
        | last_update | timestamp            | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
          +-------------+----------------------+------+-----+-------------------+-----------------------------+
          3 rows in set (0.00 sec)

    b. Create a domain class for the Country table.

        > grails create-domain-class Country

    (Note: since no package was specified, this will put the class in the `sakila` pacakage)

    c. The default data type for the `id` field is `Long`, but since MySQL is using `SMALLINT` map it to `Short`

        class Country {
            Short id
        }

    d. Add a `name` attribute that will map to `country`

        class Country {
            Short id
            String name
        }

    e. Add a `lastUpdated` attribute to map to the `last_update` field

        class Country {
            Short id
            String name
            Date lastUpdated
        }

    f. Add a `mapping` block to map the attributes to columns

        static mapping = {

        }

    g. Map the `id`, `name`, and `lastUpdated` attributes to the corresponding columns.

        static mapping = {
            id column: 'country_id'
            name column: 'country'
            lastUpdated column: 'last_update'
        }

    h. Since there's no `version` column, be sure to turn off optimistic locking

        static mapping = {
            id column: 'country_id'
            name column: 'country'
            lastUpdated column: 'last_update'
            version false
        }

    i. Add the constraint that the `name` can not be blank

        static constraints = {
            name blank:false
        }

    j. Create a scaffolded controller for `Country`

        class CountryController {
            static scaffold = true
        }

    k. Start the app and look at the countries through the dynamic scaffolding.

3. (Optional) *Map the City table*

    a. Create a domain class for the `CITY` table

        > grails create-domain-class City

    b. Check the definition of the CITY table in the Sakila database

        mysql> desc city;

                    +-------------+----------------------+------+-----+-------------------+-----------------------------+
                    | Field       | Type                 | Null | Key | Default           | Extra                       |
                      +-------------+----------------------+------+-----+-------------------+-----------------------------+
                    | city_id     | smallint(5) unsigned | NO   | PRI | NULL                  | auto_increment              |
                    | city        | varchar(50)          | NO   |     | NULL              |                             |
                    | country_id  | smallint(5) unsigned | NO   | MUL | NULL              |                             |
                    | last_update | timestamp            | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
                    +-------------+----------------------+------+-----+-------------------+-----------------------------+
                    4 rows in set (0.02 sec)

    c. Add attributes to the `City` class

        class City {
            Short id
            String name
            Date lastUpdated
        }

    d. Add the `mapping` block

        static mapping = {
            id column: 'city_id'
            name column: 'city'
            lastUpdated column: 'last_update'
            version false
        }

    e. Add the constraint that `name` can't be blank

        static constraints = {
            name blank:false
        }

    f. Add a scaffolded controller for City

        class CityController {
            static scaffold = true
        }

5. *Add a bidirectional, one-to-many relationship with cascade delete*

    a. In `Country`, add a collection of cities

        static hasMany = [cities: City]

    b. In `City`, add the bidirectional association with cascade delete

        static belongsTo = [country: Country]

    c. Add a `toString` method in each class.

        class City {
            String toString() { name }
        }

        class Country {
            String toString() { name }
        }

    d. (Re)start the server and browse the `City` and `Country` values.

Note: There are many ways to map the Sakila database. To see one solution, look at https://github.com/kousen/sakila.
