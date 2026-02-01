<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D-BOAT | Builder Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #f4f7f9; color: #333; font-family: sans-serif; }
        .sidebar { background-color: #ffffff; border-right: 1px solid #e5e7eb; width: 60px; }
        .header { background-color: #ffffff; border-bottom: 1px solid #e5e7eb; }
        .block-blue { background-color: #102060; color: white; border-radius: 4px; }
        .param-row { background-color: #eef2f7; border-radius: 4px; margin-bottom: 4px; padding: 6px 12px; }
        .btn-blue { background-color: #102060; color: white; padding: 8px 16px; border-radius: 4px; font-weight: bold; }
    </style>
</head>
<body class="flex h-screen overflow-hidden">

    <div class="sidebar flex flex-col items-center py-4 gap-6 text-zinc-400">
        <div class="p-2 hover:bg-zinc-100 rounded cursor-pointer">🔄</div>
        <div class="p-2 hover:bg-zinc-100 rounded cursor-pointer">📁</div>
        <div class="p-2 hover:bg-zinc-100 rounded cursor-pointer">💾</div>
        <div class="p-2 hover:bg-zinc-100 rounded cursor-pointer text-red-500">📊</div>
        <div class="mt-auto p-2">🔍</div>
    </div>

    <div class="flex-1 flex flex-col">
        <div class="header p-3 flex justify-between items-center px-6">
            <div class="flex gap-4 items-center">
                <span class="text-zinc-400">☰</span>
                <span class="text-blue-600 font-bold">D-BOAT</span>
            </div>
            <div class="flex gap-4 items-center">
                <div class="bg-zinc-100 px-4 py-1 rounded text-sm font-bold border flex items-center gap-2">
                    <img src="https://deriv.com/static/86ad49d95f850d55e9e03512b9380922/deriv-logo.svg" width="20">
                    40,575.59 <span class="text-blue-600">USD</span>
                </div>
            </div>
        </div>

        <div class="flex-1 p-4 overflow-y-auto">
            <button class="btn-blue mb-4">Quick strategy</button>

            <div class="max-w-md">
                <div class="block-blue p-2 text-xs flex items-center gap-2">
                    📄 1. Trade parameters
                </div>
                <div class="bg-white border p-3 border-t-0 rounded-b shadow-sm space-y-1">
                    <div class="param-row flex justify-between text-[11px] items-center">
                        <span>Market:</span>
                        <span class="font-bold">Derived > Continuous > Volatility 100</span>
                    </div>
                    <div class="param-row flex justify-between text-[11px] items-center">
                        <span>Trade Type:</span>
                        <span class="font-bold">Digits > Over/Under</span>
                    </div>
                    <div class="param-row flex justify-between text-[11px] items-center">
                        <span>Contract Type:</span>
                        <span class="font-bold">Both</span>
                    </div>
                </div>

                <div class="mt-4 block-blue p-2 text-xs">📈 Live Feed</div>
                <div class="bg-white border p-4 text-center rounded-b">
                    <div id="price" class="text-4xl font-mono font-bold">0.00</div>
                    <p class="text-[10px] text-zinc-400 mt-1 uppercase tracking-widest">Volatility 100</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        const priceEl = document.getElementById('price');
        const ws = new WebSocket('wss://ws.binaryws.com/websockets/v3?app_id=1089');
        ws.onopen = () => ws.send(JSON.stringify({ "ticks": "R_100" }));
        ws.onmessage = (msg) => {
            const data = JSON.parse(msg.data);
            if (data.tick) {
                priceEl.innerText = data.tick.quote;
                priceEl.style.color = data.tick.quote > priceEl.innerText ? '#4bb4b3' : '#ff444f';
            }
        };
    </script>
</body>
</html>
