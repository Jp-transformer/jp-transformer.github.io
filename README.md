<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Solar Calculator — JP Transformer</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;500;600;700&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --sun: #EF9F27;
    --sun-light: #FAC775;
    --green: #1D9E75;
    --green-light: #9FE1CB;
    --dark: #080E14;
    --card: #0F1923;
    --card2: #1A2535;
    --border: rgba(255,255,255,0.07);
    --text: #E8E6DF;
    --muted: #6B7280;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--dark);
    font-family: 'DM Sans', sans-serif;
    color: var(--text);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Background grid */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(239,159,39,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(239,159,39,0.03) 1px, transparent 1px);
    background-size: 48px 48px;
    pointer-events: none;
    z-index: 0;
  }

  /* Glow blobs */
  .blob1 {
    position: fixed; top: -120px; right: -100px;
    width: 500px; height: 500px; border-radius: 50%;
    background: radial-gradient(circle, rgba(239,159,39,0.12) 0%, transparent 70%);
    pointer-events: none; z-index: 0;
  }
  .blob2 {
    position: fixed; bottom: -150px; left: -100px;
    width: 450px; height: 450px; border-radius: 50%;
    background: radial-gradient(circle, rgba(29,158,117,0.1) 0%, transparent 70%);
    pointer-events: none; z-index: 0;
  }

  .container {
    position: relative; z-index: 1;
    max-width: 520px; margin: 0 auto;
    padding: 40px 20px 60px;
  }

  /* Header */
  .brand {
    display: flex; align-items: center; gap: 10px;
    margin-bottom: 48px;
  }
  .brand-icon {
    width: 36px; height: 36px; border-radius: 8px;
    background: var(--sun); display: flex; align-items: center;
    justify-content: center;
  }
  .brand-icon svg { width: 20px; height: 20px; }
  .brand-name {
    font-family: 'Syne', sans-serif;
    font-size: 16px; font-weight: 700;
    color: #fff; letter-spacing: -0.3px;
  }
  .brand-tag {
    font-size: 11px; color: var(--muted); margin-top: 1px;
  }

  /* Hero */
  .hero { margin-bottom: 40px; }
  .hero-label {
    font-size: 11px; font-weight: 500; letter-spacing: 2px;
    color: var(--sun); text-transform: uppercase; margin-bottom: 12px;
    display: flex; align-items: center; gap: 8px;
  }
  .hero-label::before {
    content: ''; display: block;
    width: 20px; height: 1px; background: var(--sun);
  }
  .hero h1 {
    font-family: 'Syne', sans-serif;
    font-size: clamp(28px, 7vw, 42px);
    font-weight: 700; line-height: 1.1;
    letter-spacing: -1px; color: #fff;
    margin-bottom: 14px;
  }
  .hero h1 span { color: var(--sun); }
  .hero p {
    font-size: 15px; color: var(--muted); line-height: 1.7;
    font-weight: 300;
  }

  /* Calculator card */
  .calc-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 28px 24px;
    margin-bottom: 20px;
  }

  .input-group { margin-bottom: 24px; }
  .input-label {
    font-size: 12px; font-weight: 500; color: var(--muted);
    letter-spacing: 0.5px; text-transform: uppercase;
    margin-bottom: 10px; display: block;
  }

  .input-row {
    display: flex; align-items: center; gap: 0;
    background: var(--card2); border-radius: 12px;
    border: 1px solid var(--border); overflow: hidden;
    transition: border-color .2s;
  }
  .input-row:focus-within { border-color: var(--sun); }

  .input-row input {
    flex: 1; background: transparent; border: none; outline: none;
    padding: 14px 16px;
    font-family: 'Syne', sans-serif;
    font-size: 22px; font-weight: 600; color: #fff;
    width: 100%;
  }
  .input-row input::placeholder { color: rgba(255,255,255,0.15); }
  .input-unit {
    padding: 0 16px; font-size: 13px;
    color: var(--muted); white-space: nowrap;
    border-left: 1px solid var(--border);
    height: 52px; display: flex; align-items: center;
  }

  /* Slider */
  .slider-wrap { margin-top: 14px; }
  input[type=range] {
    -webkit-appearance: none; width: 100%; height: 4px;
    background: var(--card2); border-radius: 2px; outline: none;
    cursor: pointer;
  }
  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 20px; height: 20px; border-radius: 50%;
    background: var(--sun); cursor: pointer;
    box-shadow: 0 0 0 4px rgba(239,159,39,0.2);
    transition: box-shadow .2s;
  }
  input[type=range]::-webkit-slider-thumb:hover {
    box-shadow: 0 0 0 8px rgba(239,159,39,0.2);
  }
  .slider-ticks {
    display: flex; justify-content: space-between;
    margin-top: 6px;
  }
  .slider-ticks span { font-size: 11px; color: var(--muted); }

  /* Button */
  .calc-btn {
    width: 100%; padding: 16px;
    background: var(--sun); border: none; border-radius: 12px;
    font-family: 'Syne', sans-serif;
    font-size: 15px; font-weight: 700; color: #080E14;
    cursor: pointer; letter-spacing: 0.3px;
    transition: opacity .2s, transform .1s;
    display: flex; align-items: center; justify-content: center; gap: 8px;
  }
  .calc-btn:hover { opacity: .9; }
  .calc-btn:active { transform: scale(.98); }

  /* Result */
  .result-wrap {
    display: none; margin-top: 20px;
    animation: slideDown .4s cubic-bezier(.22,.68,0,1.2);
  }
  .result-wrap.show { display: block; }

  @keyframes slideDown {
    from { opacity: 0; transform: translateY(-12px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .result-grid {
    display: grid; grid-template-columns: 1fr 1fr 1fr;
    gap: 10px; margin-bottom: 16px;
  }
  .result-item {
    background: var(--card2); border-radius: 12px;
    padding: 14px 10px; text-align: center;
    border: 1px solid var(--border);
  }
  .result-item.highlight {
    border-color: var(--sun);
    background: rgba(239,159,39,0.08);
  }
  .result-num {
    font-family: 'Syne', sans-serif;
    font-size: 22px; font-weight: 700;
    color: var(--sun); margin-bottom: 4px;
  }
  .result-item.highlight .result-num { font-size: 26px; }
  .result-desc { font-size: 11px; color: var(--muted); line-height: 1.4; }

  .result-msg {
    background: rgba(29,158,117,0.1);
    border: 1px solid rgba(29,158,117,0.3);
    border-radius: 10px; padding: 12px 16px;
    font-size: 13px; color: var(--green-light);
    line-height: 1.6; text-align: center;
  }

  /* Subsidy strip */
  .subsidy-strip {
    background: var(--card); border: 1px solid var(--border);
    border-radius: 16px; padding: 20px 24px;
    margin-bottom: 20px;
    display: flex; align-items: center; gap: 16px;
  }
  .subsidy-icon {
    width: 44px; height: 44px; border-radius: 10px;
    background: rgba(29,158,117,0.15);
    border: 1px solid rgba(29,158,117,0.3);
    display: flex; align-items: center; justify-content: center;
    flex-shrink: 0; font-size: 20px;
  }
  .subsidy-text h3 {
    font-family: 'Syne', sans-serif;
    font-size: 14px; font-weight: 600; color: #fff;
    margin-bottom: 3px;
  }
  .subsidy-text p { font-size: 12px; color: var(--muted); line-height: 1.5; }
  .subsidy-badge {
    display: inline-block; margin-top: 6px;
    background: var(--green); color: #fff;
    font-size: 11px; font-weight: 500;
    padding: 3px 10px; border-radius: 20px;
  }

  /* CTA */
  .cta-card {
    background: linear-gradient(135deg, rgba(239,159,39,0.12), rgba(29,158,117,0.08));
    border: 1px solid rgba(239,159,39,0.2);
    border-radius: 16px; padding: 24px;
    text-align: center;
  }
  .cta-card h3 {
    font-family: 'Syne', sans-serif;
    font-size: 18px; font-weight: 700; color: #fff;
    margin-bottom: 8px;
  }
  .cta-card p { font-size: 13px; color: var(--muted); margin-bottom: 16px; line-height: 1.6; }
  .cta-insta {
    display: inline-flex; align-items: center; gap: 8px;
    background: var(--card2); border: 1px solid var(--border);
    border-radius: 10px; padding: 10px 18px;
    font-family: 'Syne', sans-serif;
    font-size: 14px; font-weight: 600; color: var(--sun);
    text-decoration: none; transition: border-color .2s;
  }
  .cta-insta:hover { border-color: var(--sun); }

  /* Footer */
  .footer {
    text-align: center; margin-top: 40px;
    padding-top: 20px; border-top: 1px solid var(--border);
  }
  .footer p { font-size: 12px; color: var(--muted); line-height: 1.8; }
  .footer strong { color: var(--sun); }

  /* Animations */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
  }
  .brand { animation: fadeUp .5s ease .1s both; }
  .hero { animation: fadeUp .5s ease .2s both; }
  .calc-card { animation: fadeUp .5s ease .35s both; }
  .subsidy-strip { animation: fadeUp .5s ease .5s both; }
  .cta-card { animation: fadeUp .5s ease .6s both; }

  /* Mobile tweaks */
  @media (max-width: 400px) {
    .result-grid { grid-template-columns: 1fr 1fr; }
    .result-item:first-child { grid-column: 1/-1; }
  }
</style>
</head>
<body>

<div class="blob1"></div>
<div class="blob2"></div>

<div class="container">

  <div class="brand">
    <div class="brand-icon">
      <svg viewBox="0 0 24 24" fill="none" stroke="#080E14" stroke-width="2.5" stroke-linecap="round">
        <circle cx="12" cy="12" r="4"/>
        <line x1="12" y1="2" x2="12" y2="5"/>
        <line x1="12" y1="19" x2="12" y2="22"/>
        <line x1="4.22" y1="4.22" x2="6.34" y2="6.34"/>
        <line x1="17.66" y1="17.66" x2="19.78" y2="19.78"/>
        <line x1="2" y1="12" x2="5" y2="12"/>
        <line x1="19" y1="12" x2="22" y2="12"/>
        <line x1="4.22" y1="19.78" x2="6.34" y2="17.66"/>
        <line x1="17.66" y1="6.34" x2="19.78" y2="4.22"/>
      </svg>
    </div>
    <div>
      <div class="brand-name">JP Transformer</div>
      <div class="brand-tag">Solar Solutions — Haryana, India</div>
    </div>
  </div>

  <div class="hero">
    <div class="hero-label">Free Tool</div>
    <h1>Apne ghar ka<br><span>solar size</span><br>calculate karo</h1>
    <p>Apna monthly bijli bill enter karo — main bataoonga kitne kW ka solar system aapke ghar ke liye sahi hai.</p>
  </div>

  <div class="calc-card">
    <div class="input-group">
      <label class="input-label">Monthly bijli consumption</label>
      <div class="input-row">
        <input type="number" id="units-input" placeholder="300" min="0" max="2000" oninput="syncInput(this.value)">
        <div class="input-unit">units / month</div>
      </div>
      <div class="slider-wrap">
        <input type="range" id="units-slider" min="50" max="1000" value="300" step="10" oninput="syncSlider(this.value)">
        <div class="slider-ticks">
          <span>50 units</span>
          <span>500 units</span>
          <span>1000 units</span>
        </div>
      </div>
    </div>

    <button class="calc-btn" onclick="calculate()">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><path d="M13 2L3 14h9l-1 8 10-12h-9l1-8z"/></svg>
      Calculate karo
    </button>

    <div class="result-wrap" id="result-wrap">
      <div style="height:1px;background:var(--border);margin:20px 0"></div>
      <div class="result-grid">
        <div class="result-item">
          <div class="result-num" id="r-daily">—</div>
          <div class="result-desc">Daily units<br>chahiye</div>
        </div>
        <div class="result-item highlight">
          <div class="result-num" id="r-kw">—</div>
          <div class="result-desc">Solar system<br>size (kW)</div>
        </div>
        <div class="result-item">
          <div class="result-num" id="r-panels">—</div>
          <div class="result-desc">Panels<br>(~550W each)</div>
        </div>
      </div>
      <div class="result-msg" id="r-msg"></div>
    </div>
  </div>

  <div class="subsidy-strip">
    <div class="subsidy-icon">&#9728;</div>
    <div class="subsidy-text">
      <h3>Govt Subsidy Available</h3>
      <p>PM Surya Ghar Yojana ke under ₹30,000 se ₹78,000 tak subsidy milti hai.</p>
      <span class="subsidy-badge">Free apply guide — Instagram pe</span>
    </div>
  </div>

  <div class="cta-card">
    <h3>Expert se baat karo</h3>
    <p>System size pata chal gayi? Ab sahi installation ke liye JP Transformer se sampark karo. Instagram pe follow karo — daily solar tips + personal replies.</p>
    <a class="cta-insta" href="https://instagram.com/JPTransformer" target="_blank">
      <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="2" y="2" width="20" height="20" rx="5"/><circle cx="12" cy="12" r="4"/><circle cx="17.5" cy="6.5" r="1" fill="currentColor" stroke="none"/></svg>
      @JPTransformer
    </a>
  </div>

  <div class="footer">
    <p>
      <strong>JP Transformer</strong> — Senior Solar Engineer, Haryana<br>
      On-Grid · Off-Grid · Hybrid Solar Systems<br>
      <span style="font-size:11px;opacity:.6">© 2025 JP Transformer. Sab rights reserved.</span>
    </p>
  </div>

</div>

<script>
  function syncSlider(v) {
    document.getElementById('units-input').value = v;
  }
  function syncInput(v) {
    var n = parseInt(v) || 0;
    if (n >= 50 && n <= 1000) document.getElementById('units-slider').value = n;
  }

  function calculate() {
    var u = parseInt(document.getElementById('units-input').value) || 0;
    if (u <= 0) {
      document.getElementById('units-input').focus();
      return;
    }
    var daily = (u / 30).toFixed(1);
    var kw = (u / 150).toFixed(1);
    var panels = Math.ceil((parseFloat(kw) * 1000) / 550);

    document.getElementById('r-daily').textContent = daily + ' u';
    document.getElementById('r-kw').textContent = kw + ' kW';
    document.getElementById('r-panels').textContent = panels + ' nos';

    var msg = '';
    var fkw = parseFloat(kw);
    if (fkw <= 1) msg = '1 kW system kaafi hai. On-Grid best rahega aur PM Surya Ghar subsidy bhi milegi.';
    else if (fkw <= 2) msg = '1–2 kW standard home system. On-Grid ya Hybrid — dono achhe hain aapke liye.';
    else if (fkw <= 3) msg = '2–3 kW system — Hybrid inverter recommend karein. Battery ke saath raat ko bhi bijli milegi.';
    else if (fkw <= 5) msg = '3–5 kW medium system — Hybrid zaroor lagao. Growatt ya Solis inverter check karo.';
    else msg = '5 kW+ bada system — proper site assessment zaroori hai. JP Transformer se directly contact karo.';

    document.getElementById('r-msg').textContent = msg;

    var rw = document.getElementById('result-wrap');
    rw.classList.remove('show');
    void rw.offsetWidth;
    rw.classList.add('show');
  }

  document.getElementById('units-input').addEventListener('keydown', function(e) {
    if (e.key === 'Enter') calculate();
  });

  document.getElementById('units-input').value = 300;
</script>
</body>
</html>
