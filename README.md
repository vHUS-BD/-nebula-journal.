<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nebula Journal - يوميات السديم</title>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@300;400;700;900&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Tajawal', sans-serif;
            background: #0a0a1a;
            color: #e0e0ff;
            overflow-x: hidden;
            min-height: 100vh;
        }

        /* ======== BACKGROUND STARS ======== */
        #canvas-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            pointer-events: none;
        }

        /* ======== GLASSMORPHISM CONTAINER ======== */
        .glass {
            background: rgba(20, 20, 60, 0.25);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 24px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3), 
                        inset 0 1px 0 rgba(255, 255, 255, 0.1);
        }

        /* ======== MAIN CONTAINER ======== */
        .container {
            position: relative;
            z-index: 1;
            max-width: 500px;
            margin: 0 auto;
            padding: 20px;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        /* ======== HEADER ======== */
        .header {
            text-align: center;
            padding: 30px 20px;
            margin-top: 10px;
        }

        .logo {
            font-size: 2.5rem;
            font-weight: 900;
            background: linear-gradient(135deg, #a78bfa, #c084fc, #e879f9);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            text-shadow: 0 0 30px rgba(167, 139, 250, 0.3);
            margin-bottom: 5px;
            animation: glow 3s ease-in-out infinite alternate;
        }

        @keyframes glow {
            from { filter: drop-shadow(0 0 10px rgba(167, 139, 250, 0.5)); }
            to { filter: drop-shadow(0 0 25px rgba(232, 121, 249, 0.8)); }
        }

        .subtitle {
            color: #8888bb;
            font-size: 0.95rem;
            font-weight: 300;
        }

        /* ======== COSMIC CLOCK ======== */
        .clock-section {
            padding: 25px;
            text-align: center;
        }

        .clock-time {
            font-size: 3rem;
            font-weight: 700;
            color: #c084fc;
            font-family: 'Courier New', monospace;
            letter-spacing: 3px;
            text-shadow: 0 0 20px rgba(192, 132, 252, 0.4);
        }

        .clock-date {
            color: #8888bb;
            margin-top: 8px;
            font-size: 0.9rem;
        }

        /* ======== MOOD PALETTE ======== */
        .mood-section {
            padding: 20px;
        }

        .section-title {
            font-size: 1.1rem;
            color: #a78bfa;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .mood-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
        }

        .mood-btn {
            width: 100%;
            aspect-ratio: 1;
            border-radius: 16px;
            border: 2px solid transparent;
            cursor: pointer;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;
        }

        .mood-btn::before {
            content: '';
            position: absolute;
            inset: 0;
            background: radial-gradient(circle at center, rgba(255,255,255,0.3) 0%, transparent 70%);
            opacity: 0;
            transition: opacity 0.3s;
        }

        .mood-btn:hover::before {
            opacity: 1;
        }

        .mood-btn:hover {
            transform: scale(1.1) rotate(5deg);
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }

        .mood-btn.active {
            border-color: #fff;
            box-shadow: 0 0 20px currentColor;
            transform: scale(1.05);
        }

        .mood-1 { background: linear-gradient(135deg, #667eea, #764ba2); color: #667eea; }
        .mood-2 { background: linear-gradient(135deg, #f093fb, #f5576c); color: #f5576c; }
        .mood-3 { background: linear-gradient(135deg, #4facfe, #00f2fe); color: #00f2fe; }
        .mood-4 { background: linear-gradient(135deg, #43e97b, #38f9d7); color: #38f9d7; }
        .mood-5 { background: linear-gradient(135deg, #fa709a, #fee140); color: #fee140; }
        .mood-6 { background: linear-gradient(135deg, #a8edea, #fed6e3); color: #a8edea; }
        .mood-7 { background: linear-gradient(135deg, #ff9a9e, #fecfef); color: #fecfef; }
        .mood-8 { background: linear-gradient(135deg, #ffecd2, #fcb69f); color: #fcb69f; }

        /* ======== JOURNAL SECTION ======== */
        .journal-section {
            padding: 25px;
            flex: 1;
            display: flex;
            flex-direction: column;
        }

        .journal-textarea {
            width: 100%;
            min-height: 180px;
            background: rgba(10, 10, 30, 0.5);
            border: 1px solid rgba(167, 139, 250, 0.2);
            border-radius: 16px;
            padding: 15px;
            color: #e0e0ff;
            font-family: 'Tajawal', sans-serif;
            font-size: 1rem;
            resize: vertical;
            outline: none;
            transition: all 0.3s;
        }

        .journal-textarea:focus {
            border-color: rgba(167, 139, 250, 0.6);
            box-shadow: 0 0 20px rgba(167, 139, 250, 0.1);
        }

        .journal-textarea::placeholder {
            color: #5555aa;
        }

        .btn-save {
            margin-top: 15px;
            padding: 14px 30px;
            background: linear-gradient(135deg, #667eea, #764ba2);
            border: none;
            border-radius: 16px;
            color: white;
            font-family: 'Tajawal', sans-serif;
            font-size: 1rem;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
            overflow: hidden;
        }

        .btn-save::after {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255,255,255,0.2), transparent);
            transform: rotate(45deg);
            transition: all 0.5s;
        }

        .btn-save:hover::after {
            left: 100%;
        }

        .btn-save:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 30px rgba(102, 126, 234, 0.4);
        }

        .btn-save:active {
            transform: translateY(0);
        }

        /* ======== COSMIC MESSAGE ======== */
        .message-section {
            padding: 25px;
            text-align: center;
        }

        .cosmic-message {
            font-size: 1.1rem;
            color: #c084fc;
            font-style: italic;
            line-height: 1.8;
            min-height: 60px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .message-btn {
            margin-top: 15px;
            padding: 10px 25px;
            background: transparent;
            border: 1px solid rgba(192, 132, 252, 0.3);
            border-radius: 20px;
            color: #c084fc;
            font-family: 'Tajawal', sans-serif;
            cursor: pointer;
            transition: all 0.3s;
            font-size: 0.9rem;
        }

        .message-btn:hover {
            background: rgba(192, 132, 252, 0.1);
            border-color: rgba(192, 132, 252, 0.6);
        }

        /* ======== SAVED ENTRIES ======== */
        .entries-section {
            padding: 20px;
            max-height: 300px;
            overflow-y: auto;
        }

        .entries-section::-webkit-scrollbar {
            width: 6px;
        }

        .entries-section::-webkit-scrollbar-track {
            background: rgba(255,255,255,0.05);
            border-radius: 3px;
        }

        .entries-section::-webkit-scrollbar-thumb {
            background: rgba(167, 139, 250, 0.3);
            border-radius: 3px;
        }

        .entry-item {
            background: rgba(10, 10, 30, 0.4);
            border-radius: 12px;
            padding: 15px;
            margin-bottom: 10px;
            border-right: 3px solid;
            transition: all 0.3s;
            animation: slideIn 0.5s ease-out;
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateX(20px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        .entry-item:hover {
            background: rgba(10, 10, 30, 0.6);
            transform: translateX(-5px);
        }

        .entry-date {
            font-size: 0.75rem;
            color: #8888bb;
            margin-bottom: 5px;
        }

        .entry-text {
            font-size: 0.95rem;
            color: #d0d0ff;
            line-height: 1.6;
        }

        .entry-mood {
            display: inline-block;
            width: 8px;
            height: 8px;
            border-radius: 50%;
            margin-right: 5px;
        }

        .empty-state {
            text-align: center;
            color: #5555aa;
            padding: 30px;
            font-size: 0.9rem;
        }

        /* ======== FLOATING PARTICLES ======== */
        .particle {
            position: fixed;
            pointer-events: none;
            border-radius: 50%;
            animation: float linear infinite;
            z-index: 2;
        }

        @keyframes float {
            0% {
                transform: translateY(100vh) rotate(0deg);
                opacity: 0;
            }
            10% {
                opacity: 1;
            }
            90% {
                opacity: 1;
            }
            100% {
                transform: translateY(-100px) rotate(720deg);
                opacity: 0;
            }
        }

        /* ======== SAVE ANIMATION ======== */
        .save-particles {
            position: fixed;
            pointer-events: none;
            z-index: 100;
        }

        .save-particle {
            position: absolute;
            width: 6px;
            height: 6px;
            border-radius: 50%;
            animation: explode 1s ease-out forwards;
        }

        @keyframes explode {
            0% {
                transform: translate(0, 0) scale(1);
                opacity: 1;
            }
            100% {
                transform: translate(var(--tx), var(--ty)) scale(0);
                opacity: 0;
            }
        }

        /* ======== RESPONSIVE ======== */
        @media (max-width: 480px) {
            .logo { font-size: 2rem; }
            .clock-time { font-size: 2.2rem; }
            .container { padding: 15px; }
        }

        /* ======== LOADING ANIMATION ======== */
        .loading-screen {
            position: fixed;
            inset: 0;
            background: #0a0a1a;
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 1000;
            transition: opacity 0.5s;
        }

        .loading-star {
            width: 60px;
            height: 60px;
            border: 3px solid transparent;
            border-top-color: #c084fc;
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        .loading-screen.hidden {
            opacity: 0;
            pointer-events: none;
        }
    </style>
</head>
<body>

    <!-- Loading Screen -->
    <div class="loading-screen" id="loading">
        <div class="loading-star"></div>
    </div>

    <!-- Background Canvas -->
    <canvas id="canvas-bg"></canvas>

    <!-- Main Container -->
    <div class="container">

        <!-- Header -->
        <div class="glass header">
            <div class="logo">✨ Nebula Journal</div>
            <div class="subtitle">يوميات السديم - مساحتك الخاصة في الكون</div>
        </div>

        <!-- Clock -->
        <div class="glass clock-section">
            <div class="clock-time" id="clock">00:00:00</div>
            <div class="clock-date" id="date">...</div>
        </div>

        <!-- Mood Selector -->
        <div class="glass mood-section">
            <div class="section-title">🎨 اختار مزاج الكون</div>
            <div class="mood-grid">
                <button class="mood-btn mood-1" data-color="#667eea" data-name="هادئ" onclick="selectMood(this)"></button>
                <button class="mood-btn mood-2" data-color="#f5576c" data-name="عاطفي" onclick="selectMood(this)"></button>
                <button class="mood-btn mood-3" data-color="#00f2fe" data-name="منفتح" onclick="selectMood(this)"></button>
                <button class="mood-btn mood-4" data-color="#38f9d7" data-name="منعش" onclick="selectMood(this)"></button>
                <button class="mood-btn mood-5" data-color="#fee140" data-name="دافئ" onclick="selectMood(this)"></button>
                <button class="mood-btn mood-6" data-color="#a8edea" data-name="حالم" onclick="selectMood(this)"></button>
                <button class="mood-btn mood-7" data-color="#fecfef" data-name="رومانسي" onclick="selectMood(this)"></button>
                <button class="mood-btn mood-8" data-color="#fcb69f" data-name="مشرق" onclick="selectMood(this)"></button>
            </div>
        </div>

        <!-- Journal -->
        <div class="glass journal-section">
            <div class="section-title">📝 اكتب للكون</div>
            <textarea class="journal-textarea" id="journalInput" placeholder="اكتب أفكارك، أحلامك، أو أي شي يجول في خاطرك... الكون يسمعك ✨"></textarea>
            <button class="btn-save" onclick="saveEntry()">
                💫 حفظ في السديم
            </button>
        </div>

        <!-- Cosmic Message -->
        <div class="glass message-section">
            <div class="section-title">🔮 رسالة من الكون</div>
            <div class="cosmic-message" id="cosmicMessage">
                اضغط الزر لاستقبال رسالة من أعماق الكون...
            </div>
            <button class="message-btn" onclick="newMessage()">
                ✨ رسالة جديدة
            </button>
        </div>

        <!-- Saved Entries -->
        <div class="glass entries-section" id="entriesSection">
            <div class="section-title">📚 ذكرياتك في السديم</div>
            <div id="entriesList">
                <div class="empty-state">
                    لا توجد ذكريات بعد... ابدأ بكتابة أول رسالة للكون! 🌌
                </div>
            </div>
        </div>

    </div>

    <script>
        // ======== COSMIC MESSAGES ========
        const messages = [
            "الكون لا يرسل لك ما لا تستطيع تحمله. أنت أقوى مما تتخيل.",
            "كل نجمة في السماء كانت يوماً ما مجرد غبار. أنت في طريقك لتصبح نجمة.",
            "الظلام ليس غياب النور، بل فرصة للنجوم أن تتألق.",
            "اكتب أحلامك بمداد من نور، فالكون يقرأ ما تكتب.",
            "في كل لحظة صمت، الكون يهمس لك بأسراره.",
            "أنت لست قطرة في المحيط، أنت المحيط بأكمله في قطرة.",
            "الوقت في الكون ليس خطياً، كل لحظة هي بداية جديدة.",
            "ما تراه كفشل، الكون يراه كدرس في طريق النور.",
            "تنفس عميقاً... أنت الآن متصل بكل ذرة في الكون.",
            "النجوم لا تتسابق، كل واحدة تتوهج في وقتها المحدد. كن نجمتك.",
            "أحلامك ليست أوهام، هي خريطة الكون لحياتك.",
            "في لحظة اليأس، تذكر: النجوم تولد من انفجارات.",
            "أنت مصنوع من نفس مادة النجوم. توهج!",
            "الكون يوسع نطاقه باستمرار... وكذلك إمكانياتك.",
            "لا شيء يضيع في الكون، كل طاقة تتحول. حول طاقتك للإيجابية.",
            "الصمت بين النجوم ليس فارغاً، بل مليء بالإمكانيات.",
            "كل يوم جديد هو فرصة لإعادة كتابة قصتك مع الكون.",
            "النجوم البعيدة ترسل لك نورها منذ آلاف السنين. أنت الآن ترى الماضي.",
            "في عالم متسع كهذا، مشاكلك صغيرة... وإمكانياتك كبيرة.",
            "الكون يغني لمن يسمع. استمع لهمساته في صمتك."
        ];

        // ======== STATE ========
        let selectedMood = null;
        let selectedMoodColor = '#667eea';
        let selectedMoodName = 'هادئ';

        // ======== CANVAS STARS ========
        const canvas = document.getElementById('canvas-bg');
        const ctx = canvas.getContext('2d');
        let stars = [];
        let mouseX = 0;
        let mouseY = 0;

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }

        class Star {
            constructor() {
                this.reset();
            }

            reset() {
                this.x = Math.random() * canvas.width;
                this.y = Math.random() * canvas.height;
                this.size = Math.random() * 2 + 0.5;
                this.speedX = (Math.random() - 0.5) * 0.3;
                this.speedY = (Math.random() - 0.5) * 0.3;
                this.brightness = Math.random();
                this.twinkleSpeed = Math.random() * 0.02 + 0.005;
            }

            update() {
                this.x += this.speedX + (mouseX - canvas.width/2) * 0.0001;
                this.y += this.speedY + (mouseY - canvas.height/2) * 0.0001;

                if (this.x < 0) this.x = canvas.width;
                if (this.x > canvas.width) this.x = 0;
                if (this.y < 0) this.y = canvas.height;
                if (this.y > canvas.height) this.y = 0;

                this.brightness += this.twinkleSpeed;
                if (this.brightness > 1 || this.brightness < 0.3) {
                    this.twinkleSpeed = -this.twinkleSpeed;
                }
            }

            draw() {
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                ctx.fillStyle = `rgba(200, 200, 255, ${this.brightness * 0.8})`;
                ctx.fill();

                // Glow effect
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.size * 3, 0, Math.PI * 2);
                ctx.fillStyle = `rgba(167, 139, 250, ${this.brightness * 0.1})`;
                ctx.fill();
            }
        }

        function initStars() {
            stars = [];
            for (let i = 0; i < 150; i++) {
                stars.push(new Star());
            }
        }

        function animateStars() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Draw nebula gradient
            const gradient = ctx.createRadialGradient(
                canvas.width/2, canvas.height/2, 0,
                canvas.width/2, canvas.height/2, canvas.width
            );
            gradient.addColorStop(0, 'rgba(20, 20, 60, 0.3)');
            gradient.addColorStop(0.5, 'rgba(10, 10, 30, 0.1)');
            gradient.addColorStop(1, 'rgba(5, 5, 20, 0)');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            stars.forEach(star => {
                star.update();
                star.draw();
            });

            requestAnimationFrame(animateStars);
        }

        // ======== MOUSE TRACKING ========
        document.addEventListener('mousemove', (e) => {
            mouseX