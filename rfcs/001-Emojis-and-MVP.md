# Malte's Viable Product
Version 0.1

As a starting point and to set a base for discussions, I've decided to specify *one* way of implementing [The Game](https://boardgamegeek.com/boardgame/173090/game).
This specification assumes that you have read the description linked above. (You may also check the [official rulebook](https://pandasaurusgames.com/products/the-game-kwanchai-moriya-edition) as we will use that for all rules)

## Introduction

The MVP uses websockets for server-initiated communication only, because I'm to stupid to think of collision-avoiding protocols. The communication between players is reduced to a subset of emojis which I deemed safe.

## Definitions

### Authentication / Client Identification
We'll need a way to identify clients, the easiest way I know is using a token. This token, named ClientID, is shared by the backend upon client registration (`/new`/`/join`). To differentiate between sessions, another token, named SessionID, will be used. Both tokens should be included in every request by the client if not stated otherwise, i.e:
```
GET /awesome-path HTTP/1.1
Host: the-game.de
User-Agent: not-curl/7.83.1
Accept: */*
x-session-id: 2849c771-5b6b-4008-b77d-04f4f2a37cee
x-client-id: 07d23254-b09c-4343-b0d0-ecdf4d5beb3b
x-mvp-version: 0.1
```
Later on a more sophisticated approach could be implemented.

### Versioning
To prevent cross-version communication every http request/response should contain the following header:
```
x-mvp-version: 0.1
```

### Constants

- `MOVE_TIMEOUT`: 60s
- `VALID_EMOJIS`: `["🥹","🥳","🥲","🤯","🦩","🤬","🤌","️❤️","️🔥","✌️","🍆","🍑","🐒","🐮","👀","👋","👍","👎","👿","💀","💢","😇","😓","😡","😢","😭","😰","😱","🤙","🤜","🤣","🤦","🫠"]`

### Types
- `SessionID`: [UUIDv4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random))

  Identifier for a single session of the game.
- `ClientID`: [UUIDv4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random))

  Identifier for a single client (player) in a single session.
- Decks: There are four decks numbered `0-3` where `0` and `1` will begin at 1 while `2` and `3` will begin at 100.
- Cards: There are 98 playable cards in the game numbered `2-99` each represented by it's number.

### Websocket communication

The client is responsable for opening a websocket as soon as it joined a session and received `SessionID` and `ClientID`. This socket will be used for all communication that is initiated by the server.

#### Messages

Every message must be a valid JSON object. Every message contains at least the `kind` key. The following messages can be send over the socket:

##### Chat
If a player sent a message via POST `/send`, the server will send this chat message. The client will send *no* confirmation. *Keys*: `message`. I.e.
```json
{ "kind": "chat",
  "message": "❤️" }
```

##### GetMove
The server will request a move from each player in turn. The client has time until `MOVE_TIMEOUT` is reached to reply with a `Move` message. I.e.
```json
{ "kind": "get-move" }
```

##### Move
If a client is prompted by the server with a `GetMove` message, the following message must be send before `MOVE_TIMEOUT` is reached. Every move in the list of moves must be valid. The moves should be ordered by increasing time, s.t. the first move done by the player is the first move in the list of moves. Every move must contain the keys `deck` and `card`. See the Types section for deck and card numbering. The server will respond with either a `MoveDone` or a `MoveFailed` message. Keys: *moves*. I.e.
```json
{ "kind": "move",
  "moves": [
    { "deck": 0,
      "card": 2 },
    { "deck": 0,
      "card": 10 },
    { "deck": 3,
      "card": 87 }
   ]
}
```

##### MoveDone
If a client's move was successful, valid, in time, etc, the server will respond with this message.
This will almost always be followed by the `State` message. I.e.
```json
{ "kind": "move-done" }
```

##### MoveFailed
If a client submitted an invalid move, `MOVE_TIMEOUT` was reached, etc, the server will respond with this message before removing the client from the game. The player's cards are put back into the pile of new cards. Note that the number of cards per player may change by this. The server will then send `State` with the updated hand to the remaining players. I.e.
```json
{ "kind": "move-failed" }
```

