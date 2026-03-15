# logicheska_gradina2
gradina
<!DOCTYPE html>
<html lang="bg">
<head>
    <meta charset="UTF-8">
    <title>Логическа STEM Градина</title>
    <link href="https://fonts.googleapis.com/css2?family=Comfortaa:wght@700&display=swap" rel="stylesheet">
    <style>
        body { 
            background: linear-gradient(135deg, #e3f2fd 0%, #f1f8e9 100%); 
            font-family: 'Comfortaa', cursive; 
            display: flex; justify-content: center; align-items: center; 
            height: 100vh; margin: 0; 
        }
        #game-card { 
            background: #ffffff; border: 10px solid #43a047; border-radius: 50px; 
            padding: 40px; text-align: center; width: 550px; 
            box-shadow: 0 20px 40px rgba(0,0,0,0.1); 
        }
        #stage-info { font-size: 22px; color: #1b5e20; margin-bottom: 10px; }
        #visual-box { font-size: 120px; height: 150px; margin: 15px 0; }
        .equation { font-size: 55px; color: #d32f2f; margin: 20px 0; font-weight: bold; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        button { 
            background: #ffca28; border: none; padding: 20px; font-size: 35px; 
            border-radius: 25px; cursor: pointer; font-family: 'Comfortaa'; 
            border-bottom: 6px solid #ff8f00; transition: 0.1s;
        }
        button:active { transform: translateY(4px); border-bottom-width: 2px; }
        #feedback { margin-top: 25px; font-size: 20px; font-weight: bold; min-height: 25px; }
        .pop { animation: popAnim 0.5s ease-out; }
        @keyframes popAnim { 0% { transform: scale(0.6); } 100% { transform: scale(1); } }
    </style>
</head>
<body>

<div id="game-card">
    <div id="stage-info">Етап 1: Засаждане</div>
    <div id="visual-box" class="pop">🫘</div>
    <div class="equation" id="quest">? + 5 = 12</div>
    <div class="grid" id="options"></div>
    <div id="feedback">Намери липсващото число!</div>
</div>

<audio id="birds" src="https://www.soundjay.com/nature/sounds/birds-chirping-05.mp3"></audio>

<script>
    let step = 0;
    let correctAnswer;
    const stages = [
        { icon: '🫘', text: '1. Поставихме семенцето в земята' },
        { icon: '💧', text: '2. Нужна е вода, за да се събуди' },
        { icon: '🌱', text: '3. Виж! Първото кълнче се появи' },
        { icon: '🌿', text: '4. Стъблото расте смело нагоре' },
        { icon: '🍃', text: '5. Слънцето погали първите листа' },
        { icon: '🌳', text: '6. Растението вече е голямо' },
        { icon: '🎋', text: '7. Появи се малка зелена пъпка' },
        { icon: '🪻', text: '8. Пъпката започна да се отваря' },
        { icon: '🌷', text: '9. Разцъфна прекрасно цвете!' },
        { icon: '💐', text: '10. Вече имаме букет за вазата!' }
    ];

    function generateLogicTask() {
        const type = Math.floor(Math.random() * 4); 
        let a, b, res;

        // Генератор на задачи до 20 (с и без преминаване)
        if (type === 0 || type === 2) { // Събиране: ? + b = res или a + ? = res
            res = Math.floor(Math.random() * 16) + 5; // Резултат между 5 и 20
            a = Math.floor(Math.random() * (res + 1));
            b = res - a;
            if (Math.random() > 0.5) {
                document.getElementById('quest').innerText = `? + ${b} = ${res}`;
                correctAnswer = a;
            } else {
                document.getElementById('quest').innerText = `${a} + ? = ${res}`;
                correctAnswer = b;
            }
        } else { // Изваждане: ? - b = res или a - ? = res
            a = Math.floor(Math.random() * 11) + 10; // Намаляемо между 10 и 20
            b = Math.floor(Math.random() * (a + 1)); // Омалител
            res = a - b; // Разлика
            if (Math.random() > 0.5) {
                document.getElementById('quest').innerText = `? - ${b} = ${res}`;
                correctAnswer = a;
            } else {
                document.getElementById('quest').innerText = `${a} - ? = ${res}`;
                correctAnswer = b;
            }
        }
        renderButtons();
    }

    function renderButtons() {
        let options = [correctAnswer];
        while(options.length < 4) {
            let w = Math.floor(Math.random() * 21);
            if(!options.includes(w)) options.push(w);
        }
        options.sort((x, y) => x - y);
        
        const container = document.getElementById('options');
        container.innerHTML = '';
        options.forEach(opt => {
            const b = document.createElement('button');
            b.innerText = opt;
            b.onclick = () => check(opt, b);
            container.appendChild(b);
        });
    }

    function check(val, btn) {
        if(val === correctAnswer) {
            step++;
            btn.style.background = "#c8e6c9";
            document.getElementById('feedback').innerText = "Браво! Ти си математик! ✨";
            document.getElementById('feedback').style.color = "#2e7d32";
            setTimeout(nextStage, 1000);
        } else {
            btn.style.background = "#ffcdd2";
            document.getElementById('feedback').innerText = "Помисли пак! Можеш го! 💧";
            document.getElementById('feedback').style.color = "#c62828";
        }
    }

    function nextStage() {
        if(step >= stages.length) {
            document.getElementById('stage-info').innerText = "УСПЕХ!";
            document.getElementById('visual-box').innerText = "💐🏺";
            document.getElementById('quest').innerText = "10/10";
            document.getElementById('feedback').innerText = "Всички цветя разцъфнаха благодарение на теб!";
            document.getElementById('options').innerHTML = '<button onclick="location.reload()" style="grid-column: 1/3; background: #81c784;">Нова игра</button>';
            document.getElementById('birds').play();
            return;
        }
        document.getElementById('stage-info').innerText = stages[step].text;
        document.getElementById('visual-box').innerText = stages[step].icon;
        document.getElementById('feedback').innerText = "";
        generateLogicTask();
    }

    generateLogicTask();
</script>
</body>
</html>
