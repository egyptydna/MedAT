<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>MedAT BMS Quiz 2019–2026</title>
  <style>
    :root{--bg:#f5f7fb;--card:#fff;--text:#101828;--muted:#667085;--primary:#2563eb;--ok:#16a34a;--bad:#dc2626;--border:#d0d5dd;--soft:#eef4ff}
    *{box-sizing:border-box} body{margin:0;font-family:Arial,Helvetica,sans-serif;background:var(--bg);color:var(--text)}
    header{background:linear-gradient(135deg,#1e40af,#2563eb);color:white;padding:28px 18px;text-align:center}
    header h1{margin:0 0 8px;font-size:28px} header p{margin:0;opacity:.92}
    .wrap{max-width:1100px;margin:0 auto;padding:18px}
    .panel,.card{background:var(--card);border:1px solid var(--border);border-radius:16px;box-shadow:0 8px 24px rgba(16,24,40,.06)}
    .panel{padding:16px;margin-bottom:16px}.row{display:flex;gap:10px;flex-wrap:wrap;align-items:center}
    select,input,button,textarea{font:inherit;border:1px solid var(--border);border-radius:10px;padding:10px;background:white}
    button{cursor:pointer;background:var(--primary);color:white;border-color:var(--primary);font-weight:700}button.secondary{background:white;color:var(--primary)}
    button:disabled{opacity:.5;cursor:not-allowed}.stats{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:10px;margin-top:12px}
    .stat{background:var(--soft);padding:12px;border-radius:12px;font-weight:700}.muted{color:var(--muted)}
    .card{padding:18px;margin-bottom:16px}.meta{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:10px}
    .pill{background:#eef2ff;color:#3730a3;border-radius:999px;padding:5px 9px;font-size:13px;font-weight:700}
    .q{font-size:20px;font-weight:800;line-height:1.35;margin:8px 0 14px}.answers{display:grid;gap:10px}
    .ans{width:100%;text-align:left;background:white;color:var(--text);border:1px solid var(--border);font-weight:600;padding:13px;border-radius:12px}
    .ans.correct{border-color:var(--ok);background:#ecfdf3;color:#067647}.ans.wrong{border-color:var(--bad);background:#fef3f2;color:#b42318}
    .solution{margin-top:12px;padding:12px;border-left:4px solid var(--primary);background:#f8fafc;border-radius:8px}.hidden{display:none}
    textarea{width:100%;min-height:150px;font-family:ui-monospace,Menlo,Consolas,monospace}.small{font-size:13px}.links li{margin:5px 0}
  </style>
</head>
<body>
<header>
  <h1>MedAT BMS Quiz 2019–2026</h1>
  <p>Biologie · Chemie · Physik · Mathematik</p>
</header>
<main class="wrap">
  <section class="panel">
    <div class="row">
      <label>Jahr <select id="yearFilter"><option value="all">Alle</option></select></label>
      <label>Fach <select id="subjectFilter"><option value="all">Alle</option><option>Biologie</option><option>Chemie</option><option>Physik</option><option>Mathematik</option></select></label>
      <input id="search" placeholder="Suche in Fragen..." />
      <button onclick="shuffleQuestions()">Mischen</button>
      <button class="secondary" onclick="resetQuiz()">Zurücksetzen</button>
    </div>
    <div class="stats">
      <div class="stat">Fragen: <span id="total">0</span></div>
      <div class="stat">Beantwortet: <span id="answered">0</span></div>
      <div class="stat">Richtig: <span id="correct">0</span></div>
      <div class="stat">Quote: <span id="percent">0%</span></div>
    </div>
  </section>

  <section class="panel" id="importPanel">
    <h2>Fragen importieren</h2>
    <p class="muted">Füge hier JSON-Fragen ein. Format siehe Beispiel unten. Danach bleiben die Fragen im Browser gespeichert.</p>
    <textarea id="jsonInput" placeholder='[
  {
    "year": 2020,
    "subject": "Biologie",
    "question": "Welche Aussage ist richtig?",
    "answers": ["Antwort A", "Antwort B", "Antwort C", "Antwort D", "Antwort E"],
    "correct": 0,
    "explanation": "Kurze Begründung."
  }
]'></textarea>
    <div class="row" style="margin-top:10px">
      <button onclick="importQuestions()">Importieren</button>
      <button class="secondary" onclick="clearSaved()">Gespeicherte Fragen löschen</button>
    </div>
  </section>

  <section id="quiz"></section>

  <section class="panel">
    <h2>Quellen-Dokumente</h2>
    <p class="muted">Diese Links sind nur als Quellen notiert. Der Fragetext muss wegen Google-Docs/JavaScript einmal exportiert oder eingefügt werden.</p>
    <ul class="links small">
      <li>2019: https://docs.google.com/document/d/1pGLtgwooCGv0-pJBaf_sx3KwenJu4RH9JYt9H9tzYBc/edit?usp=sharing</li>
      <li>2020: https://docs.google.com/document/d/193kVOscyOrz4I2WXclY1hzypzOCPJn5FFjY8Xlz7wyE/edit?usp=sharing</li>
      <li>2021: https://docs.google.com/document/d/1Gcr5T2PuNklwBHkV6g-WSHm1rSfDWaFhpTm5R_LenF0/edit?usp=sharing</li>
      <li>2022: https://docs.google.com/document/d/1ayHsWvMa8Jg5_cln3YbBH2YsUUazqUIHZikVG4Y6I7c/edit?usp=sharing</li>
      <li>2023: https://docs.google.com/document/d/1oT0XwBrMxteZBhuqBl_eVwfrN8yW8G4MCumBHzPAKBA/edit?usp=sharing</li>
      <li>2024: https://docs.google.com/document/d/1zJJ0j3JUBGzj95M8IHBi-nnsSRDrOgJKeGmmOiBzHtU/edit?usp=sharing</li>
      <li>2025: https://docs.google.com/document/d/1mUff60AuVE0SvhWmSeEvlft9KI0mq6xt-bx_A7CDkRA/edit?usp=sharing</li>
      <li>2026: https://docs.google.com/document/d/1zwY5nQVOZg2nK0KzfTG6iFe0mEerijJHL0M16ulHyzE/edit?usp=sharing</li>
    </ul>
  </section>
</main>
<script>
// HIER können alle Fragen fest eingebaut werden.
// Ich habe keine Fake-Fragen ergänzt, weil die Google Docs für den Export hier nicht auslesbar waren.
let QUESTIONS = [];

const saved = localStorage.getItem('medat_bms_questions');
if(saved){ try{ QUESTIONS = JSON.parse(saved); }catch(e){} }
let state = {};
function setupYears(){
  const sel=document.getElementById('yearFilter');
  const years=[...new Set(QUESTIONS.map(q=>q.year))].sort();
  sel.innerHTML='<option value="all">Alle</option>'+years.map(y=>`<option>${y}</option>`).join('');
}
function getFiltered(){
  const y=document.getElementById('yearFilter').value;
  const s=document.getElementById('subjectFilter').value;
  const term=document.getElementById('search').value.toLowerCase();
  return QUESTIONS.filter(q=>(y==='all'||String(q.year)===y)&&(s==='all'||q.subject===s)&&(!term||q.question.toLowerCase().includes(term)||q.answers.join(' ').toLowerCase().includes(term)));
}
function render(){
  setupYears();
  const list=getFiltered();
  const box=document.getElementById('quiz');
  box.innerHTML=list.map((q,i)=>`<article class="card">
    <div class="meta"><span class="pill">${q.year}</span><span class="pill">${q.subject}</span><span class="pill">Frage ${i+1}</span></div>
    <div class="q">${escapeHtml(q.question)}</div>
    <div class="answers">${q.answers.map((a,idx)=>`<button class="ans" onclick="answer('${q.id||i}',${idx},${q.correct},this)">${String.fromCharCode(65+idx)}. ${escapeHtml(a)}</button>`).join('')}</div>
    <div class="solution hidden">${escapeHtml(q.explanation||'')}</div>
  </article>`).join('') || '<section class="card"><b>Noch keine Fragen eingefügt.</b><p class="muted">Importiere die BMS-Fragen als JSON oder sende mir die Dateien als TXT/DOCX/PDF, dann baue ich sie direkt hier ein.</p></section>';
  updateStats();
}
function answer(id,choice,correct,btn){
  if(state[id]!==undefined) return;
  state[id]=choice===correct;
  const card=btn.closest('.card');
  [...card.querySelectorAll('.ans')].forEach((b,idx)=>{b.disabled=true;if(idx===correct)b.classList.add('correct');if(idx===choice&&idx!==correct)b.classList.add('wrong')});
  card.querySelector('.solution').classList.remove('hidden');
  updateStats();
}
function updateStats(){
  const total=getFiltered().length, answered=Object.keys(state).length, correct=Object.values(state).filter(Boolean).length;
  document.getElementById('total').textContent=total; document.getElementById('answered').textContent=answered; document.getElementById('correct').textContent=correct; document.getElementById('percent').textContent=answered?Math.round(correct/answered*100)+'%':'0%';
}
function shuffleQuestions(){ QUESTIONS.sort(()=>Math.random()-0.5); render(); }
function resetQuiz(){ state={}; render(); }
function importQuestions(){
  try{ const arr=JSON.parse(document.getElementById('jsonInput').value); if(!Array.isArray(arr)) throw new Error('JSON muss eine Liste sein.');
    QUESTIONS=arr.map((q,i)=>({...q,id:q.id||('q'+i)})); localStorage.setItem('medat_bms_questions',JSON.stringify(QUESTIONS)); state={}; render(); alert('Importiert: '+QUESTIONS.length+' Fragen');
  }catch(e){ alert('Fehler beim Import: '+e.message); }
}
function clearSaved(){ localStorage.removeItem('medat_bms_questions'); QUESTIONS=[]; state={}; render(); }
function escapeHtml(x){return String(x??'').replace(/[&<>'"]/g,m=>({'&':'&amp;','<':'&lt;','>':'&gt;',"'":'&#39;','"':'&quot;'}[m]));}
['yearFilter','subjectFilter','search'].forEach(id=>document.getElementById(id).addEventListener('input',render));
render();
</script>
</body>
</html>
