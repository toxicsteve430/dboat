<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D-BOAT | DOLLAR PRINTER V2</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background: radial-gradient(circle at top, #1a1f2c, #0d1117); color: #e6edf3; font-family: 'Segoe UI', sans-serif; min-height: 100vh; }
        /* The "Houses" / Stat Panels */
        .house { 
            background: rgba(22, 27, 34, 0.8); 
            border: 1px solid #30363d; 
            border-radius: 16px; 
            padding: 20px;
            text-align: center;
            backdrop-filter: blur(10px);
        }
        .house-label { font-size: 10px; color: #8b949e; text-transform: uppercase; font-weight: 800; letter-spacing: 2px; }
        .house-value { font-size: 28px; font-weight: 900; margin-top: 8px; }
        .neon-green { color: #3fb950; text-shadow: 0 0 15px rgba(63, 185, 80, 0.4); }
        .neon-red { color: #f85149; text-shadow: 0 0 15px rgba(248, 81, 73, 0.4); }
        .neon-blue { color: #58a6ff; text-shadow: 0 0 15px rgba(88, 166, 255, 0.4); }
        .pulse { animation: pulse-animation 2s infinite; }
        @keyframes pulse-animation { 0% { opacity: 1; } 50% { opacity: 0.5; } 100% { opacity: 1; } }
    </style>
</head>
<body class="p-6">

    <div class="text-center mb-8">
        <h1 class="text-3xl font-black italic tracking-tighter">DOLLAR <span class="text-blue-500">PRINTER</span></h1>
        <div class="text-[10px] bg-blue-900/30 text-blue-400 py-1 px-3 rounded-full inline-block mt-2 font-bold tracking-widest">ULTIMATE MASTER V2</div>
    </div>

    <div class="grid grid-cols-2 gap-4 mb-6">
        <div class="house col-span-2 border-blue-500/30">
            <div class="house-label">Net Profit (USD)</div>
            <div id="profit" class="house-value neon-blue">$ 0.00</div>
        </div>
        <div class="house border-green-500/20">
            <div class="house-label">Total Wins</div>
            <div id="wins" class="house-value neon-green">0</div>
        </div>
        <div class="house border-red-500/20">
            <div class="house-label">Total Losses</div>
            <div id="losses" class="house-value neon-red">0</div>
        </div>
    </div>

    <div class="house mb-6 bg-black/50 border-gray-700">
        <div class="house-label mb-2">Live Market Digit</div>
        <div id="digit" class="text-7xl font-black text-white py-4">0</div>
        <div id="status" class="text-[10px] font-bold text-blue-500 pulse uppercase">Analyzing Market...</div>
    </div>

    <div class="space-y-4">
        <input id="token" type="password" placeholder="PASTE DERIV TOKEN HERE" class="w-full bg-gray-900 border border-gray-700 p-4 rounded-xl text-center text-xs tracking-widest focus:border-blue-500 outline-none transition-all">
        <button onclick="start()" class="w-full bg-blue-600 hover:bg-blue-500 text-white font-black py-5 rounded-xl uppercase tracking-widest shadow-[0_0_20px_rgba(37,99,235,0.4)] transition-all active:scale-95">
            Start Printing
        </button>
    </div>

</body>
</html>
