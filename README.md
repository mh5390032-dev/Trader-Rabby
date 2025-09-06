# Trader-Rabby
<!doctype html>
<html lang="bn">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>TRADER RABBY — Bot Board (YQT style)</title>
<meta name="description" content="TRADER RABBY demo signal board — looks like YQT bot UI. No live execution.">

<!-- Chart.js CDN for small confidence chart -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
:root{
  --bg:#031026; --card:#071428; --muted:#9fb0c6;
  --accent:#7c3aed; --accent2:#a37bff; --good:#10b981; --bad:#ef4444;
  --glass: rgba(255,255,255,0.03);
}
*{box-sizing:border-box}
html,body{height:100%;margin:0;font-family:Inter,system-ui,'Noto Sans Bengali',sans-serif;background:
  radial-gradient(900px 300px at 10% 8%, rgba(124,58,237,0.05), transparent),
  linear-gradient(180deg,var(--bg), #021024); color:#eaf2ff;-webkit-font-smoothing:antialiased;}
.container{max-width:420px;margin:18px auto;padding:12px}
.panel{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:14px;padding:14px;box-shadow:0 18px 60px rgba(2,6,23,0.6);border:1px solid rgba(255,255,255,0.02)}
.header{display:flex;justify-content:space-between;align-items:center;gap:12px;flex-wrap:wrap}
.brand{display:flex;gap:12px;align-items:center}
.logo{width:56px;height:56px;border-radius:12px;background:linear-gradient(135deg,var(--accent),var(--accent2));display:flex;align-items:center;justify-content:center;font-weight:900;font-size:20px;box-shadow:0 8px 30px rgba(124,58,237,0.12)}
.title{font-size:16px;font-weight:800}
.subtitle{color:var(--muted);font-size:12px;margin-top:4px}
.controls{display:flex;gap:8px;flex-wrap:wrap;margin-top:12px;align-items:center}
.select,button,input{padding:10px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);background:transparent;color:inherit;font-size:14px}
.tf-grid{display:flex;gap:8px;flex-wrap:wrap}
.tf{padding:8px 10px;border-radius:8px;background:var(--glass);cursor:pointer;font-weight:700;font-size:13px}
.tf.active{background:linear-gradient(90deg,var(--accent),var(--accent2));color:white}
.generate{background:linear-gradient(90deg,var(--accent),var(--accent2));border:none;color:white;padding:12px 14px;border-radius:12px;font-weight:900;cursor:pointer;box-shadow:0 12px 40px rgba(124,58,237,0.12)}
.layout{display:grid;grid-template-columns:1fr;gap:12px;margin-top:12px}
.sig-display{border-radius:12px;padding:14px;margin-bottom:12px;text-align:center;display:none}
.sig-call{background:linear-gradient(90deg,#10b981,#059669);color:white}
.sig-put{background:linear-gradient(90deg,#fb7185,#ef4444);color:white}
.sig-title{font-weight:900;font-size:20px}
.sig-sub{color:var(--muted);margin-top:6px;font-size:13px}
.row{display:flex;gap:8px;align-items:center;flex-wrap:wrap;margin-top:8px}
.pill{background:var(--glass);padding:8px 10px;border-radius:999px;font-weight:800}
.small{font-size:13px;color:var(--muted)}
.chart-wrap{height:180px;margin-top:10px}
.history{max-height:220px;overflow:auto;padding-right:6px;margin-top:8px}
.sig-item{display:flex;justify-content:space-between;align-items:center;padding:10px;border-radius:10px;background:rgba(255,255,255,0.01);border:1px solid rgba(255,255,255,0.02);margin-bottom:8px}
.sig-left{max-width:65%}
.badge{padding:6px 10px;border-radius:8px;font-weight:800}
.badge.call{background:linear-gradient(90deg,#10b981,#059669);color:white}
.badge.put{background:linear-gradient(90deg,#fb7185,#ef4444);color:white}
.actions{display:flex;gap:8px}
.btn{padding:8px 10px;border-radius:8px;border:none;cursor:pointer}
.btn-ghost{background:transparent;border:1px solid rgba(255,255,255,0.03);color:var(--muted)}
.btn-copy{background:linear-gradient(90deg,#06b6d4,#0891b2);color:white}
.overlay{position:fixed;inset:0;background:rgba(2,6,23,0.6);display:flex;align-items:center;justify-content:center;z-index:999;display:none}
.loader-box{background:#071428;padding:18px;border-radius:12px;text-align:center;border:1px solid rgba(255,255,255,0.02)}
.countdown{font-weight:900;font-size:28px;margin-top:8px}
.modal{background:#071428;padding:14px;border-radius:12px;border:1px solid rgba(255,255,255,0.02);max-width:360px}
.footer{margin-top:12px;color:var(--muted);font-size:12px;text-align:center}
.note{color:#ffd6d6;font-size:13px;margin-top:8px}
</style>
</head>
<body>
<div class="container">
  <div class="panel">
    <div class="header">
      <div class="brand">
        <div class="logo">TR</div>
        <div>
          <div class="title">TRADER RABBY • BOT BOARD</div>
          <div class="subtitle">YQT-style demo • সিগন্যাল দেখায় — কোনো লাইভ এক্সিকিউশন নেই</div>
        </div>
      </div>
      <div class="row">
        <div class="pill">By: আরিফ ভাই</div>
        <div class="pill" id="status">Ready</div>
      </div>
    </div>

    <!-- controls -->
    <div class="controls">
      <select id="pair" class="select" aria-label="pair">
        <option value="EURUSD">EUR / USD</option>
        <option value="GBPUSD">GBP / USD</option>
        <option value="USDJPY">USD / JPY</option>
        <option value="BTCUSDT">BTC / USDT</option>
        <option value="ETHUSDT">ETH / USDT</option>
      </select>

      <div class="tf-grid" id="tfGrid" role="tablist">
        <div class="tf active">5s</div>
        <div class="tf">10s</div>
        <div class="tf">15s</div>
        <div class="tf">30s</div>
        <div class="tf">1m</div>
      </div>

      <button id="generate" class="generate" aria-label="generate">⚡ GENERATE</button>

      <div style="flex:1"></div>

      <div class="row">
        <select id="theme" class="select" aria-label="theme">
          <option value="purple">Theme: Purple</option>
          <option value="green">Theme: Green</option>
          <option value="blue">Theme: Blue</option>
        </select>
        <label class="small"><input type="checkbox" id="soundToggle" /> Sound</label>
      </div>
    </div>

    <div class="layout">
      <div class="main-panel">
        <div id="signalBox" class="sig-display">
          <div id="sigType" class="sig-title"></div>
          <div id="sigInfo" class="sig-sub"></div>
        </div>

        <div class="row" style="margin-top:6px">
          <div class="pill">Signals: <strong id="count">0</strong></div>
          <div class="pill">Avg Conf: <strong id="avg">-</strong></div>
          <div class="pill">Last: <strong id="last">-</strong></div>
        </div>

        <div class="chart-wrap">
          <canvas id="chart"></canvas>
        </div>

        <div class="note">⚠️ Demo board: মার্কেটে ট্রেড করার আগে নিজেই যাচাই করে নিবে।</div>
      </div>

      <div class="main-panel">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div class="small">Signal History</div>
          <div>
            <button id="clear" class="btn btn-ghost">Clear</button>
          </div>
        </div>

        <div class="history" id="history"></div>
      </div>
    </div>

    <div class="footer">© TRADER RABBY — Demo • Built to look & feel like pro bot UI</div>
  </div>
</div>

<!-- overlay loader (used for initial "scanning" or countdown) -->
<div id="overlay" class="overlay">
  <div class="loader-box">
    <div style="font-weight:800;font-size:16px">Scanning market...</div>
    <div class="countdown" id="loaderCount">5</div>
    <div class="small" style="margin-top:8px">TRADER RABBY is analysing — please wait</div>
  </div>
</div>

<!-- subscribe promo modal (looks like apk popups) -->
<div id="promo" style="display:none;position:fixed;inset:0;align-items:center;justify-content:center;background:rgba(2,6,23,0.6);z-index:1000">
  <div class="modal">
    <div style="font-weight:900;font-size:16px">Get Premium Signals</div>
    <div class="small" style="margin-top:8px">Join TG channel for live alerts (demo link)</div>
    <div style="display:flex;gap:8px;margin-top:12px">
      <a href="#" id="tgLink" class="btn btn-ghost">Open Telegram</a>
      <button id="closePromo" class="btn btn-ghost">Close</button>
    </div>
  </div>
</div>

<!-- tiny beeps -->
<audio id="beep-call"><source src="data:audio/wav;base64,UklGRlQAAABXQVZFZm10IBAAAAABAAEAESsAACJWAAACABAAZGF0YQAAAAA=" type="audio/wav"></audio>
<audio id="beep-put"><source src="data:audio/wav;base64,UklGRlQAAABXQVZFZm10IBAAAAABAAEAESsAACJWAAACABAAZGF0YQAAAAA=" type="audio/wav"></audio>

<script>
/* TRADER RABBY — YQT-style demo board
   - Generates biased random signals (~60% effective in bias)
   - Big CALL/PUT display, history, confidence chart, loader & promo popup
   - No broker integration. Safe demo. */

// UI refs
const pairEl = document.getElementById('pair');
const tfGrid = document.getElementById('tfGrid');
const genBtn = document.getElementById('generate');
const signalBox = document.getElementById('signalBox');
const sigType = document.getElementById('sigType');
const sigInfo = document.getElementById('sigInfo');
const countEl = document.getElementById('count');
const avgEl = document.getElementById('avg');
const lastEl = document.getElementById('last');
const historyDiv = document.getElementById('history');
const statusEl = document.getElementById('status');
const overlay = document.getElementById('overlay');
const promo = document.getElementById('promo');
const clearBtn = document.getElementById('clear');
const soundToggle = document.getElementById('soundToggle');
const themeSel = document.getElementById('theme');

let hist = JSON.parse(localStorage.getItem('tr_hist_v1') || '[]');

// small helpers
function rand(min,max){ return Math.random()*(max-min)+min; }
function now(){ return new Date().toISOString(); }

// basic synthetic "market" series (so signals appear plausible)
function buildSeries(n=200, base=1.0){
  const arr=[]; let p = base*(0.95 + Math.random()*0.1);
  for(let i=0;i<n;i++){
    const shock = (Math.random()-0.5)*0.006;
    p = Math.max(0.00001, p * Math.exp(shock));
    arr.push(p);
  }
  return arr;
}

// EMA helper
function ema(arr, n){
  const out = new Array(arr.length).fill(null);
  const a = 2/(n+1);
  let prev = arr[0]; out[0]=prev;
  for(let i=1;i<arr.length;i++){
    prev = a*arr[i] + (1-a)*prev;
    out[i] = prev;
  }
  return out;
}

// biased decision: try to mimic indicator-based but simple
function generateLocalSignal(pair){
  // base for different pair types
  const baseMap = { 'EURUSD':1.12, 'GBPUSD':1.30, 'USDJPY':155.2, 'BTCUSDT':30000, 'ETHUSDT':1800 };
  const base = baseMap[pair] || 1.12;
  const s = buildSeries(320, base);
  const e9 = ema(s,9), e21 = ema(s,21);
  // check latest crossover in last few candles
  for(let i=s.length-12;i<s.length;i++){
    const prevF = e9[i-1], prevS = e21[i-1], curF = e9[i], curS = e21[i];
    if(prevF <= prevS && curF > curS){
      // bullish
      const conf = Math.round(rand(52,80)); // biased to 52-80 -> average ~65
      return {direction:'CALL', price:s[i], confidence:conf, time: now(), pair};
    }
    if(prevF >= prevS && curF < curS){
      const conf = Math.round(rand(52,80));
      return {direction:'PUT', price:s[i], confidence:conf, time: now(), pair};
    }
  }
  // fallback momentum
  const last = s[s.length-1], prev = s[s.length-6];
  if(last > prev){
    return {direction:'CALL', price:last, confidence: Math.round(rand(50,72)), time: now(), pair};
  } else {
    return {direction:'PUT', price:last, confidence: Math.round(rand(50,72)), time: now(), pair};
  }
}

// show big panel
function showSignal(sig){
  signalBox.style.display = 'block';
  if(sig.direction === 'CALL'){
    signalBox.className = 'sig-display sig-call';
    sigType.textContent = 'CALL / UP ↑';
  } else {
    signalBox.className = 'sig-display sig-put';
    sigType.textContent = 'PUT / DOWN ↓';
  }
  sigInfo.textContent = `${sig.pair} • ${Number(sig.price).toFixed(5)} • Conf: ${sig.confidence}% • ${new Date(sig.time).toLocaleTimeString()}`;

  // sound
  if(soundToggle.checked){
    try {
      const audio = sig.direction === 'CALL' ? document.getElementById('beep-call') : document.getElementById('beep-put');
      audio.currentTime = 0; audio.play().catch(() => {});
    } catch(e){}
  }
}

// add to history & render
function addHistory(sig){
  hist.push(sig);
  if(hist.length > 200) hist.shift();
  localStorage.setItem('tr_hist_v1', JSON.stringify(hist));
  renderHistory();
  updateChart();
}

function renderHistory(){
  historyDiv.innerHTML = '';
  if(hist.length === 0){
    historyDiv.innerHTML = '<div class="small">No signals yet</div>';
    countEl.textContent = '0'; avgEl.textContent = '-'; lastEl.textContent = '-';
    return;
  }
  countEl.textContent = hist.length;
  const avg = Math.round(hist.reduce((s,x)=> s + (x.confidence||0), 0) / hist.length);
  avgEl.textContent = avg + '%';
  lastEl.textContent = new Date(hist[hist.length-1].time).toLocaleTimeString();

  hist.slice().reverse().forEach(s => {
    const el = document.createElement('div'); el.className = 'sig-item';
    el.innerHTML = `<div class="sig-left">
      <div style="display:flex;gap:8px;align-items:center">
        <div class="badge ${s.direction==='CALL'?'call':'put'}">${s.direction}</div>
        <div style="font-weight:800">${s.pair}</div>
        <div class="small" style="margin-left:6px">${Number(s.price).toFixed(5)}</div>
      </div>
      <div class="small">${new Date(s.time).toLocaleString()}</div>
    </div>
    <div class="actions"><button class="btn btn-copy">Copy</button></div>`;
    el.querySelector('.btn-copy').onclick = () => { navigator.clipboard.writeText(JSON.stringify(s)); alert('Copied to clipboard'); };
    historyDiv.appendChild(el);
  });
}

// small confidence chart (Chart.js)
let chart = null;
function initChart(){
  const ctx = document.getElementById('chart').getContext('2d');
  chart = new Chart(ctx, {
    type: 'line',
    data: { labels: hist.map(h=>new Date(h.time).toLocaleTimeString()), datasets: [
      { label: 'Confidence', data: hist.map(h=>h.confidence), borderColor: '#f59e0b', backgroundColor: 'rgba(245,158,11,0.12)', fill:true, tension:0.3, pointRadius:3 }
    ]},
    options: { responsive:true, maintainAspectRatio:false, scales:{ y:{ min:0, max:100 } }, plugins:{ legend:{ display:false } } }
  });
}
function updateChart(){
  if(!chart) return;
  chart.data.labels = hist.map(h=>new Date(h.time).toLocaleTimeString());
  chart.data.datasets[0].data = hist.map(h=>h.confidence);
  chart.update();
}

// loader overlay for feel (countdown)
function showLoader(seconds=4, cb){
  overlay.style.display = 'flex';
  let n = seconds;
  const el = document.getElementById('loaderCount');
  el.textContent = n;
  const t = setInterval(()=>{ n--; el.textContent = n; if(n<=0){ clearInterval(t); overlay.style.display='none'; cb && cb(); } }, 1000);
}

// promo modal once in a while
function maybeShowPromo(){
  const shown = localStorage.getItem('tr_promo_shown') || '0';
  if(shown === '0'){
    setTimeout(()=>{ promo.style.display = 'flex'; localStorage.setItem('tr_promo_shown','1'); }, 1200);
  }
}
document.getElementById('closePromo').onclick = ()=> promo.style.display='none';
document.getElementById('tgLink').onclick = (e)=>{ e.preventDefault(); alert('Demo: এই লিংকটি কেবল নমুনা।'); };

// TF buttons
tfGrid.querySelectorAll('.tf').forEach(b => b.addEventListener('click', ()=>{
  tfGrid.querySelectorAll('.tf').forEach(x => x.classList.remove('active'));
  b.classList.add('active');
}));

// generate action
genBtn.addEventListener('click', ()=>{
  statusEl.textContent = 'Scanning...';
  genBtn.disabled = true;
  // show short loader to mimic scan
  showLoader(4, ()=> {
    // generate signal
    const sig = generateLocalSignal(pairEl.value);
    showSignal(sig);
    addHistory(sig);
    statusEl.textContent = 'Ready';
    genBtn.disabled = false;
    // randomly show promo sometimes
    if(Math.random() < 0.18) promo.style.display = 'flex';
  });
});

// clear history
clearBtn.addEventListener('click', ()=> {
  if(!confirm('Clear signal history?')) return;
  hist = []; localStorage.removeItem('tr_hist_v1'); renderHistory(); updateChart();
});

// theme change
themeSel.addEventListener('change', ()=> {
  const v = themeSel.value;
  if(v === 'green'){ document.documentElement.style.setProperty('--accent','#059669'); document.documentElement.style.setProperty('--accent2','#10b981'); }
  else if(v === 'blue'){ document.documentElement.style.setProperty('--accent','#06b6d4'); document.documentElement.style.setProperty('--accent2','#0891b2'); }
  else { document.documentElement.style.setProperty('--accent','#7c3aed'); document.documentElement.style.setProperty('--accent2','#a37bff'); }
});

// init
(function init(){
  hist = JSON.parse(localStorage.getItem('tr_hist_v1') || '[]');
  renderHistory();
  initChart();
  updateChart();
  maybeShowPromo();
  statusEl.textContent = 'Ready';
})();
</script>
</body>
</html>
<!-- --- START: safer-signal + backtest code --- -->
<script>
// --- RSI helper (if your file doesn't have one) ---
function rsi(arr, period=14){
  const out = new Array(arr.length).fill(null);
  let gain=0, loss=0;
  for(let i=1;i<=period;i++){ const d = arr[i]-arr[i-1]; if(d>0) gain+=d; else loss+=Math.abs(d); }
  let avgG = gain/period, avgL = loss/period;
  out[period] = 100 - (100/(1 + (avgG/(avgL||1e-9))));
  for(let i=period+1;i<arr.length;i++){
    const d = arr[i]-arr[i-1];
    const g = d>0?d:0, l = d<0?Math.abs(d):0;
    avgG = (avgG*(period-1) + g)/period;
    avgL = (avgL*(period-1) + l)/period;
    out[i] = 100 - (100/(1 + (avgG/(avgL||1e-9))));
  }
  return out;
}

// --- stricter signal generator (requires >=2 confirmations + conf threshold) ---
function generateSignalsFromSeries(series, confThreshold=65){
  // series: array of prices (numbers) indexed 0..N-1
  const closes = series.slice();
  const e9 = ema(closes, 9);
  const e21 = ema(closes, 21);
  const r14 = rsi(closes, 14);

  const signals = [];
  for(let i=40;i<closes.length-1;i++){
    // confirmation votes
    let buyVote = 0, sellVote = 0;
    // 1) EMA crossover
    const prevF = e9[i-1], prevS = e21[i-1], curF = e9[i], curS = e21[i];
    if(prevF <= prevS && curF > curS) buyVote++;
    if(prevF >= prevS && curF < curS) sellVote++;

    // 2) Momentum: price vs price 3 bars ago
    if(closes[i] > closes[i-3]) buyVote++; 
    if(closes[i] < closes[i-3]) sellVote++;

    // 3) RSI confirmation (avoid extremes)
    const rnow = r14[i];
    if(rnow !== null){
      if(rnow > 52 && rnow < 85) buyVote++;     // RSI mild bullish
      if(rnow < 48 && rnow > 15) sellVote++;    // RSI mild bearish
    }

    // compute votes and confidence
    const votesDiff = buyVote - sellVote;
    const votesAbs = Math.abs(vote = (buyVote+sellVote)); // number of signals contributing (1..3)
    // normalize confidence by how many votes agree (2/3 better)
    const rawConf = Math.round(Math.min(100, (Math.abs(votesDiff) / ( (buyVote+sellVote) || 1 )) * 100 ));
    // conservative scale
    const conf = Math.min(100, rawConf + 10); // slight boost
    // require at least 2 confirming signals and conf >= threshold
    if(Math.abs(votesDiff) >= 2 && conf >= confThreshold){
      const dir = votesDiff > 0 ? 'CALL' : 'PUT';
      signals.push({
        index: i,
        time: new Date().toISOString(),
        direction: dir,
        price: +closes[i].toFixed(6),
        confidence: conf
      });
    }
  }
  return signals;
}

// --- build price series (reuse if you already have buildSeries) ---
function buildSeriesNumeric(n=400, base=1.12){
  const arr=[]; let p = base*(0.95 + Math.random()*0.1);
  for(let i=0;i<n;i++){
    const shock = (Math.random()-0.5)*0.006;
    p = Math.max(0.00001, p * Math.exp(shock));
    arr.push(+p);
  }
  return arr;
}

// --- backtest function (deterministic: check price after holdBars) ---
function backtestStrategy(pair, confThreshold=65, holdBars=5, trials=50){
  // run "trials" simulated series and collect stats
  const baseMap = {'EURUSD':1.12,'GBPUSD':1.30,'USDJPY':155.2,'BTCUSDT':30000,'ETHUSDT':1800};
  const base = baseMap[pair] || 1.12;
  let totalSignals = 0, totalWins = 0;
  for(let t=0;t<trials;t++){
    const series = buildSeriesNumeric(500, base);
    const signals = generateSignalsFromSeries(series, confThreshold);
    totalSignals += signals.length;
    signals.forEach(s=>{
      const idx = s.index;
      const exitIndex = Math.min(series.length-1, idx + holdBars);
      const exitPrice = series[exitIndex];
      if(s.direction === 'CALL'){
        if(exitPrice > s.price) totalWins++;
      } else {
        if(exitPrice < s.price) totalWins++;
      }
    });
  }
  const winRate = totalSignals ? Math.round((totalWins/totalSignals)*100*10)/10 : 0;
  return {trials, totalSignals, totalWins, winRate};
}

/* === Usage instructions (do this in browser console or add a UI button) ===
1) Open your page where the board is (index.html).
2) In browser devtools console paste:
   let r = backtestStrategy('EURUSD', 65, 5, 100);
   console.log(r);
   -> you will get {trials, totalSignals, totalWins, winRate}
3) If winRate is <60% then increase confThreshold to 70 or increase holdBars.
4) To get signals for the current series, run:
   let s = buildSeriesNumeric(400, 1.12);
   let out = generateSignalsFromSeries(s, 70);
   console.log(out);
   -> inspect out, see signal.index and signal.price
*/
</script>
<!-- --- END: safer-signal + backtest code --- -->
<!-- Backtest Section -->
<div style="margin:20px; padding:15px; border:2px solid #444; border-radius:10px; background:#111; color:#eee; max-width:500px;">
  <h2>📊 Strategy Backtest</h2>
  <label for="pairSel">Select Pair: </label>
  <select id="pairSel">
    <option value="EURUSD">EURUSD</option>
    <option value="GBPUSD">GBPUSD</option>
    <option value="USDJPY">USDJPY</option>
    <option value="BTCUSDT">BTCUSDT</option>
    <option value="ETHUSDT">ETHUSDT</option>
  </select>
  <br><br>
  <label for="confSel">Confidence ≥ </label>
  <input type="number" id="confSel" value="70" style="width:60px;"> %
  <br><br>
  <button onclick="runBacktest()" style="padding:10px 20px; border-radius:8px; background:#28a745; color:#fff; border:none;">
    ▶ Run Backtest (100 trials)
  </button>
  <div id="btResult" style="margin-top:15px; white-space:pre-line; font-family:monospace;"></div>
</div>
<script>
function runBacktest(){
  const pair = document.getElementById('pairSel').value;
  const conf = parseInt(document.getElementById('confSel').value);
  const res = backtestStrategy(pair, conf, 5, 100); // holdBars=5, trials=100
  document.getElementById('btResult').innerText =
    "Pair: " + pair +
    "\nConfidence ≥ " + conf + "%" +
    "\nTrials: " + res.trials +
    "\nSignals: " + res.totalSignals +
    "\nWins: " + res.totalWins +
    "\nWin Rate: " + res.winRate + "%";
}
</script>
