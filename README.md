# team_project
bhtyyht













<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
        <script type="text/javascript">
        window.onload = function() {
            document.getElementsByTagName("body")[0].innerHTML = '';
            createInput();
            getInputValue();
            createMap(mapRow, mapCol, 15, 15);
            createBtn();
        };

        // start/restart game
        var moveSnake, direction;
        var snakeSpeed = 150;
        var mapRow = 15;
        var mapCol = 15;
        var score = 0;
        function startGame() {
            getInputValue();
            if(mapRow >= 10 && mapCol >= 10) {
            document.getElementsByTagName("body")[0].innerHTML = '';
            snakeBody.splice(0);
            direction = 4;
            score = 0;
            clearInterval(moveSnake);
            createInput();
            createMap(mapCol, mapRow, 15, 15);
            createBtn();
            createSnake();
            createFood();
            moveSnake = setInterval(move, snakeSpeed);
            
            //when arrow(direction) key clicked
            document.onkeydown = function() {
                var check = true; // prevent fast-click bug
                if (event.keyCode == 38 && direction != 2) {
                    if (snakeHead - mapBlock.length / mapRow == snakeBody[0]) check = false;
                    if (check) direction = 1;
                } else if (event.keyCode == 40 && direction != 1) {
                    if (snakeHead + mapBlock.length / mapRow == snakeBody[0]) check = false;
                    if (check) direction = 2;
                } else if (event.keyCode == 37 && direction != 4) {
                    if (snakeHead - 1 == snakeBody[0]) check = false;
                    if (check) direction = 3;
                } else if (event.keyCode == 39 && direction != 3) {
                    if (snakeHead + 1 == snakeBody[0]) check = false;
                    if (check) direction = 4;
                }
                check = true;
            }
            } else {alert("mapRow and mapCol must be bigger than 9");}
        } // startGame()

        //Make map(table)
        function createMap(col, row, width, height) {
            var table = "<table id='map' border='1'>";

            for (var i = 0; i < row; i++) {
                table += "<tr>";
                for (var k = 0; k < col; k++) {
                    table += "<td width='" + width + "' height='" + height + "'></td>";
                }
                table += "</tr>";
            }
            document.write(table);
        }

        //make button
        function createBtn() {
            document.write("<button id='btn'>start</button>");
            document.getElementById('btn').onclick = function() {
                startGame();
            };
        }

        //make input
        function createInput() {
            document
                    .write("mapRow : <input type='text' id='mapRow' value='" + mapRow + "'/>");
            document
                    .write(" mapCol : <input type='text' id='mapCol' value='" + mapCol + "'/><br/>");
            document
                    .write("Snake speed(smaller = faster) : <input type='text' id='speed'value='" + snakeSpeed + "'/><br/>");
            document.write("<p id='score'>Score : " + score + "</p>");
        }

        function getInputValue() {
            mapRow = document.getElementById("mapRow").value;
            mapCol = document.getElementById("mapCol").value;
            snakeSpeed = document.getElementById("speed").value;
        }

        //make snake
        var snakeHead;
        var snakeBody = new Array();
        var mapBlock = document.getElementsByTagName('td');
        function createSnake() {
            var startLength = 4;
            snakeHead = Math.floor((mapRow % 2 == 1) ? (mapBlock.length / 2)
                    : (mapBlock.length / 2 - mapCol / 2));
            mapBlock[snakeHead].style.backgroundColor = 'green';
            for (var i = 1; i < startLength; i++) {
                snakeBody.push(snakeHead - i);
                mapBlock[snakeHead - i].style.backgroundColor = 'black';
            }
        }

        //make food
        var food;
        function createFood() {
            var check = true;
            while (true) {
                var ranLoc = Math.floor(Math.random() * mapBlock.length);
                if (snakeHead != ranLoc) {
                    for (var i = 0; i < snakeBody.length; i++) {
                        if (snakeBody[i] == ranLoc) {
                            check = false;
                            break;
                        }
                    }
                    if (check) {
                        food = ranLoc;
                        mapBlock[ranLoc].style.backgroundColor = "red";
                        break;
                    }
                    check = true;
                }
            }
        }

        //move snake
        var temp1, temp2;
        function moveTo(loc) {
            mapBlock[snakeHead].style.backgroundColor = 'white';
            mapBlock[loc].style.backgroundColor = 'green';
            for (var i = 0; i < snakeBody.length; i++) {
                if (i == 0) {
                    mapBlock[snakeBody[i]].style.backgroundColor = 'white';
                    temp1 = snakeBody[i];
                    snakeBody[i] = snakeHead;
                    snakeHead = loc;
                    mapBlock[snakeBody[i]].style.backgroundColor = 'black';
                } else {
                    mapBlock[snakeBody[i]].style.backgroundColor = 'white';
                    temp2 = snakeBody[i];
                    snakeBody[i] = temp1;
                    temp1 = temp2;
                    mapBlock[snakeBody[i]].style.backgroundColor = 'black';
                }
            }
        }

        function moveUp() { //1
            var nextLoc = snakeHead - mapBlock.length / mapRow;
            if (nextLoc >= 0) {
                moveTo(nextLoc);
            } else {
                gameOver();
            }
        }

        function moveDown() { //2
            var nextLoc = snakeHead + mapBlock.length / mapRow;
            if (nextLoc < mapBlock.length) {
                moveTo(nextLoc);
            } else {
                gameOver();
            }
        }

        function moveLeft() { //3
            var nextLoc = snakeHead - 1;
            if ((nextLoc + 1) % mapCol != 0) {
                moveTo(nextLoc);
            } else {
                gameOver();
            }
        }

        function moveRight() { //4
            var nextLoc = snakeHead + 1;
            if (nextLoc % mapCol != 0) {
                moveTo(nextLoc);
            } else {
                gameOver();
            }
        }

        function move() {
            switch (direction) {
            case 1:
                moveUp();
                break;
            case 2:
                moveDown();
                break;
            case 3:
                moveLeft();
                break;
            case 4:
                moveRight();
                break;
            }

            for (var i = 0; i < snakeBody.length; i++) {
                if (snakeHead == snakeBody[i]) {
                    gameOver();
                    break;
                }
            }

            if (snakeHead == food)
                eat();
        }

        function eat() {
            snakeBody.push(temp2);
            mapBlock[temp2].style.backgroundColor = "black";
            score++;
            document.getElementById("score").innerHTML = "Score : " + score;
            createFood();
        }

        function gameOver() {
            clearInterval(moveSnake);
            alert("Game Over!");
        }
        </script>
    </head>
    <body>
    </body>
</html>
