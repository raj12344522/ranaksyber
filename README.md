# ranaksyber
My Personal Dashboard
<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <title>Ronak Syber OS - Secure Admin</title>
    
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>

    <style>
        :root { 
            --primary: #06b6d4; 
            --primary-bright: #67e8f9; 
            --primary-dim: rgba(6, 182, 212, 0.3); 
            --dark: #0f172a; 
            --panel: #1e293b; 
            --text-main: #f1f5f9; 
            --text-muted: #94a3b8; 
            --green: #10B981; 
            --red: #EF4444; 
            --saffron: #ff9933; 
            --border-color: rgba(255,255,255,0.1); 
            --gmail: #ea4335;
        }

        * { box-sizing: border-box; outline: none; -webkit-user-select: none; user-select: none; } 
        input, textarea { -webkit-user-select: text; user-select: text; }
        
        html, body { 
            margin: 0; padding: 0; background: var(--dark); color: var(--text-main); 
            font-family: 'Segoe UI', sans-serif; width: 100vw; height: 100vh; 
            overflow: hidden !important; position: fixed; top: 0; left: 0; 
        }

        /* Scrollbar */
        ::-webkit-scrollbar { width: 5px; } 
        ::-webkit-scrollbar-thumb { background: var(--primary); }

        /* Cyber Background */
        .cyber-bg { position: fixed; top: 0; left: 0; width: 100%; height: 100%; z-index: -1; background: radial-gradient(circle at 50% 0%, #1e293b 0%, var(--dark) 80%); }
        .grid-anim { 
            position: absolute; width: 200%; height: 200%; top: -50%; left: -50%; 
            background-image: linear-gradient(rgba(6, 182, 212, 0.03) 1px, transparent 1px), linear-gradient(90deg, rgba(6, 182, 212, 0.03) 1px, transparent 1px); 
            background-size: 40px 40px; transform: perspective(500px) rotateX(60deg); opacity: 0.4; 
        }

        /* Toast Notifications */
        #toast-container { position: fixed; bottom: 20px; right: 20px; z-index: 100002; display: flex; flex-direction: column; gap: 10px; pointer-events: none; }
        .toast-box { 
            background: rgba(15, 23, 42, 0.95); border-left: 4px solid var(--primary); color: #fff; padding: 15px 20px; 
            border-radius: 4px; box-shadow: 0 5px 15px rgba(0,0,0,0.5); display: flex; align-items: center; gap: 12px; 
            font-size: 13px; font-weight: 600; backdrop-filter: blur(10px); animation: slideIn 0.3s ease-out forwards; 
            pointer-events: auto; min-width: 280px; border-top: 1px solid rgba(255,255,255,0.1); 
        }
        .toast-box.success { border-color: var(--green); } 
        .toast-box.error { border-color: var(--red); }
        @keyframes slideIn { from { transform: translateX(100%); opacity: 0; } to { transform: translateX(0); opacity: 1; } }

        /* Login Screen */
        #login-screen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(15, 23, 42, 0.98); z-index: 99999; display: flex; justify-content: center; align-items: center; backdrop-filter: blur(15px); padding: 20px; }
        .login-box { width: 100%; max-width: 360px; background: rgba(30, 41, 59, 0.95); border: 2px solid #06b6d4; padding: 35px 25px; border-radius: 20px; text-align: center; box-shadow: 0 0 60px rgba(6, 182, 212, 0.25); display: flex; flex-direction: column; align-items: center; }
        .login-pic { width: 100px; height: 100px; border-radius: 50%; border: 3px solid #06b6d4; object-fit: cover; margin-bottom: 15px; }
        .login-field { width: 100%; padding: 14px; background: rgba(0,0,0,0.4); border: 1px solid rgba(255,255,255,0.1); color: #fff; border-radius: 10px; margin-bottom: 15px; text-align: center; }
        .login-submit-btn { width: 100%; background: linear-gradient(135deg, #06b6d4, #0891b2); color: #000; padding: 14px; font-weight: 800; border: none; border-radius: 10px; cursor: pointer; }

        /* Layout Parts */
        header { background: var(--panel); padding: 0 20px; display: flex; justify-content: space-between; align-items: center; height: 75px; position: relative; }
        .content { display: flex; height: calc(100vh - 110px); width: 100%; }
        .sidebar-left, .sidebar-right { width: 300px; background: rgba(30, 41, 59, 0.5); padding: 20px; overflow-y: auto; }
        .dashboard { flex: 1; padding: 20px; overflow-y: auto; }
        
        /* কার্ড স্টাইল */
        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(160px, 1fr)); gap: 15px; }
        .card { background: #111827; border: 1px solid var(--border-color); border-radius: 8px; padding: 15px; transition: 0.3s; }
        .card:hover { border-color: var(--primary); transform: translateY(-3px); }

        /* Bio & Photo View */
        #bio-view, #photo-view { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #e0e0e0; z-index: 20000; display: none; flex-direction: column; color: #333; }
        #bio-view.active, #photo-view.active { display: flex; }

        /* Footer */
        footer { background: var(--panel); height: 35px; width: 100%; display: flex; align-items: center; padding: 0 25px; font-size: 11px; color: var(--text-muted); position: absolute; bottom: 0; }
        
        /* Mobile Responsive */
        @media screen and (max-width: 768px) {
            .content { flex-direction: column; overflow-y: auto; }
            .sidebar-left, .sidebar-right { width: 100%; height: auto; }
        }
    </style>
</head>
<body>

    <div id="welcome-flash" style="display:none; position:fixed; width:100%; height:100%; background:#000; z-index:100000; justify-content:center; align-items:center;">
        <div style="font-size:40px; color:var(--saffron); font-weight:900;">জয় শ্রী রাম</div>
    </div>

    <div id="login-screen">
        <div class="login-box">
            <img class="login-pic" src="https://i.ibb.co/gZnygDq3/o9x81-Q-K-copy.jpg" alt="Admin">
            <h3 style="color:#fff; margin:0;">RUDRA DAS</h3>
            <p style="color:var(--primary); font-size:11px; margin-bottom:20px;">IT SOLUTION EXPERT</p>
            <input class="login-field" id="login-pass" type="password" placeholder="ENTER SECURITY CODE">
            <button class="login-submit-btn" onclick="attemptLogin()">UNLOCK SYSTEM</button>
            <p id="login-status" style="font-size:11px; margin-top:15px; color:#94a3b8;">WAITING...</p>
        </div>
    </div>

    <div id="toast-container"></div>

    <div id="main-view" style="display:none; flex-direction:column; width:100%; height:100%;">
        <header>
            <div style="font-weight:800; font-size:20px;"><i class="fa-solid fa-bolt" style="color:var(--primary);"></i> RONAK SYBER PRO</div>
            <div id="clock-display" style="font-family:monospace; font-size:24px; color:var(--primary-bright);">00:00:00</div>
            <div>
                <button onclick="logout()" style="background:var(--red); color:#fff; border:none; padding:8px 15px; border-radius:5px; cursor:pointer;">LOGOUT</button>
            </div>
        </header>

        <div class="content">
            <aside class="sidebar-left">
                <div style="text-align:center; margin-bottom:20px;">
                    <img src="https://i.ibb.co/gZnygDq3/o9x81-Q-K-copy.jpg" style="width:70px; border-radius:50%; border:2px solid var(--primary);">
                    <h4 style="margin:5px 0;">RUDRA DAS</h4>
                </div>
                
                <div style="background:rgba(255,255,255,0.05); padding:15px; border-radius:10px;">
                    <label style="font-size:12px; color:var(--primary);">ADD NEW LINK</label>
                    <input id="b-name" placeholder="Name" style="width:100%; margin-top:10px; padding:8px; background:#0f172a; border:1px solid #334155; color:#fff;">
                    <input id="b-url" placeholder="URL" style="width:100%; margin-top:10px; padding:8px; background:#0f172a; border:1px solid #334155; color:#fff;">
                    <button onclick="saveLinkToCloud()" style="width:100%; margin-top:10px; background:var(--primary); border:none; padding:10px; font-weight:bold; cursor:pointer;">SAVE TO CLOUD</button>
                </div>
            </aside>

            <main class="dashboard">
                <input id="search" onkeyup="filter()" placeholder="Search tools..." style="width:100%; padding:15px; background:rgba(30,41,59,0.8); border:1px solid var(--primary); color:#fff; border-radius:10px; margin-bottom:20px;">
                <div id="grid" class="grid">
                    </div>
            </main>

            <aside class="sidebar-right">
                <div style="background:var(--panel); padding:15px; border-radius:10px; text-align:center; border:1px solid var(--primary-dim);">
                    <div id="bengali-day" style="font-size:20px; font-weight:bold;">...</div>
                    <div style="font-size:11px; color:var(--text-muted);">SYSTEM DATE</div>
                </div>
            </aside>
        </div>

        <footer>
            <div style="flex:1;">SYSTEM ONLINE | USER: RUDRA DAS</div>
            <div>STORAGE: FIREBASE CLOUD</div>
        </footer>
    </div>

    <script>
        const firebaseConfig = {
            apiKey: "AIzaSyCQD2n6GA09JGfMuPcpMGVLbDtv_Jg8kaM",
            authDomain: "ranaksyber.firebaseapp.com",
            databaseURL: "https://ranaksyber-default-rtdb.firebaseio.com",
            projectId: "ranaksyber",
            storageBucket: "ranaksyber.firebasestorage.app",
            messagingSenderId: "701008466110",
            appId: "1:701008466110:web:38415743d609deb053ceef"
        };

        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();
        const auth = firebase.auth();

        function attemptLogin() {
            const pass = document.getElementById('login-pass').value;
            const status = document.getElementById('login-status');
            const adminEmail = "nirobdas03@gmail.com";

            if(!pass) return;
            status.innerText = "AUTHENTICATING...";
            
            auth.signInWithEmailAndPassword(adminEmail, pass)
            .then(() => {
                status.innerText = "ACCESS GRANTED";
                setTimeout(() => {
                    document.getElementById('login-screen').style.display = 'none';
                    document.getElementById('main-view').style.display = 'flex';
                    loadData();
                }, 1000);
            })
            .catch(err => {
                status.innerText = "ACCESS DENIED";
                status.style.color = "#EF4444";
            });
        }

        function loadData() {
            db.ref('links').on('value', snap => {
                const data = snap.val();
                const grid = document.getElementById('grid');
                grid.innerHTML = '';
                if(data) {
                    Object.keys(data).forEach(key => {
                        const item = data[key];
                        grid.innerHTML += `
                            <div class="card">
                                <h4 style="margin:0 0 10px 0; font-size:14px;">${item.name}</h4>
                                <a href="${item.url}" target="_blank" style="color:var(--primary); text-decoration:none; font-size:12px; font-weight:bold;">OPEN LINK</a>
                            </div>
                        `;
                    });
                }
            });
        }

        function saveLinkToCloud() {
            const name = document.getElementById('b-name').value;
            const url = document.getElementById('b-url').value;
            if(name && url) {
                db.ref('links').push({ name, url })
                .then(() => {
                    document.getElementById('b-name').value = '';
                    document.getElementById('b-url').value = '';
                    showToast("Link Saved!", "success");
                });
            }
        }

        function logout() {
            auth.signOut().then(() => location.reload());
        }

        function showToast(msg, type) {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = `toast-box ${type}`;
            toast.innerText = msg;
            container.appendChild(toast);
            setTimeout(() => toast.remove(), 3000);
        }

        function filter() {
            const val = document.getElementById('search').value.toLowerCase();
            const cards = document.querySelectorAll('.card');
            cards.forEach(card => {
                card.style.display = card.innerText.toLowerCase().includes(val) ? 'block' : 'none';
            });
        }

        // Clock
        setInterval(() => {
            document.getElementById('clock-display').innerText = new Date().toLocaleTimeString();
        }, 1000);

        // Date & Day
        const days = ['রবিবার','সোমবার','মঙ্গলবার','বুধবার','বৃহস্পতিবার','শুক্রবার','শনিবার'];
        document.getElementById('bengali-day').innerText = days[new Date().getDay()];

    </script>
</body>
</html>
