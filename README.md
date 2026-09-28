<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>Crypto 30 Minute Prediction</title>

<style>
*{box-sizing:border-box}

body{
    margin:0;
    font-family:Tahoma,Arial,sans-serif;
    color:white;
    background:linear-gradient(135deg,#031b45,#075db5,#00a9e8);
    min-height:100vh;
}

header{
    text-align:center;
    padding:24px 12px;
    background:rgba(0,0,0,.25);
}

header h1{
    margin:0;
    font-size:25px;
}

header p{
    color:#d8f4ff;
    font-size:13px;
}

.container{
    width:94%;
    max-width:900px;
    margin:20px auto;
}

.card{
    background:rgba(0,20,60,.45);
    border:1px solid rgba(255,255,255,.16);
    border-radius:20px;
    padding:18px;
    margin-bottom:18px;
    box-shadow:0 10px 35px rgba(0,0,0,.2);
    backdrop-filter:blur(10px);
}

label{
    display:block;
    margin:10px 0 7px;
    font-size:13px;
}

select,
input{
    width:100%;
    padding:13px;
    border:none;
    outline:none;
    border-radius:12px;
    font-size:14px;
    background:white;
    color:#123;
}

.price{
    text-align:center;
    margin:18px 0;
}

.price small{
    display:block;
    color:#bdeaff;
    margin-bottom:7px;
}

.price strong{
    font-size:25px;
    direction:ltr;
    display:block;
}

.address-note{
    font-size:11px;
    color:#bfe8ff;
    margin-top:6px;
}

.directions{
    display:flex;
    gap:10px;
    margin-top:15px;
}

.direction{
    flex:1;
    border:2px solid transparent;
    border-radius:14px;
    padding:15px 5px;
    font-size:18px;
    font-weight:bold;
    cursor:pointer;
    background:#123c68;
    color:white;
}

.direction.up{
    border-color:#00ff91;
}

.direction.down{
    border-color:#ff7777;
}

.direction.selected{
    background:#00dca0;
    color:#003d32;
    box-shadow:0 0 20px rgba(0,255,150,.5);
}

.direction.down.selected{
    background:#ff5555;
    color:white;
    box-shadow:0 0 20px rgba(255,60,60,.5);
}

button#start{
    width:100%;
    margin-top:16px;
    padding:15px;
    border:0;
    border-radius:14px;
    font-size:16px;
    font-weight:bold;
    cursor:pointer;
    background:#777;
    color:#ccc;
}

button#start.enabled{
    background:#00eaff;
    color:#003653;
    box-shadow:0 0 20px rgba(0,234,255,.45);
}

button#start:disabled{
    cursor:not-allowed;
}

.timer{
    text-align:center;
    display:none;
}

.timer-title{
    color:#ccefff;
    margin-bottom:10px;
}

.time{
    font-size:42px;
    font-weight:bold;
    direction:ltr;
    letter-spacing:3px;
}

.progress{
    height:10px;
    background:rgba(255,255,255,.15);
    border-radius:10px;
    overflow:hidden;
    margin-top:15px;
}

.progress-bar{
    height:100%;
    width:0%;
    background:#00eaff;
    transition:width 1s linear;
}

.result{
    display:none;
    text-align:center;
    padding:20px;
}

.result.win{
    border:2px solid #00ff91;
}

.result.loss{
    border:2px solid #ff5555;
}

.coin-list-title{
    text-align:center;
    margin-bottom:15px;
}

.coin{
    display:grid;
    grid-template-columns:42px 1fr 120px 42px;
    align-items:center;
    gap:8px;
    padding:10px;
    margin-bottom:8px;
    border-radius:14px;
    background:rgba(255,255,255,.1);
}

.coin img{
    width:36px;
    height:36px;
    border-radius:50%;
}

.coin-name{
    font-size:13px;
    font-weight:bold;
}

.coin-symbol{
    color:#b9eaff;
    font-size:10px;
    margin-top:3px;
}

.coin-price{
    text-align:center;
    direction:ltr;
    font-size:12px;
    font-weight:bold;
}

.eye{
    width:38px;
    height:38px;
    border:0;
    border-radius:50%;
    background:#00eaff;
    cursor:pointer;
    animation:blink 1.2s infinite;
}

