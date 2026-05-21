
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>小龙虾钓手 · 关注解锁欢乐钓鱼</title>
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            margin: 0;
            min-height: 100vh;
            background: linear-gradient(145deg, #1a472a 0%, #0e2a1a 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', 'Comic Neue', 'Chalkboard SE', 'Comic Sans MS', 'cursive', sans-serif;
            padding: 20px;
            box-sizing: border-box;
        }

        .game-container {
            position: relative;
            background: #2b4b2f;
            border-radius: 48px;
            padding: 20px 20px 24px;
            box-shadow: 0 20px 35px rgba(0, 0, 0, 0.4), inset 0 1px 4px rgba(255, 255, 255, 0.2);
        }

        canvas {
            display: block;
            margin: 0 auto;
            border-radius: 32px;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
            cursor: pointer;
            background: #1e3b2c;
            width: 100%;
            height: auto;
        }

        /* 关注遮罩层 - 华丽的解锁界面 */
        .follow-overlay {
            position: absolute;
            top: 20px;
            left: 20px;
            right: 20px;
            bottom: 24px;
            background: rgba(10, 25, 10, 0.85);
            backdrop-filter: blur(8px);
            border-radius: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 20;
            transition: all 0.3s ease;
            box-shadow: inset 0 0 30px rgba(0,0,0,0.3), 0 8px 20px rgba(0,0,0,0.2);
        }

        .follow-card {
            background: #fff7e8;
            border-radius: 64px;
            padding: 24px 32px;
            text-align: center;
            max-width: 280px;
            width: 80%;
            box-shadow: 0 20px 35px rgba(0,0,0,0.3);
            border: 3px solid #ffbc6e;
            animation: bounceSoft 0.5s ease-out;
        }

        @keyframes bounceSoft {
            0% { transform: scale(0.9); opacity: 0; }
            80% { transform: scale(1.02); }
            100% { transform: scale(1); opacity: 1; }
        }

        .follow-card h2 {
            font-size: 1.9rem;
            margin: 0 0 8px 0;
            color: #d2691e;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
        }

        .follow-card p {
            font-size: 1rem;
            color: #3b2a1f;
            margin: 12px 0;
            line-height: 1.4;
        }

        .qrcode-mock {
            background: #f0e2b6;
            width: 90px;
            height: 90px;
            margin: 12px auto;
            border-radius: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.8rem;
            box-shadow: inset 0 0 0 3px #dba642, 0 6px 0 #b57c2a;
        }

        .btn-follow {
            background: #ff9f4a;
            border: none;
            font-size: 1.4rem;
            font-weight: bold;
            padding: 12px 20px;
            border-radius: 50px;
            color: #2c1a0a;
            cursor: pointer;
            font-family: inherit;
            box-shadow: 0 5px 0 #b4621a;
            transition: 0.07s linear;
            margin-top: 16px;
            width: 100%;
            touch-action: manipulation;
        }

        .btn-follow:active {
            transform: translateY(3px);
            box-shadow: 0 2px 0 #b4621a;
        }

        .info-panel {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 18px;
            padding: 8px 20px;
            background: #f9e7c2;
            border-radius: 60px;
            box-shadow: inset 0 1px 4px rgba(0,0,0,0.1), 0 5px 10px rgba(0,0,0,0.2);
        }

        .score-box {
            background: #5a3e1b;
            padding: 5px 18px;
            border-radius: 40px;
            color: #ffefb9;
            font-weight: bold;
            font-size: 1.7rem;
            text-shadow: 2px 2px 0 #2d1f0c;
            letter-spacing: 2px;
        }

        .score-box span {
            font-size: 1rem;
            margin-right: 8px;
        }

        .reset-btn {
            background: #ff9f4a;
            border: none;
            font-size: 1.3rem;
            font-weight: bold;
            padding: 6px 18px;
            border-radius: 40px;
            color: #2c1a0a;
            cursor: pointer;
            font-family: inherit;
            box-shadow: 0 4px 0 #b4621a;
            transition: 0.07s linear;
            touch-action: manipulation;
        }

        .reset-btn:active {
            transform: translateY(2px);
            box-shadow: 0 1px 0 #b4621a;
        }

        .tip {
            text-align: center;
            margin-top: 12px;
            font-size: 0.9rem;
            color: #f9e0a0;
            background: #2c4b27b3;
            border-radius: 50px;
            padding: 6px 12px;
            backdrop-filter: blur(3px);
            font-weight: bold;
        }

        @media (max-width: 650px) {
            .score-box { font-size: 1.3rem; padding: 3px 14px; }
            .reset-btn { font-size: 1rem; padding: 4px 14px; }
            .tip { font-size: 0.75rem; }
            .follow-card h2 { font-size: 1.5rem; }
        }
        
        /* 隐藏遮罩 */
        .follow-overlay.hide {
            display: none;
        }
    </style>
