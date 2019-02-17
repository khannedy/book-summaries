# Patterns of Enterprise Application Architecture

## Domain Logic

### Transaction Script 

Organizes business logic by procedures where each procedure handles a single request from the presentation.

![service](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig01a.jpg)

A Transaction Script organizes all this logic primarily as a single procedure, making calls directly to the database or through a thin database wrapper. Each transaction will have its own Transaction Script, although common subtasks can be broken into subprocedures.

My preference is to separate Transaction Scripts as much as you can. At the very least put them in distinct subroutines; better still, put them in classes separate from those that handle presentation and data source.

![command](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig01.jpg)

The glory of Transaction Script is its simplicity. Organizing logic this way is natural for applications with only a small amount of logic, and it involves very little overhead either in performance or in understanding.

As the business logic gets more complicated, however, it gets progressively harder to keep it in a well-designed state. One particular problem to watch for is its duplication between transactions. Since the whole point is to handle one transaction, any common code tends to be duplicated.

### Domain Model

An object model of the domain that incorporates both behavior and data.

![domain model](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig02a.jpg)

A simple Domain Model looks very much like the database design with mostly one domain object for each database table. A rich Domain Model can look different from the database design, with inheritance, strategies, and other [Gang of Four] patterns, and complex webs of small interconnected objects

### Table Module

A single instance that handles the business logic for all rows in a database table or view.

![table module](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig03a.jpg)

A Table Module organizes domain logic with one class per table in the database, and a single instance of a class contains the various procedures that will act on the data. The primary distinction with Domain Model is that, if you have many orders, a Domain Model will have one order object per order while a Table Module will have one object to handle all orders.

### Service Layer

Defines an application’s boundary with a layer of services that establishes a set of available operations and coordinates the application’s response in each operation.

![service layer](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/09fig06a.jpg)

The benefit of Service Layer is that it defines a common set of application operations available to many kinds of clients and it coordinates an application’s response in each operation. The response may involve application logic that needs to be transacted atomically across multiple transactional resources. Thus, in an application with more than one kind of client of its business logic, and complex responses in its use cases involving multiple transactional resources, it makes a lot of sense to include a Service Layer with container-managed transactions, even in an undistributed architecture.

## DATA SOURCE PATTERNS

### Table Data Gateway 

An object that acts as a Gateway to a database table. One instance handles all the rows in the table.

![table data gateway](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/10fig01a.jpg)

A Table Data Gateway holds all the SQL for accessing a single table or view: selects, inserts, updates, and deletes. Other code calls its methods for all interaction with the database.

### Row Data Gateway

An object that acts as a Gateway to a single record in a data source. There is one instance per row.

![row data gateway](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/10fig01b.jpg)

### Active Record

An object that wraps a row in a database table or view, encapsulates the database access, and adds domain logic on that data.

![active record](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/10fig02a.jpg)

An object carries both data and behavior. Much of this data is persistent and needs to be stored in a database. Active Record uses the most obvious approach, putting data access logic in the domain object. This way all people know how to read and write their data to and from the database.

### Data Mapper

A layer of Mappers that moves data between objects and a database while keeping them independent of each other and the mapper itself.

![data mapper](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/10fig02b.jpg)

Objects and relational databases have different mechanisms for structuring data. Many parts of an object, such as collections and inheritance, aren’t present in relational databases. When you build an object model with a lot of business logic it’s valuable to use these mechanisms to better organize the data and the behavior that goes with it. Doing so leads to variant schemas; that is, the object schema and the relational schema don’t match up.

## OBJECT RELATIONAL BEHAVIORAL PATTERNS

### Unit of Work

Maintains a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems.

![unit of work](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/11fig01a.jpg)

A Unit of Work keeps track of everything you do during a business transaction that can affect the database. When you’re done, it figures out everything that needs to be done to alter the database as a result of your work.

### Identity Map

Ensures that each object gets loaded only once by keeping every loaded object in a map. Looks up objects using the map when referring to them.

![identity map](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/11fig03a.jpg)

An old proverb says that a man with two watches never knows what time it is. If two watches are confusing, you can get in an even bigger mess with loading objects from a database. If you aren’t careful you can load the data from the same database record into two different objects. Then, when you update them both you’ll have an interesting time writing the changes out to the database correctly.

An Identity Map keeps a record of all objects that have been read from the database in a single business transaction. Whenever you want an object, you check the Identity Map first to see if you already have it.

### Lazy Load

An object that doesn’t contain all of the data you need but knows how to get it.

![lazy load](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/11fig03b.jpg)

A Lazy Load interrupts this loading process for the moment, leaving a marker in the object structure so that if the data is needed it can be loaded only when it is used. As many people know, if you’re lazy about doing things you’ll win when it turns out you don’t need to do them at all.

## OBJECT RELATIONAL STRUCTURAL PATTERNS 

### Identity Field

Saves a database ID field in an object to maintain identity between an in-memory object and a database row.

![identity field](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig01a.jpg)

Relational databases tell one row from another by using key—in particular, the primary key. However, in-memory objects don’t need such a key, as the object system ensures the correct identity under the covers (or in C++’s case with raw memory locations). Reading data from a database is all very well, but in order to write data back you need to tie the database to the in-memory object system.

