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
clicked = false;
function cookieClick() {
    if (!gameOver) {
        clicked = true;
        score++;
        scoreText.innerHTML = score;
    }
    if (clicked){
setTimeout(function () {
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
}, 100); // Timeout duration set to 10 seconds
};
}

function tryagain(){
    window.location.href = "{{site.baseurl}}/game";
}
</script>
