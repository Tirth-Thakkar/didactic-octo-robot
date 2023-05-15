# Username
<!-- Form for username input -->
<form>
  <label for="user_id">Username</label>
  <input type="text" id="login_username" name="user_id" placeholder="Jhonno" value=""><br>
  <button type="button" id="login_submittion"><h3>Login</h3></button>
</form>

<script>
  let login_button = document.getElementById("login_submittion");
  // After Button Click Saving Username to local storage
  login_button.onclick = function(){
    var username = document.getElementById("login_username").value;
    localStorage.setItem("username", username);
    localStorage.setItem("logged_in", true);
    let logged_in_user = localStorage.getItem("username");
    let login_status = localStorage.getItem("logged_in")
    console.log(logged_in_user);
    console.log(login_status)
    window.location.href = "{{site.baseurl}}/game"
  };  
</script>