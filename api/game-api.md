# Game API
> [Back](../specification.md)

## GameStateEvent
> Gives the current game state to the users. Needs to be personalized since the hands of the players should not be accessable to other players. Gets sent on ever step of the game: Game start, Each Round ...

type: `"GAME/STATE"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| activePlayer | string | Username of the player whos turn it is. |
| playerHand | Card[] | (see Card) The current Hand of the player which receives this event |
| playersCardsCount | Map<String, Number> | Contains the number of cards each player has. This is represented in a map where the key is the username and the value is the amount of cards. |
| stacks | Stack[] | The card stacks in the game a player can deposit cards on |
| drawStackCardCount | number | Amount of cards left in the stack where players have to draw cards |

#### Card
| Property | Type | Description |
| ---      | ---  | ----        |
| value | number | Numerical value the card has |
| id | number | Identifier of the card. (Not sure if this is necessary ... only if there are multiple cards with the same value)|

#### Stack
| Property | Type | Description |
| ---      | ---  | ----        |
| id       | number | Identifier of the stack
| ascending | boolean | Determines if the cards in the stack need to be in the acending ot decending order
| currentValue | number | Numerical value of the current value of the stack |

#### Example
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "GAME/STATE",
    "data": {
        "activePlayer": "username",
        "playerHand": [
            { "id":1, "value": 1 },
            { "id":3, "value": 3 },
            { "id":3, "value": 3 },
            { "id":7, "value": 7 },
            { "id":4, "value": 4 },
            { "id":2, "value": 2 },
        ],
        "playerCardsCount" : {
            "username": 6,
            "username2": 6,
            "username3": 4
        },
        "stacks": [
            { "id": 1, "ascending": false, "currentValue":87 },
            { "id": 2, "ascending": false, "currentValue":51 },
            { "id": 3, "ascending": true, "currentValue":2 },
            { "id": 4, "ascending": true, "currentValue":5 },
        ],
        "drawStackCardCount": 94
    }
}

```


## PlayCardsEvent
>  Sent by a player to deposit cards on the stack

`Authentication needed`

type: `"GAME/PLAY"`

Also the game needs to check if the player sending the event has the current turn.

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| authToken | string | JWT (See authentication) |
| actions | Action[] | Actions the player whishes to perform |

#### Action
| Property | Type | Description |
| ---      | ---  | ----        |
| cardId   | number | Id the of card to play |
| stackId   | number | Id of the stack the card should land on |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "GAME/PLAY",
    "data": {
        "authToken" : "ey[...]",
        "actions": [
            { "cardId": 1, "stackId": 1 },
            { "cardId": 97, "stackId": 3 }
        ]
    }
}
```


## RequestStateEvent
>  Send by a client which, for whatever reason (reconnect etc ...), has lost the current game state. Triggers a resend of the GameStateEvent on the server

type: `"GAME/REQUEST_STATE"`


#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "GAME/REUQST_STATE",
}
```

## PlayerLostEvent
>  Sent if a play is not able to play any other cards and has lost the game.

type: `"GAME/PLAYER_LOST"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |
| loser | string | Username of the player who lost the game |


#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "GAME/PLAYER_LOST",
    "data": {
        "loser": "username"
    }
}
```

## WonEvent
>  Sent if a play is not able to play any other cards and has lost the game.

type: `"GAME/WON"`

#### EventData
| Property | Type | Description |
| ---      | ---  | ----        |


#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "GAME/PLAYER_WON",
    "data": {}
}
```


[Check the workflows](../usecases/wip.md)