In essence, Identity Field is mind-numbingly simple. All you do is store the primary key of the relational database table in the object’s fields.

### Foreign Key Mapping

Maps an association between objects to a foreign key reference between tables.

![foreign key mapping](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig01c.jpg)

Objects can refer to each other directly by object references. Even the simplest object-oriented system will contain a bevy of objects connected to each other in all sorts of interesting ways. To save these objects to a database, it’s vital to save these references. However, since the data in them is specific to the specific instance of the running program, you can’t just save raw data values. Further complicating things is the fact that objects can easily hold collections of references to other objects. Such a structure violates the first normal form of relational databases.

A Foreign Key Mapping maps an object reference to a foreign key in the database.

### Association Table Mapping

Saves an association as a table with foreign keys to the tables that are linked by the association.

![association table mapping](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig06b.jpg)

Objects can handle multivalued fields quite easily by using collections as field values. Relational databases don’t have this feature and are constrained to single-valued fields only. When you’re mapping a one-to-many association you can handle this using Foreign Key Mapping, essentially using a foreign key for the single-valued end of the association. But a many-to-many association can’t do this because there is no single-valued end to hold the foreign key.

The answer is the classic resolution that’s been used by relational data people for decades: create an extra table to record the relationship. Then use Association Table Mapping to map the multivalued field to this link table.

### Dependent Mapping

Has one class perform the database mapping for a child class.

![dependent mapping](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig06c.jpg)

Some objects naturally appear in the context of other objects. Tracks on an album may be loaded or saved whenever the underlying album is loaded or saved. If they aren’t referenced to by any other table in the database, you can simplify the mapping procedure by having the album mapper perform the mapping for the tracks as well—treating this mapping as a dependent mapping.

### Embedded Value

Maps an object into several fields of another object’s table.

![embedded value](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig07a.jpg)

Many small objects make sense in an OO system that don’t make sense as tables in a database. Examples include currency-aware money objects and date ranges. Although the default thinking is to save an object as a table, no sane person would want a table of money values.

An Embedded Value maps the values of an object to fields in the record of the object’s owner. In the sketch we have an employment object with links to a date range object and a money object. In the resulting table the fields in those objects map to fields in the employment table rather than make new records themselves.

### Serialized Lob

Saves a graph of objects by serializing them into a single large object (LOB), which it stores in a database field.

![serialized lob](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig07b.jpg)

Object models often contain complicated graphs of small objects. Much of the information in these structures isn’t in the objects but in the links between them. Consider storing the organization hierarchy for all your customers. An object model quite naturally shows the composition pattern to represent organizational hierarchies, and you can easily add methods that allow you to get ancestors, siblings, descendents, and other common relationships.

Not so easy is putting all this into a relational schema. The basic schema is simple—an organization table with a parent foreign key, however, its manipulation of the schema requires many joins, which are both slow and awkward.

Objects don’t have to be persisted as table rows related to each other. Another form of persistence is serialization, where a whole graph of objects is written out as a single large object (LOB) in a table this Serialized LOB then becomes a form of memento [Gang of Four].

### Single Table Inheritance 

Represents an inheritance hierarchy of classes as a single table that has columns for all the fields of the various classes.

![single table inheritance](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig07c.jpg)

Relational databases don’t support inheritance, so when mapping from objects to databases we have to consider how to represent our nice inheritance structures in relational tables. When mapping to a relational database, we try to minimize the joins that can quickly mount up when processing an inheritance structure in multiple tables. Single Table Inheritance maps all fields of all classes of an inheritance structure into a single table.

### Class Table Inheritance

Represents an inheritance hierarchy of classes with one table for each class.

![class table inheritance](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig08a.jpg)

A very visible aspect of the object-relational mismatch is the fact that relational databases don’t support inheritance. You want database structures that map clearly to the objects and allow links anywhere in the inheritance structure. Class Table Inheritance supports this by using one database table per class in the inheritance structure.

### Concrete Table Inheritance

Represents an inheritance hierarchy of classes with one table per concrete class in the hierarchy.

![concrete table inheritance](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig09a.jpg)

As any object purist will tell you, relational databases don’t support inheritance—a fact that complicates object-relational mapping. Thinking of tables from an object instance point of view, a sensible route is to take each object in memory and map it to a single database row. This implies Concrete Table Inheritance, where there’s a table for each concrete class in the inheritance hierarchy.

I’ll confess to having had some difficulty naming this pattern. Most people think of it as leaf oriented since you usually have one table per leaf class in a hierarchy. Following that logic, I could call this pattern leaf table inheritance, and the term “leaf” is often used for this pattern. Strictly, however, a concrete class that isn’t a leaf usually gets a table as well, so I decided to go with the more correct, if less intuitive term.

### Inheritance Mappers

A structure to organize database mappers that handle inheritance hierarchies.

![inheritance mappers](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/12fig11a.jpg)

