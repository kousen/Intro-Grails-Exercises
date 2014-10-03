The Quest for the Holy Grails
-----------------------------

1. *Knights*

    a. Add a `Knight` domain class

        grails create-domain-class nfjs.Knight

    b. Knights have titles and names

        class Knight {
            String title
            String name
        }

    c. Add appropriate constraints

        static constraints = {
            title inList: ['Sir', 'Lord', 'King', 'Squire']
            name blank: false
        }

    d. Quests have many knights, too

        class Quest {
            // ...
            static hasMany = [tasks:Task, knights:Knight]
        }

    e. Knights know which quest they are on

        class Knight {
            Quest quest
        }

    f. Knights can be between quests

        static constraints = {
            // as above, and
            quest nullable: true
        }

    g. Add a `toString` method

        String toString() { "$title $name" }

    h. Create a scaffolded controller for `Knight`

        grails create-scaffold-controller nfjs.Knight

    If you're using an IDE, you can just create the controller and replace the `index` action with
    the `static scaffold = true` command, as usual.

2. *Castles*

    a. Add a `Castle` domain class

        grails create-domain-class nfjs.Castle

    b. Castles are really just named locations

        class Castle {
            String name
            String city
            String state
            double latitude
            double longitude
        }

    c. Add appropriate constraints

        static constraints = {
            name blank:false
            city blank:false
            state blank:false
            latitude min:-90d, max:90d  // South Pole to North Pole
            longitude()
        }

    d. Castles need a `toString` method, too

        String toString() { "$name Castle" }

    e. Castles contain knights

        static hasMany = [knights:Knight]

    f. Knights know which castle they are associated with

        class Knight {
            // as before
            Castle castle
        }

    g. But Knights can be between castles, too

        static constraints = {
            // other Knight constraints
            castle nullable: true
        }

    h. Add a some castles and knights to the bootstrap data

        def init = { servletContext ->
            // existing quest and tasks
            Castle camelot = new Castle(name:'Camelot', city:'Boston', state:'MA')
                .addToKnights(title:'King', name:'Arthur', quest:q)
                .addToKnights(title:'Sir', name:'Lancelot', quest:q)
                .addToKnights(title:'Sir', name:'Robin', quest:q)
                .save(failOnError:true)
        }

    i. Add another castle near by (will be useful later)

        def init = { servletContext ->
            // others as above
            Castle swamp = new Castle(name:'Swamp', city:'Hollywood', state:'CA')
                .save(failOnError:true)
        }

3. *Generate a Castle controller*

    a. This time, instead of creating a controller, generate it. In fact, generate both the controller and the views.

        grails generate-all nfjs.Castle

    This will provide the complete `CastleController` code, as well as all the GSP files in the `grails-app\views\castle` folder.

4. *Look at generated schema*

    a. Run the `schema-export` command

        grails schema-export

    Check out the `ddl.sql` file in the target directory

    b. Start the server

        grails run-app

    c. Check out the db console at http://localhost:8080/holygrails/dbconsole.
    Be sure to use the same settings as those in the `DataSource.groovy` file.
