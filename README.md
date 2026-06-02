<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Spy Game Pro</title>
    <style>
        /* Переменные для светлой темы (по умолчанию) */
        :root {
            --bg-color: #fbfbfd;
            --text-color: #1d1d1f;
            --secondary-text: #86868b;
            --card-bg: #ffffff;
            --blue: #0071e3;
            --blue-hover: #0077ed;
            --gray-bg: #f5f5f7;
            --border: #d2d2d7;
            --radius: 22px;
            --shadow: 0 8px 30px rgba(0, 0, 0, 0.04);
            --spy-color: #ff3b30;
        }

        /* Переменные для темной темы */
        body.dark-theme {
            --bg-color: #000000;
            --text-color: #f5f5f7;
            --secondary-text: #a1a1a6;
            --card-bg: #1c1c1e;
            --blue: #0a84ff;
            --blue-hover: #409fff;
            --gray-bg: #2c2c2e;
            --border: #3a3a3c;
            --shadow: 0 8px 30px rgba(0, 0, 0, 0.4);
            --spy-color: #ff453a;
        }

        /* Общие стили с плавным переходом */
        * {
            box-sizing: border-box;
            transition: background-color 0.5s cubic-bezier(0.4, 0, 0.2, 1), 
                        color 0.5s cubic-bezier(0.4, 0, 0.2, 1), 
                        border-color 0.5s ease, 
                        box-shadow 0.5s ease;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", "SF Pro Display", "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            -webkit-font-smoothing: antialiased;
            overflow: hidden;
        }

        /* Кнопка смены темы */
        .theme-toggle {
            position: fixed;
            bottom: 20px;
            right: 20px;
            width: 44px;
            height: 44px;
            border-radius: 50%;
            background: var(--card-bg);
            border: 1px solid var(--border);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            z-index: 1000;
            box-shadow: var(--shadow);
        }

        .theme-toggle svg {
            width: 20px;
            height: 20px;
            fill: var(--text-color);
        }

        .screen {
            display: none;
            max-width: 440px;
            width: 92%;
            background: var(--card-bg);
            padding: 40px 30px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            text-align: center;
            position: relative;
        }

        .screen.active {
            display: block;
            animation: appleSlideUp 0.6s cubic-bezier(0.23, 1, 0.32, 1);
        }

        @keyframes appleSlideUp {
            from { opacity: 0; transform: translateY(30px) scale(0.98); }
            to { opacity: 1; transform: translateY(0) scale(1); }
        }

        h1 {
            font-size: 32px;
            font-weight: 700;
            letter-spacing: -0.02em;
            margin-bottom: 24px;
        }

        .form-group {
            margin-bottom: 20px;
            text-align: left;
        }

        label {
            display: block;
            font-size: 13px;
            font-weight: 600;
            margin-bottom: 8px;
            margin-left: 4px;
            color: var(--secondary-text);
            text-transform: uppercase;
            letter-spacing: 0.05em;
        }

        select, input {
            width: 100%;
            padding: 14px 16px;
            font-size: 17px;
            border: 1px solid var(--border);
            border-radius: 14px;
            background: var(--gray-bg);
            color: var(--text-color);
            outline: none;
            font-family: inherit;
        }

        button {
            background-color: var(--blue);
            color: white;
            border: none;
            padding: 16px 24px;
            font-size: 17px;
            font-weight: 600;
            border-radius: 14px;
            cursor: pointer;
            width: 100%;
            margin-top: 10px;
        }

        button:active {
            transform: scale(0.98);
            opacity: 0.9;
        }

        .btn-secondary {
            background-color: var(--gray-bg);
            color: var(--text-color);
        }

        .card-word {
            font-size: 36px;
            font-weight: 800;
            color: var(--blue);
            margin: 30px 0;
            padding: 30px 20px;
            background: var(--gray-bg);
            border-radius: 20px;
            letter-spacing: -0.03em;
        }

        #timer-display {
            font-size: 64px;
            font-weight: 700;
            font-variant-numeric: tabular-nums;
            margin: 20px 0;
            letter-spacing: -0.04em;
        }

        .word-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 12px;
            margin-top: 24px;
            max-height: 35vh;
            overflow-y: auto;
            padding-right: 8px;
        }

        /* Кастомный скроллбар */
        .word-grid::-webkit-scrollbar { width: 6px; }
        .word-grid::-webkit-scrollbar-track { background: transparent; }
        .word-grid::-webkit-scrollbar-thumb { background: var(--border); border-radius: 10px; }

        .word-item {
            background: var(--gray-bg);
            padding: 12px;
            border-radius: 12px;
            font-size: 14px;
            font-weight: 500;
            border: 1px solid transparent;
        }
    </style>