</head>
<body>
<div>
    <div class="game-container" id="gameContainer">
        <canvas id="gameCanvas" width="900" height="600" style="width:100%; height:auto; max-width:900px; aspect-ratio:900/600"></canvas>
        
        <!-- 关注公众号遮罩层 -->
        <div class="follow-overlay" id="followOverlay">
            <div class="follow-card">
                <h2>🦞 钓虾大作战 🎣</h2>
                <div class="qrcode-mock">
                    📱🐉
                </div>
                <p>⭐ 关注公众号 <strong style="color:#c24916;">「小龙虾钓鱼大师」</strong> ⭐<br>解锁黄金钓竿，即刻开钓！</p>
                <button class="btn-follow" id="unlockBtn">✅ 我已关注，开始游戏</button>
                <p style="font-size:0.7rem; margin-top:12px;">(纯欢乐解锁，点击即可玩耍~)</p>
            </div>
        </div>
        
        <div class="info-panel">
            <div class="score-box"><span>🦞 积分</span> <span id="scoreValue">0</span></div>
            <button class="reset-btn" id="resetButton">🎣 重新下钩</button>
        </div>
        <div class="tip">
            💡 关注后解锁！点击画面 / 空格键 甩钩！ 钓到小龙虾自动收回，一只+20分～ 绳子摆动更温柔啦！
        </div>
    </div>
</div>

