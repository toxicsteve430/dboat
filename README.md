<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D-BOAT ULTIMATE</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .up { color: #00ff88; }
        .down { color: #ff4444; }
        .bg-glass { background: rgba(20, 20, 25, 0.8); backdrop-filter: blur(10px); }
    </style>
</head>
<body class="bg-[#050505] text-zinc-100 min-h-screen flex flex-col p-4 font-sans">
    
    <div class="flex justify-between items-center mb-6 px-2">
        <div class="flex items-center gap-2">
            <div id="dot" class="h-2 w-2 rounded-full bg-red-500 animate-pulse"></div>
            <span id="status-text" class="text-[10px] font-bold tracking-widest uppercase text-zinc-500">Connecting</span>
        </div>
        <h1 class="text-lg font-black italic tracking-tighter text-blue-500">D-BOAT <span class="text-white">ULTIMATE</span></h1>
    </div>

    <div class="bg-glass border border-white/5 rounded-[40px] p-10 text-center mb-4">
        <p class="text-[10px] text-zinc-600 uppercase tracking-[0.3em] mb-2 font-bold">Volatility 100 Index</p>
        <div id="price" class="text-6xl font-mono font-bold tracking-tighter mb-1">0.00</div>
        <div id="digit" class="text-xs font-bold text-zinc-700 underline decoration-blue-500">Digit: -</div>
    </div>

    <div class="flex-1 overflow-hidden px-2">
        <p class="text-[10px] font-bold text-zinc-700 uppercase mb-3 tracking-widest">Live Tick Log</p>
        <div id="history" class="space-y-3 font-mono text-sm">
            </div>
    </div>

    <div class="grid grid-cols-2 gap-4 pt-4 pb-6">
        <button class="bg-[#00c087] py-6 rounded-3xl font-black text-xl shadow-lg shadow-emerald-900/20 active:scale-95 transition-all">RISE</button>
        <button class="bg-[#f84960] py-6 rounded-3xl font-black text-xl shadow-lg shadow-rose-900/20 active:scale-95 transition-all">FALL</button>
    </div>

    <script>
        let ws;
        let lastPrice = 0;
        const priceEl = document.getElementById('price');
        const dotEl = document.getElementById('dot');
        const statusText = document.getElementById('status-text');
        const historyEl = document.getElementById('history');
        const digitEl = document.getElementById('digit');

        function connect() {
            // Using App ID 1089 (Standard Deriv Testing ID)
            ws = new WebSocket('wss://ws.binaryws.com/websockets/v3?app_id=1089');

            ws.onopen = () => {
                dotEl.className = "h-2 w-2 rounded-full bg-green-500 shadow-[0_0_8px_#22c55e]";
                statusText.innerText = "Live Connection";
                ws.send(JSON.stringify({ ticks: "R_100" }));
            };

            ws.onmessage = (msg) => {
                const data = JSON.parse(msg.data);
                if (data.tick) {
                    const quote = data.tick.quote;
                    const digit = quote.toString().split('').pop();
                    
                    // Update Main Price
                    priceEl.innerText = quote;
                    digitEl.innerText = `LAST DIGIT: ${digit}`;
                    priceEl.className = quote > lastPrice ? "text-6xl font-mono font-bold tracking-tighter up" : "text-6xl font-mono font-bold tracking-tighter down";

                    // Update History Log
                    const log = document.createElement('div');
                    log.className = "flex justify-between items-center opacity-80 border-b border-white/5 pb-1";
                    log.innerHTML = `<span class="text-zinc-600">${new Date().toLocaleTimeString().split(' ')[0]}</span>
                                    <span class="font-bold ${quote > lastPrice ? 'text-emerald-500' : 'text-rose-500'}">${quote > lastPrice ? '▲' : '▼'} ${quote}</span>`;
                    historyEl.prepend(log);
                    if (historyEl.children.length > 6) historyEl.lastChild.remove();

                    lastPrice = quote;
                }
            };

            ws.onclose = () => {
                dotEl.className = "h-2 w-2 rounded-full bg-red-500 animate-pulse";
                statusText.innerText = "Reconnecting...";
                setTimeout(connect, 2000); // Auto-retry after 2 seconds
            };
        }

        connect();
    </script>
</body>
</html>