When you map from an object-oriented inheritance hierarchy in memory to a relational database you have to minimize the amount of code needed to save and load the data to the database. You also want to provide both abstract and concrete mapping behavior that allows you to save or load a superclass or a subclass.

Although the details of this behavior vary with your inheritance mapping scheme (Single Table Inheritance, Class Table Inheritance, and Concrete Table Inheritance) the general structure works the same for all of them.

## OBJECT RELATIONAL METADATA MAPPING PATTERNS 

### Metadata Mapping

Holds details of object-relational mapping in metadata.

![metadata mapping](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/13fig01a.jpg)

Much of the code that deals with object-relational mapping describes how fields in the database correspond to fields in in-memory objects. The resulting code tends to be tedious and repetitive to write. A Metadata Mapping allows developers to define the mappings in a simple tabular form, which can then be processed by generic code to carry out the details of reading, inserting, and updating the data.

### Query Object

An object that represents a database query.

![query object](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/13fig01b.jpg)

SQL can be an involved language, and many developers aren’t particularly familiar with it. Furthermore, you need to know what the database schema looks like to form queries. You can avoid this by creating specialized finder methods that hide the SQL inside parameterized methods, but that makes it difficult to form more ad hoc queries. It also leads to duplication in the SQL statements should the database schema change.

A Query Object is an interpreter [Gang of Four], that is, a structure of objects that can form itself into a SQL query. You can create this query by referring to classes and fields rather than tables and columns. In this way those who write the queries can do so independently of the database schema and changes to the schema can be localized in a single place.

### Repository

Mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects.

![repository](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/13fig01c.jpg)

A system with a complex domain model often benefits from a layer, such as the one provided by Data Mapper, that isolates domain objects from details of the database access code. In such systems it can be worthwhile to build another layer of abstraction over the mapping layer where query construction code is concentrated. This becomes more important when there are a large number of domain classes or heavy querying. In these cases particularly, adding this layer helps minimize duplicate query logic.

A Repository mediates between the domain and data mapping layers, acting like an in-memory domain object collection. Client objects construct query specifications declaratively and submit them to Repository for satisfaction. Objects can be added to and removed from the Repository, as they can from a simple collection of objects, and the mapping code encapsulated by the Repository will carry out the appropriate operations behind the scenes. Conceptually, a Repository encapsulates the set of objects persisted in a data store and the operations performed over them, providing a more object-oriented view of the persistence layer. Repository also supports the objective of achieving a clean separation and one-way dependency between the domain and data mapping layers.

## WEB PRESENTATION PATTERNS 

### Model View Controller

Splits user interface interaction into three distinct roles.

![model view controller](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/330fig01.jpg)

MVC considers three roles. The model is an object that represents some information about the domain. It’s a nonvisual object containing all the data and behavior other than that used for the UI. In its most pure OO form the model is an object within a Domain Model (116). You might also think of a Transaction Script as the model providing that it contains no UI machinery. Such a definition stretches the notion of model, but fits the role breakdown of MVC.

The view represents the display of the model in the UI. Thus, if our model is a customer object our view might be a frame full of UI widgets or an HTML page rendered with information from the model. The view is only about display of information; any changes to the information are handled by the third member of the MVC trinity: the controller. The controller takes user input, manipulates the model, and causes the view to update appropriately. In this way UI is a combination of the view and the controller.

### Page Controller

An object that handles a request for a specific page or action on a Web site.

![page controller](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/14fig01b.jpg)

Most people’s basic Web experience is with static HTML pages. When you request static HTML you pass to the Web server the name and path for a HTML document stored on it. The key notion is that each page on the Web site is a separate document on the server. With dynamic pages things can get much more interesting since there’s a much more complex relationship between path names and the file that responds. However, the approach of one path leading to one file that handles the request is a simple model to understand.

As a result, Page Controller has one input controller for each logical page of the Web site. That controller may be the page itself, as it often is in server page environments, or it may be a separate object that corresponds to that page.

The basic idea behind a Page Controller is to have one module on the Web server act as the controller for each page on the Web site. In practice, it doesn’t work out to exactly one module per page, since you may hit a link sometimes and get a different page depending on dynamic information. More strictly, the controllers tie in to each action, which may be clicking a link or a button.

### Front Controller

A controller that handles all requests for a Web site.

![front controller](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/14fig01a.jpg)

In a complex Web site there are many similar things you need to do when handling a request. These things include security, internationalization, and providing particular views for certain users. If the input controller behavior is scattered across multiple objects, much of this behavior can end up duplicated. Also, it’s difficult to change behavior at runtime.

The Front Controller consolidates all request handling by channeling requests through a single handler object. This object can carry out common behavior, which can be modified at runtime with decorators. The handler then dispatches to command objects for behavior particular to a request.

A Front Controller handles all calls for a Web site, and is usually structured in two parts: a Web handler and a command hierarchy. The Web handler is the object that actually receives post or get requests from the Web server. It pulls just enough information from the URL and the request to decide what kind of action to initiate and then delegates to a command to carry out the action

### Template View 

Renders information into HTML by embedding markers in an HTML page.

![template view](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/14fig03a.jpg)

