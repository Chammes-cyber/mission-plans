<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Friday Night Mission Picker</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500&family=Space+Grotesk:wght@500;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #F2F1FA;
    --surface: #FFFFFF;
    --ink: #1B1D3A;
    --muted: #5C5F82;
    --line: #D6D5EA;
    --accent: #4B3BE0;
    --accent-ink: #FFFFFF;
    --glow: #FFB547;
    --sans: "Space Grotesk", system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
    --mono: "IBM Plex Mono", ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
    box-sizing: border-box;
    padding-top: env(safe-area-inset-top, 0px);
    padding-bottom: env(safe-area-inset-bottom, 0px);
  }
  @media (prefers-color-scheme: dark) {
    :root:not([data-theme="light"]) {
      --bg: #12142A;
      --surface: #1C1F3D;
      --ink: #ECEBFF;
      --muted: #9FA2C8;
      --line: #34386A;
      --accent: #FFB547;
      --accent-ink: #1B1400;
      --glow: #FFB547;
    }
  }
  :root[data-theme="dark"] {
    --bg: #12142A;
    --surface: #1C1F3D;
    --ink: #ECEBFF;
    --muted: #9FA2C8;
    --line: #34386A;
    --accent: #FFB547;
    --accent-ink: #1B1400;
    --glow: #FFB547;
  }
  html { scroll-padding-top: env(safe-area-inset-top, 0px); }
  *, *::before, *::after { box-sizing: inherit; }
  body {
    margin: 0;
    background: var(--bg);
    color: var(--ink);
    font-family: var(--sans);
    line-height: 1.45;
    -webkit-text-size-adjust: 100%;
  }
  main { max-width: 34rem; margin: 0 auto; padding: 1.5rem 1.1rem 3rem; }
  .status {
    font-family: var(--mono);
    font-size: 0.8rem;
    color: var(--muted);
    margin: 0 0 0.9rem;
  }
  .status::before {
    content: "";
    display: inline-block;
    width: 0.55rem; height: 0.55rem;
    border-radius: 50%;
    background: var(--glow);
    margin-right: 0.5rem;
    vertical-align: baseline;
  }
  h1 {
    font-size: clamp(2rem, 9vw, 2.8rem);
    line-height: 1.05;
    letter-spacing: -0.02em;
    margin: 0 0 0.7rem;
    font-weight: 700;
  }
  .lede { color: var(--muted); margin: 0 0 1.8rem; max-width: 30rem; }

  fieldset { border: 0; margin: 0 0 2rem; padding: 0; display: grid; gap: 0.65rem; min-width: 0; }
  legend {
    font-weight: 700;
    font-size: 1.25rem;
    letter-spacing: -0.01em;
    padding: 0;
    margin-bottom: 0.2rem;
  }
  .sub {
    font-family: var(--mono);
    font-size: 0.82rem;
    color: var(--muted);
    margin: 0 0 0.5rem;
  }
  .opt { position: relative; display: block; }
  .opt input {
    position: absolute; inset: 0; width: 100%; height: 100%;
    opacity: 0; margin: 0; cursor: pointer;
  }
  .card {
    display: grid;
    grid-template-columns: 1.6rem 1fr;
    gap: 0.8rem;
    align-items: start;
    background: var(--surface);
    border: 1.5px solid var(--line);
    border-radius: 0.9rem;
    padding: 0.95rem 1rem;
    transition: border-color 0.15s, transform 0.15s;
  }
  .radio {
    width: 1.3rem; height: 1.3rem;
    border: 2px solid var(--line);
    border-radius: 50%;
    margin-top: 0.15rem;
    display: grid; place-items: center;
  }
  .radio::after {
    content: "";
    width: 0.62rem; height: 0.62rem;
    border-radius: 50%;
    background: var(--accent);
    transform: scale(0);
    transition: transform 0.15s;
  }
  .name { font-weight: 700; font-size: 1.12rem; display: block; }
  .desc { color: var(--muted); font-size: 0.93rem; display: block; margin-top: 0.1rem; }
  .tag {
    display: inline-block;
    font-family: var(--mono);
    font-size: 0.75rem;
    color: var(--muted);
    border: 1px solid var(--line);
    border-radius: 0.35rem;
    padding: 0.05rem 0.4rem;
    margin-top: 0.5rem;
  }
  .opt input:checked + .card { border-color: var(--accent); }
  .opt input:checked + .card .radio { border-color: var(--accent); }
  .opt input:checked + .card .radio::after { transform: scale(1); }
  .opt input:focus-visible + .card { outline: 3px solid var(--glow); outline-offset: 2px; }
  .opt.flash .card { border-color: var(--glow); transform: scale(1.015); }

  .actions { display: grid; gap: 0.6rem; margin-top: 0.4rem; }
  button {
    font: inherit;
    font-weight: 700;
    border-radius: 0.8rem;
    padding: 0.95rem 1rem;
    min-height: 3rem;
    cursor: pointer;
    border: 1.5px solid var(--accent);
  }
  .primary { background: var(--accent); color: var(--accent-ink); }
  .primary:disabled { opacity: 0.45; cursor: not-allowed; }
  .ghost { background: transparent; color: var(--ink); border-color: var(--line); }
  button:focus-visible { outline: 3px solid var(--glow); outline-offset: 2px; }

  .result {
    margin-top: 1.6rem;
    background: var(--surface);
    border: 1.5px solid var(--accent);
    border-radius: 0.9rem;
    padding: 1.1rem;
  }
  .result[hidden] { display: none; }
  .log { font-family: var(--mono); font-size: 0.85rem; color: var(--muted); margin: 0 0 0.8rem; }
  .pick { margin: 0 0 0.7rem; }
  .pick .k { font-family: var(--mono); font-size: 0.8rem; color: var(--muted); display: block; }
  .pick .v { font-size: 1.5rem; font-weight: 700; letter-spacing: -0.01em; display: block; }
  .msg {
    font-family: var(--sans);
    font-size: 1.05rem;
    line-height: 1.7;
    white-space: pre-line;
    background: var(--bg);
    border: 1px dashed var(--line);
    border-radius: 0.6rem;
    padding: 0.8rem 0.95rem;
    margin: 0.5rem 0 0.9rem;
    overflow-wrap: anywhere;
  }
  .note { font-family: var(--mono); font-size: 0.8rem; color: var(--muted); margin: 0.7rem 0 0; min-height: 1.2em; }

  @media (prefers-reduced-motion: reduce) {
    .card, .radio::after { transition: none; }
    .opt.flash .card { transform: none; }
  }
