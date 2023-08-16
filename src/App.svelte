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
    <div class="header">Random Civ</div>
    <div class="input-container">

      <p>Number of Players</p>
      <input type="number" placeholder=2 bind:value={numPlayers}>
      <p>Include Wonders</p>
      <input type="checkbox" bind:checked={includeWonders}>
      <button on:click={()=>{generate()}}>Generate</button>
    </div>
    <div class="task-list">
      {#each players as player}
        <!--each player has one civ but could have multiple wonders-->
        <div class="task-container">
        <div class="task-1">
          <p>Player: {player.id}</p>
        </div>
        <div class="task-2">
        <p>Civ: {player.civ.name}</p>
        <p>Score: {player.civ.score}</p>
        </div>
        <div class="task-3">
        {#if includeWonders && player.wonders}
          {#each player.wonders as wonder}
            <div>{wonder.name} ({wonder.score})</div>
          {/each}
        {/if}
        </div>
        </div>
      {/each}
      
    </div>
  </div>
</main>

<style>
  .container {
    display: grid;
    grid-template-columns: 1fr;
    grid-template-rows: auto 100px 1fr;
    grid-template-areas:
      "header"
      "input-container"
      "task-list";
    height: 100vh;
    width: 1200px;
  }
  
  .header {
    grid-area: header;
    font-size: 24px;
    text-align: center;
    padding: 20px;
    background-color: #313131;
  }
  
  .input-container {
    grid-area: input-container;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 10px;
    background-color: #eee;
  }
  
  input {
    padding: 10px;
    border: none;
    border-radius: 5px;
    margin-right: 10px;
  }
  
  button {
    padding: 10px;
    background-color: #333;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .task-list {
    grid-area: task-list;
    display: flex;
    flex-direction: column;
    padding: 10px;
    background-color: #fff;
    overflow-y: auto;
  }
  
  .task-container {
  display: grid;
  grid-template-rows: 1fr;
  grid-template-columns: 100px 200px 2fr;
  grid-template-areas: "task-1 task-2 task-3";
}

  .task-1 {
    padding: 10px;
    border-bottom: 1px solid #ccc;
    background-color: #b5b5b5;
    font-size: 20px;
  }

  .task-2 {
    padding: 10px;
    border-bottom: 1px solid #ccc;
    background-color: #b5b5b5;
    font-size: 20px;
  }

  .task-3 {
    padding: 10px;
    border-bottom: 1px solid #ccc;
    background-color: #b5b5b5;
    color: #000000;
    font-size: 15px;
  }

  p{
    color: #000000;
    padding: 10px;
    font-size: 20px;
    }
</style>