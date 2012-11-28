The Quest for the Holy Grails
-----------------------------

1. *Dynamic finders*

    a. After loading the bootstrap data, try a couple of dynamic finders (one at a time, or add `println` in front).
    
        Quest.findByName('Seek the grail')
        Task.findAllByPriorityGreaterThan(2)
        Task.findAllByPriorityLessThanAndStartDateBetween(4, new Date() - 1, new Date() + 1)
        
    b. Try out counters and others
    
        Task.count()
        Task.listOrderByName()
        
2. *Criteria Queries*

    a. Do a basic criteria query
    
        Quest.withCriteria {
            ilike('name', 's%')
        }
    
    b. Add conditions
    
        Task.withCriteria {
            between('startDate', new Date() - 1, new Date() + 1)
            like('name', '%e%')
        }
        
    b. Ordering works, too
    
        Task.withCriteria {
            between('startDate', new Date() - 1, new Date() + 1)
            like('name', '%e%')
            lt('priority', 4)
            order('name', 'asc')
        }
        
