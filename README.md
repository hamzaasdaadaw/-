<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>لعبة التحدي الذكي</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background: url('https://images.unsplash.com/photo-1507525428034-b723cf961d3e') no-repeat center center fixed;
      background-size: cover;
      margin: 0;
      padding: 0;
      text-align: center;
      direction: rtl;
      color: white;
    }
    .screen {
      display: none;
      padding: 40px;
      background-color: rgba(0, 0, 0, 0.6);
      min-height: 100vh;
    }
    .active {
      display: block;
    }
    button {
      padding: 10px 20px;
      font-size: 18px;
      margin: 10px;
      border-radius: 10px;
      border: none;
      background-color: #3498db;
      color: white;
      cursor: pointer;
    }
    #question {
      font-size: 24px;
      margin-bottom: 20px;
      animation: fadeIn 1s ease-in-out;
    }
    .answer {
      display: block;
      margin: 10px auto;
      padding: 10px;
      font-size: 20px;
      background-color: #ecf0f1;
      color: black;
      width: 50%;
      border-radius: 10px;
      cursor: pointer;
      transition: transform 0.2s, background-color 0.3s;
    }
    .answer:hover {
      background-color: #bdc3c7;
      transform: scale(1.05);
    }
    #timer {
      font-size: 24px;
      color: yellow;
      margin-top: 20px;
    }
    .feedback {
      font-size: 26px;
      margin-top: 15px;
      font-weight: bold;
      animation: fadeIn 0.5s;
    }
    .level-info {
      font-size: 20px;
      margin-top: 10px;
    }
    #control-panel {
      font-size: 18px;
      margin-bottom: 20px;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body>

  <div id="start-screen" class="screen active">
    <h1>🎉 لعبة التحدي الذكي 🎉</h1>
    <button onclick="startGame()">ابدأ اللعبة</button>
  </div>

  <div id="game-screen" class="screen">
    <div id="control-panel">
      <div id="level-info" class="level-info"></div>
      <div id="score-info">النقاط: 0</div>
      <div id="timer"></div>
    </div>
    <div id="question"></div>
    <div id="answers"></div>
    <div id="feedback"></div>
    <button id="next-button" style="display:none; margin-top: 20px;" onclick="nextQuestion()">السؤال التالي</button>
  </div>

  <div id="end-screen" class="screen">
    <h2>🎊 انتهت اللعبة! 🎊</h2>
    <p id="score"></p>
    <button onclick="location.reload()">العب مرة أخرى</button>
  </div>

  <audio id="correct-sound" src="https://www.soundjay.com/button/sounds/button-4.mp3"></audio>
  <audio id="wrong-sound" src="https://www.soundjay.com/button/sounds/button-10.mp3"></audio>
  <audio id="timeup-sound" src="https://www.soundjay.com/button/sounds/beep-07.mp3"></audio>
  <audio id="bg-music" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_8cb5f1fa2c.mp3?filename=chill-ambient-11184.mp3" loop autoplay></audio>

  <script>
    const allQuestions = [
      { question: "ما هي عاصمة المملكة العربية السعودية؟", answers: ["الرياض", "جدة", "مكة", "الدمام"], correct: 0 },
      { question: "كم عدد أركان الإسلام؟", answers: ["3", "4", "5", "6"], correct: 2 },
      { question: "ما هو أطول نهر في العالم؟", answers: ["الأمازون", "النيل", "الفرات", "الدانوب"], correct: 1 },
      { question: "في أي قارة تقع مصر؟", answers: ["آسيا", "أوروبا", "أفريقيا", "أمريكا"], correct: 2 },
      { question: "ما هو العنصر الكيميائي الذي يرمز له بـ O؟", answers: ["ذهب", "أوكسجين", "هيدروجين", "حديد"], correct: 1 },
      { question: "ما اسم أكبر محيط في العالم؟", answers: ["المحيط الأطلسي", "المحيط الهندي", "المحيط الهادي", "المحيط المتجمد"], correct: 2 },
      { question: "ما هو الحيوان الذي يُلقب بملك الغابة؟", answers: ["النمر", "الأسد", "الفهد", "الدب"], correct: 1 },
      { question: "ما اسم كوكب يُعرف بالكوكب الأحمر؟", answers: ["الزهرة", "المشتري", "المريخ", "عطارد"], correct: 2 },
      { question: "من هو أول نبي؟", answers: ["نوح", "إبراهيم", "آدم", "عيسى"], correct: 2 },
      { question: "كم عدد قارات العالم؟", answers: ["5", "6", "7", "8"], correct: 2 },
      { question: "ما هو أقوى حيوان في العالم؟", answers: ["الفيل", "الأسد", "الدب", "الخنفساء"], correct: 3 },
      { question: "أي كوكب هو الأقرب إلى الشمس؟", answers: ["الزهرة", "عطارد", "المريخ", "المشتري"], correct: 1 },
      { question: "ما هو أكبر بحيرة في العالم؟", answers: ["بحيرة خوارزمي", "بحيرة قزوين", "بحيرة البحيرات الكبرى", "بحيرة التنين"], correct: 1 },
      { question: "أين تقع مدينة نيوزيلندا؟", answers: ["أمريكا الجنوبية", "أوروبا", "المحيط الهادئ", "أفريقيا"], correct: 2 },
      { question: "ما هي لغة البرمجة الأكثر استخدامًا؟", answers: ["جافا", "سي شارب", "بايثون", "جافا سكربت"], correct: 3 },
      { question: "من هو مؤلف كتاب 1984؟", answers: ["أرنست همنغواي", "جورج أورويل", "دوستويفسكي", "فكتور هوغو"], correct: 1 },
      { question: "ما هي أقوى مادة طبيعية؟", answers: ["الذهب", "الماس", "الحديد", "البلاتين"], correct: 1 },
      { question: "أي حيوان يمتلك أطول عنق؟", answers: ["الفيل", "الزرافة", "الحوت", "الدب"], correct: 1 },
      { question: "أين يقع أكبر منتجع سياحي في العالم؟", answers: ["دبي", "باريس", "لاس فيغاس", "موسكو"], correct: 0 },
      { question: "من هو مبتكر الهاتف المحمول؟", answers: ["ستيف جوبز", "مارتن كوبر", "مارك زوكربيرج", "بيل غيتس"], correct: 1 }
    ];

    function shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    }

    let questions = [];
    let currentQuestion = 0;
    let score = 0;
    let level = 1; 
    const timeLimit = 15;
    let timer;

    function startGame() {
      document.getElementById('start-screen').classList.remove('active');
      document.getElementById('game-screen').classList.add('active');
      const music = document.getElementById('bg-music');
      music.muted = false;
      music.play();

      shuffleArray(allQuestions);
      questions = allQuestions.slice(0, 20); // 20 سؤال
      currentQuestion = 0;
      score = 0;
      level = 1;
      showQuestion();
    }

    function showQuestion() {
      document.getElementById('next-button').style.display = 'none';
      if (currentQuestion >= questions.length) return endGame();
      document.getElementById('feedback').textContent = '';
      const q = questions[currentQuestion];
      const questionDiv = document.getElementById('question');
      questionDiv.style.opacity = 0;
      setTimeout(() => {
        questionDiv.textContent = q.question;
        questionDiv.style.opacity = 1;
      }, 200);

      const answersDiv = document.getElementById('answers');
      answersDiv.innerHTML = '';
      q.answers.forEach((ans, idx) => {
        const btn = document.createElement('button');
        btn.className = 'answer';
        btn.textContent = ans;
        btn.onclick = () => checkAnswer(idx);
        answersDiv.appendChild(btn);
      });

      document.getElementById('level-info').textContent = `المستوى: ${level}`;
      document.getElementById('score-info').textContent = `النقاط: ${score}`;
      startTimer(timeLimit);
    }

    function checkAnswer(selected) {
      clearInterval(timer);
      const correct = questions[currentQuestion].correct;
      const feedback = document.getElementById('feedback');
      if (selected === correct) {
        document.getElementById('correct-sound').play();
        feedback.textContent = '✔️ إجابة صحيحة!';
        feedback.style.color = 'lightgreen';
        score++;
      } else {
        document.getElementById('wrong-sound').play();
        feedback.textContent = `❌ إجابة خاطئة! الإجابة الصحيحة هي: ${questions[currentQuestion].answers[correct]}`;
        feedback.style.color = 'tomato';
      }
      document.getElementById('next-button').style.display = 'inline-block';
    }

    function startTimer(timeLimit) {
      let time = timeLimit;
      const timerDiv = document.getElementById('timer');
      timerDiv.textContent = `⏳ الوقت المتبقي: ${time}`;
      timer = setInterval(() => {
        time--;
        timerDiv.textContent = `⏳ الوقت المتبقي: ${time}`;
        if (time <= 0) {
          clearInterval(timer);
          document.getElementById('timeup-sound').play();
          document.getElementById('feedback').textContent = '⏰ انتهى الوقت!';
          document.getElementById('feedback').style.color = 'orange';
          document.getElementById('next-button').style.display = 'inline-block';
        }
      }, 1000);
    }

    function nextQuestion() {
      currentQuestion++;
      showQuestion();
    }

    function endGame() {
      document.getElementById('game-screen').classList.remove('active');
      document.getElementById('end-screen').classList.add('active');
      document.getElementById('score').textContent = `درجتك: ${score} من ${questions.length}`;
    }
  </script>
</body>
</html>
