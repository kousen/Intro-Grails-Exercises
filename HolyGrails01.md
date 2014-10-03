The Quest for the Holy Grails
-----------------------------

1. *Create the application*

    a. Create an application called `holygrails` and move into the application directory
    
2. *Add a domain class and a scaffolded controller*

    a. Create a domain class called `nfjs.Quest` using the `create-domain-class` wizard.
    
    b. Add an attribute called `name` of type `String` to the `Quest` class.
    
        class Quest {
            String name
            // ...
        }
    
    c. If you are using the command line, create a controller for the `Quest` using the command:
        
        grails create-scaffold-controller nfjs.Quest
        
    d. Otherwise, use the regular `create-controller` command:
    
        grails create-controller nfjs.Quest
        
    Open the resulting controller class, remove the `index` action, and add the scaffolding command.
    
        class QuestController {
            static scaffold = true
        }
        
3. *Avoiding blank names*

    a. Start the embedded Tomcat server:
    
        grails run-app
        
    b. Browse to the application home page, http://localhost:8080/holygrails and select the `QuestController` link
    
    c. Create a new quest with any name you like (e.g., `Seek the grail`), edit it, and (optionally) delete it
    
    d. Create a quest with a blank name. You'll see that Grails is perfectly happy to do so.
    
    e. In the `Quest` class, in the `constraints` section, add the restriction that the `name` cannot be blank:
    
        static constraints = {
            name(blank:false)
        }
        
    f. Try again to create a quest with a blank name. On browsers that support HTML5, you won't be able to submit the
    form unless you add a few empty spaces in the name field.
    
    g. The Grails user guide shows that the default error key for blank constraints is `className.propertyName.blank`. 
    In the `messages.properties` file in the `i18n` directory add the line:
    
        quest.name.blank=Quests must have a name
        
    Re-submitting the quest with a blank name should give the new error message.
    