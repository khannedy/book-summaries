# Enterprise Integration Patterns

![all patterns](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/686fig01.jpg)

## Integration Style

How can I integrate multiple applications so that they work together and can exchange information?

- File Sharing

  ![file sharing](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/044fig01.jpg)

  Have each application produce files that contain the information the other applications must consume. Integrators take the responsibility of transforming files into different formats. Produce the files at regular intervals according to the nature of the business.

- Database Sharing

  ![database sharing](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/048fig01.jpg)

  Integrate applications by having them store their data in a single Shared Database, and define the schema of the database to handle all the needs of the different applications.

- Remote Procedural Invocation

  ![remote procedural invocation](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/051fig01.jpg)

  Develop each application as a large-scale object or component with encapsulated data. Provide an interface to allow other applications to interact with the running application.

- Messaging

  ![messaging](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/054fig01.jpg)

  Use Messaging to transfer packets of data frequently, immediately, reliably, and asynchronously, using customizable formats.

## Messaging System

![messaing system](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/059fig01.jpg)

- Message Channel

  How does one application communicate with another using messaging?

  ![message channel](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/061fig01.jpg)

  Connect the applications using a Message Channel, where one application writes information to the channel and the other one reads that information from the channel.

- Message

  How can two applications connected by a Message Channel exchange a piece of information?

  ![message](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/067fig01.jpg)

  Package the information into a Message, a data record that the messaging system can transmit through a Message Channel.

- Pipes & Filter

  How can we perform complex processing on a message while maintaining independence and flexibility?

  ![pipes & filter](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/071fig01.jpg)

  Use the Pipes and Filters architectural style to divide a larger processing task into a sequence of smaller, independent processing steps (filters) that are connected by channels (pipes).

- Message Router

  How can you decouple individual processing steps so that messages can be passed to different filters depending on a set of conditions?

  ![message router](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/080fig01.jpg)

  Insert a special filter, a Message Router, which consumes a Message from one Message Channel and republishes it to a different Message Channel, depending on a set of conditions.

- Message Translator

  How can systems using different data formats communicate with each other using messaging?

  ![message translator](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/086fig01.jpg)

  Use a special filter, a Message Translator, between other filters or applications to translate one data format into another.

- Message Endpoint

  How does an application connect to a messaging channel to send and receive Messages?

  ![message endpoint](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/096fig01.jpg)

  Connect an application to a messaging channel using a Message Endpoint, a client of the messaging system that the application can then use to send or receive Messages.

## Message Channel

Messaging applications transmit data through a Message Channel, a virtual pipe that connects a sender to a receiver. A newly installed messaging system typically doesn’t contain any channels; you must determine how your applications need to communicate and then create the channels to facilitate it.

- Point to Point Channel

  How can the caller be sure that exactly one receiver will receive the document or perform the call?

  ![point to point channel](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/103fig02.jpg)

  Send the message on a Point-to-Point Channel, which ensures that only one receiver will receive a particular message.

- Publish Subscribe Channel

  How can the sender broadcast an event to all interested receivers?

  ![publish subscribe channel](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/107fig01.jpg)

  Send the event on a Publish-Subscribe Channel, which delivers a copy of a particular event to each receiver.

- Datatype Channel

  How can the application send a data item such that the receiver will know how to process it?

  ![datatype channel](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/112fig01.jpg)

  Use a separate Datatype Channel for each datatype so that all data on a particular channel is of the same type.

- Invalid Message Channel

  How can a messaging receiver gracefully handle receiving a message that makes no sense?

  ![invalid message channel](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/116fig02.jpg)

  The receiver should move the improper message to an Invalid Message Channel, a special channel for messages that could not be processed by their receivers.

- Dead Letter Channel

  What will the messaging system do with a message it cannot deliver?

  ![dead letter channel](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/120fig01.jpg)

  When a messaging system determines that it cannot or should not deliver a message, it may elect to move the message to a Dead Letter Channel.

- Guaranteed Delivery

  How can the sender make sure that a message will be delivered even if the messaging system fails?

  ![guaranteed delivery](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/123fig01.jpg)

  Use Guaranteed Delivery to make messages persistent so that they are not lost even if the messaging system crashes.

- Channel Adapter

  How can you connect an application to the messaging system so that it can send and receive messages?

  ![channel adapter](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/128fig01.jpg)

  Use a Channel Adapter that can access the application’s API or data to publish messages on a channel based on this data and that likewise can receive messages and invoke functionality inside the application.

