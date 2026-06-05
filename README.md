<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>RJ316 Labs — Engineering Tomorrow</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:ital,wght@0,400;0,700;1,400&family=Syne:wght@400;600;700;800&family=JetBrains+Mono:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0c0f;
    --bg2: #0f1217;
    --bg3: #141820;
    --red: #e63329;
    --blue: #1a56db;
    --green: #22883a;
    --gold: #c8a84b;
    --white: #eef0f4;
    --muted: #5a6070;
    --border: rgba(255,255,255,0.07);
    --circuit: #1e2530;
  }

  *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }

  html { scroll-behavior: smooth; }

  body {
    background: var(--bg);
    color: var(--white);
    font-family: 'Syne', sans-serif;
    overflow-x: hidden;
    cursor: none;
  }

  /* ── CURSOR ── */
  #cursor {
    position: fixed; top: 0; left: 0; pointer-events: none; z-index: 9999;
    mix-blend-mode: difference;
  }
  #cursor .dot {
    width: 8px; height: 8px; background: #fff; border-radius: 50%;
    position: absolute; transform: translate(-50%,-50%);
    transition: width .15s, height .15s;
  }
  #cursor .ring {
    width: 36px; height: 36px; border: 1.5px solid rgba(255,255,255,.5);
    border-radius: 50%;
    position: absolute; transform: translate(-50%,-50%);
    transition: transform .08s ease, width .2s, height .2s, border-color .2s;
  }
  body:has(a:hover) #cursor .ring,
  body:has(button:hover) #cursor .ring { width: 52px; height: 52px; border-color: var(--red); }

  /* ── CANVAS ── */
  #circuit-canvas {
    position: fixed; inset: 0; z-index: 0; opacity: .18; pointer-events: none;
  }

  /* ── NAV ── */
  nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 100;
    padding: 20px 48px;
    display: flex; align-items: center; justify-content: space-between;
    background: rgba(10,12,15,.85);
    backdrop-filter: blur(16px);
    border-bottom: 1px solid var(--border);
    transition: padding .3s;
  }
  nav.scrolled { padding: 14px 48px; }

  .nav-logo {
    display: flex; align-items: center; gap: 10px;
    font-family: 'Space Mono', monospace;
    font-size: 1rem; font-weight: 700; letter-spacing: .02em;
  }
  .nav-logo .chip {
    width: 32px; height: 32px; border: 2px solid var(--muted);
    border-radius: 6px; display: grid; place-items: center;
    font-size: .65rem; color: var(--green);
    position: relative;
  }
  .nav-logo .chip::before {
    content:'';
    position: absolute; left: -6px; top: 50%; width: 4px; height: 1px;
    background: var(--muted);
  }
  .nav-logo span.r { color: var(--red); }
  .nav-logo span.j { color: var(--blue); }

  .nav-links { display: flex; gap: 36px; list-style: none; }
  .nav-links a {
    font-family: 'JetBrains Mono', monospace;
    font-size: .78rem; color: var(--muted);
    text-decoration: none; letter-spacing: .06em;
    transition: color .2s;
    position: relative;
  }
  .nav-links a::after {
    content:''; position: absolute; bottom: -3px; left: 0;
    width: 0; height: 1px; background: var(--red);
    transition: width .25s;
  }
  .nav-links a:hover { color: var(--white); }
  .nav-links a:hover::after { width: 100%; }

  .nav-cta {
    padding: 9px 20px; border: 1px solid var(--red);
    color: var(--red); font-family: 'JetBrains Mono', monospace;
    font-size: .75rem; letter-spacing: .08em;
    text-decoration: none; transition: background .2s, color .2s;
    cursor: none;
  }
  .nav-cta:hover { background: var(--red); color: #fff; }

  /* ── HERO ── */
  #hero {
    position: relative; z-index: 1;
    min-height: 100vh;
    display: flex; align-items: center;
    padding: 120px 48px 80px;
  }

  .hero-content { max-width: 720px; }

  .hero-eyebrow {
    font-family: 'JetBrains Mono', monospace;
    font-size: .75rem; letter-spacing: .2em;
    color: var(--green); margin-bottom: 24px;
    display: flex; align-items: center; gap: 12px;
  }
  .hero-eyebrow::before {
    content:''; width: 32px; height: 1px; background: var(--green);
  }

  h1 {
    font-size: clamp(3rem, 8vw, 6.5rem);
    font-weight: 800; line-height: .95;
    letter-spacing: -.02em;
    margin-bottom: 28px;
  }
  h1 .line1 { display: block; }
  h1 .r-word { color: var(--red); }
  h1 .accent { color: var(--blue); }

  .hero-sub {
    font-family: 'JetBrains Mono', monospace;
    font-size: .92rem; color: var(--muted);
    line-height: 1.8; max-width: 520px;
    margin-bottom: 44px;
  }
  .hero-sub .code-tag { color: var(--green); }

  .hero-btns { display: flex; gap: 16px; flex-wrap: wrap; }

  .btn-primary {
    padding: 14px 32px;
    background: var(--red); color: #fff;
    font-family: 'JetBrains Mono', monospace;
    font-size: .8rem; letter-spacing: .1em;
    text-decoration: none; border: none;
    position: relative; overflow: hidden;
    transition: transform .2s;
    cursor: none;
    display: inline-block;
  }
  .btn-primary::after {
    content:''; position: absolute; inset: 0;
    background: rgba(255,255,255,.12);
    transform: translateX(-100%);
    transition: transform .3s;
  }
  .btn-primary:hover { transform: translateY(-2px); }
  .btn-primary:hover::after { transform: translateX(0); }

  .btn-ghost {
    padding: 14px 32px;
    border: 1px solid var(--border); color: var(--muted);
    font-family: 'JetBrains Mono', monospace;
    font-size: .8rem; letter-spacing: .1em;
    text-decoration: none;
    transition: border-color .2s, color .2s;
    cursor: none;
    display: inline-block;
  }
  .btn-ghost:hover { border-color: var(--blue); color: var(--white); }

  /* code ticker */
  .code-ticker {
    position: absolute; right: 48px; top: 50%;
    transform: translateY(-50%);
    font-family: 'JetBrains Mono', monospace;
    font-size: .7rem; color: var(--muted);
    line-height: 1.9;
    border-left: 2px solid var(--circuit);
    padding-left: 24px;
    max-width: 260px;
    display: none;
  }
  @media(min-width:1100px){ .code-ticker { display: block; } }
  .code-ticker .hl-r { color: var(--red); }
  .code-ticker .hl-b { color: var(--blue); }
  .code-ticker .hl-g { color: var(--green); }
  .code-ticker .hl-y { color: var(--gold); }

  .hero-scroll {
    position: absolute; bottom: 36px; left: 50%;
    transform: translateX(-50%);
    display: flex; flex-direction: column; align-items: center; gap: 8px;
    font-family: 'JetBrains Mono', monospace;
    font-size: .65rem; letter-spacing: .15em; color: var(--muted);
    animation: bounce 2s infinite;
  }
  .hero-scroll .arrow { width: 1px; height: 40px; background: linear-gradient(to bottom, var(--muted), transparent); }
  @keyframes bounce {
    0%,100%{transform:translateX(-50%) translateY(0)}
    50%{transform:translateX(-50%) translateY(6px)}
  }

  /* ── MARQUEE ── */
  .marquee-strip {
    position: relative; z-index: 1;
    border-top: 1px solid var(--border);
    border-bottom: 1px solid var(--border);
    background: var(--bg2);
    overflow: hidden; padding: 14px 0;
  }
  .marquee-inner {
    display: flex; gap: 0; white-space: nowrap;
    animation: marquee 28s linear infinite;
  }
  .marquee-inner span {
    font-family: 'JetBrains Mono', monospace;
    font-size: .72rem; letter-spacing: .15em;
    color: var(--muted); padding: 0 40px;
  }
  .marquee-inner span.accent { color: var(--red); }
  @keyframes marquee { from{transform:translateX(0)} to{transform:translateX(-50%)} }

  /* ── SECTION COMMONS ── */
  section { position: relative; z-index: 1; }

  .section-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: .7rem; letter-spacing: .22em;
    color: var(--muted); margin-bottom: 12px;
    display: flex; align-items: center; gap: 10px;
  }
  .section-label::before { content:'//'; color: var(--green); }

  h2 {
    font-size: clamp(2rem, 5vw, 3.6rem);
    font-weight: 800; line-height: 1.05;
    letter-spacing: -.02em; margin-bottom: 20px;
  }

  /* ── ABOUT ── */
  #about {
    padding: 100px 48px;
    display: grid; grid-template-columns: 1fr 1fr;
    gap: 80px; align-items: center;
  }
  @media(max-width:800px){ #about { grid-template-columns:1fr; padding:80px 24px; gap:40px; } }

  .about-text p {
    font-family: 'JetBrains Mono', monospace;
    font-size: .88rem; color: var(--muted);
    line-height: 1.9; margin-bottom: 16px;
  }
  .about-text p strong { color: var(--white); }

  .about-stat-grid {
    display: grid; grid-template-columns: 1fr 1fr;
    gap: 2px; margin-top: 40px;
  }
  .stat-box {
    background: var(--bg2); padding: 28px 24px;
    border: 1px solid var(--border);
    transition: border-color .2s;
  }
  .stat-box:hover { border-color: var(--red); }
  .stat-num {
    font-size: 2.6rem; font-weight: 800;
    letter-spacing: -.03em; line-height: 1;
    margin-bottom: 6px;
  }
  .stat-num .unit { font-size: 1.4rem; color: var(--red); }
  .stat-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: .68rem; color: var(--muted); letter-spacing: .1em;
  }

  .about-visual {
    position: relative;
  }
  .chip-diagram {
    width: 100%; aspect-ratio: 1;
    max-width: 400px; margin: 0 auto;
    display: block;
  }

  /* ── SERVICES ── */
  #services {
    padding: 100px 48px;
    background: var(--bg2);
    border-top: 1px solid var(--border);
  }
  @media(max-width:600px){ #services { padding: 80px 24px; } }

  .services-header { margin-bottom: 60px; max-width: 600px; }
  .services-header p {
    font-family: 'JetBrains Mono', monospace;
    font-size: .85rem; color: var(--muted); line-height: 1.8; margin-top: 16px;
  }

  .services-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 2px;
  }

  .service-card {
    background: var(--bg); padding: 36px 32px;
    border: 1px solid var(--border);
    position: relative; overflow: hidden;
    transition: transform .3s, border-color .3s;
    cursor: none;
  }
  .service-card::before {
    content:''; position: absolute;
    top: 0; left: 0; right: 0; height: 2px;
    transform: scaleX(0); transform-origin: left;
    transition: transform .35s;
  }
  .service-card:nth-child(1)::before { background: var(--red); }
  .service-card:nth-child(2)::before { background: var(--blue); }
  .service-card:nth-child(3)::before { background: var(--green); }
  .service-card:nth-child(4)::before { background: var(--gold); }
  .service-card:nth-child(5)::before { background: var(--red); }
  .service-card:nth-child(6)::before { background: var(--blue); }
  .service-card:hover { transform: translateY(-4px); border-color: rgba(255,255,255,.14); }
  .service-card:hover::before { transform: scaleX(1); }

  .svc-icon {
    width: 44px; height: 44px; margin-bottom: 24px;
    display: grid; place-items: center;
    font-size: 1.3rem;
    border: 1px solid var(--border);
  }
  .svc-title {
    font-size: 1.1rem; font-weight: 700;
    margin-bottom: 12px; letter-spacing: -.01em;
  }
  .svc-desc {
    font-family: 'JetBrains Mono', monospace;
    font-size: .76rem; color: var(--muted); line-height: 1.8;
  }
  .svc-tag {
    display: inline-block; margin-top: 20px;
    font-family: 'JetBrains Mono', monospace;
    font-size: .62rem; letter-spacing: .12em;
    padding: 4px 10px;
    border: 1px solid var(--border);
    color: var(--muted);
  }

  /* ── TECH STACK ── */
  #stack {
    padding: 100px 48px;
    border-top: 1px solid var(--border);
  }
  @media(max-width:600px){ #stack { padding: 80px 24px; } }

  .stack-header { margin-bottom: 60px; }

  .stack-categories {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
    gap: 32px;
  }
  .stack-cat h4 {
    font-family: 'JetBrains Mono', monospace;
    font-size: .68rem; letter-spacing: .18em; color: var(--muted);
    margin-bottom: 16px;
    display: flex; align-items: center; gap: 8px;
  }
  .stack-cat h4::after { content:''; flex: 1; height: 1px; background: var(--border); }

  .tech-pills { display: flex; flex-wrap: wrap; gap: 8px; }
  .tech-pill {
    font-family: 'JetBrains Mono', monospace;
    font-size: .7rem; padding: 6px 14px;
    border: 1px solid var(--border);
    color: var(--muted);
    transition: border-color .2s, color .2s, background .2s;
  }
  .tech-pill:hover { border-color: var(--blue); color: var(--white); background: rgba(26,86,219,.08); }

  /* ── PROCESS ── */
  #process {
    padding: 100px 48px;
    background: var(--bg2);
    border-top: 1px solid var(--border);
  }
  @media(max-width:600px){ #process { padding: 80px 24px; } }

  .process-steps {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 0; margin-top: 60px;
    border: 1px solid var(--border);
  }
  .step {
    padding: 36px 28px;
    border-right: 1px solid var(--border);
    position: relative;
  }
  .step:last-child { border-right: none; }
  @media(max-width:700px){
    .process-steps { grid-template-columns: 1fr 1fr; }
    .step:nth-child(2n){ border-right: none; }
    .step { border-bottom: 1px solid var(--border); }
  }

  .step-num {
    font-family: 'Space Mono', monospace;
    font-size: 2.5rem; font-weight: 700;
    color: var(--border); line-height: 1;
    margin-bottom: 16px;
    transition: color .3s;
  }
  .step:hover .step-num { color: var(--red); }
  .step-title { font-size: .95rem; font-weight: 700; margin-bottom: 10px; }
  .step-desc {
    font-family: 'JetBrains Mono', monospace;
    font-size: .72rem; color: var(--muted); line-height: 1.7;
  }

  /* ── CTA ── */
  #cta {
    padding: 120px 48px;
    text-align: center;
    border-top: 1px solid var(--border);
    position: relative; overflow: hidden;
  }
  #cta::before {
    content:'';
    position: absolute; top: 50%; left: 50%;
    transform: translate(-50%,-50%);
    width: 600px; height: 600px;
    background: radial-gradient(circle, rgba(230,51,41,.07) 0%, transparent 70%);
    pointer-events: none;
  }

  .cta-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: .75rem; letter-spacing: .2em; color: var(--green);
    margin-bottom: 20px;
  }
  #cta h2 { margin-bottom: 20px; }
  #cta p {
    font-family: 'JetBrains Mono', monospace;
    font-size: .85rem; color: var(--muted);
    max-width: 480px; margin: 0 auto 44px; line-height: 1.8;
  }
  .cta-email {
    display: inline-flex; align-items: center; gap: 0;
    margin-top: 32px;
  }
  .cta-email input {
    background: var(--bg2); border: 1px solid var(--border);
    border-right: none; padding: 14px 20px;
    color: var(--white); font-family: 'JetBrains Mono', monospace;
    font-size: .78rem; outline: none; width: 280px;
    transition: border-color .2s;
  }
  .cta-email input:focus { border-color: var(--blue); }
  .cta-email input::placeholder { color: var(--muted); }
  .cta-email button {
    padding: 14px 24px; background: var(--blue);
    color: #fff; border: 1px solid var(--blue);
    font-family: 'JetBrains Mono', monospace;
    font-size: .78rem; letter-spacing: .08em;
    cursor: none; transition: background .2s;
  }
  .cta-email button:hover { background: #1245b8; }

  /* ── FOOTER ── */
  footer {
    background: var(--bg2);
    border-top: 1px solid var(--border);
    padding: 60px 48px 36px;
  }
  @media(max-width:600px){ footer { padding: 60px 24px 36px; } }

  .footer-top {
    display: grid; grid-template-columns: 2fr 1fr 1fr 1fr;
    gap: 48px; margin-bottom: 56px;
  }
  @media(max-width:800px){ .footer-top { grid-template-columns: 1fr 1fr; } }
  @media(max-width:500px){ .footer-top { grid-template-columns: 1fr; } }

  .footer-brand .logo-text {
    font-family: 'Space Mono', monospace;
    font-size: 1.1rem; font-weight: 700; margin-bottom: 16px;
  }
  .footer-brand p {
    font-family: 'JetBrains Mono', monospace;
    font-size: .74rem; color: var(--muted); line-height: 1.8;
    max-width: 260px;
  }
  .footer-brand .socials {
    display: flex; gap: 12px; margin-top: 24px;
  }
  .social-btn {
    width: 34px; height: 34px;
    border: 1px solid var(--border); display: grid;
    place-items: center; color: var(--muted);
    font-size: .75rem; text-decoration: none;
    transition: border-color .2s, color .2s;
    cursor: none;
  }
  .social-btn:hover { border-color: var(--blue); color: var(--white); }

  .footer-col h5 {
    font-family: 'JetBrains Mono', monospace;
    font-size: .68rem; letter-spacing: .18em; color: var(--muted);
    margin-bottom: 20px;
  }
  .footer-col ul { list-style: none; }
  .footer-col li { margin-bottom: 10px; }
  .footer-col a {
    font-family: 'JetBrains Mono', monospace;
    font-size: .76rem; color: var(--muted);
    text-decoration: none; transition: color .2s;
    cursor: none;
  }
  .footer-col a:hover { color: var(--white); }

  .footer-bottom {
    display: flex; align-items: center; justify-content: space-between;
    padding-top: 24px; border-top: 1px solid var(--border);
    font-family: 'JetBrains Mono', monospace;
    font-size: .67rem; color: var(--muted);
    flex-wrap: wrap; gap: 12px;
  }
  .footer-bottom .dot { color: var(--red); }

  /* ── REVEAL ── */
  .reveal { opacity: 0; transform: translateY(28px); transition: opacity .7s, transform .7s; }
  .reveal.visible { opacity: 1; transform: translateY(0); }

  /* ── MOBILE NAV ── */
  @media(max-width:700px){
    nav { padding: 18px 24px; }
    nav.scrolled { padding: 12px 24px; }
    .nav-links, .nav-cta { display: none; }
    #hero { padding: 120px 24px 80px; }
    h1 { font-size: clamp(2.4rem,12vw,4rem); }
  }
</style>
</head>
<body>

<canvas id="circuit-canvas"></canvas>
<div id="cursor"><div class="dot"></div><div class="ring"></div></div>

<!-- NAV -->
<nav id="nav">
  <div class="nav-logo">
    <div class="chip">&lt;{:}&gt;</div>
    <span><span class="r">R</span><span class="j">J</span> 3:16 Labs</span>
  </div>
  <ul class="nav-links">
    <li><a href="#about">// about</a></li>
    <li><a href="#services">// services</a></li>
    <li><a href="#stack">// stack</a></li>
    <li><a href="#process">// process</a></li>
  </ul>
  <a href="#cta" class="nav-cta">GET IN TOUCH →</a>
</nav>

<!-- HERO -->
<section id="hero">
  <div class="hero-content">
    <div class="hero-eyebrow">RJ316 LABS — EST. 2024</div>
    <h1>
      <span class="line1">We <span class="r-word">Build</span></span>
      <span class="line1"><span class="accent">Intelligent</span></span>
      <span class="line1">Systems.</span>
    </h1>
    <p class="hero-sub">
      <span class="code-tag">&lt;/&gt;</span> Full-spectrum engineering studio specializing in scalable software, AI integration, and enterprise-grade solutions. We write the code that powers tomorrow.
    </p>
    <div class="hero-btns">
      <a href="#services" class="btn-primary">EXPLORE SERVICES</a>
      <a href="#about" class="btn-ghost">OUR STORY →</a>
    </div>
  </div>

  <div class="code-ticker">
    <div class="hl-g">// rj316labs.init()</div>
    <div><span class="hl-b">const</span> <span class="hl-y">vision</span> = {</div>
    <div>&nbsp; mission: <span class="hl-r">"innovate"</span>,</div>
    <div>&nbsp; stack: <span class="hl-r">"full-spectrum"</span>,</div>
    <div>&nbsp; clients: <span class="hl-y">∞</span></div>
    <div>};</div>
    <div>&nbsp;</div>
    <div class="hl-g">// SYSTEM ONLINE ✓</div>
    <div class="hl-b">export default</div>
    <div>&nbsp; <span class="hl-r">Excellence</span>;</div>
  </div>

  <div class="hero-scroll">SCROLL<div class="arrow"></div></div>
</section>

<!-- MARQUEE -->
<div class="marquee-strip">
  <div class="marquee-inner">
    <span>SOFTWARE ENGINEERING</span><span class="accent">✦</span>
    <span>AI INTEGRATION</span><span class="accent">✦</span>
    <span>CLOUD INFRASTRUCTURE</span><span class="accent">✦</span>
    <span>MOBILE DEVELOPMENT</span><span class="accent">✦</span>
    <span>DATA ENGINEERING</span><span class="accent">✦</span>
    <span>SYSTEM ARCHITECTURE</span><span class="accent">✦</span>
    <span>UI/UX DESIGN</span><span class="accent">✦</span>
    <span>DEVOPS & CI/CD</span><span class="accent">✦</span>
    <!-- duplicate for seamless loop -->
    <span>SOFTWARE ENGINEERING</span><span class="accent">✦</span>
    <span>AI INTEGRATION</span><span class="accent">✦</span>
    <span>CLOUD INFRASTRUCTURE</span><span class="accent">✦</span>
    <span>MOBILE DEVELOPMENT</span><span class="accent">✦</span>
    <span>DATA ENGINEERING</span><span class="accent">✦</span>
    <span>SYSTEM ARCHITECTURE</span><span class="accent">✦</span>
    <span>UI/UX DESIGN</span><span class="accent">✦</span>
    <span>DEVOPS & CI/CD</span><span class="accent">✦</span>
  </div>
</div>

<!-- ABOUT -->
<section id="about">
  <div class="about-text reveal">
    <div class="section-label">WHO WE ARE</div>
    <h2>Labs Built for <span style="color:var(--red)">Builders</span></h2>
    <p>RJ316 Labs is an engineering-first technology studio. We don't just consult — we <strong>build alongside you</strong>, treating every project as a product we'd be proud to ship under our own name.</p>
    <p>From zero-to-one MVPs to enterprise-scale platforms, our team brings <strong>deep technical craft</strong> and relentless attention to detail. The code symbol in our logo says it all — <strong>we speak technology natively</strong>.</p>
    <div class="about-stat-grid">
      <div class="stat-box"><div class="stat-num">50<span class="unit">+</span></div><div class="stat-label">PROJECTS SHIPPED</div></div>
      <div class="stat-box"><div class="stat-num">3<span class="unit">x</span></div><div class="stat-label">FASTER DELIVERY</div></div>
      <div class="stat-box"><div class="stat-num">99<span class="unit">%</span></div><div class="stat-label">UPTIME SLA</div></div>
      <div class="stat-box"><div class="stat-num">24<span class="unit">/7</span></div><div class="stat-label">SUPPORT & OPS</div></div>
    </div>
  </div>
  <div class="about-visual reveal">
    <svg class="chip-diagram" viewBox="0 0 400 400" fill="none" xmlns="http://www.w3.org/2000/svg">
      <!-- circuit lines -->
      <line x1="80" y1="140" x2="30" y2="140" stroke="#1e2530" stroke-width="2"/>
      <line x1="30" y1="140" x2="30" y2="90" stroke="#1e2530" stroke-width="2"/>
      <circle cx="30" cy="90" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="80" y1="180" x2="20" y2="180" stroke="#1e2530" stroke-width="2"/>
      <circle cx="20" cy="180" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="80" y1="220" x2="30" y2="220" stroke="#1e2530" stroke-width="2"/>
      <line x1="30" y1="220" x2="30" y2="270" stroke="#1e2530" stroke-width="2"/>
      <circle cx="30" cy="270" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="320" y1="140" x2="370" y2="140" stroke="#1e2530" stroke-width="2"/>
      <line x1="370" y1="140" x2="370" y2="90" stroke="#1e2530" stroke-width="2"/>
      <circle cx="370" cy="90" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="320" y1="180" x2="380" y2="180" stroke="#1e2530" stroke-width="2"/>
      <circle cx="380" cy="180" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="320" y1="220" x2="370" y2="220" stroke="#1e2530" stroke-width="2"/>
      <line x1="370" y1="220" x2="370" y2="270" stroke="#1e2530" stroke-width="2"/>
      <circle cx="370" cy="270" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="140" y1="80" x2="140" y2="30" stroke="#1e2530" stroke-width="2"/>
      <circle cx="140" cy="30" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="200" y1="80" x2="200" y2="15" stroke="#1e2530" stroke-width="2"/>
      <circle cx="200" cy="15" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="260" y1="80" x2="260" y2="30" stroke="#1e2530" stroke-width="2"/>
      <circle cx="260" cy="30" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="140" y1="320" x2="140" y2="375" stroke="#1e2530" stroke-width="2"/>
      <circle cx="140" cy="375" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="200" y1="320" x2="200" y2="385" stroke="#1e2530" stroke-width="2"/>
      <circle cx="200" cy="385" r="6" stroke="#1e2530" stroke-width="2"/>
      <line x1="260" y1="320" x2="260" y2="375" stroke="#1e2530" stroke-width="2"/>
      <circle cx="260" cy="375" r="6" stroke="#1e2530" stroke-width="2"/>
      <!-- chip body -->
      <rect x="80" y="80" width="240" height="240" rx="16" fill="#0f1217" stroke="#2a3040" stroke-width="2"/>
      <!-- inner detail lines -->
      <rect x="100" y="100" width="200" height="200" rx="8" fill="none" stroke="#1e2530" stroke-width="1" stroke-dasharray="4 4"/>
      <!-- R -->
      <text x="120" y="210" font-family="Syne, sans-serif" font-weight="800" font-size="80" fill="#e63329">R</text>
      <!-- J -->
      <text x="205" y="210" font-family="Syne, sans-serif" font-weight="800" font-size="80" fill="#1a56db">J</text>
      <!-- code symbol -->
      <text x="135" y="260" font-family="JetBrains Mono, monospace" font-size="22" fill="#22883a">&lt;{:}&gt;</text>
      <!-- green dot -->
      <circle cx="200" cy="96" r="8" fill="#22883a"/>
      <!-- star sparkle -->
      <path d="M222 74 L225 66 L228 74 L236 77 L228 80 L225 88 L222 80 L214 77 Z" fill="#1a56db"/>
    </svg>
  </div>
</section>

<!-- SERVICES -->
<section id="services">
  <div class="services-header reveal">
    <div class="section-label">WHAT WE DO</div>
    <h2>Services That <span style="color:var(--blue)">Scale</span></h2>
    <p>End-to-end engineering capabilities under one roof. From architecture decisions to production deployments — we own the full lifecycle.</p>
  </div>
  <div class="services-grid">
    <div class="service-card reveal">
      <div class="svc-icon">⚙️</div>
      <div class="svc-title">Custom Software Development</div>
      <div class="svc-desc">Bespoke applications built to your exact specifications. We architect for scale from day one, not as an afterthought.</div>
      <span class="svc-tag">FULL-STACK</span>
    </div>
    <div class="service-card reveal">
      <div class="svc-icon">🤖</div>
      <div class="svc-title">AI & ML Integration</div>
      <div class="svc-desc">Embed intelligent automation into your products. LLM orchestration, RAG pipelines, computer vision, and predictive analytics.</div>
      <span class="svc-tag">AI-POWERED</span>
    </div>
    <div class="service-card reveal">
      <div class="svc-icon">☁️</div>
      <div class="svc-title">Cloud Architecture</div>
      <div class="svc-desc">Design and migration of cloud-native infrastructure. AWS, GCP, and Azure deployments that are secure, observable, and cost-efficient.</div>
      <span class="svc-tag">CLOUD-NATIVE</span>
    </div>
    <div class="service-card reveal">
      <div class="svc-icon">📱</div>
      <div class="svc-title">Mobile Development</div>
      <div class="svc-desc">React Native and native iOS/Android applications with performance-first engineering and pixel-perfect UI execution.</div>
      <span class="svc-tag">CROSS-PLATFORM</span>
    </div>
    <div class="service-card reveal">
      <div class="svc-icon">🗄️</div>
      <div class="svc-title">Data Engineering</div>
      <div class="svc-desc">Pipelines, warehouses, and dashboards. We turn raw data into structured, actionable intelligence your teams can trust.</div>
      <span class="svc-tag">DATA-DRIVEN</span>
    </div>
    <div class="service-card reveal">
      <div class="svc-icon">🔐</div>
      <div class="svc-title">Security & DevSecOps</div>
      <div class="svc-desc">Security posture reviews, penetration testing, CI/CD pipeline hardening, and compliance-ready infrastructure.</div>
      <span class="svc-tag">SECURITY-FIRST</span>
    </div>
  </div>
</section>

<!-- TECH STACK -->
<section id="stack">
  <div class="stack-header reveal">
    <div class="section-label">TECHNOLOGIES</div>
    <h2>Our <span style="color:var(--green)">Tech Stack</span></h2>
  </div>
  <div class="stack-categories reveal">
    <div class="stack-cat">
      <h4>FRONTEND</h4>
      <div class="tech-pills">
        <span class="tech-pill">React</span>
        <span class="tech-pill">Next.js</span>
        <span class="tech-pill">Vue</span>
        <span class="tech-pill">TypeScript</span>
        <span class="tech-pill">Tailwind</span>
      </div>
    </div>
    <div class="stack-cat">
      <h4>BACKEND</h4>
      <div class="tech-pills">
        <span class="tech-pill">Node.js</span>
        <span class="tech-pill">Python</span>
        <span class="tech-pill">Go</span>
        <span class="tech-pill">FastAPI</span>
        <span class="tech-pill">GraphQL</span>
      </div>
    </div>
    <div class="stack-cat">
      <h4>AI / ML</h4>
      <div class="tech-pills">
        <span class="tech-pill">OpenAI</span>
        <span class="tech-pill">LangChain</span>
        <span class="tech-pill">TensorFlow</span>
        <span class="tech-pill">PyTorch</span>
        <span class="tech-pill">Pinecone</span>
      </div>
    </div>
    <div class="stack-cat">
      <h4>CLOUD</h4>
      <div class="tech-pills">
        <span class="tech-pill">AWS</span>
        <span class="tech-pill">GCP</span>
        <span class="tech-pill">Azure</span>
        <span class="tech-pill">Docker</span>
        <span class="tech-pill">Kubernetes</span>
      </div>
    </div>
    <div class="stack-cat">
      <h4>DATABASE</h4>
      <div class="tech-pills">
        <span class="tech-pill">PostgreSQL</span>
        <span class="tech-pill">MongoDB</span>
        <span class="tech-pill">Redis</span>
        <span class="tech-pill">Supabase</span>
        <span class="tech-pill">Snowflake</span>
      </div>
    </div>
    <div class="stack-cat">
      <h4>MOBILE</h4>
      <div class="tech-pills">
        <span class="tech-pill">React Native</span>
        <span class="tech-pill">Swift</span>
        <span class="tech-pill">Kotlin</span>
        <span class="tech-pill">Expo</span>
        <span class="tech-pill">Flutter</span>
      </div>
    </div>
  </div>
</section>

<!-- PROCESS -->
<section id="process">
  <div class="reveal">
    <div class="section-label">HOW WE WORK</div>
    <h2>The <span style="color:var(--red)">Process</span></h2>
  </div>
  <div class="process-steps">
    <div class="step reveal">
      <div class="step-num">01</div>
      <div class="step-title">Discovery</div>
      <div class="step-desc">Deep-dive sessions to understand your domain, goals, and technical constraints. No assumptions — only clarity.</div>
    </div>
    <div class="step reveal">
      <div class="step-num">02</div>
      <div class="step-title">Architecture</div>
      <div class="step-desc">System design, tech stack selection, and roadmap planning. We blueprint before we build.</div>
    </div>
    <div class="step reveal">
      <div class="step-num">03</div>
      <div class="step-title">Build</div>
      <div class="step-desc">Iterative development with weekly demos. Code reviews, automated tests, and CI/CD from sprint one.</div>
    </div>
    <div class="step reveal">
      <div class="step-num">04</div>
      <div class="step-title">Deploy</div>
      <div class="step-desc">Zero-downtime deployments, observability setup, and performance benchmarking before any go-live.</div>
    </div>
    <div class="step reveal">
      <div class="step-num">05</div>
      <div class="step-title">Iterate</div>
      <div class="step-desc">Post-launch support, monitoring, and continuous improvement cycles. We don't disappear after delivery.</div>
    </div>
  </div>
</section>

<!-- CTA -->
<section id="cta">
  <div class="cta-label">// START A PROJECT</div>
  <h2>Ready to <span style="color:var(--red)">Build</span>?</h2>
  <p>Tell us what you're working on. We respond within 24 hours and are always direct about what's feasible.</p>
  <div class="hero-btns" style="justify-content:center; flex-wrap:wrap; gap:16px;">
    <a href="mailto:hello@rj316labs.com" class="btn-primary">hello@rj316labs.com</a>
    <a href="#services" class="btn-ghost">VIEW SERVICES →</a>
  </div>
  <div class="cta-email">
    <input type="email" placeholder="your@email.com">
    <button>SUBSCRIBE →</button>
  </div>
</section>

<!-- FOOTER -->
<footer>
  <div class="footer-top">
    <div class="footer-brand">
      <div class="logo-text"><span style="color:var(--red)">R</span><span style="color:var(--blue)">J</span> 3:16 Labs</div>
      <p>Engineering-first technology studio. We build software that matters, with craft that shows.</p>
      <div class="socials">
        <a href="#" class="social-btn">in</a>
        <a href="#" class="social-btn">gh</a>
        <a href="#" class="social-btn">tw</a>
        <a href="#" class="social-btn">yt</a>
      </div>
    </div>
    <div class="footer-col">
      <h5>SERVICES</h5>
      <ul>
        <li><a href="#">Software Dev</a></li>
        <li><a href="#">AI & ML</a></li>
        <li><a href="#">Cloud Arch</a></li>
        <li><a href="#">Mobile Apps</a></li>
        <li><a href="#">Data Eng</a></li>
      </ul>
    </div>
    <div class="footer-col">
      <h5>COMPANY</h5>
      <ul>
        <li><a href="#">About Us</a></li>
        <li><a href="#">Careers</a></li>
        <li><a href="#">Blog</a></li>
        <li><a href="#">Case Studies</a></li>
      </ul>
    </div>
    <div class="footer-col">
      <h5>CONTACT</h5>
      <ul>
        <li><a href="#">hello@rj316labs.com</a></li>
        <li><a href="#">Book a Call</a></li>
        <li><a href="#">Support</a></li>
        <li><a href="#">GitHub</a></li>
      </ul>
    </div>
  </div>
  <div class="footer-bottom">
    <span>© 2024 RJ316 Labs <span class="dot">✦</span> All rights reserved</span>
    <span style="font-family:'JetBrains Mono',monospace; font-size:.65rem; color:var(--muted)">
      &lt;built with precision /&gt;
    </span>
    <span>Privacy <span class="dot">·</span> Terms</span>
  </div>
</footer>

<script>
// ── CURSOR ──
const cursor = document.getElementById('cursor');
const dot = cursor.querySelector('.dot');
const ring = cursor.querySelector('.ring');
let mx = 0, my = 0, rx = 0, ry = 0;
document.addEventListener('mousemove', e => { mx = e.clientX; my = e.clientY; });
function animCursor(){
  dot.style.left = mx+'px'; dot.style.top = my+'px';
  rx += (mx - rx) * .12; ry += (my - ry) * .12;
  ring.style.left = rx+'px'; ring.style.top = ry+'px';
  requestAnimationFrame(animCursor);
}
animCursor();

// ── CIRCUIT CANVAS ──
const canvas = document.getElementById('circuit-canvas');
const ctx = canvas.getContext('2d');
let W, H, nodes = [], lines = [];

function resize(){
  W = canvas.width = window.innerWidth;
  H = canvas.height = window.innerHeight;
  generateCircuit();
}

function generateCircuit(){
  nodes = []; lines = [];
  const cols = Math.ceil(W/80), rows = Math.ceil(H/80);
  for(let i=0;i<cols;i++) for(let j=0;j<rows;j++){
    if(Math.random()>.65) nodes.push({x:i*80+Math.random()*40, y:j*80+Math.random()*40});
  }
  for(let i=0;i<nodes.length;i++){
    const n = nodes[i];
    // find nearby
    nodes.slice(i+1).forEach(m=>{
      const d = Math.hypot(m.x-n.x,m.y-n.y);
      if(d<160 && Math.random()>.5) lines.push([n,m]);
    });
  }
}

let t=0;
function drawCircuit(){
  ctx.clearRect(0,0,W,H);
  t += .003;
  lines.forEach(([a,b],i)=>{
    const alpha = .3 + .2*Math.sin(t+i*.5);
    ctx.strokeStyle = `rgba(30,37,48,${alpha})`;
    ctx.lineWidth = 1;
    ctx.beginPath(); ctx.moveTo(a.x,a.y); ctx.lineTo(b.x,b.y); ctx.stroke();
  });
  nodes.forEach((n,i)=>{
    const alpha = .4 + .3*Math.sin(t*2+i*.7);
    ctx.fillStyle = `rgba(34,136,58,${alpha})`;
    ctx.beginPath(); ctx.arc(n.x,n.y,2,0,Math.PI*2); ctx.fill();
  });
  requestAnimationFrame(drawCircuit);
}
window.addEventListener('resize', resize);
resize(); drawCircuit();

// ── NAV SCROLL ──
window.addEventListener('scroll',()=>{
  document.getElementById('nav').classList.toggle('scrolled', scrollY > 60);
});

// ── REVEAL ──
const observer = new IntersectionObserver(entries=>{
  entries.forEach(e=>{ if(e.isIntersecting) e.target.classList.add('visible'); });
},{threshold:.12});
document.querySelectorAll('.reveal').forEach(el=>observer.observe(el));

// ── COUNTER ANIMATION ──
function animateCounter(el, target, suffix){
  let cur=0, step=Math.ceil(target/60);
  const t = setInterval(()=>{
    cur = Math.min(cur+step, target);
    el.innerHTML = cur + '<span class="unit">'+suffix+'</span>';
    if(cur>=target) clearInterval(t);
  },20);
}
const statObserver = new IntersectionObserver(entries=>{
  entries.forEach(e=>{
    if(e.isIntersecting){
      const boxes = e.target.querySelectorAll('.stat-num');
      boxes[0] && animateCounter(boxes[0], 50, '+');
      boxes[1] && animateCounter(boxes[1], 3, 'x');
      boxes[2] && animateCounter(boxes[2], 99, '%');
      boxes[3] && animateCounter(boxes[3], 24, '/7');
      statObserver.disconnect();
    }
  });
},{threshold:.4});
document.querySelector('.about-stat-grid') && statObserver.observe(document.querySelector('.about-stat-grid'));
</script>
</body>
</html>
