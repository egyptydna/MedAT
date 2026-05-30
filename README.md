<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>MedAT BMS Quiz</title>
  <style>
    * { box-sizing: border-box; }
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f3f4f6;
      color: #111827;
    }
    header {
      background: linear-gradient(135deg, #111827, #1d4ed8);
      color: white;
      padding: 28px 18px;
      text-align: center;
    }
    header h1 { margin: 0 0 8px; font-size: 30px; }
    header p { margin: 0; opacity: 0.9; }
    .container {
      max-width: 1100px;
      margin: 20px auto;
      padding: 0 14px 40px;
    }
    .card {
      background: white;
      border-radius: 16px;
      padding: 18px;
      margin-bottom: 16px;
      box-shadow: 0 8px 24px rgba(0,0,0,0.08);
    }
    .controls {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
      gap: 10px;
      align-items: center;
    }
    select, input, button, textarea {
      width: 100%;
      padding: 12px;
      border: 1px solid #d1d5db;
      border-radius: 12px;
      font-size: 15px;
    }
    button {
      background: #2563eb;
      color: white;
      font-weight: bold;
      cursor: pointer;
      border: none;
      transition: 0.15s;
    }
    button:hover { background: #1d4ed8; transform: translateY(-1px); }
    button.secondary { background: #374151; }
    button.green { background: #16a34a; }
    button.red { background: #dc2626; }
    .stats {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
      gap: 10px;
      text-align: center;
    }
    .stat {
      padding: 14px;
      background: #eff6ff;
      border-radius: 12px;
      font-weight: bold;
    }
    .question {
      border: 1px solid #e5e7eb;
      border-radius: 14px;
      padding: 16px;
      margin-bottom: 14px;
      background: #ffffff;
    }
    .q-head {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      margin-bottom: 10px;
      font-size: 13px;
    }
    .badge {
      background: #e0e7ff;
      color: #1e3a8a;
      padding: 5px 9px;
      border-radius: 999px;
      font-weight: bold;
    }
    .q-text {
      font-size: 18px;
      font-weight: bold;
      line-height: 1.45;
      margin-bottom: 12px;
      white-space: pre-wrap;
    }
    .answer {
      display: block;
      width: 100%;
      text-align: left;
      margin: 7px 0;
      padding: 12px;
      background: #f9fafb;
      color: #111827;
      border: 1px solid #d1d5db;
      border-radius: 12px;
      cursor: pointer;
      font-weight: normal;
    }
    .answer:hover { background: #eef2ff; }
    .answer.correct { background: #dcfce7; border-color: #16a34a; color: #14532d; }
    .answer.wrong { background: #fee2e2; border-color: #dc2626; color: #7f1d1d; }
    .feedback {
      margin-top: 10px;
      padding: 10px;
      border-radius: 10px;
      display: none;
      font-weight: bold;
    }
    .feedback.ok { display: block; background: #dcfce7; color: #14532d; }
    .feedback.no { display: block; background: #fee2e2; color: #7f1d1d; }
    .small { font-size: 13px; color: #4b5563; line-height: 1.5; }
    details summary { cursor: pointer; font-weight: bold; margin-bottom: 10px; }
    textarea { min-height: 180px; font-family: monospace; }
    .source-list li { margin-bottom: 8px; }
    .hidden { display: none; }
  </style>
</head>
<body>
  <header>
    <h1>MedAT BMS Quiz</h1>
    <p>Biologie, Chemie, Physik und Mathematik als klickbares Lernquiz</p>
  </header>

  <main class="container">
    <section class="card">
      <h2>Steuerung</h2>
      <div class="controls">
        <select id="yearFilter">
          <option value="all">Alle Jahre</option>
        </select>
        <select id="subjectFilter">
          <option value="all">Alle BMS-Fächer</option>
          <option value="Biologie">Biologie</option>
          <option value="Chemie">Chemie</option>
          <option value="Physik">Physik</option>
          <option value="Mathematik">Mathematik</option>
        </select>
        <input id="searchInput" placeholder="Suche nach Begriff..." />
        <button onclick="renderQuiz()">Filtern</button>
        <button class="secondary" onclick="shuffleQuestions()">Mischen</button>
        <button class="red" onclick="resetQuiz()">Zurücksetzen</button>
      </div>
    </section>

    <section class="card">
      <div class="stats">
        <div class="stat">Fragen: <span id="totalCount">0</span></div>
        <div class="stat">Beantwortet: <span id="answeredCount">0</span></div>
        <div class="stat">Richtig: <span id="correctCount">0</span></div>
        <div class="stat">Punkte: <span id="scorePercent">0%</span></div>
      </div>
    </section>

    <section class="card">
      <h2>Google-Docs automatisch laden</h2>
      <p class="small">
        Diese Datei enthält alle von dir gesendeten MedAT-Dokumente als Quellen. Klicke auf „Alle Quellen laden“.
        Falls Google oder der Proxy blockiert wird, kannst du den Text aus einem Google Doc kopieren und unten manuell importieren.
      </p>
      <button class="green" onclick="loadAllSources()">Alle Quellen laden</button>
      <p id="loadStatus" class="small"></p>
      <details>
        <summary>Quellen anzeigen</summary>
        <ul id="sourceList" class="source-list small"></ul>
      </details>
    </section>

    <section class="card">
      <h2>Manueller Import</h2>
      <p class="small">
        Falls automatisches Laden nicht geht: Google Doc öffnen → alles markieren → kopieren → hier einfügen → „Text importieren“.
        Der Parser erkennt typische Multiple-Choice-Strukturen mit A/B/C/D/E oder Zeilen mit Antwortmöglichkeiten.
      </p>
      <div class="controls">
        <select id="manualYear">
          <option>MedAT unbekannt</option>
          <option>MedAT 2019</option>
          <option>MedAT 2020</option>
          <option>MedAT 2021</option>
          <option>MedAT 2022</option>
          <option>MedAT 2023</option>
          <option>MedAT 2024</option>
          <option>MedAT 2025</option>
        </select>
        <select id="manualSubject">
          <option>Automatisch erkennen</option>
          <option>Biologie</option>
          <option>Chemie</option>
          <option>Physik</option>
          <option>Mathematik</option>
        </select>
        <button onclick="importManualText()">Text importieren</button>
      </div>
      <textarea id="manualText" placeholder="Hier den Inhalt aus dem Google Doc einfügen..."></textarea>
    </section>

    <section id="quizArea"></section>
  </main>

<script>
const sources = [
  {year:'MedAT 2019', id:'1pGLtgwooCGv0-pJBaf_sx3KwenJu4RH9JYt9H9tzYBc', url:'https://docs.google.com/document/d/1pGLtgwooCGv0-pJBaf_sx3KwenJu4RH9JYt9H9tzYBc/export?format=txt'},
  {year:'MedAT 2020', id:'193kVOscyOrz4I2WXclY1hzypzOCPJn5FFjY8Xlz7wyE', url:'https://docs.google.com/document/d/193kVOscyOrz4I2WXclY1hzypzOCPJn5FFjY8Xlz7wyE/export?format=txt'},
  {year:'MedAT 2021', id:'1Gcr5T2PuNklwBHkV6g-WSHm1rSfDWaFhpTm5R_LenF0', url:'https://docs.google.com/document/d/1Gcr5T2PuNklwBHkV6g-WSHm1rSfDWaFhpTm5R_LenF0/export?format=txt'},
  {year:'MedAT 2022', id:'1ayHsWvMa8Jg5_cln3YbBH2YsUUazqUIHZikVG4Y6I7c', url:'https://docs.google.com/document/d/1ayHsWvMa8Jg5_cln3YbBH2YsUUazqUIHZikVG4Y6I7c/export?format=txt'},
  {year:'MedAT Sammlung', id:'1oT0XwBrMxteZBhuqBl_eVwfrN8yW8G4MCumBHzPAKBA', url:'https://docs.google.com/document/d/1oT0XwBrMxteZBhuqBl_eVwfrN8yW8G4MCumBHzPAKBA/export?format=txt'},
  {year:'MedAT 2024', id:'1zJJ0j3JUBGzj95M8IHBi-nnsSRDrOgJKeGmmOiBzHtU', url:'https://docs.google.com/document/d/1zJJ0j3JUBGzj95M8IHBi-nnsSRDrOgJKeGmmOiBzHtU/export?format=txt'},
  {year:'MedAT 2025', id:'1mUff60AuVE0SvhWmSeEvlft9KI0mq6xt-bx_A7CDkRA', url:'https://docs.google.com/document/d/1mUff60AuVE0SvhWmSeEvlft9KI0mq6xt-bx_A7CDkRA/export?format=txt'},
  {year:'MedAT Zusatz', id:'1zwY5nQVOZg2nK0KzfTG6iFe0mEerijJHL0M16ulHyzE', url:'https://docs.google.com/document/d/1zwY5nQVOZg2nK0KzfTG6iFe0mEerijJHL0M16ulHyzE/export?format=txt'}
];

let questions = [];
let answered = new Map();
let correct = new Map();

function setup() {
  const yearFilter = document.getElementById('yearFilter');
  [...new Set(sources.map(s => s.year))].forEach(y => {
    const opt = document.createElement('option');
    opt.value = y;
    opt.textContent = y;
    yearFilter.appendChild(opt);
  });
  document.getElementById('sourceList').innerHTML = sources.map(s =>
    `<li><b>${s.year}</b>: <a href="https://docs.google.com/document/d/${s.id}/mobilebasic" target="_blank">öffnen</a></li>`
  ).join('');
  addStarterQuestions();
  renderQuiz();
}

function addStarterQuestions() {
  questions = [
    {
      year:'Beispiel', subject:'Biologie',
      text:'In welchem Abschnitt des Zentralnervensystems befindet sich das Atem- und Kreislaufzentrum?',
      answers:['Großhirn','Rückenmark','Hirnstamm','Zwischenhirn','Kleinhirn'], correctIndex:2,
      explanation:'Das Atem- und Kreislaufzentrum liegt im Hirnstamm, besonders in der Medulla oblongata.'
    },
    {
      year:'Beispiel', subject:'Chemie',
      text:'Welche Aussage beschreibt eine Säure nach Brønsted?',
      answers:['Elektronenakzeptor','Protonendonator','Protonenakzeptor','Elektronendonator','Neutronendonator'], correctIndex:1,
      explanation:'Eine Brønsted-Säure gibt Protonen, also H⁺, ab.'
    },
    {
      year:'Beispiel', subject:'Physik',
      text:'Welche Einheit entspricht der Arbeit bzw. Energie?',
      answers:['Newton','Watt','Joule','Pascal','Volt'], correctIndex:2,
      explanation:'Arbeit und Energie werden in Joule angegeben. 1 J = 1 N·m.'
    },
    {
      year:'Beispiel', subject:'Mathematik',
      text:'Was ist 10² · 10³?',
      answers:['10⁵','10⁶','10¹','20⁵','100⁶'], correctIndex:0,
      explanation:'Bei gleicher Basis werden die Exponenten addiert: 10² · 10³ = 10⁵.'
    }
  ];
}

async function loadAllSources() {
  const status = document.getElementById('loadStatus');
  status.textContent = 'Lade Quellen...';
  let totalAdded = 0;
  let failed = [];

  for (const src of sources) {
    try {
      const proxyUrl = 'https://api.allorigins.win/raw?url=' + encodeURIComponent(src.url);
      const res = await fetch(proxyUrl);
      if (!res.ok) throw new Error('HTTP ' + res.status);
      const text = await res.text();
      const parsed = parseQuestions(text, src.year, 'Automatisch erkennen');
      totalAdded += parsed.length;
      questions.push(...parsed);
      status.textContent = `${src.year}: ${parsed.length} Fragen geladen. Insgesamt neu: ${totalAdded}`;
    } catch (e) {
      failed.push(src.year);
    }
  }
  removeDuplicateQuestions();
  renderQuiz();
  status.textContent = `Fertig. Neu erkannte Fragen: ${totalAdded}. Fehlgeschlagen: ${failed.length ? failed.join(', ') : 'keine'}.`;
}

function importManualText() {
  const text = document.getElementById('manualText').value.trim();
  if (!text) return alert('Bitte zuerst Text einfügen.');
  const year = document.getElementById('manualYear').value;
  const subject = document.getElementById('manualSubject').value;
  const parsed = parseQuestions(text, year, subject);
  questions.push(...parsed);
  removeDuplicateQuestions();
  renderQuiz();
  alert(parsed.length + ' Fragen importiert.');
}

function parseQuestions(raw, defaultYear, defaultSubject) {
  let text = raw
    .replace(/\r/g, '')
    .replace(/[•●]/g, '-')
    .replace(/\u00a0/g, ' ')
    .replace(/Antwort\s*:/gi, 'Lösung:')
    .trim();

  const lines = text.split('\n').map(l => l.trim()).filter(Boolean);
  const result = [];
  let currentSubject = defaultSubject === 'Automatisch erkennen' ? 'Unbekannt' : defaultSubject;

  const subjectFromLine = (line) => {
    const l = line.toLowerCase();
    if (l.includes('biologie')) return 'Biologie';
    if (l.includes('chemie')) return 'Chemie';
    if (l.includes('physik')) return 'Physik';
    if (l.includes('mathe') || l.includes('mathematik')) return 'Mathematik';
    return null;
  };

  let block = [];
  function flushBlock() {
    if (block.length < 2) { block = []; return; }
    const q = parseBlock(block, defaultYear, currentSubject);
    if (q) result.push(q);
    block = [];
  }

  for (const line of lines) {
    const subj = subjectFromLine(line);
    if (subj) currentSubject = subj;

    const startsQuestion = /^(\d+[.)]|Frage\s*\d+|[A-ZÄÖÜ][^.!?]{10,}\?)\s*/i.test(line) || line.endsWith('?');
    const isAnswerLine = /^([A-Ea-e][).:]|[-–])\s+/.test(line);
    const isSolutionLine = /^(Lösung|Richtig|Antwort)\s*[:=]/i.test(line);

    if (startsQuestion && block.length && block.some(x => /^([A-Ea-e][).:]|[-–])\s+/.test(x))) {
      flushBlock();
    }
    if (startsQuestion || isAnswerLine || isSolutionLine || block.length) {
      block.push(line);
    }
  }
  flushBlock();
  return result;
}

function parseBlock(block, year, subject) {
  let qTextParts = [];
  let answers = [];
  let correctIndex = null;
  let explanation = '';

  for (let line of block) {
    const sol = line.match(/^(Lösung|Richtig|Antwort)\s*[:=]\s*(.+)$/i);
    if (sol) {
      const value = sol[2].trim();
      explanation = 'Lösung laut importiertem Text: ' + value;
      const letter = value.match(/[A-E]/i);
      if (letter) correctIndex = letter[0].toUpperCase().charCodeAt(0) - 65;
      continue;
    }

    const ans = line.match(/^([A-Ea-e])[).:]\s*(.+)$/) || line.match(/^[-–]\s*(.+)$/);
    if (ans) {
      const text = (ans[2] || ans[1]).trim();
      let clean = text.replace(/^✅\s*/, '').replace(/^\*\s*/, '').trim();
      if (/✅|\(richtig\)|\[richtig\]|\brichtig\b/i.test(text)) {
        correctIndex = answers.length;
        clean = clean.replace(/✅|\(richtig\)|\[richtig\]|\brichtig\b/gi, '').trim();
      }
      answers.push(clean);
    } else {
      if (!/^(Biologie|Chemie|Physik|Mathematik|BMS)$/i.test(line)) qTextParts.push(line);
    }
  }

  let qText = qTextParts.join('\n').replace(/^\d+[.)]\s*/, '').replace(/^Frage\s*\d+[:.)]?\s*/i, '').trim();
  if (!qText || answers.length < 2) return null;
  if (correctIndex === null || correctIndex < 0 || correctIndex >= answers.length) correctIndex = 0;

  return { year, subject, text: qText, answers, correctIndex, explanation };
}

function removeDuplicateQuestions() {
  const seen = new Set();
  questions = questions.filter(q => {
    const key = (q.year + q.subject + q.text).toLowerCase().replace(/\s+/g, ' ').slice(0, 250);
    if (seen.has(key)) return false;
    seen.add(key);
    return true;
  });
}

function renderQuiz() {
  const area = document.getElementById('quizArea');
  const year = document.getElementById('yearFilter').value;
  const subject = document.getElementById('subjectFilter').value;
  const search = document.getElementById('searchInput').value.toLowerCase().trim();

  const filtered = questions.filter(q =>
    (year === 'all' || q.year === year) &&
    (subject === 'all' || q.subject === subject) &&
    (!search || (q.text + ' ' + q.answers.join(' ')).toLowerCase().includes(search))
  );

  document.getElementById('totalCount').textContent = filtered.length;
  updateStats();

  if (!filtered.length) {
    area.innerHTML = `<section class="card"><p>Keine Fragen gefunden. Lade Quellen oder importiere Text manuell.</p></section>`;
    return;
  }

  area.innerHTML = filtered.map((q, idx) => {
    const globalIndex = questions.indexOf(q);
    return `<div class="question" data-index="${globalIndex}">
      <div class="q-head">
        <span class="badge">${escapeHtml(q.year)}</span>
        <span class="badge">${escapeHtml(q.subject)}</span>
        <span class="badge">Frage ${idx + 1}</span>
      </div>
      <div class="q-text">${escapeHtml(q.text)}</div>
      ${q.answers.map((a, ai) => `<button class="answer" onclick="chooseAnswer(${globalIndex}, ${ai}, this)">${String.fromCharCode(65+ai)}) ${escapeHtml(a)}</button>`).join('')}
      <div class="feedback" id="fb-${globalIndex}"></div>
    </div>`;
  }).join('');
}

function chooseAnswer(qIndex, aIndex, btn) {
  const q = questions[qIndex];
  if (answered.has(qIndex)) return;
  answered.set(qIndex, aIndex);
  const isCorrect = aIndex === q.correctIndex;
  correct.set(qIndex, isCorrect);

  const box = btn.closest('.question');
  const buttons = box.querySelectorAll('.answer');
  buttons.forEach((b, i) => {
    if (i === q.correctIndex) b.classList.add('correct');
    if (i === aIndex && !isCorrect) b.classList.add('wrong');
  });
  const fb = document.getElementById('fb-' + qIndex);
  fb.className = 'feedback ' + (isCorrect ? 'ok' : 'no');
  fb.textContent = isCorrect ? 'Richtig ✅ ' + (q.explanation || '') : 'Falsch ❌ Richtige Antwort: ' + String.fromCharCode(65 + q.correctIndex) + ') ' + q.answers[q.correctIndex] + '. ' + (q.explanation || '');
  updateStats();
}

function updateStats() {
  const answeredCount = answered.size;
  const correctCount = [...correct.values()].filter(Boolean).length;
  document.getElementById('answeredCount').textContent = answeredCount;
  document.getElementById('correctCount').textContent = correctCount;
  document.getElementById('scorePercent').textContent = answeredCount ? Math.round(correctCount / answeredCount * 100) + '%' : '0%';
}

function shuffleQuestions() {
  for (let i = questions.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [questions[i], questions[j]] = [questions[j], questions[i]];
  }
  answered.clear();
  correct.clear();
  renderQuiz();
}

function resetQuiz() {
  answered.clear();
  correct.clear();
  renderQuiz();
}

function escapeHtml(str) {
  return String(str).replace(/[&<>'"]/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;',"'":'&#39;','"':'&quot;'}[c]));
}

setup();
</script>
</body>
</html>
