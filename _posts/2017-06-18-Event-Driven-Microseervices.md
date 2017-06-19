---
layout: post
title: Event Driven Microservices
author: Alvin Jin
---

The real-world is all about collaboration, intentions and eventually dealing with issues by compensating use-cases for exception handling.

## What's Microservice in Event Driven?
-----
* Commands are an action, which expect a response.
* Events are both a fact and a trigger. Something has happened.
* Queries are a request to look up something. Queries are side-effect free, which leave the state unchanged.
* Receiver Driven Flow: logic is pushed to the receiver of the events, rather than the sender, which reduces the coupling services.

Sometimes, we want the flexibility of remote queries rather than the overhead of maintaining a dataset locally. The trick is to limit the scope of these query interface,
ideally to within a bounded context. In this patten, event flows are the sole communication pattern between contexts. But services within a context leverage both event-driven processing
and request-driven views.

Events trigger processing, and turned into views to be queried locally. We can use remote, synchronous queries where necessary, particularly in smaller ecosystems.
Assign responsibility for propagating events to a single writer service.

## Advantages of Event-driven services:
-----
* Decoupling & Asynchronous Flows
* State Transfer: distribute datasets for reconstituting and querying inside a bounded context.
* Traceability: There is a central, immutable journal records every interactions.





