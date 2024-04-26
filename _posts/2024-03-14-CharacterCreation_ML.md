---
title: Character Creation page
permalink: /charactercreationML
---
<head>
    <style>
        /* styling was done with help of ChatGPT, but values and various styles to include was altered */
        .candle {
            max-width: 100%;
        }
        .charactercreation {
            display: flex;
            align-items: center;
        }
        .bigtitle {
            flex: 1;
            margin-right: 20px;
        }
        .mediumtitle {
            flex: 1;
            margin-right: 20px;
        }
        .smalltitle {
            flex: 1;
            margin-right: 20px;
        }
        #table {
            display: none;
        }
    </style>
</head>
<body class="charactercreation">
    <h1 class="bigtitle">Create Your Character</h1>
    <h2 class="middletitle">Make your character here!</h2>
    <!-- name and class drop down selection -->
    <h2 class="smalltitle">Name:</h2>
    <input type="text" name="name" id="name" required><br>
    <h2 class="smalltitle">Class:</h2>
    <select id="class" name="class" onchange="showinfo()">
        <option value="">Pick a class</option>
        <option value="Knight">Knight</option>
        <option value="Mage">Mage</option>
        <option value="Rogue">Rogue</option>
        <option value="Shield Bearer">Shield Bearer</option>
        <option value="Grand Wizard">Grand Wizard</option>
    </select>
    <!-- display data and image -->
    <div id="classInfo">
        <h3 id="class-description" class="class-details"></h3>
        <div id="table" class="class-details">
            <table>
                <thead>
                <tr>
                    <th>Class</th>
                    <th>Health</th>
                    <th>Attack</th>
                    <th>Range</th>
                    <th>Movement</th>
                </tr>
                </thead>
                <tbody id="result">
                <!-- generated data goes here -->
                </tbody>
            </table>
        </div>
    </div>
    <!-- submit button -->
    <br>
    <button class="buttons" onclick="submitinfo()">Submit</button>
    <img class="candle" src="https://i.postimg.cc/wj2FYHpM/candle-removebg-preview.png">
    <script>
        // frontend (HTML, Javascript) made on a GitHub repository using a teacher template according to their instructions: https://github.com/nighthawkcoders/Nighthawk-Pages
        const class_descriptions = {
            Knight: "You've selected the Knight class! Strong, loyal, and determined, this class features a balance between offense and defense.",
            Mage: "You've selected the Mage class! Intelligent and calm, this class features the ability to attack any space on the map at the cost of a lower health.",
            Rogue: "You've selected the Rogue class! Cunning and quick, this class features the ability to move multiple spaces at the cost of a lower health.",
            "Shield Bearer": "You've selected the Shield Bearer class! Sturdy and unwavering, this class features extra health.",
            "Grand Wizard": "You've selected the Grand Wizard class! This class is omnipotent, essentially unable to be beaten. Use this class for testing."
        }
        // show info and fetch data to show it too
        function showinfo() {
            var selectedclass = document.getElementById("class").value;
            var table = document.getElementById("table");
            if (table) {
                table.style.display = "block";
            }
            // fetch stuff to get class data
            // const url = "http://{{site.baseurl}}/api/classes/"; // revert back to 127.0.0.1:8086 for local
            const url = "http://127.0.0.1:8086/api/classes/";
            const options = {
                method: 'GET', // *GET, POST, PUT, DELETE, etc.
                mode: 'cors', // no-cors, *cors, same-origin
                cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
                credentials: 'include', // include, same-origin, omit
                headers: {
                    'Content-Type': 'application/json',
                },
            };
            const resultContainer = document.getElementById("result");
            fetch(url, options)
                .then(response => {
                // check for response errors
                if (response.status !== 200) {
                    const errorMsg = 'Database response error: ' + response.status;
                    console.log(errorMsg);
                    const tr = document.createElement("tr");
                    const td = document.createElement("td");
                    td.innerHTML = errorMsg;
                    tr.appendChild(td);
                    resultContainer.appendChild(tr);
                    return;
                }
                // valid response will contain JSON data, display response in table
                response.json().then(data => {
                    const just_current_class = data.filter(obj => obj.classname === selectedclass);
                    for (const row of just_current_class) {
                        // tr and td build out for each row
                        const tr = document.createElement("tr");
                        const classname = document.createElement("td");
                        const health = document.createElement("td");
                        const attack = document.createElement("td");
                        const range = document.createElement("td");
                        const movement = document.createElement("td");
                        // data is specific to the API
                        classname.innerHTML = row.classname; 
                        health.innerHTML = row.health; 
                        attack.innerHTML = row.attack;
                        range.innerHTML = row.range;
                        movement.innerHTML = row.movement;
                        // this builds td's into tr
                        tr.appendChild(classname);
                        tr.appendChild(health);
                        tr.appendChild(attack);
                        tr.appendChild(range);
                        tr.appendChild(movement);
                        // remove the old rows
                        resultContainer.innerHTML = '';
                        // append the row to table
                        resultContainer.appendChild(tr);
                    }
                })
                // show the description
                const classDetailContainer = document.getElementById("class-description");
                classDetailContainer.innerHTML = class_descriptions[selectedclass];
            })
            // catch fetch errors
            .catch(err => {
                console.error(err);
                const tr = document.createElement("tr");
                const td = document.createElement("td");
                td.innerHTML = err + ": " + url;
                tr.appendChild(td);
                resultContainer.appendChild(tr);
            });
        };
        function submitinfo() {
            // const url = "http://{{site.baseurl}}/api/currentchar/"; // revert back to 127.0.0.1:8086 for local
            const url = "http://127.0.0.1:8086/api/currentchar/";
            // get class information from table (which should be updated with the get request)
            var table = document.getElementById("result");
            var row = table.getElementsByTagName("tr");
            var cells = row[0].getElementsByTagName("td");
            const body = {
                name: document.getElementById("name").value,
                classname: cells[0].innerText,
                health: cells[1].innerText,
                attack: cells[2].innerText,
                range: cells[3].innerText,
                movement: cells[4].innerText
            };
            const AuthOptions = {
                mode: 'cors', // no-cors, *cors, same-origin
                credentials: 'include', // include, same-origin, omit
                headers: {
                    'Content-Type': 'application/json',
                },
                method: 'PUT', // override the method property
                cache: 'no-cache', // set the cache property
                body: JSON.stringify(body)
            };
            // fetch the API to update data
            fetch(url, AuthOptions)
            .then(response => {
                // check for response errors and display
                if (response.status !== 200) {
                    // window.location.href = "{{site.baseurl}}/authorizationfail"; *add endpoint for error screen if want
                    return;
                }
                // valid response will contain JSON data
                response.json().then(data => {
                    // redirect to actual game screen
                    window.location.href='{{site.baseurl}}/gamescreenML'
                })
            })
            // catch fetch errors (ie ACCESS to server blocked)
            .catch(err => {
            console.log(err)
            });
        }
        window.submitinfo = submitinfo;
    </script>
</body>