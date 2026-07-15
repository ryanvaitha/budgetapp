<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="Ledger">
<meta name="mobile-web-app-capable" content="yes">
<meta name="theme-color" content="#12161D">
<title>Ledger — Budget</title>
<style>
  :root{
    --ink:#12161D;
    --surface:#1B212B;
    --surface-2:#232B37;
    --line:#2E3744;
    --paper:#F0EBE0;
    --muted:#8D96A5;
    --gold:#E3A33D;
    --gold-dim:#3A3324;
    --teal:#5FA8A0;
    --teal-dim:#20302E;
    --coral:#E0785A;
    --coral-dim:#332320;
    --sage:#8FBF96;
    --sage-dim:#20302A;
    --danger:#D9695A;
    --radius:16px;
  }
  *{box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
  html,body{height:100%;}
  body{
    margin:0;
    background:var(--ink);
    color:var(--paper);
    font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial,sans-serif;
    -webkit-font-smoothing:antialiased;
    padding-top:env(safe-area-inset-top);
    padding-bottom:calc(72px + env(safe-area-inset-bottom));
    min-height:100%;
  }
  .serif{font-family:Georgia,"Iowan Old Style","Times New Roman",serif;}
  .num{font-variant-numeric:tabular-nums;font-feature-settings:"tnum";}
  header.top{
    padding:22px 20px 14px;
    display:flex;align-items:baseline;justify-content:space-between;
  }
  header.top h1{
    font-size:22px;margin:0;font-weight:600;letter-spacing:0.2px;
  }
  header.top .eyebrow{
    font-size:11px;letter-spacing:2px;text-transform:uppercase;color:var(--muted);margin-bottom:2px;
  }
  main{padding:0 16px;max-width:560px;margin:0 auto;}
  .screen{display:none;}
  .screen.active{display:block;animation:fade .18s ease;}
  @keyframes fade{from{opacity:0;transform:translateY(4px)}to{opacity:1;transform:none}}

  .card{
    background:var(--surface);
    border:1px solid var(--line);
    border-radius:var(--radius);
    padding:18px;
    margin-bottom:14px;
  }
  .card h2{
    font-size:13px;text-transform:uppercase;letter-spacing:1.5px;color:var(--muted);
    margin:0 0 14px 0;font-weight:600;
  }
  .hero{
    background:linear-gradient(160deg,#1E2530,#171C24);
    border:1px solid var(--line);
    border-radius:20px;
    padding:22px 20px;
    margin-bottom:16px;
  }
  .hero .amount{font-size:38px;font-weight:700;font-family:Georgia,serif;line-height:1;}
  .hero .label{color:var(--muted);font-size:13px;margin-top:6px;}
  .hero-row{display:flex;justify-content:space-between;align-items:flex-end;gap:12px;}
  .btn{
    appearance:none;border:none;border-radius:12px;
    padding:13px 18px;font-size:15px;font-weight:600;
    background:var(--gold);color:#1B140A;cursor:pointer;
    display:inline-flex;align-items:center;gap:8px;justify-content:center;
  }
  .btn.secondary{background:var(--surface-2);color:var(--paper);border:1px solid var(--line);}
  .btn.ghost{background:transparent;color:var(--gold);padding:8px 4px;}
  .btn.danger{background:var(--coral-dim);color:var(--coral);}
  .btn.small{padding:9px 14px;font-size:13px;}
  .btn.block{width:100%;}
  .row{display:flex;gap:10px;}
  .row>*{flex:1;}

  /* envelope / fill bar */
  .envelope{margin-bottom:16px;}
  .envelope:last-child{margin-bottom:0;}
  .env-top{display:flex;justify-content:space-between;align-items:baseline;margin-bottom:6px;}
  .env-name{display:flex;align-items:center;gap:8px;font-size:15px;font-weight:600;}
  .priority-badge{
    width:20px;height:20px;border-radius:50%;
    background:var(--gold-dim);color:var(--gold);
    font-size:11px;font-weight:700;
    display:inline-flex;align-items:center;justify-content:center;
    flex-shrink:0;
  }
  .env-sub{font-size:12px;color:var(--muted);margin-top:2px;}
  .env-amounts{font-size:13px;color:var(--muted);}
  .env-amounts b{color:var(--paper);font-weight:600;}
  .track{
    position:relative;height:12px;background:var(--surface-2);
    border-radius:8px;overflow:hidden;border:1px solid var(--line);
  }
  .fill{
    position:absolute;left:0;top:0;bottom:0;border-radius:8px;
    transition:width .5s cubic-bezier(.22,1,.36,1);
    background:linear-gradient(90deg, var(--fc1), var(--fc2));
  }
  .fill::after{
    content:"";position:absolute;right:0;top:0;bottom:0;width:14px;
    background:linear-gradient(90deg, transparent, rgba(255,255,255,.25));
  }
  .fill.gold{--fc1:#B87F26;--fc2:var(--gold);}
  .fill.teal{--fc1:#3E7D76;--fc2:var(--teal);}
  .fill.coral{--fc1:#B85842;--fc2:var(--coral);}
  .fill.sage{--fc1:#5F8F68;--fc2:var(--sage);}
  .locked{opacity:.45;}
  .tag{
    font-size:10px;text-transform:uppercase;letter-spacing:1px;
    padding:3px 7px;border-radius:6px;font-weight:700;
  }
  .tag.gold{background:var(--gold-dim);color:var(--gold);}
  .tag.teal{background:var(--teal-dim);color:var(--teal);}
  .tag.coral{background:var(--coral-dim);color:var(--coral);}

  .empty{
    text-align:center;color:var(--muted);padding:26px 10px;font-size:14px;
  }
  .list-item{
    display:flex;justify-content:space-between;align-items:center;
    padding:12px 0;border-bottom:1px solid var(--line);gap:10px;
  }
  .list-item:last-child{border-bottom:none;}
  .list-item .meta{font-size:12px;color:var(--muted);margin-top:2px;}
  .icon-btn{
    background:none;border:none;color:var(--muted);font-size:18px;padding:6px;cursor:pointer;
  }

  nav.tabbar{
    position:fixed;left:0;right:0;bottom:0;
    background:rgba(27,33,43,.92);backdrop-filter:blur(14px);
    border-top:1px solid var(--line);
    padding:8px 6px calc(8px + env(safe-area-inset-bottom));
    display:flex;justify-content:space-around;z-index:50;
  }
  nav.tabbar button{
    background:none;border:none;color:var(--muted);
    display:flex;flex-direction:column;align-items:center;gap:3px;
    font-size:10.5px;letter-spacing:.3px;padding:4px 10px;cursor:pointer;
  }
  nav.tabbar button svg{width:22px;height:22px;}
  nav.tabbar button.active{color:var(--gold);}
  nav.tabbar button.active svg{filter:drop-shadow(0 0 6px rgba(227,163,61,.4));}

  /* modal / sheet */
  .sheet-backdrop{
    position:fixed;inset:0;background:rgba(0,0,0,.55);
    display:none;align-items:flex-end;z-index:100;
  }
  .sheet-backdrop.open{display:flex;}
  .sheet{
    background:var(--surface);width:100%;max-height:88vh;overflow-y:auto;
    border-radius:22px 22px 0 0;padding:18px 18px calc(24px + env(safe-area-inset-bottom));
    border-top:1px solid var(--line);
    animation:slideup .22s cubic-bezier(.22,1,.36,1);
  }
  @keyframes slideup{from{transform:translateY(30px);opacity:.6}to{transform:none;opacity:1}}
  .sheet h3{margin:2px 0 16px;font-size:18px;font-family:Georgia,serif;}
  .field{margin-bottom:14px;}
  .field label{display:block;font-size:12px;color:var(--muted);margin-bottom:6px;letter-spacing:.3px;}
  .field input, .field select{
    width:100%;background:var(--surface-2);border:1px solid var(--line);
    color:var(--paper);padding:12px 12px;border-radius:10px;font-size:15px;
  }
  .field input:focus, .field select:focus{outline:2px solid var(--gold);outline-offset:0;}
  .seg{display:flex;background:var(--surface-2);border-radius:10px;padding:3px;border:1px solid var(--line);}
  .seg button{
    flex:1;background:none;border:none;color:var(--muted);padding:9px 4px;border-radius:8px;
    font-size:13px;font-weight:600;cursor:pointer;
  }
  .seg button.active{background:var(--gold);color:#1B140A;}
  .sheet-close{position:absolute;top:14px;right:16px;background:none;border:none;color:var(--muted);font-size:20px;}

  .csv-row{display:grid;grid-template-columns:1fr 1fr 90px;gap:8px;align-items:center;padding:8px 0;border-bottom:1px solid var(--line);font-size:13px;}
  .csv-row .d{color:var(--muted);}
  .csv-row select{background:var(--surface-2);border:1px solid var(--line);color:var(--paper);border-radius:8px;padding:6px;font-size:12px;}

  .fab-add{
    position:fixed;right:18px;bottom:calc(90px + env(safe-area-inset-bottom));
    width:52px;height:52px;border-radius:50%;background:var(--gold);color:#1B140A;
    display:flex;align-items:center;justify-content:center;font-size:26px;
    box-shadow:0 8px 24px rgba(227,163,61,.35);border:none;z-index:40;
  }
  .hint{font-size:12px;color:var(--muted);line-height:1.5;margin-top:10px;}
  .toast{
    position:fixed;left:50%;bottom:100px;transform:translateX(-50%);
    background:var(--surface-2);border:1px solid var(--line);color:var(--paper);
    padding:11px 18px;border-radius:12px;font-size:13px;z-index:200;
    opacity:0;pointer-events:none;transition:opacity .25s, transform .25s;
  }
  .toast.show{opacity:1;transform:translateX(-50%) translateY(-6px);}
</style>
</head>
<body>

<header class="top">
  <div>
    <div class="eyebrow" id="dateLabel">—</div>
    <h1 class="serif">Ledger</h1>
  </div>
  <button class="btn small secondary" id="logIncomeBtn">+ Log pay</button>
</header>

<main>

  <!-- DASHBOARD -->
  <section class="screen active" id="screen-dashboard">
    <div class="hero">
      <div class="hero-row">
        <div>
          <div class="label">Savings</div>
          <div class="amount num" id="savingsAmount">$0.00</div>
        </div>
        <div style="text-align:right">
          <div class="label">This cycle in</div>
          <div class="num" style="font-size:16px;font-weight:600" id="cycleIncome">$0.00</div>
        </div>
      </div>
    </div>

    <div class="card">
      <h2>Fixed expenses — filled by priority</h2>
      <div id="fixedList"><div class="empty">No fixed expenses yet. Add one with the + button.</div></div>
    </div>

    <div class="card">
      <h2>Flexible spending</h2>
      <div id="semiList"><div class="empty">No flexible funds yet.</div></div>
    </div>

    <div class="card">
      <h2>Unplanned</h2>
      <div class="envelope">
        <div class="env-top">
          <div class="env-name">Unplanned pile <span class="tag coral">variable</span></div>
        </div>
        <div class="env-sub">Whatever comes up unexpectedly — logged as it happens</div>
        <div class="track" style="margin-top:8px"><div class="fill coral" id="unplannedFill" style="width:0%"></div></div>
        <div class="env-amounts" style="margin-top:6px"><b id="unplannedSpent">$0.00</b> spent this month</div>
        <button class="btn small danger" style="margin-top:10px" id="logUnplannedBtn">+ Log unplanned expense</button>
      </div>
    </div>

    <div class="card">
      <h2>Personal investment</h2>
      <div class="envelope">
        <div class="env-top">
          <div class="env-name">Investment pot <span class="tag" style="background:var(--sage-dim);color:var(--sage)">no deadline</span></div>
        </div>
        <div class="env-sub">Whatever's left in your flexible funds at the end of each cycle rolls in here automatically</div>
        <div class="track" style="margin-top:8px"><div class="fill sage" id="investmentFill" style="width:0%"></div></div>
        <div class="env-amounts" style="margin-top:6px"><b id="investmentTotal">$0.00</b> saved so far</div>
        <div class="field" style="margin-top:12px;margin-bottom:0">
          <label>What are you investing for?</label>
          <input id="investmentGoal" placeholder="e.g. house deposit, shares, emergency fund">
        </div>
      </div>
    </div>
  </section>

  <!-- CATEGORIES -->
  <section class="screen" id="screen-categories">
    <div class="card">
      <h2>Income</h2>
      <div class="list-item">
        <div>
          <div style="font-weight:600" class="num" id="incomeSummary">$700.00 / fortnightly</div>
          <div class="meta">Used to fill envelopes in priority order when you log a pay</div>
        </div>
        <button class="btn small secondary" id="editIncomeBtn">Edit</button>
      </div>
    </div>

    <div class="card">
      <h2>All categories</h2>
      <div id="catList"><div class="empty">Nothing added yet</div></div>
    </div>
    <div class="hint">Fixed expenses are filled first, in priority order, whenever you log a pay. Monthly fixed items only start filling 7 days before their due date. Flexible funds are a spending allowance that refills each cycle. Whatever's left over becomes savings.</div>
  </section>

  <!-- STATEMENT -->
  <section class="screen" id="screen-statement">
    <div class="card">
      <h2>Upload a bank statement</h2>
      <p class="hint" style="margin-top:0">Export a CSV from your bank app, upload it here, then tag each transaction to a flexible fund or as unplanned. Nothing is uploaded anywhere — it stays on this device.</p>
      <input type="file" id="csvInput" accept=".csv,text/csv" style="display:none">
      <button class="btn block" id="csvPickBtn">Choose CSV file</button>
    </div>
    <div class="card" id="csvPreviewCard" style="display:none">
      <h2>Tag transactions</h2>
      <div id="transferNotice" class="hint" style="margin-top:0;display:none"></div>
      <div id="csvRows"></div>
      <button class="btn block" style="margin-top:14px" id="csvApplyBtn">Apply tagged spending</button>
    </div>
  </section>

  <!-- SETTINGS -->
  <section class="screen" id="screen-settings">
    <div class="card">
      <h2>About this budget</h2>
      <p class="hint" style="margin-top:0">All figures in AUD. Everything is stored only on this device (browser local storage) — nothing is sent anywhere, and there's no bank connection.</p>
    </div>
    <div class="card">
      <h2>Data</h2>
      <button class="btn secondary block" id="exportBtn">Export backup (.json)</button>
      <div style="height:10px"></div>
      <button class="btn danger block" id="resetBtn">Reset all data</button>
    </div>
  </section>

</main>

<button class="fab-add" id="fabAdd" title="Add category">+</button>

<nav class="tabbar">
  <button class="tab active" data-screen="dashboard">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8"><path d="M3 11.5 12 4l9 7.5"/><path d="M5 10v9a1 1 0 0 0 1 1h4v-6h4v6h4a1 1 0 0 0 1-1v-9"/></svg>
    Dashboard
  </button>
  <button class="tab" data-screen="categories">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8"><rect x="3" y="4" width="18" height="16" rx="2"/><path d="M3 10h18M8 4v16"/></svg>
    Categories
  </button>
  <button class="tab" data-screen="statement">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8"><path d="M14 3H7a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V8z"/><path d="M14 3v5h5M9 13h6M9 17h6"/></svg>
    Statement
  </button>
  <button class="tab" data-screen="settings">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.7 1.7 0 0 0 .34 1.87l.06.06a2 2 0 1 1-2.83 2.83l-.06-.06a1.7 1.7 0 0 0-1.87-.34 1.7 1.7 0 0 0-1.04 1.56V21a2 2 0 1 1-4 0v-.09A1.7 1.7 0 0 0 8.96 19a1.7 1.7 0 0 0-1.87.34l-.06.06a2 2 0 1 1-2.83-2.83l.06-.06a1.7 1.7 0 0 0 .34-1.87 1.7 1.7 0 0 0-1.56-1.04H3a2 2 0 1 1 0-4h.09A1.7 1.7 0 0 0 4.6 8.6a1.7 1.7 0 0 0-.34-1.87l-.06-.06a2 2 0 1 1 2.83-2.83l.06.06A1.7 1.7 0 0 0 9 4.6a1.7 1.7 0 0 0 1.04-1.56V3a2 2 0 1 1 4 0v.09A1.7 1.7 0 0 0 15 4.6a1.7 1.7 0 0 0 1.87-.34l.06-.06a2 2 0 1 1 2.83 2.83l-.06.06a1.7 1.7 0 0 0-.34 1.87V9a1.7 1.7 0 0 0 1.56 1.04H21a2 2 0 1 1 0 4h-.09A1.7 1.7 0 0 0 19.4 15z"/></svg>
    Settings
  </button>
</nav>

<div class="sheet-backdrop" id="sheetBackdrop">
  <div class="sheet" id="sheetContent"></div>
</div>

<div class="toast" id="toast"></div>

<script>
/* ---------- storage ---------- */
const STORE_KEY = 'ledger_budget_v1';
function loadState(){
  try{
    const raw = localStorage.getItem(STORE_KEY);
    if(raw) return JSON.parse(raw);
  }catch(e){}
  return {
    income:{amount:0, frequency:'weekly'},
    categories:[],
    savings:0,
    unplanned:{spent:0, monthKey: monthKey(new Date())},
    investment:{total:0, goal:''},
    payLog:[]
  };
}
function saveState(){ localStorage.setItem(STORE_KEY, JSON.stringify(state)); }
let state = loadState();
if(!state.investment) state.investment = {total:0, goal:''};

/* ---------- helpers ---------- */
function fmt(n){
  const v = isFinite(n) ? n : 0;
  return v.toLocaleString('en-AU', {style:'currency', currency:'AUD'});
}
function monthKey(d){ return d.getFullYear()+'-'+(d.getMonth()+1); }
function weekKey(d){
  const t = new Date(d); t.setHours(0,0,0,0);
  t.setDate(t.getDate() - t.getDay()); // sunday-anchored week start
  return t.toISOString().slice(0,10);
}
function fortnightKey(d){
  // anchor fortnights to a fixed epoch monday
  const epoch = new Date('2024-01-01');
  const days = Math.floor((d-epoch)/86400000);
  const fn = Math.floor(days/14);
  return 'fn'+fn;
}
function periodKeyFor(freq, d){
  if(freq==='weekly') return weekKey(d);
  if(freq==='fortnightly') return fortnightKey(d);
  return monthKey(d);
}
function uid(){ return Math.random().toString(36).slice(2,9); }
function toast(msg){
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.add('show');
  clearTimeout(toast._t);
  toast._t = setTimeout(()=>t.classList.remove('show'), 1800);
}
function nextDueDate(dueDay){
  const now = new Date();
  let d = new Date(now.getFullYear(), now.getMonth(), dueDay);
  if(d < new Date(now.getFullYear(), now.getMonth(), now.getDate())) {
    d = new Date(now.getFullYear(), now.getMonth()+1, dueDay);
  }
  return d;
}
function nextQuarterlyDue(anchorISO){
  const now = new Date(); now.setHours(0,0,0,0);
  let d = new Date(anchorISO+'T00:00:00');
  if(isNaN(d)) d = new Date();
  while(d < now){ d.setMonth(d.getMonth()+3); }
  return d;
}
function nextDueFor(c){
  if(c.frequency==='monthly') return nextDueDate(c.dueDay);
  if(c.frequency==='quarterly') return nextQuarterlyDue(c.dueDate);
  return null;
}
function isLongCycle(freq){ return freq==='monthly' || freq==='quarterly'; }
function daysUntil(date){
  const now = new Date(); now.setHours(0,0,0,0);
  const d = new Date(date); d.setHours(0,0,0,0);
  return Math.round((d-now)/86400000);
}

/* ---------- period rollover (reset envelopes when their cycle turns over) ---------- */
function rollover(){
  const now = new Date();
  state.categories.forEach(c=>{
    if(c.type==='fixed'){
      const pk = isLongCycle(c.frequency) ? nextDueFor(c).toISOString().slice(0,10) : periodKeyFor(c.frequency, now);
      if(c.periodKey !== pk){
        // new cycle: whatever wasn't spent/funded just resets; funded starts fresh
        c.periodKey = pk;
        c.funded = 0;
        c.paidOut = false;
      }
    } else if(c.type==='semi'){
      const pk = periodKeyFor(c.frequency, now);
      if(c.periodKey !== pk){
        // leftover unspent flexible money rolls into the investment pot before resetting
        if(c.periodKey !== null){
          const leftover = Math.max(0, c.amount - (c.spent||0));
          if(leftover>0) state.investment.total = (state.investment.total||0) + leftover;
        }
        c.periodKey = pk;
        c.spent = 0;
      }
    }
  });
  const mk = monthKey(now);
  if(state.unplanned.monthKey !== mk){
    state.unplanned.monthKey = mk;
    state.unplanned.spent = 0;
  }
  saveState();
}

/* ---------- allocation engine ---------- */
function logIncome(amount){
  rollover();
  let pool = amount;
  const fixed = state.categories.filter(c=>c.type==='fixed').sort((a,b)=>a.priority-b.priority);
  fixed.forEach(c=>{
    let eligible = true;
    if(isLongCycle(c.frequency)){
      const due = nextDueFor(c);
      const dleft = daysUntil(due);
      eligible = dleft <= 7;
    }
    if(!eligible) return;
    const need = Math.max(0, c.amount - (c.funded||0));
    const give = Math.min(need, pool);
    c.funded = (c.funded||0) + give;
    pool -= give;
  });
  // reserve this cycle's share of flexible funds so savings isn't overcounted
  const semis = state.categories.filter(c=>c.type==='semi');
  semis.forEach(c=>{
    const share = perCycleShare(c.amount, c.frequency, state.income.frequency);
    pool -= share; // can go negative on the fund side conceptually; we just don't let savings claim it
  });
  state.savings += pool;
  state.payLog.unshift({date:new Date().toISOString(), amount});
  saveState();
}
function perCycleShare(amount, fundFreq, incomeFreq){
  const toWeekly = f => f==='weekly'?1:f==='fortnightly'?0.5:12/52;
  const perWeek = amount * toWeekly(fundFreq);
  const incPerWeek = incomeFreq==='weekly'?1:incomeFreq==='fortnightly'?0.5:12/52;
  return perWeek / incPerWeek;
}

/* ---------- rendering ---------- */
function render(){
  rollover();
  document.getElementById('dateLabel').textContent = new Date().toLocaleDateString('en-AU',{weekday:'long', day:'numeric', month:'long'});
  document.getElementById('savingsAmount').textContent = fmt(state.savings);
  document.getElementById('incomeSummary').textContent = fmt(state.income.amount)+' / '+state.income.frequency;

  const lastPay = state.payLog[0];
  document.getElementById('cycleIncome').textContent = lastPay ? fmt(lastPay.amount) : '—';

  renderFixed();
  renderSemi();
  renderUnplanned();
  renderInvestment();
  renderCatList();
}

function renderFixed(){
  const el = document.getElementById('fixedList');
  const fixed = state.categories.filter(c=>c.type==='fixed').sort((a,b)=>a.priority-b.priority);
  if(!fixed.length){ el.innerHTML='<div class="empty">No fixed expenses yet. Add one with the + button.</div>'; return; }
  el.innerHTML = fixed.map((c,i)=>{
    const pct = Math.min(100, Math.round(((c.funded||0)/c.amount)*100));
    let sub = c.frequency;
    let locked = false;
    if(isLongCycle(c.frequency)){
      const due = nextDueFor(c);
      const dleft = daysUntil(due);
      locked = dleft > 7;
      sub = locked ? `opens in ${dleft-7} day${dleft-7===1?'':'s'} · due ${due.toLocaleDateString('en-AU',{day:'numeric',month:'short'})}`
                   : `due ${due.toLocaleDateString('en-AU',{day:'numeric',month:'short'})}`;
    }
    return `
    <div class="envelope ${locked?'locked':''}">
      <div class="env-top">
        <div class="env-name"><span class="priority-badge">${i+1}</span> ${escapeHtml(c.name)}</div>
        <div class="env-amounts"><b class="num">${fmt(c.funded||0)}</b> / ${fmt(c.amount)}</div>
      </div>
      <div class="env-sub">${sub}</div>
      <div class="track" style="margin-top:8px"><div class="fill gold" style="width:${pct}%"></div></div>
    </div>`;
  }).join('');
}

function renderSemi(){
  const el = document.getElementById('semiList');
  const semis = state.categories.filter(c=>c.type==='semi');
  if(!semis.length){ el.innerHTML='<div class="empty">No flexible funds yet.</div>'; return; }
  el.innerHTML = semis.map(c=>{
    const pct = Math.min(100, Math.round(((c.spent||0)/c.amount)*100));
    const remaining = Math.max(0, c.amount-(c.spent||0));
    return `
    <div class="envelope">
      <div class="env-top">
        <div class="env-name">${escapeHtml(c.name)} <span class="tag teal">${c.frequency}</span></div>
        <div class="env-amounts"><b class="num">${fmt(c.spent||0)}</b> / ${fmt(c.amount)}</div>
      </div>
      <div class="env-sub">${fmt(remaining)} left this ${c.frequency==='weekly'?'week':c.frequency==='fortnightly'?'fortnight':'month'}</div>
      <div class="track" style="margin-top:8px"><div class="fill teal" style="width:${pct}%"></div></div>
      <button class="btn small secondary" style="margin-top:10px" onclick="openLogSpend('${c.id}')">+ Log a spend</button>
    </div>`;
  }).join('');
}

function renderUnplanned(){
  const spent = state.unplanned.spent||0;
  const scale = Math.max(spent*1.25, 150);
  const pct = Math.min(100, Math.round((spent/scale)*100));
  document.getElementById('unplannedFill').style.width = pct+'%';
  document.getElementById('unplannedSpent').textContent = fmt(spent);
}

function renderInvestment(){
  const total = state.investment.total||0;
  const scale = Math.max(total*1.25, 150);
  const pct = Math.min(100, Math.round((total/scale)*100));
  document.getElementById('investmentFill').style.width = pct+'%';
  document.getElementById('investmentTotal').textContent = fmt(total);
  const goalInput = document.getElementById('investmentGoal');
  if(goalInput && document.activeElement!==goalInput) goalInput.value = state.investment.goal||'';
}

function renderCatList(){
  const el = document.getElementById('catList');
  if(!state.categories.length){ el.innerHTML='<div class="empty">Nothing added yet</div>'; return; }
  const order = {fixed:0, semi:1};
  const sorted = [...state.categories].sort((a,b)=>(order[a.type]-order[b.type])||(a.priority-b.priority));
  el.innerHTML = sorted.map(c=>{
    const typeLabel = c.type==='fixed' ? `Fixed · priority ${c.priority}` : `Flexible`;
    let dueInfo = '';
    if(c.type==='fixed' && c.frequency==='monthly') dueInfo = ' · due '+c.dueDay+(ordinal(c.dueDay));
    if(c.type==='fixed' && c.frequency==='quarterly') dueInfo = ' · next due '+nextDueFor(c).toLocaleDateString('en-AU',{day:'numeric',month:'short'});
    return `
    <div class="list-item">
      <div>
        <div style="font-weight:600">${escapeHtml(c.name)}</div>
        <div class="meta">${typeLabel} · ${fmt(c.amount)} ${c.frequency}${dueInfo}</div>
      </div>
      <div style="display:flex;gap:4px">
        <button class="icon-btn" onclick="openCategorySheet('${c.id}')">✎</button>
        <button class="icon-btn" onclick="deleteCategory('${c.id}')">✕</button>
      </div>
    </div>`;
  }).join('');
}
function ordinal(n){ const s=["th","st","nd","rd"],v=n%100; return s[(v-20)%10]||s[v]||s[0]; }
function escapeHtml(s){ return String(s).replace(/[&<>"]/g, c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;'}[c])); }

/* ---------- tabs ---------- */
document.querySelectorAll('nav.tabbar .tab').forEach(btn=>{
  btn.addEventListener('click', ()=>{
    document.querySelectorAll('nav.tabbar .tab').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
    document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
    document.getElementById('screen-'+btn.dataset.screen).classList.add('active');
    document.getElementById('fabAdd').style.display = btn.dataset.screen==='categories' ? 'flex' : (btn.dataset.screen==='dashboard'?'none':'none');
  });
});

/* ---------- sheet system ---------- */
const backdrop = document.getElementById('sheetBackdrop');
const sheetContent = document.getElementById('sheetContent');
function openSheet(html){
  sheetContent.innerHTML = `<button class="sheet-close" onclick="closeSheet()">✕</button>` + html;
  backdrop.classList.add('open');
}
function closeSheet(){ backdrop.classList.remove('open'); }
backdrop.addEventListener('click', e=>{ if(e.target===backdrop) closeSheet(); });

/* ---------- add / edit category ---------- */
document.getElementById('fabAdd').addEventListener('click', ()=>openCategorySheet(null));
function openCategorySheet(id){
  const editing = id ? state.categories.find(c=>c.id===id) : null;
  const type = editing ? editing.type : 'fixed';
  openSheet(`
    <h3>${editing? 'Edit' : 'Add'} category</h3>
    <div class="seg" id="typeSeg">
      <button type="button" data-v="fixed" class="${type==='fixed'?'active':''}">Fixed</button>
      <button type="button" data-v="semi" class="${type==='semi'?'active':''}">Flexible</button>
    </div>
    <div style="height:14px"></div>
    <div class="field"><label>Name</label><input id="f_name" value="${editing?escapeHtml(editing.name):''}" placeholder="e.g. Rent"></div>
    <div class="field"><label>Amount (AUD)</label><input id="f_amount" type="number" inputmode="decimal" value="${editing?editing.amount:''}" placeholder="500"></div>
    <div class="field"><label>Frequency</label>
      <select id="f_freq">
        <option value="weekly" ${editing&&editing.frequency==='weekly'?'selected':''}>Weekly</option>
        <option value="fortnightly" ${editing&&editing.frequency==='fortnightly'?'selected':''}>Fortnightly</option>
        <option value="monthly" ${editing&&editing.frequency==='monthly'?'selected':''}>Monthly</option>
        <option value="quarterly" ${editing&&editing.frequency==='quarterly'?'selected':''}>Every 3 months</option>
      </select>
    </div>
    <div class="field" id="f_priority_wrap"><label>Priority (1 = filled first)</label><input id="f_priority" type="number" min="1" value="${editing&&editing.priority?editing.priority:(state.categories.filter(c=>c.type==='fixed').length+1)}"></div>
    <div class="field" id="f_due_wrap" style="display:none"><label>Due day of month</label><input id="f_due" type="number" min="1" max="31" value="${editing&&editing.dueDay?editing.dueDay:1}"></div>
    <div class="field" id="f_due_date_wrap" style="display:none"><label>Next due date</label><input id="f_due_date" type="date" value="${editing&&editing.dueDate?editing.dueDate:''}"></div>
    <button class="btn block" id="saveCatBtn">${editing?'Save changes':'Add category'}</button>
  `);
  const seg = document.getElementById('typeSeg');
  function refreshVisibility(){
    const t = seg.querySelector('.active').dataset.v;
    document.getElementById('f_priority_wrap').style.display = t==='fixed' ? '' : 'none';
    const freq = document.getElementById('f_freq').value;
    document.getElementById('f_due_wrap').style.display = (t==='fixed' && freq==='monthly') ? '' : 'none';
    document.getElementById('f_due_date_wrap').style.display = (t==='fixed' && freq==='quarterly') ? '' : 'none';
  }
  seg.querySelectorAll('button').forEach(b=>b.addEventListener('click', ()=>{
    seg.querySelectorAll('button').forEach(x=>x.classList.remove('active'));
    b.classList.add('active'); refreshVisibility();
  }));
  document.getElementById('f_freq').addEventListener('change', refreshVisibility);
  refreshVisibility();

  document.getElementById('saveCatBtn').addEventListener('click', ()=>{
    const t = seg.querySelector('.active').dataset.v;
    const name = document.getElementById('f_name').value.trim();
    const amount = parseFloat(document.getElementById('f_amount').value);
    const frequency = document.getElementById('f_freq').value;
    if(!name || !amount || amount<=0){ toast('Enter a name and amount'); return; }
    if(editing){
      editing.name=name; editing.amount=amount; editing.frequency=frequency; editing.type=t;
      if(t==='fixed'){
        editing.priority = parseInt(document.getElementById('f_priority').value)||1;
        if(frequency==='monthly') editing.dueDay = parseInt(document.getElementById('f_due').value)||1;
        if(frequency==='quarterly') editing.dueDate = document.getElementById('f_due_date').value || editing.dueDate;
      }
    } else {
      const cat = { id:uid(), name, amount, frequency, type:t, funded:0, spent:0, periodKey:null };
      if(t==='fixed'){
        cat.priority = parseInt(document.getElementById('f_priority').value)||1;
        if(frequency==='monthly') cat.dueDay = parseInt(document.getElementById('f_due').value)||1;
        if(frequency==='quarterly') cat.dueDate = document.getElementById('f_due_date').value || new Date().toISOString().slice(0,10);
      }
      state.categories.push(cat);
    }
    saveState(); closeSheet(); render();
    toast('Saved');
  });
}
function deleteCategory(id){
  if(!confirm('Delete this category?')) return;
  state.categories = state.categories.filter(c=>c.id!==id);
  saveState(); render();
}

/* ---------- log income ---------- */
document.getElementById('logIncomeBtn').addEventListener('click', ()=>{
  const lastAmt = state.payLog[0] ? state.payLog[0].amount : (state.income.amount||'');
  openSheet(`
    <h3>Log this week's pay</h3>
    <div class="field"><label>Amount received (AUD)</label><input id="f_pay" type="number" inputmode="decimal" value="${lastAmt}" placeholder="Different every week? Type it in"></div>
    <div class="hint">Since your pay varies, just enter whatever landed this Tuesday. This fills your fixed expenses in priority order, reserves this cycle's flexible funds, and adds whatever's left to savings.</div>
    <div style="height:6px"></div>
    <button class="btn block" id="doLogIncome">Log pay & allocate</button>
  `);
  document.getElementById('doLogIncome').addEventListener('click', ()=>{
    const amt = parseFloat(document.getElementById('f_pay').value);
    if(!amt || amt<=0){ toast('Enter an amount'); return; }
    logIncome(amt);
    closeSheet(); render();
    toast('Allocated '+fmt(amt));
  });
});

document.getElementById('editIncomeBtn').addEventListener('click', ()=>{
  openSheet(`
    <h3>Edit income</h3>
    <div class="field"><label>Typical/estimate pay amount (AUD)</label><input id="f_inc_amt" type="number" value="${state.income.amount}"></div>
    <div class="field"><label>Frequency</label>
      <select id="f_inc_freq">
        <option value="weekly" ${state.income.frequency==='weekly'?'selected':''}>Weekly</option>
        <option value="fortnightly" ${state.income.frequency==='fortnightly'?'selected':''}>Fortnightly</option>
        <option value="monthly" ${state.income.frequency==='monthly'?'selected':''}>Monthly</option>
      </select>
    </div>
    <div class="hint" style="margin-top:0">This is just used to calculate your fair share of flexible funds each pay — you'll always type in the real amount when you log a pay, since it varies.</div>
    <button class="btn block" id="saveIncome">Save</button>
  `);
  document.getElementById('saveIncome').addEventListener('click', ()=>{
    state.income.amount = parseFloat(document.getElementById('f_inc_amt').value)||state.income.amount;
    state.income.frequency = document.getElementById('f_inc_freq').value;
    saveState(); closeSheet(); render(); toast('Income updated');
  });
});

/* ---------- investment goal ---------- */
document.getElementById('investmentGoal').addEventListener('input', e=>{
  state.investment.goal = e.target.value;
  saveState();
});

/* ---------- log spend on flexible fund ---------- */
function openLogSpend(id){
  const c = state.categories.find(x=>x.id===id);
  openSheet(`
    <h3>Log a spend — ${escapeHtml(c.name)}</h3>
    <div class="field"><label>Amount (AUD)</label><input id="f_spend" type="number" inputmode="decimal" placeholder="0.00"></div>
    <button class="btn block" id="doSpend">Log spend</button>
  `);
  document.getElementById('doSpend').addEventListener('click', ()=>{
    const amt = parseFloat(document.getElementById('f_spend').value);
    if(!amt || amt<=0){ toast('Enter an amount'); return; }
    c.spent = (c.spent||0)+amt;
    saveState(); closeSheet(); render(); toast('Logged '+fmt(amt));
  });
}

/* ---------- unplanned ---------- */
document.getElementById('logUnplannedBtn').addEventListener('click', ()=>{
  openSheet(`
    <h3>Log unplanned expense</h3>
    <div class="field"><label>What was it?</label><input id="f_unp_name" placeholder="e.g. car repair"></div>
    <div class="field"><label>Amount (AUD)</label><input id="f_unp_amt" type="number" inputmode="decimal"></div>
    <div class="hint">This comes straight out of savings.</div>
    <button class="btn block" id="doUnplanned">Log expense</button>
  `);
  document.getElementById('doUnplanned').addEventListener('click', ()=>{
    const amt = parseFloat(document.getElementById('f_unp_amt').value);
    if(!amt || amt<=0){ toast('Enter an amount'); return; }
    state.unplanned.spent = (state.unplanned.spent||0)+amt;
    state.savings -= amt;
    saveState(); closeSheet(); render(); toast('Logged '+fmt(amt));
  });
});

/* ---------- CSV import ---------- */
document.getElementById('csvPickBtn').addEventListener('click', ()=>document.getElementById('csvInput').click());
let parsedRows = [];
document.getElementById('csvInput').addEventListener('change', e=>{
  const file = e.target.files[0];
  if(!file) return;
  const reader = new FileReader();
  reader.onload = ev => {
    const text = ev.target.result;
    parsedRows = parseCSV(text);
    renderCsvRows();
  };
  reader.readAsText(file);
});
function looksLikeDate(s){
  return /^\d{1,4}[\/\-]\d{1,2}[\/\-]\d{1,4}/.test((s||'').trim());
}
function looksLikeAmount(s){
  return /^[+\-]?\d[\d,]*\.?\d*$/.test((s||'').trim().replace(/^"|"$/g,''));
}
// matches transfers between the user's own numbered CommBank accounts (e.g. "Transfer to xx1234", "Transfer from xx3770")
// deliberately does NOT match PayID/named transfers to other people, since those are real spending
function isLikelyOwnTransfer(desc){
  const d = (desc||'').toLowerCase();
  return /transfer (to|from)\s+xx\d{3,4}\b/.test(d) || /(^|\s)internal transfer\b/.test(d);
}
function parseCSV(text){
  const lines = text.split(/\r?\n/).filter(l=>l.trim().length);
  if(!lines.length) return [];
  const firstCols = splitCsvLine(lines[0]);

  let dateIdx, descIdx, amtIdx, startRow;

  // Headerless CommBank-style export: date, amount, description, balance
  if(looksLikeDate(firstCols[0]) && looksLikeAmount(firstCols[1])){
    dateIdx = 0; amtIdx = 1; descIdx = 2; startRow = 0;
  } else {
    // Assume first row is a header, find columns by name
    const header = firstCols.map(h=>h.trim().toLowerCase());
    dateIdx = header.findIndex(h=>h.includes('date'));
    descIdx = header.findIndex(h=>h.includes('desc')||h.includes('narrat')||h.includes('detail'));
    amtIdx = header.findIndex(h=>h.includes('amount')||h.includes('debit')||h.includes('value'));
    startRow = 1;
    if(dateIdx===-1||amtIdx===-1){
      // couldn't confidently detect columns, fall back to CommBank order anyway
      dateIdx=0; amtIdx=1; descIdx=2; startRow=0;
    }
  }

  const rows = [];
  for(let i=startRow;i<lines.length;i++){
    const cols = splitCsvLine(lines[i]);
    if(cols.length<2) continue;
    const rawAmt = parseFloat((cols[amtIdx]||'').replace(/[^0-9.\-]/g,''));
    if(isNaN(rawAmt) || rawAmt>=0) continue; // only expenses (negative amounts)
    const desc = (cols[descIdx]||'').trim();
    rows.push({
      date: (cols[dateIdx]||'').trim(),
      desc: desc,
      amount: Math.abs(rawAmt),
      tag: 'skip',
      isTransfer: isLikelyOwnTransfer(desc)
    });
  }
  return rows;
}
function splitCsvLine(line){
  const out=[]; let cur=''; let inQ=false;
  for(let i=0;i<line.length;i++){
    const ch=line[i];
    if(ch==='"'){ inQ=!inQ; continue; }
    if(ch===',' && !inQ){ out.push(cur); cur=''; continue; }
    cur+=ch;
  }
  out.push(cur);
  return out;
}
function renderCsvRows(){
  const card = document.getElementById('csvPreviewCard');
  const wrap = document.getElementById('csvRows');
  if(!parsedRows.length){ card.style.display='none'; toast('No expense rows found in that file'); return; }
  card.style.display='';
  const semis = state.categories.filter(c=>c.type==='semi');
  const opts = `<option value="skip">Don't tag</option>` +
    semis.map(c=>`<option value="semi:${c.id}">${escapeHtml(c.name)}</option>`).join('') +
    `<option value="unplanned">Unplanned</option>`;
  wrap.innerHTML = parsedRows.map((r,i)=>`
    <div class="csv-row">
      <div>
        <div>${escapeHtml(r.desc||'—')}</div>
        <div class="d">${escapeHtml(r.date)}</div>
      </div>
      <div class="num">${fmt(r.amount)}</div>
      <select onchange="parsedRows[${i}].tag=this.value">${opts}</select>
    </div>
  `).join('');
}
document.getElementById('csvApplyBtn').addEventListener('click', ()=>{
  let count=0;
  parsedRows.forEach(r=>{
    if(r.tag==='skip') return;
    if(r.tag==='unplanned'){
      state.unplanned.spent = (state.unplanned.spent||0)+r.amount;
      state.savings -= r.amount;
      count++;
    } else if(r.tag.startsWith('semi:')){
      const id = r.tag.split(':')[1];
      const c = state.categories.find(x=>x.id===id);
      if(c){ c.spent=(c.spent||0)+r.amount; count++; }
    }
  });
  saveState(); render();
  parsedRows = []; document.getElementById('csvPreviewCard').style.display='none';
  document.getElementById('csvInput').value='';
  toast(`Applied ${count} transaction${count===1?'':'s'}`);
});

/* ---------- settings ---------- */
document.getElementById('exportBtn').addEventListener('click', ()=>{
  const blob = new Blob([JSON.stringify(state,null,2)], {type:'application/json'});
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'ledger-backup-'+new Date().toISOString().slice(0,10)+'.json';
  a.click();
});
document.getElementById('resetBtn').addEventListener('click', ()=>{
  if(!confirm('This deletes everything on this device. Continue?')) return;
  localStorage.removeItem(STORE_KEY);
  state = loadState();
  render();
  toast('Reset');
});

render();
</script>
</body>
</html>
