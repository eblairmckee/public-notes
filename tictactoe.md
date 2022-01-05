# tic tac toe architecture slot

- tic tac toe game
- 2 players at one device
- can draw it out or verbally explain
- what would this look like structurally, from a component architecture standpoint?

## follow up questions

- framework
- components
  - reusability
- state management
  - who's turn is it?
- event management for game play
  - how do we know what square is clicked
- how do we calculate a winner? and when?
- how do you show who's turn it is?
  - how is state updated on each turn?

### swap out player 2 with an AI

- AI is accessible via an API
  - what are we giving the backend?
  - is the BE stateful? or should we move the state to the BE entirely?
  - how are we communicating with the BE?
    - error handling
    - pending state
    - what library? tradeoffs?

### saving incomplete/in progress games

- can only one game exist at a time? have multiple in progress games at a time? auth? multi user support?
- game id in URL
- cookies
- authentication
  - session id's vs tokens
- or how could we do it without authentication?
  - localStorage
  - unique code
- how would this affect routing?
  - what about state management?

### add a global leaderboard with a win/loss ratio

- +1 for a win, -1 for a loss
- how do you identify users?
  - database for users (logged in)
- how do you update the leaderboard?
  - both POST and fetch

#### real time

- web sockets to keep it in sync
- or long logging with repeated requests at regular intervals

### playing other people (a person at another device)

- lobby
- invite link
- match making
  - how does that work under the hood
    - queue and lobby
    - similar win/loss ratio (separate queues) and time spent in the queue
- what does it look like e2e if player 1 makes a move?

### theming

- is it dynamic? or predefined themes?
  - what kind of variables
  - injection?
- avatars
