The Quest for the Holy Grails
-----------------------------

1. *Google Geocoder Docs*

    a. The Google Geocoder documentation is at [https://developers.google.com/maps/documentation/geocoding/]('https://developers.google.com/maps/documentation/geocoding/').
It's probably worth a bookmark.

2. *Creating a Service*

    a. Create the geocoder service

        grails create-service nfjs.Geocoder

    b. Add a public, static, final attribute representing the base URL for the geocoder to the `GeocoderService` class. Be
    sure to include the trailing question mark.

        class GeocoderService {
            public static final String BASE = 'http://maps.googleapis.com/maps/api/geocode/xml?'
        }

    c. Add a method to fill in the latitude and longitude values for a `Castle`.

        void fillInLatLng(Castle c) {

        }

    d. Create an encoded address using a list and the collect and join methods.

        void fillInLatLng(Castle c) {
            String encoded = [c.city, c.state].collect {
                URLEncoder.encode(it, 'UTF-8')
            }.join(',+')
        }

    e. Build a query string using a map and the encoded address

        void fillInLatLng(Castle c) {
            // from above...
            String qs = "address=$encoded"
        }

    f. Transmit the URL to Google and parse the response XML.

        // from above...
        def root = new XmlSlurper().parse("$BASE$qs")

    g. Update the latitude and longitude of the castle by walking the tree

        // from above...
        def loc = root.result[0].geometry.location
        c.latitude = loc.lat.toDouble()
        c.longitude = loc.lng.toDouble()

3. *Test the Geocoder service*

    a. Open the `GeocoderServiceTests` class and add a method to test for Google headquarters. Note that because of the
    `@TestFor` annotation, you don't need to instantiate the service.

        void testMountainViewCA() {
            Castle google = new Castle(city:'Mountain View', state:'CA')
            service.fillInLatLng(google)
            assert (  37.4 - google.latitude).abs() < 0.1
            assert (-122.1 - google.longitude).abs() < 0.1
        }

    b. Run the unit tests until the service test passes.

4. *Injecting a service*

    a. Inject the service into the castle controller by adding an attribute to the top of the class:

        class CastleController {
            def geocoderService
            // ...
        }

    b. In the `save` action, just before the actual call to `save()`, update the castle

        def save() {
            def castleInstance = new Castle(params)
            geocoderService.fillInLatLng(castleInstance)
            // ...
        }

    c. Do the same in the `update` action

        def update() {
            // ...
            castleInstance.properties = params
            geocoderService.fillInLatLng(castleInstance)
            // ...
        }

    d. Inject the service into `BootStrap.groovy` as well

        class BootStrap {
            def geocoderService
            // ...
        }

    e. Use the service to update the castles in the bootstrap before saving them

        def init = { servletContext ->
            // ...
            Castle camelot = ...
                .addToKnights(...)
            geocoderService.fillInLatLng(camelot)
            camelot.save()
        }

    f. Do the same for Swamp castle in the bootstrap

5. *Use the service in the application*

    a. Restart the application and browse to the list of castles (access the castle controller)

    b. Add other castles, using any city and state you wish. Note that the state is interpreted broadly -- you can use country codes,
    country names, or anything else that Google understands.