Writing a program that spits out HTML is often more difficult than you might imagine. Although programming languages are better at creating text than they used to be (some of us remember character handling in Fortran and standard Pascal), creating and concatenating string constructs is still painful. If there isn’t much to do, it isn’t too bad, but a whole HTML page is a lot of text manipulation.

The best way to work is to compose the dynamic Web page as you do a static page but put in markers that can be resolved into calls to gather dynamic information. Since the static part of the page acts as a template for the particular response, I call this a Template View.

One of the most popular forms of Template View is a server page such as ASP, JSP, or PHP. These actually go a step further than the basic form of a Template View in that they allow you to embed arbitrary programming logic, referred to as scriptlets, into the page. In my view, however, this feature is actually a big problem and you’re better off limiting yourself to basic Template View behavior when you use server page technology.

### Transform View

A view that processes domain data element by element and transforms it into HTML.

![transform view](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/14fig03b.jpg)

When you issue requests for data to the domain and data source layers, you get back all the data you need to satisfy them, but without the formatting you need to make a proper Web page. The role of the view in Model View Controller is to render this data into a Web page. Using Transform View means thinking of this as a transformation where you have the model’s data as input and its HTML as output.

The basic notion of Transform View is writing a program that looks at domain-oriented data and converts it to HTML. The program walks the structure of the domain data and, as it recognizes each form of domain data, it writes out the particular piece of HTML for it. If you think about this in an imperative way, you might have a method called renderCustomer that takes a customer object and renders it into HTML. If the customer contains a lot of orders, this method loops over the orders calling renderOrder.

### Two Step Views

Turns domain data into HTML in two steps: first by forming some kind of logical page, then rendering the logical page into HTML.

![two step view](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/14fig03c.jpg)

If you have a Web application with many pages, you often want a consistent look and organization to the site. If every page looks different, you end up with a site that users find confusing. You may also want to make global changes to the appearance of the site easily, but common approaches using Template View (350) or Transform View (361) make this difficult because presentation decisions are often duplicated across multiple pages or transform modules. A global change can force you to change several files.

Two Step View deals with this problem by splitting the transformation into two stages. The first transforms the model data into a logical presentation without any specific formatting; the second converts that logical presentation with the actual formatting needed. This way you can make a global change by altering the second stage, or you can support multiple output looks and feels with one second stage each.

### Application Controller

A centralized point for handling screen navigation and the flow of an application.

![application controller](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/14fig09a.jpg)

Some applications contain a significant amount of logic about the screens to use at different points, which may involve invoking certain screens at certain times in an application. This is the wizard style of interaction, where the user is led through a series of screens in a certain order. In other cases we may see screens that are only brought in under certain conditions, or choices between different screens that depend on earlier input.

To some degree the various Model View Controller input controllers can make some of these decisions, but as an application gets more complex this can lead to duplicated code as several controllers for different screens need to know what to do in a certain situation.

You can remove this duplication by placing all the flow logic in an Application Controller. Input controllers then ask the Application Controller for the appropriate commands for execution against a model and the correct view to use depending on the application context.

## DISTRIBUTION PATTERNS 

### Remote Facade

Provides a coarse-grained facade on fine-grained objects to improve efficiency over a network.

![remote facade](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/15fig01a.jpg)

In an object-oriented model, you do best with small objects that have small methods. This gives you lots of opportunity for control and substitution of behavior, and to use good intention revealing naming to make an application easier to understand. One of the consequences of such fine-grained behavior is that there’s usually a great deal of interaction between objects, and that interaction usually requires lots of method invocations.

A Remote Facade is a coarse-grained facade [Gang of Four] over a web of fine-grained objects. None of the fine-grained objects have a remote interface, and the Remote Facade contains no domain logic. All the Remote Facade does is translate coarse-grained methods onto the underlying fine-grained objects.

Use Remote Facade whenever you need remote access to a fine-grained object model. You gain the advantages of a coarse-grained interface while still keeping the advantage of fine-grained objects, giving you the best of both worlds.

### Data Transfer Object

An object that carries data between processes in order to reduce the number of method calls.

![data transfer object](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/15fig03a.jpg)

When you’re working with a remote interface, such as Remote Facade, each call to it is expensive. As a result you need to reduce the number of calls, and that means that you need to transfer more data with each call. One way to do this is to use lots of parameters. However, this is often awkward to program—indeed, it’s often impossible with languages such as Java that return only a single value.

The solution is to create a Data Transfer Object that can hold all the data for the call. It needs to be serializable to go across the connection. Usually an assembler is used on the server side to transfer data between the DTO and any domain objects.

Many people in the Sun community use the term “Value Object” for this pattern. I use it to mean something else. 

## OFFLINE CONCURRENCY PATTERNS 

### Optimistic Offline Lock

Prevents conflicts between concurrent business transactions by detecting a conflict and rolling back the transaction.

![optimistic offline lock](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/16fig01a.jpg)

Often a business transaction executes across a series of system transactions. Once outside the confines of a single system transaction, we can’t depend on our database manager alone to ensure that the business transaction will leave the record data in a consistent state. Data integrity is at risk once two sessions begin to work on the same records and lost updates are quite possible. Also, with one session editing data that another is reading an inconsistent read becomes likely.

