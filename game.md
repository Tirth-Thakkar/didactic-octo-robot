<style>
    #Gameover {
        display: none;
    }
</style>

<div id="game">
    <div id="score">
        <h1>0</h1>
    </div>
    <button id = "restart" onclick = "tryagain()" >Restart</button>
    <br><br><img src="cookie.png" id="cookie" onclick="cookieClick()">
    <img src="GameOver.png" id="Gameover">
</div>

<script>
let cookie = document.getElementById("cookie");
let score = 0;
let scoreText = document.getElementById("score");
let gameOver = false; // Flag variable to track game over state

function cookieClick() {
    if (!gameOver) {
        score++;
        scoreText.innerHTML = score;
    }
}

function tryagain() {
    window.location.href = "{{site.baseurl}}/game";
}

function gameover() {
    if (!gameOver) {
        cookie.style.display = "none";
        document.getElementById("Gameover").style.display = "block";
        gameOver = true;

        fetch("http://localhost:8086/api/leaderboard/score", {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                "name": localStorage.getItem("username"),
                "score": score
            })
        })
        .then(response => response.json())
        .then(data => {
            console.log(data);
        })
        .catch(error => {
            console.log(error);
        });
    }
}

// Timer functionality
let timer = setTimeout(gameover, 30000); // Set the timer to 30 seconds

function resetTimer() {
    clearTimeout(timer); // Clear the timer
    timer = setTimeout(gameover, 30000); // Reset the timer to 30 seconds
}

</script>
