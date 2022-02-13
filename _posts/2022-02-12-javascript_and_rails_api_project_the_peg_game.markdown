---
layout: post
title:      "JavaScript & Rails API Project – The Peg Game"
date:       2022-02-12 20:19:06 -0500
permalink:  javascript_and_rails_api_project_the_peg_game
---


![Peg Game](http://i.imgur.com/ncOSL5Fb.jpg)

This blog describes my project ***Peg Game***. This application mimics the Peg Triangle Game where you jump a peg over another to remove all but one from the board. 

This is a Single-Page Application. To demonstrate the power of Rails API as a backend server with a JavaScript frontend, I created two separate [frontend](http://github.com/ssdiaz/peg_jump_frontend) and backend GitHub repos. 

## Game Play
When a user clicks the ‘Play’ button, the peg board renders to the DOM. To first start the game, the user must click one peg to remove. Then they can select a peg, and click on an open space to move a peg. The middle peg in that sequence that was “jumped” over is then "removed" (no pegs are actually removed; instead their ‘active’ status is set to *false* and they change to a grey color). If the user has no more pegs left to jump over with multiple pegs remaining on the board, they lose. 

However, if a user eliminates all pegs but one (or simulates a win by clicking the ‘CHEAT’ button), a “Submit New Player” form is rendered to the DOM. The user can save their name to the Winner’s Board, where a `POST fetch()` request is then sent to create the new Player and associated Win instance to the Rails API database.

After the game is over, the page is reloaded where the user will see the Winner’s Board displayed and updated based on the Rails API database.

## Backend Setup
The backend is an API-only Rails build. My models for game setup are: Game, Board and Tile. The Game model `has_one board`, and the Board model `has_many tiles`. I left Tiles unrelated to Boards because I did not find it necessary to track their relationship as I am not updating the tiles in the backend. Since this is a Single-Page Application, I only need one instance of Game and Board with the associated Tiles to play.

Additionally, I have Player and Win models that instantiate when a player wins a game and saves their name to the Winner’s Board. A Player can `have many wins` and a Win `belongs_to a player`. 

**Models:**
```
    Game
        has_one :board
        has_many :tiles, through: :board

    Board
        has_many :tiles
        belongs_to :game

    Tile 
    
    Player
        has_many :wins, :dependent => :destroy
        validates_presence_of :name
    
    Wins
        belongs_to :player
```


## What is an API?
I followed these [instructions](http://github.com/learn-co-curriculum/js-rails-as-api-creating-a-rails-api-from-scratch) to set up my Rails API.

API stands for *Application Programming Interface*, and basically allows your application’s system to communicate with another external system. 

One takeaway in using Rails as an API only and not using it for frontend is that the controllers inherit from `ActionController::API` instead of `ActionController::Base`, and render JSON instead of rendering views. 

**Example of Players Controller:**
```
class ApplicationController < ActionController::API
end

class PlayersController < ApplicationController
    def index
       players = Player.all
        render json: PlayerSerializer.new(players) 
    end
end 
```

I used the `gem 'fast_jsonapi'`  which allows you to create Serializers for your models, which helps to customize and render the JSON data outside of the controllers.

**Example of Game Serializer:**
```
class GameSerializer
  include FastJsonapi::ObjectSerializer
  attributes :board_id, :move_count, :win, :tiles
end
```

## What is JSON?
JSON stands for *JavaScript Object Notation*, and is used to pass data back and forth through `fetch()` function requests. Rails APIs can render JSON Strings, and JavaScript uses JSON Strings to translate the content data.

## What is AJAX and fetch()?
The JavaScript frontend communicates with the Rails API backend by using the AJAX technique to “fetch” data. This allows us to make requests to the backend server and update the DOM without needing to reload the page (a necessity for a Single-Page Application). 

The most efficient way to implement an AJAX request is to use a `fetch()` function. The `fetch()` function defaults to a GET request method, but can be used with POST, PUT, PATCH, and DELETE HTTP Verbs.

## Frontend
As mentioned, the frontend is built with JavaScript and uses AJAX's `fetch()` function to send & receive data to & from the backend server that can be rendered on the DOM without reloading the page. 

I used `GET fetch()` requests to create my Game, Board, and Tiles upon every reload of the page. These are only displayed to the user after they click the 'Play' button. 

A  `GET fetch()` request is also used to render the Winner's Board based on the Players and Wins saved in the backend.


## POST Fetch() Request 
####  New Player Example

To send the new player's name and win count to the database, I used a `POST fetch()` request. 

The fetch POST method requires:
* Destination URL – the players index @ http://localhost:3000/players
* Headers – I used  `content type` and `accept` as `application/json`; meaning I am sending content type of JSON and I want to accept JSON on the response back from the server
* Data stored in the body – when data is sent using `fetch()`, it sends as text from the browser to the server. So, we need to convert this data into a string by using the built in JavaScript method `JSON.stringify()`. This way, the server can take the string and convert it back into the key/value pairs in whatever language the server is using (in my case, Ruby)

```
function postFetchPlayer(userInput){ 
  fetch("http://localhost:3000/players", {
    method: "POST",
    headers: {
      'Content-Type': 'application/json',
      'Accept': 'application/json'
  },
    body: JSON.stringify({
      name: userInput 
    })    
  })
  .then(response => response.json())
  .then(player => {    
    createWin(player)
  })
}
```

Once our data is stringified and passed to the Rails API in a way it can decipher, the Rails API server sends back a `response` as a JSON object. In the code above, we use the first `.then()` method to convert that JSON object into a JavaScript object, that we can then access in a second `.then()` function.

Confused that `response.json()` does NOT return a JSON object? Here’s a note from the [MDM Web Doc](http://developer.mozilla.org/en-US/docs/Web/API/Response/json):

> "Note that despite the method being named json(), the result is not JSON but is instead the result of taking JSON as input and parsing it to produce a JavaScript object."

Next in my `postFetchPlayer` function, I call my function `createWin` and pass in that Javascript object as an argument, where I repeat the above process to `POST` to my Wins model. I use the data passed from the newly created `player` to create the new Win's associated Player data, since a Win belongs to a Player.

```
function createWin(player){
  fetch("http://localhost:3000/wins", {
    method: "POST",
    headers: {
      'Content-Type': 'application/json',
      'Accept': 'application/json'
  },
    body: JSON.stringify( {
      move_count: movesTotal,
      win_count: 1,
      player_id: player.data.id
    })    
  })
  .then(response => response.json())
  resetGame()
}
```

When the page reloads (which happens after a user clicks 'save'), I call a `GET fetch()` function to get the Player's data for the Winner's Board, and creates a new JavaScript Player Object that is later called to display the 15 most recent players when the Winner's Board is called and rendered. 

```
function fetchPlayer() {  
  fetch("http://localhost:3000/players")
  .then(response => response.json())
    .then(players => {
      players.data.forEach( data => { 
        newPlayer = new Player(data)
      })
    })
}
```

## Conclusion
This project was really fun to build – and play with after. It was helpful keeping my frontend and backend repos separate because I can change each independent of the other. One thing to note is that Objects in JavaScript are different than the Objects in Ruby. For the most part, my objects created in JavaScript were just “temporary” but necessary in order to grab specific items based on user clicks. They, in a way, only become “official” when posted to the database in the backend Rails API system. 

Want to win the game every time? ![Check this out.](http://www.wikihow.com/Win-the-Peg-Game)


