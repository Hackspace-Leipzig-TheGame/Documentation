# Party API
> [Back](../specification.md)

## CreatePartyEvent
>  Event used to trigger the creation of a new party

type: `"PARTY/CREATE_PARTY"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| owner    | string | Identifier of the user which created the party

#### Example:
``` json
{
    "id": "c215d541-0237-4858-af39-dd39aa685c48",
    "type": "PARTY/CREATE_PARTY",
    "data": {
        "owner": "username"
    }
}
```

## PartyCreatedEvent
>  Server response when a party is successfully created. `Only gets send to the user who created the party`

type: `"PARTY/PARTY_CREATED"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| responseTo| string | EventID of the event which triggered the party creation |
| partyId    | string | Identifier of the created party. The should be a 4 random character string only containing [A-Z][a-z][0-9]. This allows users to manually input the partyId to join other parties. |
| authToken | string | JWT (See authentication) Giving the player a way to authenticate its actions in the party. |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "PARTY/PARTY_CREATED",
    "data": {
        "responseTo": "c215d541-0237-4858-af39-dd39aa685c48",
        "partyId": "BeEf",
        "authToken" : "ey[...]"
    }
}
```


## JoinPartyEvent
> Register a user and join the party

type: `"PARTY/JOIN"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| username    | string | Username, must be unique since users are identified by this |
| partyId    | string | ID of the Party to join |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "PARTY/JOIN",
    "data": {
        "username": "username",
        "partyId": "BeEf"
    }
}
```


## RejoinPartyEvent 
> Used the rejoin a party after a lost connection using the autentication JWT

`Authentication needed`

type: `"PARTY/REJOIN"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| authToken    | string | JWT |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "PARTY/REJOIN",
    "data": {
        "authToken": "ey[...]"
    }
}
```

## PartyJoinedEvent
>  Server response when a party is successfully joined. `Only gets send to the user who requested to join/rejoin a party`

type: `"PARTY/PARTY_JOINED"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| responseTo| string | EventID of the PartyJoinEvent which triggered the party join |
| authToken | string | JWT (See authentication) Giving the player a way to authenticate its actions in the party. |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "PARTY/PARTY_JOINED",
    "data": {
        "responseTo": "c215d541-0237-4858-af39-dd39aa685c48",
        "partyId": "BeEf",
        "authToken" : "ey[...]"
    }
}
```

## LeavePartyEvent
>  Event used to leave a party

`Authentication needed`

type: `"PARTY/LEAVE"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| authToken    | string | Auth Token |

#### Example:
``` json
{
    "id": "c215d541-0237-4858-af39-dd39aa685c48",
    "type": "PARTY/LEAVE",
    "data": {
        "authToken": "ey[...]"
    }
}
```

## PartyPlayersListEvent
>  Send to each player to update the information of which players are in the lobby.

type: `"PARTY/PARTY_LIST"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| players | string[] | Username of the player which joined the party |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "PARTY/PARTY_LIST",
    "data": {
        "players": [
            "user1",
            "user2",
        ]
    }
}
```

## StartGameEvent
> Send by the owner of the party to start the game

`Authentication needed`

type: `"PARTY/START"`


| Property | Type | Description |
| ---      | ---  | ----        |
| authToken | string | JWT (See authentication) Giving the player a way to authenticate its actions in the party. |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "PARTY/START",
    "data": {
        "authToken": "ey[...]"
    }
}
```


[Check the workflows](../usecases/wip.md)