- Messaging Bridge

  How can multiple messaging systems be connected so that messages available on one are also available on the others?

  ![messaging bridge](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/134fig01.jpg)

  Use a Messaging Bridge, a connection between messaging systems that replicates messages between systems.

- Message Bus

  What architecture enables separate applications to work together but in a decoupled fashion such that applications can be easily added or removed without affecting the others?

  ![message bus](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/138fig01.jpg)

  Structure the connecting middleware between these applications as a Message Bus that enables them to work together using messaging.

## Message Construction

A Message is an atomic packet of data that can be transmitted on a channel. Thus, to transmit data, an application must break the data into one or more packets, wrap each packet as a message, and then send the message on a channel. Likewise, a receiver application receives a message and must extract the data from the message to process it. The message system will try repeatedly to deliver the message (e.g., transmit it from the sender to the receiver) until it succeeds.

- Command Message

  How can messaging be used to invoke a procedure in another application?

  ![command message](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/145fig02.jpg)

  Use a Command Message to reliably invoke a procedure in another application.

- Document Message
  
  How can Messaging be used to transfer data between applications?

  ![document message](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/148fig01.jpg)

  Use a Document Message to reliably transfer a data structure between applications.

- Event Message

  How can messaging be used to transmit events from one application to another?

  ![event message](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/152fig01.jpg)

  Use an Event Message for reliable, asynchronous event notification between applications.

- Request-Reply

  When an application sends a message, how can it get a response from the receiver?

  ![request reply](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/155fig01.jpg)

  Send a pair of Request-Reply messages, each on its own channel.

- Return Address

  How does a replier know where to send the reply?

  ![return address](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/160fig01.jpg)

  The request message should contain a Return Address that indicates where to send the reply message.

- Correlation Identifier

  How does a requestor that has received a reply know which request this is the reply for?

  ![correlation identifier](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/164fig01.jpg)

  Each reply message should contain a Correlation Identifier, a unique identifier that indicates which request message this reply is for.

- Message Sequence

  How can messaging transmit an arbitrarily large amount of data?

  ![message sequence](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/171fig01.jpg)

  Whenever a large set of data needs to be broken into message-size chunks, send the data as a Message Sequence and mark each message with sequence identification fields.

- Message Expiration

  How can a sender indicate when a message should be considered stale and thus shouldn’t be processed?

  ![message expiration](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/177fig01.jpg)

  Set the Message Expiration to specify a time limit for how long the message is viable.

- Format Indicator

  How can a message’s data format be designed to allow for possible future changes?

  Design a data format that includes a Format Indicator so that the message specifies what format it is using.

## Message Routing

Pipes and Filters, In the simplest case, the messaging system delivers a message directly from the sender’s computer to the receiver’s computer. However, certain actions often need to be performed on the message after it is sent by its original sender but before it is received by its final receiver. For example, the message may have to be validated or transformed because the receiver expects a message format different from the sender’s. The Pipes and Filters architecture describes how multiple processing steps can be chained together using channels.

Routing, In a large enterprise with numerous applications and channels to connect them, a message may have to go through several channels to reach its final destination. The route a message must follow may be so complex that the original sender does not know what channel will get the message to the final receiver. Instead, the original sender sends the message to a Message Router, an application component that takes the place of a filter in the Pipes and Filters architecture. The router then determines how to navigate the channel topology and directs the message to the final receiver, or at least to the next router.

![message routing](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/229fig01.jpg)

- Content Based Router

  How do we handle a situation in which the implementation of a single logical function is spread across multiple physical systems?

  ![content based router](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/232fig01.jpg)

  Use a Content-Based Router to route each message to the correct recipient based on the message’s content.

- Message Filter

  How can a component avoid receiving uninteresting messages?

  ![message filter](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/238fig01.jpg)

  Use a special kind of Message Router, a Message Filter, to eliminate undesired messages from a channel based on a set of criteria.

- Dynamic Routing

  How can you avoid the dependency of the router on all possible destinations while maintaining its efficiency?

  ![dynamic routing](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/244fig01.jpg)

  Use a Dynamic Router, a router that can self-configure based on special configuration messages from participating destinations.

- Recipient List

  How do we route a message to a dynamic list of recipients?

  ![recipient list](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/250fig01.jpg)

  Define a channel for each recipient. Then use a Recipient List to inspect an incoming message, determine the list of desired recipients, and forward the message to all channels associated with the recipients in the list.

- Splitter

  How can we process a message if it contains multiple elements, each of which may have to be processed in a different way?

  ![splitter](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/260fig01.jpg)

  Use a Splitter to break out the composite message into a series of individual messages, each containing data related to one item.

