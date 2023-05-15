
<head>
    <title>Leaderboard</title>
</head>
<body>
    <div>
        <form id="searchForm">
            <input type="text" id="usernameInput" placeholder="Enter username">
            <button type="submit">Search</button>
            <button type="button" id="returnButton" style="display: none;">Return</button>
        </form>
    </div>

    <div>
        <table id="leaderboard">
            <tr>
                <th>User</th>
                <th>Score</th>
            </tr>
        </table>
    </div>

    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script>
        // Update the leaderboard every 5 seconds
        setInterval(updateLeaderboard, 15000);

        // Retrieve the leaderboard data and create the table when the page is loaded
        updateLeaderboard();

        // Handle return button click
        $('#returnButton').on('click', function () {
        updateLeaderboard();
            $('#returnButton').hide();
        });
        
        // Handle form submission
        $('#searchForm').on('submit', function (event) {
            event.preventDefault();
            var username = $('#usernameInput').val();
            if (username !== '') {
                searchUser(username);
            }
        });

        function searchUser(username) {
        console.log('Searching for user:', username);

        // Show loading message
        $('#leaderboard').html('<tr><td colspan="2">Loading...</td></tr>');

        // Make the asynchronous POST request to query the API
        fetch('http://localhost:8086/api/leaderboard/search', {
            method: 'POST',
            headers: {
            'Content-Type': 'application/json'
            },
            body: JSON.stringify({"name": username}) // Format the payload as user: username
        })
            .then(response => response.json())
            .then(data => {
            // Clear the current leaderboard on search
            $('#leaderboard').empty();

            if (data.length > 0) {
                data.forEach(function (entry) {
                var user = entry.name;
                var score = entry.score;
                $('#leaderboard').append('<tr><td>' + user + '</td><td>' + score + '</td></tr>');
                });
            } else {
                $('#leaderboard').append('<tr><td colspan="2">User not found</td></tr>');
            }

            console.log('Search completed.');
            $('#returnButton').show();
            })
            .catch(error => {
            console.log('Error:', error);
            });
        }



        // Function to update the leaderboard
        function updateLeaderboard() {
            console.log('Updating leaderboard...');

            // Make the asynchronous GET request to retrieve leaderboard data from the API
            $.getJSON('http://localhost:8086/api/leaderboard/MaxScore')
            .done(function (data) {
                // Clear the current leaderboard on update
                $('#leaderboard').empty();

                // Adds the new scores to the leaderboard from the API response
                data.forEach(function (entry) {
                    var user = entry.name;
                    var score = entry.score;
                    $('#leaderboard').append('<tr><td>' + user + '</td><td>' + score + '</td></tr>');
                });

                console.log('Leaderboard updated.');
            })
            .fail(function (error) {
                console.log('Error:', error);
            });
        }
    </script>
</body>

