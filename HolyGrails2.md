The Quest for the Holy Grails
-----------------------------

1. *Add a `Task` class*

    a. Use the `create-domain-class` wizard to make a new domain class called `nfjs.Task`
    
    b. Add the following attributes to the `Task`
    
        class Task {
            String name
            int priority = 3
            Date startDate = new Date()
            Date endDate = new Date()
            boolean completed
        }
        
    c. Add constraints to the `Task` properties
    
        static constraints = {
            name blank:false
            priority range: 1..5
            startDate()
            endDate validator: { val, task ->
                val >= task.startDate
            }
            completed()
        }
        
    d. Create a scaffolded controller for the `Task` using the mechanism from the previous exercise.
    
2. *Relationships*

    a. Establish a one-to-many relationship between `Quest` and `Task` by adding to the `Quest.groovy` file:
    
        static hasMany = [tasks:Task]
        
    b. Ensure that deleting a quest deletes all associated tasks by adding to `Task.groovy`:
    
        static belongsTo = [quest:Quest]
        
    c. Re-start the server, add a quest, save it, then add a few tasks. Note that you can traverse from a quest
    to its tasks and from a task to the quest it belongs to. Delete the quest and note how the tasks also vanish.
    
    d. Override the default `toString` method in `Quest`:
    
        String toString() { name }
        
    e. Generate a `toString` method in `Task` using the AST Transformation:
    
        import groovy.transform.ToString
        @ToString
        class Task {
            // ...
        }
        
