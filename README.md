# coin-live
<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Crypto Live Market</title>

<style>
*{
    box-sizing:border-box;
    margin:0;
    padding:0;
    font-family:Tahoma,Arial,sans-serif;
}

body{
    background:
      radial-gradient(circle at top,#168cff 0%,#0758a8 35%,#032b5c 100%);
    min-height:100vh;
    color:white;
}

.header{
    padding:25px 15px;
    text-align:center;
    background:rgba(0,20,60,.35);
    box-shadow:0 4px 25px rgba(0,0,0,.25);
    position:sticky;
    top:0;
    z-index:10;
    backdrop-filter:blur(12px);
}

.header h1{
    font-size:27px;
    margin-bottom:8px;
}

.header p{
    color:#d9efff;
    font-size:13px;
}

.live{
    display:inline-flex;
    align-items:center;
    gap:7px;
    margin-top:12px;
    background:rgba(0,255,120,.12);
    border:1px solid rgba(0,255,120,.4);
    padding:7px 14px;
    border-radius:30px;
    font-size:12px;
}

.live-dot{
    width:9px;
    height:9px;
    background:#00ff73;
    border-radius:50%;
    animation:blink 1s infinite;
}

@keyframes blink{
    0%,100%{opacity:1;box-shadow:0 0 5px #00ff73}
    50%{opacity:.2;box-shadow:0 0 18px #00ff73}
}

.container{
    max-width:900px;
    margin:auto;
    padding:18px 12px 40px;
}

.market{
    display:flex;
    justify-content:space-between;
    align-items:center;
    padding:14px;
    margin-bottom:10px;
    background:rgba(255,255,255,.10);
    border:1px solid rgba(255,255,255,.12);
    border-radius:17px;
    backdrop-filter:blur(10px);
    transition:.2s;
}

.market:hover{
    transform:translateY(-2px);
    background:rgba(255,255,255,.16);
}

.coin{
    display:flex;
    align-items:center;
    gap:11px;
    min-width:150px;
}

.coin img{
    width:39px;
    height:39px;
    border-radius:50%;
}

.coin-name{
    font-weight:bold;
    font-size:14px;
}

.coin-symbol{
    color:#b9ddff;
    font-size:11px;
    margin-top:4px;
    text-transform:uppercase;
}

.price{
    text-align:center;
    font-size:15px;
    font-weight:bold;
}

.change{
    font-size:11px;
    margin-top:5px;
}

.green{
    color:#29ff91;
}

.red{
    color:#ff7474;
}

.eye{
    width:39px;
    height:39px;
    border:none;
    border-radius:50%;
    background:#00eaff;
    color:#00345b;
    font-size:18px;
    cursor:pointer;
    box-shadow:0 0 8px #00eaff;
    animation:eyeBlink 1.3s infinite;
}

@keyframes eyeBlink{
    0%,100%{
        transform:scale(1);
        opacity:1;
        box-shadow:0 0 8px #00eaff;
    }
    50%{
        transform:scale(.82);
        opacity:.55;
        box-shadow:0 0 25px #00eaff;
    }
}

.loading{
    text-align:center;
    padding:50px 10px;
    font-size:16px;
}

.error{
    text-align:center;
    background:rgba(255,0,0,.15);
    border:1px solid rgba(255,100,100,.4);
    padding:15px;
    border-radius:15px;
    margin-top:20px;
}

.footer{
    text-align:center;
    color:#b7d8f5;
    font-size:11px;
    margin-top:15px;
}

@media(max-width:600px){

    .market{
        padding:11px 9px;
    }

    .coin{
        min-width:125px;
    }

    .coin img{
        width:34px;
        height:34px;
    }

    .coin-name{
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

<header class="header">
    <h1>💎 بازار آنلاین ارز دیجیتال</h1>
    <p>قیمت لحظه‌ای ۵۰ ارز دیجیتال برتر</p>

    <div class="live">
        <span class="live-dot"></span>
        LIVE MARKET
    </div>
</header>

<div class="container">

    <div id="market">
        <div class="loading">
            ⏳ در حال دریافت قیمت‌های آنلاین...
        </div>
    </div>

    <div class="footer">
        قیمت‌ها به صورت آنلاین دریافت می‌شوند
    </div>

</div>

<script>

const API =
"https://api.coingecko.com/api/v3/coins/markets" +
"?vs_currency=usd&order=market_cap_desc&per_page=50&page=1&sparkline=false";

function formatPrice(price){

    if(price >= 1){
        return "$" + price.toLocaleString("en-US",{
            maximumFractionDigits:2
        });
    }

    return "$" + price.toLocaleString("en-US",{
        maximumSignificantDigits:5
    });
}

function formatChange(change){

    const value = Number(change || 0);

    if(value >= 0){
        return `<span class="green">▲ ${value.toFixed(2)}%</span>`;
    }

    return `<span class="red">▼ ${Math.abs(value).toFixed(2)}%</span>`;
}

async function loadCoins(){

    const market = document.getElementById("market");

    try{

        const response = await fetch(API);

        if(!response.ok){
            throw new Error("API Error");
        }

        const coins = await response.json();

        market.innerHTML = "";

        coins.forEach((coin,index)=>{

            const row = document.createElement("div");

            row.className = "market";

            row.innerHTML = `

                <div class="coin">

                    <img
                      src="${coin.image}"
                      alt="${coin.name}"
                      loading="lazy"
                    >

                    <div>
                        <div class="coin-name">
                            ${index+1}. ${coin.name}
                        </div>

                        <div class="coin-symbol">
                            ${coin.symbol}
                        </div>
                    </div>

                </div>

                <div class="price">

                    ${formatPrice(coin.current_price)}

                    <div class="change">
                        ${formatChange(
                          coin.price_change_percentage_24h
                        )}
                    </div>

                </div>

                <button
                    class="eye"
                    onclick="showCoin('${coin.name}')"
                    title="مشاهده"
                >
                    👁
                </button>

            `;

            market.appendChild(row);

        });

    }catch(error){

        market.innerHTML = `
            <div class="error">
                ❌ دریافت قیمت‌ها انجام نشد.
                <br><br>
                چند لحظه بعد دوباره تلاش کنید.
            </div>
        `;

    }

}

function showCoin(name){

    alert(
        "👁 " + name +
        "\n\nقیمت این ارز در لیست بازار نمایش داده می‌شود."
    );

}

loadCoins();

/* بروزرسانی خودکار هر 60 ثانیه */
setInterval(loadCoins,60000);

</script>

</body>
</html>
