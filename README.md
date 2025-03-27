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
    </style>
</head>
<body>

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

        // Function to add a person with their value to the list
        function addInput() {
            const name = document.getElementById("name1").value;
            const value = parseInt(document.getElementById("value1").value);

            if (name && value) {
                people.push({ name: name, value: value });

                // Clear the input fields after adding the person
                document.getElementById("name1").value = '';
                document.getElementById("value1").value = '';

                // Display the added person in the list
                displayAddedPeople();
            } else {
                alert("Please enter both a name and a number.");
            }
        }

        // Function to display the list of added people
        function displayAddedPeople() {
            const peopleList = document.getElementById("peopleList");
            peopleList.innerHTML = ''; // Clear the list before adding new items

            // Create a list item for each person added
            people.forEach(person => {
                const listItem = document.createElement("li");
                listItem.textContent = `${person.name}: ${person.value}`;
                peopleList.appendChild(listItem);
            });
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
            document.getElementById("teamA").textContent = subset.map(person => `${person.name}: ${person.value}`).join(", ");
            document.getElementById("totalA").textContent = subset.reduce((sum, person) => sum + person.value, 0);
            document.getElementById("teamB").textContent = remainingPeople.map(person => `${person.name}: ${person.value}`).join(", ");
            document.getElementById("totalB").textContent = remainingPeople.reduce((sum, person) => sum + person.value, 0);
            document.getElementById("difference").textContent = Math.abs(subset.reduce((sum, person) => sum + person.value, 0) - remainingPeople.reduce((sum, person) => sum + person.value, 0));
        }
    </script>

</body>
</html>

