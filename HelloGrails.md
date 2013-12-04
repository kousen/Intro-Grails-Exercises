Exercises
==========

1. *Bookmark References*
    
    Bookmark the following sites for future reference:  
        [Groovy homepage](http://groovy.codehaus.org)  
        [Groovy API](http://groovy.codehaus.org/gapi/)  
        [Groovy JDK](http://groovy.codehaus.org/groovy-jdk/)  
        [Grails homepage](http://grails.org/)  
        [Grails user guide](http://grails.org/doc/latest/)
        
2. *A new Grails application*

    a. Create a new Grails application called `hello` using the `grails create-app` command. Once it completes, change directory into the `hello` app.
    
    b. Use the `create-controller com.mycompany.Welcome` command to create a new controller called `com.mycompany.WelcomeController`. Where is the resulting file stored? Where is the test class?
    
    c. In the `WelcomeController` class, what is the default action? What URL corresponds to this action? What would happen if we started the application and accessed this action now? (If you're not sure, try it and see.)
    
3. *Render*

    a. Inside the `index` action in the `WelcomeController` class, add a line rendering the phrase `Hello, World!`.
    
        render '<h2>Hello, World!<\h2>'
    
    b. Start the application (if not already started) using the `grails run-app` command and click on the `WelcomeController` link to access the action.
    
    c. Inside the `index` action, declare a `String` variable called `n`. Assign it to the value of the `name` property in the incoming request. If `params.name` is null or empty, default to the word `World`. Change the render to use the `n` value.
    
    (Note: this is a perfect excuse to use the Elvis operator from Groovy.)
    
        String n = params.name ?: 'World'
        render "<h2>Hello, $n!</h2>"

4. *Redirect*

    a. Add a new action to the `WelcomeController` called `greet`, which takes a single `String` argument called `name` and returns `def`. What is the URL corresponding to this action?
    
    b. Implement the `greet` action to use the `name` argument (note: only slight variation on current `index` implementation):
    
        String n = name ?: 'World'
        render "<h2>Hello, $n!</h2>"
    
    c. In the `index` action, redirect to the `greet` action (note the parentheses below are optional).
    
        redirect(action:'greet')
        
    d. Access the URL corresponding to the `index` action, _without_ supplying a `name` parameter. Verify that you see the string 'Hello, World!' in the display.
    
    e. Add a `name` parameter to the URL for the `index` action and access the URL. Does the parameter get passed to the `hello` action? Why not?
    
    f. Add a second argument to the `redirect` method to pass the input parameters to the next request and try again. The supplied name should now show in the response.
    
        redirect(action:'greet', params:params)
        
5. *Return*

    a. Add a new action called `hi` to the `WelcomeController` class, with no arguments.
    
    b. This time, have the action return a map with one entry. The key in the entry should be `person` and the value is the `n` variable. Remember, the `return` keyword itself is optional. The complete implementation is shown below.
    
        def hi() {
            String n = params.name ?: 'World'
            [person:n]
        }
        
    When the controller action completes, where will Grails go next?
        
    c. Create a Groovy Server Page called `hi.gsp` in the `welcome` folder under `grails-app\views`. Give it a simple HTML structure (root element of `<html>`, with a `<head>` section and a `<body>` section). In the head section, add a `meta` tag to tell Grails to use the default SiteMesh layout.
        
    In the body, add a `<div>` element with the CSS class `body`. Inside the `<div>`, add an `<h2>` element that displays "Hello, ${person}!".  The complete code is shown below.
        
        <html>
            <head>
                <meta name="layout" content="main">
                <title>Greeting</title>
            </head>
            <body>
                <div class="body">
                    <h2>Hello, ${person}!</h2>
                </div>
            </body>
        </html>
        
    d. Access the `hi` action with a URL, once without a `name` parameter and once with one.
    
6. *Testing the Controller*

    a. Open the `WelcomeControllerTests` class in the `hello\test\unit\com\mycompany\` directory. As of Grails 2.3, the default testing mechanism uses the Spock plugin, so the test extends `spock.lang.Specification`.
    
    b. Add tests for the `greet` action both with an without a name. Use Spock's `when/then` construct to do so:
    
        void "greet without name should say Hello, World!"() {
    		when:
    		controller.greet()
    		then:
    		response.text == '<h2>Hello, World!</h2>'
        }

    	void "greet with name should say Hello, name"() {
    		when:
    		controller.greet('Dolly')
    		then:
    		response.text == '<h2>Hello, Dolly!</h2>'
    	}
    
    c. Add tests for the `index` action both with an without a name by invoking the method on the `controller` reference and checking that the re-directed URLs are correct. To supply a name, add it to the `params` map before invoking the action.
    
        void "index without name should redirect to greet without name"() {
    		when:
    		controller.index()
    		then:
    		response.redirectedUrl == '/welcome/greet'
    	}

    	void "index with name should redirect to greet with a parameter"() {
    		when:
    		params.name = 'Dolly'
    		controller.index()
    		then:
    		response.redirectedUrl == '/welcome/greet?name=Dolly'
    	}
    
    d. Add two tests for the `hi` action, one with a name and one without. Check that the returned map has the proper `person` key in it.
    
        void "hi without name should return map with person == 'World'"() {
    		when:
    		def model = controller.hi()
    		then:
    		model.person == 'World'
    	}

    	void "hi with name should return map with person == name"() {
    		when:
    		params.name = 'Dolly'
    		def model = controller.hi()
    		then:
    		model.person == 'Dolly'
    	}
    
    e. Execute the `grails test-app com.mycompany.WelcomeController` and check the resulting output to make sure everything works.