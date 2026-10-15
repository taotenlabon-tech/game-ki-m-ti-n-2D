<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chess Master Clicker - Đấu Cờ & Cày Elo</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
        }

        :root {
            --bg-gradient: radial-gradient(circle at 50% 30%, rgba(41, 128, 185, 0.15) 0%, transparent 60%), radial-gradient(circle at 80% 80%, rgba(241, 196, 15, 0.08) 0%, transparent 50%);
            --primary-color: #f1c40f;
            --accent-color: #00d2d3;
            --container-bg: rgba(22, 27, 34, 0.88);
            --border-color: rgba(241, 196, 15, 0.2);
        }

        body.theme-emerald {
            --bg-gradient: radial-gradient(circle at 50% 30%, rgba(16, 172, 132, 0.2) 0%, transparent 60%);
            --primary-color: #10ac84;
            --accent-color: #55efc4;
            --border-color: rgba(16, 172, 132, 0.3);
        }

        body.theme-ruby {
            --bg-gradient: radial-gradient(circle at 50% 30%, rgba(235, 77, 75, 0.2) 0%, transparent 60%);
            --primary-color: #ff4757;
            --accent-color: #ff6b81;
            --border-color: rgba(255, 71, 87, 0.3);
        }

        body {
            background-color: #0d1117;
            background-image: var(--bg-gradient),
                url("data:image/svg+xml,%3Csvg width='80' height='80' viewBox='0 0 80 80' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='%23f1c40f' fill-opacity='0.02' fill-rule='evenodd'%3E%3Cpath d='M0 0h40v40H0V0zm40 40h40v40H40V40zm0-40h40v40H40V0zM0 40h40v40H0V40z'/%3E%3C/g%3E%3C/svg%3E");
            color: #f5f6fa;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
            position: relative;
        }

        .bg-symbols-container {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            overflow: hidden;
            pointer-events: none;
            z-index: 0;
        }

        .bg-symbol {
            position: absolute;
            font-size: 3rem;
            color: rgba(255, 255, 255, 0.03);
            animation: floatUpBg linear infinite;
        }

        @keyframes floatUpBg {
            0% { transform: translateY(105vh) rotate(0deg); opacity: 0; }
            20% { opacity: 0.08; }
            80% { opacity: 0.08; }
            100% { transform: translateY(-10vh) rotate(360deg); opacity: 0; }
        }

        .game-container {
            display: flex;
            flex-wrap: wrap;
            gap: 25px;
            background: var(--container-bg);
            backdrop-filter: blur(12px);
            padding: 25px;
            border-radius: 24px;
            box-shadow: 0 25px 60px rgba(0, 0, 0, 0.85), 0 0 30px var(--border-color);
            max-width: 900px;
            width: 95%;
            border: 1px solid var(--border-color);
            position: relative;
            z-index: 10;
        }

        .top-controls {
            position: absolute;
            top: 15px; right: 25px;
            display: flex;
            gap: 10px;
            z-index: 20;
        }

        .theme-btn {
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            color: #fff;
            padding: 6px 12px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 0.85rem;
            transition: all 0.2s;
        }

        .theme-btn:hover { background: rgba(255, 255, 255, 0.2); }

        .shake { animation: shakeAnim 0.3s ease-in-out; }
        @keyframes shakeAnim {
            0% { transform: translate(0, 0); }
            20% { transform: translate(-8px, 5px); }
            40% { transform: translate(8px, -5px); }
            60% { transform: translate(-5px, -5px); }
            80% { transform: translate(5px, 5px); }
            100% { transform: translate(0, 0); }
        }

        .main-section {
            flex: 1.2;
            min-width: 300px;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }

        .score-board { margin-bottom: 12px; width: 100%; }
        .score-board h1 {
            font-size: 2.4rem;
            color: var(--primary-color);
            text-shadow: 0 0 15px var(--border-color);
            margin-bottom: 2px;
        }
        .score-board p { color: var(--accent-color); font-size: 1.05rem; font-weight: 600; }

        .progress-container {
            width: 100%;
            background: #0d1117;
            border-radius: 10px;
            height: 10px;
            margin-top: 8px;
            overflow: hidden;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        .progress-bar {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, var(--accent-color), var(--primary-color));
            box-shadow: 0 0 10px var(--accent-color);
            transition: width 0.3s ease;
        }

        /* BÀN CỜ VUA TƯƠNG TÁC 8x8 */
        .board-wrapper {
            position: relative;
            margin: 10px 0;
        }

        .interactive-board {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            width: 320px;
            height: 320px;
            border: 4px solid #b58863;
            border-radius: 8px;
            box-shadow: 0 12px 30px rgba(0,0,0,0.7);
            background: #b58863;
        }

        .square-main {
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 2.2rem;
            cursor: pointer;
            position: relative;
            transition: background-color 0.15s;
        }

        .square-main.light { background-color: #f0d9b5; }
        .square-main.dark { background-color: #b58863; }
        .square-main.selected { background-color: #769656 !important; }

        /* Chấm gợi ý ô có thể đi */
        .square-main.valid-move::after {
            content: '';
            width: 14px; height: 14px;
            background-color: rgba(46, 213, 115, 0.7);
            border-radius: 50%;
            position: absolute;
        }

        .square-main.valid-capture {
            background-color: #e74c3c !important;
        }

        .piece-w { color: #ffffff; text-shadow: 0 2px 4px #000; font-weight: bold; }
        .piece-b { color: #1e272e; text-shadow: 0 1px 3px rgba(255,255,255,0.4); font-weight: bold; }

        .puzzle-trigger-btn {
            background: linear-gradient(135deg, #ff9f43, #ee5253);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 12px;
            font-weight: bold;
            font-size: 0.95rem;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 5px 15px rgba(238, 82, 83, 0.4);
            margin-top: 5px;
        }

        .puzzle-trigger-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 20px rgba(238, 82, 83, 0.6);
        }

        .shop-section {
            flex: 1;
            min-width: 280px;
            background: rgba(33, 38, 45, 0.75);
            padding: 18px;
            border-radius: 18px;
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        .shop-section h2 {
            text-align: center;
            margin-bottom: 15px;
            color: var(--primary-color);
            font-size: 1.3rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            text-shadow: 0 0 10px var(--border-color);
        }

        .upgrade-btn {
            width: 100%;
            background: rgba(48, 54, 61, 0.85);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.06);
            padding: 12px 14px;
            margin-bottom: 10px;
            border-radius: 12px;
            cursor: pointer;
            text-align: left;
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: all 0.2s ease;
        }

        .upgrade-btn:hover:not(:disabled) {
            background: linear-gradient(135deg, #0984e3, #6c5ce7);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(9, 132, 227, 0.4);
        }

        .upgrade-btn:disabled {
            background: rgba(20, 24, 28, 0.6);
            color: #484f58;
            cursor: not-allowed;
        }

        .upgrade-info div:first-child { font-weight: bold; font-size: 0.95rem; }
        .upgrade-info div:last-child { font-size: 0.8rem; opacity: 0.8; }

        .badge {
            background: #ff4757;
            padding: 4px 10px;
            border-radius: 20px;
            font-size: 0.8rem;
            color: #fff;
            font-weight: bold;
        }

        .floating-text {
            position: absolute;
            font-weight: 800;
            font-size: 1.4rem;
            color: var(--accent-color);
            pointer-events: none;
            animation: floatUp 0.8s ease-out forwards;
            text-shadow: 0 0 10px var(--accent-color);
            z-index: 30;
        }

        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.8); }
            50% { transform: translateY(-25px) scale(1.1); }
            100% { opacity: 0; transform: translateY(-60px) scale(1); }
        }

        .brilliant-popup {
            position: absolute;
            font-weight: 900;
            font-size: 2rem;
            color: #2ed573;
            text-shadow: 0 0 20px #2ed573, 0 0 40px #2ed573;
            pointer-events: none;
            display: flex;
            align-items: center;
            gap: 6px;
            animation: brilliantAnim 1s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
            white-space: nowrap;
            z-index: 40;
        }

        .brilliant-badge {
            background: #2ed573;
            color: #fff;
            border-radius: 50%;
            width: 42px;
            height: 42px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.4rem;
            font-weight: bold;
            box-shadow: 0 0 20px #2ed573;
        }

        @keyframes brilliantAnim {
            0% { opacity: 0; transform: scale(0.2) translateY(0); }
            40% { opacity: 1; transform: scale(1.2) translateY(-15px); }
            100% { opacity: 0; transform: scale(1) translateY(-75px); }
        }

        /* MODAL POPUP */
        .modal-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.85);
            backdrop-filter: blur(8px);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 100;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.3s ease;
        }

        .modal-overlay.active { opacity: 1; pointer-events: auto; }

        .modal-content {
            background: #21262d;
            padding: 22px;
            border-radius: 24px;
            max-width: 440px;
            width: 90%;
            text-align: center;
            border: 2px solid var(--primary-color);
            box-shadow: 0 0 35px var(--border-color);
        }

        .modal-content h3 { color: var(--primary-color); font-size: 1.5rem; margin-bottom: 8px; }

        .mini-chessboard {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            width: 200px; height: 200px;
            margin: 10px auto;
            border: 3px solid #b58863;
            border-radius: 6px;
        }

        .square { display: flex; justify-content: center; align-items: center; font-size: 1.4rem; }
        .square.light { background-color: #f0d9b5; }
        .square.dark { background-color: #b58863; }

        .puzzle-desc {
            font-size: 0.9rem; color: #f5f6fa; margin-bottom: 12px;
            background: #0d1117; padding: 10px; border-radius: 10px;
            border-left: 4px solid #ff9f43; text-align: left;
        }

        .puzzle-options { display: flex; flex-direction: column; gap: 8px; margin-bottom: 10px; }
        .puzzle-opt-btn {
            background: #30363d; color: white; border: 1px solid rgba(255,255,255,0.1);
            padding: 10px; border-radius: 10px; font-size: 0.95rem; font-weight: 600; cursor: pointer;
        }
        .puzzle-opt-btn:hover { background: #0984e3; }
        .puzzle-timer { font-size: 0.85rem; color: #ff4757; font-weight: bold; }
    </style>
</head>
<body>

    <div class="bg-symbols-container" id="bgSymbols"></div>

    <div class="top-controls">
        <button class="theme-btn" onclick="setTheme('classic')">🟡 Cổ Điển</button>
        <button class="theme-btn" onclick="setTheme('emerald')">🟢 Lục Bảo</button>
        <button class="theme-btn" onclick="setTheme('ruby')">🔴 Hồng Ngọc</button>
    </div>

    <div class="game-container" id="gameContainer">
        <div class="main-section">
            <div class="score-board">
                <h1 id="elo">Elo: 400</h1>
                <p>Tốc độ: <span id="eps">0</span> Elo/giây</p>
                <div class="progress-container" title="Tiến trình lên cấp">
                    <div class="progress-bar" id="progressBar"></div>
                </div>
            </div>

            <!-- BÀN CỜ VUA CHÍNH TƯƠNG TÁC -->
            <div class="board-wrapper">
                <div class="interactive-board" id="mainBoard"></div>
            </div>

            <button class="puzzle-trigger-btn" id="btnOpenPuzzle">🎯 Giải Câu Đố Cờ Vua</button>
        </div>

        <div class="shop-section">
            <h2>Phòng Huấn Luyện</h2>

            <button class="upgrade-btn" id="btnClickUpgrade">
                <div class="upgrade-info">
                    <div>Luyện Nước Đi Chiến Thuật</div>
                    <div>Giá: <span id="costClick">15</span> Elo (+1/nước)</div>
                </div>
                <span class="badge" id="lvlClick">Lv 1</span>
            </button>

            <button class="upgrade-btn" id="btnAuto1">
                <div class="upgrade-info">
                    <div>Học Viên Giải Bài Tập</div>
                    <div>Giá: <span id="costAuto1">30</span> Elo (+1 Elo/s)</div>
                </div>
                <span class="badge" id="countAuto1">0</span>
            </button>

            <button class="upgrade-btn" id="btnAuto2">
                <div class="upgrade-info">
                    <div>Động Cơ AI (Stockfish)</div>
                    <div>Giá: <span id="costAuto2">150</span> Elo (+10 Elo/s)</div>
                </div>
                <span class="badge" id="countAuto2">0</span>
            </button>
        </div>
    </div>

    <div class="modal-overlay" id="puzzleModal">
        <div class="modal-content">
            <h3>⚡ Thử Thách Chiến Thuật</h3>
            <div class="mini-chessboard" id="miniBoard"></div>
            <div class="puzzle-desc" id="puzzleText">Đang tải câu hỏi...</div>
            <div class="puzzle-options" id="puzzleOptions"></div>
            <div class="puzzle-timer">Thời gian: <span id="timerCount">15</span>s</div>
        </div>
    </div>

    <script>
        let gameData = {
            elo: 400,
            clickPower: 1,
            clickCost: 15,
            clickLvl: 1,
            auto1Count: 0,
            auto1Cost: 30,
            auto2Count: 0,
            auto2Cost: 150,
            eps: 0,
            theme: 'classic'
        };

        const savedData = localStorage.getItem("chessClickerSave");
        if (savedData) {
            gameData = Object.assign(gameData, JSON.parse(savedData));
            setTheme(gameData.theme || 'classic', false);
        }

        const bgSymbolsContainer = document.getElementById("bgSymbols");
        const chessPiecesList = ['♙', '♘', '♗', '♖', '♕', '♔', '♟', '♞', '♝', '♜', '♛', '♚'];
        for (let i = 0; i < 15; i++) {
            const span = document.createElement("div");
            span.className = "bg-symbol";
            span.innerText = chessPiecesList[Math.floor(Math.random() * chessPiecesList.length)];
            span.style.left = Math.random() * 100 + "%";
            span.style.animationDuration = (Math.random() * 12 + 8) + "s";
            span.style.animationDelay = (Math.random() * 5) + "s";
            bgSymbolsContainer.appendChild(span);
        }

        function setTheme(themeName, save = true) {
            gameData.theme = themeName;
            document.body.className = "";
            if (themeName === 'emerald') document.body.classList.add('theme-emerald');
            if (themeName === 'ruby') document.body.classList.add('theme-ruby');
            if (save) localStorage.setItem("chessClickerSave", JSON.stringify(gameData));
        }

        const pieceSymbols = {
            'K': '♔', 'Q': '♕', 'R': '♖', 'B': '♗', 'N': '♘', 'P': '♙',
            'k': '♚', 'q': '♛', 'r': '♜', 'b': '♝', 'n': '♞', 'p': '♟'
        };

        // BÀN CỜ VUA CHÍNH LÚC BAN ĐẦU
        let currentBoard = [
            ['r', 'n', 'b', 'q', 'k', 'b', 'n', 'r'],
            ['p', 'p', 'p', 'p', 'p', 'p', 'p', 'p'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['P', 'P', 'P', 'P', 'P', 'P', 'P', 'P'],
            ['R', 'N', 'B', 'Q', 'K', 'B', 'N', 'R']
        ];

        let selectedSquare = null;
        let validMoves = [];

        const mainBoardEl = document.getElementById("mainBoard");

        function renderMainBoard() {
            mainBoardEl.innerHTML = "";
            for (let r = 0; r < 8; r++) {
                for (let c = 0; c < 8; c++) {
                    const square = document.createElement("div");
                    const isLight = (r + c) % 2 === 0;
                    square.className = "square-main " + (isLight ? "light" : "dark");

                    if (selectedSquare && selectedSquare.r === r && selectedSquare.c === c) {
                        square.classList.add("selected");
                    }

                    const isMove = validMoves.some(m => m.r === r && m.c === c);
                    if (isMove) {
                        if (currentBoard[r][c] !== '.') {
                            square.classList.add("valid-capture");
                        } else {
                            square.classList.add("valid-move");
                        }
                    }

                    const p = currentBoard[r][c];
                    if (p !== '.') {
                        const isWhite = p === p.toUpperCase();
                        square.innerHTML = `<span class="${isWhite ? 'piece-w' : 'piece-b'}">${pieceSymbols[p]}</span>`;
                    }

                    square.addEventListener("click", (e) => onSquareClick(r, c, e));
                    mainBoardEl.appendChild(square);
                }
            }
        }

        function onSquareClick(r, c, e) {
            const p = currentBoard[r][c];
            
            // Nếu bấm vào nước đi hợp lệ
            if (selectedSquare && validMoves.some(m => m.r === r && m.c === c)) {
                movePiece(selectedSquare.r, selectedSquare.c, r, c, e);
                selectedSquare = null;
                validMoves = [];
                renderMainBoard();
                return;
            }

            // Chọn quân cờ
            if (p !== '.') {
                selectedSquare = { r, c };
                validMoves = calculateSimpleValidMoves(r, c);
            } else {
                selectedSquare = null;
                validMoves = [];
            }
            renderMainBoard();
        }

        // Tính các nước đi cơ bản (tiến thẳng, ăn chéo, v.v.)
        function calculateSimpleValidMoves(r, c) {
            const moves = [];
            const piece = currentBoard[r][c];
            const isWhite = piece === piece.toUpperCase();

            const directions = [
                [-1, 0], [1, 0], [0, -1], [0, 1],
                [-1, -1], [-1, 1], [1, -1], [1, 1]
            ];

            // Cho phép di chuyển xung quanh 1 ô đơn giản để chơi vui vẻ mượt mà
            directions.forEach(([dr, dc]) => {
                const nr = r + dr;
                const nc = c + dc;
                if (nr >= 0 && nr < 8 && nc >= 0 && nc < 8) {
                    const target = currentBoard[nr][nc];
                    if (target === '.' || (isWhite !== (target === target.toUpperCase()))) {
                        moves.push({ r: nr, c: nc });
                    }
                }
            });
            return moves;
        }

        function movePiece(fromR, fromC, toR, toC, e) {
            const isCapture = currentBoard[toR][toC] !== '.';
            currentBoard[toR][toC] = currentBoard[fromR][fromC];
            currentBoard[fromR][fromC] = '.';

            const isBrilliant = Math.random() < 0.15;
            const multiplier = isBrilliant ? 5 : (isCapture ? 3 : 1);
            const earnedElo = gameData.clickPower * multiplier;

            gameData.elo += earnedElo;

            if (isBrilliant) {
                playBrilliantSound();
            } else {
                playNormalSound();
            }

            createClickEffect(e, isBrilliant, earnedElo);
            updateUI();
        }

        const puzzleList = [
            {
                q: "Chiếu hết 1 nước (Mate in 1): Hậu trắng kết liễu vua đen thế nào?",
                board: [
                    ['.', '.', '.', '.', 'k', '.', '.', '.'],
                    ['p', 'p', 'p', '.', '.', 'p', 'p', 'p'],
                    ['.', '.', '.', '.', '.', '.', '.', '.'],
                    ['.', '.', '.', 'Q', '.', '.', '.', '.'],
                    ['.', '.', '.', '.', '.', '.', '.', '.'],
                    ['.', '.', '.', '.', '.', '.', '.', '.'],
                    ['P', 'P', 'P', 'P', '.', 'P', 'P', 'P'],
                    ['.', '.', '.', '.', 'K', '.', '.', '.']
                ],
                options: ["Qxh7#", "Qd8+", "Qxf3"],
                correct: 0,
                reward: 250
            },
            {
                q: "Đòn Bắt Đôi (Fork): Mã trắng nhảy ô nào bắt đồng thời Vua và Xe đen?",
                board: [
                    ['r', '.', 'b', 'k', '.', 'b', 'n', 'r'],
                    ['p', 'p', 'p', '.', '.', 'p', 'p', 'p'],
                    ['.', '.', 'n', '.', '.', '.', '.', '.'],
                    ['.', '.', '.', 'p', 'P', '.', '.', '.'],
                    ['.', '.', 'N', '.', '.', '.', '.', '.'],
                    ['.', '.', '.', '.', '.', '.', '.', '.'],
                    ['P', 'P', 'P', 'P', '.', 'P', 'P', 'P'],
                    ['R', '.', 'B', 'Q', 'K', 'B', '.', 'R']
                ],
                options: ["Nf7+", "Nc3", "Nxd5"],
                correct: 0,
                reward: 300
            }
        ];

        let activePuzzle = null;
        let puzzleTimerInterval = null;
        let timeLeft = 15;

        let audioCtx = null;
        function getAudioContext() {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            if (audioCtx.state === 'suspended') audioCtx.resume();
            return audioCtx;
        }

        const eloEl = document.getElementById("elo");
        const epsEl = document.getElementById("eps");
        const gameContainer = document.getElementById("gameContainer");
        const progressBar = document.getElementById("progressBar");
        const puzzleModal = document.getElementById("puzzleModal");
        const miniBoard = document.getElementById("miniBoard");
        const puzzleText = document.getElementById("puzzleText");
        const puzzleOptions = document.getElementById("puzzleOptions");
        const timerCount = document.getElementById("timerCount");
        const btnOpenPuzzle = document.getElementById("btnOpenPuzzle");

        const btnClickUpgrade = document.getElementById("btnClickUpgrade");
        const costClickEl = document.getElementById("costClick");
        const lvlClickEl = document.getElementById("lvlClick");

        const btnAuto1 = document.getElementById("btnAuto1");
        const costAuto1El = document.getElementById("costAuto1");
        const countAuto1El = document.getElementById("countAuto1");

        const btnAuto2 = document.getElementById("btnAuto2");
        const costAuto2El = document.getElementById("costAuto2");
        const countAuto2El = document.getElementById("countAuto2");

        function playNormalSound() {
            try {
                const ctx = getAudioContext();
                const osc = ctx.createOscillator();
                const gainNode = ctx.createGain();
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(180, ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(40, ctx.currentTime + 0.05);
                gainNode.gain.setValueAtTime(0.3, ctx.currentTime);
                gainNode.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + 0.05);
                osc.connect(gainNode);
                gainNode.connect(ctx.destination);
                osc.start();
                osc.stop(ctx.currentTime + 0.05);
            } catch (e) {}
        }

        function playBrilliantSound() {
            try {
                const ctx = getAudioContext();
                const frequencies = [523.25, 659.25, 783.99, 1046.50];
                frequencies.forEach((freq, index) => {
                    setTimeout(() => {
                        const osc = ctx.createOscillator();
                        const gainNode = ctx.createGain();
                        osc.type = index === 3 ? 'triangle' : 'sine';
                        osc.frequency.setValueAtTime(freq, ctx.currentTime);
                        gainNode.gain.setValueAtTime(0.25, ctx.currentTime);
                        gainNode.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.6);
                        osc.connect(gainNode);
                        gainNode.connect(ctx.destination);
                        osc.start();
                        osc.stop(ctx.currentTime + 0.6);
                    }, index * 40);
                });
            } catch (e) {}
        }

        function updateUI() {
            gameData.eps = (gameData.auto1Count * 1) + (gameData.auto2Count * 10);
            eloEl.innerText = "Elo: " + Math.floor(gameData.elo).toLocaleString();
            epsEl.innerText = gameData.eps.toLocaleString();

            costClickEl.innerText = gameData.clickCost.toLocaleString();
            lvlClickEl.innerText = "Lv " + gameData.clickLvl;

            costAuto1El.innerText = gameData.auto1Cost.toLocaleString();
            countAuto1El.innerText = gameData.auto1Count;

            costAuto2El.innerText = gameData.auto2Cost.toLocaleString();
            countAuto2El.innerText = gameData.auto2Count;

            const progress = (gameData.elo % 2000) / 2000 * 100;
            progressBar.style.width = progress + "%";

            btnClickUpgrade.disabled = gameData.elo < gameData.clickCost;
            btnAuto1.disabled = gameData.elo < gameData.auto1Cost;
            btnAuto2.disabled = gameData.elo < gameData.auto2Cost;
        }

        function createClickEffect(e, isBrilliant, earnedElo) {
            const x = e.clientX - 30;
            const y = e.clientY - 30;

            if (isBrilliant) {
                const container = document.createElement("div");
                container.className = "brilliant-popup";
                container.innerHTML = `<div class="brilliant-badge">!!</div> Brilliant +${earnedElo} Elo!`;
                container.style.left = x + "px";
                container.style.top = y + "px";
                document.body.appendChild(container);

                gameContainer.classList.remove("shake");
                void gameContainer.offsetWidth;
                gameContainer.classList.add("shake");

                setTimeout(() => container.remove(), 1000);
            } else {
                const text = document.createElement("div");
                text.className = "floating-text";
                text.innerText = "+" + earnedElo + " Elo";
                text.style.left = x + "px";
                text.style.top = y + "px";
                document.body.appendChild(text);
                setTimeout(() => text.remove(), 800);
            }
        }

        function renderMiniBoard(boardData) {
            miniBoard.innerHTML = "";
            for (let r = 0; r < 8; r++) {
                for (let c = 0; c < 8; c++) {
                    const square = document.createElement("div");
                    const isLight = (r + c) % 2 === 0;
                    square.className = "square " + (isLight ? "light" : "dark");
                    const pieceChar = boardData[r][c];
                    if (pieceChar && pieceChar !== '.') {
                        const isWhite = pieceChar === pieceChar.toUpperCase();
                        square.innerHTML = `<span class="${isWhite ? 'piece-w' : 'piece-b'}">${pieceSymbols[pieceChar]}</span>`;
                    }
                    miniBoard.appendChild(square);
                }
            }
        }

        function openPuzzle() {
            if (puzzleModal.classList.contains("active")) return;
            activePuzzle = puzzleList[Math.floor(Math.random() * puzzleList.length)];
            puzzleText.innerText = activePuzzle.q;
            renderMiniBoard(activePuzzle.board);

            puzzleOptions.innerHTML = "";
            activePuzzle.options.forEach((opt, index) => {
                const btn = document.createElement("button");
                btn.className = "puzzle-opt-btn";
                btn.innerText = opt;
                btn.addEventListener("click", () => answerPuzzle(index));
                puzzleOptions.appendChild(btn);
            });

            timeLeft = 15;
            timerCount.innerText = timeLeft;
            puzzleModal.classList.add("active");

            clearInterval(puzzleTimerInterval);
            puzzleTimerInterval = setInterval(() => {
                timeLeft--;
                timerCount.innerText = timeLeft;
                if (timeLeft <= 0) {
                    clearInterval(puzzleTimerInterval);
                    closePuzzle();
                }
            }, 1000);
        }

        function answerPuzzle(selectedIndex) {
            clearInterval(puzzleTimerInterval);
            if (selectedIndex === activePuzzle.correct) {
                gameData.elo += activePuzzle.reward;
                playBrilliantSound();
                alert(`🎉 CHÍNH XÁC! Bạn nhận ngay +${activePuzzle.reward} Elo!`);
            } else {
                alert("❌ Trả lời sai mất rồi!");
            }
            closePuzzle();
            updateUI();
        }

        function closePuzzle() {
            puzzleModal.classList.remove("active");
            clearInterval(puzzleTimerInterval);
        }

        btnOpenPuzzle.addEventListener("click", openPuzzle);

        btnClickUpgrade.addEventListener("click", () => {
            if (gameData.elo >= gameData.clickCost) {
                gameData.elo -= gameData.clickCost;
                gameData.clickPower += 1;
                gameData.clickLvl += 1;
                gameData.clickCost = Math.floor(gameData.clickCost * 1.5);
                updateUI();
            }
        });

        btnAuto1.addEventListener("click", () => {
            if (gameData.elo >= gameData.auto1Cost) {
                gameData.elo -= gameData.auto1Cost;
                gameData.auto1Count += 1;
                gameData.auto1Cost = Math.floor(gameData.auto1Cost * 1.3);
                updateUI();
            }
        });

        btnAuto2.addEventListener("click", () => {
            if (gameData.elo >= gameData.auto2Cost) {
                gameData.elo -= gameData.auto2Cost;
                gameData.auto2Count += 1;
                gameData.auto2Cost = Math.floor(gameData.auto2Cost * 1.4);
                updateUI();
            }
        });

        setInterval(() => {
            gameData.elo += gameData.eps;
            updateUI();
        }, 1000);

        setInterval(() => {
            localStorage.setItem("chessClickerSave", JSON.stringify(gameData));
        }, 5000);

        renderMainBoard();
        updateUI();
    </script>
</body>
</html>
