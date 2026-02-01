<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D-BOAT | Builder Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #f2f3f5; color: #333; font-family: 'Segoe UI', sans-serif; }
        .sidebar { background: #ffffff; border-right: 1px solid #d6dadc; width: 48px; }
        .header { background: #ffffff; border-bottom: 1px solid #d6dadc; height: 48px; }
        .block-header { background: #102060; color: white; border-radius: 4px 4px 0 0; padding: 6px 10px; font-size: 11px; font-weight: bold; }
        .block-body { background: white; border: 1px solid #d6dadc; border-top: none; padding: 10px; border-radius: 0 0 4px 4px; box-shadow: 0 2px 4px rgba(0,0,0,0.05); }
        .param-row { background: #eef1f5; margin-bottom: 4px; padding: 4px 8px; border-radius: 3px; font-size: 11px; display: flex; justify-content: space-between; }
        .btn-run { background: #2e8836; color: white; border-radius: 4px; padding: 6px 20px; font-weight: bold; font-size: 13px; }
    </style>
</head>
<body class="flex h-screen overflow-hidden">

    <div class="sidebar flex flex-col items-center py-4 gap-6 text-zinc-400">
        <div class="cursor-pointer hover:text-blue-600">📁</div>
        <div class="cursor-pointer hover:text-blue-600">🧩</div>
        <div class="cursor-pointer hover:text-blue-600">💾</div>
        <div class="mt-auto cursor-pointer pb-4">⚙️</div>
    </div>

    <div class="flex-1 flex flex-col">
        <div class="header flex justify-between items-center px-4 shadow-sm">
            <div class="flex items-center gap-3">
                <span class="text-blue-700 font-black tracking-tighter">D-BOAT</span>
                <span class="text-[10px] text-zinc-400 font-bold uppercase tracking-widest">Bot Builder</span>
            </div>
            <div class="flex gap-2 items-center">
                <div class="bg-zinc-100 border px-3 py-1 rounded text-xs font-bold">
                    Balance: <span class="text-green-600">10,000.00 USD</span>
                </div>
                <button class="btn-run active:scale-95 transition-all">▶ RUN</button>
            </div>
        </div>

        <div class="flex-1 p-6 overflow-auto relative">
            <div class="absolute top-0 left-0 w-full h-full opacity-10 pointer-events-none" style="background-image: radial-gradient(#000 0.5px, transparent 0.5px); background-size: 20px 20px;"></div>
            
            <div class="max-w-sm mb-6 relative z-10">
                <div class="block-header flex items-center gap-2">
                    <span class="bg-blue-500 rounded-sm px-1">1</span> Trade parameters
                </div>
                <div class="block-body">
                    <div class="param-row"><span>Market:</span> <span class="font-bold">Volatility 100 Index</span></div>
                    <div class="param-row"><span>Trade Type:</span> <span class="font-bold">Rise/Fall</span></div>
                    <div class="param-row"><span>Contract:</span> <span class="font-bold">Both</span></div>
                    <div class="param-row"><span>Stake:</span> <span class="font-bold">10 USD</span></div>
                </div>
            </div>

            <div class="max-w-sm relative z-10">
                <div class="block-header flex justify-between items-center bg-zinc-800">
                    <span>📈 Market Analysis</span>
                    <span id="dot" class="h-2 w-2 rounded-full bg-red-500"></span>
                </div>
                <div class="block-body text-center py-8">
                    <div id="price" class="text-4xl font-mono font-bold tracking-tighter">0.0000</div>
                    <p class="text-[9px] text-zinc-400 mt-2 uppercase font-bold tracking-[0.2em]">Real-time Feed</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        const priceEl = document.getElementById('price');
        const dotEl = document.getElementById('dot');
        let lastPrice = 0;
        const ws = new WebSocket('wss://ws.binaryws.com/websockets/v3?app_id=1089');

        ws.onopen = () => {
            dotEl.className = "h-2 w-2 rounded-full bg-green-500 animate-pulse";
            ws.send(JSON.stringify({ "ticks": "R_100" }));
        };

        ws.onmessage = (msg) => {
            const data = JSON.parse(msg.data);
            if (data.tick) {
                const current = data.tick.quote;
                priceEl.innerText = current;
                priceEl.style.color = current > lastPrice ? "#2e8836" : "#ff444f";
                lastPrice = current;
            }
        };
    </script>
</body>
</html>