@keyframes blink{
    0%,100%{
        opacity:1;
        transform:scale(1);
    }
    50%{
        opacity:.45;
        transform:scale(.8);
    }
}

.status{
    text-align:center;
    padding:10px;
    font-size:12px;
    color:#d5f4ff;
}

.error{
    color:#ff8d8d;
}

@media(max-width:600px){

    .coin{
        grid-template-columns:38px 1fr 95px 38px;
    }

    .coin-name{
        font-size:11px;
    }

    .coin-price{
        font-size:10px;
    }

    .time{
        font-size:35px;
    }
}
</style>
</head>

<body>

<header>
    <h1>💎 پیش‌بینی ۳۰ دقیقه‌ای ارز دیجیتال</h1>
    <p>قیمت آنلاین ۵۰ ارز دیجیتال</p>
</header>

<div class="container">

    <!-- Prediction -->
    <div class="card">

        <h3>🎯 ثبت پیش‌بینی</h3>

        <label>انتخاب ارز</label>

        <select id="coinSelect" onchange="coinChanged()">
            <option value="">⏳ در حال دریافت ارزها...</option>
        </select>

        <div class="price">
            <small>قیمت فعلی</small>
            <strong id="currentPrice">---</strong>
        </div>

        <label id="addressLabel">
            آدرس کیف پول
        </label>

        <input
            id="walletAddress"
            type="text"
            placeholder="ابتدا یک ارز انتخاب کنید"
            disabled
            oninput="checkForm()"
        >

        <div class="address-note" id="addressNote">
            برای ثبت پیش‌بینی باید آدرس همان ارز را وارد کنید.
        </div>

        <div class="directions">

            <button
                id="upBtn"
                class="direction up"
                onclick="chooseDirection('up')"
                disabled
            >
                ⬆️ بالا
            </button>

            <button
                id="downBtn"
                class="direction down"
                onclick="chooseDirection('down')"
                disabled
            >
                ⬇️ پایین
            </button>

        </div>

        <button
            id="start"
            onclick="startPrediction()"
            disabled
        >
            🔒 ابتدا آدرس ارز را وارد کنید
        </button>

        <div id="formStatus" class="status"></div>

    </div>


    <!-- Timer -->

    <div class="card timer" id="timerBox">

        <div class="timer-title">
            ⏱️ پیش‌بینی شما در حال اجراست
        </div>

        <div id="timer" class="time">
            30:00
        </div>

        <div class="progress">
            <div id="progressBar" class="progress-bar"></div>
        </div>

        <div class="status" id="predictionInfo"></div>

    </div>


    <!-- Result -->

    <div class="card result" id="resultBox">

        <h2 id="resultTitle"></h2>

        <p id="resultText"></p>

        <button
            id="newPrediction"
            class="refresh"
            onclick="location.reload()"
        >
            🔄 پیش‌بینی جدید
        </button>

    </div>


    <!-- 50 Coins -->

    <div class="card">

        <h3 class="coin-list-title">
            📊 ۵۰ ارز دیجیتال آنلاین
        </h3>

        <div id="status" class="status">
            ⏳ دریافت قیمت‌ها...
        </div>

        <div id="coinList"></div>

    </div>

</div>


<script>

let coins = [];
let selectedCoin = null;
let selectedDirection = null;
let startPrice = 0;
let endTime = 0;
let timerInterval = null;


/* API */

async function loadCoins(){

    const status =
        document.getElementById("status");

    try{

        const url =
        "https://api.coingecko.com/api/v3/coins/markets" +
        "?vs_currency=usd" +
        "&order=market_cap_desc" +
        "&per_page=50" +
        "&page=1" +
        "&sparkline=false" +
        "&price_change_percentage=24h";

        const response =
            await fetch(url);

        if(!response.ok)
            throw new Error("API");

        coins =
            await response.json();

        if(coins.length < 50)
            throw new Error("50 coins");

        status.innerHTML =
            "🟢 قیمت ۵۰ ارز آنلاین است";

        createSelect();
        createList();

    }catch(e){

        status.innerHTML =
            "🔴 دریافت قیمت‌ها ناموفق بود. صفحه را دوباره باز کنید.";

    }
}


/* Select */

