The Quest for the Holy Grails
-----------------------------

1. *SQL logging*

    a. Turn on SQL logging by opening the file `grails-app/conf/DataSource.groovy` and adding:

        dataSource {
            // ...
            logSql = true
            formatSql = true
        }

    b. Optionally, in the Hibernate section of the same file, add:

        hibernate {
            use_sql_comments = true
            // ...
        }

2. *Add initialization data*

    a. Add some sample quests and tasks in the `grails-app/conf/BootStrap.groovy` file

        import nfjs.*
        class BootStrap {
            def init = { servletContext ->
                Quest q = new Quest(name:'Seek the grail')
                    .addToTasks(name:'Run away from killer rabbit')
                    .addToTasks(name:'Answer the bridgekeeper', priority:4)
                    .addToTasks(name:'Defeat the Black Knight', completed:true)
                    .save()
            }
        }

3. *The Grails Console*

    a. Start the Grails console

        grails console

    b. In the console, import the `nfjs` package

        import nfjs.*

    c. Show all the quests

        Quest.list()

    The result should be an empty list, showing that the bootstrap code is not being executed

    d. Save a new, invalid quest

        Quest q = new Quest().save()

    The result should be empty, even missing the SQL, because the Quest doesn't have a name

    e. Make the save operation throw an exception if the quest is not valid

        Quest q = new Quest().save(failOnError:true)

    f. Add the `failOnError:true` value to the `save` in the bootstrap. Note that this will not change anything until you
    restart the console.

    g. Execute the bootstrap code in the console

        new BootStrap().init()

    You should see insert statements for one quest and three tasks (or however many you added in the bootstrap).

    h. Verify that the quest and tasks have been saved

        println Quest.list()
        println Task.list()

    i. Check the `id` and `name` properties of the `Task` instances

        println Task.list()*.id
        println Task.list()*.name

    h. Verify that you can traverse from a task to its quest

        println Task.get(1).quest.name
