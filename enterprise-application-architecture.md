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

- Identity Map

  Ensures that each object gets loaded only once by keeping every loaded object in a map. Looks up objects using the map when referring to them.

  ![identity map](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/11fig03a.jpg)

  An old proverb says that a man with two watches never knows what time it is. If two watches are confusing, you can get in an even bigger mess with loading objects from a database. If you aren’t careful you can load the data from the same database record into two different objects. Then, when you update them both you’ll have an interesting time writing the changes out to the database correctly.

  An Identity Map keeps a record of all objects that have been read from the database in a single business transaction. Whenever you want an object, you check the Identity Map first to see if you already have it.

- Lazy Load

  An object that doesn’t contain all of the data you need but knows how to get it.

  ![lazy load](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/11fig03b.jpg)

  A Lazy Load interrupts this loading process for the moment, leaving a marker in the object structure so that if the data is needed it can be loaded only when it is used. As many people know, if you’re lazy about doing things you’ll win when it turns out you don’t need to do them at all.

## OBJECT RELATIONAL STRUCTURAL PATTERNS 

- Identity Field

  Saves a database ID field in an object to maintain identity between an in-memory object and a database row.

  ![identity field](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig01a.jpg)

  Relational databases tell one row from another by using key—in particular, the primary key. However, in-memory objects don’t need such a key, as the object system ensures the correct identity under the covers (or in C++’s case with raw memory locations). Reading data from a database is all very well, but in order to write data back you need to tie the database to the in-memory object system.

  In essence, Identity Field is mind-numbingly simple. All you do is store the primary key of the relational database table in the object’s fields.

- Foreign Key Mapping

  Maps an association between objects to a foreign key reference between tables.

  ![foreign key mapping](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig01c.jpg)

  Objects can refer to each other directly by object references. Even the simplest object-oriented system will contain a bevy of objects connected to each other in all sorts of interesting ways. To save these objects to a database, it’s vital to save these references. However, since the data in them is specific to the specific instance of the running program, you can’t just save raw data values. Further complicating things is the fact that objects can easily hold collections of references to other objects. Such a structure violates the first normal form of relational databases.

  A Foreign Key Mapping maps an object reference to a foreign key in the database.

- Association Table Mapping

  Saves an association as a table with foreign keys to the tables that are linked by the association.

  ![association table mapping](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig06b.jpg)

  Objects can handle multivalued fields quite easily by using collections as field values. Relational databases don’t have this feature and are constrained to single-valued fields only. When you’re mapping a one-to-many association you can handle this using Foreign Key Mapping, essentially using a foreign key for the single-valued end of the association. But a many-to-many association can’t do this because there is no single-valued end to hold the foreign key.

  The answer is the classic resolution that’s been used by relational data people for decades: create an extra table to record the relationship. Then use Association Table Mapping to map the multivalued field to this link table.

- Dependent Mapping

  Has one class perform the database mapping for a child class.

  ![dependent mapping](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig06c.jpg)

  Some objects naturally appear in the context of other objects. Tracks on an album may be loaded or saved whenever the underlying album is loaded or saved. If they aren’t referenced to by any other table in the database, you can simplify the mapping procedure by having the album mapper perform the mapping for the tracks as well—treating this mapping as a dependent mapping.

- Embedded Value

  Maps an object into several fields of another object’s table.

  ![embedded value](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig07a.jpg)

  Many small objects make sense in an OO system that don’t make sense as tables in a database. Examples include currency-aware money objects and date ranges. Although the default thinking is to save an object as a table, no sane person would want a table of money values.

  An Embedded Value maps the values of an object to fields in the record of the object’s owner. In the sketch we have an employment object with links to a date range object and a money object. In the resulting table the fields in those objects map to fields in the employment table rather than make new records themselves.

- Serialized Lob

  Saves a graph of objects by serializing them into a single large object (LOB), which it stores in a database field.

  ![serialized lob](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig07b.jpg)

  Object models often contain complicated graphs of small objects. Much of the information in these structures isn’t in the objects but in the links between them. Consider storing the organization hierarchy for all your customers. An object model quite naturally shows the composition pattern to represent organizational hierarchies, and you can easily add methods that allow you to get ancestors, siblings, descendents, and other common relationships.

  Not so easy is putting all this into a relational schema. The basic schema is simple—an organization table with a parent foreign key, however, its manipulation of the schema requires many joins, which are both slow and awkward.

  Objects don’t have to be persisted as table rows related to each other. Another form of persistence is serialization, where a whole graph of objects is written out as a single large object (LOB) in a table this Serialized LOB then becomes a form of memento [Gang of Four].

- Single Table Inheritance 

  Represents an inheritance hierarchy of classes as a single table that has columns for all the fields of the various classes.

  ![single table inheritance](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig07c.jpg)

  Relational databases don’t support inheritance, so when mapping from objects to databases we have to consider how to represent our nice inheritance structures in relational tables. When mapping to a relational database, we try to minimize the joins that can quickly mount up when processing an inheritance structure in multiple tables. Single Table Inheritance maps all fields of all classes of an inheritance structure into a single table.

- Class Table Inheritance

  Represents an inheritance hierarchy of classes with one table for each class.

  ![class table inheritance](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig08a.jpg)

  A very visible aspect of the object-relational mismatch is the fact that relational databases don’t support inheritance. You want database structures that map clearly to the objects and allow links anywhere in the inheritance structure. Class Table Inheritance supports this by using one database table per class in the inheritance structure.

## OBJECT RELATIONAL METADATA MAPPING PATTERNS 

## WEB PRESENTATION PATTERNS 

## DISTRIBUTION PATTERNS 

## OFFLINE CONCURRENCY PATTERNS 

## SESSION STATE PATTERNS 

## BASE PATTERNS 
