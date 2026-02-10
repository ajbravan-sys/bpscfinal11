<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BPSC AI Hub</title>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <style>
        :root { --primary: #800000; }
        body { font-family: sans-serif; background: #f4f4f4; margin: 0; }
        header { background: var(--primary); color: white; padding: 15px; text-align: center; font-weight: bold; }
        .container { width: 90%; max-width: 400px; margin: 20px auto; background: white; padding: 20px; border-radius: 12px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); text-align: center; }
        .hidden { display: none !important; }
        button { background: var(--primary); color: white; border: none; padding: 12px; width: 100%; cursor: pointer; border-radius: 8px; font-weight: bold; margin-bottom: 10px; }
        input { width: 90%; padding: 12px; margin: 10px 0; border: 1px solid #ccc; border-radius: 8px; }
        #quiz-box { height: 50vh; overflow-y: auto; background: #e5ddd5; padding: 15px; border-radius: 10px; text-align: left; }
    </style>
</head>
<body>
    <div id="auth-page">
        <header>BPSC AI LOGIN</header>
        <div class="container">
            <input type="email" id="email" placeholder="Email ID">
            <input type="password" id="pass" placeholder="Password">
            <button onclick="login()">Enter Portal</button>
        </div>
    </div>

    <div id="menu-page" class="hidden">
        <header>BPSC DASHBOARD</header>
        <div class="container">
            <button onclick="startPractice()">📖 Self Practice</button>
            <button onclick="location.reload()" style="background:#555">Logout</button>
        </div>
    </div>

    <div id="quiz-page" class="hidden">
        <header>Practice Mode</header>
        <div class="container">
            <div id="quiz-box"></div>
            <button onclick="fetchQ()" style="margin-top:10px;">Next Question</button>
            <button onclick="location.reload()" style="background:#555">Back</button>
        </div>
    </div>

    <script>
        const firebaseConfig = {
            apiKey: "AIzaSyAvomznD7NeC5VAlA2KwJ_d7e-wAzFefl4",
            authDomain: "mysecretbpsc.firebaseapp.com",
            projectId: "mysecretbpsc",
            storageBucket: "mysecretbpsc.firebasestorage.app",
            messagingSenderId: "179352173594",
            appId: "1:179352173594:web:24f94e1300ab8ebc72e5a3"
        };
        const GEMINI_KEY = "AIzaSyA8Bt1YwE9fPyDWKtZOXlaxmG7usPNHvPU";

        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();

        async function login() {
            const e = document.getElementById('email').value;
            const p = document.getElementById('pass').value;
            try {
                await auth.signInWithEmailAndPassword(e, p).catch(() => auth.createUserWithEmailAndPassword(e, p));
                document.getElementById('auth-page').classList.add('hidden');
                document.getElementById('menu-page').classList.remove('hidden');
            } catch(err) { alert(err.message); }
        }

        function startPractice() {
            document.getElementById('menu-page').classList.add('hidden');
            document.getElementById('quiz-page').classList.remove('hidden');
            fetchQ();
        }

        async function fetchQ() {
            const box = document.getElementById('quiz-box');
            box.innerHTML = "Sawal aa raha hai...";
            try {
                const res = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${GEMINI_KEY}`, {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json'},
                    body: JSON.stringify({ contents: [{ parts: [{ text: "1 hard BPSC question in Hindi. Format: Q | A" }] }] })
                });
                const data = await res.json();
                const [q, a] = data.candidates[0].content.parts[0].text.split('|');
                box.innerHTML = `<b>Q:</b> ${q}<br><br><button onclick="alert('Ans: ${a}')" style="background:#444; font-size:12px; width:auto; padding:5px 10px;">Show Answer</button>`;
            } catch(e) { box.innerHTML = "API Error! Key check karein."; }
        }
    </script>
</body>
</html>
