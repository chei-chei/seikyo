<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>11.11秒チャレンジ</title>

<style>
    html, body {
        width: 100%;
        height: 100%;
        margin: 0;
        padding: 0;
        overflow: hidden;
        background: white;
        color: #222;
        font-family: sans-serif;
        user-select: none;
    }

    #gameContainer {
        width: 100%;
        height: 100%;
        box-sizing: border-box;
        text-align: center;
        padding-top: 80px;
        position: relative;
        z-index: 1;
    }

    h1 {
        font-size: 40px;
        margin-bottom: 30px;
    }

    #target {
        font-size: 30px;
        margin-bottom: 20px;
    }

    #timer {
        font-size: 70px;
        font-weight: bold;
        margin: 20px;
    }

    #message {
        font-size: 20px;
        margin: 20px;
        min-height: 30px;
    }

    button {
        font-size: 24px;
        padding: 12px 35px;
        cursor: pointer;
        position: relative;
        z-index: 100;
    }

    #reward {
        font-size: 22px;
        margin-top: 30px;
        font-weight: bold;
        color: #d00000;
    }


    /* =========================
       結果演出画面（暗転用）
       ========================= */

    #effect {
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100vh;
        background: black;
        display: none;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        z-index: 9999;
        overflow: hidden;
        cursor: pointer;
    }

    #effect.dark {
        display: flex !important;
    }

    #resultDigits {
        font-size: 75px;
        font-family: monospace;
        font-weight: bold;
        color: white;
        letter-spacing: 5px;
        text-shadow: 0 0 10px #00ffff, 0 0 25px #0088ff;
        min-height: 90px;
    }

    #resultType {
        font-size: 55px;
        font-weight: bold;
        margin-top: 30px;
        min-height: 90px;
        text-align: center;
        padding: 0 20px;
    }

    #resultReward {
        font-size: 35px;
        font-weight: bold;
        margin-top: 25px;
        color: white;
        text-shadow: 0 0 10px white, 0 0 20px gold;
        opacity: 0;
    }

    #resultReward.show {
        animation: rewardAppear 0.8s ease forwards;
    }

    .retryHint {
        margin-top: 40px;
        font-size: 18px;
        color: #aaa;
        opacity: 0;
        transition: opacity 0.5s;
    }

    .retryHint.show {
        opacity: 1;
    }


    /* PERFECT & GREAT 演出 */

    .perfect {
        animation: perfectAppear 0.8s ease forwards, rainbowText 1.2s linear infinite;
    }

    @keyframes perfectAppear {
        0% { transform: scale(0.2); opacity: 0; }
        60% { transform: scale(1.2); opacity: 1; }
        100% { transform: scale(1); opacity: 1; }
    }

    @keyframes rainbowText {
        0% { color: red; text-shadow: 0 0 20px red; }
        16% { color: orange; text-shadow: 0 0 20px orange; }
        33% { color: yellow; text-shadow: 0 0 20px yellow; }
        50% { color: lime; text-shadow: 0 0 20px lime; }
        66% { color: cyan; text-shadow: 0 0 20px cyan; }
        83% { color: blue; text-shadow: 0 0 20px blue; }
        100% { color: magenta; text-shadow: 0 0 20px magenta; }
    }

    .perfectDigits {
        animation: perfectDigits 0.8s ease forwards;
    }

    @keyframes perfectDigits {
        0% { transform: scale(0.5); opacity: 0; }
        60% { transform: scale(1.15); opacity: 1; }
        100% { transform: scale(1); opacity: 1; }
    }

    .great {
        color: #00ffff;
        text-shadow: 0 0 15px #00ffff;
        animation: simpleAppear 0.5s ease forwards;
    }

    @keyframes simpleAppear {
        from { transform: scale(0.8); opacity: 0; }
        to { transform: scale(1); opacity: 1; }
    }


    /* =========================
       景品分配ルーレット画面
       ========================= */

    #rouletteOverlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100vh;
        background: rgba(0, 0, 0, 0.93);
        display: none;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        z-index: 10001;
        color: white;
    }

    #rouletteOverlay.show {
        display: flex !important;
    }

    .rouletteTitle {
        font-size: 28px;
        margin-bottom: 25px;
        color: #ffd700;
        text-shadow: 0 0 10px gold;
    }

    /* ドラム枠を3つ横並びに */
    .slotsContainer {
        display: flex;
        gap: 15px;
        justify-content: center;
        align-items: center;
        margin-bottom: 20px;
    }

    .slotCard {
        width: 100px;
        padding: 15px 5px;
        border: 3px solid #ffd700;
        border-radius: 12px;
        background: #151515;
        text-align: center;
        box-shadow: 0 0 15px rgba(255, 215, 0, 0.3);
    }

    .slotName {
        font-size: 18px;
        color: #ccc;
        margin-bottom: 10px;
        font-weight: bold;
    }

    .slotValue {
        font-size: 38px;
        font-weight: bold;
        color: #fff;
        min-height: 45px;
    }

    .slotValue.decided {
        color: #00ffff;
        text-shadow: 0 0 12px #00ffff;
        animation: popItem 0.3s ease;
    }

    @keyframes popItem {
        0% { transform: scale(0.6); }
        70% { transform: scale(1.3); }
        100% { transform: scale(1); }
    }

    #rouletteResultText {
        font-size: 24px;
        font-weight: bold;
        margin-top: 15px;
        min-height: 40px;
        color: #ff3366;
        text-shadow: 0 0 10px #ff3366;
    }


    /* フラッシュ & 振動 & アニメーション */

    #flash {
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100vh;
        background: white;
        opacity: 0;
        pointer-events: none;
        z-index: 10000;
    }

    #flash.active { animation: flash 0.45s ease; }

    @keyframes flash {
        0% { opacity: 0; }
        20% { opacity: 1; }
        100% { opacity: 0; }
    }

    .shake { animation: shake 0.5s ease; }

    @keyframes shake {
        0%   { transform: translate(0, 0); }
        20%  { transform: translate(-8px, 5px); }
        40%  { transform: translate(8px, -5px); }
        60%  { transform: translate(-6px, -3px); }
        80%  { transform: translate(6px, 3px); }
        100% { transform: translate(0, 0); }
    }

    @keyframes rewardAppear {
        0% { transform: scale(0.5); opacity: 0; }
        70% { transform: scale(1.15); opacity: 1; }
        100% { transform: scale(1); opacity: 1; }
    }

    .particle {
        position: absolute;
        width: 12px;
        height: 12px;
        border-radius: 50%;
        background: white;
        box-shadow: 0 0 8px white, 0 0 18px yellow;
        pointer-events: none;
    }
