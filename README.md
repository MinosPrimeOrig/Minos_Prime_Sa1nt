# Minos_Prime_Sa1nt
game code snake in browser BETA
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Змейка 🐍 - Глобальные рекорды</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            text-align: center;
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #1a2a6c);
            margin: 0;
            padding: 10px;
            min-height: 100vh;
            color: white;
            overflow-x: hidden;
            touch-action: manipulation;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background: rgba(0, 0, 0, 0.7);
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
        }
        h1 {
            font-size: 2.5rem;
            margin-bottom: 20px;
            text-shadow: 0 0 10px #00ff00, 0 0 20px #00ff00;
            animation: glow 1.5s infinite alternate;
        }
        @keyframes glow {
            from { text-shadow: 0 0 10px #00ff00, 0 0 20px #00ff00; }
            to { text-shadow: 0 0 15px #00ff00, 0 0 30px #00ff00, 0 0 40px #00ff00; }
        }
        #game {
            background: #111;
            display: block;
            margin: 10px auto;
            border: 3px solid #333;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.8);
        }
        #score {
            font-size: 1.8rem;
            margin: 15px 0;
            color: #ffcc00;
            font-weight: bold;
            text-shadow: 0 0 5px rgba(255, 204, 0, 0.8);
        }
        .controls {
            margin: 20px auto;
            display: grid;
            grid-template-areas:
                ". up ."
                "left down right";
            gap: 15px;
            width: 260px;
        }
        .btn {
            background: linear-gradient(to bottom, #4CAF50, #2E7D32);
            color: white;
            border: none;
            border-radius: 50%;
            width: 80px;
            height: 80px;
            font-size: 2rem;
            cursor: pointer;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .btn:active {
            transform: scale(0.95);
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
        }
        #up { grid-area: up; }
        #down { grid-area: down; }
        #left { grid-area: left; }
        #right { grid-area: right; }

        /* Меню и настройки */
        #menu, #settings, #records, #globalRecords {
            margin-top: 20px;
            animation: fadeIn 0.5s ease-out;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .menu-btn {
            background: linear-gradient(to bottom, #FF5722, #E64A19);
            color: white;
            border: none;
            border-radius: 50px;
            padding: 15px 30px;
            font-size: 1.2rem;
            margin: 15px auto;
            cursor: pointer;
            width: 80%;
            max-width: 300px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
            transition: all 0.3s;
            display: block;
        }
        .menu-btn:active {
            transform: translateY(1px);
        }
        .screen {
            display: none;
        }
        #menu {
            display: block;
        }
        .setting-option {
            margin: 20px 0;
            padding: 10px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
        }
        select, input {
            padding: 10px;
            border-radius: 10px;
            border: 2px solid #4CAF50;
            background: #333;
            color: white;
            font-size: 1rem;
            width: 100%;
            max-width: 300px;
        }
        label {
            font-size: 1.1rem;
            display: block;
            margin-bottom: 8px;
            color: #4CAF50;
        }
        .toggle-container {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 15px;
        }
        .toggle-btn {
            display: inline-block;
            width: 70px;
            height: 35px;
            background: #555;
            border-radius: 35px;
            position: relative;
            cursor: pointer;
            transition: background 0.3s;
        }
        .toggle-btn:after {
            content: "";
            position: absolute;
            width: 31px;
            height: 31px;
            background: white;
            border-radius: 50%;
            top: 2px;
            left: 2px;
            transition: 0.3s;
        }
        .toggle-btn.active {
            background: #4CAF50;
        }
        .toggle-btn.active:after {
            left: 37px;
        }
        .skin-options {
            display: flex;
            flex-wrap: wrap;
            gap: 15px;
            justify-content: center;
            margin-top: 15px;
        }
        .skin-option {
            width: 60px;
            height: 60px;
            border-radius: 10px;
            cursor: pointer;
            border: 2px solid transparent;
            transition: all 0.3s;
        }
        .skin-option.selected {
            border-color: #ffcc00;
            transform: scale(1.1);
            box-shadow: 0 0 15px #ffcc00;
        }
        .particle {
            position: absolute;
            pointer-events: none;
            border-radius: 50%;
            opacity: 0.7;
        }
        
        /* Экран паузы */
        #pauseScreen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            display: none;
            z-index: 10;
        }
        #pauseText {
            font-size: 3rem;
            color: #ffcc00;
            margin-bottom: 20px;
            text-shadow: 0 0 10px rgba(255, 204, 0, 0.8);
        }
        
        /* Таблица рекордов */
        .records-container {
            display: flex;
            justify-content: space-around;
            flex-wrap: wrap;
            gap: 20px;
            margin-top: 20px;
        }
        .records-table {
            flex: 1;
            min-width: 300px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            padding: 15px;
        }
        .records-table h3 {
            margin-top: 0;
            color: #4CAF50;
            border-bottom: 2px solid #4CAF50;
            padding-bottom: 10px;
        }
        .record-item {
            display: flex;
            justify-content: space-between;
            padding: 8px 5px;
            border-bottom: 1px solid #444;
        }
        .record-item:nth-child(even) {
            background: rgba(255, 255, 255, 0.05);
        }
        .record-name {
            font-weight: bold;
            color: #ffcc00;
        }
        .record-score {
            font-weight: bold;
        }
        .record-rank {
            display: inline-block;
            width: 25px;
            text-align: center;
            margin-right: 10px;
            font-weight: bold;
            color: #4CAF50;
        }
        
        /* Форма имени */
        #nameForm {
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border-radius: 10px;
            max-width: 400px;
            margin: 20px auto;
        }
        #nameInput {
            width: 100%;
            padding: 12px;
            font-size: 1.1rem;
            margin: 10px 0;
            border-radius: 8px;
            border: 2px solid #4CAF50;
            background: #222;
            color: white;
        }
        
        /* Новые стили */
        .beta-label {
            position: fixed;
            bottom: 10px;
            right: 10px;
            color: #ffcc00;
            font-size: 0.8rem;
            font-weight: bold;
            text-shadow: 0 0 3px rgba(255, 204, 0, 0.8);
            opacity: 0.8;
        }
        
        .copyright {
            position: fixed;
            bottom: 10px;
            left: 10px;
            color: #aaa;
            font-size: 0.8rem;
            font-style: italic;
        }
        
        #reportBtn {
            position: fixed;
            top: 20px;
            right: 20px;
            background: rgba(0, 0, 0, 0.6);
            color: white;
            border: 2px solid #FF5722;
            border-radius: 30px;
            padding: 8px 20px;
            font-size: 0.9rem;
            cursor: pointer;
            transition: all 0.3s;
            z-index: 100;
        }
        
        #reportBtn:hover {
            background: rgba(255, 87, 34, 0.3);
        }
        
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            z-index: 200;
            justify-content: center;
            align-items: center;
        }
        
        .modal-content {
            background: linear-gradient(135deg, #1a2a6c, #b21f1f);
            padding: 30px;
            border-radius: 20px;
            max-width: 500px;
            width: 90%;
            text-align: center;
            box-shadow: 0 0 30px rgba(0, 0, 0, 0.8);
        }
        
        .report-menu {
            display: none;
            position: fixed;
            top: 60px;
            right: 20px;
            background: rgba(0, 0, 0, 0.9);
            border: 2px solid #4CAF50;
            border-radius: 15px;
            padding: 15px;
            z-index: 150;
            min-width: 200px;
        }
        
        .report-menu button {
            display: block;
            width: 100%;
            padding: 12px;
            margin: 10px 0;
            background: linear-gradient(to bottom, #4CAF50, #2E7D32);
            color: white;
            border: none;
            border-radius: 50px;
            font-size: 1rem;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <!-- Кнопка сообщить о проблеме -->
    <button id="reportBtn">Сообщить о проблеме</button>
    
    <!-- Меню отчёта -->
    <div id="reportMenu" class="report-menu">
        <button id="exitToMenuBtn">Выйти в меню</button>
        <button id="closeReportBtn">Закрыть</button>
    </div>
    
    <!-- Дисклеймер -->
    <div id="disclaimerModal" class="modal">
        <div class="modal-content">
            <h2 style="color: #ffcc00;">Дисклеймер</h2>
            <p>
                Эта игра создана исключительно в развлекательных целях. 
                Если вы обнаружите какие-либо баги или проблемы, пожалуйста, 
                сообщите о них с помощью кнопки "Сообщить о проблеме".
            </p>
            <button id="disclaimerOk" class="menu-btn" style="margin-top: 20px;">Понятно</button>
        </div>
    </div>
    
    <div class="container">
        <!-- Главное меню -->
        <div id="menu" class="screen">
            <h1>Змейка 🐍</h1>
            <button class="menu-btn" id="startBtn">Начать игру</button>
            <button class="menu-btn" id="settingsBtn">Настройки</button>
            <button class="menu-btn" id="recordsBtn">Мои рекорды</button>
            <button class="menu-btn" id="globalRecordsBtn">Глобальные рекорды</button>
        </div>

        <!-- Игровой экран -->
        <div id="gameScreen" class="screen">
            <div id="score">Очки: 0</div>
            <canvas id="game" width="400" height="400"></canvas>
            <div class="controls">
                <button class="btn" id="up">↑</button>
                <button class="btn" id="left">←</button>
                <button class="btn" id="down">↓</button>
                <button class="btn" id="right">→</button>
            </div>
            
            <!-- Экран паузы -->
            <div id="pauseScreen">
                <div id="pauseText">ПАУЗА</div>
                <button class="menu-btn" id="resumeBtn">Продолжить</button>
            </div>
        </div>

        <!-- Настройки -->
        <div id="settings" class="screen">
            <h2>Настройки</h2>
            
            <div class="setting-option">
                <label>Громкость звуков:</label>
                <input type="range" id="volume" min="0" max="1" step="0.1" value="0.5">
            </div>
            
            <div class="setting-option">
                <label>Громкость музыки:</label>
                <input type="range" id="musicVolume" min="0" max="1" step="0.1" value="0.3">
            </div>
            
            <div class="setting-option">
                <label>Сложность:</label>
                <select id="difficulty">
                    <option value="easy">Легко</option>
                    <option value="hard">Сложно</option>
                    <option value="impossible">Невозможно</option>
                </select>
            </div>
            
            <div class="setting-option">
                <label>Язык:</label>
                <select id="language">
                    <option value="ru">Русский</option>
                    <option value="en">English</option>
                </select>
            </div>
            
            <div class="setting-option">
                <label>Проходить сквозь стены:</label>
                <div class="toggle-container">
                    <div id="wallToggle" class="toggle-btn"></div>
                    <span id="wallStatus">Нет</span>
                </div>
            </div>
            
            <div class="setting-option">
                <label>Скин змейки:</label>
                <div class="skin-options">
                    <div class="skin-option selected" data-skin="default" style="background: linear-gradient(135deg, #00ff00, #009900);"></div>
                    <div class="skin-option" data-skin="blue" style="background: linear-gradient(135deg, #00ffff, #0066cc);"></div>
                    <div class="skin-option" data-skin="red" style="background: linear-gradient(135deg, #ff3300, #cc0000);"></div>
                    <div class="skin-option" data-skin="purple" style="background: linear-gradient(135deg, #cc00ff, #6600cc);"></div>
                    <div class="skin-option" data-skin="gold" style="background: linear-gradient(135deg, #ffcc00, #ff6600);"></div>
                </div>
            </div>
            
            <button class="menu-btn" id="saveSettingsBtn">Сохранить</button>
            <button class="menu-btn" id="backSettingsBtn">Назад</button>
        </div>

        <!-- Локальные рекорды -->
        <div id="records" class="screen">
            <h2>Мои рекорды</h2>
            <div id="recordsList"></div>
            <button class="menu-btn" id="backRecordsBtn">Назад</button>
        </div>
        
        <!-- Глобальные рекорды -->
        <div id="globalRecords" class="screen">
            <h2>Глобальные рекорды</h2>
            <div class="records-container">
                <div class="records-table">
                    <h3>Топ-10 игроков</h3>
                    <div id="globalRecordsList"></div>
                </div>
                <div class="records-table">
                    <h3>Топ-10 сегодня</h3>
                    <div id="dailyRecordsList"></div>
                </div>
            </div>
            <button class="menu-btn" id="backGlobalRecordsBtn">Назад</button>
        </div>
        
        <!-- Форма для ввода имени -->
        <div id="nameForm" class="screen">
            <h2>Новый рекорд!</h2>
            <p>Поздравляем! Ваш результат: <span id="finalScore">0</span> очков</p>
            <p>Введите ваше имя для таблицы рекордов:</p>
            <input type="text" id="nameInput" placeholder="Ваше имя" maxlength="20">
            <button class="menu-btn" id="submitScoreBtn">Отправить</button>
        </div>
    </div>
    
    <!-- Бета-метка -->
    <div id="betaLabel" class="beta-label">Бета-тест</div>
    
    <!-- Копирайт -->
    <div class="copyright">by Minos</div>

    <!-- Аудио -->
    <audio id="eatSound" preload="auto">
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-arcade-game-jump-coin-216.mp3" type="audio/mp3">
    </audio>
    <audio id="gameOverSound" preload="auto">
        <source src="https://assets.mixkit.co/sfx/preview/mixkit-retro-arcade-lose-2027.mp3" type="audio/mp3">
    </audio>
    <audio id="bgMusic" loop preload="auto">
        <source src="https://assets.mixkit.co/music/preview/mixkit-game-show-suspense-waiting-667.mp3" type="audio/mp3">
    </audio>

    <script>
        // ========== ИНИЦИАЛИЗАЦИЯ ПЕРЕМЕННЫХ ==========
        // Элементы интерфейса
        const menu = document.getElementById("menu");
        const gameScreen = document.getElementById("gameScreen");
        const settingsScreen = document.getElementById("settings");
        const recordsScreen = document.getElementById("records");
        const globalRecordsScreen = document.getElementById("globalRecords");
        const nameForm = document.getElementById("nameForm");
        const scoreElement = document.getElementById("score");
        const canvas = document.getElementById("game");
        const ctx = canvas.getContext("2d");
        const pauseScreen = document.getElementById("pauseScreen");
        
        // Настройки по умолчанию
        let settings = {
            volume: 0.5,
            musicVolume: 0.3,
            difficulty: "easy",
            language: "ru",
            ghostWalls: false,
            skin: "default",
            musicEnabled: true
        };

        // Игровые переменные
        const gridSize = 20;
        const tileSize = canvas.width / gridSize;
        let snake = [];
        let food = {};
        let dx = 0, dy = 0;
        let score = 0;
        let speed = 0;
        let gameInterval;
        let records = JSON.parse(localStorage.getItem("snakeRecords")) || [];
        let globalRecords = JSON.parse(localStorage.getItem("globalSnakeRecords")) || [];
        let dailyRecords = JSON.parse(localStorage.getItem("dailySnakeRecords")) || [];
        let obstacles = [];
        let particles = [];
        let isPaused = false;
        let eatAnimation = 0;

        // Цвета скинов
        const skins = {
            default: { head: "#00ff00", body: "#00cc00" },
            blue: { head: "#00ffff", body: "#0099cc" },
            red: { head: "#ff3300", body: "#cc0000" },
            purple: { head: "#cc00ff", body: "#6600cc" },
            gold: { head: "#ffcc00", body: "#ff6600" }
        };

        // ========== ОСНОВНАЯ ИНИЦИАЛИЗАЦИЯ ==========
        document.addEventListener('DOMContentLoaded', () => {
            initSettings();
            initEventListeners();
            updateLanguage();
            initGlobalRecords();
            
            // Показать дисклеймер при загрузке
            document.getElementById("disclaimerModal").style.display = "flex";
        });

        // ========== ФУНКЦИИ ИНИЦИАЛИЗАЦИИ ==========
        function initGlobalRecords() {
            // Если нет глобальных рекордов, создаем демо-данные
            if (!globalRecords.length) {
                globalRecords = [
                    { name: "Змейка Про", score: 950, date: new Date(2024, 5, 15).toISOString() },
                    { name: "Игрок X", score: 850, date: new Date(2024, 5, 16).toISOString() },
                    { name: "Гений Змей", score: 780, date: new Date(2024, 5, 17).toISOString() },
                    { name: "Змеиный Король", score: 720, date: new Date(2024, 5, 18).toISOString() },
                    { name: "Мастер Управления", score: 680, date: new Date(2024, 5, 19).toISOString() },
                    { name: "Скоростной Геймер", score: 640, date: new Date(2024, 5, 20).toISOString() },
                    { name: "Ниндзя Змей", score: 610, date: new Date(2024, 5, 21).toISOString() },
                    { name: "Рекордсмен", score: 580, date: new Date(2024, 5, 22).toISOString() },
                    { name: "Змеиный Гуру", score: 550, date: new Date(2024, 5, 23).toISOString() },
                    { name: "Новичок Про", score: 520, date: new Date(2024, 5, 24).toISOString() }
                ];
                localStorage.setItem("globalSnakeRecords", JSON.stringify(globalRecords));
            }
            
            // Если нет дневных рекордов, создаем демо-данные
            if (!dailyRecords.length) {
                const today = new Date();
                dailyRecords = [
                    { name: "Сегодняшний Чемпион", score: 420, date: today.toISOString() },
                    { name: "Серебряный Призер", score: 380, date: today.toISOString() },
                    { name: "Бронзовый Игрок", score: 350, date: today.toISOString() },
                    { name: "Скоростной Змей", score: 320, date: today.toISOString() },
                    { name: "Мастер Маневров", score: 290, date: today.toISOString() },
                    { name: "Уверенный Игрок", score: 260, date: today.toISOString() },
                    { name: "Ловкий Змееныш", score: 230, date: today.toISOString() },
                    { name: "Начинающий Чемпион", score: 200, date: today.toISOString() },
                    { name: "Упорный Игрок", score: 170, date: today.toISOString() },
                    { name: "Новичок", score: 150, date: today.toISOString() }
                ];
                localStorage.setItem("dailySnakeRecords", JSON.stringify(dailyRecords));
            }
        }

        function initSettings() {
            // Загрузка настроек из localStorage
            if (localStorage.getItem("snakeSettings")) {
                const savedSettings = JSON.parse(localStorage.getItem("snakeSettings"));
                Object.assign(settings, savedSettings);
                
                // Применение настроек UI
                document.getElementById("volume").value = settings.volume;
                document.getElementById("musicVolume").value = settings.musicVolume;
                document.getElementById("difficulty").value = settings.difficulty;
                document.getElementById("language").value = settings.language;
                document.getElementById("wallToggle").classList.toggle("active", settings.ghostWalls);
                document.getElementById("wallStatus").textContent = settings.ghostWalls ? "Да" : "Нет";
                
                // Выбор скина
                const skinOption = document.querySelector(`.skin-option[data-skin="${settings.skin}"]`);
                if (skinOption) {
                    document.querySelectorAll(".skin-option").forEach(opt => opt.classList.remove("selected"));
                    skinOption.classList.add("selected");
                }
            }
            
            // Инициализация глобальных рекордов
            initGlobalRecords();
        }

        function initEventListeners() {
            // Кнопки меню
            document.getElementById("startBtn").addEventListener("click", startGame);
            document.getElementById("settingsBtn").addEventListener("click", showSettings);
            document.getElementById("recordsBtn").addEventListener("click", showRecords);
            document.getElementById("globalRecordsBtn").addEventListener("click", showGlobalRecords);
            document.getElementById("saveSettingsBtn").addEventListener("click", saveSettings);
            document.getElementById("backSettingsBtn").addEventListener("click", showMenu);
            document.getElementById("backRecordsBtn").addEventListener("click", showMenu);
            document.getElementById("backGlobalRecordsBtn").addEventListener("click", showMenu);
            document.getElementById("resumeBtn").addEventListener("click", togglePause);
            document.getElementById("submitScoreBtn").addEventListener("click", submitScore);
            
            // Кнопки управления игрой
            document.getElementById("up").addEventListener("click", () => setDirection(0, -1));
            document.getElementById("down").addEventListener("click", () => setDirection(0, 1));
            document.getElementById("left").addEventListener("click", () => setDirection(-1, 0));
            document.getElementById("right").addEventListener("click", () => setDirection(1, 0));
            
            // Переключатель стен
            document.getElementById("wallToggle").addEventListener("click", () => {
                const toggle = document.getElementById("wallToggle");
                toggle.classList.toggle("active");
                document.getElementById("wallStatus").textContent = 
                    toggle.classList.contains("active") ? "Да" : "Нет";
            });

            // Выбор скина
            document.querySelectorAll(".skin-option").forEach(option => {
                option.addEventListener("click", () => {
                    document.querySelectorAll(".skin-option").forEach(opt => opt.classList.remove("selected"));
                    option.classList.add("selected");
                });
            });

            // Управление громкостью
            document.getElementById("volume").addEventListener("input", (e) => {
                document.getElementById("eatSound").volume = e.target.value;
                document.getElementById("gameOverSound").volume = e.target.value;
            });

            document.getElementById("musicVolume").addEventListener("input", (e) => {
                document.getElementById("bgMusic").volume = e.target.value;
            });

            // Управление клавиатурой
            document.addEventListener("keydown", handleKeyDown);
            
            // Обработчики для новой системы отчетов
            document.getElementById("reportBtn").addEventListener("click", function() {
                document.getElementById("reportMenu").style.display = "block";
            });
            
            document.getElementById("exitToMenuBtn").addEventListener("click", function() {
                document.getElementById("reportMenu").style.display = "none";
                showMenu();
            });
            
            document.getElementById("closeReportBtn").addEventListener("click", function() {
                document.getElementById("reportMenu").style.display = "none";
            });
            
            document.getElementById("disclaimerOk").addEventListener("click", function() {
                document.getElementById("disclaimerModal").style.display = "none";
            });
        }

        // ========== ФУНКЦИИ ИНТЕРФЕЙСА ==========
        function showMenu() {
            menu.style.display = "block";
            settingsScreen.style.display = "none";
            recordsScreen.style.display = "none";
            globalRecordsScreen.style.display = "none";
            gameScreen.style.display = "none";
            nameForm.style.display = "none";
            document.getElementById("bgMusic").pause();
        }

        function showSettings() {
            menu.style.display = "none";
            settingsScreen.style.display = "block";
            recordsScreen.style.display = "none";
            globalRecordsScreen.style.display = "none";
            gameScreen.style.display = "none";
            nameForm.style.display = "none";
        }

        function showRecords() {
            menu.style.display = "none";
            settingsScreen.style.display = "none";
            recordsScreen.style.display = "block";
            globalRecordsScreen.style.display = "none";
            gameScreen.style.display = "none";
            nameForm.style.display = "none";
            
            // Сортируем рекорды по убыванию
            const sortedRecords = [...records].sort((a, b) => b - a);
            const recordsList = document.getElementById("recordsList");
            recordsList.innerHTML = sortedRecords.length > 0 
                ? sortedRecords.slice(0, 10).map((r, i) => 
                    `<div style="font-size: 1.2rem; margin: 10px; padding: 10px; 
                      background: rgba(255, 255, 255, 0.1); border-radius: 10px;">
                     ${i + 1}. ${r}</div>`).join("")
                : "<p>Рекордов пока нет!</p>";
        }
        
        function showGlobalRecords() {
            menu.style.display = "none";
            settingsScreen.style.display = "none";
            recordsScreen.style.display = "none";
            globalRecordsScreen.style.display = "block";
            gameScreen.style.display = "none";
            nameForm.style.display = "none";
            
            // Обновляем таблицы рекордов
            updateRecordsTable("globalRecordsList", globalRecords);
            updateRecordsTable("dailyRecordsList", dailyRecords);
        }

        function updateRecordsTable(elementId, records) {
            const table = document.getElementById(elementId);
            const sortedRecords = [...records].sort((a, b) => b.score - a.score);
            
            table.innerHTML = sortedRecords.slice(0, 10).map((record, i) => {
                const date = new Date(record.date);
                const dateStr = `${date.getDate().toString().padStart(2, '0')}.${(date.getMonth()+1).toString().padStart(2, '0')}.${date.getFullYear()}`;
                
                return `
                    <div class="record-item">
                        <div>
                            <span class="record-rank">${i+1}.</span>
                            <span class="record-name">${record.name}</span>
                        </div>
                        <div>
                            <span class="record-score">${record.score}</span>
                            <span style="font-size: 0.8rem; opacity: 0.7;">(${dateStr})</span>
                        </div>
                    </div>
                `;
            }).join("");
        }

        function saveSettings() {
            const selectedSkin = document.querySelector(".skin-option.selected")?.dataset.skin || "default";
            
            // Обновляем настройки
            settings = {
                volume: parseFloat(document.getElementById("volume").value),
                musicVolume: parseFloat(document.getElementById("musicVolume").value),
                difficulty: document.getElementById("difficulty").value,
                language: document.getElementById("language").value,
                ghostWalls: document.getElementById("wallToggle").classList.contains("active"),
                skin: selectedSkin,
                musicEnabled: true
            };
            
            localStorage.setItem("snakeSettings", JSON.stringify(settings));
            updateLanguage();
            showMenu();
        }

        function updateLanguage() {
            const isRussian = settings.language === "ru";
            
            // Обновляем тексты
            document.getElementById("startBtn").textContent = isRussian ? "Начать игру" : "Start Game";
            document.getElementById("settingsBtn").textContent = isRussian ? "Настройки" : "Settings";
            document.getElementById("recordsBtn").textContent = isRussian ? "Мои рекорды" : "My Records";
            document.getElementById("globalRecordsBtn").textContent = isRussian ? "Глобальные рекорды" : "Global Records";
            document.getElementById("saveSettingsBtn").textContent = isRussian ? "Сохранить" : "Save";
            document.getElementById("backSettingsBtn").textContent = isRussian ? "Назад" : "Back";
            document.getElementById("backRecordsBtn").textContent = isRussian ? "Назад" : "Back";
            document.getElementById("backGlobalRecordsBtn").textContent = isRussian ? "Назад" : "Back";
            document.getElementById("resumeBtn").textContent = isRussian ? "Продолжить" : "Resume";
            document.getElementById("submitScoreBtn").textContent = isRussian ? "Отправить" : "Submit";
            document.querySelector("#settings h2").textContent = isRussian ? "Настройки" : "Settings";
            document.querySelector("#records h2").textContent = isRussian ? "Мои рекорды" : "My Records";
            document.querySelector("#globalRecords h2").textContent = isRussian ? "Глобальные рекорды" : "Global Records";
            document.querySelector("#nameForm h2").textContent = isRussian ? "Новый рекорд!" : "New Record!";
            document.querySelector("#pauseText").textContent = isRussian ? "ПАУЗА" : "PAUSED";
            document.querySelector("#nameForm p").textContent = isRussian ? 
                "Поздравляем! Ваш результат: " : "Congratulations! Your score: ";
            document.querySelector("#nameForm p:nth-child(3)").textContent = isRussian ? 
                "Введите ваше имя для таблицы рекордов:" : "Enter your name for the leaderboard:";
            document.getElementById("nameInput").placeholder = isRussian ? "Ваше имя" : "Your name";
            
            if (scoreElement) {
                scoreElement.textContent = isRussian ? `Очки: ${score}` : `Score: ${score}`;
            }
            
            // Новые элементы
            document.getElementById("reportBtn").textContent = isRussian ? "Сообщить о проблеме" : "Report an issue";
            document.getElementById("betaLabel").textContent = isRussian ? "Бета-тест" : "Beta test";
            document.getElementById("exitToMenuBtn").textContent = isRussian ? "Выйти в меню" : "Exit to menu";
            document.getElementById("closeReportBtn").textContent = isRussian ? "Закрыть" : "Close";
            document.querySelector("#disclaimerModal h2").textContent = isRussian ? "Дисклеймер" : "Disclaimer";
            document.querySelector("#disclaimerModal p").textContent = isRussian ? 
                "Эта игра создана исключительно в развлекательных целях. Если вы обнаружите какие-либо баги или проблемы, пожалуйста, сообщите о них с помощью кнопки 'Сообщить о проблеме'." : 
                "This game is created for entertainment purposes only. If you find any bugs or issues, please report them using the 'Report an issue' button.";
            document.getElementById("disclaimerOk").textContent = isRussian ? "Понятно" : "OK";
        }

        // ========== ИГРОВЫЕ ФУНКЦИИ ==========
        function startGame() {
            menu.style.display = "none";
            gameScreen.style.display = "block";
            document.querySelector(".controls").style.display = "grid";
            scoreElement.style.display = "block";
            
            // Запуск музыки
            const bgMusic = document.getElementById("bgMusic");
            if (settings.musicEnabled) {
                bgMusic.currentTime = 0;
                bgMusic.volume = settings.musicVolume;
                bgMusic.play().catch(e => console.log("Автовоспроизведение заблокировано"));
            }
            
            resetGame();
        }

        function resetGame() {
            snake = [{x: Math.floor(gridSize/2), y: Math.floor(gridSize/2)}];
            dx = 1; dy = 0;
            score = 0;
            particles = [];
            eatAnimation = 0;
            
            // Настройка скорости
            switch(settings.difficulty) {
                case "easy": speed = 150; break;
                case "hard": speed = 100; break;
                case "impossible": speed = 70; break;
                default: speed = 150;
            }
            
            scoreElement.textContent = settings.language === "ru" ? `Очки: ${score}` : `Score: ${score}`;
            spawnFood();
            
            // Препятствия для сложного уровня
            obstacles = [];
            if (settings.difficulty === "impossible") {
                for (let i = 0; i < 15; i++) {
                    obstacles.push({
                        x: Math.floor(Math.random() * gridSize),
                        y: Math.floor(Math.random() * gridSize)
                    });
                }
            }
            
            draw();
            if (gameInterval) clearInterval(gameInterval);
            gameInterval = setInterval(gameLoop, speed);
        }

        function spawnFood() {
            do {
                food = {
                    x: Math.floor(Math.random() * gridSize),
                    y: Math.floor(Math.random() * gridSize)
                };
            } while (
                snake.some(segment => segment.x === food.x && segment.y === food.y) ||
                obstacles.some(obs => obs.x === food.x && obs.y === food.y)
            );
        }

        function draw() {
            // Очистка поля
            ctx.fillStyle = "#111";
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Рисуем сетку
            ctx.strokeStyle = "#222";
            ctx.lineWidth = 0.5;
            for (let x = 0; x <= gridSize; x++) {
                ctx.beginPath();
                ctx.moveTo(x * tileSize, 0);
                ctx.lineTo(x * tileSize, canvas.height);
                ctx.stroke();
            }
            for (let y = 0; y <= gridSize; y++) {
                ctx.beginPath();
                ctx.moveTo(0, y * tileSize);
                ctx.lineTo(canvas.width, y * tileSize);
                ctx.stroke();
            }

            // Рисуем препятствия
            ctx.fillStyle = "#555";
            obstacles.forEach(obs => {
                ctx.fillRect(obs.x * tileSize, obs.y * tileSize, tileSize, tileSize);
            });

            // Рисуем змейку
            const skin = skins[settings.skin] || skins.default;
            snake.forEach((segment, index) => {
                if (index === 0) {
                    // Голова
                    ctx.fillStyle = skin.head;
                    ctx.beginPath();
                    ctx.arc(
                        segment.x * tileSize + tileSize/2,
                        segment.y * tileSize + tileSize/2,
                        tileSize/2 * 0.9,
                        0, Math.PI * 2
                    );
                    ctx.fill();
                    
                    // Глаза
                    ctx.fillStyle = "white";
                    ctx.beginPath();
                    ctx.arc(
                        segment.x * tileSize + tileSize/3,
                        segment.y * tileSize + tileSize/3,
                        tileSize/6,
                        0, Math.PI * 2
                    );
                    ctx.arc(
                        segment.x * tileSize + 2*tileSize/3,
                        segment.y * tileSize + tileSize/3,
                        tileSize/6,
                        0, Math.PI * 2
                    );
                    ctx.fill();
                    
                    // Зрачки
                    ctx.fillStyle = "black";
                    ctx.beginPath();
                    ctx.arc(
                        segment.x * tileSize + tileSize/3 + (dx * tileSize/10),
                        segment.y * tileSize + tileSize/3 + (dy * tileSize/10),
                        tileSize/10,
                        0, Math.PI * 2
                    );
                    ctx.arc(
                        segment.x * tileSize + 2*tileSize/3 + (dx * tileSize/10),
                        segment.y * tileSize + tileSize/3 + (dy * tileSize/10),
                        tileSize/10,
                        0, Math.PI * 2
                    );
                    ctx.fill();
                } else {
                    // Тело
                    ctx.fillStyle = skin.body;
                    ctx.beginPath();
                    ctx.arc(
                        segment.x * tileSize + tileSize/2,
                        segment.y * tileSize + tileSize/2,
                        tileSize/2 * 0.8,
                        0, Math.PI * 2
                    );
                    ctx.fill();
                }
            });

            // Рисуем еду
            ctx.fillStyle = "#ff3300";
            ctx.beginPath();
            ctx.arc(
                food.x * tileSize + tileSize/2,
                food.y * tileSize + tileSize/2,
                tileSize/2 * (eatAnimation > 0 ? 0.7 + 0.3 * Math.sin(Date.now()/100) : 0.7),
                0, Math.PI * 2
            );
            ctx.fill();
            
            // Листик
            ctx.fillStyle = "#00cc00";
            ctx.beginPath();
            ctx.moveTo(food.x * tileSize + tileSize/2, food.y * tileSize + tileSize/4);
            ctx.lineTo(food.x * tileSize + tileSize/1.5, food.y * tileSize + tileSize/3);
            ctx.lineTo(food.x * tileSize + tileSize/2, food.y * tileSize + tileSize/2.5);
            ctx.closePath();
            ctx.fill();

            // Рисуем частицы
            particles.forEach((p) => {
                ctx.fillStyle = p.color;
                ctx.globalAlpha = p.alpha;
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                ctx.fill();
                ctx.globalAlpha = 1;
            });
        }

        function setDirection(x, y) {
            // Запрет разворота на 180 градусов
            if ((dx !== 0 && x === -dx) || (dy !== 0 && y === -dy)) return;
            dx = x;
            dy = y;
        }

        function togglePause() {
            isPaused = !isPaused;
            pauseScreen.style.display = isPaused ? "flex" : "none";
            
            if (isPaused) {
                clearInterval(gameInterval);
                document.getElementById("bgMusic").pause();
            } else {
                gameInterval = setInterval(gameLoop, speed);
                if (settings.musicEnabled) {
                    document.getElementById("bgMusic").play();
                }
            }
        }

        function handleKeyDown(e) {
            if (e.key === "ArrowUp") setDirection(0, -1);
            else if (e.key === "ArrowDown") setDirection(0, 1);
            else if (e.key === "ArrowLeft") setDirection(-1, 0);
            else if (e.key === "ArrowRight") setDirection(1, 0);
            else if (e.key === "p" || e.key === " ") togglePause();
        }

        function gameLoop() {
            if (isPaused) return;
            
            const head = {x: snake[0].x + dx, y: snake[0].y + dy};

            // Проверка на выход за границы
            if (!settings.ghostWalls && (head.x < 0 || head.x >= gridSize || head.y < 0 || head.y >= gridSize)) {
                gameOver();
                return;
            }

            // Телепортация через стены
            if (settings.ghostWalls) {
                if (head.x < 0) head.x = gridSize - 1;
                if (head.x >= gridSize) head.x = 0;
                if (head.y < 0) head.y = gridSize - 1;
                if (head.y >= gridSize) head.y = 0;
            }

            // Проверка на столкновение
            if (
                snake.some((segment, index) => index > 0 && segment.x === head.x && segment.y === head.y) ||
                obstacles.some(obs => obs.x === head.x && obs.y === head.y)
            ) {
                gameOver();
                return;
            }

            snake.unshift(head);

            // Проверка на съедение еды
            if (head.x === food.x && head.y === food.y) {
                score += 10;
                scoreElement.textContent = settings.language === "ru" ? `Очки: ${score}` : `Score: ${score}`;
                
                const eatSound = document.getElementById("eatSound");
                eatSound.currentTime = 0;
                eatSound.volume = settings.volume;
                eatSound.play();
                
                // Эффект съедения
                eatAnimation = 10;
                createParticles(food.x, food.y, 15, "#ffcc00");
                
                spawnFood();
            } else {
                snake.pop();
            }
            
            if (eatAnimation > 0) eatAnimation--;

            updateParticles();
            draw();
        }

        function createParticles(x, y, count, color) {
            for (let i = 0; i < count; i++) {
                particles.push({
                    x: x * tileSize + tileSize/2,
                    y: y * tileSize + tileSize/2,
                    size: Math.random() * 5 + 2,
                    speedX: (Math.random() - 0.5) * 6,
                    speedY: (Math.random() - 0.5) * 6,
                    color: color,
                    alpha: 1,
                    life: 30
                });
            }
        }

        function updateParticles() {
            for (let i = particles.length - 1; i >= 0; i--) {
                const p = particles[i];
                p.x += p.speedX;
                p.y += p.speedY;
                p.alpha -= 0.03;
                p.life--;
                
                if (p.life <= 0 || p.alpha <= 0) {
                    particles.splice(i, 1);
                }
            }
        }

        function gameOver() {
            clearInterval(gameInterval);
            
            const gameOverSound = document.getElementById("gameOverSound");
            gameOverSound.currentTime = 0;
            gameOverSound.volume = settings.volume;
            gameOverSound.play();
            
            records.push(score);
            localStorage.setItem("snakeRecords", JSON.stringify(records));
            
            // Проверяем, попадает ли результат в топ-10 глобальных рекордов
            const minGlobalScore = globalRecords.length > 0 
                ? Math.min(...globalRecords.map(r => r.score))
                : 0;
                
            if (score > minGlobalScore || globalRecords.length < 10) {
                document.getElementById("finalScore").textContent = score;
                gameScreen.style.display = "none";
                nameForm.style.display = "block";
            } else {
                // Эффект проигрыша
                for (let i = 0; i < snake.length; i++) {
                    createParticles(snake[i].x, snake[i].y, 3, "#ff3300");
                }
                
                draw();
                
                setTimeout(() => {
                    showGameOverAlert();
                }, 500);
            }
        }
        
        function showGameOverAlert() {
            if (settings.language === "ru") {
                alert(`Игра окончена! Очки: ${score}`);
            } else {
                alert(`Game Over! Score: ${score}`);
            }
            gameScreen.style.display = "none";
            menu.style.display = "block";
            document.getElementById("bgMusic").pause();
        }
        
        function submitScore() {
            const nameInput = document.getElementById("nameInput");
            const playerName = nameInput.value.trim() || "Анонимный игрок";
            
            // Добавляем рекорд в глобальную таблицу
            const newRecord = {
                name: playerName,
                score: score,
                date: new Date().toISOString()
            };
            
            // Обновляем глобальные рекорды
            globalRecords.push(newRecord);
            globalRecords.sort((a, b) => b.score - a.score);
            if (globalRecords.length > 10) globalRecords.length = 10;
            localStorage.setItem("globalSnakeRecords", JSON.stringify(globalRecords));
            
            // Обновляем дневные рекорды
            const today = new Date().toISOString().split('T')[0];
            const todayRecords = dailyRecords.filter(r => r.date.split('T')[0] === today);
            todayRecords.push(newRecord);
            todayRecords.sort((a, b) => b.score - a.score);
            if (todayRecords.length > 10) todayRecords.length = 10;
            
            // Обновляем все дневные рекорды
            dailyRecords = dailyRecords.filter(r => r.date.split('T')[0] !== today)
                                      .concat(todayRecords);
            localStorage.setItem("dailySnakeRecords", JSON.stringify(dailyRecords));
            
            // Эффект проигрыша
            for (let i = 0; i < snake.length; i++) {
                createParticles(snake[i].x, snake[i].y, 3, "#ff3300");
            }
            
            draw();
            
            setTimeout(() => {
                showGameOverAlert();
            }, 500);
        }
    </script>
</body>
</html>
