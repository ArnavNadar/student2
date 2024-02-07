---
permalink: /403
title: 403page
---

<html>
<head>
    <style>
        .darkmode {
            background: #252525;
            color: #ffffff;
        }
        .lightmode {
            background: #ffffff;
            color: #000000;
        }
    </style>
    <link id="theme-style" rel="stylesheet" type="text/css" href="assets/css/style.css">
</head>
<body class="lightmode">
    <div class="index">
        <div class="center-block" style="margin: 2% 10% 10%; font-family: gilroy,serif;">
            <div class="col-12" style="text-align: center;">
                <h1><strong>Unauthorized</strong></h1>
            </div>
            <div class="jumbotron jumbotron-fluid" style="text-align: center; ">
                <h2>Try Again</h2>
                <!-- Prompt the user to go back to where they were before 403 page showed -->
                <a href="javascript:history.back()">Go Back</a>
                <p>Or</p>
                <a href="/student2/register">Register</a><a> Or </a><a href="/student2/signin">Sign in</a>
            </div>
        </div>
    </div>
</body>
<script>
    var darkMode = false;
    window.onload = function() {
        var themeStyle = document.getElementById('theme-style');
        var body = document.body;
        var storedTheme = localStorage.getItem('theme');
        if (storedTheme === 'dark') {
            themeStyle.href = "assets/css/dark.css";
            body.classList.remove('lightmode');
            body.classList.add('darkmode');
        } else {
            themeStyle.href = "assets/css/style.css";
            body.classList.remove('darkmode');
            body.classList.add('lightmode');
        }
    }
</script>
</html>