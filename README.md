# LumenX-
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Клубная игра "Барашек"
    </title>
    <style>
        body { font-family: Arial, sans-serif; background: #f5f5f5; text-align: center; margin: 0; padding: 20px; }
        h1 { color: #333; }
        #info { margin-bottom: 10px; }
        #grid { display: grid; grid-template-columns: repeat(6, 60px); grid-gap: 8px; justify-content: center; }
        .tile { width: 60px; height: 60px; background: #fff; border: 2px solid #ccc; border-radius: 8px; font-size: 32px; display: flex; align-items: center; justify-content: center; cursor: pointer; }
        .tile.selected { border-color: #007bff; box-shadow: 0 0 10px rgba(0,123,255,0.5); }
        #quiz-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); display: flex; align-items: center; justify-content: center; }
        #quiz-content { background: #fff; padding: 20px; border-radius: 8px; max-width: 600px; width: 90%; text-align: left; }
        .hidden { display: none; }
        .question { margin-bottom: 15px; }
        .question p { margin: 0 0 5px; font-weight: bold; }
        .question label { display: block; margin-bottom: 5px; }
        button { padding: 8px 16px; margin-top: 10px; border: none; background: #007bff; color: #fff; border-radius: 4px; cursor: pointer; }
    </style>
</head>
<body>
    <h1>Клубная игра "Барашек"</h1>
    <div id="info">Уровень: <span id="level">1</span> | Жизни: <span id="lives">3</span></div>
    <div id="grid"></div>

    <div id="quiz-modal" class="hidden">
        <div id="quiz-content">
            <h2>Вопрос для возрождения</h2>
            <form id="quiz-form">
                <!-- Вопросы вставляются скриптом -->
            </form>
            <button id="submit-quiz">Отправить ответы</button>
        </div>
    </div>

    <script>
        const icons = ['🐑','🐏','🐐','🐑'];
        let level = 1;
        let lives = 3;
        const gridEl = document.getElementById('grid');
        const levelEl = document.getElementById('level');
        const livesEl = document.getElementById('lives');
        let selected = [];

        function initGrid() {
            gridEl.innerHTML = '';
            for (let i=0;i<36;i++) {
                const tile = document.createElement('div');
                tile.className = 'tile';
                tile.dataset.icon = icons[Math.floor(Math.random()*icons.length)];
                tile.textContent = tile.dataset.icon;
                tile.addEventListener('click', ()=>onTileClick(tile));
                gridEl.append(tile);
            }
        }

        function onTileClick(tile) {
            if (selected.includes(tile)) return;
            tile.classList.add('selected');
            selected.push(tile);
            if (selected.length===3) {
                const types = selected.map(t=>t.dataset.icon);
                if (types.every(x=>x===types[0])) {
                    selected.forEach(t=>t.remove());
                } else {
                    lives--;
                    livesEl.textContent = lives;
                    selected.forEach(t=>t.classList.remove('selected'));
                }
                selected = [];
                if (lives<=0) { showQuiz(); }
                if (!gridEl.querySelector('.tile')) { nextLevel(); }
            }
        }

        function nextLevel() {
            level++;
            levelEl.textContent = level;
            lives = 3;
            livesEl.textContent = lives;
            initGrid();
        }

        // Вопросы
        const quizQuestions = [
            { q: '1. При участии в торговых сигналах сумма каждой сделки составляет?', opts:['2%','5%','10%','50%'], correct:[0] },
            { q: '2. Новые члены группы могут предпринять какие действия?(мультивыбор)', opts:['Участвовать в официальных сигналах','Общаться и делиться личным','Записывать видео на 10 USDT','Задавать вопросы'], correct:[0,1,2,3] },
            { q: '3. Когда можно выводить прибыль?', opts:['После привязки карты','После проверки','После фото ID','После KYC в кошельке'], correct:[3] },
            { q: '4. Откуда прибыль C8?', opts:['Реферальные','Пул платформы','30% комиссии с трафика','Субсидии'], correct:[2] },
            { q: '5. Ежедневная прибыль на этапе C2?', opts:['1.5','4.8','5.6','11.2'], correct:[3] },
            { q: '6. Какие 2 бонуса в день вступления?', opts:['1 300 руб за видео','Награда или подарочная карта','Бесплатные курсы','Повышение уровня'], correct:[0,1] }
        ];

        function showQuiz() {
            document.getElementById('quiz-modal').classList.remove('hidden');
            const form = document.getElementById('quiz-form');
            form.innerHTML='';
            quizQuestions.forEach((item,i)=>{
                const div = document.createElement('div'); div.className='question';
                const p = document.createElement('p'); p.textContent = item.q;
                div.append(p);
                item.opts.forEach((opt,j)=>{
                    const label = document.createElement('label');
                    const inp = document.createElement('input');
                    inp.type = item.correct.length>1?'checkbox':'radio';
                    inp.name = 'q'+i;
                    inp.value = j;
                    label.append(inp, ' ', opt);
                    div.append(label);
                });
                form.append(div);
            });
        }
        document.getElementById('submit-quiz').addEventListener('click', ()=>{
            const form = document.getElementById('quiz-form');
            let allCorrect = true;
            quizQuestions.forEach((item,i)=>{
                const inputs = Array.from(form.querySelectorAll(`[name=q${i}]`));
                const vals = inputs.filter(inp=>inp.checked).map(inp=>parseInt(inp.value));
                if (vals.length!==item.correct.length || !item.correct.every(c=>vals.includes(c))) allCorrect=false;
            });
            document.getElementById('quiz-modal').classList.add('hidden');
            if (allCorrect) { lives=3; livesEl.textContent=lives; } else { alert('Неправильно! Игра окончена.'); location.reload(); }
        });

        // Запуск
        initGrid();
    </script>
</body>
</html>