Optimistic Offline Lock solves this problem by validating that the changes about to be committed by one session don’t conflict with the changes of another session. A successful pre-commit validation is, in a sense, obtaining a lock indicating it’s okay to go ahead with the changes to the record data. So long as the validation and the updates occur within a single system transaction the business transaction will display consistency.

An Optimistic Offline Lock is obtained by validating that, in the time since a session loaded a record, another session hasn’t altered it. It can be acquired at any time but is valid only during the system transaction in which it is obtained. Thus, in order that a business transaction not corrupt record data it must acquire an Optimistic Offline Lock for each member of its change set during the system transaction in which it applies changes to the database.

The most common implementation is to associate a version number with each record in your system. When a record is loaded that number is maintained by the session along with all other session state. Getting the Optimistic Offline Lock is a matter of comparing the version stored in your session data to the current version in the record data. Once the verification succeeds, all changes, including an increment of the version, can be committed. The version increment is what prevents inconsistent record data, as a session with an old version can’t acquire the lock.

Optimistic concurrency management is appropriate when the chance of conflict between any two business transactions is low. If conflicts are likely it’s not user friendly to announce one only when the user has finished his work and is ready to commit. Eventually he’ll assume the failure of business transactions and stop using the system. Pessimistic Offline Lock is more appropriate when the chance of conflict is high or the expense of a conflict is unacceptable.

### Pessimistic Offline Lock

Prevents conflicts between concurrent business transactions by allowing only one business transaction at a time to access data.

![pessimistic offline lock](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/16fig01b.jpg)

Since offline concurrency involves manipulating data for a business transaction that spans multiple requests, the simplest approach would seem to be having a system transaction open for the whole business transaction. Sadly, however, this doesn’t always work well because transaction systems aren’t geared to work with long transactions. For that reason you have to use multiple system transactions, at which point you’re left to your own devices to manage concurrent access to your data.

The first approach to try is Optimistic Offline Lock. However, that pattern has its problems. If several people access the same data within a business transaction, one of them will commit easily but the others will conflict and fail. Since the conflict is only detected at the end of the business transaction, the victims will do all the transaction work only to find at the last minute that the whole thing will fail and their time will have been wasted. If this happens a lot on lengthy business transactions the system will soon become very unpopular.

Pessimistic Offline Lock prevents conflicts by avoiding them altogether. It forces a business transaction to acquire a lock on a piece of data before it starts to use it, so that, most of the time, once you begin a business transaction you can be pretty sure you’ll complete it without being bounced by concurrency control.

You implement Pessimistic Offline Lock in three phases: determining what type of locks you need, building a lock manager, and defining procedures for a business transaction to use locks. Additionally, if you’re using Pessimistic Offline Lock as a complement to Optimistic Offline Lock you need to determine which record types to lock.

Pessimistic Offline Lock is appropriate when the chance of conflict between concurrent sessions is high. A user should never have to throw away work. Locking is also appropriate when the cost of a conflict is too high regardless of its likelihood. Locking every entity in a system will almost surely create tremendous data contention problems, so remember that Pessimistic Offline Lock is very complementary to Optimistic Offline Lock and only use Pessimistic Offline Lock where it’s truly required.

### Coarse-Grained Lock

Locks a set of related objects with a single lock.

![coarse grained lock](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/16fig01c.jpg)

Objects can often be edited as a group. Perhaps you have a customer and its set of addresses. If so, when using the application it makes sense to lock all of these items if you want to lock any one of them. Having a separate lock for individual objects presents a number of challenges. First, anyone manipulating them has to write code that can find them all in order to lock them. This is easy enough for a customer and its addresses, but it gets tricky as you get more locking groups. And what if the groups get complicated? Where is this behavior when your framework is managing lock acquisition? If your locking strategy requires that an object be loaded in order to be locked, such as with Optimistic Offline Lock, locking a large group affects performance. And with Pessimistic Offline Lock a large lock set is a management headache and increases lock table contention.

A Coarse-Grained Lock is a single lock that covers many objects. It not only simplifies the locking action itself but also frees you from having to load all the members of a group in order to lock them.

The first step in implementing Coarse-Grained Lock is to create a single point of contention for locking a group of objects. This makes only one lock necessary for locking the entire set. Then you provide the shortest path possible to finding that single lock point in order to minimize the group members that must be identified and possibly loaded into memory in the process of obtaining that lock.

The most obvious reason to use a Coarse-Grained Lock is to satisfy business requirements. This is the case when locking an aggregate. Consider a lease object that owns a collection of assets. It probably doesn’t make business sense for one user to edit the lease and another user to simultaneously edit an asset. Locking either the asset or the lease ought to result in the lease and all of its assets being locked.

### Implicit Lock

Allows framework or layer supertype code to acquire offline locks.

![implicit lock](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/16fig01d.jpg)

