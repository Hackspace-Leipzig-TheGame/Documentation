# Vote API
> [Back](../specification.md)


## VoteForEvent
>  Send by the server to trigger a vote for the user.

type: `"GAME/VOTE_FOR"`

| Property | Type | Description |
| ---      | ---  | ----        |
| voteId      | string | id of the vote |
| options | VoteOption[] | Options for which a user can vote |
| timeout | number | `optional` Duration in seconds how long the vote will be active (Leave empty for no timeout) |

#### VoteOption
| Property | Type | Description |
| ---      | ---  | ----        |
| id | string | UUID of the option  |
| name | string | Description of the option |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "VOTE/VOTE_FOR",
    "data": {
        "voteId": "c215d541-0237-4858-af39-dd39aa685c58",
        "timeout": 300,
        "options": [
            { "id": "c215d541-0237-4858-af39-dd39aa685c48", "name": "Option 1" },
            { "id": "c215d541-0237-4858-af39-dd39aa685c49", "name": "Option 2" },
            { "id": "c215d541-0237-4858-af39-dd39aa685c47", "name": "Option 3" },
        ]
    }
}
```

## VoteEvent
>  Send by the client to vote for an option.

type: `"VOTE/VOTE"`

| Property | Type | Description |
| ---      | ---  | ----        |
| voteId | string | UUID of the vote |
| option | string | UUID of the option the user has voted for |

#### Example:
``` json
{
    "id": "bff793ff-5cfe-46de-bc28-3a47f7a3ce2e",
    "type": "GAME/VOTE",
    "data": {
        "voteId": "c215d541-0237-4858-af39-dd39aa685c58",
        "option": "c215d541-0237-4858-af39-dd39aa685c48"
    }
}
```