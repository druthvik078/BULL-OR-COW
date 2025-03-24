<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login Page</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
        }
        .container {
            max-width: 300px;
            margin: auto;
            padding: 20px;
            border: 1px solid #ccc;
            border-radius: 10px;
            box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
        }
        .btn {
            display: inline-block;
            margin-top: 10px;
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            text-decoration: none;
            border-radius: 5px;
            border: none;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>Login</h2>
        <form id="loginForm">
            <input type="text" id="username" placeholder="Username" required><br><br>
            <input type="password" id="password" placeholder="Password" required><br><br>
            <button type="submit" class="btn">Login</button>
        </form>
    </div>

    <script>
        document.addEventListener("DOMContentLoaded", function() {
            document.getElementById("loginForm").addEventListener("submit", function(event) {
                event.preventDefault();
                
                let username = document.getElementById("username").value;
                let password = document.getElementById("password").value;
                
                // Hardcoded credentials for validation (for demo purposes)
                let validUsername = "admin";
                let validPassword = "password123";
                
                if (username === validUsername && password === validPassword) {
                    window.location.href = "success.html";
                } else {
                    alert("Invalid username or password");
                }
            });
        });
    </script>
</body>
</html>