function createSelect(){

    const select =
        document.getElementById("coinSelect");

    select.innerHTML =
        '<option value="">انتخاب ارز...</option>';

    coins.forEach((coin,index)=>{

        const option =
            document.createElement("option");

        option.value = index;

        option.textContent =
            (index+1) +
            " - " +
            coin.name +
            " (" +
            coin.symbol.toUpperCase() +
            ")";

        select.appendChild(option);

    });
}


/* List */

function createList(){

    const list =
        document.getElementById("coinList");

    list.innerHTML = "";

    coins.forEach((coin,index)=>{

        const row =
            document.createElement("div");

        row.className = "coin";

        row.innerHTML = `

            <img src="${coin.image}">

            <div>
                <div class="coin-name">
                    ${index+1}. ${coin.name}
                </div>

                <div class="coin-symbol">
                    ${coin.symbol.toUpperCase()}
                </div>
            </div>

            <div class="coin-price">
                ${formatPrice(coin.current_price)}
            </div>

            <button
                class="eye"
                onclick="selectCoin(${index})"
            >
                👁
            </button>

        `;

        list.appendChild(row);

    });
}


/* Select coin */

function selectCoin(index){

    document.getElementById("coinSelect").value =
        index;

    coinChanged();

    window.scrollTo({
        top:0,
        behavior:"smooth"
    });
}


/* Coin changed */

function coinChanged(){

    const value =
        document.getElementById("coinSelect").value;

    if(value === ""){

        selectedCoin = null;

        document.getElementById("walletAddress").disabled =
            true;

        document.getElementById("upBtn").disabled =
            true;

        document.getElementById("downBtn").disabled =
            true;

        document.getElementById("currentPrice").innerText =
            "---";

        checkForm();

        return;
    }

    selectedCoin =
        coins[Number(value)];

    document.getElementById("currentPrice").innerText =
        formatPrice(selectedCoin.current_price);

    const symbol =
        selectedCoin.symbol.toUpperCase();

    document.getElementById("addressLabel").innerText =
        "آدرس کیف پول " + symbol;

    document.getElementById("walletAddress").placeholder =
        "آدرس " + symbol + " خود را وارد کنید";

    document.getElementById("walletAddress").disabled =
        false;

    document.getElementById("upBtn").disabled =
        false;

    document.getElementById("downBtn").disabled =
        false;

    selectedDirection = null;

    document.getElementById("upBtn")
        .classList.remove("selected");

    document.getElementById("downBtn")
        .classList.remove("selected");

    checkForm();
}


/* Direction */

function chooseDirection(direction){

    if(!selectedCoin)
        return;

    selectedDirection =
        direction;

    document.getElementById("upBtn")
        .classList.toggle(
            "selected",
            direction === "up"
        );

    document.getElementById("downBtn")
        .classList.toggle(
            "selected",
            direction === "down"
        );

    checkForm();
}


/* Address validation */

function validAddress(address){

    address =
        address.trim();

    if(!address)
        return false;

    const symbol =
        selectedCoin.symbol.toUpperCase();

    /*
       این قسمت برای نسخه نمایشی است.
       اعتبارسنجی کامل هر شبکه باید جداگانه انجام شود.
    */

    if(symbol === "TRX"){
        return address.startsWith("T")
            && address.length >= 30
            && address.length <= 36;
    }

    if(symbol === "DOGE"){
        return address.startsWith("D")
            && address.length >= 30
            && address.length <= 40;
    }

    if(symbol === "BTC"){
        return (
            address.startsWith("1") ||
            address.startsWith("3") ||
            address.startsWith("bc1")
        );
    }

    if(symbol === "ETH"){
        return /^0x[a-fA-F0-9]{40}$/.test(address);
    }

    /* برای سایر ارزها حداقل بررسی طول */
    return address.length >= 20;
}


/* Check form */

function checkForm(){

    const address =
        document.getElementById("walletAddress").value;

    const start =
        document.getElementById("start");

    if(
        selectedCoin &&
        validAddress(address) &&
        selectedDirection
    ){

        start.disabled = false;

        start.classList.add("enabled");

        start.innerText =
            "🚀 ثبت پیش‌بینی و شروع ۳۰ دقیقه";

        document.getElementById("formStatus")
            .innerText =
            "🟢 اطلاعات کامل است";

    }else{

        start.disabled = true;

        start.classList.remove("enabled");

        start.innerText =
            "🔒 ابتدا آدرس همان ارز و جهت را وارد کنید";

        document.getElementById("formStatus")
            .innerText =
            "⚠️ برای ثبت پیش‌بینی، آدرس همان ارز + بالا یا پایین را انتخاب کنید.";

    }
}


