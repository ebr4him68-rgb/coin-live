<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>بازار آنلاین ارز دیجیتال</title>

<style>
*{
    box-sizing:border-box;
}

body{
    margin:0;
    font-family:Tahoma,Arial,sans-serif;
    background:linear-gradient(135deg,#001f4d,#0066cc,#00a8ff);
    min-height:100vh;
    color:#fff;
}

header{
    text-align:center;
    padding:25px 10px;
    background:rgba(0,0,0,.25);
    border-bottom:1px solid rgba(255,255,255,.2);
}

header h1{
    margin:0;
    font-size:26px;
}

header p{
    margin:10px 0 0;
    color:#dff5ff;
}

.live{
    display:inline-block;
    margin-top:12px;
    padding:7px 15px;
    border-radius:20px;
    background:#063d63;
    font-size:12px;
}

.live span{
    display:inline-block;
    width:9px;
    height:9px;
    background:#00ff66;
    border-radius:50%;
    margin-left:6px;
    animation:blink 1s infinite;
}

@keyframes blink{
    0%,100%{opacity:1}
    50%{opacity:.2}
}

.container{
    width:95%;
    max-width:900px;
    margin:20px auto;
}

.status{
    text-align:center;
    padding:15px;
    margin-bottom:15px;
    border-radius:15px;
    background:rgba(0,0,0,.2);
}

.coin{
    display:grid;
    grid-template-columns:45px 1fr 1fr 50px;
    align-items:center;
    gap:10px;

    background:rgba(255,255,255,.13);
    border:1px solid rgba(255,255,255,.15);

    margin-bottom:9px;
    padding:12px;

    border-radius:15px;

    backdrop-filter:blur(8px);

    transition:.2s;
}

.coin:hover{
    background:rgba(255,255,255,.22);
    transform:translateY(-2px);
}

.logo{
    width:38px;
    height:38px;
    border-radius:50%;
}

.name{
    font-weight:bold;
    font-size:14px;
}

.symbol{
    color:#b9eaff;
    font-size:11px;
    margin-top:4px;
    text-transform:uppercase;
}

.price{
    text-align:center;
    font-weight:bold;
    direction:ltr;
}

.change{
    font-size:11px;
    margin-top:5px;
}

.green{
    color:#00ff8a;
}

.red{
    color:#ff7777;
}

.eye{
    width:38px;
    height:38px;

    border:0;
    border-radius:50%;

    background:#00eaff;
    color:#00304c;

    font-size:18px;

    cursor:pointer;

    animation:eye 1.2s infinite;

    box-shadow:0 0 8px #00eaff;
}

@keyframes eye{
    0%,100%{
        opacity:1;
        transform:scale(1);
    }

    50%{
        opacity:.45;
        transform:scale(.82);
    }
}

.refresh{
    width:100%;
    padding:14px;

    border:0;
    border-radius:14px;

    background:#00eaff;
    color:#00304c;

    font-size:15px;
    font-weight:bold;

    cursor:pointer;

    margin-bottom:15px;
}

@media(max-width:600px){

    .coin{
        grid-template-columns:40px 1fr 100px 38px;
        gap:6px;
        padding:9px;
    }

    .name{
        font-size:12px;
    }

    .price{
        font-size:12px;
    }

    .eye{
        width:34px;
        height:34px;
        font-size:15px;
    }
}
</style>
</head>

<body>

<header>

<h1>💎 بازار آنلاین ارز دیجیتال</h1>

<p>قیمت لحظه‌ای ۵۰ ارز دیجیتال</p>

<div class="live">
<span></span>
LIVE
</div>

</header>


<div class="container">

<button class="refresh" onclick="loadCoins()">
🔄 بروزرسانی قیمت‌ها
</button>

<div id="status" class="status">
⏳ در حال دریافت ۵۰ ارز...
</div>

<div id="coins"></div>

</div>


<script>

const coinsBox = document.getElementById("coins");
const statusBox = document.getElementById("status");


async function loadCoins(){

    statusBox.innerHTML = "⏳ دریافت قیمت‌های آنلاین...";

    try{

        const url =
        "https://api.coingecko.com/api/v3/coins/markets" +
        "?vs_currency=usd" +
        "&order=market_cap_desc" +
        "&per_page=50" +
        "&page=1" +
        "&sparkline=false" +
        "&price_change_percentage=24h";

        const response = await fetch(url);

        if(!response.ok){
            throw new Error("API ERROR");
        }

        const data = await response.json();

        if(!Array.isArray(data) || data.length < 50){
            throw new Error("50 COINS NOT FOUND");
        }

        coinsBox.innerHTML = "";

        data.slice(0,50).forEach((coin,index)=>{

            const change =
                Number(coin.price_change_percentage_24h || 0);

            const changeClass =
                change >= 0 ? "green" : "red";

            const arrow =
                change >= 0 ? "▲" : "▼";

            let price;

            if(coin.current_price >= 1){

                price =
                "$" +
                Number(coin.current_price)
                .toLocaleString("en-US",{
                    minimumFractionDigits:2,
                    maximumFractionDigits:2
                });

            }else{

                price =
                "$" +
                Number(coin.current_price)
                .toLocaleString("en-US",{
                    maximumSignificantDigits:6
                });

            }

            const div = document.createElement("div");

            div.className = "coin";

            div.innerHTML = `

                <img
                    class="logo"
                    src="${coin.image}"
                    alt="${coin.name}"
                >

                <div>

                    <div class="name">
                        ${index + 1}. ${coin.name}
                    </div>

                    <div class="symbol">
                        ${coin.symbol}
                    </div>

                </div>


                <div class="price">

                    ${price}

                    <div class="change ${changeClass}">

                        ${arrow}
                        ${Math.abs(change).toFixed(2)}%

                    </div>

                </div>


                <button
                    class="eye"
                    onclick="coinInfo('${coin.name}')"
                >
                    👁
                </button>

            `;

            coinsBox.appendChild(div);

        });


        statusBox.innerHTML =
        "🟢 آنلاین | ۵۰ ارز با موفقیت دریافت شد | بروزرسانی خودکار هر ۶۰ ثانیه";


    }catch(error){

        console.error(error);

        statusBox.innerHTML =
        "🔴 اتصال به سرویس قیمت برقرار نشد. روی «بروزرسانی» بزنید.";

    }

}


function coinInfo(name){

    alert(
        "💰 " + name +
        "\n\nقیمت لحظه‌ای این ارز در لیست بالا نمایش داده می‌شود."
    );

}


// بار اول
loadCoins();


// بروزرسانی هر ۶۰ ثانیه
setInterval(loadCoins,60000);

</script>

</body>
</html>
