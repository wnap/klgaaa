<!doctype html>
<html lang="ru">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Шпион — Минималистичная игра</title>
<link rel="icon" href="data:;base64,iVBORw0KGgo=">
<style>
  :root{
    --bg:#0b0b0c;
    --card:#0f1720;
    --muted:#9aa3ad;
    --accent:#ff7a00;
    --glass: rgba(255,255,255,0.04);
    --radius:14px;
    --glass-2: rgba(255,255,255,0.02);
    --text:#e9eef5;
    --shadow: 0 6px 30px rgba(2,6,23,0.6);
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  }
  html,body{height:100%;margin:0;background:linear-gradient(180deg,#050507 0%, #0b0b0c 100%);color:var(--text);}
  .wrap{min-height:100vh;display:flex;align-items:center;justify-content:center;padding:28px;}
  .panel{
    width:100%;
    max-width:980px;
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    border-radius:20px;
    padding:28px;
    box-shadow:var(--shadow);
    backdrop-filter: blur(8px) saturate(120%);
    border:1px solid rgba(255,255,255,0.03);
  }
  header{display:flex;align-items:center;gap:18px;margin-bottom:18px}
  .logo{
    width:48px;height:48px;border-radius:10px;background:linear-gradient(135deg,#111827,#0b1220);display:flex;align-items:center;justify-content:center;font-weight:700;color:var(--accent);font-size:18px;box-shadow:0 4px 18px rgba(0,0,0,0.6);
  }
  h1{font-size:20px;margin:0;font-weight:600}
  p.lead{margin:0;color:var(--muted);font-size:13px}
  .grid{display:grid;grid-template-columns:1fr 360px;gap:20px;margin-top:18px}
  @media(max-width:880px){.grid{grid-template-columns:1fr;}.right{order:2}}
  .card{background:var(--card);border-radius:14px;padding:18px;border:1px solid rgba(255,255,255,0.03)}
  .controls{display:flex;flex-direction:column;gap:12px}
  label{font-size:12px;color:var(--muted);display:block;margin-bottom:6px}
  .row{display:flex;gap:8px;align-items:center}
  input[type="number"], select, input[type="text"]{
    width:100%;padding:10px 12px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);background:var(--glass);color:var(--text);outline:none;font-size:14px;
  }
  .players-list{display:flex;flex-direction:column;gap:8px}
  .player-input{display:flex;gap:8px}
  .player-input input{flex:1}
  .btn{
    background:linear-gradient(180deg,var(--accent), #ff6a00);
    color:#fff;padding:10px 14px;border-radius:10px;border:none;font-weight:600;cursor:pointer;box-shadow:0 6px 18px rgba(255,122,0,0.12);
  }
  .btn.ghost{background:transparent;border:1px solid rgba(255,255,255,0.04);color:var(--text);box-shadow:none}
  .theme-list{display:flex;flex-wrap:wrap;gap:8px}
  .theme-item{padding:8px 10px;border-radius:10px;background:var(--glass-2);cursor:pointer;border:1px solid rgba(255,255,255,0.02);font-size:13px;color:var(--muted)}
  .theme-item.active{background:linear-gradient(90deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01));color:var(--text);border-color:rgba(255,255,255,0.04)}
  .right .card{display:flex;flex-direction:column;gap:12px}
  .big-card{height:220px;border-radius:12px;display:flex;align-items:center;justify-content:center;font-size:28px;font-weight:700;background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.02));border:1px solid rgba(255,255,255,0.03)}
  .small{font-size:13px;color:var(--muted)}
  .center{display:flex;align-items:center;justify-content:center}
  .timer{font-size:48px;font-weight:700;color:var(--accent)}
  .controls .hint{font-size:12px;color:var(--muted)}
  footer{margin-top:14px;display:flex;justify-content:space-between;align-items:center;color:var(--muted);font-size:12px}
  .chip{padding:6px 10px;border-radius:999px;background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.02);font-size:13px}
  /* modal */
  .modal{position:fixed;inset:0;display:none;align-items:center;justify-content:center;background:rgba(2,6,23,0.6);z-index:60}
  .modal.show{display:flex}
  .modal .dialog{width:100%;max-width:420px;background:linear-gradient(180deg,#07101a,#0b1116);padding:18px;border-radius:12px;border:1px solid rgba(255,255,255,0.03)}
  .dialog .title{font-weight:700;margin-bottom:8px}
  .muted{color:var(--muted)}
  .small-btn{padding:8px 10px;border-radius:8px;border:1px solid rgba(255,255,255,0.03);background:transparent;color:var(--text);cursor:pointer}
</style>
</head>
<body>
<div class="wrap">
  <div class="panel" role="application" aria-label="Spy game">
    <header>
      <div class="logo">S</div>
      <div>
        <h1>Шпион</h1>
        <p class="lead">Минималистичная игра для 3–5 игроков. Передавайте устройство по кругу.</p>
      </div>
    </header>

    <div class="grid">
      <div class="left">
        <div class="card">
          <div class="controls">
            <div>
              <label>Количество игроков</label>
              <div class="row">
                <select id="playerCount">
                  <option value="3">3</option>
                  <option value="4" selected>4</option>
                  <option value="5">5</option>
                </select>
                <button class="btn ghost" id="autoNames">Auto</button>
              </div>
            </div>

            <div>
              <label>Имена игроков</label>
              <div class="players-list" id="players"></div>
            </div>

            <div>
              <label>Режим игры</label>
              <div class="row">
                <select id="mode">
                  <option value="classic">Classic Spy (шпион виден)</option>
                  <option value="hidden" selected>Hidden Spy (шпион не знает)</option>
                </select>
                <div style="width:8px"></div>
                <input type="number" id="roundTime" min="30" max="900" value="180" style="width:120px" />
              </div>
              <div class="hint small muted">Время обсуждения в секундах</div>
            </div>

            <div>
              <label>Тема</label>
              <div class="theme-list" id="themeList"></div>
            </div>

            <div style="display:flex;gap:8px;margin-top:8px">
              <button class="btn" id="startBtn">Start</button>
              <button class="btn ghost" id="resetBtn">Reset</button>
            </div>
            <div class="hint small muted">Порядок: Show my card → Hide → следующий игрок. После просмотра всех — Start Round.</div>
          </div>
        </div>

        <div style="margin-top:12px" class="card">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <div>
              <div class="small muted">Текущий раунд</div>
              <div id="roundInfo" style="font-weight:700">Ожидание</div>
            </div>
            <div class="chip" id="spyInfo">Шпион: —</div>
          </div>

          <div style="margin-top:12px;display:flex;gap:8px;flex-wrap:wrap">
            <button class="small-btn" id="showCardBtn">Show my card</button>
            <button class="small-btn" id="hideCardBtn">Hide</button>
            <button class="small-btn" id="nextPlayerBtn">Next player</button>
            <button class="small-btn" id="startRoundBtn">Start Round</button>
            <button class="small-btn" id="stopTimerBtn">Stop Timer</button>
          </div>
        </div>
      </div>

      <div class="right">
        <div class="card">
          <div class="big-card center" id="cardView">—</div>
          <div style="display:flex;justify-content:space-between;align-items:center;margin-top:12px">
            <div>
              <div class="small muted">Игрок</div>
              <div id="currentPlayer" style="font-weight:700">—</div>
            </div>
            <div style="text-align:right">
              <div class="small muted">Таймер</div>
              <div class="timer" id="timerDisplay">00:00</div>
            </div>
          </div>
        </div>

        <div style="margin-top:12px" class="card">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <div>
              <div class="small muted">Инструкции</div>
              <div style="font-weight:600">Передавайте устройство по кругу</div>
            </div>
            <div style="text-align:right">
              <div class="small muted">Версия</div>
              <div class="muted">1.0</div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <footer>
      <div class="muted">Дизайн минималистичный</div>
      <div class="muted">Работает на GitHub Pages</div>
    </footer>
  </div>
</div>

<!-- Modal -->
<div class="modal" id="modal">
  <div class="dialog">
    <div class="title" id="modalTitle">Card</div>
    <div id="modalBody" class="muted" style="margin-bottom:12px">—</div>
    <div style="display:flex;gap:8px;justify-content:flex-end">
      <button class="btn ghost" id="modalHide">Hide</button>
      <button class="btn" id="modalOk">OK</button>
    </div>
  </div>
</div>

<script>
/* Data: themes with >20 cards each. Each card has word and alt (for hidden spy) */
const THEMES = [
  {name:"Фрукты", cards:[
    {word:"Яблоко", alt:"Груша"},
    {word:"Банан", alt:"Папайя"},
    {word:"Апельсин", alt:"Мандарин"},
    {word:"Виноград", alt:"Изюм"},
    {word:"Киви", alt:"Актинидия"},
    {word:"Персик", alt:"Нектарин"},
    {word:"Слива", alt:"Алыча"},
    {word:"Ананас", alt:"Карамбола"},
    {word:"Манго", alt:"Папайя"},
    {word:"Гранат", alt:"Клюква"},
    {word:"Лимон", alt:"Лайм"},
    {word:"Грейпфрут", alt:"Помело"},
    {word:"Кокос", alt:"Дуриан"},
    {word:"Черешня", alt:"Вишня"},
    {word:"Клубника", alt:"Малина"},
    {word:"Ежевика", alt:"Малина"},
    {word:"Арбуз", alt:"Дыня"},
    {word:"Дыня", alt:"Арбуз"},
    {word:"Авокадо", alt:"Оливка"},
    {word:"Фейхоа", alt:"Киви"},
    {word:"Личи", alt:"Рамбутан"}
  ]},
  {name:"Животные", cards:[
    {word:"Лев", alt:"Тигр"},
    {word:"Тигр", alt:"Лев"},
    {word:"Слон", alt:"Носорог"},
    {word:"Жираф", alt:"Антилопа"},
    {word:"Обезьяна", alt:"Шимпанзе"},
    {word:"Кенгуру", alt:"Вомбат"},
    {word:"Пингвин", alt:"Альбатрос"},
    {word:"Кит", alt:"Дельфин"},
    {word:"Дельфин", alt:"Кит"},
    {word:"Волк", alt:"Лиса"},
    {word:"Лиса", alt:"Волк"},
    {word:"Медведь", alt:"Панда"},
    {word:"Панда", alt:"Медведь"},
    {word:"Крокодил", alt:"Аллигатор"},
    {word:"Аллигатор", alt:"Крокодил"},
    {word:"Орел", alt:"Сокол"},
    {word:"Сокол", alt:"Ястреб"},
    {word:"Ястреб", alt:"Сокол"},
    {word:"Кошка", alt:"Тигр"},
    {word:"Собака", alt:"Волк"},
    {word:"Кролик", alt:"Заяц"}
  ]},
  {name:"Страны", cards:[
    {word:"Франция", alt:"Италия"},
    {word:"Италия", alt:"Франция"},
    {word:"Япония", alt:"Корея"},
    {word:"Канада", alt:"США"},
    {word:"Бразилия", alt:"Аргентина"},
    {word:"Австралия", alt:"Новая Зеландия"},
    {word:"Индия", alt:"Пакистан"},
    {word:"Китай", alt:"Тайвань"},
    {word:"Германия", alt:"Нидерланды"},
    {word:"Испания", alt:"Португалия"},
    {word:"Швеция", alt:"Норвегия"},
    {word:"Швейцария", alt:"Австрия"},
    {word:"Мексика", alt:"Колумбия"},
    {word:"Египет", alt:"Марокко"},
    {word:"Турция", alt:"Греция"},
    {word:"Россия", alt:"Украина"},
    {word:"Южная Корея", alt:"Япония"},
    {word:"США", alt:"Канада"},
    {word:"Нидерланды", alt:"Бельгия"},
    {word:"Польша", alt:"Чехия"},
    {word:"Португалия", alt:"Испания"}
  ]},
  {name:"Фильмы", cards:[
    {word:"Интерстеллар", alt:"Гравитация"},
    {word:"Начало", alt:"Матрица"},
    {word:"Матрица", alt:"Начало"},
    {word:"Титаник", alt:"Авиатор"},
    {word:"Гарри Поттер", alt:"Хроники Нарнии"},
    {word:"Властелин колец", alt:"Хоббит"},
    {word:"Звёздные войны", alt:"Стартрек"},
    {word:"Форрест Гамп", alt:"Зелёная миля"},
    {word:"Темный рыцарь", alt:"Бэтмен"},
    {word:"Побег из Шоушенка", alt:"Зелёная миля"},
    {word:"Аватар", alt:"Прометей"},
    {word:"Джокер", alt:"Таксист"},
    {word:"Криминальное чтиво", alt:"Бешеные псы"},
    {word:"Бегущий по лезвию", alt:"Бегущий по лезвию 2049"},
    {word:"Назад в будущее", alt:"Громадина"},
    {word:"Индиана Джонс", alt:"Национальное сокровище"},
    {word:"Шоу Трумана", alt:"Эквилибриум"},
    {word:"Остров проклятых", alt:"Семь"},
    {word:"Семь", alt:"Остров проклятых"},
    {word:"Парк Юрского периода", alt:"Кинг Конг"},
    {word:"Мстители", alt:"Стражи Галактики"}
  ]},
  {name:"Технологии", cards:[
    {word:"Смартфон", alt:"Планшет"},
    {word:"Ноутбук", alt:"Ультрабук"},
    {word:"Интернет", alt:"Веб"},
    {word:"Искусственный интеллект", alt:"Машинное обучение"},
    {word:"Блокчейн", alt:"Криптовалюта"},
    {word:"Дрон", alt:"Квадрокоптер"},
    {word:"Робот", alt:"Автомат"},
    {word:"Сервер", alt:"Хостинг"},
    {word:"Облако", alt:"SaaS"},
    {word:"Камера", alt:"Видеокамера"},
    {word:"Процессор", alt:"Чип"},
    {word:"SSD", alt:"HDD"},
    {word:"WiFi", alt:"Bluetooth"},
    {word:"Голосовой помощник", alt:"Чатбот"},
    {word:"AR", alt:"VR"},
    {word:"VR", alt:"AR"},
    {word:"3D принтер", alt:"CNC"},
    {word:"Смарт-часы", alt:"Фитнес-браслет"},
    {word:"Графический процессор", alt:"Видеокарта"},
    {word:"Материнская плата", alt:"Чипсет"},
    {word:"Датчик", alt:"Сенсор"}
  ]},
  {name:"Спорт", cards:[
    {word:"Футбол", alt:"Регби"},
    {word:"Баскетбол", alt:"Волейбол"},
    {word:"Теннис", alt:"Падел"},
    {word:"Хоккей", alt:"Кёрлинг"},
    {word:"Бег", alt:"Спринт"},
    {word:"Плавание", alt:"Водное поло"},
    {word:"Бокс", alt:"Кикбоксинг"},
    {word:"Гольф", alt:"Мини-гольф"},
    {word:"Формула 1", alt:"Ралли"},
    {word:"Шахматы", alt:"Шашки"},
    {word:"Регби", alt:"Футбол"},
    {word:"Сёрфинг", alt:"Кайтсерфинг"},
    {word:"Скалолазание", alt:"Болдеринг"},
    {word:"Йога", alt:"Пилатес"},
    {word:"Триатлон", alt:"Дуатлон"},
    {word:"Биатлон", alt:"Лыжные гонки"},
    {word:"Крикет", alt:"Бейсбол"},
    {word:"Бейсбол", alt:"Крикет"},
    {word:"Гимнастика", alt:"Акробатика"},
    {word:"Поло", alt:"Конный спорт"},
    {word:"Бадминтон", alt:"Теннис"}
  ]},
  {name:"Цвета", cards:[
    {word:"Красный", alt:"Бордовый"},
    {word:"Синий", alt:"Индиго"},
    {word:"Зелёный", alt:"Оливковый"},
    {word:"Жёлтый", alt:"Золотой"},
    {word:"Чёрный", alt:"Графитовый"},
    {word:"Белый", alt:"Молочный"},
    {word:"Розовый", alt:"Пудровый"},
    {word:"Фиолетовый", alt:"Лавандовый"},
    {word:"Оранжевый", alt:"Мандариновый"},
    {word:"Бирюзовый", alt:"Морской"},
    {word:"Коричневый", alt:"Шоколадный"},
    {word:"Серый", alt:"Серебристый"},
    {word:"Бежевый", alt:"Песочный"},
    {word:"Мятный", alt:"Салатовый"},
    {word:"Лаймовый", alt:"Неоновый"},
    {word:"Пурпурный", alt:"Баклажановый"},
    {word:"Хаки", alt:"Оливковый"},
    {word:"Кремовый", alt:"Молочный"},
    {word:"Тёмно-синий", alt:"Ночной"},
    {word:"Лососевый", alt:"Персиковый"},
    {word:"Шафрановый", alt:"Золотистый"}
  ]},
  {name:"Транспорт", cards:[
    {word:"Автомобиль", alt:"Купе"},
    {word:"Мотоцикл", alt:"Скутер"},
    {word:"Велосипед", alt:"Шоссейный"},
    {word:"Поезд", alt:"Скоростной поезд"},
    {word:"Самолёт", alt:"Пассажирский"},
    {word:"Вертолёт", alt:"Дрон"},
    {word:"Корабль", alt:"Яхта"},
    {word:"Трамвай", alt:"Троллейбус"},
    {word:"Метро", alt:"Электричка"},
    {word:"Автобус", alt:"Маршрутка"},
    {word:"Трактор", alt:"Комбайн"},
    {word:"Грузовик", alt:"Фургон"},
    {word:"Каяк", alt:"Каноэ"},
    {word:"Рикша", alt:"Тук-тук"},
    {word:"Феррари", alt:"Ламборгини"},
    {word:"Троллейбус", alt:"Трамвай"},
    {word:"Электросамокат", alt:"Скутер"},
    {word:"Круизный лайнер", alt:"Паром"},
    {word:"Подводная лодка", alt:"Субмарина"},
    {word:"Кабриолет", alt:"Родстер"},
    {word:"Гондола", alt:"Лодка"}
  ]},
  {name:"Еда", cards:[
    {word:"Пицца", alt:"Кальцоне"},
    {word:"Суши", alt:"Роллы"},
    {word:"Бургер", alt:"Чизбургер"},
    {word:"Паста", alt:"Лазанья"},
    {word:"Салат Цезарь", alt:"Салат с курицей"},
    {word:"Стейк", alt:"Рёбрышки"},
    {word:"Рамен", alt:"Удон"},
    {word:"Тако", alt:"Буррито"},
    {word:"Кебаб", alt:"Шашлык"},
    {word:"Пельмени", alt:"Вареники"},
    {word:"Блины", alt:"Оладьи"},
    {word:"Суп", alt:"Бульон"},
    {word:"Карри", alt:"Тика масала"},
    {word:"Пирог", alt:"Тарт"},
    {word:"Мороженое", alt:"Сорбет"},
    {word:"Шоколад", alt:"Какао"},
    {word:"Кофе", alt:"Эспрессо"},
    {word:"Чай", alt:"Матча"},
    {word:"Хумус", alt:"Баба гануш"},
    {word:"Гриль", alt:"Барбекю"},
    {word:"Ризотто", alt:"Плов"}
  ]},
  {name:"Города", cards:[
    {word:"Париж", alt:"Рим"},
    {word:"Нью-Йорк", alt:"Чикаго"},
    {word:"Токио", alt:"Осака"},
    {word:"Лондон", alt:"Манчестер"},
    {word:"Барселона", alt:"Валенсия"},
    {word:"Сан-Франциско", alt:"Лос-Анджелес"},
    {word:"Берлин", alt:"Гамбург"},
    {word:"Москва", alt:"Санкт-Петербург"},
    {word:"Сидней", alt:"Мельбурн"},
    {word:"Рио-де-Жанейро", alt:"Сан-Паулу"},
    {word:"Стамбул", alt:"Анкара"},
    {word:"Дубай", alt:"Абу-Даби"},
    {word:"Сингапур", alt:"Гонконг"},
    {word:"Кейптаун", alt:"Йоханнесбург"},
    {word:"Бангкок", alt:"Чиангмай"},
    {word:"Мехико", alt:"Гвадалахара"},
    {word:"Копенгаген", alt:"Осло"},
    {word:"Хельсинки", alt:"Стокгольм"},
    {word:"Вена", alt:"Зальцбург"},
    {word:"Прага", alt:"Братислава"},
    {word:"Брюссель", alt:"Антверпен"}
  ]}
];

/* App state */
let state = {
  players: [],
  playerCount: 4,
  themeIndex: 0,
  mode: 'hidden',
  roundTime: 180,
  spyIndex: null,
  cardsAssigned: [],
  currentViewer: 0,
  timer: null,
  timeLeft: 0,
  roundActive: false
};

/* DOM refs */
const playersEl = document.getElementById('players');
const playerCountEl = document.getElementById('playerCount');
const autoNamesBtn = document.getElementById('autoNames');
const themeListEl = document.getElementById('themeList');
const startBtn = document.getElementById('startBtn');
const resetBtn = document.getElementById('resetBtn');
const cardView = document.getElementById('cardView');
const currentPlayerEl = document.getElementById('currentPlayer');
const showCardBtn = document.getElementById('showCardBtn');
const hideCardBtn = document.getElementById('hideCardBtn');
const nextPlayerBtn = document.getElementById('nextPlayerBtn');
const startRoundBtn = document.getElementById('startRoundBtn');
const startRoundControl = document.getElementById('startRoundBtn');
const timerDisplay = document.getElementById('timerDisplay');
const roundInfo = document.getElementById('roundInfo');
const spyInfo = document.getElementById('spyInfo');
const modeEl = document.getElementById('mode');
const roundTimeEl = document.getElementById('roundTime');
const modal = document.getElementById('modal');
const modalTitle = document.getElementById('modalTitle');
const modalBody = document.getElementById('modalBody');
const modalOk = document.getElementById('modalOk');
const modalHide = document.getElementById('modalHide');
const roundInfoText = document.getElementById('roundInfo');

/* Helpers */
function $(sel){return document.querySelector(sel)}
function randInt(n){return Math.floor(Math.random()*n)}
function formatTime(s){
  const m = Math.floor(s/60).toString().padStart(2,'0');
  const sec = (s%60).toString().padStart(2,'0');
  return `${m}:${sec}`;
}

/* Init UI */
function renderThemeList(){
  themeListEl.innerHTML = '';
  THEMES.forEach((t,i)=>{
    const btn = document.createElement('div');
    btn.className = 'theme-item' + (i===state.themeIndex?' active':'');
    btn.textContent = t.name;
    btn.onclick = ()=>{ state.themeIndex = i; renderThemeList(); }
    themeListEl.appendChild(btn);
  });
}
function renderPlayers(){
  playersEl.innerHTML = '';
  for(let i=0;i<state.playerCount;i++){
    const div = document.createElement('div');
    div.className = 'player-input';
    const input = document.createElement('input');
    input.type='text';
    input.placeholder = `Игрок ${i+1}`;
    input.value = state.players[i] || '';
    input.oninput = (e)=> state.players[i]=e.target.value;
    div.appendChild(input);
    playersEl.appendChild(div);
  }
}

/* Auto names */
autoNamesBtn.onclick = ()=>{
  const sample = ['Аня','Борис','Вика','Глеб','Дима','Ева','Женя'];
  state.players = Array.from({length:state.playerCount},(_,i)=>sample[i%sample.length]);
  renderPlayers();
}

/* Player count change */
playerCountEl.onchange = (e)=>{
  state.playerCount = parseInt(e.target.value,10);
  state.players = state.players.slice(0,state.playerCount);
  renderPlayers();
}

/* Mode and time */
modeEl.onchange = (e)=> state.mode = e.target.value;
roundTimeEl.onchange = (e)=> state.roundTime = Math.max(30, parseInt(e.target.value,10) || 180);

/* Start game: assign spy and cards */
startBtn.onclick = ()=>{
  // validate players
  const names = [];
  for(let i=0;i<state.playerCount;i++){
    const val = (state.players[i] || '').trim() || `Игрок ${i+1}`;
    names.push(val);
  }
  state.players = names;
  state.spyIndex = randInt(state.playerCount);
  state.cardsAssigned = assignCards();
  state.currentViewer = 0;
  state.roundActive = false;
  state.timeLeft = state.roundTime;
  updateUI();
  alert('Игра готова. Передавайте устройство первому игроку и нажмите Show my card.');
}

/* Assign cards logic */
function assignCards(){
  const theme = THEMES[state.themeIndex];
  // shuffle theme cards and pick random base word
  const pool = [...theme.cards];
  // shuffle
  for(let i=pool.length-1;i>0;i--){ const j=randInt(i+1); [pool[i],pool[j]]=[pool[j],pool[i]]; }
  // choose base words for this round: pick one card for all non-spies
  const base = pool[0];
  const assigned = [];
  for(let i=0;i<state.playerCount;i++){
    if(i===state.spyIndex){
      if(state.mode==='classic'){
        assigned.push({type:'spy', text:'SPY'});
      } else {
        // hidden: give spy an alt (similar) word
        assigned.push({type:'spy', text: base.alt || base.word});
      }
    } else {
      assigned.push({type:'player', text: base.word});
    }
  }
  return assigned;
}

/* UI update */
function updateUI(){
  renderPlayers();
  renderThemeList();
  currentPlayerEl.textContent = state.players[state.currentViewer] || '—';
  cardView.textContent = '—';
  timerDisplay.textContent = formatTime(state.timeLeft || 0);
  roundInfoText.textContent = state.roundActive ? 'Обсуждение' : 'Просмотр карточек';
  spyInfo.textContent = state.mode==='classic' ? `Шпион: ${state.spyIndex+1}` : 'Шпион: скрыт';
}

/* Show card modal */
showCardBtn.onclick = ()=>{
  const idx = state.currentViewer;
  const card = state.cardsAssigned[idx];
  if(!card){ alert('Сначала нажмите Start'); return; }
  modalTitle.textContent = state.players[idx];
  modalBody.textContent = card.text;
  modal.classList.add('show');
  // auto-hide after 8s
  setTimeout(()=>{ if(modal.classList.contains('show')) modal.classList.remove('show') }, 8000);
}
modalOk.onclick = ()=> modal.classList.remove('show');
modalHide.onclick = ()=> modal.classList.remove('show');

/* Hide card (just clears view) */
hideCardBtn.onclick = ()=>{
  cardView.textContent = '—';
}

/* Next player */
nextPlayerBtn.onclick = ()=>{
  state.currentViewer = (state.currentViewer + 1) % state.playerCount;
  currentPlayerEl.textContent = state.players[state.currentViewer];
  // if cycled back to 0, show hint
  if(state.currentViewer===0){
    alert('Все игроки просмотрели карточки. Нажмите Start Round для начала таймера обсуждения.');
  }
}

/* Start round timer */
startRoundBtn.onclick = ()=>{
  if(state.roundActive){ stopTimer(); return; }
  state.roundActive = true;
  state.timeLeft = state.roundTime;
  roundInfoText.textContent = 'Обсуждение';
  startTimer();
}

/* Timer functions */
function startTimer(){
  if(state.timer) clearInterval(state.timer);
  timerDisplay.textContent = formatTime(state.timeLeft);
  state.timer = setInterval(()=>{
    state.timeLeft--;
    timerDisplay.textContent = formatTime(state.timeLeft);
    if(state.timeLeft<=0){
      stopTimer();
      alert('Время вышло!');
    }
  },1000);
}
function stopTimer(){
  if(state.timer) clearInterval(state.timer);
  state.timer = null;
  state.roundActive = false;
  roundInfoText.textContent = 'Ожидание';
}

/* Reset */
resetBtn.onclick = ()=>{
  if(confirm('Сбросить игру?')){
    state = {
      players: [],
      playerCount: parseInt(playerCountEl.value,10),
      themeIndex: state.themeIndex,
      mode: modeEl.value,
      roundTime: parseInt(roundTimeEl.value,10),
      spyIndex: null,
      cardsAssigned: [],
      currentViewer: 0,
      timer: null,
      timeLeft: 0,
      roundActive: false
    };
    renderPlayers();
    renderThemeList();
    updateUI();
  }
}

/* Quick keyboard shortcuts */
document.addEventListener('keydown',(e)=>{
  if(e.key===' '){ e.preventDefault(); showCardBtn.click(); }
  if(e.key==='Enter'){ nextPlayerBtn.click(); }
});

/* initial render */
renderThemeList();
renderPlayers();
updateUI();
</script>
</body>
</html>
