<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>畢氏定理互動演示（柔和有對比版）</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { 
            font-family: system-ui, -apple-system, "Microsoft JhengHei", "PingFang TC", sans-serif;
            background-color: #f4f1ea; /* 溫潤燕麥奶背景 */
        }
        input[type=number]::-webkit-inner-spin-button, 
        input[type=number]::-webkit-outer-spin-button { 
            opacity: 1;
        }
        @keyframes popIn {
            0% { transform: scale(0.88); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }
        .modal-pop {
            animation: popIn 0.25s cubic-bezier(0.16, 1, 0.3, 1) forwards;
        }
    </style>
</head>
<body class="text-slate-800 min-h-screen flex flex-col items-center justify-center p-4 md:p-6">

    <div class="max-w-5xl w-full bg-[#fffdf9] rounded-3xl shadow-[0_14px_40px_rgba(30,41,59,0.12)] border-[3px] border-slate-800 p-6 md:p-8 relative">
        <!-- 標題區 -->
        <header class="mb-6 text-center">
            <div class="inline-block bg-slate-800 text-amber-200 text-xs font-bold tracking-wider px-3.5 py-1 rounded-full mb-2">
                ✨ 國中數學幾何互動教具
            </div>
            <h1 class="text-2xl md:text-3xl font-black text-slate-800 tracking-wide mb-2">
                畢氏定理 <span class="text-sky-600">a²</span> + <span class="text-rose-500">b²</span> = <span class="text-emerald-600">c²</span> 面積轉化演示
            </h1>
            <p class="text-slate-600 font-medium text-sm md:text-base">
                可自由輸入三邊長測試！若<strong class="text-rose-500 underline decoration-2">不符合畢氏定理</strong>，方格將無法完美填滿並跳出提示喔！
            </p>
        </header>

        <!-- 模式切換列 -->
        <div class="flex flex-wrap justify-center gap-3 mb-4">
            <button id="modeFreeBtn" onclick="setMode('free')" class="px-4 py-1.5 rounded-xl text-xs font-bold border-2 border-slate-800 bg-amber-300 text-slate-900 shadow-[2px_2px_0px_#1e293b] transition">
                🧪 自由輸入三邊長（可測試非直角）
            </button>
            <button id="modeAutoBtn" onclick="setMode('auto')" class="px-4 py-1.5 rounded-xl text-xs font-bold border-2 border-slate-800 bg-stone-100 text-slate-700 hover:bg-stone-200 transition">
                ✨ 輸入任兩邊自動算第三邊
            </button>
        </div>

        <!-- 控制面板（柔和飽和色卡 + 深炭灰對比框） -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4 bg-stone-100/90 p-4 rounded-2xl border-2 border-stone-300 mb-5">
            <!-- 邊 a：晴空天藍 -->
            <div id="cardA" class="bg-sky-200 text-slate-900 p-3.5 rounded-2xl border-2 border-slate-800 shadow-[3px_3px_0px_#1e293b] transition-all">
                <div class="flex justify-between items-center mb-1.5">
                    <label class="text-sm font-black text-sky-950">📏 邊長 a</label>
                    <span id="badgeA" class="text-[11px] font-bold px-2.5 py-0.5 rounded-full bg-white text-sky-800 border border-slate-800">✏️ 自由輸入</span>
                </div>
                <input type="number" id="inputA" value="3" min="0.5" max="15" step="0.5" 
                    class="w-full px-3 py-2 text-lg font-black text-slate-900 bg-white border-2 border-slate-800 rounded-xl focus:ring-2 focus:ring-sky-500 focus:outline-none">
            </div>

            <!-- 邊 b：珊瑚蜜桃粉 -->
            <div id="cardB" class="bg-rose-200 text-slate-900 p-3.5 rounded-2xl border-2 border-slate-800 shadow-[3px_3px_0px_#1e293b] transition-all">
                <div class="flex justify-between items-center mb-1.5">
                    <label class="text-sm font-black text-rose-950">📏 邊長 b</label>
                    <span id="badgeB" class="text-[11px] font-bold px-2.5 py-0.5 rounded-full bg-white text-rose-800 border border-slate-800">✏️ 自由輸入</span>
                </div>
                <input type="number" id="inputB" value="4" min="0.5" max="15" step="0.5" 
                    class="w-full px-3 py-2 text-lg font-black text-slate-900 bg-white border-2 border-slate-800 rounded-xl focus:ring-2 focus:ring-rose-500 focus:outline-none">
            </div>

            <!-- 邊 c：鼠尾草薄荷綠 -->
            <div id="cardC" class="bg-emerald-200 text-slate-900 p-3.5 rounded-2xl border-2 border-slate-800 shadow-[3px_3px_0px_#1e293b] transition-all">
                <div class="flex justify-between items-center mb-1.5">
                    <label class="text-sm font-black text-emerald-950">📐 第三邊 c（目標正方形）</label>
                    <span id="badgeC" class="text-[11px] font-bold px-2.5 py-0.5 rounded-full bg-white text-emerald-800 border border-slate-800">✏️ 自由輸入</span>
                </div>
                <input type="number" id="inputC" value="5" min="0.5" max="25" step="0.5" 
                    class="w-full px-3 py-2 text-lg font-black text-slate-900 bg-white border-2 border-slate-800 rounded-xl focus:ring-2 focus:ring-emerald-500 focus:outline-none">
            </div>
        </div>

        <!-- 錯誤防呆提示區 -->
        <div id="errorBanner" class="hidden mb-4 bg-rose-200 border-2 border-slate-800 text-rose-950 px-4 py-2.5 rounded-xl text-sm font-bold text-center shadow-[3px_3px_0px_#1e293b]">
            ⚠️ 這三個邊長無法圍成一個三角形（兩邊之和必須大於第三邊）！請重新調整。
        </div>

        <!-- 快捷範例與控制按鈕 -->
        <div class="flex flex-wrap gap-3 justify-between items-center mb-5">
            <div class="flex flex-wrap gap-2 items-center">
                <span class="text-xs font-bold text-slate-500">測試範例：</span>
                <button onclick="setThreeSides(3, 4, 5)" class="text-xs bg-emerald-200 hover:bg-emerald-300 text-emerald-950 px-3 py-1.5 rounded-xl font-bold border-2 border-slate-800 transition">✅ 3, 4, 5 (符合)</button>
                <button onclick="setThreeSides(3, 4, 6)" class="text-xs bg-rose-200 hover:bg-rose-300 text-rose-950 px-3 py-1.5 rounded-xl font-bold border-2 border-slate-800 transition">❌ 3, 4, 6 (填不滿)</button>
                <button onclick="setThreeSides(4, 5, 6)" class="text-xs bg-amber-200 hover:bg-amber-300 text-amber-950 px-3 py-1.5 rounded-xl font-bold border-2 border-slate-800 transition">❌ 4, 5, 6 (會溢出)</button>
                <label class="inline-flex items-center cursor-pointer select-none ml-1 bg-stone-200 px-3 py-1.5 rounded-xl border-2 border-slate-800">
                    <input type="checkbox" id="toggleGrid" checked class="w-4 h-4 accent-slate-800 rounded">
                    <span class="ml-1.5 text-xs font-bold text-slate-800">1×1 單位格子</span>
                </label>
            </div>

            <div class="flex flex-wrap items-center gap-3">
                <!-- 速度微調控制 -->
                <div class="flex items-center gap-2 bg-stone-200 px-3 py-1.5 rounded-xl border-2 border-slate-800">
                    <span class="text-xs font-bold text-slate-800">🐢 速度</span>
                    <input type="range" id="speedSlider" min="0.0015" max="0.01" step="0.0005" value="0.0035" class="w-16 accent-slate-800 cursor-pointer">
                </div>

                <button id="btnAnimate" class="bg-amber-300 hover:bg-amber-400 text-slate-900 font-black px-6 py-2 rounded-xl border-2 border-slate-800 shadow-[3px_3px_0px_#1e293b] transition transform active:translate-y-0.5 flex items-center gap-1.5">
                    ▶ 開始面積轉化動畫
                </button>
                <button id="btnReset" class="bg-stone-200 hover:bg-stone-300 text-slate-800 font-bold px-4 py-2 rounded-xl border-2 border-slate-800 transition">
                    重設
                </button>
            </div>
        </div>

        <!-- 柔和對比畫布區域 -->
        <div class="relative w-full aspect-[4/3] md:aspect-[16/10] bg-[#faf8f5] rounded-2xl overflow-hidden border-[3px] border-slate-800 shadow-inner flex items-center justify-center">
            <canvas id="canvas" class="w-full h-full"></canvas>
            <div id="infoOverlay" class="absolute bottom-4 left-4 right-4 md:right-auto bg-slate-800/95 border-2 border-amber-200 text-white px-4 py-2.5 rounded-xl text-sm font-bold shadow-md">
                目前面積：<span class="text-sky-300 font-black">a² = 9</span> ＋ <span class="text-rose-300 font-black">b² = 16</span> ＝ <span class="text-emerald-300 font-black">c² = 25</span>
            </div>
        </div>
    </div>

    <!-- 「再想想」互動提示對話框 (Modal) -->
    <div id="thinkModal" class="hidden fixed inset-0 z-50 bg-slate-900/50 backdrop-blur-sm flex items-center justify-center p-4">
        <div class="modal-pop bg-[#fffdf9] max-w-md w-full rounded-3xl border-[3px] border-slate-800 shadow-[6px_6px_0px_#fb7185] p-6 text-center">
            <div class="w-16 h-16 bg-rose-200 border-2 border-slate-800 text-rose-900 rounded-full flex items-center justify-center text-3xl mx-auto mb-3 font-black">
                🤔
            </div>
            <h2 class="text-2xl font-black text-slate-800 mb-2">再想想！</h2>
            <p id="modalReason" class="text-slate-700 font-bold text-base mb-4 leading-relaxed">
                這三邊長不符合畢氏定理，所以無法剛好填滿正方形喔！
            </p>
            <div id="modalMathDetail" class="bg-stone-100 border-2 border-slate-800 rounded-xl p-3 mb-5 text-sm font-black text-slate-800">
                a² + b² ≠ c²
            </div>
            <button onclick="closeThinkModal()" class="w-full bg-amber-300 hover:bg-amber-400 text-slate-900 font-black py-3 rounded-xl border-2 border-slate-800 shadow-[3px_3px_0px_#1e293b] transition transform active:translate-y-0.5 text-base">
                我知道了，再試試看！
            </button>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        
        const inputs = {
            a: document.getElementById('inputA'),
            b: document.getElementById('inputB'),
            c: document.getElementById('inputC')
        };
        const badges = {
            a: document.getElementById('badgeA'),
            b: document.getElementById('badgeB'),
            c: document.getElementById('badgeC')
        };
        const toggleGrid = document.getElementById('toggleGrid');
        const speedSlider = document.getElementById('speedSlider');
        const btnAnimate = document.getElementById('btnAnimate');
        const btnReset = document.getElementById('btnReset');
        const infoOverlay = document.getElementById('infoOverlay');
        const errorBanner = document.getElementById('errorBanner');
        const thinkModal = document.getElementById('thinkModal');
        const modalReason = document.getElementById('modalReason');
        const modalMathDetail = document.getElementById('modalMathDetail');

        let animationId = null;
        let progress = 0;
        let inputMode = 'free'; 
        let activePair = ['a', 'b']; 
        let currentSides = { a: 3, b: 4, c: 5 };

        function resizeCanvas() {
            const rect = canvas.parentElement.getBoundingClientRect();
            canvas.width = rect.width * window.devicePixelRatio;
            canvas.height = rect.height * window.devicePixelRatio;
            ctx.scale(window.devicePixelRatio, window.devicePixelRatio);
            drawScene(progress);
        }

        window.addEventListener('resize', resizeCanvas);

        function setMode(mode) {
            inputMode = mode;
            const freeBtn = document.getElementById('modeFreeBtn');
            const autoBtn = document.getElementById('modeAutoBtn');

            if (mode === 'free') {
                freeBtn.className = 'px-4 py-1.5 rounded-xl text-xs font-bold border-2 border-slate-800 bg-amber-300 text-slate-900 shadow-[2px_2px_0px_#1e293b] transition';
                autoBtn.className = 'px-4 py-1.5 rounded-xl text-xs font-bold border-2 border-slate-800 bg-stone-100 text-slate-700 hover:bg-stone-200 transition';
                ['a', 'b', 'c'].forEach(k => {
                    badges[k].textContent = '✏️ 自由輸入';
                    badges[k].className = 'text-[11px] font-bold px-2.5 py-0.5 rounded-full bg-white text-slate-800 border border-slate-800';
                });
            } else {
                autoBtn.className = 'px-4 py-1.5 rounded-xl text-xs font-bold border-2 border-slate-800 bg-amber-300 text-slate-900 shadow-[2px_2px_0px_#1e293b] transition';
                freeBtn.className = 'px-4 py-1.5 rounded-xl text-xs font-bold border-2 border-slate-800 bg-stone-100 text-slate-700 hover:bg-stone-200 transition';
                updateAutoBadges();
                calculateThirdSideAuto();
            }
        }

        function updateAutoBadges() {
            const allKeys = ['a', 'b', 'c'];
            const autoKey = allKeys.find(k => !activePair.includes(k));
            allKeys.forEach(k => {
                if (k === autoKey) {
                    badges[k].textContent = '✨ 自動計算';
                    badges[k].className = 'text-[11px] font-bold px-2.5 py-0.5 rounded-full bg-amber-300 text-slate-900 border border-slate-800';
                } else {
                    badges[k].textContent = '✏️ 已知邊';
                    badges[k].className = 'text-[11px] font-bold px-2.5 py-0.5 rounded-full bg-white text-slate-800 border border-slate-800';
                }
            });
        }

        function handleInput(changedKey) {
            if (animationId) cancelAnimationFrame(animationId);
            progress = 0;

            if (inputMode === 'auto') {
                if (!activePair.includes(changedKey)) {
                    activePair.shift();
                    activePair.push(changedKey);
                } else {
                    activePair = activePair.filter(k => k !== changedKey);
                    activePair.push(changedKey);
                }
                updateAutoBadges();
                calculateThirdSideAuto();
            } else {
                validateFreeThreeSides();
            }
        }

        function calculateThirdSideAuto() {
            const allKeys = ['a', 'b', 'c'];
            const autoKey = allKeys.find(k => !activePair.includes(k));

            let a = parseFloat(inputs.a.value);
            let b = parseFloat(inputs.b.value);
            let c = parseFloat(inputs.c.value);
            let isValid = true;

            if (autoKey === 'c') {
                if (a > 0 && b > 0) {
                    c = Math.sqrt(a * a + b * b);
                    inputs.c.value = Number.isInteger(c) ? c : +c.toFixed(2);
                } else isValid = false;
            } else if (autoKey === 'b') {
                if (a > 0 && c > a) {
                    b = Math.sqrt(c * c - a * a);
                    inputs.b.value = Number.isInteger(b) ? b : +b.toFixed(2);
                } else isValid = false;
            } else if (autoKey === 'a') {
                if (b > 0 && c > b) {
                    a = Math.sqrt(c * c - b * b);
                    inputs.a.value = Number.isInteger(a) ? a : +a.toFixed(2);
                } else isValid = false;
            }

            if (!isValid) {
                errorBanner.textContent = '⚠️ 直角三角形中，「斜邊 c」必須大於「股」喔！請調整數值。';
                errorBanner.classList.remove('hidden');
                return;
            }
            errorBanner.classList.add('hidden');
            currentSides = { a, b, c };
            updateInfoText(0);
            drawScene(0);
        }

        function validateFreeThreeSides() {
            let a = parseFloat(inputs.a.value);
            let b = parseFloat(inputs.b.value);
            let c = parseFloat(inputs.c.value);

            if (!(a > 0 && b > 0 && c > 0 && a + b > c && a + c > b && b + c > a)) {
                errorBanner.textContent = '⚠️ 這三個長度無法圍成三角形（任兩邊之和必須大於第三邊）！';
                errorBanner.classList.remove('hidden');
                return false;
            }

            errorBanner.classList.add('hidden');
            currentSides = { a, b, c };
            updateInfoText(0);
            drawScene(0);
            return true;
        }

        function setThreeSides(a, b, c) {
            if (animationId) cancelAnimationFrame(animationId);
            progress = 0;
            setMode('free');
            inputs.a.value = a;
            inputs.b.value = b;
            inputs.c.value = c;
            validateFreeThreeSides();
        }

        inputs.a.addEventListener('input', () => handleInput('a'));
        inputs.b.addEventListener('input', () => handleInput('b'));
        inputs.c.addEventListener('input', () => handleInput('c'));
        toggleGrid.addEventListener('change', () => drawScene(progress));

        function formatNum(n) {
            return Number.isInteger(n) ? n : +n.toFixed(2);
        }

        function checkPythagoreanStatus() {
            const { a, b, c } = currentSides;
            const sumSquares = a * a + b * b;
            const cSquare = c * c;
            const diff = sumSquares - cSquare;

            if (Math.abs(diff) <= 0.05) {
                return 'match';
            } else if (diff < 0) {
                return 'underfill';
            } else {
                return 'overflow';
            }
        }

        function showThinkModal(status) {
            const { a, b, c } = currentSides;
            const areaA = formatNum(a * a);
            const areaB = formatNum(b * b);
            const sumAB = formatNum(a * a + b * b);
            const areaC = formatNum(c * c);

            if (status === 'underfill') {
                modalReason.innerHTML = `這組三邊長<span class="text-rose-600">不符合畢氏定理</span>！<br>兩個小正方形的面積總和太小，目標正方形<span class="text-rose-600">無法填滿（還有空隙）</span>！`;
                modalMathDetail.innerHTML = `a² + b² = ${areaA} + ${areaB} = <span class="text-sky-600">${sumAB}</span> ＜ c² = <span class="text-emerald-600">${areaC}</span>`;
            } else if (status === 'overflow') {
                modalReason.innerHTML = `這組三邊長<span class="text-rose-600">不符合畢氏定理</span>！<br>兩個小正方形的面積總和太大，方格<span class="text-rose-600">滿出來、超出框線</span>了！`;
                modalMathDetail.innerHTML = `a² + b² = ${areaA} + ${areaB} = <span class="text-sky-600">${sumAB}</span> ＞ c² = <span class="text-emerald-600">${areaC}</span>`;
            }
            thinkModal.classList.remove('hidden');
        }

        function closeThinkModal() {
            thinkModal.classList.add('hidden');
        }

        function updateInfoText(p) {
            const { a, b, c } = currentSides;
            const areaA = formatNum(a * a);
            const areaB = formatNum(b * b);
            const sumAB = formatNum(a * a + b * b);
            const areaC = formatNum(c * c);
            const percent = Math.round(p * 100);
            const status = checkPythagoreanStatus();

            if (p === 0) {
                const sign = status === 'match' ? '＝' : (status === 'underfill' ? '＜' : '＞');
                infoOverlay.innerHTML = `目前面積：<span class="text-sky-300 font-black">a² = ${areaA}</span> ＋ <span class="text-rose-300 font-black">b² = ${areaB}</span> (${sumAB}) ${sign} <span class="text-emerald-300 font-black">c² = ${areaC}</span>`;
            } else if (p < 1) {
                infoOverlay.innerHTML = `✨ 面積轉化中 (${percent}%)：<span class="text-sky-300 font-black">晴空藍 (${areaA})</span> 與 <span class="text-rose-300 font-black">珊瑚粉 (${areaB})</span> 正移入目標正方形...`;
            } else {
                if (status === 'match') {
                    infoOverlay.innerHTML = `✅ 符合畢氏定理！<span class="text-sky-300 font-black">${areaA}</span> ＋ <span class="text-rose-300 font-black">${areaB}</span> ＝ 完美填滿 <span class="text-emerald-300 font-black">c² (${areaC})</span>！`;
                } else if (status === 'underfill') {
                    infoOverlay.innerHTML = `❌ 不符合畢氏定理！${areaA} ＋ ${areaB} ＝ ${sumAB} ＜ ${areaC}（無法填滿，留有空隙）`;
                } else {
                    infoOverlay.innerHTML = `❌ 不符合畢氏定理！${areaA} ＋ ${areaB} ＝ ${sumAB} ＞ ${areaC}（面積過大，超出框線）`;
                }
            }
        }

        function drawGridSquare(originX, originY, vecU, vecV, sideLength, fillColor, strokeColor, gridColor, showGrid) {
            ctx.save();
            ctx.beginPath();
            ctx.moveTo(originX, originY);
            ctx.lineTo(originX + vecU.x * sideLength, originY + vecU.y * sideLength);
            ctx.lineTo(originX + (vecU.x + vecV.x) * sideLength, originY + (vecU.y + vecV.y) * sideLength);
            ctx.lineTo(originX + vecV.x * sideLength, originY + vecV.y * sideLength);
            ctx.closePath();

            ctx.fillStyle = fillColor;
            ctx.fill();
            ctx.lineWidth = 3;
            ctx.strokeStyle = strokeColor;
            ctx.stroke();

            if (showGrid) {
                ctx.clip();
                ctx.beginPath();
                ctx.lineWidth = 1.4;
                ctx.strokeStyle = gridColor;

                const steps = Math.ceil(sideLength);
                for (let i = 1; i < steps; i++) {
                    ctx.moveTo(originX + vecU.x * i, originY + vecU.y * i);
                    ctx.lineTo(originX + vecU.x * i + vecV.x * sideLength, originY + vecU.y * i + vecV.y * sideLength);
                    ctx.moveTo(originX + vecV.x * i, originY + vecV.y * i);
                    ctx.lineTo(originX + vecV.x * i + vecU.x * sideLength, originY + vecV.y * i + vecU.y * sideLength);
                }
                ctx.stroke();
            }
            ctx.restore();
        }

        function easeInOutSine(t) {
            return -(Math.cos(Math.PI * t) - 1) / 2;
        }

        function drawScene(rawProgress) {
            const width = canvas.clientWidth;
            const height = canvas.clientHeight;
            ctx.clearRect(0, 0, width, height);

            // 柔和暖灰方格背景線
            ctx.strokeStyle = '#ece8df';
            ctx.lineWidth = 1;
            for (let gx = 0; gx < width; gx += 40) {
                ctx.beginPath();
                ctx.moveTo(gx, 0);
                ctx.lineTo(gx, height);
                ctx.stroke();
            }
            for (let gy = 0; gy < height; gy += 40) {
                ctx.beginPath();
                ctx.moveTo(0, gy);
                ctx.lineTo(width, gy);
                ctx.stroke();
            }

            const { a, b, c } = currentSides;
            const showGrid = toggleGrid.checked;
            const p = easeInOutSine(rawProgress);

            let cosA = (a * a + b * b - c * c) / (2 * a * b);
            cosA = Math.max(-1, Math.min(1, cosA));
            const sinA = Math.sqrt(Math.max(0, 1 - cosA * cosA));

            const totalSpan = a + b + c * 0.9;
            const scale = Math.min(width, height) / (totalSpan * 1.52);

            const pA = {
                x: width * 0.42 - (a - b) * scale * 0.12,
                y: height * 0.58 + (a - b) * scale * 0.12
            };
            const pB = { x: pA.x + a * scale, y: pA.y };
            const pC = {
                x: pA.x + b * cosA * scale,
                y: pA.y - b * sinA * scale
            };

            const unitRight = { x: scale, y: 0 };
            const unitDown  = { x: 0, y: scale };

            const acDx = (pC.x - pA.x) / b;
            const acDy = (pC.y - pA.y) / b;
            const unitAC    = { x: acDx, y: acDy };
            const unitACOut = { x: acDy, y: -acDx };

            const bcDx = (pC.x - pB.x) / c;
            const bcDy = (pC.y - pB.y) / c;
            const unitHypAlong = { x: bcDx, y: bcDy };
            const unitHypOut   = { x: -bcDy, y: bcDx };

            // 1. 底層柔和飽和色塊正方形（搭配深炭灰 #1e293b 邊框形成高對比）
            // a² 晴空天藍 (#38bdf8)
            drawGridSquare(
                pA.x, pA.y, unitRight, unitDown, a, 
                `rgba(56, 189, 248, ${0.82 - p * 0.58})`, 
                '#1e293b', 
                'rgba(255, 255, 255, 0.85)', 
                showGrid
            );
            
            // b² 珊瑚蜜桃粉 (#fb7185)
            drawGridSquare(
                pA.x, pA.y, unitAC, unitACOut, b, 
                `rgba(251, 113, 133, ${0.82 - p * 0.58})`, 
                '#1e293b', 
                'rgba(255, 255, 255, 0.85)', 
                showGrid
            );

            // c² 鼠尾草薄荷綠底框 (#34d399)
            drawGridSquare(
                pB.x, pB.y, unitHypAlong, unitHypOut, c, 
                'rgba(52, 211, 153, 0.28)', 
                '#065f46', 
                'rgba(6, 95, 70, 0.35)', 
                showGrid
            );

            // 2. 飛行動畫柔和飽和色塊小方格
            if (rawProgress > 0) {
                const colsA = Math.ceil(a);
                const colsB = Math.ceil(b);
                let targetIndex = 0;
                const tiles = [];

                for (let r = 0; r < colsA; r++) {
                    for (let col = 0; col < colsA; col++) {
                        const w = Math.min(1, a - col);
                        const h = Math.min(1, a - r);
                        if (w <= 0 || h <= 0) continue;
                        tiles.push({
                            startX: pA.x + col * scale,
                            startY: pA.y + r * scale,
                            uStart: unitRight,
                            vStart: unitDown,
                            w, h,
                            color: '#38bdf8', // 晴空天藍
                            border: '#ffffff',
                            idx: targetIndex++
                        });
                    }
                }

                for (let r = 0; r < colsB; r++) {
                    for (let col = 0; col < colsB; col++) {
                        const w = Math.min(1, b - col);
                        const h = Math.min(1, b - r);
                        if (w <= 0 || h <= 0) continue;
                        tiles.push({
                            startX: pA.x + unitAC.x * col + unitACOut.x * r,
                            startY: pA.y + unitAC.y * col + unitACOut.y * r,
                            uStart: unitAC,
                            vStart: unitACOut,
                            w, h,
                            color: '#fb7185', // 珊瑚蜜桃粉
                            border: '#ffffff',
                            idx: targetIndex++
                        });
                    }
                }

                const gridColsC = Math.ceil(c);

                tiles.forEach((tile) => {
                    const targetRow = Math.floor(tile.idx / gridColsC);
                    const targetCol = tile.idx % gridColsC;

                    const endX = pB.x + unitHypAlong.x * targetCol + unitHypOut.x * targetRow;
                    const endY = pB.y + unitHypAlong.y * targetCol + unitHypOut.y * targetRow;

                    const curX = tile.startX + (endX - tile.startX) * p;
                    const curY = tile.startY + (endY - tile.startY) * p;

                    const curUx = tile.uStart.x + (unitHypAlong.x - tile.uStart.x) * p;
                    const curUy = tile.uStart.y + (unitHypAlong.y - tile.uStart.y) * p;
                    const curVx = tile.vStart.x + (unitHypOut.x - tile.vStart.x) * p;
                    const curVy = tile.vStart.y + (unitHypOut.y - tile.vStart.y) * p;

                    ctx.beginPath();
                    ctx.moveTo(curX, curY);
                    ctx.lineTo(curX + curUx * tile.w, curY + curUy * tile.w);
                    ctx.lineTo(curX + curUx * tile.w + curVx * tile.h, curY + curUy * tile.w + curVy * tile.h);
                    ctx.lineTo(curX + curVx * tile.h, curY + curVy * tile.h);
                    ctx.closePath();

                    ctx.fillStyle = tile.color;
                    ctx.fill();
                    if (showGrid) {
                        ctx.lineWidth = 1.4;
                        ctx.strokeStyle = tile.border;
                        ctx.stroke();
                    }
                });

                // 若動畫完成且「不符合畢氏定理」，加上柔和珊瑚紅警示虛線框
                if (rawProgress === 1 && checkPythagoreanStatus() !== 'match') {
                    ctx.save();
                    ctx.beginPath();
                    ctx.moveTo(pB.x, pB.y);
                    ctx.lineTo(pB.x + unitHypAlong.x * c, pB.y + unitHypAlong.y * c);
                    ctx.lineTo(pB.x + (unitHypAlong.x + unitHypOut.x) * c, pB.y + (unitHypAlong.y + unitHypOut.y) * c);
                    ctx.lineTo(pB.x + unitHypOut.x * c, pB.y + unitHypOut.y * c);
                    ctx.closePath();
                    ctx.lineWidth = 3.5;
                    ctx.setLineDash([8, 6]);
                    ctx.strokeStyle = '#e11d48';
                    ctx.stroke();
                    ctx.restore();
                }
            }

            // 3. 中央三角形（溫暖鵝黃 #fde047 + 深炭灰粗外框）
            ctx.beginPath();
            ctx.moveTo(pA.x, pA.y);
            ctx.lineTo(pB.x, pB.y);
            ctx.lineTo(pC.x, pC.y);
            ctx.closePath();
            ctx.fillStyle = '#fde047';
            ctx.fill();
            ctx.lineWidth = 3.5;
            ctx.strokeStyle = '#1e293b';
            ctx.stroke();

            // 符合直角時顯示直角記號
            if (Math.abs(cosA) < 0.01) {
                const markSize = Math.min(15, scale * 0.35);
                ctx.beginPath();
                ctx.moveTo(pA.x + markSize, pA.y);
                ctx.lineTo(pA.x + markSize, pA.y - markSize);
                ctx.lineTo(pA.x, pA.y - markSize);
                ctx.strokeStyle = '#e11d48';
                ctx.lineWidth = 2.8;
                ctx.stroke();
            }

            // 4. 柔和白底 + 深炭灰粗框標籤徽章（高可讀性）
            ctx.font = '800 13px system-ui, sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';

            function drawSoftContrastBadge(text, x, y, bgColor, borderColor, textColor) {
                const paddingX = 11;
                const textWidth = ctx.measureText(text).width;
                ctx.fillStyle = bgColor;
                ctx.strokeStyle = borderColor;
                ctx.lineWidth = 2.2;
                ctx.beginPath();
                ctx.roundRect(x - textWidth/2 - paddingX, y - 14, textWidth + paddingX*2, 28, 14);
                ctx.fill();
                ctx.stroke();
                ctx.fillStyle = textColor;
                ctx.fillText(text, x, y);
            }

            drawSoftContrastBadge(
                `a = ${formatNum(a)} (面積 ${formatNum(a*a)})`, 
                pA.x + (a*scale)/2, pA.y + (a*scale)/2, 
                'rgba(255, 255, 255, 0.94)', '#1e293b', '#0369a1'
            );

            const bCenterX = pA.x + (unitAC.x + unitACOut.x) * (b/2);
            const bCenterY = pA.y + (unitAC.y + unitACOut.y) * (b/2);
            drawSoftContrastBadge(
                `b = ${formatNum(b)} (面積 ${formatNum(b*b)})`, 
                bCenterX, bCenterY, 
                'rgba(255, 255, 255, 0.94)', '#1e293b', '#be123c'
            );

            const cCenterX = (pB.x + pC.x)/2 + unitHypOut.x * (c/2);
            const cCenterY = (pB.y + pC.y)/2 + unitHypOut.y * (c/2);
            drawSoftContrastBadge(
                `c = ${formatNum(c)} (面積 ${formatNum(c*c)})`, 
                cCenterX, cCenterY, 
                'rgba(255, 255, 255, 0.94)', '#1e293b', '#047857'
            );
        }

        btnAnimate.addEventListener('click', () => {
            if (!errorBanner.classList.contains('hidden')) return;
            if (animationId) cancelAnimationFrame(animationId);
            progress = 0;
            
            function step() {
                const stepSpeed = parseFloat(speedSlider.value);
                progress += stepSpeed;
                if (progress >= 1) {
                    progress = 1;
                    updateInfoText(1);
                    drawScene(1);

                    const status = checkPythagoreanStatus();
                    if (status !== 'match') {
                        setTimeout(() => showThinkModal(status), 300);
                    }
                    return;
                }
                updateInfoText(progress);
                drawScene(progress);
                animationId = requestAnimationFrame(step);
            }
            step();
        });

        btnReset.addEventListener('click', () => {
            setThreeSides(3, 4, 5);
        });

        window.onload = () => {
            setMode('free');
            resizeCanvas();
            updateInfoText(0);
        };
    </script>
</body>
</html>