- Aggregator

  How do we combine the results of individual but related messages so that they can be processed as a whole?

  ![aggregator](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/269fig01.jpg)

  Use a stateful filter, an Aggregator, to collect and store individual messages until it receives a complete set of related messages. Then, the Aggregator publishes a single message distilled from the individual messages.

- Resequencer

  How can we get a stream of related but out-of-sequence messages back into the correct order?

  ![resequencer](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/284fig02.jpg)

  Use a stateful filter, a Resequencer, to collect and reorder messages so that they can be published to the output channel in a specified order.

- Composed Message Processor

  How can you maintain the overall message flow when processing a message consisting of multiple elements, each of which may require different processing?

  ![composed message processor](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/296fig01.jpg)

  Use a Composed Message Processor to process a composite message. The Composed Message Processor splits the message up, routes the submessages to the appropriate destinations, and reaggregates the responses back into a single message.

- Scatter Gather

  How do you maintain the overall message flow when a message must be sent to multiple recipients, each of which may send a reply?

  ![scatter gather](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/298fig01.jpg)

  Use a Scatter-Gather that broadcasts a message to multiple recipients and reaggregates the responses back into a single message.

- Routing Slip

  How do we route a message consecutively through a series of processing steps when the sequence of steps is not known at design time and may vary for each message?

  ![routing slip](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/305fig01.jpg)

  Attach a Routing Slip to each message, specifying the sequence of processing steps. Wrap each component with a special message router that reads the Routing Slip and routes the message to the next component in the list.

- Process Manager

  How do we route a message through multiple processing steps when the required steps may not be known at design time and may not be sequential?

  ![process manager](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/313fig01.jpg)

  Use a central processing unit, a Process Manager, to maintain the state of the sequence and determine the next processing step based on intermediate results.

- Message Broker

  How can you decouple the destination of a message from the sender and maintain central control over the flow of messages?

  ![message broker](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/324fig01.jpg)

  Use a central Message Broker that can receive messages from multiple destinations, determine the correct destination, and route the message to the correct channel. Implement the internals of the Message Broker using other message routers.

## Message Transformation

Various applications may not agree on the format for the same conceptual data; the sender formats the message one way, but the receiver expects it to be formatted another way. To reconcile this, the message must go through an intermediate filter, a Message Translator (85), which converts the message from one format to another.

- Envelope Wrapper

  How can existing systems participate in a messaging exchange that places specific requirements, such as message header fields or encryption, on the message format?

  ![envelope wrapper](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/331fig01.jpg)

  Use an Envelope Wrapper to wrap application data inside an envelope that is compliant with the messaging infrastructure. Unwrap the message when it arrives at the destination.

- Content Enricher

  How do we communicate with another system if the message originator does not have all the required data items available?

  ![content enricher](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/339fig01.jpg)

  Use a specialized transformer, a Content Enricher, to access an external data source in order to augment a message with missing information.

- Content Filter

  How do you simplify dealing with a large message when you are interested only in a few data items?

  ![content filter](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/343fig01.jpg)

  Use a Content Filter to remove unimportant data items from a message, leaving only important items.

- Claim Check

  How can we reduce the data volume of a message sent across the system without sacrificing information content?

  ![claim check](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/347fig01.jpg)

  Store message data in a persistent store and pass a Claim Check to subsequent components. These components can use the Claim Check to retrieve the stored information.

- Normalizer

  How do you process messages that are semantically equivalent but arrive in a different format?

  ![normalizer](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/353fig01.jpg)

  Use a Normalizer to route each message type through a custom Message Translator so that the resulting messages match a common format.

- Canonical Data Motel

  How can you minimize dependencies when integrating applications that use different data formats?

  ![canonical data model](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/356fig01.jpg)

  Design a Canonical Data Model that is independent from any specific application. Require each application to produce and consume messages in this common format.

## Messaging Endpoints

Most applications do not have any built-in capability to interface with a messaging system. Rather, they must contain a layer of code that knows both how the application works and how the messaging system works, bridging the two so that they work together. This bridge code is a set of coordinated Message Endpoints that enable the application to send and receive messages.

- Messaging Gateway

  How do you encapsulate access to the messaging system from the rest of the application?

  ![messaging endpoints](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/469fig01.jpg)

  Use a Messaging Gateway, a class that wraps messaging-specific method calls and exposes domain-specific methods to the application.

