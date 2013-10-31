[Table of Contents](README.md) | [Next: API](api.md)

---

Introduction
============

> The JSTP Engine is a running instance of an actual implementation of JSTP. Applications can run several Engines listening in an arbitrary amount of ports in several Transport Protocols. This flexibility is one of the features of JSTP.

_obsolete_

> An Engine must be able to perform certain basic tasks of a core functionality and offer at least a minimal API for application resources to consume. The [API is described extensively in the previous section](api.md). The basic functionality that is required for Engines is described in this section, which should be taken as a reference by developers of JSTP implementations.

_irrelevant_

Processing
----------

> **Dispatch Processing** is the intepretation by the Engine of the directives contained in a certain JSTP Dispatch. 

**Keep**, _useful definition_

> During the Processing the Engines (in this order): 
>
> 1. Determines the syntactic correctness of the Dispatch that the Emitter provided.
> 2. Determines whether or not the Dispatch is to be Forwarded, both over a network (and consequently in some of Transport Protocol) or to a Virtual Host.
> 3. Identifies if the Morphology of the Dispatch is of Subscription type. That being the case, either binds or releases the provided Subscription Callback in the Subscription Context.
> 4. If the Emitter provided an Answer Callback and emits immediatly a Subscription Dispatch to itself with the same Host Header as the Dispatch in process but aimed at the Answer Dispatch for the Transaction ID of this Dispatch.
> 5. If there was nothing in the Host Header, triggers the Dispatch. 

_incomplete and obsolete_

#### API

### 1. Validation

> The first step is to validate the Dispatch sent by the Emitter. 

_ok_

> In strongly typed programming languages Dispatches may be already checked for compliance in the construction of the `JSTPDispatch` object; in more looosely type ones, the Engine may look for missing or malformed Headers.

**Improve**, _somewhat inaccurate_

> The [Syntax](syntax/index.md) section of this reference specifies data types and restrictions for each Header.

**As reference**, _now it is an entirely different recommendation, but validations must be linked to the protocol since the Status Codes for malformations are provided there_

> If the validation fails the Engine must generate immediately an Answer Dispatch with the 400: Bad Dispatch Status Code. If the Dispatch comes from a Local Emitter  for the corresponding Transaction ID. The Engine must alg

_obsolete. it depends_

Subscription
------------

> Subscription refers to the attaching of a callback to an Endpoint pattern on a local or remote JSTP Engine. The subscription is issued via a BIND Dispatch with an Endpoint Header describing the pattern of Dispatches which, upon Processing, will trigger the subscribed callback.

**Improve**. _good definition, but obsolete functionality_

> All of callback in JSTP are added via subscription: so, for example, if a local resource wants to hook a callback on an Endpoint, it should be issue a BIND Dispatch with an empty, null, absent or a single loopback element in the Host Header. This is called Reflected Binding or Local Binding. 

_completely obsolete_

> Engine implementations may provide a simple API such a Domain Specific Language in order to simplify Local Binding.

_nice one. 'f course_

### Missing callback

> If a BIND Subscription Dispatch is emitted without callback, the Engine must abort the execution and might throw a `JSTPMissingCallbackException` but should not generate an Answer since there it no callback to send it.

**Discuss**

Answer
------

> Important update: Subscription Dispatches get ANSWERs directly, and are not re bound with a Subscription to the corresponding ANSWER Endpoint.

**Truly important**. _true story_

> When an Engine process a Regular Dispatch to which the Emitter provided a Callback, the Engine must generate and process a Subscription Dispatch to bind the Callback (or Remote Engine if it is Forwarded Dispatch) to an Endpoint for the ANSWER method and a Resource with the Transaction ID as the first item and a Wildcard as the second item.

**Keep**, _and improve_

>The Engine must keep track of each triggered Subscription, and to do that, it must:
>
> - Generate an ID (UUID, incremental ID) that is unique to each Subscription Triggering (called the "Triggering ID")
> - Assign the "Triggering ID" as the second item in the Token Header.
> 
> The callbacks should generate an Answer using both the Transaction ID and the Triggering ID. If a callback fails to provide a valid Transaction/Triggering ID pair in the Answer, the Engine must generate a 407 Not Acceptable Answer Dispatch and, if the Answer provided a callback, execute the callback with the Not Acceptable Dispatch.

**Discuss**. _looks fine but..._

If the callback generates more than one Answer with the Transaction/Triggering ID pair, the Engine must generate a 406 Not Acceptable Answer Dispatch for each subsequent Answer after the first one and if the Answer provided a callback, execute the callback with the Not Acceptable Dispatch.

If the callback emits a valid Answer once the Timeout if finished, the Engine must generate a 406 Not Acceptable Answer Dispatch and, if the Answer provided a callback, execute the callback with the Not Acceptable Dispatch.

> When issuing a 406 Not Acceptable of 400 Bad Dispatch, it is recommended for Engines to include in the body a `message` field with the Status Code label and a `source` field with the Source Dispatch so to simplify debugging.

> If no callback is provided, Engines may choose to throw a `JSTP406NotAcceptableException`.

### Timeout

Once all Subscriptions had been triggered, the Engine must start a timeout countdown (default may be 10 seconds) that, once finished, must look for Triggering IDs that have not been Answered and:

1. Generate and process and 504 Timeout Answer Dispatch for the unanswered Subscriptions
2. RELEASE the Answer Subscription
3. If the Source Dispatch was a Forwarded Dispatch, forward the RELEASE to the emitting Engine.


### Subscription Dispatches

An answer emitted for a Subscription Dispatch is forwarded to the Subscription Emitter even when the Answer Endpoint is not bound for the Callback. This implies that Subscriptions in the Subscription Table of the Engine must have the Transaction ID so that the Subscription can be identified.

Networking
----------

(connection timeout??)
_todo_

### Transport protocols

_todo_

#### TCP Sockets

_todo_

#### Websockets

_todo_

### Environment

_todo_

Configuration
-------------

_todo_

### Version management

_todo_

### Tolerance and Quirks mode

(tolerate null on optional headers)
_todo_

API Extras
----------

1. Method shorthands

---

[Table of Contents](README.md) | [Next: API](api.md)