The key to any locking scheme is that there are no gaps in its use. Forgetting to write a single line of code that acquires a lock can render an entire offline locking scheme useless. Failing to retrieve a read lock where other transactions use write locks means you might not get up-to-date session data; failing to use a version count properly can result in unknowingly writing over someone’s changes. Generally, if an item might be locked anywhere it must be locked everywhere. Ignoring its application’s locking strategy allows a business transaction to create inconsistent data. Not releasing locks won’t corrupt your record data, but it will eventually bring productivity to a halt. Because offline concurrency management is difficult to test, such errors might go undetected by all of your test suites.

One solution is to not allow developers to make such a mistake. Locking tasks that cannot be overlooked should be handled not explicitly by developers but implicitly by the application. The fact that most enterprise applications make use of some combination of framework, Layer Supertypes, and code generation provides us with ample opportunity to facilitate Implicit Lock.

Implementing Implicit Lock is a matter of factoring your code such that any locking mechanics that absolutely cannot be skipped can be carried out by your application framework. For lack of a better word we’ll use “framework” to mean a combination of Layer Supertypes, framework classes, and any other “plumbing” code. Code generation tools are another avenue to enforce proper locking. I realize this is by no means a ground-breaking idea. You’re very likely to head down this path once you’ve coded the same locking mechanics a few times over in your application. Still, I’ve seen it done poorly often enough that it merits a quick look.

Implicit Lock should be used in all but the simplest of applications that have no concept of framework. The risk of a single forgotten lock is too great.

## SESSION STATE PATTERNS 

### Client Session State

Stores session state on the client

Even the most server-oriented designs need at least a little Client Session State, if only to hold a session identifier. With some applications you can consider putting all of the session data on the client, in which case the client sends the full set of session data with each request and the server sends back the full session state with each response. This allows the server to be completely stateless.

With an HTML interface, things get a bit more complicated. There are three common ways to do client session state: URL parameters, hidden fields, and cookies.

Client Session State contains a number of advantages. In particular, it reacts well in supporting stateless server objects with maximal clustering and failover resiliency. Of course, if the client fails all is lost, but often the user expects that anyway.

### Server Session State

Keeps the session state on a server system in a serialized form.

In the simplest form of this pattern a session object is held in memory on an application server. You can have some kind of map in memory that holds these session objects keyed by a session ID; all the client needs to do is to give the session ID and the session object can be retrieved from the map to process the request.

This basic scenario assumes, of course, that the application server carries enough memory to perform this task. It also assumes that there’s only one application server—that is, no clustering—and that, if the application server fails, it’s appropriate for the session to be abandoned and all work done so far to be lost in the great bit-bucket in the sky.

### Database Session State

Stores session data as committed data in the database.

When a call goes out from the client to the server, the server object first pulls the data required for the request from the database. Then it does the work it needs to do and saves back to the database all the data required.

In order to pull information from the database, the server object will need some information about the session, which requires at least a session ID number to be stored on the client. Usually, however, this information is nothing more than the appropriate set of keys needed to find the appropriate amount of data in the database.

Database Session State is one alternative to handling session state; it should be compared with Server Session State and Client Session State.

The first aspect to consider with this pattern is performance. You’ll gain by using stateless objects on the server, thus enabling pooling and easy clustering. However, you’ll pay with the time needed to pull the data in and out of the database with each request. You can reduce this cost by caching the server object so you won’t have to read the data out of the database whenever the cache is hit, but you’ll still pay the write costs.

## BASE PATTERNS 

### Gateway

An object that encapsulates access to an external system or resource.

![gateway](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01a.jpg)

Interesting software rarely lives in isolation. Even the purest object-oriented system often has to deal with things that aren’t objects, such as relational database tables, CICS transactions, and XML data structures.

When accessing external resources like this, you’ll usually get APIs for them. However, these APIs are naturally going to be somewhat complicated because they take the nature of the resource into account. Anyone who needs to understand a resource needs to understand its API—whether JDBC and SQL for relational databases or W3C or JDOM for XML. Not only does this make the software harder to understand, it also makes it much harder to change should you shift some data from a relational database to an XML message at some point in the future.

The answer is so common that it’s hardly worth stating. Wrap all the special API code into a class whose interface looks like a regular object. Other objects access the resource through this Gateway, which translates the simple method calls into the appropriate specialized API.

In reality this is a very simple wrapper pattern. Take the external resource. What does the application need to do with it? Create a simple API for your usage and use the Gateway to translate to the external source.

You should consider Gateway whenever you have an awkward interface to something that feels external. Rather than let the awkwardness spread through the whole system, use a Gateway to contain it. There’s hardly any downside to making the Gateway, and the code elsewhere in the system becomes much easier to read.

### Mapper 

An object that sets up a communication between two independent objects.

![mapper](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01b.jpg)

Sometimes you need to set up communications between two subsystems that still need to stay ignorant of each other. This may be because you can’t modify them or you can but you don’t want to create dependencies between the two or even between them and the isolating element.

A mapper is an insulating layer between subsystems. It controls the details of the communication between them without either subsystem being aware of it.

Essentially a Mapper decouples different parts of a system. When you want to do this you have a choice between Mapper and Gateway. Gateway is by far the most common choice because it’s much simpler to use a Gateway than a Mapper both in writing the code and in using it later.

