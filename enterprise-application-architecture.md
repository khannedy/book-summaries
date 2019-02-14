# ENTERPRISE APPLICATION ARCHITECTURE

## DOMAIN LOGIC PATTERNS

- Transaction Script 

  Organizes business logic by procedures where each procedure handles a single request from the presentation.

  ![service](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig01a.jpg)

  A Transaction Script organizes all this logic primarily as a single procedure, making calls directly to the database or through a thin database wrapper. Each transaction will have its own Transaction Script, although common subtasks can be broken into subprocedures.

  My preference is to separate Transaction Scripts as much as you can. At the very least put them in distinct subroutines; better still, put them in classes separate from those that handle presentation and data source.

  ![command](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig01.jpg)

  The glory of Transaction Script is its simplicity. Organizing logic this way is natural for applications with only a small amount of logic, and it involves very little overhead either in performance or in understanding.

  As the business logic gets more complicated, however, it gets progressively harder to keep it in a well-designed state. One particular problem to watch for is its duplication between transactions. Since the whole point is to handle one transaction, any common code tends to be duplicated.

- Domain Model

  An object model of the domain that incorporates both behavior and data.

  ![domain model](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig02a.jpg)

  A simple Domain Model looks very much like the database design with mostly one domain object for each database table. A rich Domain Model can look different from the database design, with inheritance, strategies, and other [Gang of Four] patterns, and complex webs of small interconnected objects

- Table Module

  A single instance that handles the business logic for all rows in a database table or view.

  ![table module](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig03a.jpg)

  A Table Module organizes domain logic with one class per table in the database, and a single instance of a class contains the various procedures that will act on the data. The primary distinction with Domain Model is that, if you have many orders, a Domain Model will have one order object per order while a Table Module will have one object to handle all orders.

- Service Layer

  Defines an application’s boundary with a layer of services that establishes a set of available operations and coordinates the application’s response in each operation.

  ![service layer](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig06a.jpg)

  The benefit of Service Layer is that it defines a common set of application operations available to many kinds of clients and it coordinates an application’s response in each operation. The response may involve application logic that needs to be transacted atomically across multiple transactional resources. Thus, in an application with more than one kind of client of its business logic, and complex responses in its use cases involving multiple transactional resources, it makes a lot of sense to include a Service Layer with container-managed transactions, even in an undistributed architecture.

## DATA SOURCE PATTERNS

- Table Data Gateway 

  An object that acts as a Gateway to a database table. One instance handles all the rows in the table.

  ![table data gateway](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/10fig01a.jpg)

  A Table Data Gateway holds all the SQL for accessing a single table or view: selects, inserts, updates, and deletes. Other code calls its methods for all interaction with the database.

- Row Data Gateway

  An object that acts as a Gateway to a single record in a data source. There is one instance per row.

  ![row data gateway](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/10fig01b.jpg)

- Active Record
  
  An object that wraps a row in a database table or view, encapsulates the database access, and adds domain logic on that data.

  ![active record](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/10fig02a.jpg)

  An object carries both data and behavior. Much of this data is persistent and needs to be stored in a database. Active Record uses the most obvious approach, putting data access logic in the domain object. This way all people know how to read and write their data to and from the database.

- Data Mapper

  A layer of Mappers that moves data between objects and a database while keeping them independent of each other and the mapper itself.

  ![data mapper](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/10fig02b.jpg)

  Objects and relational databases have different mechanisms for structuring data. Many parts of an object, such as collections and inheritance, aren’t present in relational databases. When you build an object model with a lot of business logic it’s valuable to use these mechanisms to better organize the data and the behavior that goes with it. Doing so leads to variant schemas; that is, the object schema and the relational schema don’t match up.

## OBJECT RELATIONAL BEHAVIORAL PATTERNS

- Unit of Work

  Maintains a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems.

  ![unit of work](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/11fig01a.jpg)

  A Unit of Work keeps track of everything you do during a business transaction that can affect the database. When you’re done, it figures out everything that needs to be done to alter the database as a result of your work.

## OBJECT RELATIONAL STRUCTURAL PATTERNS 

## OBJECT RELATIONAL METADATA MAPPING PATTERNS 

## WEB PRESENTATION PATTERNS 

## DISTRIBUTION PATTERNS 

## OFFLINE CONCURRENCY PATTERNS 

## SESSION STATE PATTERNS 

## BASE PATTERNS 