/* Start */

function startPrediction(){

    const address =
        document.getElementById("walletAddress")
        .value.trim();

    if(!selectedCoin ||
       !validAddress(address) ||
       !selectedDirection){

        return;
    }

    startPrice =
        Number(selectedCoin.current_price);

    /*
       30 دقیقه
    */

    endTime =
        Date.now() +
        (30 * 60 * 1000);

    document.querySelector(".card")
        .style.display = "none";

    document.getElementById("timerBox")
        .style.display = "block";

    document.getElementById("predictionInfo")
        .innerHTML =
        "🪙 " +
        selectedCoin.name +
        " | قیمت شروع: " +
        formatPrice(startPrice) +
        "<br>پیش‌بینی: " +
        (selectedDirection === "up"
            ? "⬆️ بالا"
            : "⬇️ پایین");

    timerInterval =
        setInterval(updateTimer,1000);

    updateTimer();
}


/* Timer */

function updateTimer(){

    const remaining =
        endTime - Date.now();

    if(remaining <= 0){

        clearInterval(timerInterval);

        document.getElementById("timer")
            .innerText = "00:00";

        finishPrediction();

        return;
    }

    const totalSeconds =
        Math.floor(remaining / 1000);

    const minutes =
        Math.floor(totalSeconds / 60);

    const seconds =
        totalSeconds % 60;

    document.getElementById("timer")
        .innerText =
        String(minutes).padStart(2,"0") +
        ":" +
        String(seconds).padStart(2,"0");

    const elapsed =
        (30*60*1000) -
        remaining;

    const percent =
        Math.min(
            100,
            (elapsed/(30*60*1000))*100
        );

    document.getElementById("progressBar")
        .style.width =
        percent + "%";
}


/* Finish */

async function finishPrediction(){

    try{

        /*
          دریافت قیمت نهایی
        */

        const url =
        "https://api.coingecko.com/api/v3/simple/price" +
        "?ids=" +
        selectedCoin.id +
        "&vs_currencies=usd";

        const response =
            await fetch(url);

        const data =
            await response.json();

        const finalPrice =
            Number(
                data[selectedCoin.id].usd
            );

        showResult(finalPrice);

    }catch(e){

        document.getElementById("resultBox")
            .style.display = "block";

        document.getElementById("resultTitle")
            .innerText =
            "⚠️ قیمت نهایی دریافت نشد";

        document.getElementById("resultText")
            .innerText =
            "برای مشخص‌شدن نتیجه، قیمت نهایی باید از سرویس قیمت دریافت شود.";

    }
}


/* Result */

function showResult(finalPrice){

    const result =
        document.getElementById("resultBox");

    const title =
        document.getElementById("resultTitle");

    const text =
        document.getElementById("resultText");

    const difference =
        finalPrice - startPrice;

    let win = false;

    if(selectedDirection === "up"){
        win = difference > 0;
    }

    if(selectedDirection === "down"){
        win = difference < 0;
    }

    result.style.display = "block";

    result.classList.remove(
        "win",
        "loss"
    );

    result.classList.add(
        win ? "win" : "loss"
    );

    title.innerText =
        win
        ? "🎉 پیش‌بینی درست بود"
        : "❌ پیش‌بینی درست نبود";

    text.innerHTML =

        "ارز: <b>" +
        selectedCoin.name +
        "</b><br><br>" +

        "قیمت شروع: " +
        formatPrice(startPrice) +
        "<br>" +

        "قیمت پایان: " +
        formatPrice(finalPrice) +
        "<br><br>" +

        "پیش‌بینی شما: " +
        (selectedDirection === "up"
            ? "⬆️ بالا"
            : "⬇️ پایین");

}


/* Format */

function formatPrice(price){

    price = Number(price);

    if(price >= 1){

        return "$" +
        price.toLocaleString(
            "en-US",
            {
                minimumFractionDigits:2,
                maximumFractionDigits:2
            }
        );

    }

    return "$" +
    price.toLocaleString(
        "en-US",
        {
            maximumSignificantDigits:7
        }
    );
}


