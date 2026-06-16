<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CHROMA JUMP - ULTRA SMOOTH</title>
    <style>
        :root {
            --rank-color: #64748b;
            --gold: #facc15;
            --bg: #020617;
        }
        body { 
            margin: 0; 
            background: #000; 
            font-family: 'Segoe UI', system-ui, sans-serif; 
            overflow: hidden; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
            height: 100vh; 
            color: #f8fafc; 
            user-select: none;
            -webkit-user-select: none;
        }
        
        #game-wrap { 
            position: relative; 
            box-shadow: 0 0 40px rgba(0,0,0,1), inset 0 0 20px rgba(255,255,255,0.02); 
            border-radius: 40px; 
            overflow: hidden; 
            border: 2px solid rgba(255,255,255,0.05); 
            background: var(--bg); 
            width: 400px; 
            height: 700px;
        }
        
        canvas { display: block; }
        #ui { position: absolute; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; }

        .logo-text {
            font-size: 45px; font-weight: 900; letter-spacing: 8px; margin-bottom: 5px;
            color: var(--rank-color); text-shadow: 0 0 15px var(--rank-color); transition: 0.8s;
        }

        #current-rank-display {
            position: absolute; top: 25px; left: 25px; font-weight: 800; font-size: 10px;
            letter-spacing: 2px; text-transform: uppercase; padding: 7px 15px;
            background: rgba(0,0,0,0.8); border-radius: 30px; 
            border: 2px solid var(--rank-color); color: var(--rank-color);
            box-shadow: 0 0 15px var(--rank-color); transition: 0.8s;
        }

        .score-box { 
            position: absolute; top: 40px; width: 100%; text-align: center; 
            font-size: 42px; font-weight: 900; color: var(--gold); 
            text-shadow: 0 0 10px rgba(250, 204, 21, 0.3); z-index: 10;
        }

        .essence-wrapper {
            position: absolute; top: 100px; left: 50%; transform: translateX(-50%);
            width: 150px; height: 5px; background: rgba(255,255,255,0.05); border-radius: 10px;
        }
        #essence-fill { 
            width: 0%; height: 100%; background: var(--rank-color); 
            border-radius: 10px; box-shadow: 0 0 10px var(--rank-color); transition: 0.3s;
        }

        .screen { 
            position: absolute; width: 100%; height: 100%; 
            background: rgba(2, 6, 23, 0.97); backdrop-filter: blur(20px); 
            display: flex; flex-direction: column; justify-content: center; 
            align-items: center; pointer-events: all; z-index: 20; text-align: center; 
        }
        
        .list-container { 
            width: 88%; background: rgba(255,255,255,0.02); border-radius: 20px; 
            padding: 12px; margin: 15px 0; max-height: 300px; overflow-y: auto;
            border: 1px solid rgba(255,255,255,0.05);
        }
        .entry { display: flex; justify-content: space-between; align-items: center; padding: 12px; border-bottom: 1px solid rgba(255,255,255,0.03); font-size: 13px; }
        .entry-rank { font-size: 9px; opacity: 0.7; text-transform: uppercase; display: block; }

        button { 
            padding: 14px 35px; border: 2px solid var(--rank-color); background: transparent; 
            color: #fff; cursor: pointer; letter-spacing: 3px; text-transform: uppercase; 
            font-weight: 800; border-radius: 15px; transition: 0.4s; margin: 6px; width: 220px;
        }
        button:hover { background: var(--rank-color); color: #000; box-shadow: 0 0 20px var(--rank-color); }
        .btn-small { font-size: 10px; opacity: 0.7; border-color: #334155; width: 180px; }

        .rank-item { display: flex; justify-content: space-between; padding: 10px; font-size: 12px; font-weight: 600; border-bottom: 1px solid rgba(255,255,255,0.05); }
    </style>
</head>
<body>

<div id="game-wrap">
    <canvas id="c"></canvas>
    <div id="ui">
        <div id="current-rank-display">CHƯA CÓ HẠNG</div>
        <div class="score-box" id="sb">0</div>
        <div class="essence-wrapper"><div id="essence-fill"></div></div>
        
        <div id="menu" class="screen">
            <div class="logo-text">CHROMA</div>
            <button onclick="startGame()">BẮT ĐẦU LEO RANK</button>
            <button class="btn-small" onclick="showLeaderboard()">TOP CAO THỦ</button>
            <button class="btn-small" onclick="showRankList()">DANH SÁCH BẬC HẠNG</button>
        </div>

        <div id="rank-list-screen" class="screen" style="display:none;">
            <h3 style="letter-spacing: 5px; color: #94a3b8;">HỆ THỐNG BẬC HẠNG</h3>
            <div class="list-container" id="rank-wiki-list"></div>
            <button onclick="closeScreens()">QUAY LẠI</button>
        </div>

        <div id="leaderboard-screen" class="screen" style="display:none;">
            <h3 style="letter-spacing: 5px; color: var(--gold);">BẢNG VÀNG</h3>
            <div class="list-container" id="lb-list"></div>
            <button onclick="closeScreens()">QUAY LẠI</button>
        </div>

        <div id="game-over" class="screen" style="display:none;">
            <div style="font-size: 12px; letter-spacing: 4px; opacity: 0.6;">KẾT THÚC</div>
            <div style="font-size: 60px; font-weight: 900; color: var(--gold);" id="fs">0</div>
            <div id="rank-name-over" style="font-size: 20px; font-weight: 800; margin: 10px 0 30px 0;">RANK: ---</div>
            <button onclick="startGame()">CHƠI LẠI</button>
            <button class="btn-small" onclick="backToMenu()">MENU CHÍNH</button>
        </div>
    </div>
</div>

<script>
const canvas = document.getElementById('c');
const ctx = canvas.getContext('2d');
canvas.width = 400; canvas.height = 700;

let gameActive = false, score = 0, essence = 0;
let platforms = [], particles = [], trail = [], collectables = [];
let highScores = JSON.parse(localStorage.getItem('chroma_v4_scores')) || [];
let lastTime = 0;

let cameraY = 0;
let targetCameraY = 0;

let rankFlashTimer = 0;
let lastRankName = "Chưa có hạng";
let rainbowHue = 0; // Biến lưu góc xoay màu RGB cầu vồng

const RANKS = [
    { name: "Chưa có hạng", min: 0, color: '#475569' },
    { name: "Đồng", min: 1000, color: '#d97706' },
    { name: "Bạc", min: 2000, color: '#94a3b8' },
    { name: "Vàng", min: 3000, color: '#fbbf24' },
    { name: "Bạch Kim", min: 4000, color: '#f1f5f9' },
    { name: "Kim Cương", min: 5000, color: '#bae6fd' },
    { name: "Tinh Anh 1", min: 10000, color: '#60a5fa' },
    { name: "Tinh Anh 2", min: 12000, color: '#a855f7' },
    { name: "Tinh Anh 3", min: 13000, color: '#8b5cf6' }, 
    { name: "Thách Đấu 1", min: 14000, color: '#22d3ee' },
    { name: "Thách Đấu 2", min: 16000, color: '#06b6d4' },
    { name: "Thách Đấu 3", min: 19000, color: '#f026ff' },
    { name: "Huyền Thoại 1", min: 22000, color: '#f43f5e' },
    { name: "Huyền Thoại 2", min: 26000, color: '#e11d48' },
    { name: "Huyền Thoại 3", min: 29000, color: '#b91c1c' }, 
    { name: "Huyền Thoại Chiến Thần", min: 32000, color: 'rainbow' } // Gán nhãn đặc biệt cho rank cuối
];

const player = { x: 200, y: 450, r: 13, vy: 0, g: 0.4, jump: -12.5, left: false, right: false };

function getRank(p) { 
    for (let i = RANKS.length - 1; i >= 0; i--) {
        if (p >= RANKS[i].min) return RANKS[i];
    }
    return RANKS[0];
}

// Hàm lấy màu sắc động thực tế (Hỗ trợ đổi màu RGB mượt)
function getActiveColor() {
    const rank = getRank(score);
    if (rank.color === 'rainbow') {
        return `hsl(${rainbowHue}, 95%, 55%)`;
    }
    return rank.color;
}

function updateUIMode() {
    const rank = getRank(score);
    const activeColor = getActiveColor();
    
    if (rank.name !== lastRankName) {
        rankFlashTimer = 45; 
        lastRankName = rank.name;
        createExplosion(player.x, player.y - cameraY, '#fff');
    }

    document.documentElement.style.setProperty('--rank-color', activeColor);
    const rd = document.getElementById('current-rank-display');
    rd.innerText = rank.name;
    rd.style.color = activeColor;
    rd.style.borderColor = activeColor;
    rd.style.boxShadow = `0 0 15px ${activeColor}`;
}

function createExplosion(x, y, color) {
    for(let i=0; i<15; i++) {
        particles.push({
            x, y, 
            vx: (Math.random()-0.5)*12, 
            vy: (Math.random()-0.5)*12, 
            life: 1, 
            color: color,
            size: Math.random()*4 + 1.5
        });
    }
}

function spawnPlatform(y) {
    const w = 75 + Math.random() * 40;
    const x = Math.random() * (canvas.width - w);
    const speed = (Math.random() - 0.5) * (Math.min(score, 15000) / 4500 + 2.3);
    platforms.push({ x, y, w, h: 10, speed });
    
    if(Math.random() > 0.65) {
        const coinCount = 4; 
        const spacing = 18; 
        const startX = x + (w / 2) - ((coinCount - 1) * spacing) / 2;
        
        for(let i = 0; i < coinCount; i++) {
            collectables.push({ 
                x: startX + (i * spacing), 
                y: y - 25, 
                collected: false 
            });
        }
    }
}

function startGame() {
    document.querySelectorAll('.screen').forEach(s => s.style.display = 'none');
    
    player.left = false;
    player.right = false;
    player.x = 200; 
    player.y = 500; 
    player.vy = 0;
    
    score = 0; essence = 0;
    cameraY = 0; targetCameraY = 0;
    rankFlashTimer = 0;
    lastRankName = "Chưa có hạng";
    rainbowHue = 0;
    platforms = []; trail = []; collectables = []; particles = [];
    document.getElementById('essence-fill').style.width = '0%';
    document.getElementById('sb').innerText = "0";
    
    platforms.push({ x: 100, y: 540, w: 200, h: 10, speed: 0 });
    
    for(let i=0; i<8; i++) spawnPlatform(i * 85);
    
    gameActive = true;
    updateUIMode();
    lastTime = performance.now();
    requestAnimationFrame(loop);
}

function update(dt) {
    if(!gameActive) return;

    const ratio = dt / 16.666; 

    // Cập nhật góc màu RGB liên tục dựa vào tốc độ khung hình
    rainbowHue = (rainbowHue + 1.8 * ratio) % 360;

    player.vy += player.g * ratio;
    player.y += player.vy * ratio;

    if(player.left) player.x -= 7.0 * ratio; 
    if(player.right) player.x += 7.0 * ratio;
    
    if(player.x + player.r < -10 || player.x - player.r > canvas.width + 10) {
        endGame();
        return;
    }

    trail.push({ x: player.x, y: player.y });
    if(trail.length > 10) trail.shift();

    const activeColor = getActiveColor();

    if(player.vy > 0) {
        platforms.forEach(p => {
            if(player.x + player.r * 0.6 > p.x && 
               player.x - player.r * 0.6 < p.x + p.w && 
               player.y + player.r >= p.y && 
               player.y - player.r < p.y + p.h) {
                
                player.y = p.y - player.r;
                player.vy = player.jump;
                createExplosion(player.x, p.y, activeColor);
            }
        });
    }

    collectables.forEach(c => {
        if(!c.collected && Math.hypot(player.x - c.x, player.y - c.y) < 22) {
            c.collected = true; score += 250; essence += 15;
            if(essence >= 100) { 
                essence = 0; 
                score += 1000; 
                createExplosion(player.x, player.y, '#fff'); 
            }
            document.getElementById('essence-fill').style.width = Math.min(essence, 100) + '%';
            document.getElementById('sb').innerText = score;
            updateUIMode();
        }
    });

    platforms.forEach((p, index) => {
        p.x += p.speed * ratio;
        if(p.x < 0 || p.x + p.w > canvas.width) p.speed *= -1;
        
        if(p.y - cameraY > canvas.height) { 
            platforms.splice(index, 1); 
            spawnPlatform(cameraY - 20); 
        }
    });

    collectables.forEach((c, index) => {
        if (c.y - cameraY > canvas.height) collectables.splice(index, 1);
    });

    const triggerHeight = 350;
    if (player.y - cameraY < triggerHeight) {
        let diff = triggerHeight - (player.y - cameraY);
        targetCameraY -= diff; 
        score += Math.round(diff * 0.5);
        document.getElementById('sb').innerText = score;
        updateUIMode();
    }

    cameraY += (targetCameraY - cameraY) * 0.15 * ratio;

    particles.forEach((p, i) => {
        p.x += p.vx * ratio; 
        p.y += p.vy * ratio; 
        p.life -= 0.03 * ratio;
        if(p.life <= 0) particles.splice(i, 1);
    });

    if (rankFlashTimer > 0) rankFlashTimer -= ratio;

    // Cập nhật lại UI liên tục khi đang ở chế độ RGB
    if(getRank(score).color === 'rainbow') {
        updateUIMode();
    }

    if(player.y - cameraY > canvas.height + player.r) endGame();
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    let rc = getActiveColor();

    if (rankFlashTimer > 0 && Math.floor(rankFlashTimer / 4) % 2 === 0) {
        rc = "#ffffff"; 
    }

    ctx.save();
    ctx.translate(0, -cameraY);

    trail.forEach((t, i) => {
        ctx.fillStyle = rc; ctx.globalAlpha = i / 22;
        ctx.beginPath(); ctx.arc(t.x, t.y, player.r * (0.5 + (i/trail.length)*0.5), 0, Math.PI*2); ctx.fill();
    });
    ctx.globalAlpha = 1;

    particles.forEach(p => {
        ctx.fillStyle = p.color; ctx.globalAlpha = p.life;
        ctx.beginPath(); ctx.arc(p.x, p.y, Math.max(0.1, p.size), 0, Math.PI*2); ctx.fill();
    });
    ctx.globalAlpha = 1;

    platforms.forEach(p => {
        ctx.fillStyle = rc; ctx.globalAlpha = 0.8;
        ctx.beginPath(); ctx.roundRect(p.x, p.y, p.w, p.h, 5); ctx.fill();
    });
    ctx.globalAlpha = 1;

    collectables.forEach(c => {
        if(!c.collected) {
            ctx.fillStyle = '#facc15'; ctx.shadowBlur = 12; ctx.shadowColor = '#facc15';
            ctx.beginPath(); ctx.arc(c.x, c.y, 7, 0, Math.PI*2); ctx.fill();
            ctx.shadowBlur = 0;
        }
    });

    ctx.save();
    ctx.shadowBlur = rankFlashTimer > 0 ? 35 : 20; 
    ctx.shadowColor = rc;
    ctx.fillStyle = '#fff';
    ctx.beginPath(); ctx.arc(player.x, player.y, player.r, 0, Math.PI*2); ctx.fill();
    ctx.strokeStyle = rc; ctx.lineWidth = 3.0; ctx.stroke();
    ctx.restore();

    ctx.restore(); 
}

function endGame() {
    gameActive = false;
    const rank = getRank(score);
    const finalColor = getActiveColor();
    document.getElementById('fs').innerText = score;
    document.getElementById('rank-name-over').innerText = `HẠNG: ${rank.name}`;
    document.getElementById('rank-name-over').style.color = finalColor;
    document.getElementById('game-over').style.display = 'flex';
    
    setTimeout(() => {
        let name = prompt("NHẬP TÊN ANH HÙNG:", "PLAYER");
        if(name) {
            highScores.push({ name: name.substring(0,10), score: score, rank: rank.name, color: finalColor });
            highScores.sort((a,b) => b.score - a.score);
            highScores = highScores.slice(0, 15);
            localStorage.setItem('chroma_v4_scores', JSON.stringify(highScores));
        }
    }, 150);
}

function showLeaderboard() {
    const list = document.getElementById('lb-list');
    list.innerHTML = highScores.map((s, i) => `
        <div class="entry">
            <div>
                <span style="font-weight:900; color:#475569">#${i+1}</span> 
                <span style="margin-left:10px; font-weight:700">${s.name}</span>
                <span class="entry-rank" style="color:${s.color}">${s.rank}</span>
            </div>
            <strong style="color:var(--gold); font-size:16px">${s.score}</strong>
        </div>`).join('') || '<p style="opacity:0.5; padding:20px;">Chưa có kỷ lục nào</p>';
    document.getElementById('leaderboard-screen').style.display = 'flex';
}

function showRankList() {
    const list = document.getElementById('rank-wiki-list');
    const displayRanks = [...RANKS].reverse();
    list.innerHTML = displayRanks.map(r => `
        <div class="rank-item" style="color:${r.color === 'rainbow' ? '#ff003c' : r.color}">
            <span>${r.name}</span>
            <span>${r.min.toLocaleString()}+</span>
        </div>`).join('');
    document.getElementById('rank-list-screen').style.display = 'flex';
}

function closeScreens() { 
    document.getElementById('rank-list-screen').style.display = 'none';
    document.getElementById('leaderboard-screen').style.display = 'none';
    document.getElementById('menu').style.display = 'flex';
}

function backToMenu() {
    document.getElementById('game-over').style.display = 'none';
    document.getElementById('menu').style.display = 'flex';
    score = 0;
    updateUIMode();
}

function loop(timestamp) { 
    if(!gameActive) return;
    
    let dt = timestamp - lastTime;
    
    if (dt > 60) dt = 16.666; 
    lastTime = timestamp;

    update(dt); 
    draw(); 
    requestAnimationFrame(loop); 
}

const activeKeys = {};
window.addEventListener('keydown', e => {
    activeKeys[e.key] = true;
    if(activeKeys['ArrowLeft'] || activeKeys['a'] || activeKeys['A']) player.left = true;
    if(activeKeys['ArrowRight'] || activeKeys['d'] || activeKeys['D']) player.right = true;
});

window.addEventListener('keyup', e => {
    delete activeKeys[e.key];
    if(!activeKeys['ArrowLeft'] && !activeKeys['a'] && !activeKeys['A']) player.left = false;
    if(!activeKeys['ArrowRight'] && !activeKeys['d'] && !activeKeys['D']) player.right = false;
});

canvas.addEventListener('touchstart', e => {
    const touchX = e.touches[0].clientX - canvas.getBoundingClientRect().left;
    if (touchX < canvas.width / 2) {
        player.left = true;
    } else {
        player.right = true;
    }
}, {passive: true});

canvas.addEventListener('touchend', e => {
    player.left = false;
    player.right = false;
});
</script>
</body>
</html>
