<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>MedAT BMS Quiz 2019–2026</title>
  <style>
    :root { --bg:#0f172a; --card:#111827; --muted:#94a3b8; --text:#e5e7eb; --blue:#38bdf8; --green:#22c55e; --red:#ef4444; --yellow:#facc15; }
    * { box-sizing:border-box; }
    body { margin:0; font-family:Arial, sans-serif; background:linear-gradient(135deg,#020617,#172554); color:var(--text); }
    header { padding:28px 18px; text-align:center; background:rgba(2,6,23,.8); position:sticky; top:0; z-index:10; border-bottom:1px solid rgba(255,255,255,.12); }
    h1 { margin:0 0 8px; font-size:clamp(24px,4vw,42px); }
    .sub { color:var(--muted); max-width:850px; margin:0 auto; line-height:1.5; }
    main { max-width:1100px; margin:0 auto; padding:20px; }
    .panel, .question { background:rgba(17,24,39,.92); border:1px solid rgba(255,255,255,.12); border-radius:18px; padding:18px; margin:14px 0; box-shadow:0 10px 35px rgba(0,0,0,.25); }
    .controls { display:flex; flex-wrap:wrap; gap:10px; align-items:center; justify-content:center; }
    button, select, input { border:0; border-radius:12px; padding:11px 14px; font-size:15px; }
    button { cursor:pointer; background:var(--blue); color:#00111a; font-weight:bold; }
    button.secondary { background:#334155; color:var(--text); }
    button:disabled { opacity:.5; cursor:not-allowed; }
    select, input { background:#020617; color:var(--text); border:1px solid #334155; }
    .stats { display:grid; grid-template-columns:repeat(auto-fit,minmax(160px,1fr)); gap:10px; margin-top:12px; }
    .stat { background:#020617; border:1px solid #334155; border-radius:14px; padding:12px; text-align:center; }
    .stat b { display:block; font-size:22px; color:var(--yellow); }
    .tabs { display:flex; flex-wrap:wrap; gap:8px; justify-content:center; margin:16px 0; }
    .tab { background:#1e293b; color:var(--text); }
    .tab.active { background:var(--yellow); color:#111827; }
    .meta { color:var(--muted); font-size:14px; margin-bottom:8px; }
    .qtext { font-size:18px; line-height:1.45; margin-bottom:13px; }
    .answers { display:grid; gap:9px; }
    .answer { background:#020617; color:var(--text); text-align:left; border:1px solid #334155; border-radius:12px; padding:12px; cursor:pointer; line-height:1.35; }
    .answer:hover { border-color:var(--blue); }
    .answer.correct { background:rgba(34,197,94,.18); border-color:var(--green); }
    .answer.wrong { background:rgba(239,68,68,.18); border-color:var(--red); }
    .feedback { margin-top:10px; font-weight:bold; display:none; }
    .ok { color:var(--green); } .bad { color:var(--red); }
    .small { color:var(--muted); font-size:13px; line-height:1.5; }
    textarea { width:100%; min-height:180px; background:#020617; color:var(--text); border:1px solid #334155; border-radius:14px; padding:12px; }
    details summary { cursor:pointer; color:var(--blue); font-weight:bold; }
    .hidden { display:none; }
  </style>
</head>
<body>
<header>
  <h1>MedAT BMS Quiz 2019–2026</h1>
  <p class="sub">Biologie, Chemie, Physik und Mathematik. Die Seite lädt die Fragen aus deinen Google-Docs, filtert automatisch nur den BMS-Teil und macht daraus anklickbare Fragen.</p>
</header>
<main>
  <section class="panel">
    <div class="controls">
      <button onclick="loadAll()">Alle MedAT-Jahre laden</button>
      <select id="yearFilter" onchange="render()"><option value="all">Alle Jahre</option></select>
      <select id="subjectFilter" onchange="render()"><option value="all">Alle Fächer</option><option>Biologie</option><option>Chemie</option><option>Physik</option><option>Mathe</option><option>Mathematik</option></select>
      <input id="searchBox" placeholder="Suchen…" oninput="render()" />
      <button class="secondary" onclick="shuffleQuestions()">Mischen</button>
      <button class="secondary" onclick="resetQuiz()">Zurücksetzen</button>
    </div>
    <div class="stats">
      <div class="stat"><span>Geladene Fragen</span><b id="totalCount">0</b></div>
      <div class="stat"><span>Beantwortet</span><b id="answeredCount">0</b></div>
      <div class="stat"><span>Richtig</span><b id="correctCount">0</b></div>
      <div class="stat"><span>Punkte</span><b id="scorePercent">0%</b></div>
    </div>
    <p class="small">Hinweis: In den Altfragen-Dokumenten steht die richtige Lösung meistens als erste Antwort. Diese Webseite markiert deshalb Antwort 1 als richtig. Bei unvollständigen oder diskutierten Altfragen bitte selbst kontrollieren.</p>
  </section>

  <section class="panel">
    <details>
      <summary>Falls Google-Docs im Browser blockiert werden: Text hier einfügen</summary>
      <p class="small">Öffne ein Dokument, kopiere den BMS-Teil mit Biologie/Chemie/Physik/Mathe hier hinein und klicke auf „Eingefügten Text importieren“.</p>
      <textarea id="manualText" placeholder="Hier den Text aus dem Google Doc einfügen…"></textarea>
      <div class="controls" style="margin-top:10px">
        <select id="manualYear"><option>2019</option><option>2020</option><option>2021</option><option>2022</option><option>2023</option><option>2024</option><option>2025</option><option>2026</option></select>
        <button onclick="importManual()">Eingefügten Text importieren</button>
      </div>
    </details>
  </section>

  <div class="tabs" id="yearTabs"></div>
  <section id="status" class="panel">Klicke auf „Alle MedAT-Jahre laden“.</section>
  <section id="quiz"></section>
</main>

<script>
const SOURCES = [
  {year:'2019', id:'1pGLtgwooCGv0-pJBaf_sx3KwenJu4RH9JYt9H9tzYBc'},
  {year:'2020', id:'193kVOscyOrz4I2WXclY1hzypzOCPJn5FFjY8Xlz7wyE'},
  {year:'2021', id:'1Gcr5T2PuNklwBHkV6g-WSHm1rSfDWaFhpTm5R_LenF0'},
  {year:'2022', id:'1ayHsWvMa8Jg5_cln3YbBH2YsUUazqUIHZikVG4Y6I7c'},
  {year:'2023', id:'1oT0XwBrMxteZBhuqBl_eVwfrN8yW8G4MCumBHzPAKBA'},
  {year:'2024', id:'1zJJ0j3JUBGzj95M8IHBi-nnsSRDrOgJKeGmmOiBzHtU'},
  {year:'2025', id:'1mUff60AuVE0SvhWmSeEvlft9KI0mq6xt-bx_A7CDkRA'},
  {year:'2026', id:'1zwY5nQVOZg2nK0KzfTG6iFe0mEerijJHL0M16ulHyzE'}
];
let questions = [];
let answered = new Map();
let activeYear = 'all';

function mobileUrl(id){ return `https://docs.google.com/document/d/${id}/mobilebasic`; }
function proxyUrls(url){ return [url, `https://api.allorigins.win/raw?url=${encodeURIComponent(url)}`, `https://corsproxy.io/?${encodeURIComponent(url)}`]; }

async function fetchDocText(id){
  const url = mobileUrl(id);
  let lastErr = '';
  for (const u of proxyUrls(url)) {
    try {
      const res = await fetch(u);
      if (!res.ok) throw new Error(res.status + ' ' + res.statusText);
      const html = await res.text();
      const doc = new DOMParser().parseFromString(html, 'text/html');
      const txt = (doc.body ? doc.body.innerText : html).replace(/\u00a0/g,' ');
      if (txt.includes('Basiskenntnistest') || txt.includes('Biologie')) return txt;
      lastErr = 'Text enthält keinen BMS-Teil';
    } catch(e){ lastErr = e.message; }
  }
  throw new Error(lastErr || 'Konnte Dokument nicht laden');
}

function extractBMS(text){
  let start = text.search(/Basiskenntnistest|BMS|Biologie\s*\(/i);
  if (start < 0) start = 0;
  let end = text.slice(start).search(/\n#\s*(Textverständnis|Kognitive|Sozial|Figuren|Zahlenfolgen|Wortflüssigkeit|Implikationen|Gedächtnis)/i);
  return end >= 0 ? text.slice(start, start + end) : text.slice(start);
}

function normalizeSubject(s){
  if (/mathe|mathematik/i.test(s)) return 'Mathe';
  if (/bio/i.test(s)) return 'Biologie';
  if (/chem/i.test(s)) return 'Chemie';
  if (/phys/i.test(s)) return 'Physik';
  return s.trim();
}

function parseQuestions(text, year){
  const bms = extractBMS(text);
  const lines = bms.split(/\r?\n/).map(x => x.trim()).filter(Boolean);
  const out = [];
  let subject = '';
  let current = null;

  const push = () => {
    if (current && current.text && current.answers.length >= 2) {
      current.id = `${current.year}-${current.subject}-${out.length+1}-${Math.random().toString(36).slice(2,7)}`;
      current.correct = 0;
      out.push(current);
    }
  };

  for (let raw of lines) {
    let line = raw.replace(/^#+\s*/, '').replace(/^\*\s*/, '').trim();
    const subjMatch = line.match(/^(Biologie|Chemie|Physik|Mathe|Mathematik)\b/i);
    if (subjMatch) { push(); current = null; subject = normalizeSubject(subjMatch[1]); continue; }
    if (!subject) continue;

    const qMatch = line.match(/^(?:\d+[\.)]\s*)?(?:★\s*)?(?:\[[^\]]+\]\s*)?(.+\?)$/) || line.match(/^\d+[\.)]\s*###\s*(.+)$/) || line.match(/^###\s*(.+)$/);
    const numberedHash = raw.match(/^\d+\.\s*###\s*(.+)$/);
    if (numberedHash || (line.includes('?') && !/^\d+[\.)]\s+/.test(line))) {
      const qText = (numberedHash ? numberedHash[1] : line).replace(/^★\s*/, '').trim();
      if (qText.length > 3) { push(); current = {year, subject, text:qText, answers:[]}; continue; }
    }

    const aMatch = line.match(/^\d+[\.)]\s*(.+)$/);
    if (aMatch && current) {
      let ans = aMatch[1].replace(/^###\s*/, '').trim();
      if (ans && !ans.match(/^Link zu/i)) current.answers.push(ans);
      continue;
    }

    if (current && current.answers.length === 0 && line.length > 8 && !/^[-*]+$/.test(line) && !/^Link zu/i.test(line)) {
      if (current.text.length < 220) current.text += ' ' + line;
    }
  }
  push();
  return out;
}

async function loadAll(){
  questions = [];
  answered.clear();
  document.getElementById('status').innerHTML = 'Lade Dokumente…';
  const results = [];
  for (const src of SOURCES) {
    try {
      document.getElementById('status').innerHTML = `Lade MedAT ${src.year}…`;
      const text = await fetchDocText(src.id);
      const qs = parseQuestions(text, src.year);
      questions.push(...qs);
      results.push(`✅ ${src.year}: ${qs.length} BMS-Fragen`);
    } catch(e) {
      results.push(`❌ ${src.year}: konnte nicht automatisch geladen werden (${e.message})`);
    }
  }
  fillYearOptions();
  document.getElementById('status').innerHTML = results.join('<br>') + '<br><br><span class="small">Wenn ein Jahr rot ist: Google blockiert das Laden. Dann dieses Dokument öffnen, BMS-Teil kopieren und oben manuell importieren.</span>';
  render();
}

function importManual(){
  const txt = document.getElementById('manualText').value;
  const year = document.getElementById('manualYear').value;
  if (!txt.trim()) return alert('Bitte zuerst Text einfügen.');
  const qs = parseQuestions(txt, year);
  questions = questions.filter(q => q.year !== year).concat(qs);
  fillYearOptions();
  document.getElementById('status').innerHTML = `✅ Manuell importiert: ${year}, ${qs.length} BMS-Fragen`;
  render();
}

function fillYearOptions(){
  const years = [...new Set(questions.map(q=>q.year))].sort();
  const sel = document.getElementById('yearFilter');
  sel.innerHTML = '<option value="all">Alle Jahre</option>' + years.map(y=>`<option>${y}</option>`).join('');
  const tabs = document.getElementById('yearTabs');
  tabs.innerHTML = '<button class="tab '+(activeYear==='all'?'active':'')+'" onclick="setYear(\'all\')">Alle</button>' +
    years.map(y=>`<button class="tab ${activeYear===y?'active':''}" onclick="setYear('${y}')">${y}</button>`).join('');
}
function setYear(y){ activeYear = y; document.getElementById('yearFilter').value = y; fillYearOptions(); render(); }
function shuffleQuestions(){ questions.sort(()=>Math.random()-.5); render(); }
function resetQuiz(){ answered.clear(); render(); }

function filtered(){
  const yf = document.getElementById('yearFilter').value;
  const sf = document.getElementById('subjectFilter').value;
  const search = document.getElementById('searchBox').value.toLowerCase();
  if (yf !== activeYear) activeYear = yf;
  return questions.filter(q => (yf==='all'||q.year===yf) && (sf==='all'||q.subject===normalizeSubject(sf)) && (!search || (q.text+' '+q.answers.join(' ')).toLowerCase().includes(search)));
}

function choose(qid, idx){
  if (answered.has(qid)) return;
  const q = questions.find(x=>x.id===qid);
  answered.set(qid, idx === q.correct);
  render();
}

function render(){
  fillYearOptions();
  const list = filtered();
  let correct = [...answered.values()].filter(Boolean).length;
  document.getElementById('totalCount').textContent = questions.length;
  document.getElementById('answeredCount').textContent = answered.size;
  document.getElementById('correctCount').textContent = correct;
  document.getElementById('scorePercent').textContent = answered.size ? Math.round(correct/answered.size*100)+'%' : '0%';
  const quiz = document.getElementById('quiz');
  if (!list.length) { quiz.innerHTML = '<section class="panel">Noch keine passenden Fragen geladen.</section>'; return; }
  quiz.innerHTML = list.map((q,i)=>{
    const wasAnswered = answered.has(q.id);
    const ok = answered.get(q.id);
    return `<article class="question">
      <div class="meta">${q.year} • ${q.subject} • Frage ${i+1}</div>
      <div class="qtext">${escapeHtml(q.text)}</div>
      <div class="answers">${q.answers.map((a,idx)=>{
        let cls = 'answer';
        if (wasAnswered && idx===q.correct) cls += ' correct';
        if (wasAnswered && idx!==q.correct && !ok && answered.get(q.id)===false) cls += '';
        return `<button class="${cls}" onclick="choose('${q.id}',${idx})">${idx+1}. ${escapeHtml(a)}</button>`;
      }).join('')}</div>
      <div class="feedback ${wasAnswered ? '' : 'hidden'} ${ok ? 'ok':'bad'}">${ok ? 'Richtig ✅':'Falsch ❌'} — Richtige Lösung: ${escapeHtml(q.answers[q.correct]||'Antwort 1')}</div>
    </article>`;
  }).join('');
}
function escapeHtml(s){ return String(s).replace(/[&<>"]/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;'}[c])); }
fillYearOptions();
render();
</script>
</body>
</html>
