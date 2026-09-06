```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>アキラウォーズクリッカー</title>

<style>
body {
    margin: 0;
    padding: 20px;
    text-align: center;
    font-family: sans-serif;
    background: #eeeeee;
}

h1 {
    margin-top: 10px;
    font-size: 30px;
}

#points {
    font-size: 32px;
    font-weight: bold;
}

#clickButton {
    border: none;
    background: none;
    padding: 0;
    cursor: pointer;
    -webkit-tap-highlight-color: transparent;
}

#clickButton img {
    width: 280px;
    max-width: 80vw;
    border-radius: 20px;
    transition: transform 0.1s;
}

#clickButton:active img {
    transform: scale(0.9);
}

.shop {
    max-width: 450px;
    margin: 25px auto;
    padding: 20px;
    background: white;
    border-radius: 15px;
}

.shop h2 {
    margin-top: 0;
}

.upgrade {
    width: 100%;
    margin: 10px 0;
    padding: 15px;
    font-size: 17px;
    border: none;
    border-radius: 10px;
    background: #dddddd;
}

.upgrade:active {
    transform: scale(0.97);
}

.info {
    font-size: 16px;
}

#bgmButton,
#resetButton {
    margin: 8px;
    padding: 12px 20px;
    font-size: 16px;
    border: none;
    border-radius: 10px;
}

#resetButton {
    background: #cccccc;
}

#message {
    min-height: 24px;
    font-weight: bold;
}
</style>
</head>

<body>

<h1>アキラウォーズクリッカー</h1>

<h2>
💰 ポイント：<span id="points">0</span>
</h2>

<p>
1クリック：<span id="clickPower">1</span>ポイント<br>
1秒あたり：<span id="perSecond">0</span>ポイント
</p>

<!-- クリックする画像 -->
<button id="clickButton">
    <img
        src="https://cdn.discordapp.com/attachments/1529035810740113410/1546016258405965894/image-49.jpg?ex=6a9e3f57&is=6a9cedd7&hm=e30372c521d5f4db0e30a93a6db33be44117febb75735a5ab729c12432d1cf0f&"
        alt="アキラ"
    >
</button>

<p>画像をタップしてポイントを集めよう！</p>

<!-- 強化ショップ -->
<div class="shop">

    <h2>🛒 強化ショップ</h2>

    <button class="upgrade" onclick="buyClickUpgrade()">
        ⬆️ クリック強化<br>
        <span id="clickUpgradeText">
            価格：10ポイント
        </span>
    </button>

    <button class="upgrade" onclick="buyAutoUpgrade()">
        🤖 自動ポイント強化<br>
        <span id="autoUpgradeText">
            価格：25ポイント
        </span>
    </button>

</div>

<p id="message"></p>

<!-- BGM -->
<audio id="bgm" loop>
    <source
        src="https://media.discordapp.net/attachments/1529035810740113410/1546017608225005578/5f264ed13199eb1d.mp4?ex=6a9e4099&is=6a9cef19&hm=64457281013ec6c5edb863714086da4e3341fe09fc73b6e241fbb31193f1fdf3&"
        type="video/mp4"
    >
</audio>

<button id="bgmButton" onclick="toggleBGM()">
    🎵 BGMを再生
</button>

<button id="resetButton" onclick="resetGame()">
    🔄 ゲームをリセット
</button>


<script>

/* =========================
   ゲームデータ
========================= */

let points = Number(localStorage.getItem("points")) || 0;
let clickPower = Number(localStorage.getItem("clickPower")) || 1;
let perSecond = Number(localStorage.getItem("perSecond")) || 0;

let clickUpgradePrice =
    Number(localStorage.getItem("clickUpgradePrice")) || 10;

let autoUpgradePrice =
    Number(localStorage.getItem("autoUpgradePrice")) || 25;


/* =========================
   画面更新
========================= */

function update() {

    document.getElementById("points").textContent =
        Math.floor(points);

    document.getElementById("clickPower").textContent =
        clickPower;

    document.getElementById("perSecond").textContent =
        perSecond;

    document.getElementById("clickUpgradeText").textContent =
        "価格：" + clickUpgradePrice + "ポイント";

    document.getElementById("autoUpgradeText").textContent =
        "価格：" + autoUpgradePrice + "ポイント";

    saveGame();
}


/* =========================
   クリック
========================= */

document.getElementById("clickButton").onclick = function() {

    points += clickPower;

    update();

    startBGM();
};


/* =========================
   クリック強化
========================= */

function buyClickUpgrade() {

    if (points >= clickUpgradePrice) {

        points -= clickUpgradePrice;

        clickPower++;

        clickUpgradePrice =
            Math.floor(clickUpgradePrice * 1.5);

        showMessage("⬆️ クリック力がアップしました！");

        update();

    } else {

        showMessage("❌ ポイントが足りません！");
    }
}


/* =========================
   自動ポイント強化
========================= */

function buyAutoUpgrade() {

    if (points >= autoUpgradePrice) {

        points -= autoUpgradePrice;

        perSecond++;

        autoUpgradePrice =
            Math.floor(autoUpgradePrice * 1.6);

        showMessage("🤖 自動ポイントがアップしました！");

        update();

    } else {

        showMessage("❌ ポイントが足りません！");
    }
}


/* =========================
   自動ポイント
========================= */

setInterval(function() {

    if (perSecond > 0) {

        points += perSecond;

        update();
    }

}, 1000);


/* =========================
   メッセージ
========================= */

function showMessage(text) {

    document.getElementById("message").textContent = text;

    setTimeout(function() {

        document.getElementById("message").textContent = "";

    }, 1500);
}


/* =========================
   BGM
========================= */

let bgmPlaying = false;

function startBGM() {

    const bgm = document.getElementById("bgm");

    bgm.volume = 0.5;

    if (bgm.paused) {

        bgm.play().then(function() {

            bgmPlaying = true;

            document.getElementById("bgmButton").textContent =
                "🔇 BGMを停止";

        }).catch(function() {

            // スマホの自動再生制限などの場合
        });
    }
}


function toggleBGM() {

    const bgm = document.getElementById("bgm");

    if (bgm.paused) {

        startBGM();

    } else {

        bgm.pause();

        bgmPlaying = false;

        document.getElementById("bgmButton").textContent =
            "🎵 BGMを再生";
    }
}


/* =========================
   セーブ
========================= */

function saveGame() {

    localStorage.setItem("points", points);
    localStorage.setItem("clickPower", clickPower);
    localStorage.setItem("perSecond", perSecond);

    localStorage.setItem(
        "clickUpgradePrice",
        clickUpgradePrice
    );

    localStorage.setItem(
        "autoUpgradePrice",
        autoUpgradePrice
    );
}


/* =========================
   リセット
========================= */

function resetGame() {

    const answer =
        confirm("本当にゲームを最初からやり直しますか？");

    if (answer) {

        localStorage.clear();

        points = 0;
        clickPower = 1;
        perSecond = 0;

        clickUpgradePrice = 10;
        autoUpgradePrice = 25;

        update();

        showMessage("🔄 ゲームをリセットしました！");
    }
}


/* =========================
   最初の画面更新
========================= */

update();

</script>

</body>
</html>
```