</style>
</head>
<body>
<main>
  <p class="status">2 missions, 10 encrypted options</p>
  <h1>Friday night mission picker</h1>
  <p class="lede">Agent, your flight is landing and your night is unassigned. Two missions are waiting below, each with five encrypted options. Decode the clues, pick one per mission, or let fate route you. Full briefing arrives when you do.</p>

  <form id="form" onsubmit="return false">

    <fieldset>
      <legend>Mission 1: The main event</legend>
      <p class="sub">Decode a clue, then choose.</p>

      <label class="opt"><input type="radio" name="spot" value="The Hacienda">
        <span class="card"><span class="radio"></span><span>
          <span class="name">The Hacienda</span>
          <span class="desc">Every wall here is shouting in color. Nobody minds.</span>
          <span class="tag">clue: sun-baked and loud</span>
        </span></span></label>

      <label class="opt"><input type="radio" name="spot" value="Robot Rodeo">
        <span class="card"><span class="radio"></span><span>
          <span class="name">Robot Rodeo</span>
          <span class="desc">No human will bring you things tonight. Something with wheels will.</span>
          <span class="tag">clue: beep boop, yeehaw</span>
        </span></span></label>

      <label class="opt"><input type="radio" name="spot" value="Spin Cycle">
        <span class="card"><span class="radio"></span><span>
          <span class="name">Spin Cycle</span>
          <span class="desc">It used to clean your clothes. Now it cleans up on reviews.</span>
          <span class="tag">clue: the machines left, the charm stayed</span>
        </span></span></label>

      <label class="opt"><input type="radio" name="spot" value="The Lakehouse">
        <span class="card"><span class="radio"></span><span>
          <span class="name">The Lakehouse</span>
          <span class="desc">The water is the view and the sky is the ceiling.</span>
          <span class="tag">clue: pack a jacket, maybe</span>
        </span></span></label>

      <label class="opt"><input type="radio" name="spot" value="The Mansion">
        <span class="card"><span class="radio"></span><span>
          <span class="name">The Mansion</span>
          <span class="desc">A very old house that still has company over.</span>
          <span class="tag">clue: old Austin, big lawn</span>
        </span></span></label>
    </fieldset>

    <fieldset>
      <legend>Mission 2: After hours</legend>
      <p class="sub">Same rules. Pick the sequel.</p>

      <label class="opt"><input type="radio" name="after" value="City Lights">
        <span class="card"><span class="radio"></span><span>
          <span class="name">City Lights</span>
          <span class="desc">Walk it off. The city makes its own stars after dark.</span>
          <span class="tag">clue: best on foot</span>
        </span></span></label>

      <label class="opt"><input type="radio" name="after" value="Cool Down">
        <span class="card"><span class="radio"></span><span>
          <span class="name">Cool Down</span>
          <span class="desc">Best served cold, and best finished before it melts.</span>
          <span class="tag">clue: toppings are a personal choice</span>
        </span></span></label>

      <label class="opt"><input type="radio" name="after" value="Insert Coin">
        <span class="card"><span class="radio"></span><span>
          <span class="name">Insert Coin</span>
          <span class="desc">Bring your reflexes and your trash talk.</span>
          <span class="tag">clue: someone will lose gracefully</span>
        </span></span></label>

      <label class="opt"><input type="radio" name="after" value="Bungalow Hop">
        <span class="card"><span class="radio"></span><span>
          <span class="name">Bungalow Hop</span>
          <span class="desc">Old houses with new tricks. Several doors, one street.</span>
          <span class="tag">clue: your shoes will have opinions</span>
        </span></span></label>

      <label class="opt"><input type="radio" name="after" value="Low Power Mode">
        <span class="card"><span class="radio"></span><span>
          <span class="name">Low Power Mode</span>
          <span class="desc">Shoes off. Zero obligations.</span>
          <span class="tag">clue: battery saver enabled</span>
        </span></span></label>
    </fieldset>

    <div class="actions">
      <button class="primary" id="deploy" type="button" disabled>Lock in my picks</button>
      <button class="ghost" id="random" type="button">Route randomly</button>
    </div>
  </form>

  <section class="result" id="result" hidden aria-live="polite">
    <p class="log" id="log"></p>
    <p class="pick"><span class="k">Mission 1</span><span class="v" id="w1"></span></p>
    <p class="pick"><span class="k">Mission 2</span><span class="v" id="w2"></span></p>
    <p class="msg" id="msg"></p>
    <p class="sub">Tap Send it back, choose Messages, and send it to me.</p>
    <button class="primary" id="send" type="button" style="width:100%">Send it back</button>
    <button class="ghost" id="copy" type="button" style="width:100%;margin-top:0.6rem">Copy the text instead</button>
    <p class="note" id="note"></p>
  </section>
