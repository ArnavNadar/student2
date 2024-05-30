---
comments: True
layout: post
toc: false
title: Button Drop Down
permalink: /button
description: Drop Down
type: hacks
courses: { "compsci": { "week": 24 } }
---


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dropdown Button Example</title>
    <style>
        /* Button styling */
        .dropdown-button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }

        /* Dropdown menu styling */
        .dropdown-menu {
            display: none;
            position: absolute;
            background-color: white;
            border: 1px solid #ccc;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
            z-index: 1000;
        }

        .dropdown-menu a {
            display: block;
            padding: 10px 20px;
            text-decoration: none;
            color: black;
        }

        .dropdown-menu a:hover {
            background-color: #f0f0f0;
        }
    </style>
</head>
<body>

<!-- Button that triggers the dropdown menu -->
<button class="dropdown-button" onclick="toggleDropdown()">Click me</button>

<!-- Dropdown menu -->
<div class="dropdown-menu" id="dropdownMenu">
    <a href="#">Option 1</a>
    <a href="#">Option 2</a>
    <a href="#">Option 3</a>
</div>

<script>
    function toggleDropdown() {
        var dropdownMenu = document.getElementById("dropdownMenu");
        if (dropdownMenu.style.display === "block") {
            dropdownMenu.style.display = "none";
        } else {
            dropdownMenu.style.display = "block";
        }
    }

    // Close the dropdown menu if the user clicks outside of it
    window.onclick = function(event) {
        if (!event.target.matches('.dropdown-button')) {
            var dropdowns = document.getElementsByClassName("dropdown-menu");
            for (var i = 0; i < dropdowns.length; i++) {
                var openDropdown = dropdowns[i];
                if (openDropdown.style.display === "block") {
                    openDropdown.style.display = "none";
                }
            }
        }
    }
</script>

</body>
</html>

