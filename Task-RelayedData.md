# SaltyRTC Relayed Data Task

This task uses the end-to-end encrypted WebSocket connection set up by
the SaltyRTC protocol to send user defined messages.

# Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in
[RFC 2119](https://tools.ietf.org/html/rfc2119).

# Terminology

All terms from the [SaltyRTC protocol
specification](./Protocol.md#terminology) are valid in this document.

# Task Protocol Name

The following protocol name SHALL be used for task negotiation:

`v0.relayed-data.tasks.saltyrtc.org`

# Task Data

This task does not currently make use of the *data* field of the 'auth'
messages described in the [SaltyRTC protocol
specification](./Protocol.md#auth-message). The task data sent by the
peers should be `Nil`.

# Message Structure

The same message structure as defined in the [SaltyRTC protocol
specification](./Protocol.md#message-structure) SHALL be used.

# Client-to-Client Messages

After the task has taken over, any user defined message MAY be sent, as
long as it conforms to the format described on the section [User Defined
Messages](#user-defined-messages).

If one of the sides wants to terminate the connection, the ['close'
Message](#close-message) SHALL be sent as described in the [SaltyRTC
protocol specification](./Protocol.md#close-message).

The ['application' message](./Protocol.md#application-message) SHALL NOT
be used in the Relayed Data task.

## Message States (Beyond 'auth')

```
      +---+
      |   v
    +-+-----+    +-------+
--->+   *   +--->+ close |
    +-------+    +-------+
```

## User Defined Messages

The user MAY send any MessagePack encoded message, as long as it follows
the following format:

* The message MUST be a `Map`
* The message MUST contain a *type* key with a string value
* The type SHALL NOT be 'application' or 'close'

Example:

```
{
  "type": "chat-message",
  "from": "peter",
  "message": {
    "text": "Hello Peter, it's nice talking to you securely!",
    "timestamp": 1513604188
  }
}
```

# Processing Incoming Messages

Incoming task messages shall be processed as follows:

* The client SHALL validate and decrypt the message according to the
  [SaltyRTC protocol specification](./Protocol.md#receiving-a-signalling-message)
* If the message type is 'close', the message MUST be handled as
  described in the
  [SaltyRTC protocol specification](./Protocol.md#close-message)
* Otherwise, if the message type is 'application', the connection MUST
  be closed with a close code of `3001` (*Protocol Error*)
* Otherwise, the decrypted message SHALL be passed to the user
  application

# Sending Outgoing Messages

The task must provide a way for the user to send task messages. It MUST
validate the messages as described in the section on [Message
Structure](#user-defined-messages). Invalid messages MUST be rejected
and the user application SHALL be notified.

The same procedure as described in the [SaltyRTC protocol
specification](./Protocol.md#sending-a-signalling-message) SHALL be
followed to send task messages.
