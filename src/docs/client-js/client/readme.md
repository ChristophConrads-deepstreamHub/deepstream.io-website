---
title: Javascript Client
description: The entry point API documentation for the deepstream.io js client
---

The deepstream JavaScript client can be used by both browsers and Node.js. You can get it via NPM or Bower as `deepstream.io-client-js` or browse the source on [Github](https://github.com/deepstreamIO/deepstream.io-client-js)

### deepstream(url, options)

{{#table mode="api"}}
-
  arg: url
  typ: String
  opt: false
  des: The server URL
-
  arg: options
  typ: Object
  opt: true
  des: A map of options. Please find a list of available options [here](/docs/client-js/options/)
{{/table}}

Creates a client instance and initialises the connection to the deepstream server. The connection will be kept in a quarantine state and won't be fully usable until `login()` is called.

```javascript
var deepstream = require('deepstream.io-client-js')
const client = deepstream('localhost:6020').login()
```

## Constants
* `deepstream.CONSTANTS` grants access to constants. See [constants](/docs/common/constants/) for a full list
* `deepstream.MERGE_STRATEGIES` grants access to the default merge strategies used globally as `mergeStrategy` client option or per record with `record.mergeStrategy()`

## Events

### connectionStateChanged
Emitted every time the connectionstate changes. The connectionState is passed to the callback and can also be retrieved using <a href="#getConnectionState()">getConnectionState()</a>. A list of possible connection states is available [here](/docs/common/constants/#connection-states)

### error
Aggregates all errors that are encountered. Some errors like `CONNECTION_ERROR` or `MESSAGE_PARSE_ERROR` are exlusively emitted by the client.
Others like `ACK_TIMEOUT` or `VERSION_EXISTS` that relate to a specific Record, Event or RPC are emitted first by the object they relate to and are then forwarded to the client. You can find a list of all errors [here](/docs/common/errors/).

```javascript
client.on('error', ( error, event, topic ) =>
  console.log(error, event, topic);
)
```

## Methods

### login(authParams, callback)
```
{{#table mode="api"}}
-
  arg: authParams
  typ: Object
  opt: false
  des: |
    An object with authentication parameters, e.g <br><code>{ username: &#39;peter&#39;, password: &#39;sesame&#39; }</code>
-
  arg: callback
  typ: Function
  opt: true
  des: A function that will be called once the response to the authentication request is received.
{{/table}}
```

Authenticates the client against the server. To learn more about how authentication works, please have a look at the [Security Overview](/tutorials/core/security-overview/).

Callback will be called with: success (Boolean), data (Object).

```javascript
const deepstream = require('deepstream.io-client-js')
const client = deepstream('localhost:6020')
// client.getConnectionState() will now return 'AWAITING_AUTHENTICATION'

client.login({username: 'peter', password: 'sesame'}, (success, data) => {
  if (success) {
    // start application
    // client.getConnectionState() will now return 'OPEN'
  } else {
    // extra data can be optionaly sent from deepstream for
    // both successful and unsuccesful logins
    alert(data)

    // client.getConnectionState() will now return
    // 'AWAITING_AUTHENTICATION' or 'CLOSED'
    // if the maximum number of authentication
    // attempts has been exceeded.
  }
})

// client.getConnectionState() will now return 'AUTHENTICATING'
```

### close()
Closes the connection to the server.

```javascript
client.on('connectionStateChanged', connectionState => {
  // will be called with 'CLOSED' once the connection is successfully closed.
})

client.close()
```

### getConnectionState()
Returns the current connectionState. Please find a list of available connectionStates [here](/docs/common/constants/#connection-state).

### getUid()
Returnes a unique id. The uid starts with a Base64 encoded timestamp to allow for semi-sequentual ordering and ends with a random string.

```javascript
client.getUid() // 'i9i6db5q-1xak1s2sfzk'
```
