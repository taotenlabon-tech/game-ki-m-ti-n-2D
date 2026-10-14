<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chess Master Clicker - Cày Elo Cờ Vua</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1b1b1b, #2c3e50);
            color: #f5f6fa;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .game-container {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            background: #23272a;
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.6);
            max-width: 820px;
            width: 100%;
            border: 2px solid #36393f;
        }

        .main-section {
            flex: 1;
            min-width: 280px;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }

        .score-board {
            margin-bottom: 20px;
        }

        .score-board h1 {
            font-size: 2.5rem;
            color: #f1c40f;
            margin-bottom: 5px;
        }

        .score-board p {
            color: #a4b0be;
            font-size: 1.1rem;
        }

        /* Nút bấm hình Quân Cờ Vua (Quân Hậu ♕) */
        .chess-btn {
            width: 160px;
            height: 160px;
            background: radial-gradient(circle, #fbc531 0%, #e1b12c 100%);
            border: 8px solid #d4ac0d;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 5rem;
            cursor: pointer;
            box-shadow: 0 0 25px rgba(241, 196, 15, 0.4);
            transition: transform 0.05s ease-in-out, box-shadow 0.2s;
            position: relative;
        }

        .chess-btn:hover {
            box-shadow: 0 0 35px rgba(241, 196, 15, 0.7);
        }

        .chess-btn:active {
            transform: scale(0.92);
        }

        /* Cửa hàng nâng cấp chiến thuật */
        .shop-section {
            flex: 1;
            min-width: 280px;
            background: #2f3640;
            padding: 20px;
            border-radius: 15px;
            border: 1px solid #353b48;
        }

        .shop-section h2 {
            text-align: center;
            margin-bottom: 15px;
            color: #4cd137;
        }

        .upgrade-btn {
            width: 100%;
            background: #353b48;
            color: white;
            border: none;
            padding: 12px 15px;
            margin-bottom: 12px;
            border-radius: 10px;
            cursor: pointer;
            text-align: left;
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: background 0.2s, transform 0.1s;
        }

        .upgrade-btn:hover:not(:disabled) {
            background: #00a8ff;
            color: #fff;
            transform: translateY(-2px);
        }

        .upgrade-btn:disabled {
            background: #1e272e;
            color: #57606f;
            cursor: not-allowed;
        }

        .upgrade-info div:first-child {
            font-weight: bold;
            font-size: 1rem;
        }

        .upgrade-info div:last-child {
            font-size: 0.85rem;
            opacity: 0.8;
        }

        .badge {
            background: #e84118;
            padding: 4px 8px;
            border-radius: 12px;
            font-size: 0.85rem;
            color: #fff;
            font-weight: bold;
        }

        /* Hiệu ứng chữ số Elo bay lên (Brilliant Move!!) */
        .floating-text {
            position: absolute;
            font-weight: bold;
            font-size: 1.4rem;
            color: #00d2d3;
            pointer-events: none;
            animation: floatUp 0.8s ease-out forwards;
            text-shadow: 0 2px 4px rgba(0,0,0,0.5);
        }

        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0); }
            100% { opacity: 0; transform: translateY(-60px); }
        }
    </style>
