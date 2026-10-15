<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chess Master - Lichess Style Edition</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }

        body {
            background-color: #161512;
            color: #bababa;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .game-wrapper {
            display: flex;
            flex-wrap: wrap;
            gap: 30px;
            background: #262421;
            padding: 24px;
            border-radius: 12px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            max-width: 820px;
            width: 100%;
            align-items: center;
            justify-content: center;
        }

        /* KHU VỰC BÀN CỜ VUA BAN ĐẦU */
        .board-container {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .board {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            width: 360px;
            height: 360px;
            border-radius: 4px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.4);
        }

        .square {
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 2.5rem;
            cursor: pointer;
            position: relative;
            transition: background 0.1s;
        }

        .square.light { background-color: #eeeed2; }
        .square.dark { background-color: #769656; }

        .square.selected { background-color: #bbcb2b !important; }

        /* Gợi ý nước đi nhẹ nhàng */
        .square.valid-move::after {
            content: '';
            width: 16px;
            height: 16px;
            background-color: rgba(20, 85, 30, 0.5);
            border-radius: 50%;
            position: absolute;
        }

        .square.valid-capture {
            background-color: #d85346 !important;
        }

        .piece-w { color: #ffffff; text-shadow: 0 2px 3px rgba(0,0,0,0.8); }
        .piece-b { color: #262421; text-shadow: 0 1px 2px rgba(255,255,255,0.2); }

        /* KHU VỰC BẢNG ĐIỂM & NÂNG CẤP */
        .side-panel {
            flex: 1;
            min-width: 280px;
            display: flex;
            flex-direction: column;
            gap: 16px;
        }

        .stats-card {
            background: #1e1c18;
            padding: 18px;
            border-radius: 8px;
            text-align: center;
            border-bottom: 3px solid #629924;
        }

        .stats-card h1 {
            font-size: 2.2rem;
            color: #629924;
            font-weight: 700;
        }

        .stats-card p {
            font-size: 0.95rem;
            color: #8c8c8c;
            margin-top: 4px;
        }

        .shop-title {
            font-size: 1.1rem;
            color: #dedede;
            font-weight: 600;
            margin-bottom: 8px;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .upgrade-btn {
            background: #363431;
            border: none;
            color: #e0e0e0;
            padding: 12px 14px;
            border-radius: 6px;
            cursor: pointer;
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: background 0.15s, transform 0.1s;
            margin-bottom: 8px;
        }

        .upgrade-btn:hover:not(:disabled) {
            background: #45433f;
            transform: translateY(-1px);
        }

        .upgrade-btn:disabled {
            opacity: 0.4;
            cursor: not-allowed;
        }

        .upgrade-name { font-weight: 600; font-size: 0.95rem; }
        .upgrade-sub { font-size: 0.8rem; color: #999; }
        .badge {
            background: #629924;
            color: #fff;
            padding: 3px 8px;
            border-radius: 12px;
            font-size: 0.8rem;
            font-weight: bold;
        }

        .puzzle-btn {
            background: #d68910;
            color: #fff;
            border: none;
            padding: 12px;
            border-radius: 6px;
            font-weight: bold;
            font-size: 0.95rem;
            cursor: pointer;
            transition: background 0.15s;
            margin-top: 5px;
            width: 100%;
        }

        .puzzle-btn:hover { background: #b9770e; }

        /* CHIỮ BAY HIỆU ỨNG */
        .floating-text {
            position: absolute;
            font-weight: bold;
            font-size: 1.3rem;
            color: #629924;
            pointer-events: none;
            animation: floatUp 0.7s ease-out forwards;
            z-index: 50;
        }

        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0); }
            100% { opacity: 0; transform: translateY(-40px); }
        }

        /* MODAL CÂU ĐỐ TỐI GIẢN */
        .modal-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.75);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 100;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.2s;
        }

        .modal-overlay.active { opacity: 1; pointer-events: auto; }

        .modal-box {
            background: #262421;
            padding: 20px;
            border-radius: 8px;
            max-width: 380px;
            width: 90%;
            text-align: center;
        }

        .modal-box h3 { color: #629924; margin-bottom: 12px; }

        .mini-board {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            width: 200px; height: 200px;
            margin: 10px auto;
            border-radius: 4px;
            overflow: hidden;
        }

        .puzzle-opt {
            background: #363431;
            color: #fff;
            border: none;
            padding: 10px;
            margin-top: 8px;
            border-radius: 4px;
            width: 100%;
            cursor: pointer;
            font-weight: 600;
        }

        .puzzle-opt:hover { background: #45433f; }
    </style>
</head>
<body>

    <div class="game-wrapper">
        <!-- Bàn cờ chính -->
        <div class="board-container">
            <div class="board" id="mainBoard"></div>
        </div>

        <!-- Bảng điều khiển side panel -->
        <div class="side-panel">
            <div class="stats-card">
                <h1 id="elo">Elo: 400</h1>
                <p>Tốc độ tích lũy: <span id="eps">0</span> Elo/s</p>
            </div>

            <div>
                <div class="shop-title">Huấn luyện & Nâng cấp</div>

                <button class="upgrade-btn" id="btnClickUpgrade">
                    <div>
                        <div class="upgrade-name">Luyện Nước Đi</div>
                        <div class="upgrade-sub">Giá: <span id="costClick">15</span> Elo (+1/nước)</div>
                    </div>
                    <span class="badge" id="lvlClick">Lv 1</span>
                </button>

                <button class="upgrade-btn" id="btnAuto1">
                    <div>
                        <div class="upgrade-name">Học Viên Giải Bài</div>
                        <div class="upgrade-sub">Giá: <span id="costAuto1">30</span> Elo (+1/s)</div>
                    </div>
                    <span class="badge" id="countAuto1">0</span>
                </button>

                <button class="upgrade-btn" id="btnAuto2">
                    <div>
                        <div class="upgrade-name">Động Cơ Stockfish</div>
                        <div class="upgrade-sub">Giá: <span id="costAuto2">150</span> Elo (+10/s)</div>
                    </div>
                    <span class="badge" id="countAuto2">0</span>
                </button>

                <button class="puzzle-btn" id="btnOpenPuzzle">🎯 Thử Thách Chiến Thuật</button>
            </div>
        </div>
    </div>

    <!-- MODAL PUZZLE -->
    <div class="modal-overlay" id="puzzleModal">
        <div class="modal-box">
            <h3>⚡ Thử Thách Cờ Vua</h3>
            <div class="mini-board" id="miniBoard"></div>
            <p id="puzzleText" style="font-size: 0.9rem; margin: 8px 0; color: #aaa;"></p>
            <div id="puzzleOptions"></div>
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
            eps: 0
        };

        const saved = localStorage.getItem("chessLichessSave");
        if (saved) gameData = Object.assign(gameData, JSON.parse(saved));

        const symbols = {
            'K': '♔', 'Q': '♕', 'R': '♖', 'B': '♗', 'N': '♘', 'P': '♙',
            'k': '♚', 'q': '♛', 'r': '♜', 'b': '♝', 'n': '♞', 'p': '♟'
        };

        let boardState = [
            ['r', 'n', 'b', 'q', 'k', 'b', 'n', 'r'],
            ['p', 'p', 'p', 'p', 'p', 'p', 'p', 'p'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['P', 'P', 'P', 'P', 'P', 'P', 'P', 'P'],
            ['R', 'N', 'B', 'Q', 'K', 'B', 'N', 'R']
        ];

        let selected = null;
        let validMoves = [];

        const mainBoardEl = document.getElementById("mainBoard");

        function renderBoard() {
            mainBoardEl.innerHTML = "";
            for (let r = 0; r < 8; r++) {
                for (let c = 0; c < 8; c++) {
                    const sq = document.createElement("div");
                    sq.className = "square " + ((r + c) % 2 === 0 ? "light" : "dark");

                    if (selected && selected.r === r && selected.c === c) sq.classList.add("selected");

                    const isMove = validMoves.some(m => m.r === r && m.c === c);
                    if (isMove) {
                        if (boardState[r][c] !== '.') sq.classList.add("valid-capture");
                        else sq.classList.add("valid-move");
                    }

                    const p = boardState[r][c];
                    if (p !== '.') {
                        const isWhite = p === p.toUpperCase();
                        sq.innerHTML = `<span class="${isWhite ? 'piece-w' : 'piece-b'}">${symbols[p]}</span>`;
                    }

                    sq.addEventListener("click", (e) => handleClick(r, c, e));
                    mainBoardEl.appendChild(sq);
                }
            }
        }

        function handleClick(r, c, e) {
            const p = boardState[r][c];

            if (selected && validMoves.some(m => m.r === r && m.c === c)) {
                // Di chuyển quân
                const isCapture = boardState[r][c] !== '.';
                boardState[r][c] = boardState[selected.r][selected.c];
                boardState[selected.r][selected.c] = '.';

                const gain = gameData.clickPower * (isCapture ? 3 : 1);
                gameData.elo += gain;

                playSound();
                showFloatText(e.clientX, e.clientY, "+" + gain + " Elo");

                selected = null;
                validMoves = [];
                renderBoard();
                updateUI();
                return;
            }

            if (p !== '.') {
                selected = { r, c };
                validMoves = getMoves(r, c);
            } else {
                selected = null;
                validMoves = [];
            }
            renderBoard();
        }

        function getMoves(r, c) {
            const moves = [];
            const p = boardState[r][c];
            const isWhite = p === p.toUpperCase();
            const dirs = [[-1,0],[1,0],[0,-1],[0,1],[-1,-1],[-1,1],[1,-1],[1,1]];

            dirs.forEach(([dr, dc]) => {
                const nr = r + dr, nc = c + dc;
                if (nr >= 0 && nr < 8 && nc >= 0 && nc < 8) {
                    const t = boardState[nr][nc];
                    if (t === '.' || (isWhite !== (t === t.toUpperCase()))) {
                        moves.push({ r: nr, c: nc });
                    }
                }
            });
            return moves;
        }

        function playSound() {
            try {
                const ctx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = ctx.createOscillator();
                const gain = ctx.createGain();
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(150, ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(30, ctx.currentTime + 0.05);
                gain.gain.setValueAtTime(0.3, ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.05);
                osc.connect(gain);
                gain.connect(ctx.destination);
                osc.start();
                osc.stop(ctx.currentTime + 0.05);
            } catch (e) {}
        }

        function showFloatText(x, y, txt) {
            const el = document.createElement("div");
            el.className = "floating-text";
            el.innerText = txt;
            el.style.left = (x - 20) + "px";
            el.style.top = (y - 20) + "px";
            document.body.appendChild(el);
            setTimeout(() => el.remove(), 700);
        }

        function updateUI() {
            gameData.eps = (gameData.auto1Count * 1) + (gameData.auto2Count * 10);
            document.getElementById("elo").innerText = "Elo: " + Math.floor(gameData.elo).toLocaleString();
            document.getElementById("eps").innerText = gameData.eps.toLocaleString();

            document.getElementById("costClick").innerText = gameData.clickCost.toLocaleString();
            document.getElementById("lvlClick").innerText = "Lv " + gameData.clickLvl;

            document.getElementById("costAuto1").innerText = gameData.auto1Cost.toLocaleString();
            document.getElementById("countAuto1").innerText = gameData.auto1Count;

            document.getElementById("costAuto2").innerText = gameData.auto2Cost.toLocaleString();
            document.getElementById("countAuto2").innerText = gameData.auto2Count;

            document.getElementById("btnClickUpgrade").disabled = gameData.elo < gameData.clickCost;
            document.getElementById("btnAuto1").disabled = gameData.elo < gameData.auto1Cost;
            document.getElementById("btnAuto2").disabled = gameData.elo < gameData.auto2Cost;
        }

        // SHOP EVENT
        document.getElementById("btnClickUpgrade").onclick = () => {
            if (gameData.elo >= gameData.clickCost) {
                gameData.elo -= gameData.clickCost;
                gameData.clickPower += 1;
                gameData.clickLvl += 1;
                gameData.clickCost = Math.floor(gameData.clickCost * 1.5);
                updateUI();
            }
        };

        document.getElementById("btnAuto1").onclick = () => {
            if (gameData.elo >= gameData.auto1Cost) {
                gameData.elo -= gameData.auto1Cost;
                gameData.auto1Count += 1;
                gameData.auto1Cost = Math.floor(gameData.auto1Cost * 1.3);
                updateUI();
            }
        };

        document.getElementById("btnAuto2").onclick = () => {
            if (gameData.elo >= gameData.auto2Cost) {
                gameData.elo -= gameData.auto2Cost;
                gameData.auto2Count += 1;
                gameData.auto2Cost = Math.floor(gameData.auto2Cost * 1.4);
                updateUI();
            }
        };

        // PUZZLE SYSTEM
        const puzzleList = [
            {
                q: "Chiếu hết 1 nước (Mate in 1): Nước đi nào kết liễu trận đấu?",
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
            }
        ];

        const puzzleModal = document.getElementById("puzzleModal");
        document.getElementById("btnOpenPuzzle").onclick = () => {
            const p = puzzleList[0];
            document.getElementById("puzzleText").innerText = p.q;

            const mb = document.getElementById("miniBoard");
            mb.innerHTML = "";
            for (let r = 0; r < 8; r++) {
                for (let c = 0; c < 8; c++) {
                    const sq = document.createElement("div");
                    sq.className = "square " + ((r + c) % 2 === 0 ? "light" : "dark");
                    const ch = p.board[r][c];
                    if (ch !== '.') {
                        const isW = ch === ch.toUpperCase();
                        sq.innerHTML = `<span class="${isW ? 'piece-w' : 'piece-b'}">${symbols[ch]}</span>`;
                    }
                    mb.appendChild(sq);
                }
            }

            const opts = document.getElementById("puzzleOptions");
            opts.innerHTML = "";
            p.options.forEach((opt, idx) => {
                const btn = document.createElement("button");
                btn.className = "puzzle-opt";
                btn.innerText = opt;
                btn.onclick = () => {
                    if (idx === p.correct) {
                        gameData.elo += p.reward;
                        alert("🎉 Chính xác! + " + p.reward + " Elo");
                    } else alert("❌ Sai rồi!");
                    puzzleModal.classList.remove("active");
                    updateUI();
                };
                opts.appendChild(btn);
            });

            puzzleModal.classList.add("active");
        };

        setInterval(() => {
            gameData.elo += gameData.eps;
            updateUI();
        }, 1000);

        setInterval(() => {
            localStorage.setItem("chessLichessSave", JSON.stringify(gameData));
        }, 5000);

        renderBoard();
        updateUI();
    </script>
</body>
</html>
