---
layout: default
title: Student Blog
---
 
## Arnav Nadar's Blog 
My name is Arnav Nadar and I am a 10th grader at Del Norte High School. Evere since I was five years of age I had an intrest for coding because my father is a Software Engineer at Sony. My first coding class that I took was in 5th grade where they taught the the basics of python like how to print and etc. Over time I have at least basic knowledge of two coding languages that are Python and Java. In 7th grade I was a part of the school coding team club while I also did FLL robotics apart from school. After much work I got elected as president of the FLL club which was an amazing experience for me because I would teach incoming 6th graders about coding. 


<a href="https://ibb.co/yhwBJs4"><img src="https://i.ibb.co/CJksF0P/img-0496.jpg" alt="img-0496" border="0" width="500px" hight="400px" ></a>  


This is my freeform made on week 0 on this class. I have an Indian Flag because I was born in Mumbai, India. I then moved to the Califoria at age of 2 because my dad had gotten a job over here. I go to the school DNHS. One out of my many hobbies is listening to music and thats why I have a picture of a music symbol. 

|  <img src="https://i.ibb.co/KN8chtN/IMG-0229.jpg" width="500px" height="400px">
| :--: || :--: |
| This is a picture of me and my brother ofcourse as kids. 

|  <a href="https://ibb.co/0GSBS6K"><img src="https://i.ibb.co/0GSBS6K/10f79a49-ec88-4255-adf3-8f8004dbb437.jpg" alt="10f79a49-ec88-4255-adf3-8f8004dbb437" border="0"></a>
| :--:||:--:|
|This is a picture with one of my close friends. He is one out of many people I can trust with my life. If I need help or advice with anything I have them. If I wanna go somewhere but not alone all my friends will come just to give me support. My friends have my total respect and I have theirs. |







My class Schedule:

| Period | Class |
|----------|----------|
| 1 |    High School English 3 |
| 2 |    AP World History |
| 3 |    AP Calc AB |
| 4 |    AP Bio  |
| 5 |    AP Computer Science |



## Challenges and how I got past them. 

Throughout my journey, I encountered a multitude of errors while installing and configuring the tools. For instance, many of the commands in the setup instructions didn't function as intended on my Computer. To address these issues, I engaged in troubleshooting by seeking help from peers, more seasoned programmers, attending office hours, and conducting personal research. I believe a significant factor was my lack of familiarity with platforms like terminal, given my greater comfort with HTML, CSS, and basic Python. Eventually, I managed to decipher the execution of commands such as python, pip, and make. This experience imparted problem-solving skills even in the absence of comprehensive understanding.
As I progress and evolve as a computer scientist, I anticipate developing these troubleshooting skills further, allowing me to tackle challenges through experimentation.

TIC TAC TOE


<html>
<head>
  <style>
    .board {
      display: grid;
      grid-template-columns: repeat(3, 100px);
      grid-gap: 2px;
    }
    .cell {
      width: 100px;
      height: 100px;
      font-size: 2em;
      text-align: center;
      vertical-align: middle;
      border: 1px solid black;
    }
  </style>
</head>
<body>
<div class="board" id="board">
  <div class="cell" onclick="makeMove(0, 0)"></div>
  <div class="cell" onclick="makeMove(0, 1)"></div>
  <div class="cell" onclick="makeMove(0, 2)"></div>
  <div class="cell" onclick="makeMove(1, 0)"></div>
  <div class="cell" onclick="makeMove(1, 1)"></div>
  <div class="cell" onclick="makeMove(1, 2)"></div>
  <div class="cell" onclick="makeMove(2, 0)"></div>
  <div class="cell" onclick="makeMove(2, 1)"></div>
  <div class="cell" onclick="makeMove(2, 2)"></div>
</div>
<script>
  const board = [
    ['', '', ''],
    ['', '', ''],
    ['', '', '']
  ];
  let currentPlayer = 'X';
  function makeMove(row, col) {
    const cell = document.getElementById('board').children[row * 3 + col];
    if (board[row][col] === '') {
      cell.innerText = currentPlayer;
      board[row][col] = currentPlayer;
      if (checkWinner(currentPlayer)) {
        alert(`Player ${currentPlayer} wins!`);
        resetBoard();
      } else {
        currentPlayer = (currentPlayer === 'X') ? 'O' : 'X';
      }
    } else {
      alert('Invalid move. Cell is already occupied.');
    }
  }
  function checkWinner(player) {
    // Check rows, columns, and diagonals
    for (let i = 0; i < 3; i++) {
      if (
        (board[i][0] === player && board[i][1] === player && board[i][2] === player) ||
        (board[0][i] === player && board[1][i] === player && board[2][i] === player)
      ) {
        return true;
      }
    }
    if (
      (board[0][0] === player && board[1][1] === player && board[2][2] === player) ||
      (board[0][2] === player && board[1][1] === player && board[2][0] === player)
    ) {
      return true;
    }
    return false;
  }
  function resetBoard() {
    for (let row = 0; row < 3; row++) {
      for (let col = 0; col < 3; col++) {
        board[row][col] = '';
        document.getElementById('board').children[row * 3 + col].innerText = '';
      }
    }
    currentPlayer = 'X';
  }
</script>
</body>
</html>
















