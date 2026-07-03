<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>中堅大学入試レベル 英単語穴埋めクイズ Premium Effect</title>
  
  <style>
    body {
      font-family: 'Helvetica Neue', Arial, sans-serif;
      background-color: #f5f7fa;
      color: #333;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      margin: 0;
      padding: 20px 0;
      overflow-x: hidden; /* エフェクトの飛び出しによる横スクロール防止 */
      position: relative;
    }

    .quiz-container {
      max-width: 600px;
      width: 90%;
      padding: 30px;
      border-radius: 12px;
      background-color: #ffffff;
      box-shadow: 0 8px 20px rgba(0, 0, 0, 0.05);
      position: relative;
      z-index: 10; /* エフェクトの下に隠れないように */
    }

    h2 {
      font-size: 20px;
      text-align: center;
      color: #2b6cb0;
      margin-top: 0;
      margin-bottom: 10px;
    }

    .progress {
      text-align: center;
      font-size: 14px;
      color: #718096;
      margin-bottom: 10px;
    }

    .timer-container {
      width: 100%;
      height: 6px;
      background-color: #e2e8f0;
      border-radius: 3px;
      margin-bottom: 20px;
      overflow: hidden;
    }

    .timer-bar {
      height: 100%;
      width: 100%;
      background-color: #e53e3e;
      transition: width 1s linear;
    }

    .question {
      font-size: 18px;
      font-weight: bold;
      line-height: 1.6;
      margin-bottom: 25px;
      padding: 15px;
      background-color: #edf2f7;
      border-left: 5px solid #3182ce;
      border-radius: 4px;
    }

    .choices-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
      margin-bottom: 20px;
    }

    @media (max-width: 480px) {
      .choices-grid {
        grid-template-columns: 1fr;
      }
    }

    .choice-btn {
      padding: 14px;
      font-size: 16px;
      background-color: #ffffff;
      border: 2px solid #e2e8f0;
      border-radius: 8px;
      cursor: pointer;
      font-weight: 500;
      text-align: left;
      transition: all 0.2s ease;
    }

    .choice-btn:hover:not(:disabled) {
      background-color: #f7fafc;
      border-color: #cbd5e0;
      transform: translateY(-1px);
    }

    .choice-btn:disabled {
      cursor: not-allowed;
    }

    #result-message {
      white-space: pre-line;
      font-weight: bold;
      margin: 20px 0;
      padding: 15px;
      border-radius: 6px;
      display: none;
      line-height: 1.5;
    }

    .action-btn {
      display: block;
      width: 100%;
      padding: 14px;
      font-size: 16px;
      background-color: #3182ce;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
      transition: background 0.2s;
      margin-top: 10px;
    }

    .action-btn:hover {
      background-color: #2b6cb0;
    }

    .mode-selection {
      text-align: center;
    }

    .mode-btn {
      display: block;
      width: 100%;
      padding: 15px;
      margin: 15px 0;
      font-size: 18px;
      font-weight: bold;
      background-color: #ebf8ff;
      border: 2px solid #bee3f8;
      color: #2b6cb0;
      border-radius: 8px;
      cursor: pointer;
      transition: all 0.2s;
    }

    .mode-btn:hover {
      background-color: #3182ce;
      color: white;
      border-color: #3182ce;
    }

    /* 👇 動的に生成される絵文字エフェクトのCSSアニメーション */
    .emoji-particle {
      position: fixed;
      pointer-events: none;
      z-index: 9999;
      font-size: 24px;
      animation: flyAround 1.2s cubic-bezier(0.25, 1, 0.5, 1) forwards;
    }

    @keyframes flyAround {
      0% {
        transform: translate(0, 0) scale(0.5) rotate(0deg);
        opacity: 1;
      }
      100% {
        transform: translate(var(--x), var(--y)) scale(1.5) rotate(var(--r));
        opacity: 0;
      }
    }
  </style>
