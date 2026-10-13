
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
      max-width: 520px; margin: 35px auto;
      padding: 24px; border-radius: 20px;
      background: #1e2542;
      box-shadow: 0 12px 35px #0005;
    }
    h1 { color: #ffd166; text-align: center; }
    p { line-height: 1.7; }
    input, button {
      width: 100%; padding: 14px;
      margin-top: 12px; border-radius: 10px;
      font-size: 16px;
    }
    input { border: 1px solid #69739b; }
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
    .option:disabled { opacity: .8; }
    #status { color: #a9f5c7; }
    .small { color: #c7cbe0; font-size: 14px; }
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
      <button id="start">কুইজ শুরু করো</button>
      <p class="small">মোট ৫টি প্রশ্ন · প্রতি সঠিক উত্তরে ১ নম্বর</p>
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
    const questions = [
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
        q: "পৃথিবীর উপগ্রহ কোনটি?",
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
    ];

    const $ = id => document.getElementById(id);
    let name = "";
    let index = 0;
    let points = 0;
    let answered = false;

    $("start").addEventListener("click", () => {
      name = $("player").value.trim();
      if (!name) {
        alert("প্রথমে তোমার নাম লেখো!");
        return;
      }
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
      const item = questions[index];
      $("progress").textContent =
        "প্রশ্ন " + (index + 1) + " / " + questions.length;
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
      const item = questions[index];
      const buttons = $("options").querySelectorAll("button");

      buttons.forEach((btn, i) => {
        btn.disabled = true;
        if (i === item.c) btn.style.borderColor = "#50e3a4";
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
        index === questions.length - 1 ? "ফলাফল দেখো" : "পরের প্রশ্ন";
    }

    $("next").addEventListener("click", () => {
      if (!answered) return;
      index++;
      if (index < questions.length) {
        showQuestion();
      } else {
        $("game").hidden = true;
        $("result").hidden = false;
        $("score").textContent =
          name + ", তোমার নম্বর " + points + " / " + questions.length;
        $("message").textContent =
          points === questions.length ? "অসাধারণ! সব উত্তর সঠিক!" :
          points >= 3 ? "ভালো চেষ্টা! আরও অনুশীলন করো।" :
          "চেষ্টা চালিয়ে যাও, তুমি আরও ভালো করবে!";
      }
    });

    $("again").addEventListener("click", () => {
      $("result").hidden = true;
      $("home").hidden = false;
      $("player").value = name;
    });
  </script>
</body>
</html>

