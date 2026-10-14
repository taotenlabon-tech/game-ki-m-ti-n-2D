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
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1b1b1b, #2c3e50);
            color: #f5f6fa;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
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
            transition: transform 0.1s ease;
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
        }

        .score-board {
            margin-bottom: 15px;
        }

        .score-board h1 {
            font-size: 2.3rem;
            color: #f1c40f;
            margin-bottom: 5px;
        }

        .score-board p {
            color: #a4b0be;
            font-size: 1rem;
        }

        /* Nút bấm hình Quân Cờ Vua */
        .chess-btn {
            width: 150px;
            height: 150px;
            background: radial-gradient(circle, #fbc531 0%, #e1b12c 100%);
            border: 8px solid #d4ac0d;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 4.5rem;
            cursor: pointer;
            box-shadow: 0 0 25px rgba(241, 196, 15, 0.4);
            transition: transform 0.05s ease-in-out, box-shadow 0.2s;
            position: relative;
            margin-bottom: 15px;
        }

        .chess-btn:hover {
            box-shadow: 0 0 35px rgba(241, 196, 15, 0.7);
        }

        .chess-btn:active {
            transform: scale(0.92);
        }

        /* Nút mở thử thách Puzzle thủ công */
        .puzzle-trigger-btn {
            background: #e67e22;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 10px;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.2s, transform 0.1s;
            box-shadow: 0 4px 10px rgba(230, 126, 34, 0.4);
        }

        .puzzle-trigger-btn:hover {
            background: #d35400;
            transform: translateY(-2px);
        }

        /* Cửa hàng nâng cấp */
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

        .brilliant-popup {
            position: absolute;
            font-weight: 900;
            font-size: 2.2rem;
            color: #2ed573;
            text-shadow: 0 0 15px #2ed573, 0 0 30px #2ed573;
            pointer-events: none;
            display: flex;
            align-items: center;
            gap: 5px;
            animation: brilliantAnim 1s ease-out forwards;
            white-space: nowrap;
            z-index: 10;
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
            font-size: 1.5rem;
            font-weight: bold;
            box-shadow: 0 0 15px #2ed573;
        }

        @keyframes brilliantAnim {
            0% { opacity: 0; transform: scale(0.3) translateY(0); }
            30% { opacity: 1; transform: scale(1.3) translateY(-20px); }
            100% { opacity: 0; transform: scale(1) translateY(-80px); }
        }

        /* --- MODAL GIẢI BÀI TẬP CỜ VUA (PUZZLE) --- */
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
            transition: opacity 0.3s ease;
        }

        .modal-overlay.active {
            opacity: 1;
            pointer-events: auto;
        }

        .modal-content {
            background: #2f3640;
            padding: 30px;
            border-radius: 20px;
            max-width: 450px;
            width: 90%;
            text-align: center;
            border: 3px solid #f1c40f;
            box-shadow: 0 10px 30px rgba(0,0,0,0.8);
            transform: scale(0.8);
            transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }

        .modal-overlay.active .modal-content {
            transform: scale(1);
        }

        .modal-content h3 {
            color: #f1c40f;
            font-size: 1.6rem;
            margin-bottom: 10px;
        }

        .puzzle-desc {
            font-size: 1.1rem;
            color: #f5f6fa;
            margin-bottom: 20px;
            background: #1e272e;
            padding: 15px;
            border-radius: 10px;
            border-left: 5px solid #e67e22;
        }

        .puzzle-options {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-bottom: 15px;
        }

        .puzzle-opt-btn {
            background: #353b48;
            color: white;
            border: 2px solid #57606f;
            padding: 12px;
            border-radius: 10px;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.2s;
        }

        .puzzle-opt-btn:hover {
            background: #00a8ff;
            border-color: #0097e6;
        }

        .puzzle-timer {
            font-size: 0.9rem;
            color: #ff4757;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <div class="game-container" id="gameContainer">
        <!-- Khu vực bấm cờ vua -->
        <div class="main-section">
            <div class="score-board">
                <h1 id="elo">Elo: 400</h1>
                <p>Tốc độ: <span id="eps">0</span> Elo/giây</p>
            </div>

            <!-- Click quân Hậu -->
            <div class="chess-btn" id="clickBtn" title="Bấm để tung nước đi chiến thuật">♕</div>

            <!-- Nút gọi Thử Thách Puzzle -->
            <button class="puzzle-trigger-btn" id="btnOpenPuzzle">🎯 Thử Thách Chiến Thuật</button>
        </div>

        <!-- Cửa Hàng Huấn Luyện -->
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

    <!-- MODAL POPUP GIẢI BÀI TẬP CỜ VUA -->
    <div class="modal-overlay" id="puzzleModal">
        <div class="modal-content">
            <h3>⚡ Thử Thách Chiến Thuật!</h3>
            <div class="puzzle-desc" id="puzzleText">Đang tải câu hỏi...</div>
            <div class="puzzle-options" id="puzzleOptions">
                <!-- Các đáp án sẽ load bằng JS -->
            </div>
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
            eps: 0
        };

        const savedData = localStorage.getItem("chessClickerSave");
        if (savedData) {
            gameData = Object.assign(gameData, JSON.parse(savedData));
        }

        // Danh sách kho bài tập cờ vua phong phú
        const puzzleList = [
            {
                q: "Chiếu hết 1 nước (Mate in 1): Hậu trắng ở d1, vua đen kẹt ở g8 với hàng chắn h7. Đi nước nào chiếu hết ngay?",
                options: ["Qxh7#", "Qd8+", "Qxf3"],
                correct: 0,
                reward: 250
            },
            {
                q: "Chiêu thức Bắt đôi (Fork): Mã trắng nhảy vào ô nào để bắt đồng thời Vua và Xe của đối thủ?",
                options: ["Nf7+", "Nc3", "Nxd5"],
                correct: 0,
                reward: 300
            },
            {
                q: "Nước đi thiên tài (Brilliant Move): Tượng hy sinh vào ô nào để phá vỡ thành phòng thủ của đối phương?",
                options: ["Bxh7+!", "Be2", "Bd3"],
                correct: 0,
                reward: 400
            },
            {
                q: "Chiếu hết kinh điển mẫu Đòn Ghim (Pin) & Kết liễu: Xe trắng bất ngờ chiếu hết góc bảng ở đâu?",
                options: ["Rh8#", "Rxd7", "Raa1"],
                correct: 0,
                reward: 350
            },
            {
                q: "Tạo nước đi Chốt phong hậu (Promotion): Chốt ở hàng 7 tiến thẳng lên ô nào để hóa Hậu ngay lập tức?",
                options: ["e8=Q", "e8=R", "e8=N"],
                correct: 0,
                reward: 200
            }
        ];

        let activePuzzle = null;
        let puzzleTimerInterval = null;
        let timeLeft = 15;

        const eloEl = document.getElementById("elo");
        const epsEl = document.getElementById("eps");
        const clickBtn = document.getElementById("clickBtn");
        const gameContainer = document.getElementById("gameContainer");
        const puzzleModal = document.getElementById("puzzleModal");
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

        // Âm thanh
        function playNormalSound() {
            try {
                const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = audioCtx.createOscillator();
                const gainNode = audioCtx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(600, audioCtx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(1000, audioCtx.currentTime + 0.06);
                gainNode.gain.setValueAtTime(0.2, audioCtx.currentTime);
                gainNode.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.06);
                osc.connect(gainNode);
                gainNode.connect(audioCtx.destination);
                osc.start();
                osc.stop(audioCtx.currentTime + 0.06);
            } catch (e) {}
        }

        function playBrilliantSound() {
            try {
                const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
                const osc1 = audioCtx.createOscillator();
                const gain1 = audioCtx.createGain();
                osc1.type = 'triangle';
                osc1.frequency.setValueAtTime(1046.5, audioCtx.currentTime);
                gain1.gain.setValueAtTime(0.3, audioCtx.currentTime);
                gain1.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.3);
                osc1.connect(gain1);
                gain1.connect(audioCtx.destination);
                osc1.start();
                osc1.stop(audioCtx.currentTime + 0.3);

                setTimeout(() => {
                    const osc2 = audioCtx.createOscillator();
                    const gain2 = audioCtx.createGain();
                    osc2.type = 'sine';
                    osc2.frequency.setValueAtTime(1567.98, audioCtx.currentTime);
                    gain2.gain.setValueAtTime(0.4, audioCtx.currentTime);
                    gain2.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.5);
                    osc2.connect(gain2);
                    gain2.connect(audioCtx.destination);
                    osc2.start();
                    osc2.stop(audioCtx.currentTime + 0.5);
                }, 80);
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

            btnClickUpgrade.disabled = gameData.elo < gameData.clickCost;
            btnAuto1.disabled = gameData.elo < gameData.auto1Cost;
            btnAuto2.disabled = gameData.elo < gameData.auto2Cost;
        }

        function createClickEffect(e, isBrilliant, earnedElo) {
            const rect = clickBtn.getBoundingClientRect();
            const x = e.clientX - rect.left - 40;
            const y = e.clientY - rect.top - 20;

            if (isBrilliant) {
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
                const text = document.createElement("div");
                text.className = "floating-text";
                text.innerText = "+" + earnedElo + " Elo";
                text.style.left = x + "px";
                text.style.top = y + "px";
                clickBtn.appendChild(text);
                setTimeout(() => text.remove(), 800);
            }
        }

        // --- HỆ THỐNG PUZZLE ---
        function openPuzzle() {
            if (puzzleModal.classList.contains("active")) return;

            // Chọn ngẫu nhiên 1 câu hỏi
            activePuzzle = puzzleList[Math.floor(Math.random() * puzzleList.length)];
            puzzleText.innerText = activePuzzle.q;
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
                alert(`🎉 CHÍNH XÁC! Bạn đã giải thành công bài tập và nhận ngay +${activePuzzle.reward} Elo!`);
            } else {
                alert("❌ Sai mất rồi! Hãy cố gắng ở câu hỏi tiếp theo nhé.");
            }
            closePuzzle();
            updateUI();
        }

        function closePuzzle() {
            puzzleModal.classList.remove("active");
            clearInterval(puzzleTimerInterval);
        }

        // Click quân cờ
        clickBtn.addEventListener("click", (e) => {
            const isBrilliant = Math.random() < 0.15; 
            const bonusMultiplier = isBrilliant ? 5 : 1;
            const earnedElo = gameData.clickPower * bonusMultiplier;

            gameData.elo += earnedElo;

            if (isBrilliant) {
                playBrilliantSound();
            } else {
                playNormalSound();
            }

            createClickEffect(e, isBrilliant, earnedElo);
            updateUI();
        });

        btnOpenPuzzle.addEventListener("click", openPuzzle);

        // Tự động bật câu đố mỗi 45 giây để thử thách người chơi
        setInterval(() => {
            openPuzzle();
        }, 45000);

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
