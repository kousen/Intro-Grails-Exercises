The Quest for the Holy Grails
-----------------------------

1. *Dynamic finders*

    a. After loading the bootstrap data, try a couple of dynamic finders (one at a time, or add `println` in front of each).

        Quest.findByName('Seek the grail')
        Task.findAllByPriorityGreaterThan(2)
        Task.findAllByPriorityLessThanAndStartDateBetween(4, new Date() - 1, new Date() + 1)

    b. Try out the `count` method and others

        Task.count()
        Task.listOrderByName()

2. *Criteria Queries*

    a. Do a basic criteria query on `Task`

        Task.withCriteria {
            ilike('name', 's%')
        }

    b. Add conditions

        Task.withCriteria {
            ilike('name', '%e%')
            between('startDate', new Date() - 1, new Date() + 1)
            lt('priority', 4)
        }

    b. Ordering works, too

        Task.withCriteria {
            ilike('name', '%e%')
            between('startDate', new Date() - 1, new Date() + 1)
            lt('priority', 4)
            order('name', 'asc')
        }

    c. Try a criteria query on `Quest` that uses a join

        Quest.withCriteria {
            ilike('name', '%e%')
            tasks {
              ilike('name', '%e%')
              gte('priority', 2)
            }
        }

3. *Where queries*

    a. Try a non-lazy where query

        Task.findAll {
            priority > 2
        }

    b. Add a like clause

        Task.findAll {
            priority > 2 && name ==~ '%e%'
        }

    c. Use an `in` clause

        Task.findAll {
            priority in [1, 3, 5]
        }

    d. Define a lazy query and invoke it

        def query = Task.where {
            priority > 2
        }

        query.list()

    e. Sort the results by name

        def query = Task.where {
            priority > 2
        }

        query.list(order:'name')

    f. Can even do associations and sub-queries

        def query = Quest.where {
            name ==~ '%e%'
            tasks {
                priority >= avg(priority)
            }
        }

        query.list(sort:'name')