</style>
</head>

<body>

<div id="gameContainer">
    <h1>11.11秒チャレンジ</h1>

    <div id="target">目標：11.11秒</div>

    <div id="timer">0.00</div>

    <div id="message">
        STARTを押して11.11秒を目指せ！
    </div>

    <button id="startButton">START</button>

    <div id="reward"></div>
</div>


<!-- 暗転結果演出 -->

<div id="effect">
    <div id="resultDigits"></div>
    <div id="resultType"></div>
    <div id="resultReward"></div>
    <div id="retryHint" class="retryHint">タップして景品分配へ</div>
</div>


<!-- 景品分配ルーレット画面 -->

<div id="rouletteOverlay">
    <div class="rouletteTitle" id="rouletteTitle">🎁 景品分配中… 🎁</div>

    <div class="slotsContainer">
        <div class="slotCard">
            <div class="slotName">ポッキー</div>
            <div class="slotValue" id="slot0">?</div>
        </div>
        <div class="slotCard">
            <div class="slotName">プリッツ</div>
            <div class="slotValue" id="slot1">?</div>
        </div>
        <div class="slotCard">
            <div class="slotName">トッポ</div>
            <div class="slotValue" id="slot2">?</div>
        </div>
    </div>

    <div id="rouletteResultText"></div>
    <div id="rouletteHint" class="retryHint">タップして確定</div>
