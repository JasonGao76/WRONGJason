---
toc: true
comments: true
title: Main Menu
type: hacks
courses: { compsci: {week: 20} }
permalink: /main_menu/
---

<html>
    <head>
        <style>
            body {
                background-color: black;
                color: #7a7e7e;
            }
            h1{
                text-align: right;
                font-family: 'Verdana';
                font-size: 50px;
            }
            h2{
                text-align: right;
                font-family: 'Verdana';
                font-size: 25px;
            }
            .black-bar {
                height: 5px;
                background-color: black;
                width: 39%;
                margin-left: 55%;
            }
            .startbutton {
                text-align: center;
                font-size: 20px;
                color: white;
                background-color: black;
                position: absolute;
                margin: 0;
                left: 25%;
                top: 50%;
                padding: 80px 120px;
            }
            .leaderboardButton {
                text-align: center;
                font-size: 20px;
                color: white;
                background-color: black;
                position: absolute;
                margin: 0;
                left: 53%;
                top: 50%;
                padding: 25px 120px;
            }
            .loginButton {
                text-align: center;
                font-size: 20px;
                color: white;
                background-color: black;
                position: absolute;
                margin: 0;
                left: 53%;
                top: 65%;
                padding: 25px 152px;
            }
        </style>
    </head>
    <body>
        <h1>
            TBFG&trade; - Turn Based Fantasy Game&nbsp;&nbsp;&nbsp;&nbsp;
        </h1>
        <div class="black-bar">&nbsp;&nbsp;&nbsp;&nbsp;</div>
        <h2>
            Developed by Project WRONG&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        </h2>
        <img src="https://i.postimg.cc/wj2FYHpM/candle-removebg-preview.png">
        <button class="startbutton" type="button" onclick="()">Start</button>
        <button class="leaderboardButton" type="button" onclick="()">Leaderboard</button>
        <button class="loginButton" type="button" onclick="()">Login</button>
        <a href="http://{{site.baseurl}}/charactercreation"><button class="startbutton" type="button">Start Game</button></a> <!-- revert back to 127.0.0.1:8086 for local -->
        <a href="http://1{{site.baseurl}}/leaderboard"><button class="leaderboardButton" type="button">Leaderboard</button></a> <!-- revert back to 127.0.0.1:8086 for local -->
        <a href="http://{{site.baseurl}}/login"><button class="loginButton" type="button">Login</button></a> <!-- revert back to 127.0.0.1:8086 for local -->
    </body>
</html>
