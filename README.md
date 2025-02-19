# gamehtml
!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>بازی ریاضی چالشی</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
            direction: rtl;
            background: url('https://upload.wikimedia.org/wikipedia/commons/5/5c/Flag_of_Afghanistan_%282021%29.svg') no-repeat center center fixed;
            background-size: cover;
            color: rgb(24, 218, 33);
        }
        button { 
            padding: 10px 20px; 
            font-size: 18px; 
            margin: 10px; 
            cursor: pointer; 
            background: black; 
            color: rgb(235, 32, 32);
            border: 2px solid rgb(36, 17, 211);
        }
        #message { font-size: 20px; font-weight: bold; margin-top: 20px; }
        #timer, #score { font-size: 18px; font-weight: bold; }
        #timer { color: red; }
        #score { color: rgba(17, 17, 10, 0.884); }
    </style>
</head>
<body>
    <h1>🎮 بازی ریاضی چالشی 🎮</h1>
    <p id="question">یک عدد را حدس بزن:</p>
    <div id="options"></div>
    <p id="message"></p>
    <p id="timer">⏳ زمان باقی‌مانده: <span id="timeLeft">10</span> ثانیه</p>
    <p id="score">⭐ امتیاز: <span id="playerScore">0</span></p>
    <button onclick="startGame()">🔄 شروع مجدد</button>

    <audio id="correctSound" src="https://www.fesliyanstudios.com/play-mp3/4381"></audio>
    <audio id="wrongSound" src="https://www.fesliyanstudios.com/play-mp3/4392"></audio>

    <script>
        let stage = 1;
        let correctAnswer;
        let options = [];
        let score = 0;
        let timer;
        let timeLeft = 10;
        const totalStages = 10;

        function startGame() {
            stage = 1;
            score = 0;
            updateScore();
            nextStage();
        }

        function nextStage() {
            clearInterval(timer);
            timeLeft = 10;
            document.getElementById("timeLeft").textContent = timeLeft;
            startTimer();

            let questionText = document.getElementById("question");
            let optionsDiv = document.getElementById("options");
            let message = document.getElementById("message");

            optionsDiv.innerHTML = "";
            message.textContent = "";

            let num1 = Math.floor(Math.random() * (stage * 10)) + 1;
            let num2 = Math.floor(Math.random() * (stage * 5)) + 1;

            switch (stage) {
                case 1:
                    correctAnswer = Math.floor(Math.random() * 100) + 1;
                    questionText.textContent = "یک عدد بین ۱ تا ۱۰۰ حدس بزن:";
                    generateOptions(correctAnswer, 1, 100);
                    break;
                case 2:
                    correctAnswer = num1 * num2;
                    questionText.textContent = `حاصل ضرب ${num1} × ${num2} چیست؟`;
                    generateOptions(correctAnswer, 1, 200);
                    break;
                case 3:
                    correctAnswer = num1 - num2;
                    questionText.textContent = `حاصل تفریق ${num1} - ${num2} چیست؟`;
                    generateOptions(correctAnswer, -50, 100);
                    break;
                case 4:
                    num1 = (Math.floor(Math.random() * 10) + 1) * (Math.floor(Math.random() * 5) + 1);
                    num2 = Math.floor(Math.random() * 5) + 1;
                    correctAnswer = num1 / num2;
                    questionText.textContent = `حاصل تقسیم ${num1} ÷ ${num2} چیست؟`;
                    generateOptions(correctAnswer, 1, 20);
                    break;
                case 5:
                    correctAnswer = num1 + num2;
                    questionText.textContent = `حاصل جمع ${num1} + ${num2} چیست؟`;
                    generateOptions(correctAnswer, 1, 200);
                    break;
                case 6:
                    correctAnswer = Math.pow(num1, 2);
                    questionText.textContent = `مجذور ${num1} چیست؟`;
                    generateOptions(correctAnswer, 1, 500);
                    break;
                case 7:
                    correctAnswer = Math.pow(num1, 3);
                    questionText.textContent = `مکعب ${num1} چیست؟`;
                    generateOptions(correctAnswer, 1, 1000);
                    break;
                case 8:
                    correctAnswer = num1 % num2;
                    questionText.textContent = `باقی‌مانده ${num1} ÷ ${num2} چیست؟`;
                    generateOptions(correctAnswer, 0, 10);
                    break;
                case 9:
                    correctAnswer = Math.sqrt(num1).toFixed(2);
                    questionText.textContent = `ریشه دوم ${num1} (تقریبی) چیست؟`;
                    generateOptions(correctAnswer, 1, 20);
                    break;
                case 10:
                    questionText.textContent = "🎉 تبریک! شما همه مراحل را پشت سر گذاشتید!";
                    return;
            }

            options.forEach(num => {
                let btn = document.createElement("button");
                btn.textContent = num;
                btn.onclick = () => checkAnswer(num);
                optionsDiv.appendChild(btn);
            });
        }

        function generateOptions(correct, min, max) {
            options = [correct];

            while (options.length < 4) {
                let num = Math.floor(Math.random() * (max - min + 1)) + min;
                if (!options.includes(num)) {
                    options.push(num);
                }
            }

            options.sort(() => Math.random() - 0.5);
        }

        function checkAnswer(selectedNumber) {
            let message = document.getElementById("message");
            let correctSound = document.getElementById("correctSound");
            let wrongSound = document.getElementById("wrongSound");

            if (selectedNumber == correctAnswer) {
                message.innerHTML = "✅ درست است! 🎉";
                message.style.color = "green";
                correctSound.play();
                score += timeLeft * 10; 
                updateScore();
                stage++;
                clearInterval(timer);
                setTimeout(nextStage, 1000);
            } else {
                message.innerHTML = "❌ اشتباه! دوباره امتحان کن.";
                message.style.color = "red";
                wrongSound.play();
                setTimeout(() => wrongSound.pause(), 1000);
            }
        }

        function startTimer() {
            timer = setInterval(() => {
                timeLeft--;
                document.getElementById("timeLeft").textContent = timeLeft;
                if (timeLeft === 0) {
                    clearInterval(timer);
                    alert("⏳ زمان تمام شد! بازی از اول شروع می‌شود.");
                    startGame();
                }
            }, 1000);
        }

        function updateScore() {
            document.getElementById("playerScore").textContent = score;
        }

        window.onload = startGame;
    </script>
</body>
</html>