</head>
<body>

    <div class="theme-toggle" onclick="toggleTheme()" id="theme-btn">
        <svg id="sun-icon" style="display:none;" viewBox="0 0 24 24"><path d="M12 7c-2.76 0-5 2.24-5 5s2.24 5 5 5 5-2.24 5-5-2.24-5-5-5zM2 13h2c.55 0 1-.45 1-1s-.45-1-1-1H2c-.55 0-1 .45-1 1s.45 1 1 1zm18 0h2c.55 0 1-.45 1-1s-.45-1-1-1h-2c-.55 0-1 .45-1 1s.45 1 1 1zM11 2v2c0 .55.45 1 1 1s1-.45 1-1V2c0-.55-.45-1-1-1s-1 .45-1 1zm0 18v2c0 .55.45 1 1 1s1-.45 1-1v-2c0-.55-.45-1-1-1s-1 .45-1 1zM5.99 4.58c-.39-.39-1.03-.39-1.41 0-.39.39-.39 1.03 0 1.41l1.06 1.06c.39.39 1.03.39 1.41 0s.39-1.03 0-1.41L5.99 4.58zm12.37 12.37c-.39-.39-1.03-.39-1.41 0-.39.39-.39 1.03 0 1.41l1.06 1.06c.39.39 1.03.39 1.41 0s.39-1.03 0-1.41l-1.06-1.06zm1.06-12.37c-.39-.39-1.03-.39-1.41 0l-1.06 1.06c-.39.39-.39 1.03 0 1.41s1.03.39 1.41 0l1.06-1.06c.39-.39.39-1.03 0-1.41zm-12.37 12.37c-.39-.39-1.03-.39-1.41 0l-1.06 1.06c-.39.39-.39 1.03 0 1.41s1.03.39 1.41 0l1.06-1.06c.39-.39.39-1.03 0-1.41z"/></svg>
        <svg id="moon-icon" viewBox="0 0 24 24"><path d="M12.12 22a10 10 0 01-7.12-17.1c.39-.38 1-.36 1.36.03.35.4.32 1-.06 1.35a7.97 7.97 0 002.32 12.59c.47.19.64.74.39 1.2-.26.47-.85.64-1.32.44A9.94 9.94 0 0112.12 22z"/></svg>
    </div>

    <div id="screen-lang" class="screen active">
        <h1>Spy Game</h1>
        <button onclick="setLanguage('ru')">Русский</button>
        <button onclick="setLanguage('en')" class="btn-secondary">English</button>
    </div>

    <div id="screen-setup" class="screen">
        <h1 id="t-setup-title">Setup</h1>
        <div class="form-group">
            <label id="t-players">Players</label>
            <input type="number" id="input-players" min="3" max="5" value="4">
        </div>
        <div class="form-group">
            <label id="t-mode">Game Mode</label>
            <select id="select-mode">
                <option value="1" id="t-mode-1">Classic</option>
                <option value="2" id="t-mode-2">Clueless Spy</option>
            </select>
        </div>
        <div class="form-group">
            <label id="t-theme">Theme</label>
            <select id="select-theme"></select>
        </div>
        <div class="form-group">
            <label id="t-time">Time</label>
            <input type="number" id="input-time" min="1" max="15" value="5">
        </div>
        <button onclick="startGame()" id="t-start">Start</button>
    </div>

    <div id="screen-pass" class="screen">
        <h1 id="pass-title">Player 1</h1>
        <p id="t-ready" style="color:var(--secondary-text); margin-bottom: 30px;">Make sure no one is looking.</p>
        <button onclick="showCard()" id="t-show">Reveal Card</button>
    </div>

    <div id="screen-card" class="screen">
        <h2 id="card-player">Player</h2>
        <div class="card-word" id="card-word">Word</div>
        <p id="card-role-desc" style="color:var(--secondary-text); font-size: 15px; margin-bottom: 25px; line-height: 1.4;"></p>
        <button onclick="nextPlayer()" id="t-hide">Done</button>
    </div>

    <div id="screen-game" class="screen">
        <h1 id="t-game-active">Playing</h1>
        <div id="timer-display">05:00</div>
        <button onclick="toggleTimer()" id="btn-timer-toggle" class="btn-secondary">Pause</button>
        <button onclick="endGame()" id="t-end" style="margin-top: 15px; background: transparent; color: var(--spy-color); border: 1px solid var(--spy-color);">End</button>
        <h3 id="t-word-list" style="margin-top: 30px; font-size: 14px; color: var(--secondary-text);">Possible Words:</h3>
        <div class="word-grid" id="word-grid"></div>
    </div>

    <script>
        const rawData = {
            ru: {
                "Места": "Банк,Казино,Больница,Отель,Ресторан,Школа,Университет,Аэропорт,Метро,Супермаркет,Театр,Музей,Цирк,Бассейн,Пляж,Стадион,Пожарная часть,Космос,Парк,Полиция,Спа",
                "Животные": "Собака,Кошка,Слон,Тигр,Лев,Медведь,Волк,Лиса,Кролик,Олень,Обезьяна,Жираф,Зебра,Кенгуру,Панда,Пингвин,Дельфин,Кит,Акула,Орел,Сова",
                "Еда": "Пицца,Суши,Бургер,Паста,Салат,Стейк,Суп,Мороженое,Торт,Блины,Пельмени,Шашлык,Тако,Сэндвич,Фри,Сыр,Шоколад,Яблоко,Банан,Арбуз,Стейк",
                "Профессии": "Врач,Учитель,Инженер,Повар,Актер,Музыкант,Художник,Писатель,Полицейский,Пожарный,Строитель,Водитель,Пилот,Космонавт,Юрист,Бухгалтер,Дизайнер,Фотограф,Спортсмен,Журналист,Хирург",
                "Техника": "Смартфон,Ноутбук,Планшет,Часы,Наушники,ТВ,Камера,Дрон,Принтер,Пылесос,Консоль,Роутер,Микрофон,Колонка,Монитор,Мышь,Флешка,Диск,Очки,Плеер,Проектор",
                "Транспорт": "Авто,Автобус,Поезд,Самолет,Вертолет,Корабль,Лодка,Велосипед,Мотоцикл,Скутер,Метро,Трамвай,Троллейбус,Грузовик,Трактор,Ракета,Танк,Дирижабль,Самокат,Квадроцикл,Яхта",
                "Спорт": "Футбол,Баскетбол,Волейбол,Теннис,Хоккей,Бокс,Плавание,Гимнастика,Фигурное катание,Биатлон,Атлетика,Шахматы,Гольф,Бейсбол,Регби,Дзюдо,Карате,Сноуборд,Серфинг,Велоспорт,Йога",
                "Страны": "Россия,США,Китай,Япония,Германия,Франция,Италия,Испания,Бразилия,Канада,Австралия,Мексика,Индия,Египет,Турция,Англия,Корея,Швеция,Швейцария,Греция,Таиланд",
                "Одежда": "Футболка,Джинсы,Куртка,Пальто,Свитер,Рубашка,Платье,Юбка,Шорты,Брюки,Шапка,Шарф,Перчатки,Носки,Кроссовки,Туфли,Сапоги,Ботинки,Кепка,Очки,Ремень",
                "Природа": "Лес,Гора,Озеро,Река,Океан,Море,Пустыня,Джунгли,Поле,Пещера,Вулкан,Водопад,Остров,Каньон,Долина,Тундра,Степь,Болото,Ледник,Пляж,Роща",
                "Хобби": "Чтение,Рисование,Фото,Кулинария,Рыбалка,Охота,Сад,Пение,Танцы,Гитара,Коллекция,Код,Оригами,Астрономия,Шахматы,Модели,Поход,Йога,Игры,Карты,Кино"
            },
            en: {
                "Places": "Bank,Casino,Hospital,Hotel,Restaurant,School,University,Airport,Subway,Supermarket,Theater,Museum,Circus,Pool,Beach,Stadium,Fire Station,Space,Park,Police,Spa",
                "Animals": "Dog,Cat,Elephant,Tiger,Lion,Bear,Wolf,Fox,Rabbit,Deer,Monkey,Giraffe,Zebra,Kangaroo,Panda,Penguin,Dolphin,Whale,Shark,Eagle,Owl",
                "Food": "Pizza,Sushi,Burger,Pasta,Salad,Steak,Soup,Ice Cream,Cake,Pancakes,Dumplings,Kebab,Taco,Sandwich,Fries,Cheese,Chocolate,Apple,Banana,Watermelon,Steak",
                "Professions": "Doctor,Teacher,Engineer,Chef,Actor,Musician,Artist,Writer,Police Officer,Firefighter,Builder,Driver,Pilot,Astronaut,Lawyer,Accountant,Designer,Photographer,Athlete,Journalist,Surgeon",
                "Tech": "Smartphone,Laptop,Tablet,Watch,Headphones,TV,Camera,Drone,Printer,Vacuum,Console,Router,Microphone,Speaker,Monitor,Mouse,Flash Drive,Drive,Glasses,Player,Projector",
                "Transport": "Car,Bus,Train,Airplane,Helicopter,Ship,Boat,Bicycle,Motorcycle,Scooter,Subway,Tram,Trolleybus,Truck,Tractor,Rocket,Tank,Airship,Scooter,ATV,Yacht",
                "Sports": "Football,Basketball,Volleyball,Tennis,Hockey,Boxing,Swimming,Gymnastics,Skating,Biathlon,Athletics,Chess,Golf,Baseball,Rugby,Judo,Karate,Snowboarding,Surfing,Cycling,Yoga",
                "Countries": "Russia,USA,China,Japan,Germany,France,Italy,Spain,Brazil,Canada,Australia,Mexico,India,Egypt,Turkey,England,Korea,Sweden,Switzerland,Greece,Thailand",
                "Clothes": "T-shirt,Jeans,Jacket,Coat,Sweater,Shirt,Dress,Skirt,Shorts,Pants,Hat,Scarf,Gloves,Socks,Sneakers,Shoes,Boots,Ankle Boots,Cap,Glasses,Belt",
                "Nature": "Forest,Mountain,Lake,River,Ocean,Sea,Desert,Jungle,Field,Cave,Volcano,Waterfall,Island,Canyon,Valley,Tundra,Steppe,Swamp,Glacier,Beach,Grove",
                "Hobbies": "Reading,Drawing,Photo,Cooking,Fishing,Hunting,Garden,Singing,Dancing,Guitar,Collecting,Coding,Origami,Astronomy,Chess,Modeling,Hiking,Yoga,Games,Cards,Cinema"
            }
        };

        const uiTexts = {
            ru: {
                title: "Настройка", players: "Игроки", mode: "Режим",
                mode1: "Классика (Шпион не знает слово)", mode2: "Слепой шпион (Похожее слово)",
                theme: "Тема", time: "Время (мин)", start: "Начать",
                pass: "Передай игроку ", ready: "Никто не должен подглядывать.",
                show: "Открыть", hide: "Готово",
                spy: "ШПИОН", spyClassicDesc: "Вы не знаете локацию. Слушайте вопросы и не выдайте себя!",
                spyBlindDesc: "Вы шпион! Но вам дали похожее слово, чтобы вы могли запутать других.",
                civilianDesc: "Вы в локации. Задавайте вопросы, чтобы вычислить шпиона.",
                gameActive: "Игра идёт", pause: "Пауза", resume: "Продолжить",
                endGame: "Завершить", wordList: "Возможные слова:", alert: "От 3 до 5 игроков"
            },
            en: {
                title: "Setup", players: "Players", mode: "Mode",
                mode1: "Classic (Spy knows nothing)", mode2: "Clueless Spy (Similar word)",
                theme: "Theme", time: "Time (min)", start: "Start",
                pass: "Pass to Player ", ready: "No peeking allowed.",
                show: "Reveal", hide: "Done",
                spy: "SPY", spyClassicDesc: "You don't know the location. Listen carefully and blend in!",
                spyBlindDesc: "You are the spy! You have a similar word to confuse the others.",
                civilianDesc: "You are in the location. Ask questions to find the spy.",
                gameActive: "Live", pause: "Pause", resume: "Resume",
                endGame: "End", wordList: "Possible words:", alert: "3 to 5 players required"
            }
        };

        let currentLang = 'en';
        let gameThemes = {};
        let gameState = { players: [], currentPlayerIndex: 0, timeLeft: 0, isPaused: false };

        function toggleTheme() {
            const body = document.body;
            const sunIcon = document.getElementById('sun-icon');
            const moonIcon = document.getElementById('moon-icon');

            if (body.classList.contains('dark-theme')) {
                body.classList.remove('dark-theme');
                sunIcon.style.display = 'none';
                moonIcon.style.display = 'block';
            } else {
                body.classList.add('dark-theme');
                sunIcon.style.display = 'block';
                moonIcon.style.display = 'none';
            }
        }

        function showScreen(id) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(id).classList.add('active');
        }

        function setLanguage(lang) {
            currentLang = lang;
            const ui = uiTexts[lang];
            gameThemes = {};
            for (let theme in rawData[lang]) gameThemes[theme] = rawData[lang][theme].split(',');

            document.getElementById('t-setup-title').textContent = ui.title;
            document.getElementById('t-players').textContent = ui.players;
            document.getElementById('t-mode').textContent = ui.mode;
            document.getElementById('t-mode-1').textContent = ui.mode1;
            document.getElementById('t-mode-2').textContent = ui.mode2;
            document.getElementById('t-theme').textContent = ui.theme;
            document.getElementById('t-time').textContent = ui.time;
            document.getElementById('t-start').textContent = ui.start;
            document.getElementById('t-ready').textContent = ui.ready;
            document.getElementById('t-show').textContent = ui.show;
            document.getElementById('t-hide').textContent = ui.hide;
            document.getElementById('t-game-active').textContent = ui.gameActive;
            document.getElementById('btn-timer-toggle').textContent = ui.pause;
            document.getElementById('t-end').textContent = ui.endGame;
            document.getElementById('t-word-list').textContent = ui.wordList;

            const themeSelect = document.getElementById('select-theme');
            themeSelect.innerHTML = "";
            Object.keys(gameThemes).forEach(t => {
                let opt = document.createElement('option');
                opt.value = t; opt.textContent = t;
                themeSelect.appendChild(opt);
            });
            showScreen('screen-setup');
        }

        function startGame() {
            const n = parseInt(document.getElementById('input-players').value);
            if (n < 3 || n > 5) { alert(uiTexts[currentLang].alert); return; }

            const mode = document.getElementById('select-mode').value;
            const theme = document.getElementById('select-theme').value;
            const words = gameThemes[theme];
            const targetIdx = Math.floor(Math.random() * words.length);
            const targetWord = words[targetIdx];
            const spyIdx = Math.floor(Math.random() * n);

            gameState.players = [];
            for (let i = 0; i < n; i++) {
                if (i === spyIdx) {
                    if (mode === "1") {
                        gameState.players.push({ role: 'spy', word: uiTexts[currentLang].spy, desc: uiTexts[currentLang].spyClassicDesc });
                    } else {
                        let fIdx = Math.floor(Math.random() * words.length);
                        while(fIdx === targetIdx) fIdx = Math.floor(Math.random() * words.length);
                        gameState.players.push({ role: 'spy-blind', word: words[fIdx], desc: uiTexts[currentLang].spyBlindDesc });
                    }
                } else {
                    gameState.players.push({ role: 'civilian', word: targetWord, desc: uiTexts[currentLang].civilianDesc });
                }
            }

            gameState.currentPlayerIndex = 0;
            gameState.timeLeft = parseInt(document.getElementById('input-time').value) * 60;
            
            const grid = document.getElementById('word-grid');
            grid.innerHTML = "";
            words.forEach(w => {
                let d = document.createElement('div');
                d.className = 'word-item'; d.textContent = w;
                grid.appendChild(d);
            });

            updatePassScreen();
        }

        function updatePassScreen() {
            document.getElementById('pass-title').textContent = uiTexts[currentLang].pass + (gameState.currentPlayerIndex + 1);
            showScreen('screen-pass');
        }

        function showCard() {
            const p = gameState.players[gameState.currentPlayerIndex];
            document.getElementById('card-player').textContent = (currentLang === 'ru' ? 'Игрок ' : 'Player ') + (gameState.currentPlayerIndex + 1);
            document.getElementById('card-word').textContent = p.word;
            document.getElementById('card-role-desc').textContent = p.desc;
            document.getElementById('card-word').style.color = (p.role === 'spy') ? 'var(--spy-color)' : 'var(--blue)';
            showScreen('screen-card');
        }

        function nextPlayer() {
            gameState.currentPlayerIndex++;
            if (gameState.currentPlayerIndex < gameState.players.length) updatePassScreen();
            else startTimer();
        }

        function startTimer() {
            showScreen('screen-game');
            updateTimerDisplay();
            if(gameState.timerInterval) clearInterval(gameState.timerInterval);
            gameState.timerInterval = setInterval(() => {
                if (!gameState.isPaused && gameState.timeLeft > 0) {
                    gameState.timeLeft--;
                    updateTimerDisplay();
                }
            }, 1000);
        }

        function updateTimerDisplay() {
            const m = Math.floor(gameState.timeLeft / 60).toString().padStart(2, '0');
            const s = (gameState.timeLeft % 60).toString().padStart(2, '0');
            document.getElementById('timer-display').textContent = `${m}:${s}`;
        }

        function toggleTimer() {
            gameState.isPaused = !gameState.isPaused;
            document.getElementById('btn-timer-toggle').textContent = gameState.isPaused ? uiTexts[currentLang].resume : uiTexts[currentLang].pause;
        }

        function endGame() {
            clearInterval(gameState.timerInterval);
            showScreen('screen-setup');
        }
    </script>
</body>
</html>