/* Start */

loadCoins();

</script>
<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>آدرس واریز ارزها</title>

<style>
*{
    box-sizing:border-box;
}

body{
    margin:0;
    font-family:Arial,sans-serif;
    background:#071a35;
    color:white;
}

.header{
    background:#0d2d55;
    padding:20px 15px;
    text-align:center;
    font-size:23px;
    font-weight:bold;
}

.container{
    max-width:700px;
    margin:auto;
    padding:15px;
}

.coin{
    background:#10345f;
    margin-bottom:8px;
    padding:17px;
    border-radius:14px;
    display:flex;
    align-items:center;
    justify-content:space-between;
    cursor:pointer;
    transition:.2s;
}

.coin:active{
    transform:scale(.98);
}

.coin:hover{
    background:#174576;
}

.coin-info{
    text-align:right;
}

.coin-name{
    font-size:18px;
    font-weight:bold;
}

.coin-symbol{
    color:#a9c4df;
    font-size:13px;
    margin-top:5px;
}

.arrow{
    font-size:25px;
}

.deposit{
    display:none;
    background:white;
    color:#111;
    padding:17px;
    margin-top:-8px;
    margin-bottom:10px;
    border-radius:0 0 14px 14px;
}

.deposit-title{
    font-weight:bold;
    margin-bottom:10px;
}

.address{
    direction:ltr;
    text-align:left;
    word-break:break-all;
    background:#f1f3f5;
    border:1px solid #ddd;
    border-radius:10px;
    padding:13px;
    font-size:14px;
    line-height:1.6;
}

.copy-btn{
    width:100%;
    margin-top:11px;
    padding:13px;
    border:0;
    border-radius:10px;
    background:#16c784;
    color:white;
    font-size:16px;
    font-weight:bold;
    cursor:pointer;
}

.copy-btn:active{
    transform:scale(.98);
}
</style>
</head>

<body>

<div class="header">
    💰 آدرس واریز ارزهای دیجیتال
</div>

<div class="container">

<!-- 1 BTC -->
<div class="coin" onclick="toggleDeposit('btc')">
    <div class="coin-info">
        <div class="coin-name">Bitcoin</div>
        <div class="coin-symbol">BTC</div>
    </div>
    <div class="arrow">‹</div>
</div>

<div class="deposit" id="btc">
    <div class="deposit-title">آدرس واریز Bitcoin</div>

    <div class="address" id="btcAddress">
        1Q99GpYnEU9yELNLjiJUWopNT1HatRYQrV
    </div>

    <button class="copy-btn" onclick="copyAddress('btcAddress',this)">
        📋 کپی آدرس
    </button>
</div>


<!-- 2 ETH -->
<div class="coin" onclick="toggleDeposit('eth')">
    <div class="coin-info">
        <div class="coin-name">Ethereum</div>
        <div class="coin-symbol">ETH</div>
    </div>
    <div class="arrow">‹</div>
</div>

<div class="deposit" id="eth">
    <div class="deposit-title">آدرس واریز Ethereum</div>

    <div class="address" id="ethAddress">
        0x3765C083F36B7D874d3a6249436a84C9e9bDAbA6
    </div>

    <button class="copy-btn" onclick="copyAddress('ethAddress',this)">
        📋 کپی آدرس
    </button>
</div>


<!-- 3 TRX -->
<div class="coin" onclick="toggleDeposit('trx')">
    <div class="coin-info">
        <div class="coin-name">TRON</div>
        <div class="coin-symbol">TRX</div>
    </div>
    <div class="arrow">‹</div>
</div>

<div class="deposit" id="trx">
    <div class="deposit-title">آدرس واریز TRON</div>

    <div class="address" id="trxAddress">
        TRb33idZSi7svRyBTRsEKq8BfL54ADYMh3
    </div>

    <button class="copy-btn" onclick="copyAddress('trxAddress',this)">
        📋 کپی آدرس
    </button>
</div>


<!-- 4 ETH -->
<div class="coin" onclick="toggleDeposit('eth2')">
    <div class="coin-info">
        <div class="coin-name">Ethereum</div>
        <div class="coin-symbol">ETH - آدرس دوم</div>
    </div>
    <div class="arrow">‹</div>
