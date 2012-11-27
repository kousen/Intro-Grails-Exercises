The Quest for the Holy Grails
-----------------------------

1. *The Google Visualization Plugin*

    a. Check out the plugin documentation at [http://grails.org/plugin/google-visualization]('http://grails.org/plugin/google-visualization').
    This plugin shows how to use Google visualizations in your applications without writing any JavaScript.
    
    b. Open the file `BuildConfig.groovy` in the `grails-app/conf` folder. In the `plugins` section, add:
    
        compile ':google-visualization:0.6'
        
    c. Open the `GeocoderService` class. Add a method to return the required headers
    
        def headers() {
            [['number','Lat'], ['number','Lng'], ['string','Name']]
        }
        
    d. Add a method to return the latitude / longitude / name triples for each castle
    
        def data() {
            Castle.list().collect { c ->
                [c.latitude, c.longitude, c.toString()]
            }
        }
        
2. *Using the service in a controller*

    a. The `list` action in `CastleController` already returns a list of castles and the total. Add the headers and data to the 
    returned map
    
        def list() {
            // ...
            [castleInstanceList: Castle.list(), castleInstanceTotal: Castle.count(),
             mapColumns: geocoderService.headers(), mapData: geocoderService.data()]
        }
        
3. *Updating the view*

    a. Open the `list.gsp` file in the `grails-app\views\castle` folder. Add the required library tag to the header.
    
        <head>
            ...
            <gvisualization:apiImport />
        </head>
        
    b. Somewhere in the body, add the map tag from the plugin
    
        <gvisualization:map elementId="map" columns="${mapColumns}" data="${mapData}" />
        
    c. Below that tag, add a div to hold the map
    
        <div id="map" style="width: 100%;"></div>
        
    d. Restart the server and browse to the castle list page. The map should now appear.
    
    e. To see the castle names when you mouse over them, add the `showTip` property to the map tag
    
        <gvisualization:map ... showTip="${true}" />
        
4. *Congratulations! The app is complete!*

    Other potential additions include producing JSON and/or XML representations of the domain classes, using the Grails Ajax tags,
    working with other plugins like Quartz, Searchable, or Spring Security, and much more...