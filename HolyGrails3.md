The Quest for the Holy Grails
-----------------------------

1. *Unit test the Quest*

    a. Open the file `test\unit\nfjs\QuestTests.groovy` and add a `Quest` attribute with a name
    
        class QuestTests {
            Quest q = new Quest(name:'name')
        }
        
    b. Add a `setUp` method with JUnit's `@Before` annotation to mock the domain class for unit testing:
    
        @Before
        void setUp() {
            mockForConstraintsTests(Quest)
        }
        
    c. Add a test to check that the quest attribute is valid
    
        void testValid() {
            assert q.validate()
        }
        
    d. Add a test to check that an empty name fails as expected
    
        void testBlankName() {
            q.name = ' '
            assert !q.validate()
            assert 'blank' == q.errors.name
        }
        
    e. Execute the unit test and check the test report
    
        grails test-app -unit
        
2. *Unit test the Task*

    a. Open the file `test\unit\nfjs\TaskTests.groovy` and add a valid `Task` attribute
    
        Task t = new Task(name:'name')
        
    b. Add a `setUp` method with a `@Before` annotation. Add a quest to the task and mock it for unit testing
    
        @Before
        void setUp() {
            t.quest = new Quest(name:'name')
            mockForConstraintsTests(Task)
        }
        
    c. Verify that the task is valid
    
        void testValid() {
            assert t.validate()
        }
        
    d. Test that a blank name fails as expected
    
        void testBlankName() {
            t.name = ''
            assert !t.validate()
            assert 'blank' == t.errors.name
        }
        
    e. Check that priorities too low fail
    
        void testPriorityTooLow() {
            t.priority = 0
            assert !t.validate()
            assert 'range' == t.errors.priority
        }
        
    f. Do the same for priorities too high
    
        void testPriorityTooHigh() {
            t.priority = 6
            assert !t.validate()
            assert 'range' == t.errors.priority
        }
        
    g. Check that valid priorities work properly
    
        void testPrioritiesOk() {
            (1..5).each { p ->
                t.priority = p
                assert t.validate()
            }
        }
        
    h. Check that end dates before the start date fail
    
        void testEndDateBeforeStartDate() {
            t.endDate = t.startDate - 1
            assert !t.validate()
            assert 'validator' == task.errors.endDate
        }
        
    i. In each case, run the unit tests and correct any errors