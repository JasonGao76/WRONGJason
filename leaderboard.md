---
Permalink: /leaderboard/
---

<style>
    h1 {text-align: center;}
</style>

<h1>
    Leaderboard!
</h1>
<table>
  <thead>
  <tr>
    <th>Name</th>
    <th>ID</th>
    <th>Age</th>
    <th>Nickname</th>
    <th>Role</th> 
    <th>Wins</th>
  </tr>
  </thead>
  <tbody id="result">
    <!-- javascript generated data -->
  </tbody>
</table>
<h3>Return to Menu</h3>
<form action="{{site.baseurl}}/main_menu/">
  <button type="submit">Menu</button>
  </form>

<a href="http://{{site.baseurl}}/WRONG/charactercreation"><button class="startbutton" type="button">Start Game</button></a> <!-- revert back to 127.0.0.1:8086 for local-->


<script type="module">
  // uri variable and options object are obtained from config.js
  import { uri, options } from '{{site.baseurl}}/assets/js/api/config.js';

  // Set Users endpoint (list of users)
  const url = uri + '/api/users/';

  // prepare HTML result container for new output
  const resultContainer = document.getElementById("result");

  // fetch the API
  fetch(url, options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors and display
      if (response.status !== 200) {
          const errorMsg = 'Database response error: ' + response.status;
          console.log(errorMsg);
          window.location.href = "{{site.baseurl}}/403_Error/";
          const tr = document.createElement("tr");
          const td = document.createElement("td");
          td.innerHTML = errorMsg;
          tr.appendChild(td);
          resultContainer.appendChild(tr);
          return;
      }
      // valid response will contain JSON data
      response.json().then(data => {
          data.sort((first, second) => {
          return second.wins - first.wins;
          });
          console.log(data);
          for (const row of data) {
            // tr and td build out for each row
            const tr = document.createElement("tr");
            const name = document.createElement("td");
            const id = document.createElement("td");
            const age = document.createElement("td");
            const Nick = document.createElement("td");
            const role = document.createElement("td");
            const wins = document.createElement("td");
            // data is specific to the API
            name.innerHTML = row.name; 
            id.innerHTML = row.uid; 
            age.innerHTML = row.age; 
            Nick.innerHTML = row.Nick;
            role.innerHTML = row.role;
            wins.innerHTML = row.wins;
            // this builds td's into tr
            tr.appendChild(name);
            tr.appendChild(id);
            tr.appendChild(age);
            tr.appendChild(Nick);
            tr.appendChild(role);
            tr.appendChild(wins)
            // append the row to table
            resultContainer.appendChild(tr);
          }
            // Store users in an array
            const users = [];

            for (const row of data) {
                // Create a user object
                const user = {
                    name: row.name,
                    id: row.uid,
                    age: row.age,
                    Nick: row.Nick,
                    role: row.role,
                    wins: row.wins,
            };
          }
      })
  })
  // catch fetch errors (ie ACCESS to server blocked)
  .catch(err => {
    console.error(err);
    const tr = document.createElement("tr");
    const td = document.createElement("td");
    td.innerHTML = err + ": " + url;
    tr.appendChild(td);
    resultContainer.appendChild(tr);
  });
</script>

