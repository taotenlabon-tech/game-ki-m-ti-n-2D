<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chess Master - Auto Play Edition</title>
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
            padding: 28px;
            border-radius: 16px;
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.6);
            max-width: 1000px;
            width: 100%;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        .shake {
            animation: shakeAnim 0.3s ease-in-out;
        }

        @keyframes shakeAnim {
            0% { transform: translate(0, 0); }
            20% { transform: translate(-8px, 5px); }
            40% { transform: translate(8px, -5px); }
            60% { transform: translate(-5px, -5px); }
            80% { transform: translate(5px, 5px); }
            100% { transform: translate(0, 0); }
        }

        /* BÀN CỜ VUA KÍCH THƯỚC SIÊU TO */
        .board-container {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .board {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            width: 520px;
            height: 520px;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.6);
            border: 4px solid #363431;
        }

        @media (max-width: 600px) {
            .board {
                width: 340px;
                height: 340px;
            }
        }

        .square {
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 3.4rem;
            cursor: pointer;
            position: relative;
            transition: background 0.15s;
        }

        @media (max-width: 600px) {
            .square { font-size: 2.2rem; }
        }

        .square.light { background-color: #eeeed2; }
        .square.dark { background-color: #769656; }

        .square.selected { background-color: #bbcb2b !important; }
        .square.last-move { background-color: rgba(205, 210, 106, 0.6) !important; }

        .square.valid-move::after {
            content: '';
            width: 20px;
            height: 20px;
            background-color: rgba(20, 85, 30, 0.5);
            border-radius: 50%;
            position: absolute;
        }

        .square.valid-capture {
            background-color: #d85346 !important;
        }

        .piece-w { color: #ffffff; text-shadow: 0 3px 5px rgba(0,0,0,0.8); }
        .piece-b { color: #262421; text-shadow: 0 1px 3px rgba(255,255,255,0.3); }

        /* KHU VỰC ĐIỀU KHIỂN BÊN PHẢI */
        .side-panel {
            flex: 1;
            min-width: 300px;
            display: flex;
            flex-direction: column;
            gap: 18px;
        }

        .stats-card {
            background: #1e1c18;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            border-bottom: 4px solid #629924;
        }

        .stats-card h1 {
            font-size: 2.4rem;
            color: #629924;
            font-weight: 700;
        }

        .stats-card p {
            font-size: 1rem;
            color: #8c8c8c;
            margin-top: 4px;
        }

        .shop-title {
            font-size: 1.1rem;
            color: #dedede;
            font-weight: 600;
            margin-bottom: 10px;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .upgrade-btn {
            background: #363431;
            border: none;
            color: #e0e0e0;
            padding: 14px 16px;
            border-radius: 8px;
            cursor: pointer;
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: background 0.15s, transform 0.1s;
            margin-bottom: 10px;
            width: 100%;
        }

        .upgrade-btn:hover:not(:disabled) {
            background: #45433f;
            transform: translateY(-2px);
        }

        .upgrade-btn:disabled {
            opacity: 0.4;
            cursor: not-allowed;
        }

        .upgrade-name { font-weight: 600; font-size: 1rem; }
        .upgrade-sub { font-size: 0.85rem; color: #999; }
        .badge {
            background: #629924;
            color: #fff;
            padding: 4px 10px;
            border-radius: 12px;
            font-size: 0.85rem;
            font-weight: bold;
        }

        .puzzle-btn {
            background: #d68910;
            color: #fff;
            border: none;
            padding: 12px;
            border-radius: 8px;
            font-weight: bold;
            font-size: 1rem;
            cursor: pointer;
            transition: background 0.15s;
            margin-top: 5px;
            width: 100%;
        }

        .puzzle-btn:hover { background: #b9770e; }

        /* HIỆU ỨNG CHIỮ BAY & BRILLIANT */
        .floating-text {
            position: absolute;
            font-weight: bold;
            font-size: 1.5rem;
            color: #629924;
            pointer-events: none;
            animation: floatUp 0.8s ease-out forwards;
            z-index: 50;
        }

        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.8); }
            50% { transform: translateY(-25px) scale(1.1); }
            100% { opacity: 0; transform: translateY(-50px) scale(1); }
        }

        .brilliant-popup {
            position: absolute;
            font-weight: 900;
            font-size: 2.2rem;
            color: #2ed573;
            text-shadow: 0 0 20px #2ed573, 0 0 40px #2ed573;
            pointer-events: none;
            display: flex;
            align-items: center;
            gap: 8px;
            animation: brilliantAnim 1s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
            white-space: nowrap;
            z-index: 60;
        }

        .brilliant-badge {
            background: #2ed573;
            color: #fff;
            border-radius: 50%;
            width: 48px;
            height: 48px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.6rem;
            font-weight: bold;
            box-shadow: 0 0 20px #2ed573;
        }

        @keyframes brilliantAnim {
            0% { opacity: 0; transform: scale(0.2) translateY(0); }
            40% { opacity: 1; transform: scale(1.3) translateY(-15px); }
            100% { opacity: 0; transform: scale(1) translateY(-75px); }
        }

        /* MODAL CÂU ĐỐ */
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
            padding: 24px;
            border-radius: 12px;
            max-width: 400px;
            width: 90%;
            text-align: center;
        }

        .modal-box h3 { color: #629924; margin-bottom: 12px; }

        .mini-board {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            width: 220px; height: 220px;
            margin: 10px auto;
            border-radius: 4px;
            overflow: hidden;
        }

        .puzzle-opt {
            background: #363431;
            color: #fff;
            border: none;
            padding: 12px;
            margin-top: 8px;
            border-radius: 6px;
            width: 100%;
            cursor: pointer;
            font-weight: 600;
        }

        .puzzle-opt:hover { background: #45433f; }
    </style>
</head>
<body>

    <div class="game-wrapper" id="gameWrapper">
        <!-- Bàn cờ chính -->
        <div class="board-container">
            <div class="board" id="mainBoard"></div>
        </div>

        <!-- Bảng điều khiển -->
        <div class="side-panel">
            <div class="stats-card">
                <h1 id="elo">Elo: 400</h1>
                <p>Tốc độ tích lũy: <span id="eps">0</span> Elo/s</p>
            </div>

            <div>
                <div class="shop-title">Phòng Huấn Luyện</div>

                <button class="upgrade-btn" id="btnClickUpgrade">
                    <div>
                        <div class="upgrade-name">Luyện Nước Đi</div>
                        <div class="upgrade-sub">Giá: <span id="costClick">15</span> Elo (+1/nước)</div>
                    </div>
                    <span class="badge" id="lvlClick">Lv 1</span>
                </button>

                <button class="upgrade-btn" id="btnAuto1">
                    <div>
                        <div class="upgrade-name">Tự Động Đấu Cờ</div>
                        <div class="upgrade-sub">Giá: <span id="costAuto1">30</span> Elo (+1 Elo/s)</div>
                    </div>
                    <span class="badge" id="countAuto1">0</span>
                </button>

                <button class="upgrade-btn" id="btnAuto2">
                    <div>
                        <div class="upgrade-name">Động Cơ Stockfish</div>
                        <div class="upgrade-sub">Giá: <span id="costAuto2">150</span> Elo (+10 Elo/s)</div>
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
            <p id="puzzleText" style="font-size: 0.95rem; margin: 8px 0; color: #aaa;"></p>
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

        const saved = localStorage.getItem("chessLichessAutoSave");
        if (saved) gameData = Object.assign(gameData, JSON.parse(saved));

        const symbols = {
            'K': '♔', 'Q': '♕', 'R': '♖', 'B': '♗', 'N': '♘', 'P': '♙',
            'k': '♚', 'q': '♛', 'r': '♜', 'b': '♝', 'n': '♞', 'p': '♟'
        };

        let initialBoard = [
            ['r', 'n', 'b', 'q', 'k', 'b', 'n', 'r'],
            ['p', 'p', 'p', 'p', 'p', 'p', 'p', 'p'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['.', '.', '.', '.', '.', '.', '.', '.'],
            ['P', 'P', 'P', 'P', 'P', 'P', 'P', 'P'],
            ['R', 'N', 'B', 'Q', 'K', 'B', 'N', 'R']
        ];

        let boardState = JSON.parse(JSON.stringify(initialBoard));
        let selected = null;
        let validMoves = [];
        let lastMove = null;

        const mainBoardEl = document.getElementById("mainBoard");
        const gameWrapper = document.getElementById("gameWrapper");

        // KHỞI TẠO ÂM THANH
        let audioCtx = null;
        function getAudioContext() {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            if (audioCtx.state === 'suspended') audioCtx.resume();
            return audioCtx;
        }

        function playNormalSound() {
            try {
                const ctx = getAudioContext();
                const osc = ctx.createOscillator();
                const gain = ctx.createGain();
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(160, ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(30, ctx.currentTime + 0.05);
                gain.gain.setValueAtTime(0.3, ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.05);
                osc.connect(gain);
                gain.connect(ctx.destination);
                osc.start();
                osc.stop(ctx.currentTime + 0.05);
            } catch (e) {}
        }

        function playBrilliantSound() {
            try {
                const ctx = getAudioContext();
                const freqs = [523.25, 659.25, 783.99, 1046.50];
                freqs.forEach((freq, idx) => {
                    setTimeout(() => {
                        const osc = ctx.createOscillator();
                        const gain = ctx.createGain();
                        osc.type = idx === 3 ? 'triangle' : 'sine';
                        osc.frequency.setValueAtTime(freq, ctx.currentTime);
                        gain.gain.setValueAtTime(0.25, ctx.currentTime);
                        gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.6);
                        osc.connect(gain);
                        gain.connect(ctx.destination);
                        osc.start();
                        osc.stop(ctx.currentTime + 0.6);
                    }, idx * 45);
                });
            } catch (e) {}
        }

        function renderBoard() {
            mainBoardEl.innerHTML = "";
            for (let r = 0; r < 8; r++) {
                for (let c = 0; c < 8; c++) {
                    const sq = document.createElement("div");
                    sq.className = "square " + ((r + c) % 2 === 0 ? "light" : "dark");

                    if (selected && selected.r === r && selected.c === c) sq.classList.add("selected");
                    if (lastMove && ((lastMove.fromR === r && lastMove.fromC === c) || (lastMove.toR === r && lastMove.toC === c))) {
                        sq.classList.add("last-move");
                    }

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

        function makeMove(fromR, fromC, toR, toC, e) {
            const isCapture = boardState[toR][toC] !== '.';
            boardState[toR][toC] = boardState[fromR][fromC];
            boardState[fromR][fromC] = '.';
            lastMove = { fromR, fromC, toR, toC };

            // 15% CƠ HỘI BRILLIANT MOVE
            const isBrilliant = Math.random() < 0.15;
            const multiplier = isBrilliant ? 5 : (isCapture ? 3 : 1);
            const gain = gameData.clickPower * multiplier;

            gameData.elo += gain;

            const rect = mainBoardEl.getBoundingClientRect();
            const posX = e ? e.clientX : rect.left + (toC * (rect.width / 8)) + 30;
            const posY = e ? e.clientY : rect.top + (toR * (rect.height / 8)) + 30;

            if (isBrilliant) {
                playBrilliantSound();
                showBrilliantPopup(posX, posY, gain);
            } else {
                playNormalSound();
                showFloatText(posX, posY, "+" + gain + " Elo");
            }

            selected = null;
            validMoves = [];
            renderBoard();
            updateUI();
        }

        function handleClick(r, c, e) {
            const p = boardState[r][c];

            if (selected && validMoves.some(m => m.r === r && m.c === c)) {
                makeMove(selected.r, selected.c, r, c, e);
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

        // TỰ ĐỘNG DI CHUYỂN QUÂN CỜ
        function autoPlayMove() {
            let allPieces = [];
            for (let r = 0; r < 8; r++) {
                for (let c = 0; c < 8; c++) {
                    if (boardState[r][c] !== '.') {
                        const moves = getMoves(r, c);
                        if (moves.length > 0) {
                            allPieces.push({ r, c, moves });
                        }
                    }
                }
            }

            if (allPieces.length === 0) {
                // Reset bàn cờ nếu hết nước
                boardState = JSON.parse(JSON.stringify(initialBoard));
                renderBoard();
                return;
            }

            const randomPiece = allPieces[Math.floor(Math.random() * allPieces.length)];
            const randomMove = randomPiece.moves[Math.floor(Math.random() * randomPiece.moves.length)];

            makeMove(randomPiece.r, randomPiece.c, randomMove.r, randomMove.c, null);
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

        function showFloatText(x, y, txt) {
            const el = document.createElement("div");
            el.className = "floating-text";
            el.innerText = txt;
            el.style.left = (x - 20) + "px";
            el.style.top = (y - 20) + "px";
            document.body.appendChild(el);
            setTimeout(() => el.remove(), 700);
        }

        function showBrilliantPopup(x, y, gain) {
            const container = document.createElement("div");
            container.className = "brilliant-popup";
            container.innerHTML = `<div class="brilliant-badge">!!</div> Brilliant +${gain} Elo!`;
            container.style.left = (x - 60) + "px";
            container.style.top = (y - 30) + "px";
            document.body.appendChild(container);

            gameWrapper.classList.remove("shake");
            void gameWrapper.offsetWidth;
            gameWrapper.classList.add("shake");

            setTimeout(() => container.remove(), 1000);
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

        // BỎ BẤM NÂNG CẤP SHOP
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

        // CÂU ĐỐ THỬ THÁCH
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
                        playBrilliantSound();
                        alert("🎉 Chính xác! + " + p.reward + " Elo");
                    } else alert("❌ Sai rồi!");
                    puzzleModal.classList.remove("active");
                    updateUI();
                };
                opts.appendChild(btn);
            });

            puzzleModal.classList.add("active");
        };

        // LOOP TỰ ĐỘNG ĐẤU CỜ MỖI 1.5 GIÂY (MẠNH TỰ ĐỘNG KHI CÓ MÁY HOẶC BẤM)
        setInterval(() => {
            if (gameData.auto1Count > 0 || gameData.auto2Count > 0) {
                autoPlayMove();
            }
        }, 1500);

        setInterval(() => {
            gameData.elo += gameData.eps;
            updateUI();
        }, 1000);

        setInterval(() => {
            localStorage.setItem("chessLichessAutoSave", JSON.stringify(gameData));
        }, 5000);

        renderBoard();
        updateUI();
    </script>
</body>
</html>
