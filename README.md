<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Responsive Infinite Chess</title>
    <link rel="stylesheet"
      href="https://unpkg.com/@chrisoakman/chessboardjs@1.0.0/dist/chessboard-1.0.0.min.css">
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #262421; /* Chess.com dark theme */
            font-family: sans-serif;
            color: white;
        }
        #board-container {
            width: 90vw;
            max-width: 600px; /* Limits size on desktop */
        }
        .controls {
            margin-top: 20px;
            text-align: center;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background: #81b64c;
            border: none;
            color: white;
            border-radius: 4px;
            font-weight: bold;
        }
        button:hover { background: #a3d16a; }
        .status { margin-bottom: 10px; }
    </style>
</head>
<body>

    <div class="status" id="status">White to move</div>
    
    <div id="board-container">
        <div id="myBoard"></div>
    </div>

    <div class="controls">
        <button onclick="resetGame()">New Game</button>
        <p>Time: ∞ (Infinite Mode)</p>
    </div>

    <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
    <script src="https://unpkg.com/@chrisoakman/chessboardjs@1.0.0/dist/chessboard-1.0.0.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.3/chess.min.js"></script>

    <script>
        var board = null;
        var game = new Chess();
        var $status = $('#status');

        function onDragStart (source, piece, position, orientation) {
            // Do not pick up pieces if the game is over
            if (game.game_over()) return false;

            // Only pick up pieces for the player whose turn it is
            if ((game.turn() === 'w' && piece.search(/^b/) !== -1) ||
                (game.turn() === 'b' && piece.search(/^w/) !== -1)) {
                return false;
            }
        }

        function onDrop (source, target) {
            // See if the move is legal
            var move = game.move({
                from: source,
                to: target,
                promotion: 'q' // NOTE: Always promote to a queen for example simplicity
            });

            // Illegal move
            if (move === null) return 'snapback';

            updateStatus();
        }

        function updateStatus () {
            var status = '';
            var moveColor = 'White';
            if (game.turn() === 'b') { moveColor = 'Black'; }

            if (game.in_checkmate()) {
                status = 'Game over, ' + moveColor + ' is in checkmate.';
            } else if (game.in_draw()) {
                status = 'Game over, drawn position';
            } else {
                status = moveColor + ' to move';
                if (game.in_check()) { status += ', ' + moveColor + ' is in check'; }
            }

            $status.html(status);
        }

        var config = {
            draggable: true,
            position: 'start',
            onDragStart: onDragStart,
            onDrop: onDrop,
            pieceTheme: 'https://chessboardjs.com/img/chesspieces/wikipedia/{piece}.png'
        };

        board = Chessboard('myBoard', config);
        
        // Make board responsive on window resize
        $(window).resize(board.resize);

        function resetGame() {
            game.reset();
            board.start();
            updateStatus();
        }

        updateStatus();
    </script>
</body>
</html>