</div>

<div id="flash"></div>


<script>

/* =========================================================
 * テストモード設定
 * null      : 本番モード（プレイヤーの実力通り判定）
 * "fail"    : 残念（景品なし）
 * "good"    : 1個獲得
 * "great"   : 3個獲得
 * "perfect" : 11個獲得
 * ========================================================= */
const TEST_MODE = null;


const TARGET_TIME = 11.11;
const VISIBLE_TIME = 3.33;

const RANGE_PERFECT = 0.01;
const RANGE_GREAT   = 0.10;
const RANGE_GOOD    = 0.50;

const DARK_TIME = 800;
const DIGIT_INTERVAL = 700;
const LAST_DIGIT_PAUSE = 1500;

/* ゲーム状態 */
let running = false;
let startTime = 0;
let timerId = null;
let finalTime = 0;
let displayTimeStr = "";

let showingResult = false;
let earnedCount = 0; // 獲得合計数（1, 3, 11）
let finalAllocation = [0, 0, 0]; // 確定した分配数

let rouletteSpinning = false;
let rouletteReadyToClose = false;
let rouletteTimer = null;


/* HTML要素 */
const timer = document.getElementById("timer");
const message = document.getElementById("message");
const startButton = document.getElementById("startButton");
const reward = document.getElementById("reward");

const effect = document.getElementById("effect");
const resultDigits = document.getElementById("resultDigits");
const resultType = document.getElementById("resultType");
const resultReward = document.getElementById("resultReward");
const retryHint = document.getElementById("retryHint");

const rouletteOverlay = document.getElementById("rouletteOverlay");
const rouletteTitle = document.getElementById("rouletteTitle");
const slot0 = document.getElementById("slot0");
const slot1 = document.getElementById("slot1");
const slot2 = document.getElementById("slot2");
const rouletteResultText = document.getElementById("rouletteResultText");
const rouletteHint = document.getElementById("rouletteHint");

const flash = document.getElementById("flash");


/* イベント登録 */

startButton.addEventListener("click", () => {
    if (!running) {
        startGame();
    } else {
        stopGame();
    }
});

/* タイム結果画面のクリック */
effect.addEventListener("click", () => {
    if (showingResult) {
        effect.classList.remove("dark");
        showingResult = false;
        startRoulette();
    }
});

/* ルーレット画面のクリック */
rouletteOverlay.addEventListener("click", () => {
    if (rouletteSpinning) {
        // スピン中にタップされたら即時確定
        stopRoulette();
    } else if (rouletteReadyToClose) {
        // 確定後にタップされたら閉じて最初に戻る
        closeRouletteAndReset();
    }
});


/* =========================
   ゲーム開始
   ========================= */

function startGame() {
    if (running) return;

    running = true;
    showingResult = false;
    effect.classList.remove("dark");

    startTime = performance.now();

    timer.textContent = "0.00";
    message.textContent = "11.11秒を狙え！";
    reward.textContent = "";

    startButton.textContent = "STOP";

    timerId = requestAnimationFrame(updateTimer);
}


/* =========================
   タイマー更新
   ========================= */

function updateTimer() {
    if (!running) return;

    const elapsed = (performance.now() - startTime) / 1000;

    if (elapsed < VISIBLE_TIME) {
        timer.textContent = elapsed.toFixed(2);
    } else {
        timer.textContent = "？？？";
    }

    timerId = requestAnimationFrame(updateTimer);
}


/* =========================
   ゲーム停止
   ========================= */

