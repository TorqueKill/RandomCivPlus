<script>
  //import civs and wonders
  import { civs,wonders } from "./assets/civ"



  let players = [];

  let taskName = "";
  let numPlayers = 3;
  let includeWonders = false;
  let scoreBudget = 25;
  
  const maxPlayers = 8;
  const minPlayers = 2;

  function generate(){
    //check if number of players is valid
    if(numPlayers > maxPlayers || numPlayers < minPlayers){
      alert("Need between 2 and 8 players");
      return;
    }

    //create copies of civs and wonders
    //so that they can be modified
    let _civs = [...civs];
    let _wonders = [...wonders];

    //make player list with civ = "" and wonders = []
    //if include wonders is not checked, remove wonders from list
    players = [];
    for(let i = 0; i < numPlayers; i++){
      let player;
      if(includeWonders){
        //add first 3 as dummy wonders from wonders
        player = {id:i+1,civ: civs[0], wonders: [wonders[0],wonders[1],wonders[2],wonders[3],wonders[4]]}
      }else{
        player = {id:i+1,civ: civs[0]}
      }
      players.push(player);
    }

    console.log(players);

    //randomize civs and wonders
    //if include wonders is not checked, dont randomize wonders
    //make sure no civs are repeated, use copied version of civs
    //make sure no wonders are repeated, use copied version of wonders
    //keep a score for each player, add civ score and wonder score
    //after adding civ to player, remove from copied civs and subtract score from budget
    //keep adding wonders until score is within budget

    for (let i = 0; i < numPlayers; i++) {
      //randomize civs
      let playerScore = scoreBudget;
      let randomIndex = Math.floor(Math.random() * _civs.length);
      players[i].civ = _civs[randomIndex];
      _civs.splice(randomIndex, 1);
      playerScore -= players[i].civ.score;

      //randomize wonders
      if(includeWonders){
        players[i].wonders = [];
        
        //lower civ bias
        //if player has a civ of score 5 or less, give them at least a wonder of score 6 or more if possible
        if(players[i].civ.score <= 5){
          randomIndex = Math.floor(Math.random() * _wonders.length);
          while(_wonders[randomIndex].score < 6 && checkWondersByScore(6, _wonders)){
            randomIndex = Math.floor(Math.random() * _wonders.length);
          }

          console.log("bias wonder: " + _wonders[randomIndex].name);
          players[i].wonders.push(_wonders[randomIndex]);
          _wonders.splice(randomIndex, 1);
          playerScore -= players[i].wonders[players[i].wonders.length-1].score;
        }




        while(playerScore > 0){
          randomIndex = Math.floor(Math.random() * _wonders.length);
          //check if player can afford wonder
          if(playerScore - _wonders[randomIndex].score < 0){
            break;
          }
          players[i].wonders.push(_wonders[randomIndex]);
          _wonders.splice(randomIndex, 1);
          playerScore -= players[i].wonders[players[i].wonders.length-1].score;
        }
      }
    }

    //check if any wonder exists of given score or more
    function checkWondersByScore(score, wonderList){
      for(let i = 0; i < wonderList.length; i++){
        if(wonderList[i].score >= score){
          return true;
        }
      }
      return false;
    }


  }




</script>

<main>
  <div class="container">
    <header class="header">Random Civilization Generator</header>
    <section class="input-container">
      <label for="numPlayers">Number of Players:</label>
      <input type="number" id="numPlayers" value={numPlayers} min={minPlayers} max={maxPlayers} />

      <label for="includeWonders">Include Wonders:</label>
      <input type="checkbox" id="includeWonders" bind:checked={includeWonders}>
      <button class="generate-button" on:click={()=>{generate()}}>Generate</button>
    </section>
    <section class="task-list">
      {#each players as player}
        <div class="player-container">
          <div class="player-info">
            <h2>Player {player.id}</h2>
            <p>Civilization: <span>{player.civ.name}</span></p>
            <p>Score: <span>{player.civ.score}</span></p>
          </div>
          {#if includeWonders && player.wonders}
            <div class="wonders-list">
              {#each player.wonders as wonder}
                <div class="wonder-item">{wonder.name} (Score: {wonder.score})</div>
              {/each}
            </div>
          {/if}
        </div>
      {/each}
    </section>
  </div>
</main>


<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100vh;
  max-width: 1200px;
  margin: auto;
  padding: 20px;
}

.header {
  font-size: 28px;
  margin-bottom: 20px;
  color: #2c3e50;
}

.input-container {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  align-items: center;
  justify-content: center;
}

input[type="number"],
input[type="checkbox"] {
  padding: 10px;
  border: 1px solid #bdc3c7;
  border-radius: 5px;
}

.generate-button {
  padding: 10px 15px;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.generate-button:hover {
  background-color: #2980b9;
}

.task-list {
  width: 100%;
  overflow-y: auto;
}

.player-container {
  border: 1px solid #bdc3c7;
  border-radius: 10px;
  padding: 15px;
  margin-bottom: 10px;
}

.player-info h2 {
  margin-top: 0;
  color: #34495e;
}

.player-info p {
  margin: 5px 0;
  color: #7f8c8d;
}

.wonders-list {
  margin-top: 10px;
}

.wonder-item {
  background-color: #34495e;
  padding: 8px;
  border-radius: 5px;
  margin-top: 5px;
}

</style>