</head>
<body>

  <div id="quiz-wrapper" class="quiz-container">
    <div id="start-screen" class="mode-selection">
      <h2>英文穴埋めクイズ（中堅大レベル）</h2>
      <p style="color: #718096; margin-bottom: 30px;">どちらのモードで挑戦しますか？（制限時間: 1問15秒）</p>
      <button class="mode-btn" onclick="startQuiz('mini')">⏱️ ランダム10問ミニテスト</button>
      <button class="mode-btn" onclick="startQuiz('all')">🔥 50問全問マスターコース</button>
    </div>

    <div id="quiz-screen" style="display: none;">
      <h2 id="quiz-title">英文穴埋めクイズ</h2>
      <div id="progress-bar" class="progress"></div>
      
      <div class="timer-container">
        <div id="timer-bar" class="timer-bar"></div>
      </div>
      
      <p id="question-text" class="question"></p>
      <div id="choices-container" class="choices-grid"></div>
      <div id="result-message"></div>
      <button id="next-btn" class="action-btn" style="display: none;" onclick="nextQuestion()">次の問題へ</button>
    </div>
  </div>

  <script>
    const masterData = [
      { q: "The company decided to ( _ ) the new technology to improve efficiency.", c: ["adopt", "adapt", "addict", "admit"], a: "adopt", e: "adopt（〜を採用する）。効率向上のため新技術を採用した。" },
      { q: "Due to the heavy rain, the outdoor concert was ( _ ) until next Sunday.", c: ["put off", "called off", "taken off", "kept off"], a: "put off", e: "put off（〜を延期する）。大雨のため来週に延期された。" },
      { q: "She has the ( _ ) to speak three languages fluently.", c: ["ability", "disability", "probability", "stability"], a: "ability", e: "ability（能力）。have the ability to doで「〜する能力がある」" },
      { q: "The government is trying to ( _ ) economic growth.", c: ["stimulate", "simulate", "accumulate", "calculate"], a: "stimulate", e: "stimulate（〜を刺激する、促す）。経済成長を促そうとしている。" },
      { q: "We must take immediate action to protect ( _ ) species.", c: ["endangered", "dangerous", "endured", "damaging"], a: "endangered", e: "endangered speciesで「絶滅危惧種」という超頻出フレーズ。" },
      { q: "He is ( _ ) of completing the project on his own.", c: ["capable", "able", "competent", "possible"], a: "capable", e: "be capable of -ing で「〜する能力がある、〜できる」。" },
      { q: "The internet has a huge ( _ ) on our daily lives.", c: ["influence", "affection", "infliction", "ingredient"], a: "influence", e: "have a huge influence on ~ で「〜に巨大な影響を与える」。" },
      { q: "The two countries signed a peace ( _ ) to end the war.", c: ["treaty", "threat", "treatment", "treasury"], a: "treaty", e: "treaty（条約）。平和条約に調印して戦争を終わらせた。" },
      { q: "He apologized for his behavior, but she refused to ( _ ) him.", c: ["forgive", "forget", "forbid", "forecast"], a: "forgive", e: "forgive（〜を許す）。彼は謝罪したが彼女は許すのを拒んだ。" },
      { q: "Medical technology has ( _ ) significantly in recent years.", c: ["advanced", "advantaged", "adventure", "adversary"], a: "advanced", e: "advance（進歩する、進む）。近年、医療技術は著しく進歩した。" },
      { q: "The cold weather ( _ ) us from going out.", c: ["prevented", "predicted", "promoted", "provided"], a: "prevented", e: "prevent A from -ing で「Aが〜するのを妨げる/防ぐ」。" },
      { q: "Smoking is strictly ( _ ) inside the building.", c: ["prohibited", "permitted", "promoted", "provided"], a: "prohibited", e: "prohibit（〜を禁止する）。建物内は喫煙が厳しく禁止されている。" },
      { q: "You should ( _ ) a doctor if the pain continues.", c: ["consult", "consider", "conceal", "consent"], a: "consult", e: "consult A（Aに相談する、診てもらう）。医師の診察を受けるべきだ。" },
      { q: "The price of vegetables fluctuates depending on the ( _ ).", c: ["season", "session", "section", "sequence"], a: "season", e: "season（季節）。野菜の価格は季節に応じて変動する。" },
      { q: "Many students ( _ ) from lack of sleep during exam periods.", c: ["suffer", "surfer", "suffocate", "support"], a: "suffer", e: "suffer from ~ で「〜で苦しむ、悩む」。" },
      { q: "The detective tried to ( _ ) the mystery of the missing painting.", c: ["solve", "dissolve", "evolve", "involve"], a: "solve", e: "solve（〜を解決する、解く）。消えた絵画の謎を解こうとした。" },
      { q: "Please ( _ ) that the flight schedule might change.", c: ["note", "notice", "notify", "notion"], a: "note", e: "please note that ~ で「〜であることにご注意ください」。" },
      { q: "Plastic waste poses a serious ( _ ) to the marine environment.", c: ["threat", "treat", "thread", "thrill"], a: "threat", e: "pose a threat to ~ で「〜に脅威をもたらす」。" },
      { q: "The instructions were too ( _ ), so everyone got confused.", c: ["vague", "vocal", "valuable", "vacant"], a: "vague", e: "vague（あいまいな）。指示があいまいすぎて全員が混乱した。" },
      { q: "She wants to ( _ ) a career in international business.", c: ["pursue", "persuade", "perceive", "purchase"], a: "pursue", e: "pursue（〜を追求する）。国際ビジネスでのキャリアを追求したい。" },
      { q: "This restaurant is famous for its ( _ ) Japanese dishes.", c: ["traditional", "additional", "conditional", "educational"], a: "traditional", e: "traditional（伝統的な）。伝統的な日本料理で有名。" },
      { q: "The program aims to ( _ ) youth employment.", c: ["promote", "prohibit", "protect", "propose"], a: "promote", e: "promote（〜を促進する）。若者の雇用を促進することを目指している。" },
      { q: "Scientists are conducting research to ( _ ) a cure for the disease.", c: ["discover", "recover", "cover", "uncover"], a: "discover", e: "discover（〜を発見する）。病気の治療法を発見するための研究。" },
      { q: "He managed to ( _ ) his parents to let him study abroad.", c: ["persuade", "pursue", "perceive", "permit"], a: "persuade", e: "persuade A to do で「Aを説得して〜させる」。" },
      { q: "The sudden noise ( _ ) the birds in the trees.", c: ["frightened", "brightened", "enlightened", "tightened"], a: "frightened", e: "frighten（〜を怖がらせる、びっくりさせる）。" },
      { q: "Water is ( _ ) for all living things to survive.", c: ["essential", "efficient", "effective", "evident"], a: "essential", e: "be essential for ~ で「〜にとって不可欠である」。" },
      { q: "The population of the village has been ( _ ) for a decade.", c: ["decreasing", "increasingly", "decorating", "deserving"], a: "decreasing", e: "decrease（減少する）。村の人口は10年間減少し続けている。" },
      { q: "Students are required to ( _ ) assignments on time.", c: ["submit", "admit", "permit", "commit"], a: "submit", e: "submit（〜を提出する）。課題を期限通りに提出する必要がある。" },
      { q: "He works hard to ( _ ) his family.", c: ["support", "suppose", "supply", "supplement"], a: "support", e: "support（〜を養う、支持する）。家族を養うために一生懸命働く。" },
      { q: "Technology plays a vital ( _ ) in modern education.", c: ["role", "rule", "root", "route"], a: "role", e: "play a vital role in ~ で「〜で極めて重要な役割を果たす」。" },
      { q: "The teacher asked the students to ( _ ) on their studies.", c: ["concentrate", "concern", "conclude", "conduct"], a: "concentrate", e: "concentrate on ~ で「〜に集中する」。" },
      { q: "Regular exercise is ( _ ) to your physical health.", c: ["beneficial", "artificial", "superficial", "beneficiary"], a: "beneficial", e: "be beneficial to ~ で「〜にとって有益である」。" },
      { q: "The museum attracts millions of ( _ ) every year.", c: ["visitors", "creators", "ancestors", "conductors"], a: "visitors", e: "visitor（訪問者、観光客）。毎年何百万人もの観光客を惹きつける。" },
      { q: "We need to ( _ ) the true cause of the accident.", c: ["determine", "terminate", "contaminate", "dominate"], a: "determine", e: "determine（〜を特定する、決定する）。事故の本当の原因を特定する。" },
      { q: "Her explanation was based on ( _ ) facts, not rumors.", c: ["actual", "annual", "casual", "visual"], a: "actual", e: "actual（実際の）。うわさではなく実際の事実に基づいていた。" },
      { q: "You should take advantage of this rare ( _ ).", c: ["opportunity", "opposition", "operation", "obligation"], a: "opportunity", e: "opportunity（機会）。take advantage of ~ で「〜を利用する」。" },
      { q: "The new tax system will ( _ ) low-income families.", c: ["affect", "effect", "afford", "effort"], a: "affect", e: "affect（〜に影響を与える）。新税制は低所得世帯に影響を与える。" },
      { q: "It is ( _ ) that he didn't mean to hurt your feelings.", c: ["obvious", "obscure", "obsolete", "obligatory"], a: "obvious", e: "It is obvious that ~ で「〜なのは明白だ」。悪気はなかったのは明白だ。" },
      { q: "The high cost of living is a major ( _ ) for students.", c: ["concern", "concept", "concert", "concrete"], a: "concern", e: "concern（懸念、心配事）。高い生活費は学生にとって大きな懸念だ。" },
      { q: "They have a lot of things in ( _ ) despite their different backgrounds.", c: ["common", "comment", "commerce", "comfort"], a: "common", e: "have ~ in common で「〜を共通して持つ」。" },
      { q: "The team made an ( _ ) effort to win the championship.", c: ["enormous", "anonymous", "autonomous", "infamous"], a: "enormous", e: "enormous（莫大な、ものすごい）。優勝するために凄まじい努力をした。" },
      { q: "He was selected as the representative of the ( _ ) community.", c: ["local", "vocal", "loyal", "legal"], a: "local", e: "local communityで「地域社会」。地元の代表として選ばれた。" },
      { q: "The contract is ( _ ) for three years.", c: ["valid", "value", "vague", "vain"], a: "valid", e: "valid（有効な）。その契約は3年間有効である。" },
      { q: "She has gained a lot of ( _ ) through her internship.", c: ["experience", "experiment", "expense", "expert"], a: "experience", e: "experience（経験）。インターンを通じて多くの経験を得た。" },
      { q: "The weather forecast predicts that it will remain ( _ ) tomorrow.", c: ["stable", "staple", "subtle", "suitable"], a: "stable", e: "stable（安定した）。明日の天気は安定したままだと予測されている。" },
      { q: "The internet allows us to access information ( _ ).", c: ["instantly", "instance", "constantly", "distant"], a: "instantly", e: "instantly（即座に）。ネットにより即座に情報にアクセスできる。" },
      { q: "He was ( _ ) to accept the job offer because of the low salary.", c: ["reluctant", "reliant", "redundant", "relevant"], a: "reluctant", e: "be reluctant to do で「〜するのを嫌がる、ためらう」。" },
      { q: "The city's public transport system is very ( _ ).", c: ["reliable", "relieved", "liable", "relaxed"], a: "reliable", e: "reliable（信頼できる、確かな）。公共交通機関が非常に信頼できる。" },
      { q: "We must reduce the ( _ ) of fossil fuels to combat climate change.", c: ["consumption", "assumption", "presumption", "description"], a: "consumption", e: "consumption（消費、消費量）。化石燃料の消費を減らさねばならない。" },
      { q: "The main ( _ ) of the project is to reduce greenhouse gas emissions.", c: ["objective", "objection", "obligation", "obstacle"], a: "objective", e: "objective（目的、目標）。プロジェクトの主な目的は〜である。" }
    ];

    let quizData = [];
    let incorrectQuestions = [];
    let currentQuestionIndex = 0;
    let score = 0;
    
    let timeLeft = 15;
    let timerInterval = null;
    const TIME_LIMIT = 15;

    // 👇 【エフェクト機能】指定した絵文字を画面上に飛び散らせる関数
    function createEmojiEffect(emoji, count = 20) {
      for (let i = 0; i < count; i++) {
        const particle = document.createElement('div');
        particle.textContent = emoji;
        particle.className = 'emoji-particle';

        // 画面の中央付近から発生させる
        particle.style.left = `${window.innerWidth / 2}px`;
        particle.style.top = `${window.innerHeight / 2 - 50}px`;

        // ランダムな飛び散る方向と回転を計算（CSSの変数へ渡す）
        const angle = Math.random() * Math.PI * 2;
        const velocity = 50 + Math.random() * 200; // 飛び散る距離
        const x = Math.cos(angle) * velocity;
        const y = Math.sin(angle) * velocity - 100; // 少し上に持ち上げる
        const rotate = Math.random() * 360;

        particle.style.setProperty('--x', `${x}px`);
        particle.style.setProperty('--y', `${y}px`);
        particle.style.setProperty('--r', `${rotate}deg`);

        document.body.appendChild(particle);

        // アニメーション終了後に消去
        setTimeout(() => {
          particle.remove();
        }, 1200);
      }
    }

    function startQuiz(mode) {
      document.getElementById("start-screen").style.display = "none";
      document.getElementById("quiz-screen").style.display = "block";
      
      if (mode === 'mini') {
        quizData = [...masterData].sort(() => Math.random() - 0.5).slice(0, 10);
        document.getElementById("quiz-title").textContent = "⏱️ ランダム10問ミニテスト";
        incorrectQuestions = [];
        score = 0;
        currentQuestionIndex = 0;
      } else if (mode === 'all') {
        quizData = [...masterData].sort(() => Math.random() - 0.5);
        document.getElementById("quiz-title").textContent = "🔥 50問全問マスターコース";
        incorrectQuestions = [];
        score = 0;
        currentQuestionIndex = 0;
      } else if (mode === 'review') {
        quizData = [...incorrectQuestions].sort(() => Math.random() - 0.5);
        incorrectQuestions = [];
        score = 0;
        currentQuestionIndex = 0;
        document.getElementById("quiz-title").textContent = "🔄 苦手克服！復習モード";
      }

      loadQuiz();
    }

    function loadQuiz() {
      clearInterval(timerInterval);
      
      const currentQuiz = quizData[currentQuestionIndex];
      
      document.getElementById("progress-bar").textContent = `問題: ${currentQuestionIndex + 1} / ${quizData.length}`;
      document.getElementById("question-text").textContent = `${currentQuiz.q}`;
      
      const resultMessage = document.getElementById("result-message");
      resultMessage.style.display = "none";
      document.getElementById("next-btn").style.display = "none";
      
      const choicesContainer = document.getElementById("choices-container");
      choicesContainer.innerHTML = ""; 
      
      const shuffledChoices = [...currentQuiz.c].sort(() => Math.random() - 0.5);
      
      shuffledChoices.forEach(choice => {
        const button = document.createElement("button");
        button.textContent = choice;
        button.className = "choice-btn";
        button.onclick = () => selectAnswer(choice, button);
        choicesContainer.appendChild(button);
      });

      startTimer();
    }

    function startTimer() {
      timeLeft = TIME_LIMIT;
      const timerBar = document.getElementById("timer-bar");
      timerBar.style.width = "100%";

      timerInterval = setInterval(() => {
        timeLeft--;
        const widthPercentage = (timeLeft / TIME_LIMIT) * 100;
        timerBar.style.width = `${widthPercentage}%`;

        if (timeLeft <= 0) {
          clearInterval(timerInterval);
          timeOutAction();
        }
      }, 1000);
    }

    function timeOutAction() {
      const currentQuiz = quizData[currentQuestionIndex];
      incorrectQuestions.push(currentQuiz);
      
      // 😡 間違えた（時間切れ）エフェクトを発動
      createEmojiEffect('😡', 15);

      const buttons = document.querySelectorAll(".choice-btn");
      buttons.forEach(btn => btn.disabled = true);

      const resultMessage = document.getElementById("result-message");
      resultMessage.style.display = "block";
      resultMessage.textContent = `⏰ 時間切れ！\n（正解は「${currentQuiz.a}」）\n\n💡解説: ${currentQuiz.e}`;
      resultMessage.style.backgroundColor = "#fff5f5";
      resultMessage.style.color = "#742a2a";

      document.getElementById("next-btn").style.display = "block";
    }

    function selectAnswer(selectedChoice, clickedButton) {
      clearInterval(timerInterval);
      
      const currentQuiz = quizData[currentQuestionIndex];
      const resultMessage = document.getElementById("result-message");
      
      const buttons = document.querySelectorAll(".choice-btn");
      buttons.forEach(btn => btn.disabled = true);

      resultMessage.style.display = "block";

      if (selectedChoice === currentQuiz.a) {
        score++;
        
        // 😆 正解エフェクトを発動！
        createEmojiEffect('😆', 15);

        clickedButton.style.backgroundColor = "#48bb78";
        clickedButton.style.borderColor = "#48bb78";
        clickedButton.style.color = "white";
        resultMessage.textContent = `⭕ 正解！\n\n💡解説: ${currentQuiz.e}`;
        resultMessage.style.backgroundColor = "#f0fff4";
        resultMessage.style.color = "#22543d";
      } else {
        incorrectQuestions.push(currentQuiz);
        
        // 😡 不正解エフェクトを発動！
        createEmojiEffect('😡', 15);

        clickedButton.style.backgroundColor = "#f56565";
        clickedButton.style.borderColor = "#f56565";
        clickedButton.style.color = "white";
        resultMessage.textContent = `❌ 不正解...（正解は「${currentQuiz.a}」）\n\n💡解説: ${currentQuiz.e}`;
        resultMessage.style.backgroundColor = "#fff5f5";
        resultMessage.style.color = "#742a2a";
      }
      
      document.getElementById("next-btn").style.display = "block";
    }

    function nextQuestion() {
      currentQuestionIndex++;
      
      if (currentQuestionIndex < quizData.length) {
        loadQuiz();
      } else {
        clearInterval(timerInterval);
        showFinalResults();
      }
    }

    function showFinalResults() {
      const wrapper = document.getElementById("quiz-wrapper");
      
      let htmlContent = `
        <h2 style="font-size: 24px;">🎉 テスト完了！</h2>
        <p style="text-align: center; font-size: 18px; font-weight: bold; color: #2b6cb0; margin: 20px 0;">
          スコア: ${score} / ${quizData.length} 問中
        </p>
      `;

      if (incorrectQuestions.length > 0) {
        htmlContent += `
          <p style="text-align: center; color: #e53e3e; font-weight: bold;">
            ⚠️ 間違えた問題・時間切れが ${incorrectQuestions.length} 問あります。
          </p>
          <button class="action-btn" style="background-color: #dd6b20;" onclick="startQuiz('review')">🔄 間違えた問題だけを復習する</button>
        `;
      } else {
        // 💮 全問正解（パーフェクト）エフェクトを発動！！大量に降らせます
        setTimeout(() => {
          createEmojiEffect('💮', 40);
        }, 300);

        htmlContent += `
          <p style="text-align: center; color: #38a169; font-weight: bold; margin-bottom: 20px;">
            🌟 素晴らしい！全問正解、パーフェクトです！
          </p>
        `;
      }

      htmlContent += `
        <button class="action-btn" style="background-color: #718096;" onclick="location.reload()">🏠 最初のメニューに戻る</button>
      `;

      wrapper.innerHTML = htmlContent;
    }
  </script>
</body>
</html>