As a result you should only use a Mapper when you need to ensure that neither subsystem has a dependency on this interaction. The only time this is really important is when the interaction between the subsystems is particularly complicated and somewhat independent to the main purpose of both subsystems. Thus, in enterprise applications we mostly find Mapper used for interactions with a database, as in Data Mapper.

Mapper is similar to Mediator [Gang of Four] in that it’s used to separate different elements. However, the objects that use a mediator are aware of it, even if they aren’t aware of each other; the objects that a Mapper separates aren’t even aware of the mapper.

### Layer Supertype

A type that acts as the supertype for all types in its layer.

It’s not uncommon for all the objects in a layer to have methods you don’t want to have duplicated throughout the system. You can move all of this behavior into a common Layer Supertype.

Layer Supertype is a simple idea that leads to a very short pattern. All you need is a superclass for all the objects in a layer—for example, a Domain Object superclass for all the domain objects in a Domain Model. Common features, such as the storage and handling of Identity Fields, can go there. Similarly all Data Mappers in the mapping layer can have a superclass that relies on the fact that all domain objects have a common superclass.

If you have more than one kind of object in a layer, it’s useful to have more than one Layer Supertype.

Use Layer Supertype when you have common features from all objects in a layer. I often do this automatically because I make a lot of use of common features.

### Separated Interface

Defines an interface in a separate package from its implementation.

![separated interface](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01c.jpg)

As you develop a system, you can improve the quality of its design by reducing the coupling between the system’s parts. A good way to do this is to group the classes into packages and control the dependencies between them. You can then follow rules about how classes in one package can call classes in another—for example, one that says that classes in the domain layer may not call classes in the presentation package.

However, you might need to invoke methods that contradict the general dependency structure. If so, use Separated Interface to define an interface in one package but implement it in another. This way a client that needs the dependency to the interface can be completely unaware of the implementation. The Separated Interface provides a good plug point for Gateway.

This pattern is very simple to employ. Essentially it takes advantage of the fact that an implementation has a dependency to its interface but not vice versa. This means you can put the interface and the implementation in separate packages and the implementation package has a dependency to the interface package. Other packages can depend on the interface package without depending on the implementation package.

You use Separated Interface when you need to break a dependency between two parts of the system.

### Registry

A well-known object that other objects can use to find common objects and services.

![registry](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01d.jpg)

When you want to find an object you usually start with another object that has an association to it, and use the association to navigate to it. Thus, if you want to find all the orders for a customer, you start with the customer object and use a method on it to get the orders. However, in some cases you won’t have an appropriate object to start with. You may know the customer’s ID number but not have a reference. In this case you need some kind of lookup method—a finder—but the question remains: How do you get to the finder?

A Registry is essentially a global object, or at least it looks like one—even if it isn’t as global as it may appear.

As with any object, you have to think about the design of a Registry in terms of interface and implementation. And like many objects, the two are quite different, although people often make the mistake of thinking they should be the same.

Despite the encapsulation of a method, a Registry is still global data and as such is something I’m uncomfortable using. I almost always see some form of Registry in an application, but I always try to access objects through regular inter-object references instead. Basically, you should only use a Registry as a last resort.

There are alternatives to using a Registry. One is to pass around any widely needed data in parameters. The problem with this is that parameters are added to method calls where they aren’t needed by the called method but only by some other method that’s called several layers deep in the call tree. Passing a parameter around when it’s not needed 90 percent of the time is what leads me to use a Registry instead.

### Value Object

A small simple object, like money or a date range, whose equality isn’t based on identity.

With object systems of various kinds, I’ve found it useful to distinguish between reference objects and Value Objects. Of the two a Value Object is usually the smaller; it’s similar to the primitive types present in many languages that aren’t purely object-oriented.

Defining the difference between a reference object and Value Object can be a tricky thing. In a broad sense we like to think that Value Objects are small objects, such as a money object or a date, while reference objects are large, such as an order or a customer. Such a definition is handy but annoyingly informal.

Value Objects shouldn’t be persisted as complete records. Instead use Embedded Value or Serialized LOB. Since Value Objects are small, Embedded Value is usually the best choice because it also allows SQL querying using the data in a Value Object.

Treat something as a Value Object when you’re basing equality on something other than an identity. It’s worth considering this for any small object that’s easy to create.

### Money

Represents a monetary value.

![money](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01e.jpg)

A large proportion of the computers in this world manipulate money, so it’s always puzzled me that money isn’t actually a first class data type in any mainstream programming language. The lack of a type causes problems, the most obvious surrounding currencies. If all your calculations are done in a single currency, this isn’t a huge problem, but once you involve multiple currencies you want to avoid adding your dollars to your yen without taking the currency differences into account. The more subtle problem is with rounding. Monetary calculations are often rounded to the smallest currency unit. When you do this it’s easy to lose pennies (or your local equivalent) because of rounding errors.

The good thing about object-oriented programming is that you can fix these problems by creating a Money class that handles them. Of course, it’s still surprising that none of the mainstream base class libraries actually do this.

