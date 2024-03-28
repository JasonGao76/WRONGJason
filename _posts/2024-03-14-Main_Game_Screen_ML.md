---
layout: post
title: Game Screen
hide: true
description: Game screen page
permalink: /gamescreenML
---

<html lang="en">
<head>
<meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        .black-box {
            background-color: black;
            color: white;
            padding: 100px;
            border-radius: 10px;
            border: 2px solid white;
            text-align: left;
            width: 880px;
        }
        .white-input {
            background-color: white;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            width: 30%;
            box-sizing: border-box; /* Include padding and border in the width calculation */
        }
        .gray-box {
            background-color: #504E4E;
            color: white;
            padding: 100px;
            border-radius: 10px;
            border: 2px solid white;
            text-align: left;
            width: 880px;
        }
    </style>
</head>

<body>
    <img id="map" src="https://i.postimg.cc/x1YqnQJZ/MapPos1.jpg" alt="Game Screen" usemap="#gameMap">
    <div class="black-box">
      <h2 id="boxtext">You are currently at point <span id="currentposition"></span> on the map. <span id="enemyalert"></span>You can <span id="actions"></span> the points <span id="possibleactionpositions"></span>. Please input the number of the point you would like to act on in the respective action box.</h2>
      <input type="number" min="1" max="9" class="white-input" placeholder="Type here..." id="playerinputmove">
      <button onclick="movement()">Move</button><br>
      <input type="number" min="1" max="9" class="white-input" placeholder="Type here..." id="playerinputattack">
      <button onclick="attack()">Attack</button>
    </div>
    <div class="black-box">
      <h2 id="boxtext">Want to try your luck? Enter four numbers from 1 to 100 below and see if you gain or lose HP!</h2>
      <input type="number" min="1" max="100" class="white-input" placeholder="First number..." id="firstnumber"><br>
      <input type="number" min="1" max="100" class="white-input" placeholder="Second number..." id="secondnumber"><br>
      <input type="number" min="1" max="100" class="white-input" placeholder="Third number..." id="thirdnumber"><br>
      <input type="number" min="1" max="100" class="white-input" placeholder="Fourth number..." id="fourthnumber"><br>
      <button onclick="findprobability()">Try your luck!</button>
      <h2 id="boxtext"><span id="result"></span></h2>
    </div>
</body>

