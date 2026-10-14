<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crypto Clicker - Game Bấm Kiếm Tiền</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1e1e2f, #121212);
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .game-container {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            background: #282a36;
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            max-width: 800px;
            width: 100%;
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
            color: #f1fa8c;
            margin-bottom: 5px;
        }

        .score-board p {
            color: #bd93f9;
            font-size: 1.1rem;
        }

        .coin {
            width: 160px;
            height: 160px;
            background: radial-gradient(circle, #ffeaa7 0%, #fdcb6e 100%);
            border: 8px solid #e1b12c;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 4rem;
            font-weight: bold;
            color: #d63031;
            cursor: pointer;
            box-shadow: 0 0 20px rgba(253, 203, 110, 0.4);
            transition: transform 0.05s ease-in-out;
            position: relative;
        }

        .coin:active {
            transform: scale(0.92);
        }

        .shop-section {
            flex: 1;
            min-width: 280px;
            background: #44475a;
            padding: 20px;
            border-radius: 15px;
        }

        .shop-section h2 {
            text-align: center;
            margin-bottom: 15px;
            color: #50fa7b;
        }

        .upgrade-btn {
            width: 100%;
            background: #6272a4;
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
            transition: background 0.2s;
        }

        .upgrade-btn:hover:not(:disabled) {
            background: #bd93f9;
            color: #282a36;
        }

        .upgrade-btn:disabled {
            background: #333;
            color: #777;
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
            background: #ff79c6;
            padding: 4px 8px;
            border-radius: 12px;
            font-size: 0.85rem;
            color: #fff;
        }

        .floating-text {
            position: absolute;
            font-weight: bold;
            font-size: 1.5rem;
            color: #50fa7b;
            pointer-events: none;
            animation: floatUp 0.8s ease-out forwards;
        }

        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0); }
            100% { opacity: 0; transform: translateY(-50px); }
        }
    </style>
