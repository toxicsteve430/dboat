<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D-BOAT PRO</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .up { color: #00ff88; text-shadow: 0 0 10px rgba(0,255,136,0.5); }
        .down { color: #ff4444; text-shadow: 0 0 10px rgba(255,68,68,0.5); }
        .tick-item { animation: slideIn 0.3s ease-out; }
        @keyframes slideIn { from { opacity: 0; transform: translateX(-10px); } to { opacity: 1; transform: translateX(0); } }
    </style>
</head>
<body class="bg-[#050505] text-white min-h-screen flex flex-col p-5 font-sans">
    
    <div class="flex justify-between items-center mb-6">
        <h1 class="text-xl font-black tracking-tighter">D-BOAT <span class="text-blue-500">PRO</span></h1>
        <div id="status" class="text-[10px] font-bold py-1 px-3 rounded-full bg-zinc-900 text-yellow-500 italic">CONNECTING...</div>
    </div>

    <div class="bg-zinc-900/40 border border-white/5 rounded-[32px] p-8 text-center mb-4 shadow-2xl">
        <p class="text-[10px] text-zinc-500 uppercase tracking-widest mb-2">Volatility 100 (1s)</p>
        <div id="price" class="text-6xl font-mono font-bold tracking-tighter mb-2">0.00</div>
        <div id="digit-display" class="text-sm font-bold opacity-40 italic">Last Digit: -</div>
    </div>

    <div class="bg-zinc-900/20 rounded-2xl p-4 mb-6 border border-white/5">
        <h2 class="text-[10px] font-bold text-zinc-600 uppercase mb-3 tracking-widest text-center">Recent Ticks</h2>
        <div id="tick-history" class="space-y-2 text-sm font-mono">
            </div>
    </div>

    <div class="grid grid-cols-2 gap-4 mt-auto mb-6">
        <button class="bg-emerald-600 h-16 rounded-2xl font-black text-lg active:scale-95 transition-all shadow-lg shadow-emerald-900/20">RISE</button>
        <button class="bg-rose-600 h-16 rounded-2xl font-black text-lg active:scale-95 transition-all shadow-lg shadow-rose-900/20">FALL</button>
    </div>

    <script>
        const priceEl = document.getElementById('price');
        const statusEl = document.getElementById('status');
        const historyEl = document.getElementById('tick-history');
        const digitEl = document.getElementById('digit-display');
        let lastPrice = 0;
        let ticks = [];

        const ws = new WebSocket('wss://ws.binaryws.com/websockets/v3?app_id=1089');

        ws.onopen = () => {
            statusEl.innerText = "● LIVE DATA";
            statusEl.className = "text-[10px] font-bold py-1 px-3 rounded-full bg-emerald-500/10 text-emerald-500";
            ws.send(JSON.stringify({ "ticks": "R_100" }));
        };

        ws.onmessage = (msg) => {
            const data = JSON.parse(msg.data);
            if (data.tick) {
                const current = data.tick.quote;
                const lastDigit = current.toString().slice(-1);
                
                // Update Main UI
                priceEl.innerText = current;
                digitEl.innerText = `Last Digit: ${lastDigit}`;
                priceEl.className = current > lastPrice ? "text-6xl font-mono font-bold tracking-tighter up" : "text-6xl font-mono font-bold tracking-tighter down";

                // Update History
                const time = new Date().toLocaleTimeString().split(' ')[0];
                const colorClass = current > lastPrice ? "text-emerald-400" : "text-rose-400";
                const arrow = current > lastPrice ? "↑" : "↓";
                
                const tickHtml = `<div class="flex justify-between border-b border-white/5 pb-1 tick-item">
                    <span class="text-zinc-600">${time}</span>
                    <span class="${colorClass} font-bold">${arrow} ${current}</span>
                </div>`;
                
                historyEl.insertAdjacentHTML('afterbegin', tickHtml);
                if (historyEl.children.length > 5) historyEl.lastElementChild.remove();

                lastPrice = current;
            }
        };
    </script>
</body>
</html>
