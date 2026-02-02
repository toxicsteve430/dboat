<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ultimate Bot Trader | Admin: 07960 27150</title>
    <style>
        :root {
            --bg: #0b0e11; --sidebar: #1e2329; --card: #161a1e;
            --accent: #00ff88; --danger: #ff4d4d; --text: #eaecef;
        }
        body { margin: 0; font-family: 'Inter', sans-serif; background: var(--bg); color: var(--text); display: flex; height: 100vh; overflow: hidden; }
        
        /* Sidebar Styling */
        nav { width: 280px; background: var(--sidebar); display: flex; flex-direction: column; padding: 20px; border-right: 1px solid #333; }
        .logo { font-size: 24px; font-weight: bold; color: var(--accent); margin-bottom: 30px; }
        .nav-item { padding: 12px; margin-bottom: 5px; cursor: pointer; border-radius: 8px; color: #848e9c; }
        .nav-item.active { background: #2b3139; color: white; border-left: 4px solid var(--accent); }

        /* Dashboard Area */
        main { flex: 1; padding: 20px; overflow-y: auto; }
        .stats { display: flex; justify-content: space-between; gap: 15px; margin-bottom: 20px; }
        .stat-card { background: var(--card); padding: 20px; border-radius: 12px; flex: 1; border-bottom: 3px solid var(--accent); }

        .trading-zone { display: grid; grid-template-columns: 2fr 1fr; gap: 20px; }
        .chart-frame { background: var(--card); height: 550px; border-radius: 12px; overflow: hidden; border: 1px solid #333; }
        .bot-settings { background: var(--card); padding: 20px; border-radius: 12px; border: 1px solid #333; }

        /* UI Components */
        input, select { width: 100%; background: #2b3139; border: 1px solid #444; color: white; padding: 12px; border-radius: 5px; margin: 10px 0; }
        .btn { width: 100%; padding: 15px; border: none; border-radius: 5px; font-weight: bold; cursor: pointer; font-size: 15px; transition: 0.3s; }
        .btn-link { background: var(--accent); color: black; }
        .btn-bot { background: #ffaa00; color: black; margin-top: 10px; }
        
        /* Admin Footer */
        .admin-footer { margin-top: auto; padding-top: 20px; border-top: 1px solid #333; font-size: 13px; color: #848e9c; }
        .whatsapp { background: #25d366; color: white; padding: 10px; text-decoration: none; border-radius: 5px; display: block; text-align: center; margin-top: 10px; font-weight: bold; }
    </style>
</head>
<body>

    <nav>
        <div class="logo">TRADER PRO HUB</div>
        <div class="nav-item active">Dashboard</div>
        <div class="nav-item">Bot Builder</div>
        <div class="nav-item">Copy Trading</div>
        
        <div class="admin-footer">
            <p><strong>DEVELOPER SUPPORT:</strong></p>
            <p>📞 Phone: 07960 27150</p>
            <p>📧 Email: YourGmail@gmail.com</p>
            <p style="color: #ffaa00;">License: M-Pesa 07960 27150</p>
            <a href="https://wa.me/254796027150?text=Hello%20Admin,%20I%20want%20to%20activate%20my%20bot" class="whatsapp">WhatsApp Support</a>
        </div>
    </nav>

    <main>
        <div class="stats">
            <div class="stat-card"><small>Live Balance</small><h2 id="balance">$0.00</h2></div>
            <div class="stat-card"><small>Active Account</small><h2 id="acc-id">Disconnected</h2></div>
            <div class="stat-card"><small>Price (V100)</small><h2 id="live-price" style="color: var(--accent)">0.00</h2></div>
        </div>

        <div class="trading-zone">
            <div class="chart-frame">
                <iframe src="https://tradingview.binary.com/v2.1.0/main.html?symbol=R_100&theme=dark" width="100%" height="100%" frameborder="0"></iframe>
            </div>

            <div class="bot-settings">
                <h3>Account Link</h3>
                <select id="acc-type">
                    <option value="demo">Demo Account</option>
                    <option value="real">Real Account (CR)</option>
                </select>
                <input type="password" id="api-token" placeholder="Paste Deriv API Token">
                <button class="btn btn-link" onclick="connectAccount()">LINK ACCOUNT</button>

                <hr style="border: 0.5px solid #333; margin: 25px 0;">

                <h3>Bot Configuration</h3>
                <label>Stake ($)</label>
                <input type="number" id="stake" value="10">
                <label>Martingale Factor</label>
                <input type="number" id="martingale" value="2.1">

                <button class="btn btn-bot" id="bot-toggle" onclick="runAutoBot()">START AUTO-BOT</button>
                <p id="bot-status" style="text-align: center; font-size: 12px; margin-top: 10px;">Ready for analysis</p>
            </div>
        </div>
    </main>

    <script>
        let ws;
        let isRunning = false;
        const APP_ID = 1089; // Replace this with your Registered App ID to earn commission

        function connectAccount() {
            const token = document.getElementById('api-token').value;
            const mode = document.getElementById('acc-type').value;

            ws = new WebSocket(`wss://ws.binaryws.com/websockets/v3?app_id=${APP_ID}`);

            ws.onopen = () => {
                ws.send(JSON.stringify({ authorize: token }));
            };

            ws.onmessage = (msg) => {
                const data = JSON.parse(msg.data);

                if (data.msg_type === 'authorize') {
                    const accounts = data.authorize.account_list;
                    const selected = accounts.find(a => mode === 'demo' ? a.is_virtual === 1 : a.is_virtual === 0);
                    
                    if (selected) {
                        document.getElementById('acc-id').innerText = selected.loginid;
                        document.getElementById('balance').innerText = `$${selected.balance}`;
                        document.getElementById('balance').style.color = mode === 'real' ? 'var(--danger)' : 'var(--accent)';
                        ws.send(JSON.stringify({ ticks: 'R_100', subscribe: 1 }));
                        alert("Account Linked Successfully!");
                    } else {
                        alert("The token provided does not match the selected account type (Demo/Real).");
                    }
                }

                if (data.msg_type === 'tick') {
                    document.getElementById('live-price').innerText = data.tick.quote;
                }
            };
        }

        function runAutoBot() {
            const btn = document.getElementById('bot-toggle');
            isRunning = !isRunning;
            
            if (isRunning) {
                btn.innerText = "STOP BOT";
                btn.style.background = "var(--danger)";
                document.getElementById('bot-status').innerText = "Bot Status: Analyzing Patterns...";
            } else {
                btn.innerText = "START AUTO-BOT";
                btn.style.background = "#ffaa00";
                document.getElementById('bot-status').innerText = "Bot Status: Stopped";
            }
        }
    </script>
</body>
</html>