</head>
<body>

    <div class="game-container">
        <!-- Khu vực bấm chiến thuật cờ vua -->
        <div class="main-section">
            <div class="score-board">
                <h1 id="elo">Elo: 400</h1>
                <p>Tốc độ: <span id="eps">0</span> Elo/giây</p>
            </div>

            <!-- Click vào quân Hậu để tung nước đi thiên tài -->
            <div class="chess-btn" id="clickBtn" title="Bấm để tung nước đi Brilliant !!">♕</div>
        </div>

        <!-- Khu vực Cửa Hàng Huấn Luyện -->
        <div class="shop-section">
            <h2>Phòng Huấn Luyện</h2>

            <!-- Upgrade Click Power -->
            <button class="upgrade-btn" id="btnClickUpgrade">
                <div class="upgrade-info">
                    <div>Tấn Công Chiến Thuật (Brilliant !!)</div>
                    <div>Giá: <span id="costClick">15</span> Elo (+1/click)</div>
                </div>
                <span class="badge" id="lvlClick">Lv 1</span>
            </button>

            <!-- Auto 1: Học Viên -->
            <button class="upgrade-btn" id="btnAuto1">
                <div class="upgrade-info">
                    <div>Học Viên Giải Bài Tập</div>
                    <div>Giá: <span id="costAuto1">30</span> Elo (+1 Elo/s)</div>
                </div>
                <span class="badge" id="countAuto1">0</span>
            </button>

            <!-- Auto 2: Đội Ngũ AI Phân Tích -->
            <button class="upgrade-btn" id="btnAuto2">
                <div class="upgrade-info">
                    <div>Động Cơ AI (Stockfish)</div>
                    <div>Giá: <span id="costAuto2">150</span> Elo (+10 Elo/s)</div>
                </div>
                <span class="badge" id="countAuto2">0</span>
            </button>
        </div>
    </div>

    <script>
        // Dữ liệu game Cờ Vua
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

        // Tải tiến trình cũ từ trình duyệt
        const savedData = localStorage.getItem("chessClickerSave");
        if (savedData) {
            gameData = Object.assign(gameData, JSON.parse(savedData));
        }

        // DOM Elements
        const eloEl = document.getElementById("elo");
        const epsEl = document.getElementById("eps");
        const clickBtn = document.getElementById("clickBtn");

        const btnClickUpgrade = document.getElementById("btnClickUpgrade");
        const costClickEl = document.getElementById("costClick");
        const lvlClickEl = document.getElementById("lvlClick");

        const btnAuto1 = document.getElementById("btnAuto1");
        const costAuto1El = document.getElementById("costAuto1");
        const countAuto1El = document.getElementById("countAuto1");

        const btnAuto2 = document.getElementById("btnAuto2");
        const costAuto2El = document.getElementById("costAuto2");
        const countAuto2El = document.getElementById("countAuto2");

        // Âm thanh nước đi thiên tài "Ting"
        function playChessSound() {
            try {
                const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = audioCtx.createOscillator();
                const gainNode = audioCtx.createGain();

                osc.type = 'triangle';
                osc.frequency.setValueAtTime(800, audioCtx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(1600, audioCtx.currentTime + 0.08);

                gainNode.gain.setValueAtTime(0.25, audioCtx.currentTime);
                gainNode.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.08);

                osc.connect(gainNode);
                gainNode.connect(audioCtx.destination);

                osc.start();
                osc.stop(audioCtx.currentTime + 0.08);
            } catch (e) {}
        }

        // Cập nhật giao diện
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

            // Khóa/Mở nút mua
            btnClickUpgrade.disabled = gameData.elo < gameData.clickCost;
            btnAuto1.disabled = gameData.elo < gameData.auto1Cost;
            btnAuto2.disabled = gameData.elo < gameData.auto2Cost;
        }

        // Hiệu ứng chữ Brilliant !! bay lên
        const phrases = ["+ ", "Brilliant !! ", "Checkmate! ", "+ "];
        function createFloatingText(e) {
            const text = document.createElement("div");
            text.className = "floating-text";
            text.innerText = "+" + gameData.clickPower + " Elo";
            
            const rect = clickBtn.getBoundingClientRect();
            const x = e.clientX - rect.left - 20;
            const y = e.clientY - rect.top - 20;

            text.style.left = x + "px";
            text.style.top = y + "px";

            clickBtn.appendChild(text);
            setTimeout(() => text.remove(), 800);
        }

        // Sự kiện click quân cờ
        clickBtn.addEventListener("click", (e) => {
            gameData.elo += gameData.clickPower;
            playChessSound();
            createFloatingText(e);
            updateUI();
        });

        // Nâng cấp lực click
        btnClickUpgrade.addEventListener("click", () => {
            if (gameData.elo >= gameData.clickCost) {
                gameData.elo -= gameData.clickCost;
                gameData.clickPower += 1;
                gameData.clickLvl += 1;
                gameData.clickCost = Math.floor(gameData.clickCost * 1.5);
                updateUI();
            }
        });

        // Mua học viên
        btnAuto1.addEventListener("click", () => {
            if (gameData.elo >= gameData.auto1Cost) {
                gameData.elo -= gameData.auto1Cost;
                gameData.auto1Count += 1;
                gameData.auto1Cost = Math.floor(gameData.auto1Cost * 1.3);
                updateUI();
            }
        });

        // Mua Stockfish AI
        btnAuto2.addEventListener("click", () => {
            if (gameData.elo >= gameData.auto2Cost) {
                gameData.elo -= gameData.auto2Cost;
                gameData.auto2Count += 1;
                gameData.auto2Cost = Math.floor(gameData.auto2Cost * 1.4);
                updateUI();
            }
        });

        // Vòng lặp game tự động cộng Elo theo giây
        setInterval(() => {
            gameData.elo += gameData.eps;
            updateUI();
        }, 1000);

        // Tự lưu tiến trình mỗi 5 giây
        setInterval(() => {
            localStorage.setItem("chessClickerSave", JSON.stringify(gameData));
        }, 5000);

        updateUI();
    </script>
</body>
</html>