</main>

<script>
(function () {
  var groups = ['spot', 'after'].map(function (n) {
    return { name: n, radios: Array.prototype.slice.call(document.querySelectorAll('input[name="' + n + '"]')) };
  });
  var deployBtn = document.getElementById('deploy');
  var randomBtn = document.getElementById('random');
  var sendBtn = document.getElementById('send');
  var result = document.getElementById('result');
  var logEl = document.getElementById('log');
  var w1 = document.getElementById('w1');
  var w2 = document.getElementById('w2');
  var msgEl = document.getElementById('msg');
  var note = document.getElementById('note');
  var reduce = window.matchMedia && window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  var busy = false;

  function sel(g) {
    for (var i = 0; i < g.radios.length; i++) if (g.radios[i].checked) return g.radios[i];
    return null;
  }
  function updateBtn() { deployBtn.disabled = !(sel(groups[0]) && sel(groups[1])); }
  function clearFlash(g) {
    g.radios.forEach(function (r) { r.closest('.opt').classList.remove('flash'); });
  }
  function show(how) {
    var a = sel(groups[0]).value;
    var b = sel(groups[1]).value;
    logEl.textContent = how === 'random' ? '> missions assigned' : '> missions locked';
    w1.textContent = a;
    w2.textContent = b;
    msgEl.textContent = 'Friday mission report\nMission 1: ' + a + '\nMission 2: ' + b;
    note.textContent = '';
    result.hidden = false;
    result.scrollIntoView({ behavior: reduce ? 'auto' : 'smooth', block: 'nearest' });
  }

  groups.forEach(function (g) {
    g.radios.forEach(function (r) { r.addEventListener('change', updateBtn); });
  });

  deployBtn.addEventListener('click', function () {
    if (sel(groups[0]) && sel(groups[1])) show('chosen');
  });

  function spin(g, done) {
    var target = Math.floor(Math.random() * g.radios.length);
    var steps = reduce ? 0 : 12 + target;
    var i = 0;
    function finish() {
      clearFlash(g);
      g.radios[target].checked = true;
      done();
    }
    if (steps === 0) { finish(); return; }
    (function tick() {
      clearFlash(g);
      g.radios[i % g.radios.length].closest('.opt').classList.add('flash');
      i++;
      if (i > steps) { finish(); return; }
      setTimeout(tick, 80 + i * 9);
    })();
  }

  randomBtn.addEventListener('click', function () {
    if (busy) return;
    busy = true;
    var targets = groups.filter(function (g) { return !sel(g); });
    if (!targets.length) targets = groups.slice();
    var pending = targets.length;
    targets.forEach(function (g) {
      spin(g, function () {
        pending--;
        if (pending === 0) {
          updateBtn();
          show('random');
          busy = false;
        }
      });
    });
  });

  var copyBtn = document.getElementById('copy');
  copyBtn.addEventListener('click', function () {
    var text = msgEl.textContent;
    try {
      if (navigator.clipboard && navigator.clipboard.writeText) {
        navigator.clipboard.writeText(text).then(
          function () { note.textContent = '> copied. paste it into a text.'; },
          function () { note.textContent = '> press and hold the message above, then tap Copy.'; }
        );
        return;
      }
    } catch (e) {}
    note.textContent = '> press and hold the message above, then tap Copy.';
  });

  sendBtn.addEventListener('click', function () {
    var text = msgEl.textContent;
    function fallbackCopy() {
      try {
        if (navigator.clipboard && navigator.clipboard.writeText) {
          navigator.clipboard.writeText(text).then(
            function () { note.textContent = '> copied. paste it into a text.'; },
            function () { note.textContent = '> long-press the message above to copy it.'; }
          );
          return;
        }
      } catch (e) {}
      note.textContent = '> long-press the message above to copy it.';
    }
    try {
      if (navigator.share) {
        navigator.share({ text: text }).catch(function () {});
        return;
      }
    } catch (e) {}
    fallbackCopy();
  });
})();
</script>
</body>
</html>
