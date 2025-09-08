<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>주간 공부 타이머</title>
  <style>
    :root{
      --bg:#0b1020; /* deep navy */
      --card:#101935;
      --ink:#e9efff;
      --ink-dim:#b8c3e6;
      --accent:#6ea8ff;
      --accent-2:#8be9d6;
      --danger:#ff6e6e;
      --ok:#82f7a1;
      --muted:#243055;
      --ring: 0 0 0 3px rgba(110,168,255,.25);
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Noto Sans KR", Arial, "Apple SD Gothic Neo", "Malgun Gothic", sans-serif;
      background: radial-gradient(1000px 600px at 15% -10%, #1a2550 0%, transparent 70%),
                  radial-gradient(800px 500px at 110% 10%, #12304d 0%, transparent 65%),
                  var(--bg);
      color:var(--ink);
      display:flex; align-items:center; justify-content:center; padding:24px;
    }
    .wrap{ width:min(1000px, 96vw); }
    .app{
      background: linear-gradient(180deg, #0f1732 0%, #0d142b 100%);
      border:1px solid #1b2650;
      border-radius:20px;
      box-shadow: 0 20px 60px rgba(0,0,0,.35), inset 0 1px 0 rgba(255,255,255,.03);
      overflow:hidden;
    }
    header{
      display:flex; align-items:center; justify-content:space-between; gap:12px;
      padding:18px 22px; border-bottom:1px solid #1b2650; background:rgba(16,25,53,.5);
    }
    header h1{ font-size:18px; margin:0; font-weight:700; letter-spacing:.3px; }
    header .range{ font-size:13px; color:var(--ink-dim); }

    .grid{ display:grid; grid-template-columns: 1.15fr 1fr; gap:18px; padding:20px; }
    @media (max-width: 820px){ .grid{ grid-template-columns: 1fr; } }

    .card{ background:var(--card); border:1px solid #1c274f; border-radius:16px; padding:18px; }
    .title{ font-size:13px; color:var(--ink-dim); letter-spacing:.2px; margin-bottom:10px; }

    .big-time, .big-num{ font-variant-numeric: tabular-nums; font-size: clamp(36px, 7.5vw, 64px); line-height:1.05; font-weight:800; letter-spacing:.5px; }
    .sub{ color:var(--ink-dim); font-size:12px; margin-top:6px; display:flex; flex-wrap:wrap; gap:8px; }
    .pill{ display:inline-flex; align-items:center; gap:8px; font-size:12px; background:#0f1a36; border:1px solid #243463; padding:8px 10px; border-radius:999px; }
    .muted{ color:var(--ink-dim); }

    .controls{ display:flex; flex-wrap:wrap; gap:10px; margin-top:14px; }
    button{
      appearance:none; border:1px solid #28407a; background:#122045; color:var(--ink);
      padding:10px 14px; border-radius:12px; font-weight:700; letter-spacing:.2px; cursor:pointer;
      transition: transform .05s ease, background .2s ease, border-color .2s ease, box-shadow .2s ease;
    }
    button:hover{ background:#172b5a; border-color:#33509e; }
    button:active{ transform: translateY(1px); }
    button.primary{ background:linear-gradient(180deg, #2a63ff 0%, #1d4ed8 100%); border-color:#2a63ff; }
    button.primary:hover{ filter:brightness(1.05); box-shadow: var(--ring); }
    button.danger{ background: linear-gradient(180deg, #ff5b5b 0%, #e03636 100%); border-color:#ff5b5b; }

    .goal-box{ display:flex; align-items:center; gap:10px; margin-top:8px; }
    .goal-box input{
      width:90px; padding:10px 12px; border-radius:12px; border:1px solid #273a6f; background:#0c1736; color:var(--ink);
      font-weight:700; text-align:right; outline:none; transition: box-shadow .2s ease, border-color .2s ease;
    }
    .goal-box input:focus{ border-color:#3d62c2; box-shadow: var(--ring); }

    .progress{ margin-top:8px; height:12px; border-radius:999px; background:#0f1a36; border:1px solid #1c274f; position:relative; overflow:hidden; }
    .progress .bar{ position:absolute; left:0; top:0; bottom:0; width:0%; background: linear-gradient(90deg, var(--accent), var(--accent-2)); border-right:1px solid rgba(255,255,255,.25); }
    .progress .label{ position:absolute; inset:0; display:flex; align-items:center; justify-content:center; font-size:10px; color:#0c1226; font-weight:800; text-shadow: 0 1px 0 rgba(255,255,255,.35); mix-blend-mode:screen; }

    /* Subjects table */
    .subjects{ display:grid; gap:10px; }
    .row{
      display:grid; grid-template-columns: 1fr auto auto; gap:12px; align-items:center;
      background:#0f1a36; border:1px solid #1c274f; border-radius:14px; padding:12px;
    }
    .row .name{ font-weight:800; letter-spacing:.2px; display:flex; align-items:center; gap:8px; }
    .row .time{ font-variant-numeric: tabular-nums; font-weight:800; }
    .row .right{ display:flex; gap:8px; align-items:center; }
    .tiny-btn{ padding:8px 10px; border-radius:10px; font-size:12px; }
    .subgoal{ display:flex; align-items:center; gap:8px; }
    .subgoal input{ width:72px; padding:6px 8px; border-radius:10px; border:1px solid #273a6f; background:#0c1736; color:var(--ink); text-align:right; font-weight:700; }
    .now{ background:#132861; border-color:#3551a8; }

    footer{ padding:14px 20px; border-top:1px solid #1b2650; display:flex; flex-wrap:wrap; gap:10px; justify-content:space-between; align-items:center; }
    .note{ font-size:12px; color:var(--ink-dim); }
    .tiny{ font-size:11px; opacity:.85; }
    a { color: var(--accent-2); text-decoration: none; }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="app" role="application" aria-label="주간 공부 타이머">
      <header>
        <h1>주간 공부 타이머</h1>
        <div class="range" id="weekRange">—</div>
      </header>

      <div class="grid">
        <section class="card">
          <div class="title">이번 주 누적(전체)</div>
          <div class="big-time" id="weeklyTime" aria-live="polite">00:00:00</div>
          <div class="sub">
            <span class="pill"><span class="muted">주 시작:</span><strong id="weekStartTxt">—</strong></span>
            <span class="pill"><span class="muted">주 끝:</span><strong id="weekEndTxt">—</strong></span>
            <span class="pill" id="nowStudying">현재: —</span>
          </div>
          <div class="controls">
            <button id="pauseAllBtn">전체 일시정지</button>
            <button id="resetBtn" class="danger" title="이번 주 누적을 0으로 초기화">이번 주 리셋</button>
          </div>
        </section>

        <section class="card">
          <div class="title">전체 목표 & 진행률</div>
          <div class="goal-box">
            <label for="goalInput" class="muted">주간 목표(시간)</label>
            <input id="goalInput" type="number" min="0" step="0.5" inputmode="decimal" aria-label="주간 목표 시간" />
            <button id="saveGoalBtn">저장</button>
          </div>
          <div class="progress" aria-label="주간 목표 진행률">
            <div class="bar" id="progressBar"></div>
            <div class="label" id="progressLabel">0%</div>
          </div>
          <div class="sub" id="goalNote" style="margin-top:8px;">목표를 설정해 두면 막대가 채워져요.</div>
        </section>

        <!-- Problems Counter Card -->
        <section class="card" id="problemsCard">
          <div class="title">문제수 카운터 (이번 주)</div>
          <div class="big-num" id="problemsCount">0</div>
          <div class="controls">
            <button id="add1Btn" class="primary tiny-btn">+1</button>
            <button id="add5Btn" class="tiny-btn">+5</button>
            <button id="minus1Btn" class="tiny-btn">-1</button>
            <button id="resetProblemsBtn" class="danger tiny-btn">초기화</button>
          </div>
          <div class="sub">버튼을 눌러 문제수를 늘리거나 줄일 수 있어요. 새 주(월 00:00)에 자동 리셋.</div>
        </section>

        <section class="card" style="grid-column:1/-1;">
          <div class="title">과목별 타이머 (국·영·수·사·과)</div>
          <div class="subjects" id="subjects"></div>
        </section>
      </div>

      <footer>
        <div class="note">
          ISO 기준 <strong>월요일 00:00</strong> 새 주 시작 · 주간 자동 리셋 (기기 현지 시간)
        </div>
        <div class="tiny">v2.1 • 과목별 + 문제수 카운터 · 로컬 저장</div>
      </footer>
    </div>
  </div>

  <script>
    // ===== Config & Utils ===== //
    const SUBJECTS = [
      { id:'kor', name:'국어', emoji:'📝' },
      { id:'eng', name:'영어', emoji:'🔤' },
      { id:'math', name:'수학', emoji:'➗' },
      { id:'soc', name:'사회', emoji:'🌏' },
      { id:'sci', name:'과학', emoji:'🧪' },
    ];

    const fmt2 = n => String(n).padStart(2, '0');
    function formatHMS(ms){
      if(!Number.isFinite(ms) || ms < 0) ms = 0;
      const totalSec = Math.floor(ms/1000);
      const h = Math.floor(totalSec/3600);
      const m = Math.floor((totalSec%3600)/60);
      const s = totalSec%60;
      return `${fmt2(h)}:${fmt2(m)}:${fmt2(s)}`;
    }
    function getWeekStart(d=new Date()){
      const t = new Date(d.getFullYear(), d.getMonth(), d.getDate());
      const day = (t.getDay() + 6) % 7; // 0=Mon ... 6=Sun
      t.setDate(t.getDate() - day);
      t.setHours(0,0,0,0);
      return t;
    }
    function getWeekEnd(d=new Date()){ const s=getWeekStart(d); const e=new Date(s); e.setDate(s.getDate()+7); return e; }
    function weekKeyFromDate(d=new Date()){ return getWeekStart(d).toISOString().slice(0,10); }
    function formatDateK(d){
      const y=d.getFullYear();
      const m=d.getMonth()+1; const day=d.getDate();
      const weekday=['일','월','화','수','목','금','토'][d.getDay()];
      return `${y}.${fmt2(m)}.${fmt2(day)}(${weekday})`;
    }

    // ===== State (v2.1) ===== //
    const LS_KEY = 'wst.state.v2';
    const defaultSubjects = () => ({ kor:{accumMs:0, goalHrs:0}, eng:{accumMs:0, goalHrs:0}, math:{accumMs:0, goalHrs:0}, soc:{accumMs:0, goalHrs:0}, sci:{accumMs:0, goalHrs:0} });
    const defaultState = () => ({
      version: 2,
      weekKey: weekKeyFromDate(new Date()),
      isRunning: false,
      startTs: null,
      active: null,
      goalHrs: 0,      // overall weekly goal (optional)
      legacyMs: 0,     // carry-over from v1
      subjects: defaultSubjects(),
      problems: 0      // weekly problem counter
    });

    function migrateFromV1(obj){
      const s = defaultState();
      s.weekKey = obj.weekKey || s.weekKey;
      s.goalHrs = Number(obj.goalHrs||0);
      s.legacyMs = Math.max(0, Number(obj.accumMs||0)); // keep old total once
      if(obj.isRunning){ s.isRunning = true; s.startTs = Date.now(); s.active = null; }
      return s;
    }

    function loadState(){
      try{
        const raw2 = localStorage.getItem(LS_KEY);
        if(raw2){
          const obj2 = JSON.parse(raw2);
          // Backfill missing fields (like problems)
          const base = Object.assign(defaultState(), obj2);
          if(typeof base.problems !== 'number') base.problems = 0;
          for(const id of Object.keys(defaultSubjects())){
            base.subjects[id] = Object.assign({accumMs:0, goalHrs:0}, base.subjects?.[id]);
          }
          return base;
        }
        // Attempt migrate from v1 key
        const raw1 = localStorage.getItem('wst.state.v1');
        if(raw1){
          const obj1 = JSON.parse(raw1);
          const migrated = migrateFromV1(obj1);
          localStorage.setItem(LS_KEY, JSON.stringify(migrated));
          return migrated;
        }
        return defaultState();
      }catch(err){ console.warn('load err', err); return defaultState(); }
    }
    function saveState(){ localStorage.setItem(LS_KEY, JSON.stringify(state)); }

    let state = loadState();

    // ===== DOM ===== //
    const elWeekRange = document.getElementById('weekRange');
    const elWeekStartTxt = document.getElementById('weekStartTxt');
    const elWeekEndTxt = document.getElementById('weekEndTxt');
    const elWeeklyTime = document.getElementById('weeklyTime');
    const elPauseAll = document.getElementById('pauseAllBtn');
    const elReset = document.getElementById('resetBtn');
    const elGoalInput = document.getElementById('goalInput');
    const elSaveGoal = document.getElementById('saveGoalBtn');
    const elProg = document.getElementById('progressBar');
    const elProgLabel = document.getElementById('progressLabel');
    const elGoalNote = document.getElementById('goalNote');
    const elNow = document.getElementById('nowStudying');
    const elSubjects = document.getElementById('subjects');

    // Problems DOM
    const elProblems = document.getElementById('problemsCount');
    const btnAdd1 = document.getElementById('add1Btn');
    const btnAdd5 = document.getElementById('add5Btn');
    const btnMinus1 = document.getElementById('minus1Btn');
    const btnResetProblems = document.getElementById('resetProblemsBtn');

    // ===== Rendering ===== //
    function renderWeekTexts(){
      const s = getWeekStart(new Date());
      const e = getWeekEnd(new Date());
      elWeekStartTxt.textContent = formatDateK(s);
      const endDisp = new Date(e.getTime()-1);
      elWeekEndTxt.textContent = formatDateK(endDisp);
      elWeekRange.textContent = `${formatDateK(s)} ~ ${formatDateK(endDisp)}`;
    }

    function subjectCurrentMs(id){
      const base = Math.max(0, state.subjects[id]?.accumMs || 0);
      if(state.isRunning && state.active === id){
        const now = Date.now();
        return base + Math.max(0, now - (state.startTs ?? now));
      }
      return base;
    }
    function totalCurrentMs(){
      let sum = Math.max(0, state.legacyMs || 0);
      for(const s of SUBJECTS){ sum += subjectCurrentMs(s.id); }
      return sum;
    }

    function updateOverallProgress(ms){
      const goalMs = Math.max(0, Number(state.goalHrs)||0) * 3600_000;
      if(goalMs <= 0){
        elProg.style.width = '0%';
        elProgLabel.textContent = '목표 없음';
        elGoalNote.textContent = '목표를 설정해 두면 막대가 채워져요.';
        return;
      }
      const pct = Math.min(100, Math.floor(ms / goalMs * 100));
      elProg.style.width = pct + '%';
      elProgLabel.textContent = `${pct}%`;
      const remain = Math.max(0, goalMs - ms);
      const hrs = Math.floor(remain/3600_000);
      const mins = Math.floor((remain%3600_000)/60_000);
      elGoalNote.textContent = `전체 목표까지 남은 시간: ${hrs}시간 ${fmt2(mins)}분`;
    }

    function renderSubjects(){
      elSubjects.innerHTML = '';
      for(const s of SUBJECTS){
        const row = document.createElement('div');
        row.className = 'row' + (state.active===s.id && state.isRunning ? ' now' : '');
        row.dataset.sid = s.id;
        row.innerHTML = `
          <div>
            <div class=\"name\">${s.emoji} ${s.name}</div>
            <div class=\"progress\" aria-label=\"${s.name} 목표 진행률\">
              <div class=\"bar\" id=\"bar-${s.id}\"></div>
              <div class=\"label\" id=\"label-${s.id}\">0%</div>
            </div>
          </div>
          <div class=\"time\" id=\"time-${s.id}\">00:00:00</div>
          <div class=\"right\">
            <button class=\"tiny-btn\" data-action=\"start\" data-sid=\"${s.id}\">시작</button>
            <button class=\"tiny-btn\" data-action=\"pause\" data-sid=\"${s.id}\">일시정지</button>
            <span class=\"subgoal\">
              <label class=\"muted\" for=\"goal-${s.id}\">목표</label>
              <input id=\"goal-${s.id}\" type=\"number\" min=\"0\" step=\"0.5\" inputmode=\"decimal\" />
              <button class=\"tiny-btn\" data-action=\"saveGoal\" data-sid=\"${s.id}\">저장</button>
            </span>
          </div>`;
        elSubjects.appendChild(row);
      }

      // Attach listeners
      elSubjects.addEventListener('click', (e)=>{
        const btn = e.target.closest('button');
        if(!btn) return;
        const sid = btn.dataset.sid;
        const act = btn.dataset.action;
        if(act==='start') startSubject(sid);
        if(act==='pause') pauseActiveIf(sid);
        if(act==='saveGoal') saveSubjectGoal(sid);
      });

      // Init goal inputs
      for(const s of SUBJECTS){
        const inp = document.getElementById(`goal-${s.id}`);
        if(inp){ inp.value = Number(state.subjects[s.id]?.goalHrs||0); }
      }
    }

    function renderProblems(){
      elProblems.textContent = String(Math.max(0, Number(state.problems||0)));
    }

    function renderAll(){
      renderWeekTexts();
      elWeeklyTime.textContent = formatHMS(totalCurrentMs());
      document.title = `${state.active? '▶️':''} ${formatHMS(totalCurrentMs())} · 주간 공부`;
      elGoalInput.value = state.goalHrs;
      updateOverallProgress(totalCurrentMs());
      renderProblems();

      // Now studying pill
      const cur = SUBJECTS.find(x=>x.id===state.active);
      elNow.textContent = cur && state.isRunning ? `현재: ${cur.emoji} ${cur.name}` : '현재: —';

      // Per-subject times & bars
      for(const s of SUBJECTS){
        const tEl = document.getElementById(`time-${s.id}`);
        if(tEl) tEl.textContent = formatHMS(subjectCurrentMs(s.id));
        const bar = document.getElementById(`bar-${s.id}`);
        const lab = document.getElementById(`label-${s.id}`);
        const goalH = Number(state.subjects[s.id]?.goalHrs||0);
        const ms = subjectCurrentMs(s.id);
        if(goalH>0){
          const goalMs = goalH*3600_000;
          const pct = Math.min(100, Math.floor(ms/goalMs*100));
          if(bar) bar.style.width = pct+'%';
          if(lab) lab.textContent = pct+'%';
        }else{
          if(bar) bar.style.width = '0%';
          if(lab) lab.textContent = '목표 없음';
        }
      }

      // Highlight active row
      for(const row of elSubjects.querySelectorAll('.row')){
        if(row.dataset.sid === state.active && state.isRunning){ row.classList.add('now'); }
        else { row.classList.remove('now'); }
      }
    }

    // ===== Week alignment & ticking ===== //
    function alignStateToCurrentWeek(){
      const curKey = weekKeyFromDate(new Date());
      if(state.weekKey !== curKey){
        // Reset weekly accumulations
        for(const s of Object.keys(state.subjects)){ state.subjects[s].accumMs = 0; }
        state.legacyMs = 0;
        state.problems = 0;
        state.weekKey = curKey;
        if(state.isRunning){ state.startTs = Date.now(); }
        saveState();
      }
    }

    function tick(){
      // If week rolled over while open
      const curKey = weekKeyFromDate(new Date());
      if(state.weekKey !== curKey){
        for(const s of Object.keys(state.subjects)){ state.subjects[s].accumMs = 0; }
        state.legacyMs = 0;
        state.problems = 0;
        state.weekKey = curKey;
        if(state.isRunning){ state.startTs = Date.now(); }
        saveState();
        renderWeekTexts();
        renderProblems();
      }
      elWeeklyTime.textContent = formatHMS(totalCurrentMs());
      updateOverallProgress(totalCurrentMs());
      for(const s of SUBJECTS){
        const tEl = document.getElementById(`time-${s.id}`);
        if(tEl) tEl.textContent = formatHMS(subjectCurrentMs(s.id));
      }
    }
    setInterval(tick, 500);

    // ===== Actions ===== //
    function foldRunningIntoActive(){
      if(!state.isRunning || !state.active) return;
      const delta = Math.max(0, Date.now() - (state.startTs ?? Date.now()));
      state.subjects[state.active].accumMs = Math.max(0, (state.subjects[state.active].accumMs||0) + delta);
      state.isRunning = false;
      state.startTs = null;
    }
    function startSubject(sid){
      if(!sid || !state.subjects[sid]) return;
      if(state.isRunning && state.active === sid) return; // already running same subject
      // Fold previous
      foldRunningIntoActive();
      // Align to current week
      alignStateToCurrentWeek();
      state.active = sid;
      state.isRunning = true;
      state.startTs = Date.now();
      saveState();
      renderAll();
    }
    function pauseActiveIf(sid){
      if(!state.isRunning) return;
      if(sid && state.active !== sid) return; // pressed pause on other row
      foldRunningIntoActive();
      saveState();
      renderAll();
    }
    function saveSubjectGoal(sid){
      const inp = document.getElementById(`goal-${sid}`);
      const v = Number(inp?.value);
      if(!Number.isFinite(v) || v < 0){ alert('0 이상 숫자로 입력해 주세요.'); return; }
      state.subjects[sid].goalHrs = v;
      saveState();
      renderAll();
    }

    // Problem counter actions
    function changeProblems(delta){
      const cur = Math.max(0, Number(state.problems||0));
      const next = Math.max(0, cur + delta);
      state.problems = next;
      saveState();
      renderProblems();
    }
    btnAdd1.addEventListener('click', ()=> changeProblems(1));
    btnAdd5.addEventListener('click', ()=> changeProblems(5));
    btnMinus1.addEventListener('click', ()=> changeProblems(-1));
    btnResetProblems.addEventListener('click', ()=>{
      const s = getWeekStart(new Date());
      const e = new Date(getWeekEnd(new Date()).getTime()-1);
      if(confirm(`이번 주 문제수를 0으로 초기화할까요?
기간: ${formatDateK(s)} ~ ${formatDateK(e)}`)){
        state.problems = 0;
        saveState();
        renderProblems();
      }
    });

    elPauseAll.addEventListener('click', ()=>{ pauseActiveIf(state.active); });
    elReset.addEventListener('click', () => {
      const s = getWeekStart(new Date());
      const e = new Date(getWeekEnd(new Date()).getTime()-1);
      const msg = `이번 주 누적(전체/과목별/문제수) 시간을 0으로 초기화할까요?
기간: ${formatDateK(s)} ~ ${formatDateK(e)}`;
      if(confirm(msg)){
        for(const k of Object.keys(state.subjects)){ state.subjects[k].accumMs = 0; }
        state.legacyMs = 0;
        state.problems = 0;
        if(state.isRunning){ state.startTs = Date.now(); } else { state.startTs = null; }
        saveState();
        renderAll();
      }
    });

    elSaveGoal.addEventListener('click', () => {
      const v = Number(elGoalInput.value);
      if(!Number.isFinite(v) || v < 0){ alert('0 이상 숫자로 입력해 주세요.'); return; }
      state.goalHrs = v;
      saveState();
      renderAll();
    });

    // ===== Init ===== //
    renderWeekTexts();
    renderSubjects();
    renderAll();
  </script>
</body>
</html>
