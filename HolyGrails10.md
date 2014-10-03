The Quest for the Holy Grails
-----------------------------

1. *Set up the Sakila data source*

    If you haven't already done so, set up the Sakila database and data source by performing Steps 1 and 2 from the previous exercise.

2. *Create a service*

    a. Create a service called `StoredProcService` in the `sakila` package. Be careful not to put the word `Service` at the end of the command.

        > grails create-service StoredProc

    b. To test a stored procedure, we need to use the real database. Therefore, create an integration test for the new service. The wizard that created the service generated a unit test called `StoredProcServiceTests`, so we need to use a different name.

        > grails create-integration-test StoredProcIntegration

3. *Implement the integration test*

    a. The documentation for the `film_in_stock` stored procedure is located at https://dev.mysql.com/doc/sakila/en/sakila-structure-procedures-film_in_stock.html. Note that it takes two input parameters, 'p_film_id' and 'p_store_id', representing the film and store, and one output parameter, 'p_film_count', which gives the number of copies of that film at that store.

    The documentation shows that for film 1 at store 1, the procedure returns 4, so implement that as the test.

    b. Since this is an integration test, we can take advantage of Grails's dependency injection for the service.

        class StoredProcServiceIntegrationTests {
            def storedProcService
        }

    c. Implement the test.

        class StoredProcServiceIntegrationTests {
            def storedProcService

            void testGetFilmsInStock() {
                assert storedProcService.getFilmsInStock(1,1) == 4
            }
        }

    d. In order to run the test, stub out the method in the `StoredProcService`

        class StoredProcService {
            int getFilmsInStock(filmId, storeId) {
                return 0
            }
        }

    e. Now run the test and watch it fail.

        > grails test-app integration: cc.StoredProcServiceIntegration

4. *Implement the service*

    a. Inject the data source into the service

        class StoredProcService {
            def dataSource

            int getFilmsInStock(filmId, storeId) {
                return 0
            }
        }

    b. Define the stored procedure call using the JDBC escape syntax (inside the `getFilmsInStock` method)

        String sqlTxt = '{call film_in_stock(?,?,?)}'

    c. Use the `groovy.sql.Sql` class to connect.

        Sql db = new Sql(dataSource)  \\ import groovy.sql.Sql

    d. Implement the call

        db.call(sqlTxt, [filmId, storeId, Type.INTEGER]) { count ->
            println count
        }

    e. Since returning from a closure only returns from the closure and not the method, use a temporary variable to return the result. Here is the complete class.

        class StoredProcService {
            def dataSource

            int getFilmsInStock(filmId, storeId) {
                String sqlTxt = '{call film_in_stock(?,?,?)}'
                Sql db = new Sql(dataSource)
                int result = 0
                db.call(sqlTxt, [filmId, storeId, Type.INTEGER]) { count ->
                    println count
                    result = count
                }
                return result
            }
        }

    f. The integration test should now run successfully.
