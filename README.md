# egocollapse.github.io
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Balanced Shuffle with Names and Numbers</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
            transition: background-color 0.3s, color 0.3s;
        }

        /* Light Mode */
        body.light-mode {
            background-color: #f8f9fa;
            color: #212529;
        }

        /* Dark Mode */
        body.dark-mode {
            background-color: #343a40;
            color: #f8f9fa;
        }

        .button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            margin-top: 10px;
        }

        .result {
            margin-top: 20px;
            display: flex;
            justify-content: space-between; /* Space the teams across the columns */
        }

        .team {
            width: 45%; /* Each team takes 45% of the space */
        }

        .team h3 {
            margin-bottom: 10px;
        }

        .team-list {
            display: flex;
            flex-direction: column; /* Display in columns */
            gap: 10px; /* Add space between each person */
        }

        .team-member {
            display: flex;
            justify-content: space-between; /* Align name and value horizontally */
            align-items: center;
        }

        .input-group {
            margin-bottom: 10px;
        }

        .input-group input {
            padding: 8px;
            margin: 5px 0;
        }

        .added-persons {
            margin-top: 20px;
        }

        .added-persons ul {
            list-style-type: none;
            padding-left: 0;
        }

        .added-persons li {
            padding: 5px;
            display: flex;
            align-items: center;
        }

        .buttons-container {
            display: inline-block;
            margin-left: 10px;
        }

        /* Dark Mode Button */
        .dark-mode-toggle {
            position: fixed;
            top: 10px;
            right: 10px;
            padding: 10px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        .dark-mode-toggle:hover {
            background-color: #0056b3;
        }

        /* Ensure text is visible on dark background */
        body.dark-mode .added-persons li {
            color: #f8f9fa; /* White text in dark mode */
        }

        body.dark-mode .team h3, body.dark-mode .result p {
            color: #f8f9fa; /* White text for team and result */
        }

        body.light-mode .added-persons li {
            color: #212529; /* Dark text in light mode */
        }

        body.light-mode .team h3, body.light-mode .result p {
            color: #212529; /* Dark text for team and result */
        }

        /* Styling for the medal name to ensure visibility */
        .medal-name {
            font-weight: bold;
        }
    </style>
</head>
<body class="light-mode">

    <!-- Dark Mode Toggle Button -->
    <button class="dark-mode-toggle" onclick="toggleDarkMode()">Switch to Dark Mode</button>

    <h1>Balanced Shuffle of Numbers with Names</h1>

    <div id="inputForm">
        <div class="input-group">
            <label for="name1">Name:</label>
            <input type="text" id="name1" placeholder="Enter name">
            <label for="value1">Value:</label>
            <input type="number" id="value1" placeholder="Enter number">
            <button class="button" onclick="addInput()">Add Person</button>
        </div>
    </div>

    <!-- List of added persons -->
    <div class="added-persons">
        <h3>Added People:</h3>
        <ul id="peopleList"></ul>
    </div>

    <button class="button" onclick="balancedShuffle()">Shuffle & Balance</button>

    <div class="result">
        <div class="team">
            <h3>Team A:</h3>
            <div id="teamA" class="team-list"></div>
            <p>Total: <span id="totalA"></span></p>
        </div>
        <div class="team">
            <h3>Team B:</h3>
            <div id="teamB" class="team-list"></div>
            <p>Total: <span id="totalB"></span></p>
        </div>
    </div>

    <p><strong>Difference:</strong> <span id="difference"></span></p>

    <script>
        let people = [];

        // Function to toggle between dark mode and light mode
        function toggleDarkMode() {
            const body = document.body;
            const button = document.querySelector(".dark-mode-toggle");

            // Check current mode and toggle
            if (body.classList.contains("light-mode")) {
                body.classList.remove("light-mode");
                body.classList.add("dark-mode");
                button.textContent = "Switch to Light Mode";
                localStorage.setItem("theme", "dark");
            } else {
                body.classList.remove("dark-mode");
                body.classList.add("light-mode");
                button.textContent = "Switch to Dark Mode";
                localStorage.setItem("theme", "light");
            }
        }

        // Load the user's theme preference from localStorage
        window.onload = function() {
            const savedTheme = localStorage.getItem("theme");
            if (savedTheme === "dark") {
                document.body.classList.add("dark-mode");
                document.querySelector(".dark-mode-toggle").textContent = "Switch to Light Mode";
            } else {
                document.body.classList.add("light-mode");
                document.querySelector(".dark-mode-toggle").textContent = "Switch to Dark Mode";
            }
        };

        // Function to add a person with their value to the list
        function addInput() {
            const name = document.getElementById("name1").value;
            const value = parseInt(document.getElementById("value1").value);

            if (name && value) {
                const medal = getMedal(value); // Assign medal based on MMR
                people.push({ name: name, value: value, medal: medal });

                // Clear the input fields after adding the person
                document.getElementById("name1").value = '';
                document.getElementById("value1").value = '';

                // Display the added person in the list
                displayAddedPeople();
            } else {
                alert("Please enter both a name and a number.");
            }
        }

        // Function to display the list of added people with medals (without pictures)
        function displayAddedPeople() {
            const peopleList = document.getElementById("peopleList");
            peopleList.innerHTML = ''; // Clear the list before adding new items

            // Create a list item for each person added
            people.forEach((person, index) => {
                const listItem = document.createElement("li");
                listItem.textContent = `${person.name} - ${person.value}`;

                // Create medal name text with a specific class for visibility
                const medalName = document.createElement("span");
                medalName.classList.add("medal-name");
                medalName.textContent = ` (${person.medal.name})`;

                // Create the edit and remove buttons
                const buttonsContainer = document.createElement("div");
                buttonsContainer.classList.add("buttons-container");

                // Edit button
                const editButton = document.createElement("button");
                editButton.classList.add("button");
                editButton.textContent = "Edit";
                editButton.onclick = () => editPerson(index);

                // Remove button
                const removeButton = document.createElement("button");
                removeButton.classList.add("button");
                removeButton.textContent = "Remove";
                removeButton.onclick = () => removePerson(index);

                buttonsContainer.appendChild(editButton);
                buttonsContainer.appendChild(removeButton);

                listItem.appendChild(medalName); // Add medal name (no image)
                listItem.appendChild(buttonsContainer);

                peopleList.appendChild(listItem);
            });
        }

        // Function to edit a person's details
        function editPerson(index) {
            const person = people[index];
            const newName = prompt("Enter new name:", person.name);
            const newValue = prompt("Enter new value:", person.value);

            if (newName && newValue) {
                person.name = newName;
                person.value = parseInt(newValue);
                person.medal = getMedal(person.value); // Recalculate medal

                displayAddedPeople(); // Refresh the list
            }
        }

        // Function to remove a person from the list
        function removePerson(index) {
            people.splice(index, 1); // Remove the person from the array
            displayAddedPeople(); // Refresh the list
        }

        // Function to get the medal based on the person's MMR
        function getMedal(mmr) {
            if (mmr <= 769) return { name: "Herald" };
            if (mmr <= 1539) return { name: "Guardian" };
            if (mmr <= 2309) return { name: "Crusader" };
            if (mmr <= 3079) return { name: "Archon" };
            if (mmr <= 3849) return { name: "Legend" };
            if (mmr <= 4619) return { name: "Ancient" };
            if (mmr <= 5620) return { name: "Divine" };
            return { name: "Immortal" };
        }

        // Function to perform multiple shuffles and find the best possible minimum difference
        function balancedShuffle() {
            let bestDifference = Infinity;
            let bestTeamA = [];
            let bestTeamB = [];

            // Set number of shuffles to perform
            const numShuffles = 1000; // You can adjust the number of shuffles

            for (let i = 0; i < numShuffles; i++) {
                // Shuffle the people array randomly
                let shuffledPeople = shuffleArray([...people]);

                // Split shuffled people into two teams
                let teamA = [];
                let teamB = [];
                let teamATotal = 0;
                let teamBTotal = 0;

                for (let j = 0; j < shuffledPeople.length; j++) {
                    if (teamATotal < teamBTotal) {
                        teamA.push(shuffledPeople[j]);
                        teamATotal += shuffledPeople[j].value;
                    } else {
                        teamB.push(shuffledPeople[j]);
                        teamBTotal += shuffledPeople[j].value;
                    }
                }

                // Calculate the difference between the two teams
                let difference = Math.abs(teamATotal - teamBTotal);

                // If this shuffle is better (smaller difference), update the best teams and difference
                if (difference < bestDifference) {
                    bestDifference = difference;
                    bestTeamA = teamA;
                    bestTeamB = teamB;
                }
            }

            // Display the best result after multiple shuffles
            displayTeams(bestTeamA, bestTeamB, bestDifference);
        }

        // Function to shuffle an array randomly
        function shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        // Function to display the shuffled teams
        function displayTeams(teamA, teamB, difference) {
            const teamAContainer = document.getElementById("teamA");
            const teamBContainer = document.getElementById("teamB");
            const totalA = document.getElementById("totalA");
            const totalB = document.getElementById("totalB");
            const diffContainer = document.getElementById("difference");

            teamAContainer.innerHTML = '';
            teamBContainer.innerHTML = '';
            totalA.textContent = teamA.reduce((sum, person) => sum + person.value, 0);
            totalB.textContent = teamB.reduce((sum, person) => sum + person.value, 0);
            diffContainer.textContent = difference;

            // Add members to team A
            teamA.forEach(person => {
                const personDiv = document.createElement("div");
                personDiv.textContent = `${person.name} - ${person.value} (${person.medal.name})`;
                teamAContainer.appendChild(personDiv);
            });

            // Add members to team B
            teamB.forEach(person => {
                const personDiv = document.createElement("div");
                personDiv.textContent = `${person.name} - ${person.value} (${person.medal.name})`;
                teamBContainer.appendChild(personDiv);
            });
        }
    </script>
</body>
</html>