function stopGame() {
    if (!running) return;

    running = false;
    cancelAnimationFrame(timerId);

    finalTime = (performance.now() - startTime) / 1000;
    
    const formattedString = finalTime.toFixed(2);
    const roundedTime = parseFloat(formattedString);

    startButton.textContent = "START";

    const diff = Math.abs(roundedTime - TARGET_TIME);

    let mode = TEST_MODE;

    if (!mode) {
        if (diff > RANGE_GOOD) mode = "fail";
        else if (diff > RANGE_GREAT) mode = "good";
        else if (diff > RANGE_PERFECT) mode = "great";
        else mode = "perfect";
    }

    if (TEST_MODE) {
        if (mode === "perfect") displayTimeStr = "11.11";
        else if (mode === "great") displayTimeStr = "11.08";
        else if (mode === "good") displayTimeStr = "11.35";
        else displayTimeStr = "12.45";
    } else {
        displayTimeStr = formattedString;
    }

    /* 獲得数の設定 */
    if (mode === "perfect") earnedCount = 11;
    else if (mode === "great") earnedCount = 3;
    else if (mode === "good") earnedCount = 1;
    else earnedCount = 0;


    /* １．0.5秒以上のズレ（獲得なし・失敗） */
    if (mode === "fail") {
        timer.textContent = displayTimeStr;
        message.textContent = "残念。。また挑戦してね！";
        reward.textContent = "";
    }

    /* ２．0.1秒〜0.5秒のズレ（1個獲得 -> ルーレットへ） */
    else if (mode === "good") {
        timer.textContent = displayTimeStr;
        message.textContent = "いいねまあ111年後に出直してきなよ";
        reward.textContent = "🎁 1個獲得！ (タップで分配ルーレットへ)";
        
        // 画面タップでルーレット起動できるように少し待ってから自動起動も可
        setTimeout(() => {
            startRoulette();
        }, 1200);
    }

    /* ３ / ４．暗転演出へ移行（3個 または 11個獲得） */
    else {
        timer.textContent = "？？？";
        showResult(displayTimeStr, mode);
    }
}


/* =========================
   暗転結果表示
   ========================= */

function showResult(displayString, result) {
    showingResult = true;
    playResultEffect(displayString, result);
}

function playResultEffect(displayString, result) {
    effect.className = "";
    resultDigits.className = "";
    resultType.className = "";
    resultReward.className = "";
    retryHint.classList.remove("show");

    resultDigits.textContent = "";
    resultType.textContent = "";
    resultReward.textContent = "";

    document.querySelectorAll(".particle").forEach(p => p.remove());

    setTimeout(() => {
        effect.classList.add("dark");
    }, 50);

    setTimeout(() => {
        showDigits(displayString, result);
    }, DARK_TIME + 50);
}

function showDigits(displayString, result) {
    resultDigits.textContent = "";
    let index = 0;

    function step() {
        if (index >= displayString.length) {
            if (result === "perfect") {
                resultDigits.classList.add("perfectDigits");
            }
            setTimeout(() => {
                showJudgement(result);
            }, 500);
            return;
        }

        index++;
        resultDigits.textContent = displayString.substring(0, index);

        if (index < displayString.length) {
            const nextDelay = (index === displayString.length - 1) ? LAST_DIGIT_PAUSE : DIGIT_INTERVAL;
            setTimeout(step, nextDelay);
        } else {
            step();
        }
    }

    step();
}

function showJudgement(result) {
    if (result === "perfect") {
        resultType.textContent = "お前が一番";
        resultType.classList.add("perfect");
        resultReward.textContent = "🌈 11個獲得！";
        resultReward.classList.add("show");

        flash.classList.remove("active");
        void flash.offsetWidth;
        flash.classList.add("active");

        effect.classList.remove("shake");
        void effect.offsetWidth;
        effect.classList.add("shake");

        createParticles(120);
    } else if (result === "great") {
        resultType.textContent = "11番目くらいには最高";
        resultType.classList.add("great");
        resultReward.textContent = "✨ 3個獲得！";
        resultReward.classList.add("show");
    }

    setTimeout(() => {
        retryHint.classList.add("show");
    }, 600);
}


/* =========================
   ランダム分配アルゴリズム
   ========================= */

function allocateItems(total) {
    let res = [0, 0, 0];
    for (let i = 0; i < total; i++) {
        const r = Math.floor(Math.random() * 3);
        res[r]++;
    }
    return res;
}


