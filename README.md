# egocollapse.github.io
<!DOCTYPE html>
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
        }

        .team {
            margin-bottom: 10px;
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
        }

        .buttons-container {
            display: inline-block;
            margin-left: 10px;
        }

        img.medal {
            width: 30px;
            height: 30px;
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
            <p id="teamA"></p>
            <p>Total: <span id="totalA"></span></p>
        </div>
        <div class="team">
            <h3>Team B:</h3>
            <p id="teamB"></p>
            <p>Total: <span id="totalB"></span></p>
        </div>
        <p><strong>Difference:</strong> <span id="difference"></span></p>
    </div>

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

        // Function to display the list of added people with medals as images
        function displayAddedPeople() {
            const peopleList = document.getElementById("peopleList");
            peopleList.innerHTML = ''; // Clear the list before adding new items

            // Create a list item for each person added
            people.forEach((person, index) => {
                const listItem = document.createElement("li");
                listItem.textContent = `${person.name} - ${person.value}`;
                
                // Add medal image
                const medalImage = document.createElement("img");
                medalImage.src = person.medal.imgSrc; // Get the image source
                medalImage.alt = person.medal.name;
                medalImage.classList.add("medal");

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

                listItem.appendChild(medalImage);
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
    if (mmr >= 1 && mmr <= 769) return { name: "Herald", imgSrc: "https://via.placeholder.com/30x30/ff0000/ffffff?text=H" }; // Herald
    if (mmr >= 770 && mmr <= 1539) return { name: "Guardian", imgSrc: "https://via.placeholder.com/30x30/00ff00/ffffff?text=G" }; // Guardian
    if (mmr >= 1540 && mmr <= 2309) return { name: "Crusader", imgSrc: "https://via.placeholder.com/30x30/0000ff/ffffff?text=C" }; // Crusader
    if (mmr >= 2310 && mmr <= 3079) return { name: "Archon", imgSrc: "https://via.placeholder.com/30x30/ff00ff/ffffff?text=A" }; // Archon
    if (mmr >= 3080 && mmr <= 3849) return { name: "Legend", imgSrc: "https://via.placeholder.com/30x30/ffff00/ffffff?text=L" }; // Legend
    if (mmr >= 3850 && mmr <= 4619) return { name: "Ancient", imgSrc: "https://via.placeholder.com/30x30/00ffff/ffffff?text=AN" }; // Ancient
    if (mmr >= 4620 && mmr <= 5620) return { name: "Divine", imgSrc: "https://via.placeholder.com/30x30/ff9900/ffffff?text=D" }; // Divine
    return { name: "Immortal", imgSrc: "https://via.placeholder.com/30x30/ff6666/ffffff?text=I" }; // Immortal
}

        // Function to perform the balanced shuffle
        function balancedShuffle() {
            const totalSum = people.reduce((sum, person) => sum + person.value, 0);
            const target = Math.floor(totalSum / 2);

            // DP array to track achievable sums
            let dp = Array(target + 1).fill(false);
            dp[0] = true;  // Base case: zero sum is always achievable

            // Fill DP array with achievable sums
            for (let person of people) {
                for (let i = target; i >= person.value; i--) {
                    dp[i] = dp[i] || dp[i - person.value];
                }
            }

            // Find the closest sum to the target
            let closestSum = target;
            while (closestSum >= 0 && !dp[closestSum]) {
                closestSum--;
            }

            // Backtrack to find the subset that forms the closest sum
            let subset = [];
            let remainingSum = closestSum;
            let remainingPeople = [...people];

            for (let person of remainingPeople.reverse()) {
                if (remainingSum >= person.value && dp[remainingSum - person.value]) {
                    subset.push(person);
                    remainingSum -= person.value;
                    remainingPeople.splice(remainingPeople.indexOf(person), 1);
                }
            }

            // Display the result with names and their totals
            document.getElementById("teamA").textContent = subset.map(person => `${person.name}: ${person.value} (${person.medal.name})`).join(", ");
            document.getElementById("totalA").textContent = subset.reduce((sum, person) => sum + person.value, 0);
            document.getElementById("teamB").textContent = remainingPeople.map(person => `${person.name}: ${person.value} (${person.medal.name})`).join(", ");
            document.getElementById("totalB").textContent = remainingPeople.reduce((sum, person) => sum + person.value, 0);
            document.getElementById("difference").textContent = Math.abs(subset.reduce((sum, person) => sum + person.value, 0) - remainingPeople.reduce((sum, person) => sum + person.value, 0));
        }
    </script>

</body>
</html>


