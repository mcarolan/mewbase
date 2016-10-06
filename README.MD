# mewbase

*Please note Mewbase is a very new project and very much a work in progress. Don't use it in production yet! *

## The problem

Mewbase is an event streaming and view engine.

Many applications now realise that its vital to keep track of all the streams of events produced by your application.

Exammples of streams of events might be:

* events from IoT sensors
* shopping basket events (add/remove/order) from a website
* stock movement events in a supply chain

There are many examples!

These events often need to be persisted fast, in high volume and potentially stored for very long periods of time. 

Storing logs of events provides many advantages including

* Complete audit trail of everything that happened
* Events can be replayed to for back-testing or debugging
* Analytics can be run on the events to extract business value (e.g. customers who deleted items from their basket
before placing an order are likely to still want the item- opportunity for a reminder)
* Machine learning models can be trained in the events to do all sorts of funky stuff
* Events can be used to co-ordinated busines processes between different services
* Functions can be used to implement distributed eventually consistent transactions

But many applications typically want to deal with *aggregated* event data, not the raw event data. Examples would include:

* a website wants to see the *current* state of a shopping basket, not deal with the raw add/remove item events
* a report needs to display average temperature from IoT sensors broken down by region
* a stock inventory needs to maintain current stock of products per warehouse not deal with individual stock movement
events
* a business process needs to listen to events and maintain state

This leads many applications either to either:

* Not capture raw events at all - only deal with the current state of their objects - thus losing valuable business
data
* maintaining their own "views" on the event log data perhaps by subscribing to messages and updating tables or documennts
in a separate database

The latter is usually implemented in an ad-hoc way for each application and requires several moving parts, and has to deal
with the synchronization between events and views. What's more it means the client has to deal with accessing two separate
pieces of technology - a messaging system and a database.

## Introducing mewbase

The idea behind mewbase is to provide an engine that functions as a very high performance streaming engine but also
 allows you to run persistent *functions* in the engine that listen to raw events and maintain multiple *views* of
 the event data.
 
The views can then be queried in similar way to how you would with a document or graph database.

So you can think of mewbase as combining the following:

* an event streaming engine
* a document/graph database
* functions which map events to documents

An interesting observation is that, unlike a normal document database, the documents are not updated directly by clients,
they are only updated in the engine by the functions.

The client simply sends events (*add item to basket*, and issues queries *give me current state of basket 1234*).
That means the client has no access to shared mutable state - a big win!  

Since the events, functions and documents are all maintained in the same engine, mewbase keeps track of synchronization
between the events and the views so you know they are always up to date.

That means the client has no access to shared mutable state - a big win! 

All this functionality will be accessible over a simple wire protocol and via a simple client API which we will
provide in Java, JavaScript and potentially other languages going ahead.

Mewbase engine will also be full embeddable in any Java (or Scala, or Groovy, or...) application so you can
colocate your "server" in your actual service.

Mewbase is a great match for implementing event driven microservices using an event sourcing / CQRS pattern.

Mewbase will be designed from scratch to cope with very large datasets and optimised for modern storage.

### Features

Mewbase will contain the following features

* Multiple event logs scaling to petabyte size
* Horizontal scalability via sharding/partitioning
* Replication
* Event persistence
* Transactional persistence of events
* Transaction updates of multiple documents in same binder 
* Event subscription
* Subscription filters
* User defined functions
* BSON messages
* Persistent documents
* Document queries
* Event streaming
* Document streaming
* Simple wire protocol
* Clients in Java, JS, Go, ...
* Pluggable authentication and authorisation
* TLS for connections with server + client certs
* Metrics
* Fully embeddable
* Standalone option
* Log archiving
* Client helpers for event sourcing (?)
* Completely non blocking, and reactive APIS
* Live persistent queries - sends more matching data to client as it arrives on server 
* Sidecars for node.js/go

### What does this look like from a client perspective

Let's take a simple example of an order microservice whose job it is to maintain shopping baskets for customers. It has
a REST API and is designed using CQRS/event sourcing principles. It implements the following commands:

* add item to basket
* remove item from basket
* place order

and provide the following queries:

* view specific shoping basket

As commands come in the service processes them and emits events by sending them to the mewbase engine. When the service
wants to get current state of a shopping basket it simply asks mewbase for it. The service provides a function that
maps the events to the basket which runs in the engine.

The engine can be emdedded directly in any Java service - this follows the microservice architectural style where 
services maintain their own data. Or alternatively different services can share a standalone engine or a cluster,
depending on which architectural style you prefer.

The mewbase client will provide caching at the client side and live results which update automatically so you don't
need to reissue the same queries every time you want to get the information.

Mewbase works really well with event driven microservices but is not limited to applications that use that
architectural style - it's applicable to any application that wants high performance event streaming and/or
document style views on the events.

### Why the name "mewbase"?

"mew" is the UK pronunciation of the Greek letter μ. This letter is often used in the scientific world to represent
"micro" i.e. 10^-6 or more loosely "something small". Mewbase is a great choice for event based microservices hence
the connection.

Also "mew" is the sound made by a kitten, and it would be great to have a logo of cute kitten for the project. (Any
volunteers?)

## Get involved!

There is a big scope in this project and a lot to do with some serious engineering challenges.

If you'd like to get involved and be a part of it, please say hi on our google group.