/* =========================
   景品分配ルーレット演出
   ========================= */

function startRoulette() {
    rouletteOverlay.classList.add("show");
    rouletteSpinning = true;
    rouletteReadyToClose = false;

    rouletteTitle.textContent = `🎁 合計 ${earnedCount}個 を分配中… 🎁`;

    [slot0, slot1, slot2].forEach(s => {
        s.classList.remove("decided");
        s.textContent = "0";
    });

    rouletteResultText.textContent = "";
    rouletteHint.textContent = "タップしてストップ！";
    rouletteHint.classList.add("show");

    // 事前に今回のランダム分配結果を計算
    finalAllocation = allocateItems(earnedCount);

    // ドラム高速回転（ランダムな数値をパラパラ表示）
    rouletteTimer = setInterval(() => {
        slot0.textContent = Math.floor(Math.random() * (earnedCount + 1));
        slot1.textContent = Math.floor(Math.random() * (earnedCount + 1));
        slot2.textContent = Math.floor(Math.random() * (earnedCount + 1));
    }, 40);

    // 2.2秒後に自動停止
    setTimeout(() => {
        if (rouletteSpinning) {
            stopRoulette();
        }
    }, 2200);
}

function stopRoulette() {
    if (!rouletteSpinning) return;

    rouletteSpinning = false;
    clearInterval(rouletteTimer);

    // 確定結果を画面にセット
    slot0.textContent = finalAllocation[0];
    slot1.textContent = finalAllocation[1];
    slot2.textContent = finalAllocation[2];

    [slot0, slot1, slot2].forEach(s => s.classList.add("decided"));

    // テキスト形式のサマリー作成
    const names = ["ポッキー", "プリッツ", "トッポ"];
    let summaryArr = [];
    for (let i = 0; i < 3; i++) {
        if (finalAllocation[i] > 0) {
            summaryArr.push(`${names[i]}×${finalAllocation[i]}`);
        }
    }

    rouletteResultText.textContent = "🎉 " + summaryArr.join("、") + " 獲得！";
    rouletteHint.textContent = "タップして確定";

    // フラッシュ演出
    flash.classList.remove("active");
    void flash.offsetWidth;
    flash.classList.add("active");

    setTimeout(() => {
        rouletteReadyToClose = true;
    }, 300);
}

function closeRouletteAndReset() {
    rouletteOverlay.classList.remove("show");
    rouletteReadyToClose = false;

    // テキストサマリー作成
    const names = ["ポッキー", "プリッツ", "トッポ"];
    let summaryArr = [];
    for (let i = 0; i < 3; i++) {
        if (finalAllocation[i] > 0) {
            summaryArr.push(`${names[i]} ${finalAllocation[i]}個`);
        }
    }

    // メイン画面に獲得内訳を記載
    timer.textContent = displayTimeStr;
    message.textContent = "もう一度チャレンジ！";
    reward.textContent = `🎁 前回獲得：${summaryArr.join(" / ")}`;
}


/* =========================
   パーティクル生成
   ========================= */

function createParticles(count) {
    for (let i = 0; i < count; i++) {
        const particle = document.createElement("div");
        particle.className = "particle";

        const x = window.innerWidth / 2;
        const y = window.innerHeight / 2;

        particle.style.left = `${x}px`;
        particle.style.top = `${y}px`;

        const angle = Math.random() * Math.PI * 2;
        const velocity = Math.random() * 320 + 100;
        const tx = Math.cos(angle) * velocity;
        const ty = Math.sin(angle) * velocity;

        effect.appendChild(particle);

        particle.animate([
            { transform: 'translate(0, 0) scale(1)', opacity: 1 },
            { transform: `translate(${tx}px, ${ty}px) scale(0)`, opacity: 0 }
        ], {
            duration: Math.random() * 1000 + 800,
            easing: 'cubic-bezier(0.1, 0.8, 0.3, 1)',
            fill: 'forwards'
        });
    }
}

</script>
</body>
</html>
