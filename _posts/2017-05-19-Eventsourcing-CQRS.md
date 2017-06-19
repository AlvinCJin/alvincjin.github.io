---
layout: post
title: Event Sourcing and CQRS
author: Alvin Jin
---

The real-world is all about collaboration, intentions and eventually dealing with issues by compensating use-cases for exception handling.

## What's Event Sourcing? 
-----
Event:

Atomic,
Immutable,
Happened in the past,
Reflect use-cases,
Single source of truth

Event-driven:

Eventually consistent,
Split-up use-cases & transactions,
Communication via events,
Events published reliably


Enables building a forward-compatible application architecture - the ability to add more application in the future that need to process the same event but create a different materialized view.
The essence of event sourcing: rather than performing destructive state mutation on a database when writing to it, we should record every write as a "command", as an immutable event.

All changes to application state are stored as a sequence of events. This sequence of events can be used to query the current state of an object but also the history of an object. Event sourcing allows one to create different kinds of views, even views which one did not even think of when creating the application. Event store only directly supports PK-based lookup => use CQRS to handle.


## Command Query Responsibility Segregation(CQRS)
-----
An application architecture pattern most commonly used with event sourcing. CQRS involves splitting an application into two parts internally - the command side ordering the system to update state and the query side that gets information without changing state. The command or write side is all about the business; it doesn't care about the queries. On the other hand, the query or read side is all about the read access; its main purpose is making queries fast and efficient.

The way event sourcing works with CQRS is to have part of the application models updates as writes to an event log or Kafka topic. This is paired with an event handler that subscribes to the Kafka topic, transforms the event and writes the materialized view to a read store. Finally, the read part of the application issues queries against the read store.

CQRS decouples the load from writes and reads allowing each to be scaled and optimized independently.The input events are quite simple. They are immutable facts, we can simply store them all. The database we read from is just a cached view of the event log.
The beautiful thing about this separation between source of truth and caches is that in your caches, you can denormalize data to your content.

To perform an action in the domain, you have to send a command to it. The command will be validated and processed. This will lead to one or more events that are stored in the journal. This is the Command part of CQRS.
To create a view of the domain model (which is eventsourced) you have to collect the events and process them into a view. This is Query part of CQRS.

Advantages:

If you write data to the database in the same schema as you use for reading, you have tight coupling between the part of the application doing the writing and the part doing the reading. Otherwise, you can have fast writes and fast reads. If data is immutable, you can always replay events in the same order when something is wrong.

## Why Scala and Akka?

The Akka actor model works on the basis of processing a command and outputting one or many events that represent the result of processing that command.

Scala is a great language for implementing ES-based domain models:
Case classes
Pattern matching
Recreating state = functional fold over events




