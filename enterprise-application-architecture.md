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

## OFFLINE CONCURRENCY PATTERNS 

## SESSION STATE PATTERNS 

## BASE PATTERNS 
