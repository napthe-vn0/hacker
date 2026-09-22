<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML Viewer - Dark Mode</title>
    <style>
        :root {
            --bg: #0d1117;
            --card: #161b22;
            --text: #c9d1d9;
            --border: #30363d;
            --accent: #238636;
            --copy-bg: #1f6feb;
            --code-text: #7ee787;
        }

        [data-theme="light"] {
            --bg: #f6f8fa;
            --card: #ffffff;
            --text: #24292f;
            --border: #d0d7de;
            --accent: #1f883d;
            --copy-bg: #0969da;
            --code-text: #0550ae;
        }

        body {
            font-family: monospace;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 15px;
            transition: 0.3s;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            background: var(--card);
            padding: 15px;
            border-radius: 8px;
            border: 1px solid var(--border);
        }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 12px;
        }

        .theme-btn {
            background: transparent;
            border: 1px solid var(--border);
            color: var(--text);
            padding: 6px 10px;
            border-radius: 5px;
            cursor: pointer;
        }

        input {
            width: 100%;
            padding: 10px;
            background: var(--bg);
            border: 1px solid var(--border);
            color: var(--text);
            border-radius: 6px;
            box-sizing: border-box;
            margin-bottom: 10px;
        }

        .btn {
            width: 100%;
            padding: 10px;
            color: white;
            border: none;
            border-radius: 6px;
            font-weight: bold;
            cursor: pointer;
            margin-bottom: 8px;
        }

        .run-btn {
            background: var(--accent);
        }

        .copy-btn {
            background: var(--copy-bg);
        }

        pre {
            background: var(--bg);
            color: var(--code-text);
            padding: 10px;
            border-radius: 6px;
            border: 1px solid var(--border);
            max-height: 380px;
            overflow: auto;
            white-space: pre-wrap;
            word-break: break-all;
            margin-top: 10px;
            font-size: 11px;
        }
    </style>
</head>
<body>

<div class="container">
    <div class="header">
        <b style="font-size: 16px;">Source Code Viewer</b>
        <button class="theme-btn" onclick="toggleTheme()" id="btnTheme">🌙 Tối</button>
    </div>

    <input type="text" id="targetUrl" placeholder="Nhập link (Ví dụ: https://vi.wikipedia.org)">
    <button class="btn run-btn" onclick="fetchHTML()">⚡ BẮT ĐẦU LẤY HTML</button>
    <button class="btn copy-btn" onclick="copyCode()">📋 SAO CHÉP MÃ NGOÙN</button>

    <pre id="output">Mã nguồn sẽ hiển thị ở đây...</pre>
</div>

<script>
    function toggleTheme() {
        const body = document.body;
        const btn = document.getElementById('btnTheme');
        if (body.getAttribute('data-theme') === 'light') {
            body.removeAttribute('data-theme');
            btn.innerText = '🌙 Tối';
        } else {
            body.setAttribute('data-theme', 'light');
            btn.innerText = '☀️ Sáng';
        }
    }

        async function fetchHTML() {
        let url = document.getElementById('targetUrl').value.trim();
        const out = document.getElementById('output');

        if (!url) return alert('Nhập link vào đã bạn ơi!');

        // Tự động thêm https:// nếu quên nhập
        if (!url.startsWith('http://') && !url.startsWith('https://')) {
            url = 'https://' + url;
        }

        out.innerText = 'Đang kết nối Server Proxy...';

        // Bộ Proxy công cộng
        const proxies = [
            `https://api.codetabs.com/v1/proxy?quest=${encodeURIComponent(url)}`,
            `https://corsproxy.io/?${encodeURIComponent(url)}`,
            `https://api.allorigins.win/get?url=${encodeURIComponent(url)}`
        ];

        for (let i = 0; i < proxies.length; i++) {
            try {
                out.innerText = `Đang thử kết nối Server ${i + 1}...`;
                const res = await fetch(proxies[i]);
                
                if (!res.ok) continue;

                let data;
                if (i === 2) {
                    const json = await res.json();
                    data = json.contents;
                } else {
                    data = await res.text();
                }

                if (data && data.length > 50) {
                    out.innerText = data;
                    return;
                }
            } catch (e) {
                console.log(`Proxy ${i + 1} không phản hồi...`);
            }
        }

        out.innerText = 'Lỗi: Không lấy được code từ trang này (CÓ THỂ DO TRANG CHẶN PROXY HẶC BẠN DÙNG WEB TRÊN VERCEL). Hãy chạy Preview trực tiếp trong Acode nhé!';
    }

    function copyCode() {
        const code = document.getElementById('output').innerText;
        if (!code || code === 'Mã nguồn sẽ hiển thị ở đây...' || code.includes('Đang')) {
            alert("Chưa có code để sao chép đâu bro!");
            return;
        }
        
        navigator.clipboard.writeText(code).then(() => {
            alert("Đã copy toàn bộ mã nguồn!");
        }).catch(err => {
            alert("Lỗi sao chép: " + err);
        });
    }
</script>

</body>
</html>

