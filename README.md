<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Ёлка-Конструктор — Интерактивная новогодняя открытка</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Caveat:wght@600;700&family=Cormorant+Garamond:ital,wght@0,600;0,700;1,600&family=Inter:wght@400;500;600;700;800&family=Orbitron:wght@600;800&family=Playfair+Display:ital,wght@0,600;0,700;1,600&display=swap" rel="stylesheet">
    <style>
        :root {
            --font-sans: 'Inter', sans-serif;
        }
        body {
            font-family: var(--font-sans);
            background-color: #0b1120;
            color: #f8fafc;
            min-height: 100vh;
            min-height: 100dvh;
            padding-bottom: env(safe-area-inset-bottom, 0px);
            overflow-x: hidden;
            -webkit-tap-highlight-color: transparent;
        }

        /* Кастомный скроллбар для панели конструктора */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.4);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(148, 163, 184, 0.3);
            border-radius: 9999px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(148, 163, 184, 0.5);
        }

        /* Шрифты для тем открытки */
        .font-theme-nordic { font-family: 'Playfair Display', Georgia, serif; }
        .font-theme-cyber { font-family: 'Orbitron', 'Inter', sans-serif; }
        .font-theme-artdeco { font-family: 'Cormorant Garamond', Georgia, serif; }
        .font-theme-retro { font-family: 'Caveat', cursive; }

        /* Темы фона и оформления открытки */
        .card-theme-nordic {
            background: radial-gradient(circle at 20% 15%, #fef9f0 0%, #f3e8d2 45%, #234236 100%);
            color: #1c2e26;
            box-shadow: 0 25px 60px -15px rgba(20, 45, 35, 0.7), inset 0 0 0 2px rgba(255, 255, 255, 0.65);
        }
        .card-theme-cyber {
            background: radial-gradient(circle at 50% 15%, #2a0e4a 0%, #0d0722 55%, #050211 100%);
            color: #e0f2fe;
            box-shadow: 0 0 45px rgba(168, 85, 247, 0.45), 0 0 18px rgba(34, 211, 238, 0.35), inset 0 0 0 2px rgba(34, 211, 238, 0.65);
        }
        .card-theme-artdeco {
            background: radial-gradient(circle at 50% 20%, #241f18 0%, #110e0b 65%, #070604 100%);
            color: #f6e0a6;
            box-shadow: 0 25px 60px -12px rgba(0, 0, 0, 0.85), inset 0 0 0 2px rgba(212, 175, 55, 0.75);
        }
        .card-theme-retro {
            background: radial-gradient(circle at 50% 25%, #fff5e4 0%, #f5dfb8 65%, #e2bd85 100%);
            color: #5c1916;
            box-shadow: 0 25px 55px -12px rgba(65, 22, 15, 0.65), inset 0 0 0 3px rgba(168, 38, 34, 0.55);
        }

        /* Декоративные рамки внутри тем */
        .artdeco-border {
            border: 1px solid rgba(212, 175, 55, 0.45);
            box-shadow: inset 0 0 0 4px rgba(212, 175, 55, 0.12);
        }
        .retro-border {
            border: 2px dashed rgba(168, 38, 34, 0.45);
        }

        /* Анимации интерактивных стикеров */
        @keyframes treeWobble {
            0%, 100% { transform: rotate(0deg) scale(1); filter: drop-shadow(0 0 0px transparent); }
            20% { transform: rotate(-11deg) scale(1.15); filter: drop-shadow(0 0 16px #fde047); }
            45% { transform: rotate(10deg) scale(1.2); filter: drop-shadow(0 0 22px #facc15); }
            70% { transform: rotate(-6deg) scale(1.1); filter: drop-shadow(0 0 14px #fde047); }
        }
        @keyframes spinFast {
            0% { transform: rotate(0deg) scale(1); }
            50% { transform: rotate(180deg) scale(1.28); filter: drop-shadow(0 0 14px #7dd3fc); }
            100% { transform: rotate(360deg) scale(1); }
        }
        @keyframes giftBounce {
            0%, 100% { transform: translateY(0) scale(1, 1); }
            25% { transform: translateY(5px) scale(1.22, 0.82); }
            55% { transform: translateY(-24px) scale(0.92, 1.15); }
            80% { transform: translateY(-6px) scale(1.05, 0.96); }
        }
        @keyframes clinkGlasses {
            0%, 100% { transform: rotate(0deg) scale(1); }
            25% { transform: rotate(-18deg) translateX(-4px) scale(1.15); }
            50% { transform: rotate(18deg) translateX(4px) scale(1.2); filter: drop-shadow(0 0 15px #fef08a); }
            75% { transform: rotate(-8deg) scale(1.08); }
        }
        @keyframes tangerinePop {
            0% { transform: scale(1); }
            35% { transform: scale(1.38) rotate(12deg); filter: drop-shadow(0 0 18px #fb923c); }
            65% { transform: scale(0.85) rotate(-10deg); }
            100% { transform: scale(1) rotate(0deg); }
        }
        @keyframes snowmanDance {
            0%, 100% { transform: translateY(0) rotate(0deg); }
            30% { transform: translateY(-12px) rotate(-12deg) scale(1.15); }
            65% { transform: translateY(-8px) rotate(12deg) scale(1.15); }
        }
        @keyframes orbPulse {
            0%, 100% { transform: scale(1); filter: drop-shadow(0 0 6px #c084fc); }
            50% { transform: scale(1.25); filter: drop-shadow(0 0 24px #e879f9); }
        }
        @keyframes starShine {
            0%, 100% { transform: scale(1) rotate(0deg); }
            50% { transform: scale(1.32) rotate(22deg); filter: drop-shadow(0 0 22px #fde047); }
        }

        .anim-tree { animation: treeWobble 0.85s ease-in-out; }
        .anim-snowflake { animation: spinFast 0.85s cubic-bezier(0.34, 1.56, 0.64, 1); }
        .anim-gift { animation: giftBounce 0.75s ease-out; }
        .anim-glasses { animation: clinkGlasses 0.7s ease-in-out; }
        .anim-tangerine { animation: tangerinePop 0.6s ease-out; }
        .anim-snowman { animation: snowmanDance 0.75s ease-in-out; }
        .anim-orb { animation: orbPulse 0.85s ease-in-out; }
        .anim-star { animation: starShine 0.75s ease-in-out; }

        /* Мерцание гирлянды */
        @keyframes bulbTwinkle {
            0%, 100% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.38; transform: scale(0.82); }
        }
        .garland-bulb {
            animation: bulbTwinkle 1.6s infinite ease-in-out;
        }

        /* Плавающее облачко предсказания */
        @keyframes toastFloat {
            0% { opacity: 0; transform: translate(-50%, 10px) scale(0.92); }
            15% { opacity: 1; transform: translate(-50%, 0px) scale(1); }
            85% { opacity: 1; transform: translate(-50%, -4px) scale(1); }
            100% { opacity: 0; transform: translate(-50%, -14px) scale(0.95); }
        }
        .prediction-bubble {
            animation: toastFloat 4.2s forwards cubic-bezier(0.22, 1, 0.36, 1);
        }

        /* Защита холста от перехвата скролла в Telegram WebView и iframe */
        #cardStage,
        #frostCanvas,
        .sticker-item {
            touch-action: none;
            user-select: none;
            -webkit-user-select: none;
        }
        .sticker-item {
            transition: filter 0.2s ease;
        }
        .sticker-item:hover .sticker-delete,
        .sticker-item:focus-within .sticker-delete {
            opacity: 1;
            transform: scale(1);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-slate-950 text-slate-100 selection:bg-amber-400/30 selection:text-amber-200">

    <!-- Верхняя панель навигации -->
    <header class="sticky top-0 z-40 backdrop-blur-xl bg-slate-900/85 border-b border-slate-800/80 px-3 sm:px-4 py-2.5 sm:py-3">
        <div class="max-w-7xl mx-auto flex flex-wrap items-center justify-between gap-2 sm:gap-3">
            <!-- Логотип -->
            <div class="flex items-center gap-2.5">
                <div class="w-9 h-9 rounded-xl bg-gradient-to-br from-emerald-500 to-teal-700 flex items-center justify-center text-xl shadow-lg shadow-emerald-950/60 border border-emerald-400/30">
                    🎄
                </div>
                <div>
                    <div class="flex items-center gap-2">
                        <span class="font-bold text-sm sm:text-lg tracking-tight text-white">Ёлка-Конструктор</span>
                        <span id="headerPlanBadge" class="text-[10px] sm:text-[11px] font-bold uppercase tracking-wider px-2 py-0.5 rounded-full bg-slate-800 text-slate-300 border border-slate-700">
                            FREE
                        </span>
                    </div>
                    <p id="tgContextHint" class="text-[11px] text-slate-400 hidden sm:block">Интерактивная открытка для Telegram и соцсетей</p>
                </div>
            </div>

            <!-- Правые элементы управления -->
            <div class="flex items-center gap-1.5 sm:gap-2.5">
                <!-- Переключатель демо-режима FREE / PRO -->
                <div class="flex items-center bg-slate-950/90 p-1 rounded-xl border border-slate-800" title="Переключение демо-тарифа">
                    <button id="btnModeFree" type="button" class="px-2 sm:px-2.5 py-1.5 rounded-lg text-xs font-semibold transition-all bg-slate-800 text-white shadow">
                        FREE
                    </button>
                    <button id="btnModePro" type="button" class="px-2 sm:px-2.5 py-1.5 rounded-lg text-xs font-semibold transition-all text-amber-300/80 hover:text-amber-200 flex items-center gap-1">
                        <span>✨ PRO</span>
                    </button>
                </div>

                <!-- Кнопка «Сбросить» -->
                <button id="btnResetAll" type="button" class="px-2.5 py-2 rounded-xl text-xs font-semibold bg-slate-800/90 hover:bg-slate-700/90 border border-slate-700/80 text-slate-300 hover:text-white transition-all active:scale-95" title="Сбросить открытку к начальному виду">
                    <span>🔄</span>
                    <span class="hidden md:inline ml-1">Сбросить</span>
                </button>

                <!-- Кнопка звука -->
                <button id="btnToggleSound" type="button" class="p-2 sm:p-2.5 rounded-xl bg-slate-800/90 hover:bg-slate-700/90 border border-slate-700/80 text-slate-200 transition-all active:scale-95" title="Включить или выключить звук">
                    <span id="soundIcon" class="text-sm sm:text-base leading-none">🔊</span>
                </button>

                <!-- Кнопка Предпросмотр глазами получателя -->
                <button id="btnTogglePreview" type="button" class="flex items-center gap-1.5 px-3 sm:px-3.5 py-2 rounded-xl text-xs sm:text-sm font-semibold bg-indigo-600 hover:bg-indigo-500 text-white shadow-lg shadow-indigo-950/60 transition-all active:scale-95">
                    <span id="previewBtnIcon">👁️</span>
                    <span id="previewBtnText">Глазами получателя</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Основная рабочая область -->
    <main class="flex-1 max-w-7xl w-full mx-auto px-3 sm:px-6 py-4 sm:py-6 grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">

        <!-- ЛЕВАЯ КОЛОНКА: Панель конструктора -->
        <section id="editorPanel" class="lg:col-span-5 xl:col-span-5 bg-slate-900/75 backdrop-blur-md border border-slate-800/90 rounded-3xl p-4 sm:p-5 shadow-2xl transition-all duration-300">
            <!-- Баннер тарифа внутри редактора -->
            <div id="freeUpsellBanner" class="mb-4 p-3.5 rounded-2xl bg-gradient-to-r from-amber-500/15 via-orange-500/10 to-purple-500/15 border border-amber-400/30 flex items-center justify-between gap-3">
                <div class="text-xs leading-relaxed">
                    <div class="font-bold text-amber-300 flex items-center gap-1.5">
                        <span>🎁 Режим FREE</span>
                    </div>
                    <p class="text-slate-300 mt-0.5">Базовая тема, до 5 стикеров и бейдж. Нажмите на любой PRO-элемент для предпросмотра покупки.</p>
                </div>
                <button id="btnBannerUnlock" type="button" class="shrink-0 px-3 py-2 rounded-xl text-xs font-bold bg-amber-400 hover:bg-amber-300 text-slate-950 transition-all active:scale-95 shadow">
                    PRO
                </button>
            </div>

            <div id="proActiveBanner" class="hidden mb-4 p-3 rounded-2xl bg-gradient-to-r from-emerald-500/15 to-teal-500/15 border border-emerald-400/30 flex items-center justify-between gap-2">
                <div class="text-xs text-emerald-200 flex items-center gap-2">
                    <span class="text-base">👑</span>
                    <span><strong>PRO-доступ активен!</strong> Все темы, салют, скретч-иней и стикеры открыты.</span>
                </div>
            </div>

            <!-- Вкладки конструктора -->
            <div class="grid grid-cols-4 gap-1.5 bg-slate-950/90 p-1.5 rounded-2xl border border-slate-800/90 mb-5" role="tablist">
                <button type="button" data-tab="themes" class="tab-btn py-2.5 px-2 rounded-xl text-xs font-semibold flex flex-col sm:flex-row items-center justify-center gap-1 bg-slate-800 text-white shadow transition-all">
                    <span>🎨</span><span>Темы</span>
                </button>
                <button type="button" data-tab="effects" class="tab-btn py-2.5 px-2 rounded-xl text-xs font-semibold flex flex-col sm:flex-row items-center justify-center gap-1 text-slate-400 hover:text-slate-200 transition-all">
                    <span>✨</span><span>Эффекты</span>
                </button>
                <button type="button" data-tab="stickers" class="tab-btn py-2.5 px-2 rounded-xl text-xs font-semibold flex flex-col sm:flex-row items-center justify-center gap-1 text-slate-400 hover:text-slate-200 transition-all">
                    <span>🧸</span><span>Стикеры</span>
                </button>
                <button type="button" data-tab="text" class="tab-btn py-2.5 px-2 rounded-xl text-xs font-semibold flex flex-col sm:flex-row items-center justify-center gap-1 text-slate-400 hover:text-slate-200 transition-all">
                    <span>✍️</span><span>Текст</span>
                </button>
            </div>

            <!-- Контент вкладок -->
            <div class="space-y-4">

                <!-- ВКЛАДКА 1: ТЕМЫ -->
                <div id="tab-themes" class="tab-content space-y-3">
                    <div class="flex items-center justify-between">
                        <h2 class="text-sm font-bold text-slate-200">Выберите стиль открытки</h2>
                        <span class="text-[11px] text-slate-400">Фон, шрифт и огоньки</span>
                    </div>
                    <div id="themesGrid" class="grid grid-cols-1 sm:grid-cols-2 gap-3"></div>
                </div>

                <!-- ВКЛАДКА 2: ЭФФЕКТЫ -->
                <div id="tab-effects" class="tab-content hidden space-y-3">
                    <div class="flex items-center justify-between">
                        <h2 class="text-sm font-bold text-slate-200">Интерактивные эффекты</h2>
                        <span class="text-[11px] text-slate-400">Можно комбинировать</span>
                    </div>
                    <div id="effectsList" class="space-y-2.5"></div>
                    <div id="scratchControls" class="hidden p-3.5 rounded-2xl bg-slate-950/70 border border-cyan-500/30 space-y-2">
                        <div class="flex items-center justify-between">
                            <span class="text-xs font-semibold text-cyan-300">❄️ Слой морозного узора активен</span>
                            <button type="button" id="btnResetFrost" class="px-2.5 py-1 rounded-lg bg-cyan-500/20 hover:bg-cyan-500/30 text-cyan-200 text-xs font-medium transition">
                                Восстановить иней
                            </button>
                        </div>
                        <p class="text-[11px] text-slate-400">Проведите пальцем или мышкой по центру открытки, чтобы стереть иней и открыть секретное пожелание.</p>
                    </div>
                </div>

                <!-- ВКЛАДКА 3: СТИКЕРЫ -->
                <div id="tab-stickers" class="tab-content hidden space-y-3">
                    <div class="flex items-center justify-between">
                        <h2 class="text-sm font-bold text-slate-200">Интерактивные стикеры</h2>
                        <span id="stickerCounterBadge" class="text-xs font-semibold px-2.5 py-0.5 rounded-full bg-slate-800 text-slate-300">
                            На открытке: 2 / 5 (FREE)
                        </span>
                    </div>
                    <p class="text-xs text-slate-400">Нажмите на стикер ниже, чтобы добавить на открытку. На холсте стикеры можно <strong>перетаскивать</strong> и <strong>нажимать на них</strong>!</p>

                    <div class="space-y-3 pt-1">
                        <div>
                            <div class="text-[11px] font-bold uppercase tracking-wider text-emerald-400 mb-2">Базовый набор (FREE)</div>
                            <div id="freeStickersGrid" class="grid grid-cols-3 sm:grid-cols-5 gap-2"></div>
                        </div>
                        <div>
                            <div class="text-[11px] font-bold uppercase tracking-wider text-amber-400 mb-2 flex items-center gap-1">
                                <span>👑 Расширенный набор (PRO)</span>
                            </div>
                            <div id="proStickersGrid" class="grid grid-cols-2 sm:grid-cols-3 gap-2"></div>
                        </div>
                    </div>

                    <div class="pt-2 flex justify-between items-center border-t border-slate-800/80">
                        <button type="button" id="btnClearStickers" class="text-xs text-slate-400 hover:text-rose-400 transition">
                            Очистить все стикеры
                        </button>
                        <button type="button" id="btnResetStickers" class="text-xs text-indigo-400 hover:text-indigo-300 transition">
                            Вернуть стандартные
                        </button>
                    </div>
                </div>

                <!-- ВКЛАДКА 4: ТЕКСТ -->
                <div id="tab-text" class="tab-content hidden space-y-3.5">
                    <div class="grid grid-cols-2 gap-2.5">
                        <div>
                            <label for="inputTo" class="block text-[11px] font-semibold text-slate-400 mb-1">Кому</label>
                            <input id="inputTo" type="text" maxlength="28" placeholder="Например: Аня" class="w-full px-3 py-2 rounded-xl bg-slate-950/90 border border-slate-800 text-sm text-white focus:outline-none focus:border-amber-400 transition">
                        </div>
                        <div>
                            <label for="inputFrom" class="block text-[11px] font-semibold text-slate-400 mb-1">От кого</label>
                            <input id="inputFrom" type="text" maxlength="28" placeholder="Например: Макс" class="w-full px-3 py-2 rounded-xl bg-slate-950/90 border border-slate-800 text-sm text-white focus:outline-none focus:border-amber-400 transition">
                        </div>
                    </div>

                    <div>
                        <div class="flex items-center justify-between mb-1.5">
                            <label class="text-[11px] font-semibold text-slate-400">Готовые шаблоны (Юмор, Друзья, Коллеги, Тёплые)</label>
                        </div>
                        <div id="templatesList" class="max-h-44 overflow-y-auto space-y-1.5 pr-1"></div>
                    </div>

                    <div>
                        <label for="inputMessage" class="block text-[11px] font-semibold text-slate-400 mb-1">Текст поздравления</label>
                        <textarea id="inputMessage" rows="3" maxlength="220" class="w-full px-3 py-2 rounded-xl bg-slate-950/90 border border-slate-800 text-sm text-white focus:outline-none focus:border-amber-400 transition resize-none"></textarea>
                    </div>

                    <div class="p-3 rounded-2xl bg-slate-950/80 border border-amber-400/25 space-y-1.5">
                        <div class="flex items-center justify-between">
                            <label for="inputSecret" class="text-xs font-semibold text-amber-300 flex items-center gap-1.5">
                                <span>❄️ Секретное послание под инеем</span>
                                <span class="text-[10px] px-1.5 py-0.5 rounded bg-amber-400/20 text-amber-300 font-bold">PRO</span>
                            </label>
                        </div>
                        <input id="inputSecret" type="text" maxlength="110" placeholder="Откроется, когда получатель сотрёт иней..." class="w-full px-3 py-2 rounded-xl bg-slate-900 border border-slate-800 text-xs text-white focus:outline-none focus:border-amber-400 transition">
                    </div>
                </div>
            </div>
        </section>

        <!-- ПРАВАЯ КОЛОНКА: Интерактивный холст открытки -->
        <section id="canvasColumn" class="lg:col-span-7 xl:col-span-7 flex flex-col items-center justify-center">
            <!-- Плашка режима предпросмотра (показывается в режиме «Глазами получателя») -->
            <div id="recipientModeBanner" class="hidden w-full max-w-[400px] mb-3 px-4 py-2.5 rounded-2xl bg-indigo-950/85 border border-indigo-400/35 flex items-center justify-between gap-2 text-xs">
                <span class="text-indigo-200 flex items-center gap-1.5">
                    <span>👀</span> <span>Режим получателя открытки</span>
                </span>
                <button id="btnBackToBuilder" type="button" class="font-bold text-white underline hover:text-indigo-200 shrink-0">
                    Открыть конструктор
                </button>
            </div>

            <!-- Рамка смартфона / Карточка 9:16 -->
            <div class="relative w-full max-w-[385px] sm:max-w-[405px] rounded-[2.6rem] p-2.5 bg-gradient-to-b from-slate-700 via-slate-800 to-slate-900 shadow-[0_30px_90px_rgba(0,0,0,0.85)] border border-slate-600/40">
                <!-- Верхний вырез динамика -->
                <div class="absolute top-4 left-1/2 -translate-x-1/2 w-24 h-4 bg-slate-950 rounded-full z-30 flex items-center justify-end px-2.5 pointer-events-none">
                    <div class="w-2 h-2 rounded-full bg-slate-800"></div>
                </div>

                <!-- Сама открытка 9:16 -->
                <div id="cardStage" class="card-theme-nordic relative w-full aspect-[9/16] rounded-[2.1rem] overflow-hidden select-none flex flex-col justify-between transition-all duration-500">

                    <!-- Декоративная внутренняя рамка (для Арт-деко и Ретро) -->
                    <div id="cardInnerFrame" class="pointer-events-none absolute inset-3 rounded-[1.6rem] z-10 transition-all duration-500"></div>

                    <!-- Верхняя гирлянда -->
                    <div id="garlandContainer" class="relative z-20 pt-7 px-4 flex justify-between items-center pointer-events-none"></div>

                    <!-- Слой 1 Canvas: Боке и Снегопад -->
                    <canvas id="bgEffectsCanvas" class="absolute inset-0 w-full h-full pointer-events-none z-10"></canvas>

                    <!-- Слой 2: Текстовый блок и скретч-зона -->
                    <div class="relative z-20 px-6 my-auto flex flex-col items-center text-center pointer-events-none">
                        <div id="cardToBadge" class="inline-block px-3.5 py-1 rounded-full text-xs font-semibold tracking-wide mb-2 backdrop-blur-sm bg-white/25 border border-current/15 shadow-sm">
                            Для: Аня ✨
                        </div>

                        <h1 id="cardTitle" class="font-theme-nordic text-2xl sm:text-[26px] font-bold leading-tight mb-2.5 drop-shadow-sm">
                            С Новым 2027 годом!
                        </h1>

                        <p id="cardBodyText" class="text-sm sm:text-[15px] leading-relaxed font-medium opacity-95 max-w-[290px]">
                            Пусть в новом году батарейка всегда будет на 100%, тревоги — на 0%, а поводов для радости будет с запасом!
                        </p>

                        <!-- Блок секретного послания + скретч-слой (PRO эффект) -->
                        <div id="secretScratchBox" class="hidden relative mt-4 w-full max-w-[275px] rounded-2xl overflow-hidden shadow-lg border border-white/40 pointer-events-auto">
                            <div class="p-3.5 bg-white/90 text-slate-900 text-xs font-semibold leading-snug min-h-[68px] flex flex-col items-center justify-center">
                                <span class="text-[10px] uppercase tracking-wider text-amber-600 font-bold mb-0.5">✨ Секретное пожелание</span>
                                <span id="cardSecretText">Пусть исполнится самое заветное желание, загаданное под бой курантов!</span>
                            </div>
                            <!-- Canvas для стирания инея -->
                            <canvas id="frostCanvas" class="absolute inset-0 w-full h-full cursor-crosshair"></canvas>
                        </div>

                        <div id="cardFromText" class="mt-3 text-xs font-semibold opacity-85 italic">
                            С теплом, Макс 🎄
                        </div>
                    </div>

                    <!-- Слой 3: Интерактивные перетаскиваемые стикеры -->
                    <div id="stickersLayer" class="absolute inset-0 z-20 overflow-hidden pointer-events-none"></div>

                    <!-- Слой 4 Canvas: Салюты, искры и конфетти поверх сцены -->
                    <canvas id="fxOverlayCanvas" class="absolute inset-0 w-full h-full pointer-events-none z-30"></canvas>

                    <!-- Всплывающее облачко предсказания (от Шара судьбы) -->
                    <div id="predictionContainer" class="absolute bottom-24 left-0 right-0 z-40 pointer-events-none"></div>

                    <!-- Нижняя часть открытки: Подсказка интерактива и Вирусный бейдж FREE -->
                    <div class="relative z-30 px-4 pb-3.5 pt-2 flex flex-col items-center gap-2">
                        <div id="interactiveHintPill" class="px-3 py-1 rounded-full text-[11px] font-medium backdrop-blur-md bg-black/25 text-white/90 border border-white/15 pointer-events-none transition-opacity">
                            👆 Нажимай на стикеры или перетаскивай их!
                        </div>

                        <!-- Вирусный бейдж (только для FREE режима) -->
                        <button id="viralFreeBadge" type="button" class="w-full py-2 px-3 rounded-xl bg-slate-950/80 hover:bg-slate-900 backdrop-blur-md border border-white/15 text-[11px] text-slate-200 flex items-center justify-center gap-1.5 shadow-lg transition-all group">
                            <span>🎄 Сделано в <strong>Ёлка-Конструкторе</strong></span>
                            <span class="text-amber-300 font-semibold group-hover:underline">· Создать свою</span>
                        </button>
                    </div>
                </div>
            </div>

            <!-- Панель действий под открыткой (Telegram Share + Копирование ссылки + Ответная) -->
            <div class="w-full max-w-[405px] mt-4 flex flex-col gap-2.5">
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-2.5">
                    <!-- Главная кнопка: Отправить в Telegram -->
                    <button id="btnSendTelegram" type="button" class="py-3 px-4 rounded-2xl bg-gradient-to-r from-sky-500 to-blue-600 hover:from-sky-400 hover:to-blue-500 text-white font-bold text-sm shadow-lg shadow-sky-950/60 flex items-center justify-center gap-2 transition-all active:scale-95">
                        <svg class="w-4 h-4 fill-current shrink-0" viewBox="0 0 24 24" aria-hidden="true">
                            <path d="M11.944 0A12 12 0 0 0 0 12a12 12 0 0 0 12 12 12 12 0 0 0 12-12A12 12 0 0 0 12 0a12 12 0 0 0-.056 0zm4.962 7.224c.1-.002.321.023.465.14a.506.506 0 0 1 .171.325c.016.093.036.306.02.472-.18 1.898-.962 6.502-1.36 8.627-.168.9-.499 1.201-.82 1.23-.696.065-1.225-.46-1.9-.902-1.056-.693-1.653-1.124-2.678-1.8-1.185-.78-.417-1.21.258-1.91.177-.184 3.247-2.977 3.307-3.23.007-.032.014-.15-.056-.212s-.174-.041-.249-.024c-.106.024-1.793 1.14-5.061 3.345-.48.33-.913.49-1.302.48-.428-.008-1.252-.241-1.865-.44-.752-.245-1.349-.374-1.297-.789.027-.216.325-.437.893-.663 3.498-1.524 5.83-2.529 6.998-3.014 3.332-1.386 4.025-1.627 4.476-1.635z"/>
                        </svg>
                        <span>Отправить в Telegram</span>
                    </button>

                    <!-- Вторая кнопка: Скопировать ссылку -->
                    <button id="btnQuickCopyUrl" type="button" class="py-3 px-4 rounded-2xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-slate-100 font-bold text-sm shadow-lg flex items-center justify-center gap-2 transition-all active:scale-95">
                        <span>🔗 Скопировать ссылку</span>
                    </button>
                </div>

                <!-- Кнопка вирусной петли в режиме получателя -->
                <button id="btnReplyCard" type="button" class="hidden w-full py-3 px-4 rounded-2xl bg-gradient-to-r from-amber-400 to-orange-500 hover:from-amber-300 hover:to-orange-400 text-slate-950 font-extrabold text-sm shadow-lg shadow-amber-950/50 flex items-center justify-center gap-2 transition-all active:scale-95">
                    <span>💌 Отправить ответную открытку</span>
                </button>
            </div>
        </section>
    </main>

    <!-- МОДАЛЬНОЕ ОКНО: Покупка PRO (Демо-оплата) -->
    <div id="proModal" class="hidden fixed inset-0 z-50 flex items-center justify-center p-4 bg-slate-950/80 backdrop-blur-md">
        <div class="relative w-full max-w-md rounded-3xl bg-slate-900 border border-amber-400/40 p-6 shadow-[0_25px_70px_rgba(0,0,0,0.9)] overflow-hidden">
            <div class="absolute -top-24 -right-24 w-48 h-48 bg-amber-400/20 rounded-full blur-3xl pointer-events-none"></div>

            <button id="btnCloseProModal" type="button" class="absolute top-4 right-4 w-8 h-8 rounded-full bg-slate-800 hover:bg-slate-700 text-slate-300 flex items-center justify-center text-sm transition">
                ✕
            </button>

            <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-amber-400/15 border border-amber-400/30 text-amber-300 text-xs font-bold mb-3">
                <span>👑 ЁЛКА-КОНСТРУКТОР PRO</span>
            </div>

            <h3 id="proModalTitle" class="text-xl font-extrabold text-white leading-snug">
                Откройте все эффекты и уберите водяной знак
            </h3>
            <p class="text-xs text-slate-300 mt-1.5 leading-relaxed">
                Превратите открытку в настоящее интерактивное шоу для друзей, близких и коллег.
            </p>

            <ul class="mt-4 space-y-2.5 text-xs text-slate-200">
                <li class="flex items-center gap-2.5">
                    <span class="w-5 h-5 rounded-full bg-emerald-500/20 text-emerald-300 flex items-center justify-center font-bold">✓</span>
                    <span><strong>4 дизайнерские темы:</strong> Кибер-год, Золотой арт-деко, Ретро</span>
                </li>
                <li class="flex items-center gap-2.5">
                    <span class="w-5 h-5 rounded-full bg-emerald-500/20 text-emerald-300 flex items-center justify-center font-bold">✓</span>
                    <span><strong>Вау-эффекты:</strong> Салют по тапу, усиленное боке и стираемый иней</span>
                </li>
                <li class="flex items-center gap-2.5">
                    <span class="w-5 h-5 rounded-full bg-emerald-500/20 text-emerald-300 flex items-center justify-center font-bold">✓</span>
                    <span><strong>Безлимит стикеров:</strong> Мандарин, Шар судьбы, Снеговик, Олень, Часы, Премия</span>
                </li>
                <li class="flex items-center gap-2.5">
                    <span class="w-5 h-5 rounded-full bg-emerald-500/20 text-emerald-300 flex items-center justify-center font-bold">✓</span>
                    <span><strong>Чистая открытка</strong> без нижнего бейджа конструктора</span>
                </li>
            </ul>

            <div class="mt-5 grid grid-cols-2 gap-2.5">
                <button type="button" data-pro-plan="single" class="pro-plan-btn p-3 rounded-2xl bg-slate-800/90 hover:bg-slate-800 border border-slate-700 text-left transition group">
                    <div class="text-[11px] text-slate-400">Открыть PRO на 1 открытку</div>
                    <div class="text-base font-extrabold text-white mt-0.5 group-hover:text-amber-300">149 ₽</div>
                    <div class="text-[10px] text-slate-400 mt-0.5">Навсегда для этой ссылки</div>
                </button>
                <button type="button" data-pro-plan="season" class="pro-plan-btn p-3 rounded-2xl bg-gradient-to-br from-amber-400/20 to-orange-500/20 hover:from-amber-400/30 hover:to-orange-500/30 border border-amber-400/60 text-left transition relative">
                    <span class="absolute -top-2 right-2 px-1.5 py-0.5 rounded bg-amber-400 text-slate-950 text-[9px] font-extrabold uppercase">Хит</span>
                    <div class="text-[11px] text-amber-200">Сезонный безлимит</div>
                    <div class="text-base font-extrabold text-white mt-0.5">399 ₽</div>
                    <div class="text-[10px] text-amber-200/80 mt-0.5">Сколько угодно открыток</div>
                </button>
            </div>

            <button type="button" data-pro-plan="demo" class="pro-plan-btn mt-4 w-full py-3 rounded-2xl bg-gradient-to-r from-amber-400 via-yellow-400 to-orange-400 hover:brightness-105 text-slate-950 font-extrabold text-sm shadow-lg shadow-amber-500/25 transition-all active:scale-95">
                ✨ Активировать PRO бесплатно (Демо-тест)
            </button>
        </div>
    </div>

    <!-- МОДАЛЬНОЕ ОКНО: Ссылка на открытку (Fallback для iframe/WebView и ручного копирования) -->
    <div id="shareModal" class="hidden fixed inset-0 z-50 flex items-center justify-center p-4 bg-slate-950/80 backdrop-blur-md">
        <div class="relative w-full max-w-md rounded-3xl bg-slate-900 border border-slate-700 p-6 shadow-2xl">
            <button id="btnCloseShareModal" type="button" class="absolute top-4 right-4 w-8 h-8 rounded-full bg-slate-800 hover:bg-slate-700 text-slate-300 flex items-center justify-center text-sm">
                ✕
            </button>
            <div class="text-2xl mb-2">🎄</div>
            <h3 class="text-lg font-extrabold text-white">Персональная ссылка на открытку</h3>
            <p class="text-xs text-slate-300 mt-1">
                Все выбранные эффекты, тексты и стикеры уже зашиты в эту ссылку.
            </p>

            <div class="mt-4 flex items-center gap-2 p-2 rounded-2xl bg-slate-950 border border-slate-800">
                <input id="shareLinkInput" type="text" readonly value="" class="w-full bg-transparent text-xs text-emerald-300 px-2 focus:outline-none">
                <button id="btnModalCopy" type="button" class="shrink-0 px-3.5 py-2 rounded-xl bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-bold text-xs transition">
                    Копировать
                </button>
            </div>

            <div id="sharePlanNotice" class="mt-3 p-3 rounded-xl bg-amber-500/10 border border-amber-400/25 text-[11px] text-amber-200 flex items-center justify-between gap-2"></div>

            <div class="mt-4 grid grid-cols-2 gap-2">
                <button id="btnModalTgShare" type="button" class="py-2.5 px-3 rounded-xl bg-sky-600 hover:bg-sky-500 text-white text-xs font-bold transition flex items-center justify-center gap-1.5">
                    <span>✈️ В Telegram</span>
                </button>
                <button id="btnModalPreview" type="button" class="py-2.5 px-3 rounded-xl bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-bold transition">
                    👁️ Предпросмотр
                </button>
            </div>
        </div>
    </div>

    <!-- Всплывающее системное уведомление (вместо alert) -->
    <div id="toastNotification" class="fixed bottom-5 left-1/2 -translate-x-1/2 z-50 pointer-events-none opacity-0 transition-all duration-300 translate-y-3">
        <div id="toastText" class="px-4 py-2.5 rounded-2xl bg-slate-900/95 text-white text-xs font-semibold border border-slate-700 shadow-2xl backdrop-blur-md text-center max-w-[90vw]">
            Уведомление
        </div>
    </div>

    <script>
        /**
         * ============================================================================
         * 1. БЕЗОПАСНАЯ ИНТЕГРАЦИЯ TELEGRAM WEBAPP SDK И SANDBOX/IFRAME ЗАЩИТА
         * ============================================================================
         */
        const tgWebApp = (() => {
            try {
                if (window.Telegram && window.Telegram.WebApp) {
                    const tg = window.Telegram.WebApp;
                    if (typeof tg.ready === 'function') tg.ready();
                    if (typeof tg.expand === 'function') tg.expand();
                    return tg;
                }
            } catch (err) {
                console.warn('Telegram WebApp SDK недоступен в текущем окружении:', err);
            }
            return null;
        })();

        function triggerHaptic(style = 'light') {
            try {
                if (tgWebApp && tgWebApp.HapticFeedback && typeof tgWebApp.HapticFeedback.impactOccurred === 'function') {
                    tgWebApp.HapticFeedback.impactOccurred(style);
                }
            } catch (_) {}
        }

        function safeStorageGet(key) {
            try {
                return window.localStorage ? window.localStorage.getItem(key) : null;
            } catch (_) {
                return null;
            }
        }

        function safeStorageSet(key, value) {
            try {
                if (window.localStorage) {
                    window.localStorage.setItem(key, value);
                }
            } catch (_) {}
        }

        function safeReplaceHash(newHash) {
            try {
                if (window.history && typeof window.history.replaceState === 'function') {
                    window.history.replaceState(null, '', newHash || window.location.pathname + window.location.search);
                }
            } catch (_) {}
        }

        /**
         * ============================================================================
         * 2. КОНФИГУРАЦИЯ ДАННЫХ КОНСТРУКТОРА (ТЕМЫ, ЭФФЕКТЫ, СТИКЕРЫ, ТЕКСТЫ)
         * ============================================================================
         */
        const FREE_STICKER_LIMIT = 5;

        const THEMES = [
            {
                id: 'nordic',
                name: 'Тёплый скандинавский уют',
                desc: 'Хвоя, крем и тёплое золото',
                pro: false,
                cardClass: 'card-theme-nordic',
                fontClass: 'font-theme-nordic',
                previewBg: 'linear-gradient(135deg, #fef9f0 0%, #234236 100%)',
                bulbColors: ['#fde047', '#fb923c', '#f87171', '#4ade80']
            },
            {
                id: 'cyber',
                name: 'Неоновый кибер-Новый год',
                desc: 'Индиго, циан и маджента',
                pro: true,
                cardClass: 'card-theme-cyber',
                fontClass: 'font-theme-cyber',
                previewBg: 'linear-gradient(135deg, #2a0e4a 0%, #06b6d4 100%)',
                bulbColors: ['#22d3ee', '#e879f9', '#a855f7', '#38bdf8']
            },
            {
                id: 'artdeco',
                name: 'Золотой арт-деко / Гэтсби',
                desc: 'Чёрный бархат и золото',
                pro: true,
                cardClass: 'card-theme-artdeco',
                fontClass: 'font-theme-artdeco',
                previewBg: 'linear-gradient(135deg, #241f18 0%, #d4af37 100%)',
                bulbColors: ['#fde047', '#f59e0b', '#fef08a', '#d97706']
            },
            {
                id: 'retro',
                name: 'Советский ретро-мультфильм',
                desc: 'Тёплая винтажная открытка',
                pro: true,
                cardClass: 'card-theme-retro',
                fontClass: 'font-theme-retro',
                previewBg: 'linear-gradient(135deg, #fff5e4 0%, #b91c1c 100%)',
                bulbColors: ['#ef4444', '#f59e0b', '#10b981', '#3b82f6']
            }
        ];

        const EFFECTS = [
            {
                id: 'snow',
                name: 'Снегопад',
                desc: 'Плавные снежинки на фоне открытки',
                icon: '❄️',
                pro: false
            },
            {
                id: 'bokeh',
                name: 'Гирлянды и боке',
                desc: 'Мерцающие огоньки и световые блики (в PRO — ярче)',
                icon: '✨',
                pro: false
            },
            {
                id: 'fireworks',
                name: 'Салют по тапу',
                desc: 'Нажатие на экран запускает салют со звуком',
                icon: '🎆',
                pro: true
            },
            {
                id: 'frost',
                name: 'Морозный скретч-слой',
                desc: 'Секретное послание под слоем стираемого инея',
                icon: '🧊',
                pro: true
            }
        ];

        const STICKER_TYPES = [
            // FREE (5)
            { type: 'tree', emoji: '🎄', label: 'Ёлка', actionHint: 'Огоньки и звон', animClass: 'anim-tree', pro: false },
            { type: 'snowflake', emoji: '❄️', label: 'Снежинка', actionHint: 'Морозный вихрь', animClass: 'anim-snowflake', pro: false },
            { type: 'gift', emoji: '🎁', label: 'Подарок', actionHint: 'Прыжок и конфетти', animClass: 'anim-gift', pro: false },
            { type: 'glasses', emoji: '🥂', label: 'Бокалы', actionHint: 'Звон бокалов', animClass: 'anim-glasses', pro: false },
            { type: 'star', emoji: '⭐', label: 'Звезда', actionHint: 'Яркая вспышка', animClass: 'anim-star', pro: false },
            // PRO (6)
            { type: 'tangerine', emoji: '🍊', label: 'Мандарин', actionHint: 'Взрыв блёсток', animClass: 'anim-tangerine', pro: true },
            { type: 'orb', emoji: '🔮', label: 'Шар судьбы', actionHint: 'Предсказание года', animClass: 'anim-orb', pro: true },
            { type: 'snowman', emoji: '⛄', label: 'Снеговик', actionHint: 'Меняет эмоцию', animClass: 'anim-snowman', pro: true },
            { type: 'deer', emoji: '🦌', label: 'Олень', actionHint: 'Волшебный прыжок', animClass: 'anim-gift', pro: true },
            { type: 'clock', emoji: '🕛', label: 'Часы 23:55', actionHint: 'Бой курантов', animClass: 'anim-tree', pro: true },
            { type: 'trophy', emoji: '🏆', label: 'Премия года', actionHint: 'Золотой салют', animClass: 'anim-star', pro: true }
        ];

        const SNOWMAN_FACES = ['⛄', '☃️', '🎅', '🥳'];

        const TEXT_TEMPLATES = [
            // Юмор
            {
                category: '😄 Юмор (FREE)',
                pro: false,
                text: 'Пусть в новом году батарейка всегда будет на 100%, тревоги — на 0%, а поводов для радости будет с запасом!'
            },
            {
                category: '😄 Юмор (PRO)',
                pro: true,
                text: 'Желаю, чтобы баланс на карте рос быстрее, чем счётчик непрочитанных сообщений после праздников!'
            },
            // Друзья
            {
                category: '🥂 Друзьям (FREE)',
                pro: false,
                text: 'С Новым годом! Пусть каждый месяц приносит крутые истории, живые встречи и поводы сказать: «Вот это было мощно!»'
            },
            {
                category: '🥂 Друзьям (PRO)',
                pro: true,
                text: 'Пусть в этом году «надо как-нибудь пересечься» наконец превратится в громкий смех до слёз и общие приключения!'
            },
            // Коллеги
            {
                category: '💼 Коллегам (FREE)',
                pro: false,
                text: 'С Новым годом! Пусть задачи решаются с первого созвона, статусы горят зелёным, а после работы остаётся энергия на жизнь.'
            },
            {
                category: '💼 Коллегам (PRO)',
                pro: true,
                text: 'Пусть в новом году всё сдаётся вовремя, правки будут минимальными, а кофе всегда остаётся горячим!'
            },
            // Тёплые / Романтичные
            {
                category: '❤️ Тёплое (FREE)',
                pro: false,
                text: 'Пусть новый год принесёт в твой дом свет, здоровье, добрые новости и ощущение, что всё складывается так, как нужно.'
            },
            {
                category: '❤️ Романтичное (PRO)',
                pro: true,
                text: 'Мой лучший итог года — то, что ты рядом. Пусть в новом году у нас будет ещё больше тёплых вечеров и тихого счастья.'
            }
        ];

        const PREDICTIONS = [
            '🔮 В этом году тебя ждёт неожиданное путешествие, о котором ты давно мечтал(а)!',
            '🔮 Финансовый прогноз: денежный магнит включится уже в первом квартале!',
            '🔮 Год принесёт знакомство, которое круто изменит планы к лучшему.',
            '🔮 Уровень удачи вырастет на 300% — смело берись за самые дерзкие идеи!',
            '🔮 Тебя ждёт год без авралов, с идеальным балансом отдыха и побед!',
            '🔮 Самое заветное желание, загаданное под бой курантов, сбудется быстрее, чем ожидаешь!'
        ];

        function createDefaultState() {
            return {
                isPro: false,
                isPreviewMode: false,
                soundEnabled: true,
                activeTab: 'themes',
                themeId: 'nordic',
                effects: {
                    snow: true,
                    bokeh: true,
                    fireworks: false,
                    frost: false
                },
                to: 'Аня',
                from: 'Макс',
                message: TEXT_TEMPLATES[0].text,
                secretMessage: 'Пусть исполнится самое заветное желание, загаданное под бой курантов!',
                stickers: [
                    { id: 'st_1', type: 'tree', x: 0.20, y: 0.76, scale: 1.15, emoji: '🎄', faceIdx: 0 },
                    { id: 'st_2', type: 'gift', x: 0.78, y: 0.77, scale: 1.05, emoji: '🎁', faceIdx: 0 }
                ]
            };
        }

        let state = createDefaultState();

        /**
         * ============================================================================
         * 3. WEB AUDIO API (СИНТЕЗ ПРАЗДНИЧНЫХ ЗВУКОВ ПО ПЕРВОМУ КАСАНИЮ)
         * ============================================================================
         */
        let audioCtx = null;

        function ensureAudioContext() {
            try {
                if (!audioCtx) {
                    const AudioContextClass = window.AudioContext || window.webkitAudioContext;
                    if (AudioContextClass) {
                        audioCtx = new AudioContextClass();
                    }
                }
                if (audioCtx && audioCtx.state === 'suspended') {
                    audioCtx.resume();
                }
            } catch (_) {}
            return audioCtx;
        }

        function playSoundEffect(kind) {
            if (!state.soundEnabled) return;
            const ctx = ensureAudioContext();
            if (!ctx) return;

            try {
                const now = ctx.currentTime;

                if (kind === 'chime' || kind === 'tree' || kind === 'star') {
                    const notes = [523.25, 659.25, 783.99, 1046.5];
                    notes.forEach((freq, idx) => {
                        const osc = ctx.createOscillator();
                        const gain = ctx.createGain();
                        osc.type = 'sine';
                        osc.frequency.setValueAtTime(freq, now + idx * 0.06);
                        gain.gain.setValueAtTime(0.001, now + idx * 0.06);
                        gain.gain.exponentialRampToValueAtTime(0.07, now + idx * 0.06 + 0.02);
                        gain.gain.exponentialRampToValueAtTime(0.0001, now + idx * 0.06 + 0.45);
                        osc.connect(gain);
                        gain.connect(ctx.destination);
                        osc.start(now + idx * 0.06);
                        osc.stop(now + idx * 0.06 + 0.48);
                    });
                } else if (kind === 'firework') {
                    const osc = ctx.createOscillator();
                    const gain = ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(220, now);
                    osc.frequency.exponentialRampToValueAtTime(880, now + 0.14);
                    osc.frequency.exponentialRampToValueAtTime(110, now + 0.42);
                    gain.gain.setValueAtTime(0.09, now);
                    gain.gain.exponentialRampToValueAtTime(0.0001, now + 0.44);
                    osc.connect(gain);
                    gain.connect(ctx.destination);
                    osc.start(now);
                    osc.stop(now + 0.45);
                } else if (kind === 'glasses') {
                    [1318.5, 1760].forEach((freq) => {
                        const osc = ctx.createOscillator();
                        const gain = ctx.createGain();
                        osc.type = 'sine';
                        osc.frequency.setValueAtTime(freq, now);
                        gain.gain.setValueAtTime(0.07, now);
                        gain.gain.exponentialRampToValueAtTime(0.0001, now + 0.55);
                        osc.connect(gain);
                        gain.connect(ctx.destination);
                        osc.start(now);
                        osc.stop(now + 0.58);
                    });
                } else if (kind === 'orb') {
                    const osc = ctx.createOscillator();
                    const gain = ctx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(340, now);
                    osc.frequency.exponentialRampToValueAtTime(680, now + 0.35);
                    gain.gain.setValueAtTime(0.07, now);
                    gain.gain.exponentialRampToValueAtTime(0.0001, now + 0.5);
                    osc.connect(gain);
                    gain.connect(ctx.destination);
                    osc.start(now);
                    osc.stop(now + 0.52);
                } else {
                    const osc = ctx.createOscillator();
                    const gain = ctx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(420, now);
                    osc.frequency.exponentialRampToValueAtTime(760, now + 0.12);
                    gain.gain.setValueAtTime(0.07, now);
                    gain.gain.exponentialRampToValueAtTime(0.0001, now + 0.22);
                    osc.connect(gain);
                    gain.connect(ctx.destination);
                    osc.start(now);
                    osc.stop(now + 0.24);
                }
            } catch (_) {}
        }

        /**
         * ============================================================================
         * 4. КОДИРОВАНИЕ И ДЕКОДИРОВАНИЕ СОСТОЯНИЯ В URL (#card=...)
         * ============================================================================
         */
        function encodeCardStateToHash() {
            const payload = {
                p: state.isPro ? 1 : 0,
                t: state.themeId,
                e: [
                    state.effects.snow ? 1 : 0,
                    state.effects.bokeh ? 1 : 0,
                    state.effects.fireworks ? 1 : 0,
                    state.effects.frost ? 1 : 0
                ],
                to: state.to,
                fr: state.from,
                m: state.message,
                sm: state.secretMessage,
                st: state.stickers.map(s => [
                    s.type,
                    Math.round(s.x * 1000) / 1000,
                    Math.round(s.y * 1000) / 1000,
                    s.emoji
                ])
            };

            const jsonStr = JSON.stringify(payload);
            const bytes = new TextEncoder().encode(jsonStr);
            let binary = '';
            for (let i = 0; i < bytes.length; i++) {
                binary += String.fromCharCode(bytes[i]);
            }
            return btoa(binary)
                .replace(/\+/g, '-')
                .replace(/\//g, '_')
                .replace(/=+$/, '');
        }

        function buildShareUrl() {
            const encoded = encodeCardStateToHash();
            const baseUrl = window.location.href.split('#')[0].split('?')[0];
            return `${baseUrl}#card=${encoded}`;
        }

        function decodeCardStateFromUrl() {
            try {
                let rawParam = null;
                const hash = window.location.hash || '';
                const search = window.location.search || '';

                if (hash.startsWith('#card=')) {
                    rawParam = hash.slice(6);
                } else if (hash.startsWith('#c=')) {
                    rawParam = hash.slice(3);
                } else {
                    const params = new URLSearchParams(search);
                    rawParam = params.get('card') || params.get('c');
                }

                if (!rawParam) return false;

                let base64 = rawParam.replace(/-/g, '+').replace(/_/g, '/');
                while (base64.length % 4 !== 0) {
                    base64 += '=';
                }

                const binary = atob(base64);
                const bytes = new Uint8Array(binary.length);
                for (let i = 0; i < binary.length; i++) {
                    bytes[i] = binary.charCodeAt(i);
                }
                const jsonStr = new TextDecoder().decode(bytes);
                const data = JSON.parse(jsonStr);

                if (!data || typeof data !== 'object') return false;

                state.isPro = Boolean(data.p);

                if (typeof data.t === 'string' && THEMES.some(th => th.id === data.t)) {
                    state.themeId = data.t;
                }

                if (Array.isArray(data.e) && data.e.length >= 4) {
                    state.effects.snow = Boolean(data.e[0]);
                    state.effects.bokeh = Boolean(data.e[1]);
                    state.effects.fireworks = state.isPro && Boolean(data.e[2]);
                    state.effects.frost = state.isPro && Boolean(data.e[3]);
                }

                if (typeof data.to === 'string') state.to = data.to;
                if (typeof data.fr === 'string') state.from = data.fr;
                if (typeof data.m === 'string') state.message = data.m;
                if (typeof data.sm === 'string') state.secretMessage = data.sm;

                if (Array.isArray(data.st)) {
                    const validStickers = [];
                    data.st.forEach((item, idx) => {
                        if (!Array.isArray(item) || item.length < 3) return;
                        const [type, x, y, customEmoji] = item;
                        const meta = STICKER_TYPES.find(m => m.type === type);
                        if (!meta) return;
                        if (meta.pro && !state.isPro) return;
                        validStickers.push({
                            id: `st_url_${idx}_${Date.now()}`,
                            type: meta.type,
                            emoji: typeof customEmoji === 'string' ? customEmoji : meta.emoji,
                            x: Math.min(0.92, Math.max(0.08, Number(x) || 0.5)),
                            y: Math.min(0.88, Math.max(0.10, Number(y) || 0.5)),
                            scale: 1.1,
                            faceIdx: 0
                        });
                    });
                    state.stickers = state.isPro
                        ? validStickers
                        : validStickers.slice(0, FREE_STICKER_LIMIT);
                }

                return true;
            } catch (err) {
                console.warn('Не удалось декодировать открытку из ссылки:', err);
                return false;
            }
        }

        /**
         * ============================================================================
         * 5. ОТПРАВКА В TELEGRAM И БЕЗОПАСНОЕ КОПИРОВАНИЕ ССЫЛКИ
         * ============================================================================
         */
        function shareToTelegram() {
            triggerHaptic('medium');
            const shareUrl = buildShareUrl();
            safeReplaceHash(`#card=${encodeCardStateToHash()}`);

            const recipientPart = state.to.trim() ? `${state.to.trim()}, ` : '';
            const shareText = `🎄 ${recipientPart}тебе интерактивная новогодняя открытка! Нажми, чтобы открыть и запустить салют ✨`;
            const telegramShareLink = `https://t.me/share/url?url=${encodeURIComponent(shareUrl)}&text=${encodeURIComponent(shareText)}`;

            try {
                if (tgWebApp && typeof tgWebApp.openTelegramLink === 'function') {
                    tgWebApp.openTelegramLink(telegramShareLink);
                    return;
                }
            } catch (err) {
                console.warn('Не удалось вызвать tg.openTelegramLink, открываем через window.open:', err);
            }

            const popup = window.open(telegramShareLink, '_blank', 'noopener,noreferrer');
            if (!popup) {
                // Если всплывающее окно заблокировано (например, в строгом iframe), показываем модалку со ссылкой
                openShareModal();
                showToast('✈️ Скопируйте ссылку ниже или нажмите «В Telegram»');
            }
        }

        async function copyShareUrlWithFallback(silentOpenModal = true) {
            triggerHaptic('light');
            const freshUrl = buildShareUrl();
            safeReplaceHash(`#card=${encodeCardStateToHash()}`);

            let copied = false;

            // 1. Современный Clipboard API
            try {
                if (navigator.clipboard && typeof navigator.clipboard.writeText === 'function') {
                    await navigator.clipboard.writeText(freshUrl);
                    copied = true;
                }
            } catch (_) {
                copied = false;
            }

            // 2. Fallback через скрытый textarea + execCommand('copy') для iframe и WebView
            if (!copied) {
                try {
                    const tempArea = document.createElement('textarea');
                    tempArea.value = freshUrl;
                    tempArea.setAttribute('readonly', '');
                    tempArea.style.position = 'fixed';
                    tempArea.style.top = '-9999px';
                    tempArea.style.opacity = '0';
                    document.body.appendChild(tempArea);
                    tempArea.focus();
                    tempArea.select();
                    tempArea.setSelectionRange(0, freshUrl.length);
                    copied = document.execCommand('copy');
                    document.body.removeChild(tempArea);
                } catch (_) {
                    copied = false;
                }
            }

            if (copied) {
                showToast('📋 Ссылка скопирована! Можно вставить в любой чат');
            } else if (silentOpenModal) {
                openShareModal();
                showToast('Выделите и скопируйте ссылку из поля вручную');
            }
        }

        /**
         * ============================================================================
         * 6. УВЕДОМЛЕНИЯ И МОДАЛЬНЫЕ ОКНА
         * ============================================================================
         */
        let toastTimer = null;
        function showToast(msg) {
            const toast = document.getElementById('toastNotification');
            const text = document.getElementById('toastText');
            if (!toast || !text) return;
            text.textContent = msg;
            toast.classList.remove('opacity-0', 'translate-y-3');
            toast.classList.add('opacity-100', 'translate-y-0');
            clearTimeout(toastTimer);
            toastTimer = setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-3');
                toast.classList.remove('opacity-100', 'translate-y-0');
            }, 2700);
        }

        function openProModal(customTitle) {
            triggerHaptic('light');
            const modal = document.getElementById('proModal');
            const titleEl = document.getElementById('proModalTitle');
            titleEl.textContent = customTitle || 'Откройте все эффекты и уберите водяной знак';
            modal.classList.remove('hidden');
        }

        function closeProModal() {
            document.getElementById('proModal').classList.add('hidden');
        }

        function activateProDemo(planType) {
            triggerHaptic('heavy');
            setProMode(true);
            closeProModal();
            playSoundEffect('chime');
            const label = planType === 'single'
                ? '👑 PRO открыт для этой открытки!'
                : planType === 'season'
                    ? '👑 Сезонный PRO-абонемент активирован!'
                    : '✨ Демо-режим PRO активирован! Все функции доступны.';
            showToast(label);
        }

        function openShareModal() {
            const modal = document.getElementById('shareModal');
            const notice = document.getElementById('sharePlanNotice');
            const input = document.getElementById('shareLinkInput');
            const shareUrl = buildShareUrl();
            input.value = shareUrl;

            if (state.isPro) {
                notice.className = 'mt-3 p-3 rounded-xl bg-emerald-500/10 border border-emerald-400/25 text-[11px] text-emerald-200 flex items-center justify-between gap-2';
                notice.innerHTML = '<span>👑 Открытка в режиме <strong>PRO</strong> — без бейджа и со всеми эффектами!</span>';
            } else {
                notice.className = 'mt-3 p-3 rounded-xl bg-amber-500/10 border border-amber-400/25 text-[11px] text-amber-200 flex items-center justify-between gap-2';
                notice.innerHTML = '<span>Сейчас открытка в режиме <strong>FREE</strong> (с нижним бейджем).</span><button type="button" id="btnNoticeUpgrade" class="underline font-bold text-amber-300 shrink-0">Убрать бейдж</button>';
                const upgradeBtn = document.getElementById('btnNoticeUpgrade');
                if (upgradeBtn) {
                    upgradeBtn.addEventListener('click', () => {
                        closeShareModal();
                        openProModal();
                    });
                }
            }

            modal.classList.remove('hidden');
            setTimeout(() => {
                input.focus();
                input.select();
            }, 50);
        }

        function closeShareModal() {
            document.getElementById('shareModal').classList.add('hidden');
        }

        /**
         * ============================================================================
         * 7. УПРАВЛЕНИЕ ТАРИФОМ (FREE VS PRO) И РЕЖИМОМ ПРОСМОТРА
         * ============================================================================
         */
        function setProMode(enabled) {
            state.isPro = enabled;
            safeStorageSet('elka_demo_pro', enabled ? '1' : '0');

            const btnFree = document.getElementById('btnModeFree');
            const btnPro = document.getElementById('btnModePro');
            const headerBadge = document.getElementById('headerPlanBadge');
            const freeBanner = document.getElementById('freeUpsellBanner');
            const proBanner = document.getElementById('proActiveBanner');
            const viralBadge = document.getElementById('viralFreeBadge');

            if (enabled) {
                btnFree.className = 'px-2 sm:px-2.5 py-1.5 rounded-lg text-xs font-semibold transition-all text-slate-400 hover:text-white';
                btnPro.className = 'px-2 sm:px-2.5 py-1.5 rounded-lg text-xs font-bold transition-all bg-gradient-to-r from-amber-400 to-orange-400 text-slate-950 shadow';
                headerBadge.textContent = 'PRO';
                headerBadge.className = 'text-[10px] sm:text-[11px] font-extrabold uppercase tracking-wider px-2 py-0.5 rounded-full bg-amber-400/20 text-amber-300 border border-amber-400/40';
                freeBanner.classList.add('hidden');
                proBanner.classList.remove('hidden');
                viralBadge.classList.add('hidden');
            } else {
                btnFree.className = 'px-2 sm:px-2.5 py-1.5 rounded-lg text-xs font-semibold transition-all bg-slate-800 text-white shadow';
                btnPro.className = 'px-2 sm:px-2.5 py-1.5 rounded-lg text-xs font-semibold transition-all text-amber-300/80 hover:text-amber-200 flex items-center gap-1';
                headerBadge.textContent = 'FREE';
                headerBadge.className = 'text-[10px] sm:text-[11px] font-bold uppercase tracking-wider px-2 py-0.5 rounded-full bg-slate-800 text-slate-300 border border-slate-700';
                freeBanner.classList.remove('hidden');
                proBanner.classList.add('hidden');
                viralBadge.classList.remove('hidden');

                const currentTheme = THEMES.find(t => t.id === state.themeId);
                if (currentTheme && currentTheme.pro) {
                    applyTheme('nordic');
                }
                state.effects.fireworks = false;
                state.effects.frost = false;
                state.effects.snow = true;

                state.stickers = state.stickers.filter(s => {
                    const meta = STICKER_TYPES.find(m => m.type === s.type);
                    return meta && !meta.pro;
                }).slice(0, FREE_STICKER_LIMIT);
            }

            renderAllControls();
            updateCardView();
        }

        function togglePreviewMode(forceValue) {
            state.isPreviewMode = typeof forceValue === 'boolean' ? forceValue : !state.isPreviewMode;

            const editorPanel = document.getElementById('editorPanel');
            const canvasColumn = document.getElementById('canvasColumn');
            const recipientBanner = document.getElementById('recipientModeBanner');
            const btnText = document.getElementById('previewBtnText');
            const btnIcon = document.getElementById('previewBtnIcon');
            const btnReply = document.getElementById('btnReplyCard');

            if (state.isPreviewMode) {
                editorPanel.classList.add('hidden');
                canvasColumn.classList.remove('lg:col-span-7', 'xl:col-span-7');
                canvasColumn.classList.add('lg:col-span-12');
                recipientBanner.classList.remove('hidden');
                btnText.textContent = 'Конструктор';
                btnIcon.textContent = '🛠️';
                btnReply.classList.remove('hidden');
            } else {
                editorPanel.classList.remove('hidden');
                canvasColumn.classList.add('lg:col-span-7', 'xl:col-span-7');
                canvasColumn.classList.remove('lg:col-span-12');
                recipientBanner.classList.add('hidden');
                btnText.textContent = 'Глазами получателя';
                btnIcon.textContent = '👁️';
                btnReply.classList.add('hidden');
            }

            renderStickersOnStage();
            setTimeout(() => {
                resizeAllCanvases(false);
            }, 50);
        }

        function resetCardToDefaults() {
            triggerHaptic('medium');
            const keepPro = state.isPro;
            state = createDefaultState();
            state.isPro = keepPro;
            safeReplaceHash('');

            document.getElementById('inputTo').value = state.to;
            document.getElementById('inputFrom').value = state.from;
            document.getElementById('inputMessage').value = state.message;
            document.getElementById('inputSecret').value = state.secretMessage;

            togglePreviewMode(false);
            setProMode(keepPro);
            applyTheme(state.themeId);
            renderAllControls();
            updateCardView();
            showToast('🔄 Открытка сброшена к начальному виду');
        }

        /**
         * ============================================================================
         * 8. РЕНДЕР ПАНЕЛИ УПРАВЛЕНИЯ (ТЕМЫ, ЭФФЕКТЫ, СТИКЕРЫ, ТЕКСТ)
         * ============================================================================
         */
        function renderThemesControl() {
            const grid = document.getElementById('themesGrid');
            grid.innerHTML = '';

            THEMES.forEach(theme => {
                const isSelected = state.themeId === theme.id;
                const isLocked = theme.pro && !state.isPro;

                const btn = document.createElement('button');
                btn.type = 'button';
                btn.className = `relative p-3 rounded-2xl border text-left transition-all flex items-center gap-3 ${
                    isSelected
                        ? 'bg-slate-800/95 border-amber-400 shadow-lg shadow-amber-500/10'
                        : 'bg-slate-950/70 hover:bg-slate-900 border-slate-800'
                }`;

                btn.innerHTML = `
                    <div class="w-11 h-11 rounded-xl shrink-0 shadow-inner border border-white/20" style="background: ${theme.previewBg}"></div>
                    <div class="min-w-0 flex-1">
                        <div class="flex items-center gap-1.5">
                            <span class="text-xs font-bold text-white truncate">${theme.name}</span>
                        </div>
                        <p class="text-[11px] text-slate-400 truncate mt-0.5">${theme.desc}</p>
                        <div class="mt-1">
                            ${theme.pro
                                ? `<span class="inline-flex items-center gap-1 text-[10px] font-bold px-1.5 py-0.5 rounded ${isLocked ? 'bg-amber-400/15 text-amber-300' : 'bg-emerald-500/20 text-emerald-300'}">${isLocked ? '🔒 PRO' : '👑 PRO'}</span>`
                                : `<span class="inline-block text-[10px] font-bold px-1.5 py-0.5 rounded bg-slate-800 text-slate-300">FREE</span>`
                            }
                        </div>
                    </div>
                `;

                btn.addEventListener('click', () => {
                    if (isLocked) {
                        openProModal(`Тема «${theme.name}» доступна в PRO-версии`);
                        return;
                    }
                    triggerHaptic('light');
                    applyTheme(theme.id);
                    renderThemesControl();
                });

                grid.appendChild(btn);
            });
        }

        function renderEffectsControl() {
            const list = document.getElementById('effectsList');
            list.innerHTML = '';

            EFFECTS.forEach(eff => {
                const isActive = !!state.effects[eff.id];
                const isLocked = eff.pro && !state.isPro;

                const row = document.createElement('div');
                row.className = `p-3 rounded-2xl border transition-all flex items-center justify-between gap-3 cursor-pointer ${
                    isActive
                        ? 'bg-slate-800/90 border-indigo-400/70'
                        : 'bg-slate-950/70 hover:bg-slate-900 border-slate-800'
                }`;

                row.innerHTML = `
                    <div class="flex items-center gap-3 min-w-0">
                        <div class="w-10 h-10 rounded-xl bg-slate-900 border border-slate-800 flex items-center justify-center text-lg shrink-0">
                            ${eff.icon}
                        </div>
                        <div class="min-w-0">
                            <div class="flex items-center gap-2">
                                <span class="text-xs font-bold text-white">${eff.name}</span>
                                ${eff.pro
                                    ? `<span class="text-[10px] font-bold px-1.5 py-0.5 rounded ${isLocked ? 'bg-amber-400/15 text-amber-300' : 'bg-emerald-500/20 text-emerald-300'}">${isLocked ? '🔒 PRO' : '👑 PRO'}</span>`
                                    : `<span class="text-[10px] font-bold px-1.5 py-0.5 rounded bg-slate-800 text-slate-300">FREE</span>`
                                }
                            </div>
                            <p class="text-[11px] text-slate-400 mt-0.5 leading-snug">${eff.desc}</p>
                        </div>
                    </div>
                    <div class="shrink-0">
                        <div class="w-11 h-6 rounded-full p-0.5 transition-colors ${isActive ? 'bg-indigo-500' : 'bg-slate-700'}">
                            <div class="w-5 h-5 rounded-full bg-white shadow transform transition-transform ${isActive ? 'translate-x-5' : 'translate-x-0'}"></div>
                        </div>
                    </div>
                `;

                row.addEventListener('click', () => {
                    if (isLocked) {
                        openProModal(`Эффект «${eff.name}» доступен в PRO-версии`);
                        return;
                    }
                    triggerHaptic('light');
                    state.effects[eff.id] = !state.effects[eff.id];
                    if (eff.id === 'frost' && state.effects.frost) {
                        updateCardView();
                        initFrostLayer();
                        showToast('❄️ Слой инея нанесён! Потрите центр открытки');
                    } else if (eff.id === 'fireworks' && state.effects.fireworks) {
                        const rect = document.getElementById('cardStage').getBoundingClientRect();
                        spawnFirework(rect.width * 0.5, rect.height * 0.35);
                        playSoundEffect('firework');
                        showToast('🎆 Нажимайте в любое место открытки для салюта!');
                    }
                    renderEffectsControl();
                    updateCardView();
                });

                list.appendChild(row);
            });

            const scratchControls = document.getElementById('scratchControls');
            if (state.effects.frost && state.isPro) {
                scratchControls.classList.remove('hidden');
            } else {
                scratchControls.classList.add('hidden');
            }
        }

        function renderStickersControl() {
            const freeGrid = document.getElementById('freeStickersGrid');
            const proGrid = document.getElementById('proStickersGrid');
            const counterBadge = document.getElementById('stickerCounterBadge');

            freeGrid.innerHTML = '';
            proGrid.innerHTML = '';

            if (state.isPro) {
                counterBadge.textContent = `На открытке: ${state.stickers.length} (Безлимит PRO)`;
                counterBadge.className = 'text-xs font-semibold px-2.5 py-0.5 rounded-full bg-amber-400/20 text-amber-300 border border-amber-400/30';
            } else {
                counterBadge.textContent = `На открытке: ${state.stickers.length} / ${FREE_STICKER_LIMIT} (FREE)`;
                counterBadge.className = 'text-xs font-semibold px-2.5 py-0.5 rounded-full bg-slate-800 text-slate-300';
            }

            STICKER_TYPES.forEach(st => {
                const isLocked = st.pro && !state.isPro;
                const btn = document.createElement('button');
                btn.type = 'button';
                btn.className = `relative p-2 rounded-2xl border text-center transition-all flex flex-col items-center justify-center gap-1 ${
                    isLocked
                        ? 'bg-slate-950/60 border-slate-800/80 opacity-85 hover:border-amber-400/40'
                        : 'bg-slate-950/90 hover:bg-slate-800 border-slate-800 active:scale-95'
                }`;

                btn.innerHTML = `
                    ${isLocked ? '<span class="absolute top-1.5 right-1.5 text-[10px]">🔒</span>' : ''}
                    <span class="text-2xl leading-none">${st.emoji}</span>
                    <span class="text-[11px] font-bold text-white leading-tight">${st.label}</span>
                    <span class="text-[9px] text-slate-400 leading-tight">${st.actionHint}</span>
                `;

                btn.addEventListener('click', () => {
                    if (isLocked) {
                        openProModal(`Стикер «${st.label} ${st.emoji}» доступен в PRO`);
                        return;
                    }
                    if (!state.isPro && state.stickers.length >= FREE_STICKER_LIMIT) {
                        openProModal(`В FREE-версии можно добавить до ${FREE_STICKER_LIMIT} стикеров`);
                        return;
                    }
                    triggerHaptic('light');
                    addStickerToCard(st.type);
                });

                if (st.pro) {
                    proGrid.appendChild(btn);
                } else {
                    freeGrid.appendChild(btn);
                }
            });
        }

        function renderTemplatesControl() {
            const list = document.getElementById('templatesList');
            list.innerHTML = '';

            TEXT_TEMPLATES.forEach(tpl => {
                const isLocked = tpl.pro && !state.isPro;
                const isSelected = state.message === tpl.text;

                const btn = document.createElement('button');
                btn.type = 'button';
                btn.className = `w-full p-2.5 rounded-xl border text-left transition-all ${
                    isSelected
                        ? 'bg-slate-800 border-amber-400/70'
                        : 'bg-slate-950/80 hover:bg-slate-900 border-slate-800'
                }`;

                btn.innerHTML = `
                    <div class="flex items-center justify-between gap-2 mb-1">
                        <span class="text-[10px] font-bold uppercase tracking-wider ${tpl.pro ? 'text-amber-300' : 'text-emerald-400'}">
                            ${isLocked ? '🔒 ' : ''}${tpl.category}
                        </span>
                    </div>
                    <p class="text-xs text-slate-200 line-clamp-2 leading-snug">${tpl.text}</p>
                `;

                btn.addEventListener('click', () => {
                    if (isLocked) {
                        openProModal('Расширенная библиотека поздравлений доступна в PRO');
                        return;
                    }
                    triggerHaptic('light');
                    state.message = tpl.text;
                    document.getElementById('inputMessage').value = tpl.text;
                    renderTemplatesControl();
                    updateCardView();
                });

                list.appendChild(btn);
            });
        }

        function renderAllControls() {
            renderThemesControl();
            renderEffectsControl();
            renderStickersControl();
            renderTemplatesControl();
        }

        /**
         * ============================================================================
         * 9. ОБНОВЛЕНИЕ СЦЕНЫ ОТКРЫТКИ И СТИКЕРОВ
         * ============================================================================
         */
        function applyTheme(themeId) {
            state.themeId = themeId;
            const theme = THEMES.find(t => t.id === themeId) || THEMES[0];

            const stage = document.getElementById('cardStage');
            const title = document.getElementById('cardTitle');
            const innerFrame = document.getElementById('cardInnerFrame');
            const garland = document.getElementById('garlandContainer');

            THEMES.forEach(t => {
                stage.classList.remove(t.cardClass);
                title.classList.remove(t.fontClass);
            });
            stage.classList.add(theme.cardClass);
            title.classList.add(theme.fontClass);

            innerFrame.className = 'pointer-events-none absolute inset-3 rounded-[1.6rem] z-10 transition-all duration-500';
            if (theme.id === 'artdeco') {
                innerFrame.classList.add('artdeco-border');
            } else if (theme.id === 'retro') {
                innerFrame.classList.add('retro-border');
            }

            garland.innerHTML = '';
            const count = 11;
            for (let i = 0; i < count; i++) {
                const color = theme.bulbColors[i % theme.bulbColors.length];
                const offset = Math.sin((i / (count - 1)) * Math.PI) * 10;
                const bulb = document.createElement('div');
                bulb.className = 'garland-bulb w-2.5 h-3.5 rounded-full';
                bulb.style.backgroundColor = color;
                bulb.style.boxShadow = `0 0 10px ${color}, 0 0 18px ${color}`;
                bulb.style.transform = `translateY(${offset}px)`;
                bulb.style.animationDelay = `${(i * 0.18).toFixed(2)}s`;
                garland.appendChild(bulb);
            }
        }

        function updateCardView() {
            const toBadge = document.getElementById('cardToBadge');
            const bodyText = document.getElementById('cardBodyText');
            const fromText = document.getElementById('cardFromText');
            const secretBox = document.getElementById('secretScratchBox');
            const secretText = document.getElementById('cardSecretText');
            const hintPill = document.getElementById('interactiveHintPill');

            toBadge.textContent = state.to.trim() ? `Для: ${state.to.trim()} ✨` : 'С праздником! ✨';
            bodyText.textContent = state.message.trim() || 'С Новым годом и волшебным настроением!';
            fromText.textContent = state.from.trim() ? `С теплом, ${state.from.trim()} 🎄` : '';
            secretText.textContent = state.secretMessage.trim() || 'Пусть этот год станет самым счастливым!';

            if (state.effects.frost && state.isPro) {
                secretBox.classList.remove('hidden');
            } else {
                secretBox.classList.add('hidden');
            }

            if (state.effects.frost && state.isPro) {
                hintPill.textContent = '❄️ Сотри иней в центре или нажимай на стикеры!';
            } else if (state.effects.fireworks && state.isPro) {
                hintPill.textContent = '🎆 Тапай по экрану для салюта и по стикерам!';
            } else {
                hintPill.textContent = '👆 Нажимай на стикеры или перетаскивай их!';
            }

            renderStickersOnStage();
        }

        function addStickerToCard(type) {
            const meta = STICKER_TYPES.find(s => s.type === type);
            if (!meta) return;

            const randomX = 0.22 + Math.random() * 0.56;
            const randomY = 0.22 + Math.random() * 0.58;

            state.stickers.push({
                id: 'st_' + Date.now() + '_' + Math.floor(Math.random() * 999),
                type: meta.type,
                emoji: meta.emoji,
                x: randomX,
                y: randomY,
                scale: 1.1,
                faceIdx: 0
            });

            playSoundEffect('pop');
            renderStickersControl();
            renderStickersOnStage();
        }

        function triggerStickerAction(stickerObj, el) {
            const meta = STICKER_TYPES.find(m => m.type === stickerObj.type);
            if (!meta) return;

            triggerHaptic('medium');

            const stage = document.getElementById('cardStage');
            const stageRect = stage.getBoundingClientRect();
            const px = stickerObj.x * stageRect.width;
            const py = stickerObj.y * stageRect.height;

            const innerEmoji = el.querySelector('.sticker-emoji');
            innerEmoji.classList.remove(meta.animClass);
            void innerEmoji.offsetWidth;
            innerEmoji.classList.add(meta.animClass);

            if (stickerObj.type === 'tree' || stickerObj.type === 'clock') {
                playSoundEffect('tree');
                spawnBurstParticles(px, py, ['#fde047', '#4ade80', '#f87171', '#38bdf8'], 20, 'spark');
            } else if (stickerObj.type === 'snowflake') {
                playSoundEffect('chime');
                spawnBurstParticles(px, py, ['#e0f2fe', '#7dd3fc', '#ffffff'], 18, 'snow');
            } else if (stickerObj.type === 'gift' || stickerObj.type === 'deer') {
                playSoundEffect('gift');
                spawnBurstParticles(px, py, ['#f43f5e', '#facc15', '#38bdf8', '#a855f7'], 28, 'confetti');
            } else if (stickerObj.type === 'glasses') {
                playSoundEffect('glasses');
                spawnBurstParticles(px, py, ['#fef08a', '#fde047', '#ffffff'], 22, 'spark');
            } else if (stickerObj.type === 'star' || stickerObj.type === 'trophy') {
                playSoundEffect('star');
                spawnFirework(px, py - 20);
            } else if (stickerObj.type === 'tangerine') {
                playSoundEffect('tangerine');
                spawnBurstParticles(px, py, ['#fb923c', '#f97316', '#fde047', '#4ade80'], 30, 'confetti');
            } else if (stickerObj.type === 'snowman') {
                playSoundEffect('pop');
                stickerObj.faceIdx = ((stickerObj.faceIdx || 0) + 1) % SNOWMAN_FACES.length;
                stickerObj.emoji = SNOWMAN_FACES[stickerObj.faceIdx];
                innerEmoji.textContent = stickerObj.emoji;
                spawnBurstParticles(px, py, ['#ffffff', '#bae6fd'], 14, 'snow');
            } else if (stickerObj.type === 'orb') {
                playSoundEffect('orb');
                spawnBurstParticles(px, py, ['#e879f9', '#c084fc', '#38bdf8'], 26, 'spark');
                showPredictionBubble();
            }
        }

        function showPredictionBubble() {
            const container = document.getElementById('predictionContainer');
            const randomText = PREDICTIONS[Math.floor(Math.random() * PREDICTIONS.length)];

            container.innerHTML = `
                <div class="prediction-bubble absolute left-1/2 bottom-0 w-[86%] max-w-[310px] p-3 rounded-2xl bg-slate-950/90 text-white border border-purple-400/50 shadow-2xl text-xs font-semibold text-center leading-snug backdrop-blur-md">
                    ${randomText}
                </div>
            `;
        }

        function renderStickersOnStage() {
            const layer = document.getElementById('stickersLayer');
            layer.innerHTML = '';

            state.stickers.forEach(st => {
                const wrapper = document.createElement('div');
                wrapper.className = 'sticker-item pointer-events-auto absolute -translate-x-1/2 -translate-y-1/2 cursor-grab active:cursor-grabbing group';
                wrapper.style.left = `${st.x * 100}%`;
                wrapper.style.top = `${st.y * 100}%`;

                wrapper.innerHTML = `
                    <div class="relative flex items-center justify-center p-1.5">
                        <span class="sticker-emoji text-4xl sm:text-[42px] leading-none drop-shadow-md inline-block">${st.emoji}</span>
                        ${!state.isPreviewMode ? `
                            <button type="button" class="sticker-delete opacity-80 sm:opacity-0 scale-90 sm:scale-75 transition-all absolute -top-1.5 -right-1.5 w-5 h-5 rounded-full bg-rose-500 text-white text-[10px] font-bold flex items-center justify-center shadow-md hover:bg-rose-400" title="Удалить стикер">
                                ✕
                            </button>
                        ` : ''}
                    </div>
                `;

                const delBtn = wrapper.querySelector('.sticker-delete');
                if (delBtn) {
                    delBtn.addEventListener('pointerdown', (e) => {
                        e.stopPropagation();
                    });
                    delBtn.addEventListener('click', (e) => {
                        e.stopPropagation();
                        triggerHaptic('light');
                        state.stickers = state.stickers.filter(item => item.id !== st.id);
                        renderStickersControl();
                        renderStickersOnStage();
                    });
                }

                let isDragging = false;
                let movedDistance = 0;
                let startClientX = 0;
                let startClientY = 0;
                let startNormX = st.x;
                let startNormY = st.y;

                wrapper.addEventListener('pointerdown', (e) => {
                    e.stopPropagation();
                    isDragging = true;
                    movedDistance = 0;
                    startClientX = e.clientX;
                    startClientY = e.clientY;
                    startNormX = st.x;
                    startNormY = st.y;
                    try {
                        wrapper.setPointerCapture(e.pointerId);
                    } catch (_) {}
                });

                wrapper.addEventListener('pointermove', (e) => {
                    if (!isDragging) return;
                    const dx = e.clientX - startClientX;
                    const dy = e.clientY - startClientY;
                    movedDistance = Math.hypot(dx, dy);

                    if (movedDistance > 4) {
                        const stageRect = document.getElementById('cardStage').getBoundingClientRect();
                        const newX = Math.min(0.92, Math.max(0.08, startNormX + dx / stageRect.width));
                        const newY = Math.min(0.88, Math.max(0.10, startNormY + dy / stageRect.height));
                        st.x = newX;
                        st.y = newY;
                        wrapper.style.left = `${newX * 100}%`;
                        wrapper.style.top = `${newY * 100}%`;
                    }
                });

                const finishPointer = (e) => {
                    if (!isDragging) return;
                    isDragging = false;
                    try {
                        wrapper.releasePointerCapture(e.pointerId);
                    } catch (_) {}

                    if (movedDistance <= 6) {
                        triggerStickerAction(st, wrapper);
                    }
                };

                wrapper.addEventListener('pointerup', finishPointer);
                wrapper.addEventListener('pointercancel', finishPointer);

                layer.appendChild(wrapper);
            });
        }

        /**
         * ============================================================================
         * 10. CANVAS-ДВИЖОК: СНЕГОПАД, БОКЕ, САЛЮТЫ, КОНФЕТТИ И СКРЕТЧ-ИНЕЙ
         * ============================================================================
         */
        const bgCanvas = document.getElementById('bgEffectsCanvas');
        const bgCtx = bgCanvas.getContext('2d');

        const fxCanvas = document.getElementById('fxOverlayCanvas');
        const fxCtx = fxCanvas.getContext('2d');

        const frostCanvas = document.getElementById('frostCanvas');
        const frostCtx = frostCanvas.getContext('2d');

        let snowflakes = [];
        let bokehLights = [];
        let activeParticles = [];

        function initBackgroundParticles(width, height) {
            snowflakes = Array.from({ length: 55 }, () => ({
                x: Math.random() * width,
                y: Math.random() * height,
                r: 1.5 + Math.random() * 2.8,
                vy: 0.45 + Math.random() * 1.1,
                vx: (Math.random() - 0.5) * 0.4,
                phase: Math.random() * Math.PI * 2
            }));

            const bokehColors = [
                'rgba(253, 224, 71, 0.25)',
                'rgba(56, 189, 248, 0.22)',
                'rgba(244, 114, 182, 0.22)',
                'rgba(167, 139, 250, 0.24)',
                'rgba(255, 255, 255, 0.20)'
            ];
            bokehLights = Array.from({ length: 20 }, (_, i) => ({
                x: Math.random() * width,
                y: Math.random() * height,
                r: 18 + Math.random() * 34,
                vx: (Math.random() - 0.5) * 0.25,
                vy: -0.15 - Math.random() * 0.25,
                color: bokehColors[i % bokehColors.length],
                pulse: Math.random() * Math.PI * 2
            }));
        }

        function initFrostLayer() {
            const box = document.getElementById('secretScratchBox');
            if (!box || box.classList.contains('hidden')) return;

            const rect = frostCanvas.getBoundingClientRect();
            const dpr = Math.min(window.devicePixelRatio || 1, 2);
            frostCanvas.width = Math.max(1, Math.floor(rect.width * dpr));
            frostCanvas.height = Math.max(1, Math.floor(rect.height * dpr));

            frostCtx.setTransform(dpr, 0, 0, dpr, 0, 0);
            frostCtx.globalCompositeOperation = 'source-over';

            const grad = frostCtx.createLinearGradient(0, 0, rect.width, rect.height);
            grad.addColorStop(0, '#bae6fd');
            grad.addColorStop(0.5, '#e0f2fe');
            grad.addColorStop(1, '#93c5fd');
            frostCtx.fillStyle = grad;
            frostCtx.fillRect(0, 0, rect.width, rect.height);

            frostCtx.fillStyle = 'rgba(255, 255, 255, 0.55)';
            for (let i = 0; i < 90; i++) {
                const sx = Math.random() * rect.width;
                const sy = Math.random() * rect.height;
                const sr = 1 + Math.random() * 2.5;
                frostCtx.beginPath();
                frostCtx.arc(sx, sy, sr, 0, Math.PI * 2);
                frostCtx.fill();
            }

            frostCtx.fillStyle = '#0c4a6e';
            frostCtx.font = '700 12px Inter, sans-serif';
            frostCtx.textAlign = 'center';
            frostCtx.textBaseline = 'middle';
            frostCtx.fillText('❄️ Сотри иней пальцем ❄️', rect.width / 2, rect.height / 2);
        }

        let isScratching = false;
        function scratchAt(clientX, clientY) {
            const rect = frostCanvas.getBoundingClientRect();
            const x = clientX - rect.left;
            const y = clientY - rect.top;

            frostCtx.globalCompositeOperation = 'destination-out';
            frostCtx.beginPath();
            frostCtx.arc(x, y, 18, 0, Math.PI * 2);
            frostCtx.fill();
        }

        frostCanvas.addEventListener('pointerdown', (e) => {
            e.stopPropagation();
            isScratching = true;
            try {
                frostCanvas.setPointerCapture(e.pointerId);
            } catch (_) {}
            scratchAt(e.clientX, e.clientY);
        });

        frostCanvas.addEventListener('pointermove', (e) => {
            if (!isScratching) return;
            e.stopPropagation();
            scratchAt(e.clientX, e.clientY);
        });

        const endScratch = (e) => {
            isScratching = false;
            try {
                frostCanvas.releasePointerCapture(e.pointerId);
            } catch (_) {}
        };
        frostCanvas.addEventListener('pointerup', endScratch);
        frostCanvas.addEventListener('pointercancel', endScratch);

        function spawnBurstParticles(x, y, colors, count = 24, mode = 'spark') {
            for (let i = 0; i < count; i++) {
                const angle = (Math.PI * 2 * i) / count + (Math.random() - 0.5) * 0.35;
                const speed = mode === 'confetti'
                    ? 1.8 + Math.random() * 4.2
                    : 1.5 + Math.random() * 4.8;
                activeParticles.push({
                    x,
                    y,
                    vx: Math.cos(angle) * speed,
                    vy: Math.sin(angle) * speed - (mode === 'confetti' ? 1.5 : 0),
                    gravity: mode === 'snow' ? 0.03 : 0.11,
                    drag: 0.96,
                    alpha: 1,
                    decay: 0.016 + Math.random() * 0.015,
                    size: mode === 'confetti' ? 4 + Math.random() * 4 : 2 + Math.random() * 2.5,
                    color: colors[Math.floor(Math.random() * colors.length)],
                    mode,
                    rotation: Math.random() * Math.PI * 2,
                    vRot: (Math.random() - 0.5) * 0.25
                });
            }
        }

        function spawnFirework(x, y) {
            const palettes = [
                ['#fde047', '#fb923c', '#f43f5e', '#ffffff'],
                ['#22d3ee', '#e879f9', '#a855f7', '#fef08a'],
                ['#4ade80', '#38bdf8', '#facc15', '#ffffff']
            ];
            const palette = palettes[Math.floor(Math.random() * palettes.length)];
            spawnBurstParticles(x, y, palette, 38, 'spark');
        }

        function resizeAllCanvases(reinitParticles = true) {
            const stage = document.getElementById('cardStage');
            if (!stage) return;
            const rect = stage.getBoundingClientRect();
            const dpr = Math.min(window.devicePixelRatio || 1, 2);

            [bgCanvas, fxCanvas].forEach(c => {
                c.width = Math.max(1, Math.floor(rect.width * dpr));
                c.height = Math.max(1, Math.floor(rect.height * dpr));
            });

            bgCtx.setTransform(dpr, 0, 0, dpr, 0, 0);
            fxCtx.setTransform(dpr, 0, 0, dpr, 0, 0);

            if (reinitParticles || snowflakes.length === 0) {
                initBackgroundParticles(rect.width, rect.height);
            }
            if (state.effects.frost && state.isPro) {
                initFrostLayer();
            }
        }

        function animationLoop() {
            const stage = document.getElementById('cardStage');
            if (stage) {
                const w = stage.clientWidth;
                const h = stage.clientHeight;

                bgCtx.clearRect(0, 0, w, h);

                if (state.effects.bokeh) {
                    const activeLights = state.isPro ? bokehLights : bokehLights.slice(0, 8);
                    activeLights.forEach(b => {
                        b.x += b.vx;
                        b.y += b.vy;
                        b.pulse += 0.03;

                        if (b.y < -b.r) b.y = h + b.r;
                        if (b.x < -b.r) b.x = w + b.r;
                        if (b.x > w + b.r) b.x = -b.r;

                        const currentR = b.r * (0.9 + Math.sin(b.pulse) * 0.14);
                        const grad = bgCtx.createRadialGradient(b.x, b.y, 0, b.x, b.y, currentR);
                        grad.addColorStop(0, b.color);
                        grad.addColorStop(1, 'rgba(255, 255, 255, 0)');

                        bgCtx.fillStyle = grad;
                        bgCtx.beginPath();
                        bgCtx.arc(b.x, b.y, currentR, 0, Math.PI * 2);
                        bgCtx.fill();
                    });
                }

                if (state.effects.snow) {
                    bgCtx.fillStyle = state.themeId === 'nordic' || state.themeId === 'retro'
                        ? 'rgba(255, 255, 255, 0.85)'
                        : 'rgba(224, 242, 254, 0.85)';

                    snowflakes.forEach(f => {
                        f.phase += 0.025;
                        f.y += f.vy;
                        f.x += f.vx + Math.sin(f.phase) * 0.35;

                        if (f.y > h + 6) {
                            f.y = -6;
                            f.x = Math.random() * w;
                        }
                        if (f.x > w + 6) f.x = -6;
                        if (f.x < -6) f.x = w + 6;

                        bgCtx.beginPath();
                        bgCtx.arc(f.x, f.y, f.r, 0, Math.PI * 2);
                        bgCtx.fill();
                    });
                }

                fxCtx.clearRect(0, 0, w, h);
                for (let i = activeParticles.length - 1; i >= 0; i--) {
                    const p = activeParticles[i];
                    p.vx *= p.drag;
                    p.vy = p.vy * p.drag + p.gravity;
                    p.x += p.vx;
                    p.y += p.vy;
                    p.alpha -= p.decay;
                    p.rotation += p.vRot;

                    if (p.alpha <= 0) {
                        activeParticles.splice(i, 1);
                        continue;
                    }

                    fxCtx.save();
                    fxCtx.globalAlpha = Math.max(0, p.alpha);
                    fxCtx.fillStyle = p.color;

                    if (p.mode === 'confetti') {
                        fxCtx.translate(p.x, p.y);
                        fxCtx.rotate(p.rotation);
                        fxCtx.fillRect(-p.size / 2, -p.size / 2, p.size, p.size * 0.6);
                    } else {
                        fxCtx.beginPath();
                        fxCtx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                        fxCtx.fill();
                    }
                    fxCtx.restore();
                }
            }

            requestAnimationFrame(animationLoop);
        }

        /**
         * ============================================================================
         * 11. ЕДИНАЯ НАДЁЖНАЯ ИНИЦИАЛИЗАЦИЯ ВСЕХ СЛУШАТЕЛЕЙ И ЗАПУСК
         * ============================================================================
         */
        let appInitialized = false;

        function initApp() {
            if (appInitialized) return;
            appInitialized = true;

            // Отображаем бейдж Telegram Mini App, если открыто внутри Telegram
            if (tgWebApp && tgWebApp.initDataUnsafe) {
                const tgHint = document.getElementById('tgContextHint');
                if (tgHint) {
                    tgHint.textContent = 'Telegram Mini App · Готово к отправке в чаты';
                }
            }

            // Вкладки редактора
            document.querySelectorAll('.tab-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    triggerHaptic('light');
                    const target = btn.dataset.tab;
                    state.activeTab = target;

                    document.querySelectorAll('.tab-btn').forEach(b => {
                        b.className = 'tab-btn py-2.5 px-2 rounded-xl text-xs font-semibold flex flex-col sm:flex-row items-center justify-center gap-1 text-slate-400 hover:text-slate-200 transition-all';
                    });
                    btn.className = 'tab-btn py-2.5 px-2 rounded-xl text-xs font-semibold flex flex-col sm:flex-row items-center justify-center gap-1 bg-slate-800 text-white shadow transition-all';

                    document.querySelectorAll('.tab-content').forEach(c => c.classList.add('hidden'));
                    const targetContent = document.getElementById(`tab-${target}`);
                    if (targetContent) targetContent.classList.remove('hidden');
                });
            });

            // Кнопки переключения FREE / PRO
            document.getElementById('btnModeFree').addEventListener('click', () => {
                triggerHaptic('light');
                setProMode(false);
                showToast('Переключено на демо-тариф FREE');
            });

            document.getElementById('btnModePro').addEventListener('click', () => {
                triggerHaptic('medium');
                setProMode(true);
                playSoundEffect('chime');
                showToast('👑 Переключено на демо-тариф PRO');
            });

            document.getElementById('btnBannerUnlock').addEventListener('click', () => openProModal());
            document.getElementById('viralFreeBadge').addEventListener('click', () => openProModal());
            document.getElementById('btnCloseProModal').addEventListener('click', closeProModal);

            document.querySelectorAll('.pro-plan-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    activateProDemo(btn.dataset.proPlan || 'demo');
                });
            });

            // Кнопка «Сбросить»
            document.getElementById('btnResetAll').addEventListener('click', resetCardToDefaults);

            // Кнопка звука
            document.getElementById('btnToggleSound').addEventListener('click', () => {
                state.soundEnabled = !state.soundEnabled;
                document.getElementById('soundIcon').textContent = state.soundEnabled ? '🔊' : '🔇';
                if (state.soundEnabled) {
                    playSoundEffect('pop');
                    showToast('🔊 Звуковые эффекты включены');
                } else {
                    showToast('🔇 Звук выключен');
                }
            });

            // Предпросмотр глазами получателя
            document.getElementById('btnTogglePreview').addEventListener('click', () => {
                triggerHaptic('light');
                togglePreviewMode();
            });
            document.getElementById('btnBackToBuilder').addEventListener('click', () => {
                triggerHaptic('light');
                togglePreviewMode(false);
            });

            // Клик по самой открытке (запускает салют в PRO или лёгкие искры в FREE)
            document.getElementById('cardStage').addEventListener('pointerdown', (e) => {
                const rect = e.currentTarget.getBoundingClientRect();
                const x = e.clientX - rect.left;
                const y = e.clientY - rect.top;

                if (state.effects.fireworks && state.isPro) {
                    triggerHaptic('medium');
                    spawnFirework(x, y);
                    playSoundEffect('firework');
                } else {
                    spawnBurstParticles(x, y, ['#fde047', '#ffffff', '#7dd3fc'], 10, 'spark');
                }
            });

            // Кнопка восстановления инея
            document.getElementById('btnResetFrost').addEventListener('click', () => {
                triggerHaptic('light');
                initFrostLayer();
                showToast('❄️ Иней восстановлен');
            });

            // Управление стикерами
            document.getElementById('btnClearStickers').addEventListener('click', () => {
                triggerHaptic('light');
                state.stickers = [];
                renderStickersControl();
                renderStickersOnStage();
            });

            document.getElementById('btnResetStickers').addEventListener('click', () => {
                triggerHaptic('light');
                state.stickers = [
                    { id: 'st_1', type: 'tree', x: 0.20, y: 0.76, scale: 1.15, emoji: '🎄', faceIdx: 0 },
                    { id: 'st_2', type: 'gift', x: 0.78, y: 0.77, scale: 1.05, emoji: '🎁', faceIdx: 0 }
                ];
                renderStickersControl();
                renderStickersOnStage();
            });

            // Поля ввода текста
            const inputTo = document.getElementById('inputTo');
            const inputFrom = document.getElementById('inputFrom');
            const inputMessage = document.getElementById('inputMessage');
            const inputSecret = document.getElementById('inputSecret');

            inputTo.addEventListener('input', (e) => {
                state.to = e.target.value;
                updateCardView();
            });

            inputFrom.addEventListener('input', (e) => {
                state.from = e.target.value;
                updateCardView();
            });

            inputMessage.addEventListener('input', (e) => {
                state.message = e.target.value;
                renderTemplatesControl();
                updateCardView();
            });

            inputSecret.addEventListener('focus', (e) => {
                if (!state.isPro) {
                    e.target.blur();
                    openProModal('Секретное послание под инеем доступно в PRO');
                }
            });

            inputSecret.addEventListener('input', (e) => {
                state.secretMessage = e.target.value;
                updateCardView();
            });

            // Кнопки отправки в Telegram, копирования ссылки и ответной открытки
            document.getElementById('btnSendTelegram').addEventListener('click', shareToTelegram);
            document.getElementById('btnQuickCopyUrl').addEventListener('click', () => copyShareUrlWithFallback(true));
            document.getElementById('btnCloseShareModal').addEventListener('click', closeShareModal);
            document.getElementById('btnModalCopy').addEventListener('click', () => copyShareUrlWithFallback(false));
            document.getElementById('btnModalTgShare').addEventListener('click', () => {
                closeShareModal();
                shareToTelegram();
            });
            document.getElementById('btnModalPreview').addEventListener('click', () => {
                closeShareModal();
                togglePreviewMode(true);
            });

            document.getElementById('btnReplyCard').addEventListener('click', () => {
                triggerHaptic('medium');
                const prevTo = state.to;
                const prevFrom = state.from;
                state.to = prevFrom || 'Друг';
                state.from = prevTo || '';
                state.message = '';
                state.secretMessage = '';
                inputTo.value = state.to;
                inputFrom.value = state.from;
                inputMessage.value = '';
                inputSecret.value = '';
                safeReplaceHash('');
                togglePreviewMode(false);
                renderTemplatesControl();
                updateCardView();
                showToast('💌 Имена поменялись местами! Напишите ответное поздравление');
            });

            // Проверяем, открыта ли страница по персональной ссылке с #card=...
            const loadedFromUrl = decodeCardStateFromUrl();
            if (!loadedFromUrl && safeStorageGet('elka_demo_pro') === '1') {
                state.isPro = true;
            }

            // Синхронизируем поля ввода с актуальным состоянием
            inputTo.value = state.to;
            inputFrom.value = state.from;
            inputMessage.value = state.message;
            inputSecret.value = state.secretMessage;

            // Первичная отрисовка
            setProMode(state.isPro);
            applyTheme(state.themeId);
            renderAllControls();
            updateCardView();

            if (loadedFromUrl) {
                togglePreviewMode(true);
            }

            resizeAllCanvases(true);
            animationLoop();
        }

        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', initApp);
        } else {
            initApp();
        }

        window.addEventListener('resize', () => {
            resizeAllCanvases(false);
        });
    </script>
</body>
</html>