##### State
State update happened and the server is sending the current game state to all clients. `hand` contains all cards on this players hand. `decks` is a list of all decks where the index is the deck's number. *Keys*: `hand`, `decks`. I.e.
```json
{ "kind": "state",
  "hand": [ 12, 1, 65, 87, 43, 29 ],
  "decks": [ 1, 1, 100, 100 ] }
```

## Backend

### Endpoints

Responses should always contain sensible return codes. If `/join` is requested for an invalid `SessionID` a 4** or 5** code might be appropriate. The example requests/responses contain `(*)` for headers that *must* be present.

#### GET `/new`

Create a new session of the game. Neither a `SessionID` nor a `ClientID` are expected. Should return code `201 Created` on success.

Example Request:
```
GET /new HTTP/1.1
Host: localhost:12345
User-Agent: curl/7.83.1
Accept: */*
x-mvp-version: 0.1                                  (*)
```

Possible Response:
```
HTTP/1.1 201 Created
x-session-id: ea7006d3-0318-41ca-9531-6d5b41f2cadf  (*)
x-client-id: d4fb9b0f-d405-455c-a515-82f5965e246c   (*)
x-mvp-version: 0.1                                  (*)
content-length: 0
date: Thu, 28 Jul 2022 15:53:28 GMT
```

#### GET `/join`

Join a session by `SessionID`. `ClientID` is not necessary. Should return code `200 OK` on success.

Example Request:
```
GET /join HTTP/1.1
Host: localhost:3030
User-Agent: curl/7.83.1
Accept: */*
x-session-id: ea7006d3-0318-41ca-9531-6d5b41f2cadf  (*)
x-mvp-version: 0.1                                  (*)
```

Possible Response:
```
HTTP/1.1 200 OK
x-session-id: ea7006d3-0318-41ca-9531-6d5b41f2cadf
x-client-id: dd17b73a-bbe8-42de-943f-cdeb14765a60   (*)
x-mvp-version: 0.1                                  (*)
content-length: 0
date: Thu, 28 Jul 2022 15:59:31 GMT
```

#### POST `/send`

Send a message to all other players. Only single emojis from the `VALID_EMOJIS` should be sent to other players, but this may change in future specifications. Should return code `200 OK` on success.

Example Request:
```
POST /send HTTP/1.1
Host: localhost:3031
User-Agent: curl/7.83.1
Accept: */*
x-session-id: ea7006d3-0318-41ca-9531-6d5b41f2cadf  (*)
x-client-id: dd17b73a-bbe8-42de-943f-cdeb14765a60   (*)
x-mvp-version: 0.1                                  (*)
Content-Type: text/html; charset=UTF-8              (*)
Content-Length: 6

❤️
```

Example Response:
```
HTTP/1.1 200 OK
content-length: 0
x-mvp-version: 0.1                                  (*)
date: Thu, 28 Jul 2022 16:42:55 GMT
```

The backend is then responsable for relaying this message to all connected players (see Websockets and kind `message`).

#### GET `/state`

Retrieve the game state in case it was lost by the client. The `ClientID` is not required. The response will contain valid json as described for the Websocket `State` message. This could be used by 3rd person to watch the game?

Example Request:
```
GET /state HTTP/1.1
Host: localhost:3031
User-Agent: curl/7.83.1
Accept: */*
x-session-id: ea7006d3-0318-41ca-9531-6d5b41f2cadf  (*)
x-mvp-version: 0.1                                  (*)
```

Example Response:
```
HTTP/1.1 200 OK
content-type: application/json
x-mvp-version: 0.1
content-length: 64
date: Thu, 28 Jul 2022 19:24:05 GMT

{"kind":"State","hand":[12,1,65,87,43,29],"decks":[1,1,100,100]}
```

## Frontend

The frontend may implement any design of the game, but the recommendation is to keep it 'end-laser' to improve user engagement.
