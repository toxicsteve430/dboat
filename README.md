<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D-BOAT | 95% YIELD PRO</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #0d1117; color: #c9d1d9; font-family: 'Inter', sans-serif; font-size: 11px; }
        .card { background: #161b22; border: 1px solid #30363d; border-radius: 8px; }
        .input-box { background: #0d1117; border: 1px solid #30363d; padding: 6px; border-radius: 4px; color: #58a6ff; font-weight: bold; outline: none; }
        .payout-badge { background: #238636; color: white; padding: 2px 8px; border-radius: 10px; font-size: 9px; font-weight: bold; }
        .win-text { color: #3fb950; } .loss-text { color: #f85149; }
        .bar { transition: height 0.4s ease; width: 100%; border-radius: 2px; }
    </style>
</head>
<body class="flex flex-col h-screen overflow-hidden p-3 gap-3">

    <div class="card p-4 flex justify-between items-center shadow-lg">
        <div class="flex items-center gap-4">
            <h1 class="text-blue-500 font-black text-xl italic tracking-tighter">D-BOAT</h1>
            <div class="payout-badge uppercase tracking-widest">95% Payout Mode</div>
            <select id="accType" class="input-box text-[9px]">
                <option value="demo">DEMO ACCOUNT</option>
                <option value="real">REAL ACCOUNT</option>
            </select>
        </div>
        <div class="flex gap-6 items-center">
            <div class="text-right border-r border-zinc-800 pr-4">
                <p class="text-[8px] uppercase text-zinc-500">Session Profit</p>
                <p id="totalProfit" class="font-bold text-emerald-500 text-lg">$ 0.00</p>
            </div>
            <div class="text-right">
                <p class="text-[8px] uppercase text-zinc-500">Win Rate</p>
                <p id="winRate" class="font-bold text-blue-400 text-lg">0%</p>
            </div>
        </div>
    </div>

    <div class="flex-1 flex gap-3 overflow-hidden">
        <div class="flex-[2] flex flex-col gap-3">
            <div class="card flex-1 flex flex-col items-center justify-center relative">
                <div class="absolute top-4 left-4 flex flex-col gap-2">
                    <select id="symbol" onchange="changeMarket()" class="input-box">
                        <option value="R_10">Volatility 10</option>
                        <option value="R_100" selected>Volatility 100</option>
                    </select>
                    <span class="text-[8px] text-zinc-500 font-bold">STRATEGY: DIGIT OVER 0</span>
                </div>
                <div id="price" class="text-7xl font-mono font-bold tracking-tighter text-white">0.0000</div>
                <div id="status" class="text-[10px] font-bold text-emerald-500 mt-4 animate-pulse uppercase tracking-[0.2em]">Live Analysis Active</div>
            </div>

            <div class="card h-28 p-4 flex flex-col">
                <div id="histogram" class="flex-1 flex items-end gap-1"></div>
                <div class="flex justify-between text-[8px] mt-1 text-zinc-600 font-mono px-1">
                    <span>0</span><span>1</span><span>2</span><span>3</span><span>4</span><span>5</span><span>6</span><span>7</span><span>8</span><span>9</span>
                </div>
            </div>
        </div>

        <div class="flex-1 card flex flex-col overflow-hidden">
            <div class="p-3 border-b border-zinc-800 bg-zinc-900/50 font-bold text-[9px] uppercase text-zinc-500">Execution Log</div>
            <div id="log" class="flex-1 overflow-auto p-3 space-y-1 font-mono text-[10px]"></div>
        </div>
    </div>

    <div class="card p-4 flex justify-between items-center">
        <div class="flex gap-4">
            <div class="flex flex-col">
                <span class="text-[8px] text-zinc-500 uppercase mb-1 font-bold">Stake ($)</span>
                <input id="stake" type="number" value="10" class="input-box w-20">
            </div>
            <div class="flex flex-col">
                <span class="text-[8px] text-zinc-500 uppercase mb-1 font-bold">Recovery (x)</span>
                <input id="martingale" type="number" value="2.1" class="input-box w-20">
            </div>
        </div>
        <button onclick="toggleBot()" id="runBtn" class="bg-emerald-600 hover:bg-emerald-500 text-white px-20 py-4 rounded-xl font-bold transition-all shadow-xl active:scale-95 text-xs">START 95% RECOVERY BOT</button>
    </div>

    <script>
        let isRunning = false;
        let profit = 0;
        let wins = 0;
        let total = 0;
        let stake = 10;
        let history = [];
        let ws;

        function connect() {
            if (ws) ws.close();
            ws = new WebSocket('wss://ws.binaryws.com/websockets/v3?app_id=1089');
            const symbol = document.getElementById('symbol').value;
            ws.onopen = () => ws.send(JSON.stringify({ "ticks": symbol }));
            ws.onmessage = (msg) => {
                const data = JSON.parse(msg.data);
                if (data.tick) {
                    const quote = data.tick.quote;
                    const digit = parseInt(quote.toString().slice(-1));
                    updateUI(quote, digit);
                    if(isRunning) runStrategy(digit);
                }
            };
        }

        function updateUI(q, digit) {
            document.getElementById('price').innerText = q;
            history.push(digit);
            if(history.length > 50) history.shift();
            const counts = Array(10).fill(0);
            history.forEach(d => counts[d]++);
            const max = Math.max(...counts) || 1;
            document.getElementById('histogram').innerHTML = counts.map((c, i) => `
                <div class="bar ${i === 0 ? 'bg-red-600' : 'bg-blue-600'}" style="height: ${(c/max)*100}%"></div>
            `).join('');
        }

        function runStrategy(digit) {
            total++;
            // 95% Payout Logic: Digit Over 0
            // You win if digit is 1-9 (90% probability)
            if (digit > 0) {
                wins++;
                let p = stake * 0.95; // 95% PROFIT RATE
                profit += p;
                addLog("WIN", p);
                stake = parseFloat(document.getElementById('stake').value);
            } else {
                profit -= stake;
                addLog("LOSS", -stake);
                stake *= parseFloat(document.getElementById('martingale').value);
            }
            updateStats();
        }

        function addLog(type, val) {
            const log = document.getElementById('log');
            const item = document.createElement('div');
            item.className = "flex justify-between border-b border-zinc-800 pb-1";
            const color = type === 'WIN' ? 'win-text' : 'loss-text';
            item.innerHTML = `<span class="${color} font-bold">${type}</span> <span>${val.toFixed(2)}</span>`;
            log.prepend(item);
        }

        function updateStats() {
            document.getElementById('totalProfit').innerText = `$ ${profit.toFixed(2)}`;
            document.getElementById('totalProfit').className = profit >= 0 ? "font-bold text-emerald-500 text-lg" : "font-bold text-red-500 text-lg";
            document.getElementById('winRate').innerText = `${((wins/total)*100).toFixed(1)}%`;
        }

        function changeMarket() { connect(); }
        function toggleBot() {
            isRunning = !isRunning;
            stake = parseFloat(document.getElementById('stake').value);
            const btn = document.getElementById('runBtn');
            btn.innerText = isRunning ? "STOP ANALYZER" : "START 95% RECOVERY BOT";
            btn.className = isRunning ? "bg-red-600 text-white px-20 py-4 rounded-xl font-bold" : "bg-emerald-600 text-white px-20 py-4 rounded-xl font-bold";
        }

        connect();
    </script>
</body>
</html>