<script>
    // Define function to calculate damage
    async function calculateDamage() {
      var dataHealth = 0;
      // Fetch data to get HP
      // const url = "http://{{site.baseurl}}/api/currentchar/"; // revert back to 127.0.0.1:8086 for local
      const url = "http://127.0.0.1:8086/api/currentchar/";
      var options = {
          method: 'GET', // *GET, POST, PUT, DELETE, etc.
          mode: 'cors', // no-cors, *cors, same-origin
          cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
          credentials: 'include', // include, same-origin, omit
          headers: {
              'Content-Type': 'application/json',
          },
      };
      const response = await fetch(url, options);
      const data = await response.json();
      console.log(data);

      var dataObject = data[0];
      console.log(dataObject);

      dataHealth = dataObject["health"];
      console.log("EEE " + dataHealth);

      var newhealth = dataHealth - 1;
      console.log(newhealth)

      dataObject["health"] = newhealth;
      console.log(dataObject)

      // Send PUT request
      body = {
          // name: document.getElementById("name").value,
          classname: dataObject["classname"],
          health: newhealth,
          attack: dataObject["attack"],
          range: dataObject["range"],
          movement: dataObject["movement"]
      };
      var AuthOptions = {
          mode: 'cors', // no-cors, *cors, same-origin
          credentials: 'include', // include, same-origin, omit
          headers: {
              'Content-Type': 'application/json',
          },
          method: 'PUT', // Override the method property
          cache: 'no-cache', // Set the cache property
          body: JSON.stringify(body)
      };
      // fetch the API
      fetch(url, AuthOptions)
      // response is a RESTful "promise" on any successful fetch
      .then(response => {
          // check for response errors and display
          if (response.status !== 200) {
              // window.location.href = "{{site.baseurl}}/authorizationfail"; *update with link for error
              return;
          }
          // valid response will contain JSON data
          response.json().then(data => {
              console.log("Data updated")
          })
      })
      // catch fetch errors (ie ACCESS to server blocked)
      .catch(err => {
      console.log(err)
      });
      
      alert("You've been hit! Your health is now " + newhealth)
      // Check if health is 0
      if (newhealth == 0) {
        window.location.href = '{{site.baseurl}}/losescreen';
      }
    };
    
    // Define variable for initial position and update it in text
    var position = 1;
    document.getElementById("currentposition").textContent = position;

    // Define object for the map images (MapPos1 means at point 1)
    var mapImages = {
        1: "https://i.postimg.cc/x1YqnQJZ/MapPos1.jpg",
        2: "https://i.postimg.cc/GmQtpfvm/MapPos2.jpg",
        3: "https://i.postimg.cc/SK1K6zT0/MapPos3.jpg",
        4: "https://i.postimg.cc/SKxsYGG7/MapPos4.jpg",
        5: "https://i.postimg.cc/ZYdqNn6g/MapPos5.jpg",
        6: "https://i.postimg.cc/rsxF2Z8c/MapPos6.jpg",
        7: "https://i.postimg.cc/RFjV8xxX/MapPos7.jpg",
        8: "https://i.postimg.cc/637pY5g8/MapPos8.jpg",
        9: "https://i.postimg.cc/dtcVjFB0/MapPos9.jpg"
    };

    // Define object for possible actions (movement and attack) depending on initial position and update it in text
    var possibleActionPositions = {
        1: [2, 3],
        2: [1, 3, 4, 5],
        3: [1, 2, 5, 6],
        4: [2, 5, 7],
        5: [2, 3, 4, 6, 7, 8],
        6: [3, 5, 8],
        7: [4, 5, 8, 9],
        8: [5, 6, 7, 9],
        9: [7, 8]
    }
    document.getElementById("possibleactionpositions").textContent = possibleActionPositions[position];

    // Define function to check if a number is in one of the possible actions
    var enemyposition = 9;
    var enemyspot = possibleActionPositions[enemyposition]
    function checkPosition(number, arr) {
      for (var i = 0; i < arr.length; i++) {
        if (arr[i] == number) {
          document.getElementById("enemyalert").textContent = "The enemy is one step away from you! ";
          document.getElementById("actions").textContent = "move to or attack";
          return true;
        }
        else {
          document.getElementById("enemyalert").textContent = "";
          document.getElementById("actions").textContent = "move to";
        }
      }
    };
    checkPosition(enemyposition, possibleActionPositions[position]);

    // Function to pick a random number from 1 to max, and this is how to decide what the enemy is going to do and where
    function enemychoice(max) {
      var randomnumber = Math.random();
      var scalednumber = randomnumber * max;
      var endnumber = Math.floor(scalednumber) + 1;
      return endnumber;
    };

    // Function to pick a random number from an inputted array
    function enemychoice2(array) {
      const randomIndex = Math.floor(Math.random() * array.length);
      var moveposition = array[randomIndex];
      return moveposition;
    };

    // Define function for enemy movement
    function enemymove() {
      var moveposition = enemychoice2(enemyspot);
      while (moveposition == position) {
        moveposition = enemychoice2(enemyspot);
      };
      enemyposition = moveposition;
      enemyspot = possibleActionPositions[enemyposition]
      return;
    };
        
    // Define function for AI attack
    function enemyattack() {
      var attackposition = enemychoice2(enemyspot);
      if (attackposition == position) {
        calculateDamage();
        alert("You got hit!");
      }
    };

    // Define function for player movement
    function movement() {
      // Set inputValue to the number entered
      inputmovement = document.getElementById("playerinputmove");
      inputValue = inputmovement.value;
      possiblemoves = possibleActionPositions[position];
      for (var i = 0; i < possiblemoves.length; i++) {
        if (possiblemoves[i] == inputValue) {
          // Set number entered as new postion and update text for current position, the possible places to act on, and map
          position = inputValue
          document.getElementById("currentposition").textContent = position;
          document.getElementById("possibleactionpositions").textContent = possibleActionPositions[position];
          var map = document.getElementById("map")
          map.src = mapImages[position]
          // Enemy act
          var choice = enemychoice(2)
          if (choice == 1) {
            enemymove();
            console.log("Enemy has moved to " + enemyposition)
          }
          else if (choice == 2) {
            enemyattack();
            console.log("Enemy has attacked")
          }
          // Check positions and give alert if conditions met
          checkPosition(position, enemyspot)
          return;
        }
      }
      alert("Invalid number!")
    }

    // Define function for player attack
    function attack() {
      inputattack = document.getElementById("playerinputattack");
      inputattackValue = inputattack.value;
      possiblemoves = possibleActionPositions[position];
      for (var i = 0; i < possiblemoves.length; i++) {
        if (possiblemoves[i] == inputattackValue) {
          // Check if hit or not
          if (possiblemoves[i] == enemyposition) {
            alert("You Hit!")
            window.location.href='{{site.baseurl}}/winscreen'
            return;
          }
          else {
            alert("You Missed!")
          }
          // Enemy act
          var choice = enemychoice(2)
          if (choice == 1) {
            enemymove();
            console.log("Enemy has moved")
            return;
          }
          else if (choice == 2) {
            enemyattack();
            console.log("Enemy has attacked")
            return;
          }
        }
      }
      alert("Invalid number!")
    }

    // Define function to add 1 HP (used in ML incorporation)
    async function addHP() {
      var dataHealth = 0;
      // const url = "http://{{site.baseurl}}/api/currentchar/"; // revert back to 127.0.0.1:8086 for local
      const url = "http://127.0.0.1:8086/api/currentchar/";
      var options = {
        method: 'GET', // *GET, POST, PUT, DELETE, etc.
        mode: 'cors', // no-cors, *cors, same-origin
        cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
        credentials: 'include', // include, same-origin, omit
        headers: {
          'Content-Type': 'application/json',
        },
      };
      const response = await fetch(url, options);
      const data = await response.json();
      console.log(data);

      var dataObject = data[0];
      console.log(dataObject);

      dataHealth = dataObject["health"];
      console.log(dataHealth);

      var newhealth = dataHealth + 1;
      console.log(newhealth)

      dataObject["health"] = newhealth;
      console.log(dataObject)

      // Send PUT request
      body = {
        // name: document.getElementById("name").value,
        classname: dataObject["classname"],
        health: newhealth,
        attack: dataObject["attack"],
        range: dataObject["range"],
        movement: dataObject["movement"]
      };
      var AuthOptions = {
        mode: 'cors', // no-cors, *cors, same-origin
        credentials: 'include', // include, same-origin, omit
        headers: {
          'Content-Type': 'application/json',
        },
        method: 'PUT', // Override the method property
        cache: 'no-cache', // Set the cache property
        body: JSON.stringify(body)
      };
      // fetch the API
      fetch(url, AuthOptions)
      // response is a RESTful "promise" on any successful fetch
        .then(response => {
          // check for response errors and display
          if (response.status !== 200) {
            // window.location.href = "{{site.baseurl}}/authorizationfail"; *update with link for error
            return;
          }
          // valid response will contain JSON data
          response.json().then(data => {
          console.log("Data updated")
          })
        })
      // catch fetch errors (ie ACCESS to server blocked)
      .catch(err => {
      console.log(err)
      });
      
      alert("Your luck paid off! You gained 1 HP! Your new health is " + newhealth)
    };

    // Define function to subtract 1 HP (used in ML incorporation)
      async function subtractHP() {
      var dataHealth = 0;
      // const url = "http://{{site.baseurl}}/api/currentchar/"; // revert back to 127.0.0.1:8086 for local
      const url = "http://127.0.0.1:8086/api/currentchar/";
      var options = {
        method: 'GET', // *GET, POST, PUT, DELETE, etc.
        mode: 'cors', // no-cors, *cors, same-origin
        cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
        credentials: 'include', // include, same-origin, omit
        headers: {
          'Content-Type': 'application/json',
        },
      };
      const response = await fetch(url, options);
      const data = await response.json();
      console.log(data);

      var dataObject = data[0];
      console.log(dataObject);

      dataHealth = dataObject["health"];
      console.log(dataHealth);

      var newhealth = dataHealth - 1;
      console.log(newhealth)

      dataObject["health"] = newhealth;
      console.log(dataObject)

      // Send PUT request
      body = {
        // name: document.getElementById("name").value,
        classname: dataObject["classname"],
        health: newhealth,
        attack: dataObject["attack"],
        range: dataObject["range"],
        movement: dataObject["movement"]
      };
      var AuthOptions = {
        mode: 'cors', // no-cors, *cors, same-origin
        credentials: 'include', // include, same-origin, omit
        headers: {
          'Content-Type': 'application/json',
        },
        method: 'PUT', // Override the method property
        cache: 'no-cache', // Set the cache property
        body: JSON.stringify(body)
      };
      // fetch the API
      fetch(url, AuthOptions)
      // response is a RESTful "promise" on any successful fetch
        .then(response => {
          // check for response errors and display
          if (response.status !== 200) {
            // window.location.href = "{{site.baseurl}}/authorizationfail"; *update with link for error
            return;
          }
          // valid response will contain JSON data
          response.json().then(data => {
          console.log("Data updated")
          })
        })
      // catch fetch errors (ie ACCESS to server blocked)
      .catch(err => {
      console.log(err)
      });

      // relay result
      alert("Your luck unfortunately did not pay off. You lost 1 HP. Your new health is " + newhealth)

      // check if health is 0
      if (newhealth == 0) {
        window.location.href = '{{site.baseurl}}/losescreen';
      }
    };

    // Define function to find probability of survival using Titanic model and then send request to update health
    function findprobability() {
      // Get value of inputted numbers and store as firstnumber, secondnumber, thirdnumber, and fourthnumber
      firstnumberinput = document.getElementById("firstnumber");
      secondnumberinput = document.getElementById("secondnumber");
      thirdnumberinput = document.getElementById("thirdnumber");
      fourthnumberinput = document.getElementById("fourthnumber");
      firstnumber = firstnumberinput.value;
      secondnumber = secondnumberinput.value;
      thirdnumber = thirdnumberinput.value;
      fourthnumber = fourthnumberinput.value;

      // Randomize numbers
      originalfirst = firstnumber;
      originalsecond = secondnumber;
      originalthird = thirdnumber;
      originalfourth = fourthnumber;
      inputs = [firstnumber, secondnumber, thirdnumber, fourthnumber];
      for (let i = inputs.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [inputs[i], inputs[j]] = [inputs[j], inputs[i]];
      }
      [firstnumber, secondnumber, thirdnumber, fourthnumber] = inputs;
      
      // First number used to determine gender and alone
      if (firstnumber % 2 == 0) {
        sex = 'm';
        alone = 'y';
      }
      else {
        sex = 'f';
        alone = 'n';
      }

      // Second number used to determine fare and class
      fare = secondnumber * 12
      if (fare >= 500) {
        pclass = 1;
      }
      else if (fare >= 250 && fare < 500) {
        pclass = 2;
      }
      else {
        pclass = 3;
      }

      // Third number used to determine age and departure place
      age = thirdnumber / 2;
      if (age % 3 == 0) {
        embarked = 'S';
      } 
      else if (age % 3 == 1) {
        embarked = 'C';
      }
      else {
        embarked = 'Q';
      }

      // Fourth number used to determine siblings and parents
      while (fourthnumber > 5) {
        fourthnumber = Math.round(fourthnumber / 2)
      }
      siblings = fourthnumber
      while (fourthnumber > 2) {
        fourthnumber = Math.round(fourthnumber/2)
      }
      siblings = fourthnumber
      parents = fourthnumber

      // code to send POST request
      var url = "http://127.0.0.1:8086/api/ml/"
      var body = {
        socialclass: pclass,
        age: age,
        sex: sex,
        siblings: siblings,
        family: parents,
        fare: fare,
        port: embarked,
        alone: alone
      };
      var requestOptions = {
        method: "POST",
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(body),
        redirect: "follow"
      };
      fetch(url, requestOptions)
        .then(response => {
          if (response.status !== 200) {
            return;
          }
          response.json().then(data => {
            console.log("Data received")
            var survivability = data;
            var survivabilitypercent = Math.round(survivability * 100);
            document.getElementById("result").textContent = "You have a " + survivabilitypercent + "% chance to gain HP!";
            
            // code to process probability
            var decision = Math.floor(Math.random() * 100);
            if (decision <= survivabilitypercent) {
              addHP();
            }
            else {
              subtractHP();
            } 
          })
        })
      .catch(err => {
        console.log(err)
      }); 
    }
</script>
