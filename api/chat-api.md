# Chat API
> [Back](../specification.md)


## Sent Message Event
>  An event sent from the user to the server to send a message

`Authentication needed`

type: `"CHAT/SEND_MESSAGE"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| authToken | string | JWT (See authentication) |
| message | string | The message the user wants to sent |


#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "GAME/PLAY",
    "data": {
        "authToken" : "ey[...]",
        "message": "Hi there"
    }
}
```

## Message Event
>  An event sent from the user to the server to send a message

type: `"CHAT/MESSAGE"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| username | string | username of the user which sent the message - extracted from the JWT in `MESSAGE/SENT_MESSAGE` |
| message | string | The message the user wants to sent |


#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "CHAT/MESSAGE",
    "data": {
        "username" : "grievous",
        "message": "General Kenobi"
    }
}
```


[Check the workflows](../usecases/wip.md)