The basic idea is to have a Money class with fields for the numeric amount and the currency. You can store the amount as either an integral type or a fixed decimal type. The decimal type is easier for some manipulations, the integral for others. You should absolutely avoid any kind of floating point type, as that will introduce the kind of rounding problems that Money is intended to avoid. Most of the time people want monetary values rounded to the smallest complete unit, such as cents in the dollar. However, there are times when fractional units are needed. It’s important to make it clear what kind of money you’re working with, especially in an application that uses both kinds. It makes sense to have different types for the two cases as they behave quite differently under arithmetic.

Money is a Value Object, so it should have its equality and hash code operations overridden to be based on the currency and amount.

### Special Case

A subclass that provides special behavior for particular cases.

![special case](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01f.jpg)

Nulls are awkward things in object-oriented programs because they defeat polymorphism. Usually you can invoke foo freely on a variable reference of a given type without worrying about whether the item is the exact type or a subclass. With a strongly typed language you can even have the compiler check that the call is correct. However, since a variable can contain null, you may run into a runtime error by invoking a message on null, which will get you a nice, friendly stack trace.

If it’s possible for a variable to be null, you have to remember to surround it with null test code so you’ll do the right thing if a null is present. Often the right thing is the same in many contexts, so you end up writing similar code in lots of places—committing the sin of code duplication.

Instead of returning null, or some odd value, return a Special Case that has the same interface as what the caller expects.

The basic idea is to create a subclass to handle the Special Case. Thus, if you have a customer object and you want to avoid null checks, you make a null customer object. Take all of the methods for customer and override them in the Special Case to provide some harmless behavior. Then, whenever you have a null, put in an instance of null customer instead.

Use Special Case whenever you have multiple places in the system that have the same behavior after a conditional check for a particular class instance, or the same behavior after a null check.

### Plugin

Links classes during configuration rather than compilation.

![plugin](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01g.jpg)

Separated Interface is often used when application code runs in multiple runtime environments, each requiring different implementations of particular behavior. Most developers supply the correct implementation by writing a factory method. Suppose you define your primary key generator with a Separated Interface (476) so that you can use a simple in-memory counter for unit testing but a database-managed sequence for production. Your factory method will most likely contain a conditional statement that looks at a local environment variable, determines if the system is in test mode, and returns the correct key generator. Once you have a few factories you have a mess on your hands. Establishing a new deployment configuration—say “execute unit tests against in-memory database without transaction control” or “execute in production mode against DB2 database with full transaction control”—requires editing conditional statements in a number of factories, rebuilding, and redeploying. Configuration shouldn’t be scattered throughout your application, nor should it require a rebuild or redeployment. Plugin solves both problems by providing centralized, runtime configuration.

The first thing to do is define with a Separated Interface any behaviors that will have different implementations based on runtime environment. Beyond that, we use the basic factory pattern, only with a few special requirements. The Plugin factory requires its linking instructions to be stated at a single, external point in order that configuration can be easily managed. Additionally, the linking to implementations must occur dynamically at runtime rather than during compilation, so that reconfiguration won’t require a rebuild.

Use Plugin whenever you have behaviors that require different implementations based on runtime environment.

### Service Stub

Removes dependence upon problematic services during testing.

![service stub](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01h.jpg)

Enterprise systems often depend on access to third-party services such as credit scoring, tax rate lookups, and pricing engines. Any developer who has built such a system can speak to the frustration of being dependent on resources completely out of his control. Feature delivery is unpredictable, and as these services are often remote reliability and performance can suffer as well.

At the very least these problems slow the development process. Developers sit around waiting for the service to come back on line or maybe put some hacks into the code to compensate for yet to be delivered features. Much worse, and quite likely, such dependencies will lead to times when tests can’t execute. When tests can’t run the development process is broken.

Replacing the service during testing with a Service Stub that runs locally, fast, and in memory improves your development experience.

The first step is to define access to the service with a Gateway. The Gateway should not be a class but rather a Separated Interface so you can have one implementation that calls the real service and at least one that’s only a Service Stub. The desired implementation of the Gateway should be loaded using a Plugin. The key to writing a Service Stub is that you keep it as simple as possible—complexity will defeat your purpose.

Use Service Stub whenever you find that dependence on a particular service is hindering your development and testing.

### Record Set

An in-memory representation of tabular data.

![record set](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/graphics/18fig01i.jpg)

In the last twenty years, the dominant way to represent data in a database has been the tabular relational form. Backed by database companies big and small, and a fairly standard query language, almost every new development I see uses relational data.

The idea of the Record Set is to give you your cake and let you eat it, by providing an in-memory structure that looks exactly like the result of an SQL query but can be generated and manipulated by other parts of the system.

A Record Set is usually something that you won’t build yourself, provided by the vendor of the software platform you’re working with. Examples include the data set of ADO.NET and the row set of JDBC 2.0.

To my mind the value of Record Set comes from having an environment that relies on it as a common way of manipulating data. A lot of UI tools use Record Set, and that’s a compelling reason to use them yourself. If you have such an environment, you should use Table Module to organize your domain logic: Get a Record Set from the database; pass it to a Table Module to calculate derived information; pass it to a UI for display and editing; and pass it back to a Table Module for validation. Then commit the updates to the database.
