# Error Events
> [Back](./specification.md)

## PartyNotFoundError
>  Sent if the party if does not exist. Only gets sent to the socket request to join a party

type: `"ERROR/PARTY_NOT_FOUND"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| responseTo| string | EventID of the triggering Event |
| partyId | string | Id of the non existent party |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "ERROR/PARTY_NOT_FOUND",
    "data": {
        "responseTo": "c215d541-0237-4858-af39-dd39aa685c48",
        "partyId": "BeEf",
    }
}
```

## InvalidPlayError
>  Sent if the play of a user is invalid

type: `"ERROR/INVALID_PLAY"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| responseTo| string | EventID of the triggering Event |
| reason | string | Textform Error message. Might be usefull to specifiy the error cases in more detail ... |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "ERROR/INVALID_PLAY",
    "data": {
        "responseTo": "c215d541-0237-4858-af39-dd39aa685c48",
        "reason": "BeEf",
    }
}
```

## AuthenticationError
>  Sent if the authentication of a user is invalid

type: `"ERROR/AUTEHNTICATION"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| responseTo| string | EventID of the triggering Event |
| reason | string | Textform Error message. Might be usefull to specifiy the error cases in more detail ... |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "ERROR/AUTEHNTICATION",
    "data": {
        "responseTo": "c215d541-0237-4858-af39-dd39aa685c48",
        "reason": "BeEf",
    }
}
```