<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Minecraft — Little In‑Game Store</title>
  <style>
    :root{--bg:#0b1220;--card:#0f1724;--muted:#9aa6b2;--accent:#2dd4bf;--glass:rgba(255,255,255,0.03)}
    *{box-sizing:border-box}
    body{font-family:Inter, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial; background:linear-gradient(180deg,#071028 0%, #08121a 100%); color:#e6eef6; margin:0; min-height:100vh; display:flex; align-items:center; justify-content:center; padding:28px}
    .store{width:980px; max-width:96vw; background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); border-radius:12px; box-shadow:0 8px 30px rgba(2,6,23,0.7); overflow:hidden; display:grid; grid-template-columns:1fr 320px}
    header{padding:18px 22px; border-bottom:1px solid rgba(255,255,255,0.03); display:flex; align-items:center; gap:12px}
    header .logo{font-weight:700; font-size:20px}
    main{padding:18px 22px}
    .items{display:flex; gap:14px; flex-wrap:wrap}
    .item{background:var(--card); padding:14px; border-radius:10px; width:220px; display:flex; flex-direction:column; gap:10px; box-shadow:inset 0 1px 0 rgba(255,255,255,0.02)}
    .item .art{height:96px; border-radius:8px; background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.12)); display:flex; align-items:center; justify-content:center; font-size:44px}
    .item h3{margin:0; font-size:16px}
    .price{display:flex; align-items:center; gap:8px; color:var(--muted)}
    button.buy{margin-top:auto; background:linear-gradient(180deg,var(--accent), #14b8a6); color:#042022; border:none; padding:8px 10px; border-radius:8px; font-weight:600; cursor:pointer}
    button.buy:active{transform:translateY(1px)}
    aside{background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.02)); padding:18px; border-left:1px solid rgba(255,255,255,0.03)}
    .wallet{background:var(--glass); padding:12px; border-radius:10px; margin-bottom:14px}
    .wallet h4{margin:0 0 8px 0}
    .counts{display:flex; gap:10px}
    .count{flex:1; background:rgba(255,255,255,0.02); padding:8px; border-radius:8px; text-align:center}
    .count b{display:block; font-size:18px}

    .log{height:260px; overflow:auto; background:rgba(255,255,255,0.02); border-radius:8px; padding:10px; font-size:13px; color:var(--muted)}
    .log p{margin:6px 0}
    .small{font-size:12px; color:var(--muted)}

    footer{padding:12px 18px; border-top:1px solid rgba(255,255,255,0.02); text-align:center; font-size:13px; color:var(--muted)}

    /* responsive */
    @media (max-width:860px){.store{grid-template-columns:1fr} aside{order:2}}
  </style>
</head>
<body>
  <div class="store" role="application" aria-label="Minecraft mini store">
    <div>
      <header>
        <div class="logo">🛒 Minecraft Store</div>
        <div class="small">Buy wool with diamonds — quick, in‑game style</div>
      </header>
      <main>
        <section class="items">
          <!-- Item: 32 Wool for 1 Diamond -->
          <div class="item" id="pack32">
            <div class="art">🧶</div>
            <h3>32 Wool</h3>
            <div class="small">A stack of 32 wool blocks — great for building.</div>
            <div class="price">Price: <strong>1</strong> <span title="diamond">💎</span></div>
            <button class="buy" onclick="buy(32,1,'32 wool')">Buy 32 wool — 1 💎</button>
          </div>

          <!-- Item: 64 Wool for 2 Diamonds -->
          <div class="item" id="pack64">
            <div class="art">🧶🧶</div>
            <h3>64 Wool</h3>
            <div class="small">Double stack: 64 wool blocks.</div>
            <div class="price">Price: <strong>2</strong> <span title="diamonds">💎</span></div>
            <button class="buy" onclick="buy(64,2,'64 wool')">Buy 64 wool — 2 💎</button>
          </div>
        </section>
      </main>
      <footer>Tip: You can change your diamond count in the wallet panel.</footer>
    </div>

    <aside>
      <div class="wallet">
        <h4>Your Wallet</h4>
        <div class="counts">
          <div class="count">
            <div class="small">Diamonds</div>
            <b id="diamonds">5</b>
            <div class="small">💎</div>
          </div>
          <div class="count">
            <div class="small">Wool</div>
            <b id="wool">0</b>
            <div class="small">🧶</div>
          </div>
        </div>
        <div style="margin-top:10px; display:flex; gap:8px">
          <input id="setDiam" type="number" min="0" placeholder="set diamonds" style="flex:1;padding:8px;border-radius:8px;border:none;background:rgba(255,255,255,0.02);color:inherit" />
          <button class="buy" style="padding:8px 10px" onclick="setDiam()">Set</button>
        </div>
        <div style="margin-top:8px;font-size:12px;color:var(--muted)">(This simulates the player's inventory. Values persist in your browser.)</div>
      </div>

      <div>
        <h4>Activity Log</h4>
        <div class="log" id="log"></div>
      </div>
    </aside>
  </div>

  <script>
    // Simple in-browser store simulation. No servers involved.
    const elDiam = document.getElementById('diamonds');
    const elWool = document.getElementById('wool');
    const elLog = document.getElementById('log');

    // Load saved state from localStorage (so reloads keep values)
    const state = {
      diamonds: parseInt(localStorage.getItem('mc_diamonds') || '5', 10),
      wool: parseInt(localStorage.getItem('mc_wool') || '0', 10),
    };
    function render(){
      elDiam.textContent = state.diamonds;
      elWool.textContent = state.wool;
    }

    function save(){
      localStorage.setItem('mc_diamonds', state.diamonds);
      localStorage.setItem('mc_wool', state.wool);
    }

    function log(msg){
      const p = document.createElement('p');
      p.textContent = msg;
      elLog.prepend(p);
    }

    function buy(woolAmount, costDiamonds, label){
      if(state.diamonds < costDiamonds){
        log(`Not enough diamonds to buy ${label}. Need ${costDiamonds} 💎.`);
        flashButton(false);
        return;
      }
      // perform purchase
      state.diamonds -= costDiamonds;
      state.wool += woolAmount;
      save(); render();
      log(`Purchased ${label} for ${costDiamonds} 💎. +${woolAmount} 🧶`);
      flashButton(true);
    }

    // Visual feedback: tiny flash on success/fail
    function flashButton(success){
      document.body.animate([{opacity:1},{opacity:0.95},{opacity:1}],{duration:260});
      if(success){
        // small confetti-like text (simple)
        const a = document.createElement('div');
        a.textContent = '✔';
        a.style.position='fixed'; a.style.right='22px'; a.style.top='22px'; a.style.fontSize='20px'; a.style.opacity=0.9;
        document.body.appendChild(a);
        setTimeout(()=>a.remove(),600);
      }else{
        const a = document.createElement('div');
        a.textContent = '✖';
        a.style.position='fixed'; a.style.right='22px'; a.style.top='22px'; a.style.fontSize='20px'; a.style.opacity=0.9;
        document.body.appendChild(a);
        setTimeout(()=>a.remove(),600);
      }
    }

    function setDiam(){
      const v = parseInt(document.getElementById('setDiam').value,10);
      if(Number.isInteger(v) && v>=0){
        state.diamonds = v; save(); render(); log(`Set diamonds to ${v} 💎`);
      } else { log('Enter a valid number of diamonds (0 or more).'); }
    }

    // init
    render();
    log('Welcome to the little in‑game store.');
    log('Store sells: 32 wool for 1 💎 and 64 wool for 2 💎.');
  </script>
</body>
</html>
