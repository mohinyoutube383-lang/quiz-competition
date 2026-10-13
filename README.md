<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="theme-color" content="#10152b">
  <title>Quiz Competition</title>
  <style>
    * { box-sizing: border-box; }
    body {
      margin: 0; padding: 20px;
      min-height: 100vh;
      font-family: Arial, sans-serif;
      color: #fff;
      background: linear-gradient(145deg,#10152b,#30205a);
    }
    .card {
      max-width: 520px; margin: 20px auto;
      padding: 24px; border-radius: 20px;
      background: #1e2542;
      box-shadow: 0 12px 35px #0005;
    }
    h1 { color: #ffd166; text-align: center; }
    p { line-height: 1.7; }
    input, button, select {
      width: 100%; padding: 14px;
      margin-top: 12px; border-radius: 10px;
      font-size: 16px;
    }
    input, select {
      border: 1px solid #69739b;
      background: white;
      color: #17172b;
    }
    button {
      border: 0; background: #ffd166;
      color: #201934; font-weight: bold;
      cursor: pointer;
    }
    .option {
      background: #303958; color: white;
      border: 1px solid #596487;
      text-align: left;
    }
    .option:disabled { opacity: .9; }
    #status { color: #a9f5c7; }
    .small { color: #c7cbe0; font-size: 14px; }
    .stat {
      background: #303958;
      padding: 12px;
      border-radius: 10px;
      text-align: center;
    }
  </style>
</head>
<body>
<main class="card">
  <h1>🏆 Quiz Competition</h1>
  <p class="small">সাধারণ জ্ঞান ও শিক্ষামূলক কুইজ</p>

  <section id="home">
    <p>তোমার নাম লিখে কুইজ শুরু করো।</p>
    <input id="player" maxlength="30"
           placeholder="তোমার নাম লিখো">

    <p>প্রতি কুইজে কতটি প্রশ্ন খেলবে?</p>
    <select id="questionCount">
      <option value="5">৫টি প্রশ্ন</option>
      <option value="10">১০টি প্রশ্ন</option>
      <option value="20" selected>২০টি প্রশ্ন</option>
      <option value="50">৫০টি প্রশ্ন</option>
      <option value="100">১০০টি প্রশ্ন</option>
    </select>

    <p class="stat" id="bankInfo">প্রশ্ন তৈরি হচ্ছে...</p>
    <button id="start">কুইজ শুরু করো</button>
    <p class="small">প্রতি সঠিক উত্তরে ১ নম্বর।</p>
  </section>

  <section id="game" hidden>
    <p id="welcome"></p>
    <p id="progress"></p>
    <h3 id="question"></h3>
    <div id="options"></div>
    <p id="status" aria-live="polite"></p>
    <button id="next" hidden>পরের প্রশ্ন</button>
  </section>

  <section id="result" hidden>
    <h2>🎉 কুইজ শেষ!</h2>
    <p id="score"></p>
    <p id="message"></p>
    <button id="again">আবার খেলো</button>
  </section>
</main>

<script>
const questions = [];

// চারটি অপশন তৈরি
function addQuestion(q, correct) {
  const answers = new Set([correct]);

  while (answers.size < 4) {
    let wrong = correct +
      (Math.floor(Math.random() * 19) - 9);

    if (wrong < 0) wrong = correct + 10;
    if (wrong !== correct) answers.add(wrong);
  }

  const a = [...answers].sort(() => Math.random() - 0.5);

  questions.push({
    q: q,
    a: a.map(String),
    c: a.indexOf(correct)
  });
}

// ১. যোগ: ২৫০টি স্বতন্ত্র প্রশ্ন
let count = 0;
for (let x = 0; x <= 99 && count < 250; x++) {
  for (let y = 0; y <= 99 && count < 250; y++) {
    addQuestion(`${x} + ${y} = কত?`, x + y);
    count++;
  }
}

// ২. বিয়োগ: ২৫০টি স্বতন্ত্র প্রশ্ন
count = 0;
for (let x = 1; x <= 99 && count < 250; x++) {
  for (let y = 0; y < x && count < 250; y++) {
    addQuestion(`${x} - ${y} = কত?`, x - y);
    count++;
  }
}

// ৩. গুণ: ১৫০টি স্বতন্ত্র প্রশ্ন
count = 0;
for (let x = 2; x <= 20 && count < 150; x++) {
  for (let y = 2; y <= 20 && count < 150; y++) {
    addQuestion(`${x} × ${y} = কত?`, x * y);
    count++;
  }
}

// ৪. ভাগ: ৫০টি স্বতন্ত্র প্রশ্ন
count = 0;
for (let y = 2; y <= 10 && count < 50; y++) {
  for (let x = 2; x <= 20 && count < 50; x++) {
    addQuestion(`${x * y} ÷ ${y} = কত?`, x);
    count++;
  }
}

// ৫. সাধারণ জ্ঞানের নমুনা প্রশ্ন
questions.push(
  {
    q: "বাংলাদেশের রাজধানী কোনটি?",
    a: ["চট্টগ্রাম", "ঢাকা", "খুলনা", "রাজশাহী"],
    c: 1
  },
  {
    q: "বাংলাদেশের জাতীয় ফুল কোনটি?",
    a: ["গোলাপ", "সূর্যমুখী", "শাপলা", "জবা"],
    c: 2
  },
  {
    q: "পৃথিবীর প্রাকৃতিক উপগ্রহ কোনটি?",
    a: ["সূর্য", "চাঁদ", "মঙ্গল", "শুক্র"],
    c: 1
  },
  {
    q: "এক সপ্তাহে কত দিন?",
    a: ["৫ দিন", "৬ দিন", "৭ দিন", "৮ দিন"],
    c: 2
  },
  {
    q: "পানির রাসায়নিক সংকেত কোনটি?",
    a: ["CO2", "O2", "NaCl", "H2O"],
    c: 3
  }
);
  {q:"বাংলাদেশের জাতীয় ফল কোনটি?",a:["আম","কাঁঠাল","লিচু","জাম"],c:1},
  {q:"বাংলাদেশের জাতীয় পাখি কোনটি?",a:["কাক","দোয়েল","ময়না","টিয়া"],c:1},
  {q:"বাংলাদেশের জাতীয় পশু কোনটি?",a:["হাতি","হরিণ","রয়েল বেঙ্গল টাইগার","সিংহ"],c:2},
  {q:"বাংলাদেশের জাতীয় মাছ কোনটি?",a:["রুই","কাতলা","ইলিশ","পাঙ্গাস"],c:2},
  {q:"বাংলাদেশের স্বাধীনতা দিবস কবে?",a:["২১ ফেব্রুয়ারি","২৬ মার্চ","১৬ ডিসেম্বর","১৪ এপ্রিল"],c:1},
  {q:"বাংলাদেশের বিজয় দিবস কবে?",a:["২৬ মার্চ","২১ ফেব্রুয়ারি","১৬ ডিসেম্বর","১ মে"],c:2},
  {q:"বাংলাদেশের ভাষা আন্দোলনের স্মরণীয় দিন কোনটি?",a:["২১ ফেব্রুয়ারি","২৬ মার্চ","১৬ ডিসেম্বর","১ জানুয়ারি"],c:0},
  {q:"বাংলাদেশের মুদ্রার নাম কী?",a:["রুপি","টাকা","রিয়াল","ডলার"],c:1},
  {q:"বাংলাদেশের জাতীয় সংসদ ভবনের স্থপতি কে?",a:["এফ আর খান","লুই আই কান","জামশেদজি টাটা","জাহা হাদিদ"],c:1},
  {q:"বাংলাদেশের বৃহত্তম ম্যানগ্রোভ বন কোনটি?",a:["মধুপুর বন","সুন্দরবন","ভাওয়াল বন","লাউয়াছড়া"],c:1},

  // বিজ্ঞান
  {q:"উদ্ভিদ খাদ্য তৈরির সময় কোন গ্যাস গ্রহণ করে?",a:["অক্সিজেন","কার্বন ডাই-অক্সাইড","হাইড্রোজেন","নাইট্রোজেন"],c:1},
  {q:"মানবদেহে রক্ত পাম্প করে কোন অঙ্গ?",a:["ফুসফুস","কিডনি","হৃদপিণ্ড","যকৃত"],c:2},
  {q:"পৃথিবীর সবচেয়ে কাছের নক্ষত্র কোনটি?",a:["চাঁদ","সূর্য","সিরিয়াস","ধ্রুবতারা"],c:1},
  {q:"মানুষ শ্বাস নেওয়ার সময় কোন গ্যাস গ্রহণ করে?",a:["অক্সিজেন","কার্বন ডাই-অক্সাইড","হিলিয়াম","মিথেন"],c:0},
  {q:"পানির হিমাঙ্ক কত ডিগ্রি সেলসিয়াস?",a:["০","১০","৫০","১০০"],c:0},
  {q:"পানির স্ফুটনাঙ্ক সমুদ্রপৃষ্ঠে প্রায় কত?",a:["০°C","৫০°C","১০০°C","২০০°C"],c:2},
  {q:"মানবদেহের সবচেয়ে বড় অঙ্গ কোনটি?",a:["হৃদপিণ্ড","ত্বক","মস্তিষ্ক","ফুসফুস"],c:1},
  {q:"উদ্ভিদের সবুজ রঞ্জক পদার্থের নাম কী?",a:["হিমোগ্লোবিন","ক্লোরোফিল","মেলানিন","কেরাটিন"],c:1},
  {q:"পৃথিবী সূর্যের চারদিকে একবার ঘুরতে প্রায় কত সময় নেয়?",a:["২৪ ঘণ্টা","৩০ দিন","৩৬৫ দিন","১২ ঘণ্টা"],c:2},
  {q:"বিদ্যুৎ প্রবাহের একক কী?",a:["ভোল্ট","ওয়াট","অ্যাম্পিয়ার","জুল"],c:2},

  // ভূগোল
  {q:"পৃথিবীর বৃহত্তম মহাদেশ কোনটি?",a:["আফ্রিকা","এশিয়া","ইউরোপ","অস্ট্রেলিয়া"],c:1},
  {q:"পৃথিবীর বৃহত্তম মহাসাগর কোনটি?",a:["আটলান্টিক","ভারত","প্রশান্ত","আর্কটিক"],c:2},
  {q:"পৃথিবীর সর্বোচ্চ পর্বতশৃঙ্গ কোনটি?",a:["কে-টু","এভারেস্ট","কাঞ্চনজঙ্ঘা","মাকালু"],c:1},
  {q:"জাপানের রাজধানী কোনটি?",a:["বেইজিং","সিউল","টোকিও","ব্যাংকক"],c:2},
  {q:"ভারতের রাজধানী কোনটি?",a:["মুম্বাই","কলকাতা","নয়াদিল্লি","চেন্নাই"],c:2},
  {q:"নেপালের রাজধানী কোনটি?",a:["কাঠমান্ডু","থিম্পু","ঢাকা","করাচি"],c:0},
  {q:"সৌদি আরবের রাজধানী কোনটি?",a:["জেদ্দা","রিয়াদ","মক্কা","মদিনা"],c:1},
  {q:"পৃথিবীর বৃহত্তম উষ্ণ মরুভূমি কোনটি?",a:["গোবি","সাহারা","কালাহারি","থর"],c:1},
  {q:"কোন মহাদেশকে অন্ধকারাচ্ছন্ন মহাদেশ বলা হতো?",a:["এশিয়া","ইউরোপ","আফ্রিকা","দক্ষিণ আমেরিকা"],c:2},
  {q:"বাংলাদেশের দক্ষিণে কোন জলভাগ অবস্থিত?",a:["আরব সাগর","বঙ্গোপসাগর","লোহিত সাগর","ভূমধ্যসাগর"],c:1},

  // ইতিহাস ও সংস্কৃতি
  {q:"বাংলা নববর্ষের প্রথম দিন কোনটি?",a:["১ বৈশাখ","১ ফাল্গুন","১ চৈত্র","১ অগ্রহায়ণ"],c:0},
  {q:"আন্তর্জাতিক মাতৃভাষা দিবস কবে?",a:["২১ ফেব্রুয়ারি","২৬ মার্চ","১৬ ডিসেম্বর","৫ জুন"],c:0},
  {q:"বাংলা ভাষার জাতীয় কবি কে?",a:["রবীন্দ্রনাথ ঠাকুর","কাজী নজরুল ইসলাম","জসীমউদ্দীন","সুকান্ত ভট্টাচার্য"],c:1},
  {q:"'গীতাঞ্জলি' গ্রন্থের রচয়িতা কে?",a:["কাজী নজরুল ইসলাম","রবীন্দ্রনাথ ঠাকুর","শরৎচন্দ্র চট্টোপাধ্যায়","বঙ্কিমচন্দ্র"],c:1},
  {q:"বাংলাদেশের জাতীয় সংগীতের রচয়িতা কে?",a:["কাজী নজরুল ইসলাম","রবীন্দ্রনাথ ঠাকুর","জসীমউদ্দীন","দ্বিজেন্দ্রলাল রায়"],c:1},
  {q:"বাংলাদেশের জাতীয় স্মৃতিসৌধ কোথায়?",a:["সাভার","কুমিল্লা","সিলেট","খুলনা"],c:0},
  {q:"মহান মুক্তিযুদ্ধের চূড়ান্ত বিজয় অর্জিত হয় কোন সালে?",a:["১৯৫২","১৯৬৯","১৯৭১","১৯৭৫"],c:2},
  {q:"বাংলাদেশের প্রথম রাষ্ট্রপতি কে ছিলেন?",a:["শেখ মুজিবুর রহমান","জিয়াউর রহমান","আবু সাঈদ চৌধুরী","তাজউদ্দীন আহমদ"],c:0},
  {q:"বাংলাদেশের জাতীয় কবি কে?",a:["রবীন্দ্রনাথ ঠাকুর","কাজী নজরুল ইসলাম","মাইকেল মধুসূদন দত্ত","জীবনানন্দ দাশ"],c:1},
  {q:"বাংলাদেশের জাতীয় পতাকার মূল রং কয়টি?",a:["একটি","দুটি","তিনটি","চারটি"],c:1},

  // কম্পিউটার ও সাধারণ জ্ঞান
  {q:"কম্পিউটারের মস্তিষ্ক বলা হয় কোনটিকে?",a:["মনিটর","কিবোর্ড","CPU","মাউস"],c:2},
  {q:"WWW-এর পূর্ণরূপ কী?",a:["World Wide Web","World Web Window","Wide World Wire","Web World Wide"],c:0},
  {q:"কম্পিউটারে টাইপ করার জন্য কোনটি ব্যবহার করা হয়?",a:["মাউস","কিবোর্ড","স্পিকার","প্রিন্টার"],c:1},
  {q:"ই-মেইল কী কাজে ব্যবহৃত হয়?",a:["ইলেকট্রনিক বার্তা পাঠাতে","শুধু ছবি আঁকতে","কম্পিউটার পরিষ্কার করতে","বিদ্যুৎ তৈরি করতে"],c:0},
  {q:"১ কিলোবাইটে প্রচলিতভাবে কত বাইট ধরা হয়?",a:["১০০","৫১২","১০২৪","২০০০"],c:2},
  {q:"HTML প্রধানত কী কাজে ব্যবহৃত হয়?",a:["ওয়েবপেজের কাঠামো তৈরিতে","ভিডিও ধারণে","বিদ্যুৎ মাপতে","গান রেকর্ডে"],c:0},
  {q:"ইন্টারনেট ব্রাউজারের উদাহরণ কোনটি?",a:["Google Chrome","Microsoft Word","Calculator","Notepad"],c:0},
  {q:"সৌরজগতের বৃহত্তম গ্রহ কোনটি?",a:["পৃথিবী","মঙ্গল","বৃহস্পতি","শুক্র"],c:2},
  {q:"এক বছরে সাধারণত কত মাস?",a:["১০","১১","১২","১৩"],c:2},
  {q:"ত্রিভুজের বাহু কয়টি?",a:["২","৩","৪","৫"],c:1}
);
const $ = id => document.getElementById(id);

$("bankInfo").textContent =
  "মোট " + questions.length + "টি প্রশ্নের ব্যাংক";

let name = "";
let index = 0;
let points = 0;
let answered = false;
let quizQuestions = [];

$("start").addEventListener("click", () => {
  name = $("player").value.trim();

  if (!name) {
    alert("প্রথমে তোমার নাম লেখো!");
    return;
  }

  const total = Number($("questionCount").value);

  // প্রশ্নের ক্রম এলোমেলো
  quizQuestions = [...questions]
    .sort(() => Math.random() - 0.5)
    .slice(0, total);

  index = 0;
  points = 0;

  $("home").hidden = true;
  $("result").hidden = true;
  $("game").hidden = false;

  $("welcome").textContent = "শুভকামনা, " + name + "!";
  showQuestion();
});

function showQuestion() {
  answered = false;

  const item = quizQuestions[index];

  $("progress").textContent =
    "প্রশ্ন " + (index + 1) + " / " + quizQuestions.length;

  $("question").textContent = item.q;
  $("status").textContent = "";
  $("next").hidden = true;
  $("options").innerHTML = "";

  item.a.forEach((answer, i) => {
    const btn = document.createElement("button");
    btn.className = "option";
    btn.textContent = answer;

    btn.addEventListener("click", () => choose(i));

    $("options").appendChild(btn);
  });
}

function choose(choice) {
  if (answered) return;
  answered = true;

  const item = quizQuestions[index];
  const buttons = $("options").querySelectorAll("button");

  buttons.forEach((btn, i) => {
    btn.disabled = true;

    if (i === item.c) {
      btn.style.borderColor = "#50e3a4";
      btn.style.background = "#176747";
    }

    if (i === choice && choice !== item.c) {
      btn.style.borderColor = "#ff6868";
    }
  });

  if (choice === item.c) {
    points++;
    $("status").textContent = "সঠিক উত্তর! ✅";
  } else {
    $("status").textContent =
      "ভুল উত্তর। সঠিক উত্তর: " + item.a[item.c];
  }

  $("next").hidden = false;
  $("next").textContent =
    index === quizQuestions.length - 1
      ? "ফলাফল দেখো"
      : "পরের প্রশ্ন";
}

$("next").addEventListener("click", () => {
  if (!answered) return;

  index++;

  if (index < quizQuestions.length) {
    showQuestion();
    return;
  }

  $("game").hidden = true;
  $("result").hidden = false;

  $("score").textContent =
    name + ", তোমার নম্বর " + points +
    " / " + quizQuestions.length;

  const percent = Math.round(
    points / quizQuestions.length * 100
  );

  $("message").textContent =
    percent === 100
      ? "অসাধারণ! সব উত্তর সঠিক! 🏆"
      : percent >= 60
        ? "ভালো চেষ্টা! আরও অনুশীলন করো।"
        : "চেষ্টা চালিয়ে যাও, তুমি আরও ভালো করবে!";
});

$("again").addEventListener("click", () => {
  $("result").hidden = true;
  $("home").hidden = false;
  $("player").value = name;
});
</script>
</body>
</html>
