<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chess Master Clicker - Cày Elo & Giải Puzzles</title>
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

        /* Quân cờ bay ở hậu cảnh */
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
            padding: 30px;
            border-radius: 24px;
            box-shadow: 0 25px 60px rgba(0, 0, 0, 0.85), 0 0 30px var(--border-color);
            max-width: 850px;
            width: 95%;
            border: 1px solid var(--border-color);
            transition: transform 0.1s ease;
            position: relative;
            z-index: 10;
        }

        /* Nút đổi Theme & Cài đặt góc trên */
        .top-controls {
            position: absolute;
            top: 20px; right: 25px;
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

        .theme-btn:hover {
            background: rgba(255, 255, 255, 0.2);
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

        .main-section {
            flex: 1;
            min-width: 280px;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        .score-board {
            margin-bottom: 15px;
            width: 100%;
        }

        .score-board h1 {
            font-size: 2.6rem;
            color: var(--primary-color);
            text-shadow: 0 0 15px var(--border-color);
            margin-bottom: 5px;
        }

        .score-board p {
            color: var(--accent-color);
            font-size: 1.1rem;
            font-weight: 600;
        }

        .progress-container {
            width: 100%;
            background: #0d1117;
            border-radius: 10px;
            height: 12px;
            margin-top: 10px;
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

        /* Nút bấm hình Quân Cờ Vua 3D Tiến Hóa & Hào Quang Lửa */
        .chess-btn-wrapper {
            position: relative;
            margin: 20px 0;
        }

        .chess-btn {
            width: 160px;
            height: 160px;
            background: radial-gradient(circle, #ffeaa7 0%, #fdcb6e 60%, #e1b12c 100%);
            border: 6px solid var(--primary-color);
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 4.8rem;
            cursor: pointer;
            box-shadow: 0 0 35px var(--border-color), inset 0 0 15px rgba(255, 255, 255, 0.8);
            transition: transform 0.1s cubic-bezier(0.175, 0.885, 0.32, 1.275), box-shadow 0.2s;
            animation: floatPulse 3s infinite ease-in-out;
            position: relative;
            z-index: 2;
        }

        @keyframes floatPulse {
            0%, 100% { transform: translateY(0) scale(1); }
            50% { transform: translateY(-6px) scale(1.02); }
        }

        /* Hiệu ứng hào quang lửa khi bấm liên tục (Combo Aura) */
        .chess-btn.fire-aura {
            box-shadow: 0 0 50px #ff4757, 0 0 90px #ff4757, inset 0 0 25px #ffffff !important;
            border-color: #ff4757 !important;
            animation: firePulse 0.4s infinite alternate !important;
        }

        @keyframes firePulse {
            0% { transform: scale(0.96); }
            100% { transform: scale(1.05); }
        }

        .chess-btn:active {
            transform: scale(0.9) !important;
        }

        .puzzle-trigger-btn {
            background: linear-gradient(135deg, #ff9f43, #ee5253);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 12px;
            font-weight: bold;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 5px 15px rgba(238, 82, 83, 0.4);
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .puzzle-trigger-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(238, 82, 83, 0.6);
        }

        .shop-section {
            flex: 1.1;
            min-width: 290px;
            background: rgba(33, 38, 45, 0.75);
            padding: 20px;
            border-radius: 18px;
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        .shop-section h2 {
            text-align: center;
            margin-bottom: 18px;
            color: var(--primary-color);
            font-size: 1.4rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            text-shadow: 0 0 10px var(--border-color);
        }

        .upgrade-btn {
            width: 100%;
            background: rgba(48, 54, 61, 0.85);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.06);
            padding: 14px 16px;
            margin-bottom: 12px;
            border-radius: 12px;
            cursor: pointer;
            text-align: left;
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: all 0.2s ease;
            position: relative;
            overflow: hidden;
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

        .upgrade-info div:first-child {
            font-weight: bold;
            font-size: 1rem;
            margin-bottom: 3px;
        }

        .upgrade-info div:last-child {
            font-size: 0.85rem;
            opacity: 0.8;
        }

        .badge {
            background: #ff4757;
            padding: 4px 10px;
            border-radius: 20px;
            font-size: 0.85rem;
            color: #fff;
            font-weight: bold;
            box-shadow: 0 2px 5px rgba(255, 71, 87, 0.4);
        }

        .particle {
            position: absolute;
            pointer-events: none;
            border-radius: 50%;
            animation: particleAnim 0.6s ease-out forwards;
            z-index: 10;
        }

        @keyframes particleAnim {
            0% { transform: translate(0, 0) scale(1); opacity: 1; }
            100% { transform: translate(var(--dx), var(--dy)) scale(0); opacity: 0; }
        }

        .floating-text {
            position: absolute;
            font-weight: 800;
            font-size: 1.5rem;
            color: var(--accent-color);
            pointer-events: none;
            animation: floatUp 0.8s ease-out forwards;
            text-shadow: 0 0 10px var(--accent-color);
            z-index: 20;
        }

        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.8); }
            50% { transform: translateY(-30px) scale(1.1); }
            100% { opacity: 0; transform: translateY(-70px) scale(1); }
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
            z-index: 30;
        }

        .brilliant-badge {
            background: #2ed573;
            color: #fff;
            border-radius: 50%;
            width: 46px;
            height: 46px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.6rem;
            font-weight: bold;
            box-shadow: 0 0 20px #2ed573;
        }

        @keyframes brilliantAnim {
            0% { opacity: 0; transform: scale(0.2) translateY(0); }
            40% { opacity: 1; transform: scale(1.3) translateY(-20px); }
            100% { opacity: 0; transform: scale(1) translateY(-90px); }
        }

        /* --- MODAL & BÀN CỜ VUA TRỰC QUAN --- */
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

        .modal-overlay.active {
            opacity: 1;
            pointer-events: auto;
        }

        .modal-content {
            background: #21262d;
            padding: 25px;
            border-radius: 24px;
            max-width: 460px;
            width: 90%;
            text-align: center;
            border: 2px solid var(--primary-color);
            box-shadow: 0 0 35px var(--border-color);
            transform: scale(0.8);
            transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }

        .modal-overlay.active .modal-content {
            transform: scale(1);
        }

        .modal-content h3 {
            color: var(--primary-color);
            font-size: 1.6rem;
            margin-bottom: 8px;
            text-shadow: 0 0 10px var(--border-color);
        }

        .mini-chessboard {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            width: 220px;
            height: 220px;
            margin: 12px auto;
            border: 3px solid #b58863;
            border-radius: 6px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.6);
        }

        .square {
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.6rem;
        }

        .square.light { background-color: #f0d9b5; }
        .square.dark { background-color: #b58863; }
        
        .piece-w { color: #ffffff; text-shadow: 0 1px 2px #000; font-weight: bold; }
        .piece-b { color: #1e272e; text-shadow: 0 1px 2px rgba(255,255,255,0.3); font-weight: bold; }

        .puzzle-desc {
            font-size: 0.95rem;
            color: #f5f6fa;
            margin-bottom: 15px;
            background: #0d1117;
            padding: 10px 14px;
            border-radius: 10px;
            border-left: 4px solid #ff9f43;
            text-align: left;
            line-height: 1.4;
        }

        .puzzle-options {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-bottom: 12px;
        }

        .puzzle-opt-btn {
            background: #30363d;
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.1);
            padding: 12px;
            border-radius: 10px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .puzzle-opt-btn:hover {
            background: #0984e3;
            border-color: #74b9ff;
            transform: translateY(-2px);
        }

        .puzzle-timer {
            font-size: 0.9rem;
            color: #ff4757;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <!-- Các quân cờ trôi nổi ở hình nền -->
    <div class="bg-symbols-container" id="bgSymbols"></div>

    <!-- Nút đổi chủ đề giao diện -->
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

            <!-- Nút bấm tiến hóa quân cờ -->
            <div class="chess-btn-wrapper">
                <div class="chess-btn" id="clickBtn" title="Bấm để tung nước đi chiến thuật">♕</div>
            </div>

            <button class="puzzle-trigger-btn" id="btnOpenPuzzle">🎯 Giải Câu Đố Cờ Vua</button>
        </div>

        <div class="shop-section">
            <h2>Phòng Huấn Luyện</h2>

            <button class="upgrade-btn" id="btnClickUpgrade">
                <div class="upgrade-info">
                    <div>Tấn Công Chiến Thuật</div>
                    <div>Giá: <span id="costClick">15</span> Elo (+1/click)</div>
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

    <!-- MODAL POPUP GIẢI CÂU ĐỐ -->
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

        // Tạo các quân cờ bay tự động ở hậu cảnh
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

        // Đổi màu chủ đề (Theme)
        function setTheme(themeName, save = true) {
            gameData.theme = themeName;
            document.body.className = "";
            if (themeName === 'emerald') document.body.classList.add('theme-emerald');
            if (themeName === 'ruby') document.body.classList.add('theme-ruby');
            if (save) {
                localStorage.setItem("chessClickerSave", JSON.stringify(gameData));
            }
        }

        const pieceSymbols = {
            'K': '♔', 'Q': '♕', 'R': '♖', 'B': '♗', 'N': '♘', 'P': '♙',
            'k': '♚', 'q': '♛', 'r': '♜', 'b': '♝', 'n': '♞', 'p': '♟'
        };

        // Tiến hóa quân cờ chính theo cấp độ
        function getEvolutionPiece(lvl) {
            if (lvl < 5) return '♙';  // Chốt
            if (lvl < 10) return '♘'; // Mã
            if (lvl < 15) return '♗'; // Tượng
            if (lvl < 25) return '♖'; // Xe
            if (lvl < 40) return '♕'; // Hậu
            if (lvl < 60) return '♔'; // Vua
            return '👑';              // Vương miện Đại Kiện Tướng
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
            },
            {
                q: "Nước đi thiên tài (Brilliant Move): Hy sinh Tượng phá vỡ thành phòng thủ!",
                board: [
                    ['r', 'n', 'b', 'q', 'k', '.', 'r', '.'],
                    ['p', 'p', 'p', '.', '.', 'p', 'p', 'p'],
                    ['.', '.', '.', '.', '.', 'n', '.', '.'],
                    ['.', '.', 'B', '.', '.', '.', '.', '.'],
                    ['.', '.', '.', '.', 'P', '.', '.', '.'],
                    ['.', '.', '.', '.', '.', 'N', '.', '.'],
                    ['P', 'P', 'P', 'P', '.', 'P', 'P', 'P'],
                    ['R', '.', 'B', 'Q', 'K', '.', '.', 'R']
                ],
                options: ["Bxh7+!", "Be2", "Bd3"],
                correct: 0,
                reward: 400
            }
        ];

        let activePuzzle = null;
        let puzzleTimerInterval = null;
        let timeLeft = 15;

        let audioCtx = null;
        function getAudioContext() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
            return audioCtx;
        }

        const eloEl = document.getElementById("elo");
        const epsEl = document.getElementById("eps");
        const clickBtn = document.getElementById("clickBtn");
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

        function createParticles(e, color) {
            const rect = clickBtn.getBoundingClientRect();
            const startX = e.clientX - rect.left;
            const startY = e.clientY - rect.top;

            for (let i = 0; i < 8; i++) {
                const particle = document.createElement("div");
                particle.className = "particle";
                particle.style.background = color;
                particle.style.width = Math.random() * 8 + 4 + "px";
                particle.style.height = particle.style.width;

                const angle = Math.random() * Math.PI * 2;
                const speed = Math.random() * 60 + 20;
                const dx = Math.cos(angle) * speed + "px";
                const dy = Math.sin(angle) * speed + "px";

                particle.style.setProperty('--dx', dx);
                particle.style.setProperty('--dy', dy);
                particle.style.left = startX + "px";
                particle.style.top = startY + "px";

                clickBtn.appendChild(particle);
                setTimeout(() => particle.remove(), 600);
            }
        }

        function updateUI() {
            gameData.eps = (gameData.auto1Count * 1) + (gameData.auto2Count * 10);

            eloEl.innerText = "Elo: " + Math.floor(gameData.elo).toLocaleString();
            epsEl.innerText = gameData.eps.toLocaleString();

            costClickEl.innerText = gameData.clickCost.toLocaleString();
            lvlClickEl.innerText = "Lv " + gameData.clickLvl;
            clickBtn.innerText = getEvolutionPiece(gameData.clickLvl);

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

        // Theo dõi tốc độ click để bật hiệu ứng lửa hào quang (Combo Aura)
        let recentClicks = [];
        function checkClickCombo() {
            const now = Date.now();
            recentClicks.push(now);
            recentClicks = recentClicks.filter(t => now - t < 1000);
            if (recentClicks.length >= 6) {
                clickBtn.classList.add("fire-aura");
            } else {
                clickBtn.classList.remove("fire-aura");
            }
        }

        function createClickEffect(e, isBrilliant, earnedElo) {
            const rect = clickBtn.getBoundingClientRect();
            const x = e.clientX - rect.left - 40;
            const y = e.clientY - rect.top - 20;

            if (isBrilliant) {
                createParticles(e, "#2ed573");
                const container = document.createElement("div");
                container.className = "brilliant-popup";
                container.innerHTML = `<div class="brilliant-badge">!!</div> Brilliant +${earnedElo} Elo!`;
                container.style.left = (x - 40) + "px";
                container.style.top = y + "px";
                clickBtn.appendChild(container);

                gameContainer.classList.remove("shake");
                void gameContainer.offsetWidth;
                gameContainer.classList.add("shake");

                setTimeout(() => container.remove(), 1000);
            } else {
                createParticles(e, "#f1c40f");
                const text = document.createElement("div");
                text.className = "floating-text";
                text.innerText = "+" + earnedElo + " Elo";
                text.style.left = x + "px";
                text.style.top = y + "px";
                clickBtn.appendChild(text);
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
                        const symbol = pieceSymbols[pieceChar] || '';
                        const isWhite = pieceChar === pieceChar.toUpperCase();
                        square.innerHTML = `<span class="${isWhite ? 'piece-w' : 'piece-b'}">${symbol}</span>`;
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
                alert(`🎉 CHÍNH XÁC! Bạn đã giải thành công và nhận +${activePuzzle.reward} Elo!`);
            } else {
                alert("❌ Sai mất rồi! Hãy thử lại ở câu sau nhé.");
            }
            closePuzzle();
            updateUI();
        }

        function closePuzzle() {
            puzzleModal.classList.remove("active");
            clearInterval(puzzleTimerInterval);
        }

        clickBtn.addEventListener("click", (e) => {
            const isBrilliant = Math.random() < 0.15; 
            const bonusMultiplier = isBrilliant ? 5 : 1;
            const earnedElo = gameData.clickPower * bonusMultiplier;

            gameData.elo += earnedElo;
            checkClickCombo();

            if (isBrilliant) {
                playBrilliantSound();
            } else {
                playNormalSound();
            }

            createClickEffect(e, isBrilliant, earnedElo);
            updateUI();
        });

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

        updateUI();
    </script>
</body>
</html>