<script>
    (function(){
        // ---------- 游戏是否解锁（前置条件）----------
        let gameEnabled = false;      // 必须关注公众号后才能变成true
        
        // ---------- CANVAS ----------
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = 900;
        canvas.height = 600;
        
        // ---------- 游戏参数 ----------
        let score = 0;              
        let crayfishArray = [];     
        
        // 钩子系统
        const HOOK_BASE = { x: canvas.width/2, y: 80 };
        const REST_LENGTH = 45;      
        const MAX_LENGTH = 420;      
        const HOOK_RADIUS = 12;       
        
        let currentLength = REST_LENGTH;   
        let angle = 0;                     
        // ✅ 减慢绳子摆动速度 (原0.023 => 0.012，更柔和)
        let angleDelta = 0.012;            
        let minAngle = -Math.PI / 2.8;     
        let maxAngle = Math.PI / 2.8;      
        
        let gameState = "swing";    
        let caughtTarget = null;      
        let floatingTexts = [];       
        let shootCooldown = false;
        
        const BASE_SCORE = 20;
        
        // ---------- UI 元素 ----------
        const scoreSpan = document.getElementById('scoreValue');
        const resetBtn = document.getElementById('resetButton');
        const followOverlay = document.getElementById('followOverlay');
        const unlockButton = document.getElementById('unlockBtn');
        
        // ---------- 辅助函数 ----------
        function updateScoreUI() {
            if(scoreSpan) scoreSpan.innerText = Math.floor(score);
        }
        
        function addFloatingText(msg, x, y) {
            floatingTexts.push({
                text: msg,
                x: x,
                y: y,
                life: 1.0
            });
        }
        
        function addPoints(amount, worldX, worldY) {
            score += amount;
            updateScoreUI();
            addFloatingText(`+${amount} 🦞`, worldX, worldY);
        }
        
        // 生成一只可爱小龙虾
        function generateOneCrayfish() {
            const radius = 18 + Math.random() * 4;
            let safe = false;
            let tryX, tryY;
            let attempts = 0;
            while(!safe && attempts < 40) {
                tryX = 70 + Math.random() * (canvas.width - 140);
                tryY = 180 + Math.random() * (canvas.height - 220);
                const dxToBase = tryX - HOOK_BASE.x;
                const dyToBase = tryY - HOOK_BASE.y;
                const distToBase = Math.hypot(dxToBase, dyToBase);
                if(distToBase > 65 && tryY > 130) {
                    let overlap = false;
                    for(let c of crayfishArray) {
                        if(Math.hypot(tryX - c.x, tryY - c.y) < (radius + c.radius + 6)) {
                            overlap = true;
                            break;
                        }
                    }
                    if(!overlap) safe = true;
                }
                attempts++;
            }
            return {
                x: tryX,
                y: tryY,
                radius: radius,
                value: BASE_SCORE,
            };
        }
        
        function initCrayfish(count = 8) {
            const newArray = [];
            for(let i=0; i<count; i++) {
                newArray.push(generateOneCrayfish());
            }
            return newArray;
        }
        
        function replenishCrayfish() {
            if(!gameEnabled) return;
            let need = 6 - crayfishArray.length;
            if(need > 0) {
                for(let i=0; i<need; i++) {
                    crayfishArray.push(generateOneCrayfish());
                }
            }
            if(crayfishArray.length > 14) crayfishArray = crayfishArray.slice(0,14);
        }
        
        // 重置游戏（仅在解锁后生效）
        function resetGame() {
            if(!gameEnabled) return;   // 未关注公众号不能重置/开始
            score = 0;
            updateScoreUI();
            crayfishArray = initCrayfish(8);
            caughtTarget = null;
            gameState = "swing";
            currentLength = REST_LENGTH;
            angle = 0;
            angleDelta = 0.012;    // 保持温柔摆动
            floatingTexts = [];
            shootCooldown = false;
        }
        
        function getHookTip() {
            const tipX = HOOK_BASE.x + Math.sin(angle) * currentLength;
            const tipY = HOOK_BASE.y + Math.cos(angle) * currentLength;
            return { x: tipX, y: tipY };
        }
        
        function checkCollisionWithCrayfish(tipX, tipY) {
            for(let i = 0; i < crayfishArray.length; i++) {
                const crab = crayfishArray[i];
                const dist = Math.hypot(tipX - crab.x, tipY - crab.y);
                if(dist < HOOK_RADIUS + crab.radius) {
                    return { index: i, crayfish: crab };
                }
            }
            return null;
        }
        
        // 发射钩子 (只有解锁+摆动状态可用)
        function shootHook() {
            if(!gameEnabled) return false;
            if(gameState !== "swing") return false;
            gameState = "shoot";
            currentLength = REST_LENGTH;
            return true;
        }
        
        function finishRetrieve() {
            if(caughtTarget !== null) {
                addPoints(caughtTarget.value, caughtTarget.x, caughtTarget.y);
                caughtTarget = null;
            }
            currentLength = REST_LENGTH;
            gameState = "swing";
            replenishCrayfish();
            shootCooldown = false;
        }
        
        // 物理更新（仅当解锁后）
        function updateGame() {
            if(!gameEnabled) return;  // 未解锁时不更新游戏逻辑，保持冻结
            
            if(gameState === "swing") {
                angle += angleDelta;
                if(angle > maxAngle) {
                    angle = maxAngle;
                    angleDelta = -Math.abs(angleDelta);
                }
                if(angle < minAngle) {
                    angle = minAngle;
                    angleDelta = Math.abs(angleDelta);
                }
                currentLength = REST_LENGTH;
            }
            else if(gameState === "shoot") {
                let extendStep = 7;
                let newLength = currentLength + extendStep;
                if(newLength >= MAX_LENGTH) {
                    newLength = MAX_LENGTH;
                    gameState = "retract";
                }
                currentLength = newLength;
                const tip = getHookTip();
                if(tip.x < 15 || tip.x > canvas.width - 15 || tip.y > canvas.height - 20 || tip.y < 20) {
                    gameState = "retract";
                }
                if(gameState === "shoot" && caughtTarget === null) {
                    const collision = checkCollisionWithCrayfish(tip.x, tip.y);
                    if(collision) {
                        const caughtCrab = collision.crayfish;
                        const idx = crayfishArray.findIndex(c => c === caughtCrab);
                        if(idx !== -1) crayfishArray.splice(idx, 1);
                        caughtTarget = {
                            x: caughtCrab.x,
                            y: caughtCrab.y,
                            radius: caughtCrab.radius,
                            value: caughtCrab.value
                        };
                        gameState = "retract";
                    }
                }
            }
            else if(gameState === "retract") {
                let retractStep = 6;
                let newLength = currentLength - retractStep;
                if(newLength <= REST_LENGTH) {
                    newLength = REST_LENGTH;
                    finishRetrieve();
                }
                currentLength = newLength;
                if(caughtTarget !== null) {
                    const tip = getHookTip();
                    caughtTarget.x = tip.x;
                    caughtTarget.y = tip.y;
                }
                const tipNow = getHookTip();
                if(tipNow.x < 0 || tipNow.x > canvas.width || tipNow.y > canvas.height + 30) {
                    if(caughtTarget !== null) {
                        addPoints(caughtTarget.value, tipNow.x, tipNow.y);
                        caughtTarget = null;
                    }
                    gameState = "swing";
                    currentLength = REST_LENGTH;
                    replenishCrayfish();
                }
            }
            
            // 漂浮文字更新
            for(let i=0; i<floatingTexts.length; i++) {
                floatingTexts[i].life -= 0.02;
                floatingTexts[i].y -= 1.2;
                if(floatingTexts[i].life <= 0) {
                    floatingTexts.splice(i,1);
                    i--;
                }
            }
        }
        
        // ---------- 华丽绘图部分 ----------
        function drawBackground() {
            const grad = ctx.createLinearGradient(0, 0, 0, canvas.height);
            grad.addColorStop(0, '#3b835f');
            grad.addColorStop(0.5, '#246e4a');
            grad.addColorStop(1, '#125d38');
            ctx.fillStyle = grad;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            for(let i=0; i<100; i++) {
                ctx.fillStyle = `rgba(200, 240, 210, ${0.03 + Math.random()*0.05})`;
                ctx.beginPath();
                ctx.arc(Math.random()*canvas.width, Math.random()*canvas.height, Math.random()*8+2, 0, Math.PI*2);
                ctx.fill();
            }
            ctx.fillStyle = '#3c9e5a';
            for(let i=0; i<12; i++) {
                let baseX = 30 + i*70;
                ctx.beginPath();
                ctx.moveTo(baseX, canvas.height);
                ctx.quadraticCurveTo(baseX-15, canvas.height-45, baseX+5, canvas.height-85);
                ctx.quadraticCurveTo(baseX+20, canvas.height-50, baseX+35, canvas.height);
                ctx.fill();
            }
            for(let i=0;i<15;i++) {
                ctx.beginPath();
                ctx.arc(60 + i*70, canvas.height-40 - (Date.now()*0.003 + i*23) % 120, 3+Math.sin(Date.now()*0.005+i)*1.5, 0, Math.PI*2);
                ctx.fillStyle = 'rgba(210, 255, 230, 0.5)';
                ctx.fill();
            }
        }
        
        function drawCrayfish(c) {
            ctx.save();
            const x = c.x;
            const y = c.y;
            const r = c.radius;
            ctx.beginPath();
            ctx.ellipse(x, y, r*0.9, r*0.75, 0, 0, Math.PI*2);
            ctx.fillStyle = '#e35f21';
            ctx.fill();
            ctx.strokeStyle = '#b13e0c';
            ctx.lineWidth = 1.5;
            ctx.stroke();
            ctx.fillStyle = 'white';
            ctx.beginPath();
            ctx.arc(x - r*0.35, y - r*0.2, r*0.2, 0, Math.PI*2);
            ctx.fill();
            ctx.beginPath();
            ctx.arc(x + r*0.35, y - r*0.2, r*0.2, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = '#040404';
            ctx.beginPath();
            ctx.arc(x - r*0.35, y - r*0.25, r*0.08, 0, Math.PI*2);
            ctx.fill();
            ctx.beginPath();
            ctx.arc(x + r*0.35, y - r*0.25, r*0.08, 0, Math.PI*2);
            ctx.fill();
            ctx.beginPath();
            ctx.moveTo(x - r*0.85, y + r*0.1);
            ctx.lineTo(x - r*1.2, y + r*0.3);
            ctx.lineTo(x - r*0.9, y + r*0.5);
            ctx.fillStyle = '#dc6028';
            ctx.fill();
            ctx.beginPath();
            ctx.moveTo(x + r*0.85, y + r*0.1);
            ctx.lineTo(x + r*1.2, y + r*0.3);
            ctx.lineTo(x + r*0.9, y + r*0.5);
            ctx.fill();
            ctx.beginPath();
            ctx.moveTo(x - r*0.5, y - r*0.55);
            ctx.quadraticCurveTo(x - r*0.9, y - r*0.85, x - r*1.0, y - r*0.5);
            ctx.strokeStyle = '#c0612b';
            ctx.lineWidth = 2;
            ctx.stroke();
            ctx.beginPath();
            ctx.moveTo(x + r*0.5, y - r*0.55);
            ctx.quadraticCurveTo(x + r*0.9, y - r*0.85, x + r*1.0, y - r*0.5);
            ctx.stroke();
            ctx.beginPath();
            ctx.strokeStyle = '#b24d20';
            ctx.lineWidth = 1.8;
            for(let i=-1; i<=1; i++) {
                ctx.beginPath();
                ctx.moveTo(x + i*r*0.5, y - r*0.1);
                ctx.lineTo(x + i*r*0.2, y + r*0.35);
                ctx.stroke();
            }
            ctx.fillStyle = '#ffb77c';
            ctx.beginPath();
            ctx.ellipse(x - r*0.2, y - r*0.35, r*0.15, r*0.1, 0, 0, Math.PI*2);
            ctx.fill();
            ctx.restore();
        }
        
        function drawHookAndRope() {
            const tip = getHookTip();
            ctx.beginPath();
            ctx.moveTo(HOOK_BASE.x, HOOK_BASE.y);
            ctx.lineTo(tip.x, tip.y);
            ctx.strokeStyle = '#7a5d3c';
            ctx.lineWidth = 3;
            ctx.stroke();
            ctx.beginPath();
            ctx.moveTo(HOOK_BASE.x, HOOK_BASE.y);
            ctx.lineTo(tip.x, tip.y);
            ctx.strokeStyle = '#c99e6f';
            ctx.lineWidth = 1.5;
            ctx.stroke();
            ctx.save();
            ctx.shadowBlur = 4;
            ctx.shadowColor = "rgba(0,0,0,0.3)";
            ctx.beginPath();
            ctx.arc(tip.x, tip.y, HOOK_RADIUS-2, 0, Math.PI*2);
            ctx.fillStyle = '#ffc857';
            ctx.fill();
            ctx.beginPath();
            ctx.moveTo(tip.x+3, tip.y-2);
            ctx.lineTo(tip.x+11, tip.y-8);
            ctx.lineTo(tip.x+6, tip.y-1);
            ctx.fillStyle = '#d68d2a';
            ctx.fill();
            ctx.beginPath();
            ctx.arc(tip.x-2, tip.y+1, 3, 0, Math.PI*2);
            ctx.fillStyle = '#cea043';
            ctx.fill();
            ctx.restore();
            if(caughtTarget !== null && gameState !== "swing") {
                drawCrayfish(caughtTarget);
            }
        }
        
        function drawRod() {
            ctx.fillStyle = '#b97f48';
            ctx.beginPath();
            ctx.rect(HOOK_BASE.x-20, HOOK_BASE.y-12, 40, 25);
            ctx.fill();
            ctx.fillStyle = '#e4a86a';
            ctx.beginPath();
            ctx.ellipse(HOOK_BASE.x, HOOK_BASE.y-6, 12, 8, 0, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = '#d48d3b';
            ctx.beginPath();
            ctx.rect(HOOK_BASE.x-5, HOOK_BASE.y-18, 10, 20);
            ctx.fill();
        }
        
        function drawFloatingTexts() {
            for(let t of floatingTexts) {
                ctx.font = `bold ${22 + (1-t.life)*6}px "Comic Neue", cursive`;
                ctx.fillStyle = `rgba(255, 235, 140, ${t.life})`;
                ctx.shadowColor = "rgba(0,0,0,0.5)";
                ctx.fillText(t.text, t.x-20, t.y-20);
                ctx.fillStyle = `rgba(255, 100, 30, ${t.life*0.8})`;
                ctx.fillText(t.text, t.x-22, t.y-22);
            }
        }
        
        function drawStatusHelper() {
            if(!gameEnabled) return;
            if(gameState === "swing") {
                ctx.font = "bold 20px 'Segoe UI', system-ui";
                ctx.fillStyle = "#fff9cf";
                ctx.shadowBlur = 3;
                ctx.fillText("⚡ 点击画面 甩钩子！", canvas.width-200, 45);
            } else if(gameState === "shoot") {
                ctx.font = "bold 18px cursive";
                ctx.fillStyle = "#dbf5b0";
                ctx.fillText("🎣 下钩中...", canvas.width-160, 45);
            } else if(gameState === "retract") {
                ctx.font = "bold 18px cursive";
                ctx.fillStyle = "#ffe2a4";
                ctx.fillText("🦞 收杆啦！", canvas.width-150, 45);
            }
        }
        
        function render() {
            drawBackground();
            for(let crab of crayfishArray) {
                drawCrayfish(crab);
            }
            drawRod();
            drawHookAndRope();
            drawFloatingTexts();
            drawStatusHelper();
            ctx.font = "bold 22px monospace";
            ctx.fillStyle = "#ffe2aa";
            ctx.shadowBlur = 2;
            ctx.fillText(`🦐  ${crayfishArray.length} 只小龙虾`, 25, 52);
            ctx.font = "bold 18px cursive";
            ctx.fillStyle = "#ffffbb";
            ctx.fillText("钓上来 +20分", 25, 92);
            
            // 未解锁时额外加一把可爱锁的提示（视觉效果）
            if(!gameEnabled) {
                ctx.font = "bold 34px 'Segoe UI'";
                ctx.fillStyle = "#fff1b5";
                ctx.shadowBlur = 8;
                ctx.fillText("🔒 关注公众号解锁", canvas.width/2-150, canvas.height-70);
            }
        }
        
        // ---------- 解锁游戏（前置条件达成）----------
        function unlockGameAndStart() {
            if(gameEnabled) return;
            gameEnabled = true;
            // 隐藏遮罩
            if(followOverlay) followOverlay.classList.add('hide');
            // 真正开始游戏：重置所有状态
            resetGame();          // 此时gameEnabled=true，会生成小龙虾，分数归零
            // 额外确保物理状态正确
            gameState = "swing";
            currentLength = REST_LENGTH;
            angle = 0;
            caughtTarget = null;
            updateScoreUI();
        }
        
        // ---------- 事件绑定 (仅当解锁后可用，但shootHook内部已判断gameEnabled) ----------
        function bindGameEvents() {
            canvas.addEventListener('click', (e) => {
                e.preventDefault();
                shootHook();
            });
            canvas.addEventListener('touchstart', (e) => {
                e.preventDefault();
                shootHook();
            });
            window.addEventListener('keydown', (e) => {
                if(e.code === 'Space' || e.code === 'ArrowDown') {
                    e.preventDefault();
                    shootHook();
                }
            });
            resetBtn.addEventListener('click', () => {
                if(gameEnabled) resetGame();
            });
            resetBtn.addEventListener('touchstart', (e) => {
                e.preventDefault();
                if(gameEnabled) resetGame();
            });
        }
        
        // 关注按钮事件
        if(unlockButton) {
            unlockButton.addEventListener('click', (e) => {
                e.preventDefault();
                unlockGameAndStart();
            });
            unlockButton.addEventListener('touchstart', (e) => {
                e.preventDefault();
                unlockGameAndStart();
            });
        }
        
        // 初始化不需要重置游戏，等解锁后才会有小龙虾。但是为了让canvas初始显示不空，设一个空数组，渲染正常
        crayfishArray = [];   // 开始时没有小龙虾（没解锁）
        score = 0;
        updateScoreUI();
        bindGameEvents();
        
        // 动画循环一直跑，但updateGame只会生效在gameEnabled==true时
        function gameLoop() {
            updateGame();    // 内部根据gameEnabled决定是否更新物理
            render();
            requestAnimationFrame(gameLoop);
        }
        gameLoop();
    })();
</script>
</body>
</html>
```