</div>

<div class="deposit" id="eth2">
    <div class="deposit-title">آدرس واریز Ethereum</div>

    <div class="address" id="eth2Address">
        0x3765C083F36B7D874d3a6249436a84C9e9bDAbA6
    </div>

    <button class="copy-btn" onclick="copyAddress('eth2Address',this)">
        📋 کپی آدرس
    </button>
</div>


<!-- 5 SOL -->
<div class="coin" onclick="toggleDeposit('sol')">
    <div class="coin-info">
        <div class="coin-name">Solana</div>
        <div class="coin-symbol">SOL</div>
    </div>
    <div class="arrow">‹</div>
</div>

<div class="deposit" id="sol">
    <div class="deposit-title">آدرس واریز Solana</div>

    <div class="address" id="solAddress">
        rrheWe3u6o8zPRetY5jyaayURPmM6vnhww
    </div>

    <button class="copy-btn" onclick="copyAddress('solAddress',this)">
        📋 کپی آدرس
    </button>
</div>


<!-- 6 TRX -->
<div class="coin" onclick="toggleDeposit('trx2')">
    <div class="coin-info">
        <div class="coin-name">TRON</div>
        <div class="coin-symbol">TRX - آدرس دوم</div>
    </div>
    <div class="arrow">‹</div>
</div>

<div class="deposit" id="trx2">
    <div class="deposit-title">آدرس واریز TRON</div>

    <div class="address" id="trx2Address">
        TRb33idZSi7svRyBTRsEKq8BfL54ADYMh3
    </div>

    <button class="copy-btn" onclick="copyAddress('trx2Address',this)">
        📋 کپی آدرس
    </button>
</div>


<!-- 7 BCH -->
<div class="coin" onclick="toggleDeposit('bch')">
    <div class="coin-info">
        <div class="coin-name">Bitcoin Cash</div>
        <div class="coin-symbol">BCH</div>
    </div>
    <div class="arrow">‹</div>
</div>

<div class="deposit" id="bch">
    <div class="deposit-title">آدرس واریز Bitcoin Cash</div>

    <div class="address" id="bchAddress">
        bitcoincash:qrj64uh0xlah2wzksudq3g5eeg2ewdyg6urq5kywku
    </div>

    <button class="copy-btn" onclick="copyAddress('bchAddress',this)">
        📋 کپی آدرس
    </button>
</div>


<!-- 8 LTC -->
<div class="coin" onclick="toggleDeposit('ltc')">
    <div class="coin-info">
        <div class="coin-name">Litecoin</div>
        <div class="coin-symbol">LTC</div>
    </div>
    <div class="arrow">‹</div>
</div>

<div class="deposit" id="ltc">
    <div class="deposit-title">آدرس واریز Litecoin</div>

    <div class="address" id="ltcAddress">
        LZeRDFWbPLpuqeAw7m5i5YcYiu32KRAM6c
    </div>

    <button class="copy-btn" onclick="copyAddress('ltcAddress',this)">
        📋 کپی آدرس
    </button>
</div>

</div>


<script>

function toggleDeposit(id){

    const box = document.getElementById(id);

    // بستن همه بخش‌ها
    document.querySelectorAll(".deposit").forEach(function(item){
        if(item !== box){
            item.style.display = "none";
        }
    });

    // باز/بسته کردن بخش انتخاب‌شده
    if(box.style.display === "block"){
        box.style.display = "none";
    }else{
        box.style.display = "block";
    }
}


function copyAddress(id,button){

    const address =
        document.getElementById(id).innerText.trim();

    navigator.clipboard.writeText(address)
    .then(function(){

        const oldText = button.innerText;

        button.innerText = "✅ آدرس کپی شد";

        setTimeout(function(){
            button.innerText = oldText;
        },2000);

    })
    .catch(function(){

        const textarea =
            document.createElement("textarea");

        textarea.value = address;

        document.body.appendChild(textarea);

        textarea.select();
        textarea.setSelectionRange(0,99999);

        document.execCommand("copy");

        textarea.remove();

        button.innerText = "✅ آدرس کپی شد";

        setTimeout(function(){
            button.innerText = "📋 کپی آدرس";
        },2000);
    });
}

</script>

</body>
</html>
</body>
</html>
