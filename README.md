<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>深刻認識一個人｜電子卡牌</title>

  <style>
    * { box-sizing: border-box; }

    body {
      margin: 0;
      min-height: 100vh;
      padding: 30px 16px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-family: "Microsoft JhengHei", sans-serif;
      color: #29465b;
      background: linear-gradient(135deg, #e8f2f8, #f8f4ed);
    }

    .app {
      width: 100%;
      max-width: 520px;
      text-align: center;
    }

    h1 {
      font-size: clamp(25px, 6vw, 34px);
      margin-bottom: 8px;
    }

    .subtitle {
      color: #6b8190;
      line-height: 1.8;
      margin-bottom: 26px;
    }

    select, button {
      font: inherit;
      border-radius: 12px;
      padding: 12px 16px;
    }

    select {
      width: 100%;
      border: 1px solid #bdceda;
      background: white;
      color: #29465b;
      margin-bottom: 20px;
    }

    .card-scene {
      perspective: 1000px;
      margin: 0 auto 20px;
      width: 100%;
      max-width: 340px;
    }

    .card {
      position: relative;
      width: 100%;
      height: 390px;
      cursor: pointer;
      transform-style: preserve-3d;
      transition: transform 0.65s;
    }

    .card.flipped {
      transform: rotateY(180deg);
    }

    .face {
      position: absolute;
      inset: 0;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      padding: 28px;
      border: 2px solid #a9c5d7;
      border-radius: 24px;
      backface-visibility: hidden;
      -webkit-backface-visibility: hidden;
      box-shadow: 0 12px 30px #45677a20;
    }

    .front {
      color: #315c79;
      background: linear-gradient(145deg, #ffffff, #dcebf4);
    }

    .back {
      color: #fffdf8;
      background: linear-gradient(145deg, #456f8b, #29465b);
      transform: rotateY(180deg);
    }

    .symbol {
      font-size: 46px;
      margin-bottom: 20px;
    }

    .card-label {
      font-size: 14px;
      letter-spacing: 3px;
      margin-bottom: 18px;
      opacity: 0.85;
    }

    #question {
      font-size: 23px;
      line-height: 1.8;
      overflow-wrap: anywhere;
    }

    .hint {
      margin-top: 22px;
      font-size: 13px;
      opacity: 0.8;
    }

    .buttons {
      display: flex;
      justify-content: center;
      gap: 10px;
      flex-wrap: wrap;
    }

    button {
      border: none;
      cursor: pointer;
      transition: opacity 0.2s, transform 0.2s;
    }

    button:hover {
      opacity: 0.88;
      transform: translateY(-1px);
    }

    button:focus-visible, select:focus-visible,
    .card:focus-visible {
      outline: 3px solid #dfac62;
      outline-offset: 4px;
    }

    .primary {
      background: #315c79;
      color: white;
    }

    .secondary {
      background: white;
      color: #315c79;
      border: 1px solid #bdceda;
    }

    button:disabled {
      opacity: 0.45;
      cursor: not-allowed;
      transform: none;
    }

    #status {
      min-height: 24px;
      margin-top: 18px;
      font-size: 14px;
      color: #6b8190;
    }

    .footer {
      margin-top: 18px;
      font-size: 12px;
      color: #81919b;
    }

    @media (max-width: 420px) {
      .card { height: 350px; }
      #question { font-size: 21px; }
    }

    @media (prefers-reduced-motion: reduce) {
      .card { transition: none; }
    }
  </style>
</head>

<body>
  <main class="app">
    <h1>深刻認識一個人</h1>
    <p class="subtitle">
      從一道問題開始，慢慢認識彼此。<br>
      不用急著回答，照自己的步調分享就好。
    </p>

    <label for="category">今天想聊什麼？</label>
    <select id="category">
      <option value="全部">全部分類</option>
      <option value="暖身破冰">暖身破冰</option>
      <option value="一星連結">一星連結</option>
      <option value="二星連結">二星連結</option>
      <option value="三星連結">三星連結</option>
    </select>

    <div class="card-scene">
      <div
        class="card"
        id="card"
        role="button"
        tabindex="0"
        aria-label="卡牌，按下翻開問題"
        aria-pressed="false"
      >
        <div class="face front">
          <div class="symbol">✦</div>
          <div class="card-label">GET TO KNOW YOU</div>
          <h2>深刻認識一個人</h2>
          <p>讓我們更靠近彼此一點。</p>
          <p class="hint">抽卡後，點擊卡牌翻開</p>
        </div>

        <div class="face back">
          <div class="card-label" id="cardCategory">你的問題</div>
          <div id="question">準備好抽一張卡了嗎？</div>
          <p class="hint">每個人的故事都值得被聽見。</p>
        </div>
      </div>
    </div>

    <div class="buttons">
      <button class="primary" id="drawBtn">抽一張卡</button>
      <button class="secondary" id="flipBtn" disabled>翻開問題</button>
      <button class="secondary" id="nextBtn" disabled>下一題</button>
      <button class="secondary" id="resetBtn">重新洗牌</button>
    </div>

    <p id="status" aria-live="polite">請選擇分類，開始抽卡。</p>
    <p class="footer">可以慢慢回答，也可以選擇跳過。</p>
  </main>

  <script>
    // 題目放在這裡：可以自行新增或修改
    const questions = [
      // 暖身破冰：10 題
      { category: "暖身破冰", text: "所有玩家一起合照一張!並上傳打卡!" },
      { category: "暖身破冰", text: "所有玩家分享一句深刻影響你的話,並逐一分享!" },
      { category: "暖身破冰", text: "所有玩家打開手機,挑一張最近拍的照片並說說它的故事。" },
      { category: "暖身破冰", text: "所有玩家分享你小時候的樣子給大家看!" },
      { category: "暖身破冰", text: "所有玩家分享兩件今日想感恩的事情。" },
      { category: "暖身破冰", text: "所有玩家用十秒唱一首最喜歡的歌。" },
      { category: "暖身破冰", text: "你認為你的形象是什麼?你做過什麼與你形象不符的事(所有玩家回答)。" },
      { category: "暖身破冰", text: "所有玩家分享平常你都是如何表達「愛」?" },
      { category: "暖身破冰", text: "所有玩家想出三個代表自己的標籤,並順時針逐一分享選擇的原因!" },
      { category: "暖身破冰", text: "抽到卡的人,指定兩位玩家扮最醜的鬼臉!並且合影一張做記錄。" },

      // 一星連結：20 題
      { category: "一星連結", text: "如果可以跟任何一位名人一起吃飯,你會選誰?" },
      { category: "一星連結", text: "分享一個最近讓你感到幸運的事情。" },
      { category: "一星連結", text: "如果你能瞬間掌握一種才藝,你會選什麼?" },
      { category: "一星連結", text: "關於童年,你最難忘的記憶是什麼?" },
      { category: "一星連結", text: "你覺得維持一段友誼最重要的元素是什麼?" },
      { category: "一星連結", text: "你想要在生活中融入什麼樣的新習慣?" },
      { category: "一星連結", text: "分享一份近期生活中得到的體悟?" },
      { category: "一星連結", text: "分享人生的第一份工作與心境,有什麼特別的回憶嗎?" },
      { category: "一星連結", text: "有沒有哪一本書或哪一部電影,讓你看完印象深刻?" },
      { category: "一星連結", text: "工作或學習之餘,你最常做的休閒活動是什麼?" },
      { category: "一星連結", text: "你覺得自己比較像外向還是內向的人?有沒有具體的例子?" },
      { category: "一星連結", text: "分享一個最近讓你覺得好笑或開心的時刻。" },
      { category: "一星連結", text: "如果能夠回到某個特定年齡,你會想回到幾歲?" },
      { category: "一星連結", text: "你最喜歡收到什麼樣子的禮物?" },
      { category: "一星連結", text: "小時候最喜歡的一部卡通或動畫是什麼?" },
      { category: "一星連結", text: "你覺得自己在社交中最大的優勢是什麼?" },
      { category: "一星連結", text: "送大家你很喜歡的一段話,它如何影響你?" },
      { category: "一星連結", text: "請大家分享最想對你說的一句話,可能是感謝、稱讚或祝福。(由所有玩家回應抽卡人)" },
      { category: "一星連結", text: "邀請大家說說對你的第一印象?(由所有玩家回應抽卡人)" },
      { category: "一星連結", text: "你認為最能讓你放鬆或療癒的事情是什麼?" },

      // 二星連結：20 題
      { category: "二星連結", text: "什麼事是你知道要改變,但總是告訴自己還有時間?" },
      { category: "二星連結", text: "大家認為跟你有哪些相似之處呢?特質、個性或是談吐。(由所有的玩家回應抽卡人)" },
      { category: "二星連結", text: "你會如何形容在家庭中你所扮演的角色?家庭成員也是這樣看待你嗎?" },
      { category: "二星連結", text: "對一位玩家說出你心中的感謝。記得給一個擁抱。" },
      { category: "二星連結", text: "你認為自己在哪些方面成長最多?它是否來自什麼關鍵事件或人物?" },
      { category: "二星連結", text: "有沒有哪件事當時讓你感到心碎,但現在回想起來已經可以微笑面對?" },
      { category: "二星連結", text: "如果不會被嘲笑或反對,你最想完成什麼事情?" },
      { category: "二星連結", text: "描述一次你覺得自己被照亮的時刻(精確地說出你的特質與優點)?" },
      { category: "二星連結", text: "分享一個現在生活中最喜歡的部分?他滿足了你內心什麼樣的需求?" },
      { category: "二星連結", text: "嘉許在場某一人的行為、特質或是共同經歷的回憶。他對你產生了什麼影響?" },
      { category: "二星連結", text: "什麼樣的行為最令你憤怒? 你都如何表達憤怒?" },
      { category: "二星連結", text: "你最近一次對父母撒謊是什麼時候?是什麼事件?" },
      { category: "二星連結", text: "你會如何表達關心或在乎? 身邊的人都能收到嗎?" },
      { category: "二星連結", text: "大家會如何向陌生人介紹我呢?(由所有玩家回應抽卡人)" },
      { category: "二星連結", text: "最近有沒有什麼時刻讓你紅了眼眶?" },
      { category: "二星連結", text: "有沒有哪一次你覺得自己的感受與需求被重要的人忽視?當時你的反應是什麼?" },
      { category: "二星連結", text: "你認為一段理想的關係,需要具備哪些特質?為什麼?" },
      { category: "二星連結", text: "最近有沒有誰說過一句話,或做過一件事,讓你覺得「他真的懂我」?" },
      { category: "二星連結", text: "描述一次你克服恐懼的經歷,當時發生了什麼?" },
      { category: "二星連結", text: "你覺得自己身上最值得驕傲的特質是什麼?" },

      // 三星連結：20 題
      { category: "三星連結", text: "最近一次讓你感到內疚的事情是什麼?" },
      { category: "三星連結", text: "你認為過去一年裡,你最大的成長是什麼?" },
      { category: "三星連結", text: "你曾經做過最勇敢的決定是什麼?分享一下當時的心境為何?" },
      { category: "三星連結", text: "在家庭關係中你最想改變的是什麼?最需要克服的是什麼?" },
      { category: "三星連結", text: "分享人際關係中你最想被珍惜的是什麼?你也珍惜跟你有相同特點的人嗎?" },
      { category: "三星連結", text: "談一談你正在面對的人生課題,阻礙你的是什麼?你認為自己會怎麼突破?" },
      { category: "三星連結", text: "分享一件你會全力以赴達成的一件事,當時你有什麼心情?為了什麼而去努力?" },
      { category: "三星連結", text: "想像一個完美的「療癒日」 你會怎麼安排?哪些元素對你來說是最具療癒效果?" },
      { category: "三星連結", text: "你覺得怎麼樣的經歷能讓人重新認識自己?分享一件關於你的人生轉折點。" },
      { category: "三星連結", text: "你曾經完成過的最有成就感的一件事是什麼?你是如何完成的?" },
      { category: "三星連結", text: "在生命最後的時刻,你會最感謝自己做過哪個決定?" },
      { category: "三星連結", text: "面對壓力與挑戰時,你如何給予自己力量與信心?" },
      { category: "三星連結", text: "如果可以回到過去刪除某一個記憶,那會是什麼樣的事件?重來一次的話,你會怎麼做?" },
      { category: "三星連結", text: "如果可以重塑與某人的關係,你最希望改變哪個部分?為什麼?" },
      { category: "三星連結", text: "說出一個你在群體或關係中最令你驕傲的特質?你怎麼發現的?" },
      { category: "三星連結", text: "分享一個你印象深刻的失敗經歷,描述當初的感受, 以及你現在如何看待它?" },
      { category: "三星連結", text: "分享你與父母之間印象最深刻的一個回憶?" },
      { category: "三星連結", text: "如果現在有機會對一個人說出你的歉意或感謝,你會選誰?" },
      { category: "三星連結", text: "分享生命中最讓你感到幸福的時刻?是什麼讓它如此特別?" },
      { category: "三星連結", text: "分享一個你一直想做但至今都未完成的事情?如果選擇去完成它,該克服的挑戰是什麼?" }
    ];

    const categorySelect = document.getElementById("category");
    const card = document.getElementById("card");
    const questionEl = document.getElementById("question");
    const cardCategory = document.getElementById("cardCategory");
    const statusEl = document.getElementById("status");
    const drawBtn = document.getElementById("drawBtn");
    const flipBtn = document.getElementById("flipBtn");
    const nextBtn = document.getElementById("nextBtn");
    const resetBtn = document.getElementById("resetBtn");

    let currentQuestion = null;
    let remainingQuestions = [];
    let usedCount = 0;

    function getFilteredQuestions() {
      const category = categorySelect.value;

      return questions.filter(q =>
        category === "全部" || q.category === category
      );
    }

    // Fisher-Yates 洗牌：讓每題在同一輪中只抽一次
    function shuffle(array) {
      const result = [...array];

      for (let i = result.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [result[i], result[j]] = [result[j], result[i]];
      }

      return result;
    }

    function setFlipped(flipped) {
      card.classList.toggle("flipped", flipped);
      card.setAttribute("aria-pressed", String(flipped));
      flipBtn.textContent = flipped ? "收回卡牌" : "翻開問題";
    }

    function prepareCard() {
      setFlipped(false);
      currentQuestion = null;
      questionEl.textContent = "準備好抽一張卡了嗎？";
      cardCategory.textContent = "你的問題";
      flipBtn.disabled = true;
      nextBtn.disabled = true;
    }

    function drawCard() {
      if (remainingQuestions.length === 0) {
        statusEl.textContent = "這個分類的題目已經抽完，請重新洗牌。";
        drawBtn.disabled = true;
        nextBtn.disabled = true;
        return;
      }

      currentQuestion = remainingQuestions.pop();
      usedCount++;

      questionEl.textContent = currentQuestion.text;
      cardCategory.textContent = currentQuestion.category;
      setFlipped(false);

      flipBtn.disabled = false;
      nextBtn.disabled = false;
      drawBtn.disabled = true;

      statusEl.textContent =
        `本輪已抽 ${usedCount} 題，剩下 ${remainingQuestions.length} 題。`;
    }

    function flipCard() {
      if (!currentQuestion) {
        statusEl.textContent = "請先抽一張卡，再翻開問題。";
        return;
      }

      const flipped = !card.classList.contains("flipped");
      setFlipped(flipped);

      statusEl.textContent = flipped
        ? "慢慢讀題目，照自己的步調分享就好。"
        : "問題已收回，可以再翻開看看。";
    }

    function resetDeck() {
      remainingQuestions = shuffle(getFilteredQuestions());
      usedCount = 0;

      prepareCard();

      drawBtn.disabled = remainingQuestions.length === 0;

      statusEl.textContent = remainingQuestions.length
        ? `已重新洗牌，共 ${remainingQuestions.length} 題。`
        : "這個分類目前沒有題目。";
    }

    drawBtn.addEventListener("click", drawCard);
    flipBtn.addEventListener("click", flipCard);

    nextBtn.addEventListener("click", () => {
      drawBtn.disabled = false;
      drawCard();
    });

    resetBtn.addEventListener("click", resetDeck);

    categorySelect.addEventListener("change", resetDeck);

    card.addEventListener("click", flipCard);

    card.addEventListener("keydown", event => {
      if (event.key === "Enter" || event.key === " ") {
        event.preventDefault();
        flipCard();
      }
    });

    resetDeck();
  </script>
</body>
</html>
