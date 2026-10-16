## Hi there 👋

<!--
**RVFinchart/rvfinchart** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
--><!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>RVfinchart - Stock Market Dashboard</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#0b1120;color:#edf2ff;font-family:Arial,sans-serif}
header{padding:22px;background:#111c30;border-bottom:1px solid #293650}
.logo{font-size:28px;font-weight:bold;color:#56d6ff}
header p,.muted{color:#a9b8d0}
main{max-width:1100px;margin:auto;padding:20px}
.card{background:#131e33;border:1px solid #293650;border-radius:12px;padding:18px;margin-bottom:18px}
input,button{padding:12px;border-radius:7px;border:1px solid #394b68;font-size:15px}
input{background:#0b1120;color:white;min-width:0;flex:1}
button{background:#1687db;color:white;border:0;cursor:pointer}
.form{display:flex;gap:8px;flex-wrap:wrap}
.form input{flex:1 1 180px}
table{width:100%;border-collapse:collapse;margin-top:15px}
th,td{text-align:left;padding:12px 6px;border-bottom:1px solid #293650}
th{color:#a9b8d0;font-size:13px}
.remove{background:#a83248;padding:8px 10px}
.chart{height:520px;border-radius:10px;overflow:hidden;background:white}
iframe{width:100%;height:100%;border:0}
footer{text-align:center;color:#a9b8d0;padding:20px}
</style>
</head>
<body>
<header>
  <div class="logo">RVfinchart</div>
  <p>Stock Market Analysis & Watchlist</p>
</header>

<main>
  <section class="card">
    <h2>My Watchlist</h2>
    <p class="muted">Add NSE symbols to track your favourite stocks.</p>
    <form class="form" id="addForm">
      <input id="symbol" placeholder="NSE Symbol e.g. RELIANCE" required>
      <button type="submit">+ Add Stock</button>
    </form>
    <p id="message" class="muted" aria-live="polite"></p>
    <table>
      <thead>
        <tr><th>Symbol</th><th>Exchange</th><th>Action</th></tr>
      </thead>
      <tbody id="stockList"></tbody>
    </table>
    <p id="empty" class="muted">Your watchlist is empty. Add a stock above.</p>
  </section>

  <section class="card">
    <h2>Market Chart</h2>
    <p class="muted">Example chart: NIFTY 50</p>
    <div class="chart">
      <iframe title="NIFTY 50 chart"
        src="https://s.tradingview.com/widgetembed/?symbol=NSE%3ANIFTY&interval=D&theme=dark&style=1&locale=en&allow_symbol_change=true"
        loading="lazy"></iframe>
    </div>
  </section>
  <p class="muted">Watchlist is saved in this browser on this device. Prices are not included in the watchlist yet.</p>
</main>

<footer>RVfinchart © 2026</footer>

<script>
const KEY = "rvfinchart_watchlist_v1";
const form = document.getElementById("addForm");
const input = document.getElementById("symbol");
const list = document.getElementById("stockList");
const empty = document.getElementById("empty");
const message = document.getElementById("message");

function getStocks() {
  try {
    const data = JSON.parse(localStorage.getItem(KEY) || "[]");
    return Array.isArray(data)
      ? data.filter(s => typeof s === "string")
      : [];
  } catch {
    return [];
  }
}

let stocks = [...new Set(getStocks())];

function saveStocks() {
  try {
    localStorage.setItem(KEY, JSON.stringify(stocks));
    return true;
  } catch {
    message.textContent = "Could not save. Please check browser storage.";
    return false;
  }
}

function render() {
  list.replaceChildren();
  empty.hidden = stocks.length > 0;

  stocks.forEach(symbol => {
    const row = document.createElement("tr");
    const nameCell = document.createElement("td");
    const exchangeCell = document.createElement("td");
    const actionCell = document.createElement("td");
    const remove = document.createElement("button");

    nameCell.textContent = symbol;
    exchangeCell.textContent = "NSE";
    remove.textContent = "Remove";
    remove.className = "remove";
    remove.type = "button";
    remove.addEventListener("click", () => {
      stocks = stocks.filter(s => s !== symbol);
      if (saveStocks()) {
        message.textContent = symbol + " removed.";
        render();
      }
    });

    actionCell.appendChild(remove);
    row.append(nameCell, exchangeCell, actionCell);
    list.appendChild(row);
  });
}

form.addEventListener("submit", event => {
  event.preventDefault();
  const symbol = input.value.trim().toUpperCase();

  if (!/^[A-Z0-9&.-]{1,30}$/.test(symbol)) {
    message.textContent = "Enter a valid stock symbol.";
    return;
  }

  if (stocks.includes(symbol)) {
    message.textContent = symbol + " is already in your watchlist.";
    return;
  }

  stocks.push(symbol);
  if (saveStocks()) {
    input.value = "";
    message.textContent = symbol + " added successfully.";
    render();
  } else {
    stocks.pop();
  }
});

render();
</script>
</body>
</html>
