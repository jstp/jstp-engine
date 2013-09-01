[Table of Contents](README.md) | [Previous: Introduction](introduction.md)

---

API
===

JSTPEngine
----------

For the API, the Engine is described as the `JSTPEngine` class, that JSTP libraries in object oriented languages should implement.

### JSTPEngine#dispatch

The `#dispatch` instance method of the Engine runs the [Processing](https://github.com/southlogics/jstp-rfc/blob/master/version/0.5/engine.md#processing) of the Dispatch. The function prototype of `JSTPEngine#dispatch` is as follows:

```
JSTPDispatch dispatch( 
  JSTPDispatch dispatch 
  [, void Function callback (
    JSTPEngine engine, 
    JSTPDispatch answer 
    [, JSTPDispatch triggering]
  ) 
  [, Object context ]]
)
```

#### Return value

The return value of the `#dispatch` method must be a JSTPDispatch instance representing the same Dispatch that got processed, including all Header values filled in by the Engine, such as the Timestamp and the Protocol Headers.

#### Method name

The name of the method in the Engine instance should be `dispatch`.

#### Required arguments

The first argument must be the partially or completely formed `JSTPDispatch` to be processed. Since a JSTP Dispatch can be represented as a data structure, implementation may accept data structures other than an actual `JSTPDispatch` instance.

#### Optional callback

The optional function callback must be compliant with the `JSTPCallback` prototype. Since methods are not first member classes in many programming languages, it is not required for callbacks to be instances of any particular object.

---
**TODO** continue edition here

4. The optional function callback must accept:
  - _Required_. A `JSTPEngine` as the first argument. This will be the Engine executing the callback. Useful for both answers and subsequent Dispatches.
  - _Required_. A `JSTPDispatch` as the second argument. This will be the ANSWER Dispatch if the callback is executed by an Answer Dispatch
  - _Required for BIND Subscription Dispatches only_ A `JSTPDispatch` as the third argument. This will be the Dispatch that triggered the callback execution. If this callback is missing in a BIND Subscription Dispatch, the Dispatch is aborted with no Answer (since there is no callback to send the Answer). The Engine might throw a `JSTPMissingCallbackException`.
  
  The function callback should be present in every BIND Subscription Dispatch, since otherwise nothing will be bound to the Dispatch Endpoint.
  
  Implementations should discard any return value from the callbacks.
  
  If the Dispatch is a RELEASE Subscription Dispatch, the function callback should be already bound in the Engine otherwise it will have no effect.
5. The optional context provides a binding for the callback when executed by the Engine. Depending on the language details and the applications architrecture the context will be more or less important.

### JSTPEngine#answer

The `#answer` instance method of the Engine allows the emitters to easily issue Answer Dispatches from an original Dispatch. The function prototype for the `JSTPEngine#answer` is as follows:

```
JSTPDispatch answer( JSTPDispatch source, Integer statusCode [, Object body [, void Function callback (JSTPEngine engine, JSTPDispatch answer) [, Object context]]])
```

Breaking it down:

1. The return value will be the generated Answer Dispatch.
2. The first argument must be the Source Dispatch whose Transaction ID will be used as the first item in the Resource Header of the Answer Dispatch.
3. The second argument must be the Integer [status code](syntax/status-code.md) right for the type of Answer.
4. The third argument is optional, and is a callback function to be bound for the Answer to the Answer. Since the Answer Dispatch is a first level citizen Dispatch, it features an unique own Transaction ID and the receiver of the Answer can itself answer to it. 
5. The fourth argument is optional and it is the context in which the callback will be processed.

JSTPDispatch
------------

The object model of the Dispatch is a class called `JSTPDispatch`. The JSTPDispatch class should provide getter and setter methods for each of the Headers. For now it should be just a dictionary-like structure native to the programming environment.

JSTPCallback
------------

The function callback should accept the arguments:

- **JSTPEngine engine**: The engine that called the callback with the triggered dispatch.
- **JSTPDispatch answer**: The answer, if present.
- **JSTPDispatch dispatch**: The dispatch, if not an answer.

---

[Table of Contents](README.md) | [Previous: Introduction](introduction.md)
