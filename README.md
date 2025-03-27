# egocollapse.github.io
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Balanced Shuffle with Names</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
        }
        .button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
        .result {
            margin-top: 20px;
        }
        .team {
            margin-bottom: 10px;
        }
    </style>
</head>
<body>

    <h1>Balanced Shuffle of Numbers with Names</h1>
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
        // List of people with their associated numbers
        const people = [
            { name: "Alice", value: 100 },
            { name: "Bob", value: 100 },
            { name: "Charlie", value: 304 },
            { name: "David", value: 400 },
            { name: "Eve", value: 500 },
            { name: "Frank", value: 200 },
            { name: "Grace", value: 199 },
            { name: "Hannah", value: 300 },
            { name: "Ivy", value: 400 },
            { name: "Jack", value: 500 }
        ];

        function balancedShuffle() {
            // Get the total sum of all values
            const totalSum = people.reduce((sum, person) => sum + person.value, 0);
            const target = Math.floor(totalSum / 2);

            // DP table to track achievable sums
            let dp = Array(target + 1).fill(false);
            dp[0] = true;

            // Fill DP table
            for (let person of people) {
                for (let i = target; i >= person.value; i--) {
                    dp[i] = dp[i] || dp[i - person.value];
                }
            }

            // Find the closest sum to target
            let closestSum = target;
            while (closestSum >= 0 && !dp[closestSum]) {
                closestSum--;
            }

            // Backtrack to find the subset that forms the closest sum
            let subset = [];
            let remainingSum = closestSum;
            let remainingPeople = [...people];

            for (let person of people.reverse()) {
                if (remainingSum >= person.value && dp[remainingSum - person.value]) {
                    subset.push(person);
                    remainingSum -= person.value;
                    remainingPeople.splice(remainingPeople.indexOf(person), 1);
                }
            }

            // Display the result with names and values
            document.getElementById("teamA").textContent = subset.map(person => `${person.name}: ${person.value}`).join(", ");
            document.getElementById("totalA").textContent = subset.reduce((sum, person) => sum + person.value, 0);
            document.getElementById("teamB").textContent = remainingPeople.map(person => `${person.name}: ${person.value}`).join(", ");
            document.getElementById("totalB").textContent = remainingPeople.reduce((sum, person) => sum + person.value, 0);
            document.getElementById("difference").textContent = Math.abs(subset.reduce((sum, person) => sum + person.value, 0) - remainingPeople.reduce((sum, person) => sum + person.value, 0));
        }
    </script>

</body>
</html>