</head>
<body>

    <div class="game-container">
        <div class="main-section">
            <div class="score-board">
                <h1 id="money">$0</h1>
                <p>Thu nhập: <span id="cps">0</span> $/giây</p>
            </div>
            <div class="coin" id="coinBtn">$</div>
        </div>

        <div class="shop-section">
            <h2>Cửa Hàng Nâng Cấp</h2>
            <button class="upgrade-btn" id="btnClickUpgrade">
                <div class="upgrade-info">
                    <div>Tăng Lực Bấm</div>
                    <div>Giá: $<span id="costClick">10</span> (+1 $/click)</div>
                </div>
                <span class="badge" id="lvlClick">Lv 1</span>
            </button>
            <button class="upgrade-btn" id="btnAuto1">
                <div class="upgrade-info">
                    <div>Chuột Tự Động</div>
                    <div>Giá: $<span id="costAuto1">15</span> (+1 $/s)</div>
                </div>
                <span class="badge" id="countAuto1">0</span>
            </button>
            <button class="upgrade-btn" id="btnAuto2">
                <div class="upgrade-info">
                    <div>Máy Đào Siêu Cấp</div>
                    <div>Giá: $<span id="costAuto2">100</span> (+8 $/s)</div>
                </div>
                <span class="badge" id="countAuto2">0</span>
            </button>
        </div>
    </div>

    <script>
        let gameData = {
            money: 0,
            clickPower: 1,
            clickCost: 10,
            clickLvl: 1,
            auto1Count: 0,
            auto1Cost: 15,
            auto2Count: 0,
            auto2Cost: 100,
            cps: 0
        };

        const savedData = localStorage.getItem("cryptoClickerSave");
        if (savedData) {
            gameData = Object.assign(gameData, JSON.parse(savedData));
        }

        const moneyEl = document.getElementById("money");
        const cpsEl = document.getElementById("cps");
        const coinBtn = document.getElementById("coinBtn");

        const btnClickUpgrade = document.getElementById("btnClickUpgrade");
        const costClickEl = document.getElementById("costClick");
        const lvlClickEl = document.getElementById("lvlClick");

        const btnAuto1 = document.getElementById("btnAuto1");
        const costAuto1El = document.getElementById("costAuto1");
        const countAuto1El = document.getElementById("countAuto1");

        const btnAuto2 = document.getElementById("btnAuto2");
        const costAuto2El = document.getElementById("costAuto2");
        const countAuto2El = document.getElementById("countAuto2");

        // --- HÀM TẠO ÂM THANH "TING" ---
        function playTingSound() {
            try {
                const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = audioCtx.createOscillator();
                const gainNode = audioCtx.createGain();

                osc.type = 'sine';
                osc.frequency.setValueAtTime(900, audioCtx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(1400, audioCtx.currentTime + 0.08);

                gainNode.gain.setValueAtTime(0.2, audioCtx.currentTime);
                gainNode.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.08);

                    osc.connect(gainNode);
                gainNode.connect(audioCtx.destination);

                osc.start();
                osc.stop(audioCtx.currentTime + 0.08);
            } catch (e) {
                // Trình duyệt chặn âm thanh nếu chưa tương tác (phòng ngừa)
            }
        }

        function updateUI() {
            gameData.cps = (gameData.auto1Count * 1) + (gameData.auto2Count * 8);

            moneyEl.innerText = "$" + Math.floor(gameData.money).toLocaleString();
            cpsEl.innerText = gameData.cps.toLocaleString();

            costClickEl.innerText = gameData.clickCost.toLocaleString();
            lvlClickEl.innerText = "Lv " + gameData.clickLvl;

            costAuto1El.innerText = gameData.auto1Cost.toLocaleString();
            countAuto1El.innerText = gameData.auto1Count;

            costAuto2El.innerText = gameData.auto2Cost.toLocaleString();
            countAuto2El.innerText = gameData.auto2Count;

            btnClickUpgrade.disabled = gameData.money < gameData.clickCost;
            btnAuto1.disabled = gameData.money < gameData.auto1Cost;
            btnAuto2.disabled = gameData.money < gameData.auto2Cost;
        }

        function createFloatingText(e) {
            const text = document.createElement("div");
            text.className = "floating-text";
            text.innerText = "+" + gameData.clickPower;
            
            const rect = coinBtn.getBoundingClientRect();
            const x = e.clientX - rect.left - 20;
            const y = e.clientY - rect.top - 20;

            text.style.left = x + "px";
            text.style.top = y + "px";

            coinBtn.appendChild(text);

            setTimeout(() => text.remove(), 800);
        }

        coinBtn.addEventListener("click", (e) => {
            gameData.money += gameData.clickPower;
            playTingSound(); // Phát âm thanh khi bấm
            createFloatingText(e);
            updateUI();
        });

        btnClickUpgrade.addEventListener("click", () => {
            if (gameData.money >= gameData.clickCost) {
                gameData.money -= gameData.clickCost;
                gameData.clickPower += 1;
                gameData.clickLvl += 1;
                gameData.clickCost = Math.floor(gameData.clickCost * 1.5);
                updateUI();
            }
        });

        btnAuto1.addEventListener("click", () => {
            if (gameData.money >= gameData.auto1Cost) {
                gameData.money -= gameData.auto1Cost;
                gameData.auto1Count += 1;
                gameData.auto1Cost = Math.floor(gameData.auto1Cost * 1.3);
                updateUI();
            }
        });

        btnAuto2.addEventListener("click", () => {
            if (gameData.money >= gameData.auto2Cost) {
                gameData.money -= gameData.auto2Cost;
                gameData.auto2Count += 1;
                gameData.auto2Cost = Math.floor(gameData.auto2Cost * 1.4);
                updateUI();
            }
        });

        setInterval(() => {
            gameData.money += gameData.cps;
            updateUI();
        }, 1000);

        setInterval(() => {
            localStorage.setItem("cryptoClickerSave", JSON.stringify(gameData));
        }, 5000);

        updateUI();
    </script>
</body>
</html>