- Messaging Mapper

  How do you move data between domain objects and the messaging infrastructure while keeping the two independent of each other?

  ![messaging mapper](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/480fig01.jpg)

  Create a separate Messaging Mapper that contains the mapping logic between the messaging infrastructure and the domain objects. Neither the objects nor the infrastructure have knowledge of the Messaging Mapper’s existence.

- Transactional Client

  How can a client control its transactions with the messaging system?

  ![transaction client](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/486fig01.jpg)

  Use a Transactional Client—make the client’s session with the messaging system transactional so that the client can specify transaction boundaries.

- Polling Consumer

  How can an application consume a message when the application is ready?

  ![polling consumer](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/494fig02.jpg)

  The application should use a Polling Consumer, one that explicitly makes a call when it wants to receive a message.

- Event-Driven Consumer

  How can an application automatically consume messages as they become available?

  ![event driven consumer](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/498fig02.jpg)

  The application should use an Event-Driven Consumer, one that is automatically handed messages as they’re delivered on the channel.

- Competing Consumers

  How can a messaging client process multiple messages concurrently?

  ![competing consumers](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/503fig01.jpg)

  Create multiple Competing Consumers on a single channel so that the consumers can process multiple messages concurrently.

- Message Dispatcher

  How can multiple consumers on a single channel coordinate their message processing?

  ![message dispatcher](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/509fig01.jpg)

  Create a Message Dispatcher on a channel that will consume messages from a channel and distribute them to performers.

- Selective Consumer

  How can a message consumer select which messages it wishes to receive?

  ![selective consumer](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/516fig01.jpg)

  Make the consumer a Selective Consumer, one that filters the messages delivered by its channel so that it receives only the ones that match its criteria.

- Durable Subscriber

  How can a subscriber avoid missing messages while it’s not listening for them?

  ![durable subscriber](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/523fig01.jpg)

  Use a Durable Subscriber to make the messaging system save messages published while the subscriber is disconnected.

- Idempotent Receiver

  How can a message receiver deal with duplicate messages?

  Design a receiver to be an Idempotent Receiver, one that can safely receive the same message multiple times.

- Service Activator

  How can an application design a service to be invoked both via various messaging technologies and via non-messaging techniques?

  ![service activator](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/533fig01.jpg)

  Design a Service Activator that connects the messages on the channel to the service being accessed.

## System Management

Besides the inherent complexities and scale of integrating distributed packaged and custom applications, the architectural benefits of loose coupling actually make testing and debugging a system harder. Martin Fowler refers to this as the “architect’s dream, developer’s nightmare”

- Control Bus

  How can we effectively administer a messaging system that is distributed across multiple platforms and a wide geographic area?

  ![control bus](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/541fig01.jpg)

  Use a Control Bus to manage an enterprise integration system. The Control Bus uses the same messaging mechanism used by the application data but uses separate channels to transmit data that is relevant to the management of components involved in the message flow.

- Detour

  How can you route a message through intermediate steps to perform validation, testing, or debugging functions?

  ![detour](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/545fig02.jpg)

  Construct a Detour with a Context-Based Router controlled via the Control Bus. In one state, the router routes incoming messages through additional steps, while in the other it routes messages directly to the destination channel.

- Wire Tap

  How do you inspect messages that travel on a Point-to-Point Channel?

  ![wire trap](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/548fig01.jpg)

  Insert a Wire Tap into the channel, a simple Recipient List that publishes each incoming message to the main channel as well as to a secondary channel.

- Message History

  How can we effectively analyze and debug the flow of messages in a loosely coupled system?

  ![message history](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/552fig01.jpg)

  Attach a Message History to the message. The Message History is a list of all applications or components that the message passed through since its origination.

- Message Store

  How can we report against message information without disturbing the loosely coupled and transient nature of a messaging system?

  ![message store](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/556fig01.jpg)

  Use a Message Store to capture information about each message in a central location.

- Smart Proxy

  How can you track messages on a service that publishes reply messages to the Return Address specified by the requestor?

  ![smart proxy](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/559fig01.jpg)

  Use a Smart Proxy to store the Return Address supplied by the original requestor and replace it with the address of the Smart Proxy. When the service sends the reply message, route it to the original Return Address.

- Test Message

  What happens if a component is actively processing messages but garbles outgoing messages due to an internal fault?

  ![test message](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/569fig02.jpg)

  Inject a Test Message into the message stream to confirm the health of message processing components.

- Channel Purger

  How can you keep leftover messages on a channel from disturbing tests or running systems?

  ![channel purger](https://learning.oreilly.com/library/view/enterprise-integration-patterns/0321200683/graphics/574fig01.jpg)

  Use a Channel Purger to remove unwanted messages from a channel.

