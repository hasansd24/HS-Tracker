# HS-Tracker
Health Tracker
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="theme-color" content="#0a1530">
<title>HS Tracker</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }

  :root {
    --text: rgba(255, 255, 255, 0.98);
    --text-dim: rgba(255, 255, 255, 0.72);
    --text-faint: rgba(255, 255, 255, 0.5);

    --glass-bg: rgba(255, 255, 255, 0.1);
    --glass-bg-strong: rgba(255, 255, 255, 0.16);
    --glass-border: rgba(255, 255, 255, 0.22);
    --glass-border-strong: rgba(255, 255, 255, 0.32);
    --glass-highlight: rgba(255, 255, 255, 0.5);

    --accent: #66e1ff;
    --accent-glow: rgba(102, 225, 255, 0.55);
    --warn: #ffd166;
    --danger: #ff7a90;
    --info: #87b8ff;
    --good: #7df5b3;

    --font: -apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", "Helvetica Neue", Helvetica, Arial, sans-serif;
    --font-rounded: ui-rounded, -apple-system, BlinkMacSystemFont, "SF Pro Rounded", "SF Pro Display", "Helvetica Neue", sans-serif;

    --bg-gradient:
      radial-gradient(ellipse 80% 60% at 20% 0%, #4a7dff 0%, transparent 55%),
      radial-gradient(ellipse 70% 60% at 100% 30%, #1a4dd6 0%, transparent 55%),
      radial-gradient(ellipse 80% 60% at 0% 70%, #2cb5ff 0%, transparent 55%),
      radial-gradient(ellipse 70% 60% at 90% 100%, #0c3fa8 0%, transparent 55%),
      linear-gradient(180deg, #0a1530 0%, #061029 100%);
  }

  html, body {
    height: 100%;
    font-family: var(--font);
    font-weight: 400;
    color: var(--text);
    overscroll-behavior: none;
    -webkit-font-smoothing: antialiased;
    letter-spacing: -0.01em;
  }

  body {
    background: #0a1530;
    overflow-x: hidden;
    position: relative;
    min-height: 100vh;
    min-height: 100dvh;
  }

  /* Themed gradient backdrop */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background: var(--bg-gradient);
    z-index: -2;
    animation: drift 30s ease-in-out infinite alternate;
  }

  body::after {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/%3E%3CfeColorMatrix values='0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0.35 0'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
    opacity: 0.4;
    mix-blend-mode: overlay;
    pointer-events: none;
    z-index: -1;
  }

  @keyframes drift {
    0%   { transform: translate(0, 0) scale(1); }
    50%  { transform: translate(-3%, 2%) scale(1.05); }
    100% { transform: translate(2%, -2%) scale(1.03); }
  }

  .app {
    max-width: 480px;
    margin: 0 auto;
    min-height: 100vh;
    min-height: 100dvh;
    padding: 0 16px 100px;
    position: relative;
  }

  /* ============ LIQUID GLASS PRIMITIVE ============ */
  .glass {
    position: relative;
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.18) 0%, rgba(255, 255, 255, 0.04) 50%, rgba(255, 255, 255, 0.12) 100%),
      var(--glass-bg);
    backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    -webkit-backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    border: 1px solid var(--glass-border);
    border-radius: 24px;
    overflow: hidden;
    box-shadow:
      inset 0 1.5px 1px 0 var(--glass-highlight),
      inset 0 -1px 0 0 rgba(255, 255, 255, 0.08),
      inset 1px 0 0 0 rgba(255, 255, 255, 0.06),
      inset -1px 0 0 0 rgba(255, 255, 255, 0.06),
      0 12px 40px rgba(0, 8, 28, 0.25),
      0 1px 2px rgba(0, 0, 0, 0.1);
  }

  /* Top-edge specular highlight (the "lensed light" feel) */
  .glass::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 50%;
    background: linear-gradient(180deg, rgba(255, 255, 255, 0.16) 0%, rgba(255, 255, 255, 0.03) 60%, transparent 100%);
    pointer-events: none;
    border-radius: inherit;
    mix-blend-mode: screen;
    opacity: 0.9;
  }

  /* Diagonal sweep highlight (Liquid Glass refraction) */
  .glass::after {
    content: '';
    position: absolute;
    inset: 0;
    background:
      linear-gradient(115deg, transparent 30%, rgba(255, 255, 255, 0.08) 45%, rgba(255, 255, 255, 0.14) 50%, rgba(255, 255, 255, 0.08) 55%, transparent 70%);
    pointer-events: none;
    border-radius: inherit;
    opacity: 0.6;
    mix-blend-mode: overlay;
  }

  .glass-strong {
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.24) 0%, rgba(255, 255, 255, 0.06) 50%, rgba(255, 255, 255, 0.16) 100%),
      var(--glass-bg-strong);
    border-color: var(--glass-border-strong);
  }

  /* Ensure direct children of glass sit above the lensing pseudo-elements */
  .glass > *:not(.glass) {
    position: relative;
    z-index: 1;
  }

  /* ============ HEADER ============ */
  .header {
    position: sticky;
    top: 0;
    z-index: 50;
    padding: 16px 4px 12px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 12px;
    transition: transform 0.35s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s ease;
    will-change: transform;
  }

  .header.hidden {
    transform: translateY(-110%);
    opacity: 0;
    pointer-events: none;
  }

  .logo {
    font-family: var(--font-rounded);
    font-size: 22px;
    font-weight: 700;
    letter-spacing: -0.02em;
    line-height: 1;
    text-shadow: 0 0 16px rgba(102, 225, 255, 0.4);
  }
  .logo .accent { color: var(--accent); }

  .streak-pill {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 8px 14px;
    border-radius: 100px;
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.18) 0%, rgba(255, 255, 255, 0.04) 50%, rgba(255, 255, 255, 0.12) 100%),
      var(--glass-bg);
    backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    -webkit-backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    border: 1px solid var(--glass-border);
    box-shadow:
      inset 0 1.5px 1px var(--glass-highlight),
      inset 0 -1px 0 rgba(255,255,255,0.08),
      0 4px 20px rgba(0, 8, 28, 0.2);
    font-size: 13px;
    font-weight: 500;
    flex-shrink: 0;
    position: relative;
    overflow: hidden;
  }
  .streak-pill::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 50%;
    background: linear-gradient(180deg, rgba(255,255,255,0.18) 0%, transparent 100%);
    pointer-events: none;
    mix-blend-mode: screen;
  }
  .streak-pill > * { position: relative; z-index: 1; }

  .streak-pill .num {
    color: var(--accent);
    font-weight: 700;
    text-shadow: 0 0 8px var(--accent-glow);
  }

  .pulse-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--accent);
    box-shadow: 0 0 10px var(--accent);
    animation: pulse 2s ease-in-out infinite;
    flex-shrink: 0;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.5; transform: scale(0.85); }
  }

  /* ============ DAY NAV ============ */
  .day-nav {
    display: grid;
    grid-template-columns: 44px 1fr 44px;
    align-items: center;
    gap: 12px;
    padding: 8px 4px 20px;
  }

  .nav-btn {
    width: 44px; height: 44px;
    border-radius: 14px;
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.18) 0%, rgba(255, 255, 255, 0.04) 50%, rgba(255, 255, 255, 0.12) 100%),
      var(--glass-bg);
    backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    -webkit-backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    border: 1px solid var(--glass-border);
    box-shadow:
      inset 0 1.5px 1px var(--glass-highlight),
      inset 0 -1px 0 rgba(255,255,255,0.08),
      0 4px 20px rgba(0, 8, 28, 0.2);
    color: var(--text);
    font-size: 22px;
    font-family: var(--font);
    font-weight: 400;
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    transition: transform 0.2s, background 0.2s;
    line-height: 1;
    position: relative;
    overflow: hidden;
  }
  .nav-btn::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 50%;
    background: linear-gradient(180deg, rgba(255,255,255,0.18) 0%, transparent 100%);
    pointer-events: none;
    border-radius: inherit;
    mix-blend-mode: screen;
  }
  .nav-btn > * { position: relative; z-index: 1; }

  .nav-btn:active { transform: scale(0.92); background: var(--glass-bg-strong); }

  .day-label-wrap {
    text-align: center;
    min-width: 0;
  }

  .day-label {
    font-size: 22px;
    font-weight: 600;
    letter-spacing: -0.02em;
    line-height: 1.15;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .day-sub {
    font-size: 11px;
    font-weight: 600;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.12em;
    margin-top: 4px;
  }

  /* ============ TABS ============ */
  .tabs {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 6px;
    padding: 4px;
    margin-bottom: 20px;
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.16) 0%, rgba(255, 255, 255, 0.04) 50%, rgba(255, 255, 255, 0.1) 100%),
      var(--glass-bg);
    backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    -webkit-backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    border: 1px solid var(--glass-border);
    border-radius: 16px;
    box-shadow:
      inset 0 1.5px 1px var(--glass-highlight),
      inset 0 -1px 0 rgba(255,255,255,0.06),
      0 4px 20px rgba(0, 8, 28, 0.15);
    position: relative;
    overflow: hidden;
  }
  .tabs::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 50%;
    background: linear-gradient(180deg, rgba(255,255,255,0.14) 0%, transparent 100%);
    pointer-events: none;
    mix-blend-mode: screen;
  }
  .tabs > * { position: relative; z-index: 1; }

  .tab {
    padding: 10px 4px;
    background: transparent;
    border: none;
    color: var(--text-dim);
    font-family: var(--font);
    font-weight: 600;
    font-size: 13px;
    cursor: pointer;
    border-radius: 12px;
    transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
    text-align: center;
    line-height: 1.2;
    letter-spacing: -0.01em;
  }

  .tab.active {
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.28) 0%, rgba(255, 255, 255, 0.08) 50%, rgba(255, 255, 255, 0.18) 100%),
      var(--glass-bg-strong);
    color: var(--text);
    box-shadow:
      inset 0 1.5px 1px var(--glass-highlight),
      inset 0 -1px 0 rgba(255,255,255,0.08),
      0 2px 8px rgba(0, 0, 0, 0.15);
  }

  /* ============ SECTIONS ============ */
  .section { margin-bottom: 16px; }

  .section-label {
    font-size: 12px;
    font-weight: 600;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    padding: 0 4px;
    margin-bottom: 10px;
  }

  /* ============ HERO WEIGHT ============ */
  .hero {
    padding: 22px;
    margin-bottom: 12px;
  }

  .hero-row {
    display: grid;
    grid-template-columns: 1fr auto;
    gap: 16px;
    align-items: end;
    position: relative;
    z-index: 1;
  }

  .hero-label {
    font-size: 12px;
    font-weight: 600;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin-bottom: 8px;
    line-height: 1;
  }

  .hero-val {
    font-size: 48px;
    font-weight: 700;
    line-height: 1;
    letter-spacing: -0.03em;
    font-feature-settings: "tnum";
  }

  .hero-val .unit {
    font-size: 18px;
    font-weight: 500;
    color: var(--text-dim);
    margin-left: 4px;
    letter-spacing: -0.01em;
  }

  .hero-side {
    text-align: right;
    line-height: 1.3;
  }

  .delta {
    font-size: 15px;
    font-weight: 600;
    color: var(--good);
    text-shadow: 0 0 12px rgba(125, 245, 179, 0.4);
    line-height: 1;
    font-feature-settings: "tnum";
  }
  .delta.up { color: var(--warn); text-shadow: 0 0 12px rgba(255, 209, 102, 0.4); }

  .delta-sub {
    font-size: 12px;
    font-weight: 500;
    color: var(--text-faint);
    margin-top: 4px;
    line-height: 1.2;
  }

  /* ============ CALORIE RING ============ */
  .ring-card {
    padding: 20px;
    margin-bottom: 12px;
    display: grid;
    grid-template-columns: 110px 1fr;
    gap: 20px;
    align-items: center;
  }

  .ring-svg {
    width: 110px; height: 110px;
    filter: drop-shadow(0 0 8px rgba(102, 225, 255, 0.35));
  }

  .ring-track { fill: none; stroke: rgba(255, 255, 255, 0.12); stroke-width: 8; }
  .ring-fill {
    fill: none;
    stroke: var(--accent);
    stroke-width: 8;
    stroke-linecap: round;
    transform: rotate(-90deg);
    transform-origin: center;
    transition: stroke-dasharray 0.8s cubic-bezier(0.4, 0, 0.2, 1);
  }

  .ring-info { min-width: 0; }
  .ring-info .label {
    font-size: 12px;
    font-weight: 600;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin-bottom: 6px;
    line-height: 1;
  }
  .ring-info .big {
    font-size: 38px;
    font-weight: 700;
    line-height: 1;
    letter-spacing: -0.03em;
    font-feature-settings: "tnum";
  }
  .ring-info .target-line {
    font-size: 13px;
    font-weight: 500;
    color: var(--text-faint);
    margin-top: 6px;
    line-height: 1;
  }
  .ring-info .left {
    font-size: 13px;
    font-weight: 600;
    color: var(--accent);
    margin-top: 6px;
    line-height: 1;
  }

  /* ============ STAT GRID ============ */
  .stat-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
  }

  .stat-card {
    padding: 14px;
    cursor: pointer;
    transition: transform 0.2s;
    display: flex;
    flex-direction: column;
    min-height: 120px;
  }
  .stat-card:active { transform: scale(0.98); }

  .stat-label {
    font-size: 12px;
    font-weight: 600;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.08em;
    margin-bottom: 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 8px;
    line-height: 1;
    min-height: 18px;
  }

  .stat-val {
    font-size: 28px;
    font-weight: 700;
    line-height: 1;
    letter-spacing: -0.02em;
    font-feature-settings: "tnum";
  }
  .stat-val .unit {
    font-size: 13px;
    font-weight: 500;
    color: var(--text-faint);
    margin-left: 3px;
    letter-spacing: -0.01em;
  }

  .stat-target {
    font-size: 12px;
    font-weight: 500;
    color: var(--text-faint);
    margin-top: auto;
    padding-top: 8px;
    line-height: 1.2;
  }

  .macro-bar {
    height: 4px;
    background: rgba(255, 255, 255, 0.12);
    border-radius: 2px;
    overflow: hidden;
    margin-top: 10px;
  }
  .macro-bar > div {
    height: 100%;
    border-radius: 2px;
    transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1);
  }

  /* ============ PILL ============ */
  .pill {
    font-size: 10px;
    font-weight: 700;
    padding: 3px 7px;
    border-radius: 6px;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    line-height: 1;
    flex-shrink: 0;
  }
  .pill.low { background: rgba(255, 122, 144, 0.22); color: var(--danger); border: 1px solid rgba(255, 122, 144, 0.35); }
  .pill.ok { background: rgba(255, 209, 102, 0.22); color: var(--warn); border: 1px solid rgba(255, 209, 102, 0.35); }
  .pill.good { background: rgba(125, 245, 179, 0.22); color: var(--good); border: 1px solid rgba(125, 245, 179, 0.4); }

  /* ============ MEALS ============ */
  .meal-list { overflow: hidden; }
  .meal-row {
    padding: 14px 16px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.1);
    display: grid;
    grid-template-columns: 1fr auto auto;
    align-items: center;
    gap: 12px;
    position: relative;
    z-index: 1;
  }
  .meal-row:last-child { border-bottom: none; }

  .meal-info { min-width: 0; }
  .meal-info .name {
    font-size: 15px;
    font-weight: 600;
    margin-bottom: 4px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    line-height: 1.2;
    letter-spacing: -0.01em;
  }
  .meal-info .macros {
    font-size: 12px;
    font-weight: 500;
    color: var(--text-dim);
    line-height: 1;
    font-feature-settings: "tnum";
  }

  .meal-cal {
    font-size: 14px;
    font-weight: 600;
    color: var(--accent);
    text-align: right;
    line-height: 1;
    font-feature-settings: "tnum";
  }

  .meal-del {
    width: 30px; height: 30px;
    border-radius: 9px;
    background: rgba(255, 255, 255, 0.08);
    border: 1px solid rgba(255, 255, 255, 0.14);
    color: var(--text-faint);
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    font-family: var(--font);
    font-weight: 400;
    font-size: 18px;
    line-height: 1;
    padding: 0;
    transition: all 0.2s;
  }
  .meal-del:active {
    background: var(--danger);
    color: white;
    border-color: var(--danger);
  }

  .empty-state {
    padding: 28px 16px;
    text-align: center;
    color: var(--text-faint);
    font-size: 14px;
    font-weight: 500;
  }

  /* ============ BUTTONS ============ */
  .btn {
    background: var(--accent);
    color: #0a1530;
    border: none;
    border-radius: 16px;
    padding: 14px 20px;
    font-family: var(--font);
    font-weight: 600;
    font-size: 15px;
    cursor: pointer;
    width: 100%;
    transition: transform 0.15s;
    display: flex; align-items: center; justify-content: center; gap: 8px;
    box-shadow: 0 4px 20px rgba(102, 225, 255, 0.35), inset 0 1px 0 rgba(255, 255, 255, 0.5);
    line-height: 1.2;
    letter-spacing: -0.01em;
  }
  .btn:active { transform: scale(0.97); }

  .btn-glass {
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.18) 0%, rgba(255, 255, 255, 0.04) 50%, rgba(255, 255, 255, 0.12) 100%),
      var(--glass-bg);
    color: var(--text);
    backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    -webkit-backdrop-filter: blur(40px) saturate(200%) brightness(1.1);
    border: 1px solid var(--glass-border);
    box-shadow:
      inset 0 1.5px 1px var(--glass-highlight),
      inset 0 -1px 0 rgba(255,255,255,0.08),
      0 4px 16px rgba(0, 8, 28, 0.15);
    position: relative;
    overflow: hidden;
  }
  .btn-glass::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 50%;
    background: linear-gradient(180deg, rgba(255,255,255,0.18) 0%, transparent 100%);
    pointer-events: none;
    border-radius: inherit;
    mix-blend-mode: screen;
  }
  .btn-glass > * { position: relative; z-index: 1; }
  .btn-glass:active { background: var(--glass-bg-strong); }

  .btn-ghost {
    background: transparent;
    color: var(--text);
    border: 1px dashed var(--glass-border-strong);
    box-shadow: none;
  }
  .btn-ghost:active { background: var(--glass-bg); }

  /* ============ MODAL ============ */
  .modal-bg {
    position: fixed;
    inset: 0;
    background: rgba(5, 12, 30, 0.5);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    z-index: 100;
    display: none;
    align-items: flex-end;
    justify-content: center;
  }
  .modal-bg.show { display: flex; }

  .modal {
    width: 100%;
    max-width: 480px;
    background:
      linear-gradient(180deg, rgba(255, 255, 255, 0.12) 0%, rgba(255, 255, 255, 0.04) 100%),
      rgba(15, 28, 60, 0.75);
    backdrop-filter: blur(60px) saturate(220%) brightness(1.05);
    -webkit-backdrop-filter: blur(60px) saturate(220%) brightness(1.05);
    border-top: 1px solid var(--glass-border-strong);
    border-radius: 28px 28px 0 0;
    padding: 16px 20px 32px;
    max-height: 90vh;
    overflow-y: auto;
    box-shadow:
      0 -10px 50px rgba(0, 0, 0, 0.5),
      inset 0 1.5px 1px var(--glass-highlight);
    animation: slideUp 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    position: relative;
  }
  .modal::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 80px;
    background: linear-gradient(180deg, rgba(255,255,255,0.12) 0%, transparent 100%);
    pointer-events: none;
    border-radius: 28px 28px 0 0;
    mix-blend-mode: screen;
  }
  .modal > * { position: relative; z-index: 1; }
  @keyframes slideUp {
    from { transform: translateY(100%); }
    to { transform: translateY(0); }
  }

  .handle {
    width: 36px; height: 4px;
    background: rgba(255, 255, 255, 0.3);
    border-radius: 2px;
    margin: 0 auto 16px;
  }

  .modal h3 {
    font-size: 22px;
    font-weight: 700;
    margin-bottom: 6px;
    line-height: 1.15;
    letter-spacing: -0.02em;
  }
  .modal-sub {
    font-size: 12px;
    font-weight: 600;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin-bottom: 18px;
  }

  /* ============ FORM ============ */
  .field { margin-bottom: 12px; }
  .field label {
    display: block;
    font-size: 11px;
    font-weight: 600;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.08em;
    margin-bottom: 6px;
  }

  .input {
    width: 100%;
    background:
      linear-gradient(180deg, rgba(255, 255, 255, 0.04) 0%, rgba(255, 255, 255, 0.08) 100%),
      rgba(255, 255, 255, 0.06);
    border: 1px solid rgba(255, 255, 255, 0.16);
    border-radius: 14px;
    padding: 13px 16px;
    color: var(--text);
    font-family: var(--font);
    font-weight: 500;
    font-size: 16px;
    transition: all 0.2s;
    box-shadow:
      inset 0 1px 1px rgba(255,255,255,0.06),
      inset 0 -1px 0 rgba(0,0,0,0.05);
  }
  .input::placeholder { color: var(--text-faint); }
  .input:focus {
    outline: none;
    border-color: var(--accent);
    background: rgba(255, 255, 255, 0.12);
    box-shadow: 0 0 0 3px rgba(102, 225, 255, 0.2);
  }

  .input-grid-4 {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    gap: 8px;
  }

  .form-actions {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
    margin-top: 20px;
  }

  /* ============ CHART ============ */
  .chart-card { padding: 20px; }
  .chart-header {
    display: flex; justify-content: space-between; align-items: baseline;
    margin-bottom: 16px;
    position: relative; z-index: 1;
    gap: 12px;
  }
  .chart-title {
    font-size: 22px;
    font-weight: 700;
    line-height: 1;
    letter-spacing: -0.02em;
  }
  .chart-meta {
    font-size: 12px;
    font-weight: 500;
    color: var(--text-dim);
    line-height: 1;
  }

  .chart-svg {
    width: 100%;
    height: 180px;
    overflow: visible;
    position: relative;
    z-index: 1;
  }

  /* ============ SUPPS ============ */
  .supp-card {
    padding: 14px 16px;
    margin-bottom: 8px;
    display: grid;
    grid-template-columns: 1fr 38px;
    align-items: center;
    gap: 12px;
  }
  .supp-info { min-width: 0; position: relative; z-index: 1; }
  .supp-name {
    font-size: 15px;
    font-weight: 600;
    margin-bottom: 2px;
    line-height: 1.2;
    letter-spacing: -0.01em;
  }
  .supp-detail {
    font-size: 12px;
    font-weight: 500;
    color: var(--text-dim);
    line-height: 1.3;
  }
  .supp-toggle {
    width: 38px; height: 38px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.08);
    border: 1px solid rgba(255, 255, 255, 0.18);
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    padding: 0;
    transition: all 0.2s;
    position: relative; z-index: 1;
  }
  .supp-toggle.taken {
    background: var(--accent);
    border-color: var(--accent);
    box-shadow: 0 0 16px var(--accent-glow);
  }
  .supp-toggle svg { width: 16px; height: 16px; display: block; }
  .supp-toggle.taken svg { color: #0a1530; }

  /* ============ INSIGHTS ============ */
  .insight {
    padding: 14px 16px;
    margin-bottom: 8px;
    border-left: 3px solid var(--accent);
    position: relative;
  }
  .insight.warn { border-left-color: var(--warn); }
  .insight.danger { border-left-color: var(--danger); }
  .insight.info { border-left-color: var(--info); }
  .insight.good { border-left-color: var(--good); }

  .insight-content { position: relative; z-index: 1; }
  .insight-title {
    font-size: 14px;
    font-weight: 600;
    margin-bottom: 6px;
    line-height: 1.3;
    letter-spacing: -0.01em;
  }
  .insight-body {
    font-size: 13px;
    font-weight: 400;
    line-height: 1.5;
    color: var(--text-dim);
  }

  /* ============ BOTTOM TAB BAR ============ */
  .tab-bar {
    position: fixed;
    bottom: 12px;
    left: 16px; right: 16px;
    max-width: 448px;
    margin: 0 auto;
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.18) 0%, rgba(255, 255, 255, 0.04) 50%, rgba(255, 255, 255, 0.12) 100%),
      rgba(10, 22, 50, 0.45);
    backdrop-filter: blur(50px) saturate(220%) brightness(1.1);
    -webkit-backdrop-filter: blur(50px) saturate(220%) brightness(1.1);
    border: 1px solid var(--glass-border-strong);
    border-radius: 24px;
    padding: 6px;
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 4px;
    z-index: 40;
    box-shadow:
      inset 0 1.5px 1px var(--glass-highlight),
      inset 0 -1px 0 rgba(255,255,255,0.08),
      0 12px 40px rgba(0, 8, 28, 0.4);
    overflow: hidden;
  }
  .tab-bar::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 50%;
    background: linear-gradient(180deg, rgba(255,255,255,0.14) 0%, transparent 100%);
    pointer-events: none;
    border-radius: inherit;
    mix-blend-mode: screen;
  }
  .tab-bar > * { position: relative; z-index: 1; }

  .tab-bar-item {
    padding: 10px 4px 8px;
    background: transparent;
    border: none;
    color: var(--text-faint);
    cursor: pointer;
    border-radius: 16px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    font-family: var(--font);
    font-weight: 500;
    font-size: 10px;
    letter-spacing: 0.01em;
    transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
    line-height: 1;
  }
  .tab-bar-item.active {
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.28) 0%, rgba(255, 255, 255, 0.08) 50%, rgba(255, 255, 255, 0.18) 100%),
      var(--glass-bg-strong);
    color: var(--text);
    box-shadow:
      inset 0 1.5px 1px var(--glass-highlight),
      inset 0 -1px 0 rgba(255,255,255,0.08);
  }
  .tab-bar-item svg {
    width: 22px; height: 22px;
    display: block;
  }
  .tab-bar-item.active svg {
    color: var(--accent);
    filter: drop-shadow(0 0 4px var(--accent-glow));
  }

  /* ============ THEME PICKER ============ */
  .theme-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    position: relative;
    z-index: 1;
  }

  /* ============ RANGE BUTTONS ============ */
  .range-btn {
    padding: 10px 6px;
    background: rgba(255,255,255,0.06);
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 12px;
    color: var(--text-dim);
    font-family: var(--font);
    font-weight: 600;
    font-size: 13px;
    cursor: pointer;
    transition: all 0.2s;
    line-height: 1;
    letter-spacing: -0.01em;
  }
  .range-btn:active { transform: scale(0.95); }
  .range-btn.active {
    background:
      linear-gradient(135deg, rgba(255, 255, 255, 0.24) 0%, rgba(255, 255, 255, 0.06) 50%, rgba(255, 255, 255, 0.16) 100%),
      var(--glass-bg-strong);
    color: var(--text);
    border-color: var(--glass-border-strong);
    box-shadow: inset 0 1px 0 var(--glass-highlight);
  }

  .theme-swatch {
    aspect-ratio: 1;
    border-radius: 14px;
    border: 2px solid transparent;
    cursor: pointer;
    overflow: hidden;
    position: relative;
    padding: 0;
    transition: all 0.2s;
    box-shadow: inset 0 1px 0 rgba(255,255,255,0.2);
  }
  .theme-swatch:active { transform: scale(0.95); }
  .theme-swatch.active {
    border-color: rgba(255,255,255,0.8);
    box-shadow: 0 0 0 3px rgba(255,255,255,0.15);
  }
  .theme-swatch .label {
    position: absolute;
    bottom: 6px;
    left: 0; right: 0;
    text-align: center;
    color: white;
    font-family: var(--font);
    font-weight: 600;
    font-size: 11px;
    letter-spacing: -0.01em;
    text-shadow: 0 1px 3px rgba(0,0,0,0.6);
  }

  /* ============ PHOTO BUTTON ============ */
  .photo-btn {
    background: var(--glass-bg);
    color: var(--text);
    backdrop-filter: blur(20px) saturate(180%);
    -webkit-backdrop-filter: blur(20px) saturate(180%);
    border: 1px dashed var(--glass-border-strong);
    border-radius: 16px;
    padding: 14px 20px;
    font-family: var(--font);
    font-weight: 600;
    font-size: 15px;
    cursor: pointer;
    width: 100%;
    transition: transform 0.15s;
    display: flex; align-items: center; justify-content: center; gap: 10px;
    box-shadow: inset 0 1px 0 var(--glass-highlight);
    line-height: 1.2;
    margin-top: 8px;
    letter-spacing: -0.01em;
  }
  .photo-btn:active { transform: scale(0.98); background: var(--glass-bg-strong); }
  .photo-btn svg { width: 20px; height: 20px; flex-shrink: 0; }

  .photo-preview {
    width: 100%;
    border-radius: 14px;
    margin-bottom: 14px;
    max-height: 280px;
    object-fit: cover;
  }

  .ai-status {
    padding: 12px 14px;
    border-radius: 12px;
    background: rgba(102, 225, 255, 0.1);
    border: 1px solid rgba(102, 225, 255, 0.25);
    color: var(--accent);
    font-size: 12px;
    text-align: center;
    margin-bottom: 12px;
    line-height: 1.4;
  }
  .ai-status.error {
    background: rgba(255, 122, 144, 0.1);
    border-color: rgba(255, 122, 144, 0.3);
    color: var(--danger);
  }
  .ai-status.success {
    background: rgba(125, 245, 179, 0.1);
    border-color: rgba(125, 245, 179, 0.3);
    color: var(--good);
  }

  .spinner {
    display: inline-block;
    width: 14px; height: 14px;
    border: 2px solid currentColor;
    border-top-color: transparent;
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
    vertical-align: middle;
    margin-right: 6px;
  }
  @keyframes spin { to { transform: rotate(360deg); } }

  /* ============ NOTE BAR ============ */
  .note-bar {
    padding: 12px 14px;
    margin-bottom: 16px;
    font-size: 12px;
    line-height: 1.55;
    color: var(--text-dim);
    border-left: 3px solid var(--info);
  }
  .note-bar strong { color: var(--info); }

  ::-webkit-scrollbar { width: 0; }
</style>
</head>
<body>

<div class="app">

  <!-- Header -->
  <header class="header">
    <div class="logo">HS<span class="accent"> </span>Tracker</div>
    <div class="streak-pill">
      <span class="pulse-dot"></span>
      <span><span class="num" id="streakNum">0</span> nicotine-free</span>
    </div>
  </header>

  <!-- Day nav -->
  <div class="day-nav">
    <button class="nav-btn" id="prevDay" aria-label="Previous">‹</button>
    <div class="day-label-wrap">
      <div class="day-label" id="dayLabel">—</div>
      <div class="day-sub" id="daySub">—</div>
    </div>
    <button class="nav-btn" id="nextDay" aria-label="Next">›</button>
  </div>

  <!-- Tabs -->
  <div class="tabs">
    <button class="tab active" data-tab="today">Today</button>
    <button class="tab" data-tab="trends">Trends</button>
    <button class="tab" data-tab="supps">Supps</button>
    <button class="tab" data-tab="insights">Insights</button>
    <button class="tab" data-tab="settings">Settings</button>
  </div>

  <!-- TODAY -->
  <div id="tab-today">

    <div class="section">
      <div class="glass glass-strong hero" id="heroWeight">
        <div class="hero-row">
          <div>
            <div class="hero-label">Weight</div>
            <div class="hero-val" id="weightVal">—<span class="unit">kg</span></div>
          </div>
          <div class="hero-side">
            <div class="delta" id="weightDelta">—</div>
            <div class="delta-sub" id="weightTotal">—</div>
          </div>
        </div>
      </div>

      <div class="glass ring-card">
        <svg class="ring-svg" viewBox="0 0 110 110">
          <circle class="ring-track" cx="55" cy="55" r="46"/>
          <circle class="ring-fill" id="ringFill" cx="55" cy="55" r="46" stroke-dasharray="0 289"/>
        </svg>
        <div class="ring-info">
          <div class="label">Calories</div>
          <div class="big"><span id="calVal">0</span></div>
          <div class="target-line">/ <span id="calTarget">1800</span> kcal</div>
          <div class="left" id="calLeft">— left</div>
        </div>
      </div>

      <div class="stat-grid">
        <div class="glass stat-card">
          <div class="stat-label"><span>Protein</span><span class="pill" id="proteinPill">—</span></div>
          <div class="stat-val"><span id="proteinVal">0</span><span class="unit">g</span></div>
          <div class="macro-bar"><div id="proteinBar" style="width: 0%; background: var(--danger);"></div></div>
          <div class="stat-target">/ <span id="proteinTarget">180</span>g target</div>
        </div>
        <div class="glass stat-card">
          <div class="stat-label"><span>Carbs</span><span class="pill" id="carbsPill">—</span></div>
          <div class="stat-val"><span id="carbsVal">0</span><span class="unit">g</span></div>
          <div class="macro-bar"><div id="carbsBar" style="width: 0%; background: var(--info);"></div></div>
          <div class="stat-target">/ <span id="carbsTarget">180</span>g target</div>
        </div>
        <div class="glass stat-card">
          <div class="stat-label"><span>Fat</span><span class="pill" id="fatPill">—</span></div>
          <div class="stat-val"><span id="fatVal">0</span><span class="unit">g</span></div>
          <div class="macro-bar"><div id="fatBar" style="width: 0%; background: var(--warn);"></div></div>
          <div class="stat-target">/ <span id="fatTarget">60</span>g target</div>
        </div>
        <div class="glass stat-card">
          <div class="stat-label"><span>Net</span></div>
          <div class="stat-val"><span id="netVal">0</span><span class="unit">kcal</span></div>
          <div class="stat-target" id="netDetail">After training</div>
        </div>
      </div>
    </div>

    <div class="section">
      <div class="section-label">Meals</div>
      <div class="glass meal-list" id="mealList"></div>
      <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-top: 12px;">
        <button class="btn btn-ghost" onclick="openMealModal()">+ Manual</button>
        <button class="photo-btn" onclick="openPhotoModal()">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M23 19a2 2 0 01-2 2H3a2 2 0 01-2-2V8a2 2 0 012-2h4l2-3h6l2 3h4a2 2 0 012 2z"/><circle cx="12" cy="13" r="4"/></svg>
          Photo
        </button>
      </div>
    </div>

    <div class="section">
      <div class="section-label">Daily checks</div>
      <div class="stat-grid">
        <div class="glass stat-card" onclick="openCheckModal('sleep')">
          <div class="stat-label"><span>Sleep</span></div>
          <div class="stat-val"><span id="sleepVal">—</span><span class="unit" id="sleepUnit"></span></div>
          <div class="stat-target">Target 7+ hrs</div>
        </div>
        <div class="glass stat-card" onclick="openCheckModal('water')">
          <div class="stat-label"><span>Water</span></div>
          <div class="stat-val"><span id="waterVal">—</span><span class="unit" id="waterUnit"></span></div>
          <div class="stat-target">Target 3.5+ L</div>
        </div>
        <div class="glass stat-card" onclick="openCheckModal('cravings')">
          <div class="stat-label"><span>Cravings</span></div>
          <div class="stat-val"><span id="cravingsVal">—</span><span class="unit" id="cravingsUnit"></span></div>
          <div class="stat-target">0–10 scale</div>
        </div>
        <div class="glass stat-card" onclick="openCheckModal('training')">
          <div class="stat-label"><span>Training</span></div>
          <div class="stat-val"><span id="trainingVal">—</span><span class="unit" id="trainingUnit"></span></div>
          <div class="stat-target" id="trainingTypeLabel">Tap to log</div>
        </div>
      </div>
    </div>

  </div>

  <!-- TRENDS -->
  <div id="tab-trends" style="display: none;">
    <div class="section">
      <div class="glass chart-card" style="margin-bottom: 12px;">
        <div class="chart-header">
          <div class="chart-title">Weight</div>
          <div class="chart-meta" id="trendMeta">—</div>
        </div>
        <svg class="chart-svg" id="weightChart" viewBox="0 0 400 180" preserveAspectRatio="none"></svg>
      </div>

      <div class="stat-grid">
        <div class="glass stat-card">
          <div class="stat-label"><span>Days logged</span></div>
          <div class="stat-val" id="totalDays">0</div>
          <div class="stat-target">Since start</div>
        </div>
        <div class="glass stat-card">
          <div class="stat-label"><span>Total lost</span></div>
          <div class="stat-val"><span id="totalLost">0</span><span class="unit">kg</span></div>
          <div class="stat-target">From 114.5 kg</div>
        </div>
        <div class="glass stat-card">
          <div class="stat-label"><span>Weekly avg</span></div>
          <div class="stat-val"><span id="avgWeekly">0</span><span class="unit">kg/wk</span></div>
          <div class="stat-target">Loss rate</div>
        </div>
        <div class="glass stat-card">
          <div class="stat-label"><span>Avg protein</span></div>
          <div class="stat-val"><span id="avgProtein">0</span><span class="unit">g</span></div>
          <div class="stat-target">Last 7 days</div>
        </div>
      </div>
    </div>
  </div>

  <!-- SUPPS -->
  <div id="tab-supps" style="display: none;">
    <div class="section">
      <div class="glass note-bar">
        <strong>Note —</strong> discuss this stack with your urologist or andrologist before starting, especially given your post-surgery status and ongoing fertility concerns.
      </div>
      <div class="section-label">Today's stack</div>
      <div id="suppList"></div>
    </div>
  </div>

  <!-- INSIGHTS -->
  <div id="tab-insights" style="display: none;">
    <div class="section">
      <div class="section-label">Today's read</div>
      <div id="insightList"></div>
    </div>
  </div>

  <!-- SETTINGS -->
  <div id="tab-settings" style="display: none;">

    <div class="section">
      <div class="section-label">AI photo recognition</div>
      <div class="glass" style="padding: 16px; margin-bottom: 12px;">
        <div style="font-size: 13px; line-height: 1.6; color: var(--text-dim); margin-bottom: 12px; position: relative; z-index: 1;">
          Snap a photo of any meal and the AI will estimate calories and macros. Uses Google Gemini's free tier — 1,500 photos per day at no cost.
        </div>
        <div style="font-size: 12px; line-height: 1.7; color: var(--text-faint); margin-bottom: 16px; position: relative; z-index: 1;">
          <strong style="color: var(--accent);">How to set up:</strong><br>
          1. Open <span style="color: var(--accent);">aistudio.google.com/apikey</span> in your browser<br>
          2. Sign in with Google (free account)<br>
          3. Tap "Create API key"<br>
          4. Copy the key, paste below, tap Save<br>
          5. The key stays only on your phone
        </div>
        <div class="field" style="position: relative; z-index: 1;">
          <label>Gemini API key</label>
          <input class="input" id="apiKeyInput" type="password" placeholder="Paste your key here">
        </div>
        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 8px; position: relative; z-index: 1;">
          <button class="btn btn-glass" onclick="clearApiKey()">Clear</button>
          <button class="btn" onclick="saveApiKey()">Save key</button>
        </div>
        <div id="apiKeyStatus" style="font-size: 12px; margin-top: 12px; text-align: center; position: relative; z-index: 1;"></div>
      </div>
    </div>

    <div class="section">
      <div class="section-label">Background theme</div>
      <div class="glass" style="padding: 16px;">
        <div style="font-size: 12px; line-height: 1.5; color: var(--text-dim); margin-bottom: 14px; position: relative; z-index: 1;">
          Tap a theme to switch the background colors instantly.
        </div>
        <div class="theme-grid" id="themeGrid"></div>
      </div>
    </div>

    <div class="section">
      <div class="section-label">Targets</div>
      <div class="glass" style="padding: 16px;">
        <div class="input-grid-4" style="position: relative; z-index: 1;">
          <div class="field">
            <label>kcal</label>
            <input class="input" id="targetCal" type="number" inputmode="numeric">
          </div>
          <div class="field">
            <label>P (g)</label>
            <input class="input" id="targetP" type="number" inputmode="numeric">
          </div>
          <div class="field">
            <label>C (g)</label>
            <input class="input" id="targetC" type="number" inputmode="numeric">
          </div>
          <div class="field">
            <label>F (g)</label>
            <input class="input" id="targetF" type="number" inputmode="numeric">
          </div>
        </div>
        <button class="btn" style="margin-top: 8px; position: relative; z-index: 1;" onclick="saveTargets()">Save targets</button>
      </div>
    </div>

    <div class="section">
      <div class="section-label">Export summary</div>
      <div class="glass" style="padding: 16px;">
        <div style="font-size: 13px; line-height: 1.5; color: var(--text-dim); margin-bottom: 14px; position: relative; z-index: 1;">
          Generate a beautifully styled summary image for any time range, with all your stats and progress.
        </div>
        <div class="field" style="position: relative; z-index: 1;">
          <label>Time range</label>
          <div style="display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px;">
            <button class="range-btn active" data-range="day" onclick="setExportRange('day')">Day</button>
            <button class="range-btn" data-range="week" onclick="setExportRange('week')">Week</button>
            <button class="range-btn" data-range="month" onclick="setExportRange('month')">Month</button>
            <button class="range-btn" data-range="all" onclick="setExportRange('all')">All</button>
          </div>
        </div>
        <div class="field" style="position: relative; z-index: 1;" id="customDateRange">
          <label>Or pick custom dates</label>
          <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 8px;">
            <input class="input" type="date" id="exportFrom" style="font-size: 14px;">
            <input class="input" type="date" id="exportTo" style="font-size: 14px;">
          </div>
        </div>
        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 8px; position: relative; z-index: 1;">
          <button class="btn btn-glass" onclick="exportData()">Backup JSON</button>
          <button class="btn" onclick="exportSummaryImage()">Export image</button>
        </div>
      </div>
    </div>

    <div class="section">
      <div class="section-label">Reset</div>
      <div class="glass" style="padding: 16px;">
        <div style="position: relative; z-index: 1;">
          <button class="btn btn-glass" onclick="confirmReset()" style="color: var(--danger);">Reset all data</button>
        </div>
      </div>
    </div>

  </div>

</div>

<nav class="tab-bar">
  <button class="tab-bar-item active" data-tab="today">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 9l9-7 9 7v11a2 2 0 01-2 2H5a2 2 0 01-2-2z"/></svg>
    Today
  </button>
  <button class="tab-bar-item" data-tab="trends">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="23 6 13.5 15.5 8.5 10.5 1 18"/></svg>
    Trends
  </button>
  <button class="tab-bar-item" data-tab="supps">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M10.5 20.5L20.5 10.5a5 5 0 00-7-7L3.5 13.5a5 5 0 007 7z"/><line x1="8.5" y1="8.5" x2="15.5" y2="15.5"/></svg>
    Supps
  </button>
  <button class="tab-bar-item" data-tab="insights">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>
    Insights
  </button>
  <button class="tab-bar-item" data-tab="settings">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 00.33 1.82l.06.06a2 2 0 010 2.83 2 2 0 01-2.83 0l-.06-.06a1.65 1.65 0 00-1.82-.33 1.65 1.65 0 00-1 1.51V21a2 2 0 01-2 2 2 2 0 01-2-2v-.09A1.65 1.65 0 009 19.4a1.65 1.65 0 00-1.82.33l-.06.06a2 2 0 01-2.83 0 2 2 0 010-2.83l.06-.06a1.65 1.65 0 00.33-1.82 1.65 1.65 0 00-1.51-1H3a2 2 0 01-2-2 2 2 0 012-2h.09A1.65 1.65 0 004.6 9a1.65 1.65 0 00-.33-1.82l-.06-.06a2 2 0 010-2.83 2 2 0 012.83 0l.06.06a1.65 1.65 0 001.82.33H9a1.65 1.65 0 001-1.51V3a2 2 0 012-2 2 2 0 012 2v.09a1.65 1.65 0 001 1.51 1.65 1.65 0 001.82-.33l.06-.06a2 2 0 012.83 0 2 2 0 010 2.83l-.06.06a1.65 1.65 0 00-.33 1.82V9a1.65 1.65 0 001.51 1H21a2 2 0 012 2 2 2 0 01-2 2h-.09a1.65 1.65 0 00-1.51 1z"/></svg>
    Settings
  </button>
</nav>

<div class="modal-bg" id="modalBg" onclick="closeOnBg(event)">
  <div class="modal" onclick="event.stopPropagation()">
    <div class="handle"></div>
    <div id="modalContent"></div>
  </div>
</div>

<script>
const CONFIG = {
  startWeight: 114.5,
  height: 185,
  age: 31,
  nicotineQuitDate: '2026-05-05',
  targets: { calories: 1800, protein: 180, carbs: 180, fat: 60 }
};

const SUPPLEMENTS = [
  { id: 'coq10', name: 'CoQ10 (Ubiquinol)', dose: '200–400 mg', for: 'Sperm motility' },
  { id: 'd3', name: 'Vitamin D3', dose: '4,000 IU', for: 'Test, mood, immunity' },
  { id: 'zinc', name: 'Zinc', dose: '25–30 mg', for: 'Test, sperm production' },
  { id: 'carnitine', name: 'L-Carnitine', dose: '2–3 g', for: 'Sperm motility' },
  { id: 'omega3', name: 'Omega-3 (EPA/DHA)', dose: '2–3 g', for: 'Sperm membrane' },
  { id: 'mag', name: 'Magnesium glycinate', dose: '300–400 mg', for: 'Sleep, T support' },
  { id: 'creatine', name: 'Creatine monohydrate', dose: '5 g', for: 'Muscle preservation' },
  { id: 'whey', name: 'Whey protein', dose: '1–2 scoops', for: 'Hit protein target' }
];

const THEMES = [
  {
    id: 'ocean',
    name: 'Ocean',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #4a7dff 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #1a4dd6 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #2cb5ff 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #0c3fa8 0%, transparent 55%),linear-gradient(180deg, #0a1530 0%, #061029 100%)`,
    swatch: 'linear-gradient(135deg, #4a7dff, #2cb5ff, #0c3fa8)'
  },
  {
    id: 'sunset',
    name: 'Sunset',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #ff6b9d 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #ff9544 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #c93dff 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #ff4d4d 0%, transparent 55%),linear-gradient(180deg, #2a0a1f 0%, #1a0612 100%)`,
    swatch: 'linear-gradient(135deg, #ff6b9d, #ff9544, #c93dff)'
  },
  {
    id: 'forest',
    name: 'Forest',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #4ade80 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #14b8a6 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #84cc16 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #16a34a 0%, transparent 55%),linear-gradient(180deg, #0a1f15 0%, #061a10 100%)`,
    swatch: 'linear-gradient(135deg, #4ade80, #14b8a6, #84cc16)'
  },
  {
    id: 'midnight',
    name: 'Midnight',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #6b3df5 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #3b1d8a 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #1e1b4b 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #4c1d95 0%, transparent 55%),linear-gradient(180deg, #0a0a1f 0%, #050514 100%)`,
    swatch: 'linear-gradient(135deg, #6b3df5, #3b1d8a, #1e1b4b)'
  },
  {
    id: 'rose',
    name: 'Rose',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #f472b6 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #ec4899 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #fb7185 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #be185d 0%, transparent 55%),linear-gradient(180deg, #2a0a1f 0%, #1a0612 100%)`,
    swatch: 'linear-gradient(135deg, #f472b6, #ec4899, #be185d)'
  },
  {
    id: 'mono',
    name: 'Mono',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #4b5563 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #1f2937 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #374151 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #111827 0%, transparent 55%),linear-gradient(180deg, #0a0a0f 0%, #000000 100%)`,
    swatch: 'linear-gradient(135deg, #4b5563, #1f2937, #111827)'
  },
  {
    id: 'fire',
    name: 'Fire',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #f97316 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #dc2626 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #fbbf24 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #991b1b 0%, transparent 55%),linear-gradient(180deg, #1a0a05 0%, #0f0603 100%)`,
    swatch: 'linear-gradient(135deg, #f97316, #dc2626, #fbbf24)'
  },
  {
    id: 'mint',
    name: 'Mint',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #5eead4 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #06b6d4 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #67e8f9 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #0891b2 0%, transparent 55%),linear-gradient(180deg, #051f1f 0%, #021212 100%)`,
    swatch: 'linear-gradient(135deg, #5eead4, #06b6d4, #67e8f9)'
  },
  {
    id: 'gold',
    name: 'Gold',
    gradient: `radial-gradient(ellipse 80% 60% at 20% 0%, #fbbf24 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 100% 30%, #d97706 0%, transparent 55%),radial-gradient(ellipse 80% 60% at 0% 70%, #fde047 0%, transparent 55%),radial-gradient(ellipse 70% 60% at 90% 100%, #b45309 0%, transparent 55%),linear-gradient(180deg, #1a1205 0%, #0f0a02 100%)`,
    swatch: 'linear-gradient(135deg, #fbbf24, #d97706, #fde047)'
  }
];

const SEED = {
  '2026-05-05': {
    weight: 112.5,
    meals: [
      { name: 'Pita with kofta 90g', cal: 410, p: 23, c: 34, f: 20 },
      { name: 'Ayran 1 cup', cal: 60, p: 5, c: 7, f: 2 },
      { name: '2 Americanos', cal: 10, p: 0, c: 1, f: 0 },
      { name: 'Chicken Caesar salad', cal: 445, p: 30, c: 12, f: 30 }
    ],
    sleep: 4.5, water: null, cravings: 0,
    trainingMin: 60, trainingType: 'Cardio circuit',
    supps: {}
  },
  '2026-05-06': {
    weight: 112.5,
    meals: [
      { name: 'Barebells vanilla shake', cal: 191, p: 24, c: 16, f: 6 },
      { name: 'Banana', cal: 105, p: 1, c: 27, f: 0 },
      { name: 'Americano', cal: 5, p: 0, c: 1, f: 0 },
      { name: 'Kofta arayes x2', cal: 480, p: 26, c: 38, f: 24 },
      { name: 'Ayran 1 cup', cal: 60, p: 5, c: 7, f: 2 },
      { name: 'Jarjir salad', cal: 15, p: 1, c: 2, f: 0 },
      { name: 'Olive oil 1 tsp', cal: 40, p: 0, c: 0, f: 4.5 },
      { name: 'Fruit salad 1 cup', cal: 110, p: 1, c: 28, f: 0 },
      { name: 'Honey 1 tsp', cal: 20, p: 0, c: 6, f: 0 }
    ],
    sleep: null, water: null, cravings: null,
    trainingMin: null, trainingType: null,
    supps: {}
  }
};

let state = { days: {}, currentDate: '2026-05-06', view: 'today', settings: { theme: 'ocean', apiKey: '', targets: null } };
const STORAGE_KEY = 'hs-tracker-v3';

function loadState() {
  try {
    const stored = localStorage.getItem(STORAGE_KEY);
    if (stored) {
      const p = JSON.parse(stored);
      state.days = p.days || {};
      state.currentDate = p.currentDate || '2026-05-06';
      if (p.settings) state.settings = Object.assign(state.settings, p.settings);
      Object.keys(SEED).forEach(d => { if (!state.days[d]) state.days[d] = SEED[d]; });
    } else {
      state.days = JSON.parse(JSON.stringify(SEED));
    }
    if (!state.days[state.currentDate]) state.days[state.currentDate] = blankDay();
    // Apply user-saved targets if any
    if (state.settings.targets) {
      Object.assign(CONFIG.targets, state.settings.targets);
    }
  } catch (e) {
    state.days = JSON.parse(JSON.stringify(SEED));
  }
}

function saveState() {
  try { localStorage.setItem(STORAGE_KEY, JSON.stringify({ days: state.days, currentDate: state.currentDate, settings: state.settings })); } catch (e) {}
}

function applyTheme(themeId) {
  const theme = THEMES.find(t => t.id === themeId) || THEMES[0];
  document.documentElement.style.setProperty('--bg-gradient', theme.gradient);
}

function blankDay() {
  return { weight: null, meals: [], sleep: null, water: null, cravings: null, trainingMin: null, trainingType: null, supps: {} };
}

function isoToDate(iso) { return new Date(iso + 'T00:00:00'); }

function fmtDay(iso) {
  return isoToDate(iso).toLocaleDateString('en-US', { weekday: 'long', month: 'long', day: 'numeric' });
}

function fmtSub(iso) {
  const today = new Date(); today.setHours(0,0,0,0);
  const target = isoToDate(iso);
  const diff = Math.round((target - today) / 86400000);
  if (diff === 0) return 'Today';
  if (diff === -1) return 'Yesterday';
  if (diff === 1) return 'Tomorrow';
  return iso;
}

function nicotineStreak() {
  const quit = isoToDate(CONFIG.nicotineQuitDate);
  const now = new Date(); now.setHours(0,0,0,0);
  return Math.max(0, Math.floor((now - quit) / 86400000) + 1);
}

function getDay(iso) { if (!state.days[iso]) state.days[iso] = blankDay(); return state.days[iso]; }

function totals(meals) {
  return meals.reduce((a, m) => ({
    cal: a.cal + (+m.cal || 0), p: a.p + (+m.p || 0),
    c: a.c + (+m.c || 0), f: a.f + (+m.f || 0)
  }), { cal: 0, p: 0, c: 0, f: 0 });
}

function setPill(id, pct) {
  const el = document.getElementById(id);
  if (pct < 50) { el.textContent = 'low'; el.className = 'pill low'; }
  else if (pct < 90) { el.textContent = 'ok'; el.className = 'pill ok'; }
  else { el.textContent = 'good'; el.className = 'pill good'; }
}

function escapeHtml(s) {
  return String(s).replace(/[&<>"']/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
}

function render() {
  document.getElementById('streakNum').textContent = nicotineStreak();
  document.getElementById('dayLabel').textContent = fmtDay(state.currentDate);
  document.getElementById('daySub').textContent = fmtSub(state.currentDate);
  if (state.view === 'today') renderToday();
  if (state.view === 'trends') renderTrends();
  if (state.view === 'supps') renderSupps();
  if (state.view === 'insights') renderInsights();
  if (state.view === 'settings') renderSettings();
}

function renderSettings() {
  // Theme grid
  const grid = document.getElementById('themeGrid');
  grid.innerHTML = THEMES.map(t => `
    <button class="theme-swatch ${state.settings.theme === t.id ? 'active' : ''}" onclick="setTheme('${t.id}')" style="background: ${t.swatch};">
      <span class="label">${t.name}</span>
    </button>
  `).join('');

  // API key (masked)
  const keyInput = document.getElementById('apiKeyInput');
  if (state.settings.apiKey) {
    keyInput.placeholder = '••••••••••' + state.settings.apiKey.slice(-4);
    keyInput.value = '';
    document.getElementById('apiKeyStatus').innerHTML = '<span style="color: var(--good);">✓ API key saved · Photo logging enabled</span>';
  } else {
    keyInput.placeholder = 'Paste your key here';
    document.getElementById('apiKeyStatus').innerHTML = '<span style="color: var(--text-faint);">No key saved · Photo logging disabled</span>';
  }

  // Targets
  document.getElementById('targetCal').value = CONFIG.targets.calories;
  document.getElementById('targetP').value = CONFIG.targets.protein;
  document.getElementById('targetC').value = CONFIG.targets.carbs;
  document.getElementById('targetF').value = CONFIG.targets.fat;

  // Export range default fill
  if (document.getElementById('exportFrom') && !document.getElementById('exportFrom').value) {
    setExportRange(exportRange);
  }
}

function setTheme(themeId) {
  state.settings.theme = themeId;
  applyTheme(themeId);
  saveState();
  renderSettings();
}

function saveApiKey() {
  const v = document.getElementById('apiKeyInput').value.trim();
  if (!v) return;
  state.settings.apiKey = v;
  saveState();
  renderSettings();
}

function clearApiKey() {
  state.settings.apiKey = '';
  saveState();
  renderSettings();
}

function saveTargets() {
  const cal = +document.getElementById('targetCal').value;
  const p = +document.getElementById('targetP').value;
  const c = +document.getElementById('targetC').value;
  const f = +document.getElementById('targetF').value;
  if (cal > 0) CONFIG.targets.calories = cal;
  if (p > 0) CONFIG.targets.protein = p;
  if (c > 0) CONFIG.targets.carbs = c;
  if (f > 0) CONFIG.targets.fat = f;
  state.settings.targets = { ...CONFIG.targets };
  saveState();
  alert('Targets saved');
  render();
}

function exportData() {
  const blob = new Blob([JSON.stringify({ days: state.days, settings: state.settings }, null, 2)], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'hs-tracker-backup-' + new Date().toISOString().slice(0,10) + '.json';
  a.click();
  URL.revokeObjectURL(url);
}

// ============ EXPORT RANGE & SUMMARY ============
let exportRange = 'day';

function setExportRange(range) {
  exportRange = range;
  document.querySelectorAll('.range-btn').forEach(b => {
    b.classList.toggle('active', b.dataset.range === range);
  });

  // Auto-fill the date inputs
  const today = state.currentDate;
  const todayDate = isoToDate(today);
  let from = today;
  if (range === 'week') {
    const d = new Date(todayDate);
    d.setDate(d.getDate() - 6);
    from = d.toISOString().slice(0, 10);
  } else if (range === 'month') {
    const d = new Date(todayDate);
    d.setDate(d.getDate() - 29);
    from = d.toISOString().slice(0, 10);
  } else if (range === 'all') {
    const all = Object.keys(state.days).sort();
    from = all[0] || today;
  }
  document.getElementById('exportFrom').value = from;
  document.getElementById('exportTo').value = today;
}

function getRangeData() {
  const fromVal = document.getElementById('exportFrom').value;
  const toVal = document.getElementById('exportTo').value;
  if (!fromVal || !toVal) {
    alert('Pick a date range first');
    return null;
  }
  const dates = Object.keys(state.days).sort().filter(d => d >= fromVal && d <= toVal);
  return { from: fromVal, to: toVal, dates };
}

function computeRangeSummary() {
  const range = getRangeData();
  if (!range) return null;
  const { from, to, dates } = range;

  let totalCal = 0, totalP = 0, totalC = 0, totalF = 0;
  let mealsLogged = 0;
  let totalSleep = 0, sleepCount = 0;
  let totalWater = 0, waterCount = 0;
  let totalCravings = 0, cravingsCount = 0;
  let totalTraining = 0, trainingDays = 0;
  let suppCount = 0, suppDays = 0;
  let weighed = [];

  dates.forEach(d => {
    const day = state.days[d];
    if (!day) return;
    const t = totals(day.meals || []);
    if (t.cal > 0) {
      totalCal += t.cal; totalP += t.p; totalC += t.c; totalF += t.f;
      mealsLogged++;
    }
    if (day.sleep != null) { totalSleep += day.sleep; sleepCount++; }
    if (day.water != null) { totalWater += day.water; waterCount++; }
    if (day.cravings != null) { totalCravings += day.cravings; cravingsCount++; }
    if (day.trainingMin != null) { totalTraining += day.trainingMin; trainingDays++; }
    if (day.weight != null) weighed.push({ date: d, weight: day.weight });
    if (day.supps) {
      const taken = Object.values(day.supps).filter(Boolean).length;
      if (taken > 0) { suppCount += taken; suppDays++; }
    }
  });

  const startWeight = weighed.length ? weighed[0].weight : null;
  const endWeight = weighed.length ? weighed[weighed.length - 1].weight : null;
  const weightChange = (startWeight != null && endWeight != null) ? endWeight - startWeight : null;

  return {
    from, to, dates,
    days: dates.length,
    avgCal: mealsLogged ? Math.round(totalCal / mealsLogged) : 0,
    avgP: mealsLogged ? Math.round(totalP / mealsLogged) : 0,
    avgC: mealsLogged ? Math.round(totalC / mealsLogged) : 0,
    avgF: mealsLogged ? Math.round(totalF / mealsLogged) : 0,
    totalCal: Math.round(totalCal),
    mealsLogged,
    avgSleep: sleepCount ? (totalSleep / sleepCount).toFixed(1) : null,
    avgWater: waterCount ? (totalWater / waterCount).toFixed(1) : null,
    avgCravings: cravingsCount ? (totalCravings / cravingsCount).toFixed(1) : null,
    totalTraining,
    trainingDays,
    startWeight, endWeight, weightChange,
    weighed,
    suppCount, suppDays
  };
}

async function exportSummaryImage() {
  const summary = computeRangeSummary();
  if (!summary) return;

  // Show loading
  const btn = event.target;
  const originalText = btn.textContent;
  btn.textContent = 'Generating...';
  btn.disabled = true;

  try {
    // Build the export node
    const exportNode = buildExportNode(summary);
    document.body.appendChild(exportNode);

    // Wait a tick for fonts/styles to apply
    await new Promise(r => setTimeout(r, 100));

    const canvas = await html2canvas(exportNode, {
      backgroundColor: null,
      scale: 2,
      useCORS: true,
      logging: false,
      width: exportNode.offsetWidth,
      height: exportNode.offsetHeight
    });

    document.body.removeChild(exportNode);

    canvas.toBlob(blob => {
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      const rangeLabel = exportRange === 'day' ? summary.to :
                          exportRange === 'all' ? 'all-time' :
                          summary.from + '_to_' + summary.to;
      a.href = url;
      a.download = `HS-Tracker-${rangeLabel}.png`;
      a.click();
      URL.revokeObjectURL(url);

      btn.textContent = 'Image saved ✓';
      setTimeout(() => { btn.textContent = originalText; btn.disabled = false; }, 2000);
    }, 'image/png');
  } catch (err) {
    console.error(err);
    alert('Could not generate image: ' + (err.message || 'Unknown error'));
    btn.textContent = originalText;
    btn.disabled = false;
  }
}

function buildExportNode(s) {
  // Get current theme gradient
  const theme = THEMES.find(t => t.id === state.settings.theme) || THEMES[0];
  const streak = nicotineStreak();

  // Format date range nicely
  const fromDate = isoToDate(s.from);
  const toDate = isoToDate(s.to);
  const sameDay = s.from === s.to;
  const rangeStr = sameDay
    ? fromDate.toLocaleDateString('en-US', { weekday: 'long', month: 'long', day: 'numeric', year: 'numeric' })
    : fromDate.toLocaleDateString('en-US', { month: 'short', day: 'numeric' }) + ' → ' + toDate.toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' });

  const rangeLabel = exportRange === 'day' ? 'Daily Summary' :
                     exportRange === 'week' ? 'Weekly Summary' :
                     exportRange === 'month' ? 'Monthly Summary' :
                     exportRange === 'all' ? 'All-Time Summary' : 'Custom Summary';

  const node = document.createElement('div');
  node.style.cssText = `
    position: fixed;
    left: -9999px;
    top: 0;
    width: 480px;
    padding: 28px 24px 32px;
    background: #0a1530;
    background-image: ${theme.gradient};
    color: #fff;
    font-family: var(--font);
    box-sizing: border-box;
    overflow: hidden;
  `;

  // Build weight chart inline if we have weighed data
  let chartHtml = '';
  if (s.weighed.length >= 2) {
    chartHtml = buildExportChart(s.weighed);
  }

  // Format weight change
  const wcText = s.weightChange != null
    ? (s.weightChange > 0 ? '+' : '') + s.weightChange.toFixed(1) + ' kg'
    : '—';
  const wcColor = s.weightChange != null ? (s.weightChange <= 0 ? '#7df5b3' : '#ffd166') : '#fff';

  node.innerHTML = `
    <!-- App-style header -->
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 22px;">
      <div style="font-size: 22px; font-weight: 700; letter-spacing: -0.02em;">
        HS<span style="color: #66e1ff;"> </span>Tracker
      </div>
      <div style="
        display: inline-flex; align-items: center; gap: 8px;
        padding: 8px 14px; border-radius: 100px;
        background: rgba(255,255,255,0.12);
        border: 1px solid rgba(255,255,255,0.22);
        font-size: 13px; font-weight: 500;
      ">
        <span style="width: 8px; height: 8px; border-radius: 50%; background: #66e1ff; box-shadow: 0 0 10px #66e1ff;"></span>
        <span><span style="color: #66e1ff; font-weight: 700;">${streak}</span> nicotine-free</span>
      </div>
    </div>

    <!-- Title block -->
    <div style="margin-bottom: 24px;">
      <div style="font-size: 11px; font-weight: 600; color: rgba(255,255,255,0.7); text-transform: uppercase; letter-spacing: 0.15em; margin-bottom: 6px;">${rangeLabel}</div>
      <div style="font-size: 24px; font-weight: 700; letter-spacing: -0.02em; line-height: 1.2;">${rangeStr}</div>
      <div style="font-size: 13px; font-weight: 500; color: rgba(255,255,255,0.65); margin-top: 4px;">${s.days} day${s.days !== 1 ? 's' : ''} tracked</div>
    </div>

    <!-- Weight hero -->
    <div style="
      background: linear-gradient(135deg, rgba(255,255,255,0.24), rgba(255,255,255,0.06) 50%, rgba(255,255,255,0.16));
      border: 1px solid rgba(255,255,255,0.32);
      border-radius: 22px;
      padding: 20px 22px;
      margin-bottom: 12px;
      display: flex; justify-content: space-between; align-items: flex-end;
      box-shadow: inset 0 1.5px 1px rgba(255,255,255,0.5), 0 8px 32px rgba(0,8,28,0.2);
    ">
      <div>
        <div style="font-size: 11px; font-weight: 600; color: rgba(255,255,255,0.7); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 8px;">Weight</div>
        <div style="font-size: 40px; font-weight: 700; line-height: 1; letter-spacing: -0.03em;">
          ${s.endWeight != null ? s.endWeight : '—'}<span style="font-size: 16px; font-weight: 500; color: rgba(255,255,255,0.6); margin-left: 4px;">kg</span>
        </div>
      </div>
      <div style="text-align: right;">
        <div style="font-size: 15px; font-weight: 600; color: ${wcColor};">${wcText}</div>
        <div style="font-size: 12px; font-weight: 500; color: rgba(255,255,255,0.55); margin-top: 4px;">in range</div>
      </div>
    </div>

    ${chartHtml}

    <!-- Stats grid -->
    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 12px;">
      ${statCard('Avg calories', s.avgCal.toLocaleString(), 'kcal/day', '#66e1ff')}
      ${statCard('Avg protein', s.avgP, 'g/day', '#7df5b3')}
      ${statCard('Avg carbs', s.avgC, 'g/day', '#87b8ff')}
      ${statCard('Avg fat', s.avgF, 'g/day', '#ffd166')}
      ${statCard('Sleep avg', s.avgSleep != null ? s.avgSleep : '—', s.avgSleep != null ? 'hrs' : '', '#a78bfa')}
      ${statCard('Water avg', s.avgWater != null ? s.avgWater : '—', s.avgWater != null ? 'L' : '', '#66e1ff')}
      ${statCard('Training', s.totalTraining, 'min total', '#ff7a90')}
      ${statCard('Train days', s.trainingDays, 'sessions', '#7df5b3')}
    </div>

    <!-- Footer -->
    <div style="margin-top: 24px; padding-top: 18px; border-top: 1px solid rgba(255,255,255,0.15); display: flex; justify-content: space-between; align-items: center;">
      <div style="font-size: 11px; font-weight: 500; color: rgba(255,255,255,0.5);">${s.mealsLogged} meal${s.mealsLogged !== 1 ? 's' : ''} logged · ${s.suppCount} supp${s.suppCount !== 1 ? 's' : ''} taken</div>
      <div style="font-size: 11px; font-weight: 600; color: rgba(255,255,255,0.7); letter-spacing: -0.01em;">HS Tracker</div>
    </div>
  `;

  return node;
}

function statCard(label, val, unit, accent) {
  return `
    <div style="
      background: linear-gradient(135deg, rgba(255,255,255,0.18), rgba(255,255,255,0.04) 50%, rgba(255,255,255,0.12));
      border: 1px solid rgba(255,255,255,0.22);
      border-radius: 18px;
      padding: 14px 16px;
      box-shadow: inset 0 1.5px 1px rgba(255,255,255,0.45);
    ">
      <div style="font-size: 11px; font-weight: 600; color: rgba(255,255,255,0.65); text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 8px;">${label}</div>
      <div style="font-size: 22px; font-weight: 700; line-height: 1; letter-spacing: -0.02em; color: ${accent};">
        ${val}<span style="font-size: 11px; font-weight: 500; color: rgba(255,255,255,0.5); margin-left: 3px; letter-spacing: -0.01em;">${unit}</span>
      </div>
    </div>
  `;
}

function buildExportChart(weighed) {
  const w = 432, h = 140, pad = 28;
  const weights = weighed.map(d => d.weight);
  const minW = Math.min(...weights) - 0.5;
  const maxW = Math.max(...weights) + 0.5;
  const wRange = maxW - minW || 1;
  const xs = weighed.map((_, i) => pad + (weighed.length === 1 ? (w - pad * 2) / 2 : (i / (weighed.length - 1)) * (w - pad * 2)));
  const ys = weighed.map(d => pad + (1 - (d.weight - minW) / wRange) * (h - pad * 2));

  let areaPath = `M ${xs[0]} ${h - pad}`;
  xs.forEach((x, i) => { areaPath += ` L ${x} ${ys[i]}`; });
  areaPath += ` L ${xs[xs.length-1]} ${h - pad} Z`;

  let linePath = `M ${xs[0]} ${ys[0]}`;
  for (let i = 1; i < xs.length; i++) {
    const xc = (xs[i-1] + xs[i]) / 2;
    linePath += ` Q ${xs[i-1]} ${ys[i-1]} ${xc} ${(ys[i-1] + ys[i]) / 2}`;
  }
  linePath += ` T ${xs[xs.length-1]} ${ys[xs.length-1]}`;

  let points = '';
  xs.forEach((x, i) => {
    points += `<circle cx="${x}" cy="${ys[i]}" r="4" fill="#0a1530" stroke="#66e1ff" stroke-width="2.5"/>`;
  });

  return `
    <div style="
      background: linear-gradient(135deg, rgba(255,255,255,0.18), rgba(255,255,255,0.04) 50%, rgba(255,255,255,0.12));
      border: 1px solid rgba(255,255,255,0.22);
      border-radius: 22px;
      padding: 16px 18px 12px;
      margin-bottom: 12px;
      box-shadow: inset 0 1.5px 1px rgba(255,255,255,0.45);
    ">
      <div style="font-size: 11px; font-weight: 600; color: rgba(255,255,255,0.7); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 8px;">Weight trend</div>
      <svg width="100%" height="${h}" viewBox="0 0 ${w} ${h}" preserveAspectRatio="none" style="display: block;">
        <defs>
          <linearGradient id="exportGrad" x1="0" y1="0" x2="0" y2="1">
            <stop offset="0%" stop-color="#66e1ff" stop-opacity="0.45"/>
            <stop offset="100%" stop-color="#66e1ff" stop-opacity="0"/>
          </linearGradient>
        </defs>
        <path d="${areaPath}" fill="url(#exportGrad)"/>
        <path d="${linePath}" fill="none" stroke="#66e1ff" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"/>
        ${points}
      </svg>
    </div>
  `;
}

function confirmReset() {
  if (confirm('Reset everything? All your logged data will be deleted. This cannot be undone.')) {
    if (confirm('Are you really sure? This is permanent.')) {
      localStorage.removeItem(STORAGE_KEY);
      location.reload();
    }
  }
}

// ============ PHOTO MODAL & GEMINI AI ============
function openPhotoModal() {
  const hasKey = !!state.settings.apiKey;
  const html = `
    <h3>Photo meal log</h3>
    <div class="modal-sub">${fmtDay(state.currentDate)}</div>
    ${!hasKey ? `
      <div class="ai-status error">
        No API key set. Go to <strong>Settings → AI photo recognition</strong> to add your free Gemini key first.
      </div>
      <div class="form-actions">
        <button class="btn btn-glass" onclick="closeModal()">Close</button>
        <button class="btn" onclick="closeModal(); setView('settings');">Open settings</button>
      </div>
    ` : `
      <div id="photoZone">
        <input type="file" accept="image/*" capture="environment" id="photoInput" style="display: none;" onchange="handlePhoto(event)">
        <button class="photo-btn" onclick="document.getElementById('photoInput').click();" style="margin-top: 0;">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M23 19a2 2 0 01-2 2H3a2 2 0 01-2-2V8a2 2 0 012-2h4l2-3h6l2 3h4a2 2 0 012 2z"/><circle cx="12" cy="13" r="4"/></svg>
          Take photo or pick from gallery
        </button>
      </div>
      <div id="photoResult"></div>
      <div class="form-actions">
        <button class="btn btn-glass" onclick="closeModal()">Close</button>
      </div>
    `}
  `;
  showModal(html);
}

async function handlePhoto(event) {
  const file = event.target.files[0];
  if (!file) return;

  const result = document.getElementById('photoResult');
  result.innerHTML = '<img class="photo-preview" id="previewImg"><div class="ai-status"><span class="spinner"></span>Analyzing meal...</div>';

  // Convert to base64 and show preview
  const reader = new FileReader();
  reader.onload = async (e) => {
    document.getElementById('previewImg').src = e.target.result;
    const base64 = e.target.result.split(',')[1];
    const mimeType = file.type;

    try {
      const data = await analyzeWithGemini(base64, mimeType);
      showPhotoResult(data, e.target.result);
    } catch (err) {
      result.innerHTML = `
        <img class="photo-preview" src="${e.target.result}">
        <div class="ai-status error">${err.message || 'Failed to analyze photo'}</div>
        <button class="btn btn-glass" onclick="openMealModal()">Add manually instead</button>
      `;
    }
  };
  reader.readAsDataURL(file);
}

async function analyzeWithGemini(base64Data, mimeType) {
  const apiKey = state.settings.apiKey;
  if (!apiKey) throw new Error('No API key');

  const prompt = `Analyze this meal photo and estimate the nutritional content. Respond ONLY with valid JSON in this exact format, no markdown, no extra text:
{"name":"short meal description","cal":number,"p":number,"c":number,"f":number,"confidence":"low"|"medium"|"high"}

Where:
- name: brief description (e.g. "Grilled chicken with rice and salad")
- cal: total calories (number)
- p: protein in grams (number)
- c: carbs in grams (number)
- f: fat in grams (number)
- confidence: how confident you are about the estimate

If you cannot identify food in the image, respond with {"error":"no food detected"}.`;

  const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${encodeURIComponent(apiKey)}`;

  const res = await fetch(url, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      contents: [{
        parts: [
          { text: prompt },
          { inline_data: { mime_type: mimeType, data: base64Data } }
        ]
      }],
      generationConfig: { temperature: 0.2, maxOutputTokens: 256 }
    })
  });

  if (!res.ok) {
    const err = await res.text();
    if (res.status === 400 && err.includes('API key')) throw new Error('Invalid API key. Check Settings.');
    if (res.status === 429) throw new Error('Rate limit hit. Wait a minute.');
    throw new Error('AI service error: ' + res.status);
  }

  const json = await res.json();
  const text = json.candidates?.[0]?.content?.parts?.[0]?.text || '';

  // Extract JSON from response (Gemini sometimes wraps in markdown despite instructions)
  let cleaned = text.trim().replace(/^```json\s*/i, '').replace(/^```\s*/i, '').replace(/\s*```$/, '').trim();

  let parsed;
  try {
    parsed = JSON.parse(cleaned);
  } catch (e) {
    // Try to extract JSON from text
    const match = cleaned.match(/\{[\s\S]*\}/);
    if (match) parsed = JSON.parse(match[0]);
    else throw new Error('Could not parse AI response');
  }

  if (parsed.error) throw new Error(parsed.error);
  return parsed;
}

function showPhotoResult(data, imgSrc) {
  const result = document.getElementById('photoResult');
  const conf = data.confidence || 'medium';
  const confColor = conf === 'high' ? 'var(--good)' : conf === 'low' ? 'var(--warn)' : 'var(--accent)';

  result.innerHTML = `
    <img class="photo-preview" src="${imgSrc}">
    <div class="ai-status success" style="text-align: left; padding: 14px 16px;">
      <div style="font-size: 13px; margin-bottom: 8px;"><strong>${escapeHtml(data.name)}</strong></div>
      <div style="font-size: 11px; color: var(--text-dim);">Confidence: <span style="color: ${confColor};">${conf}</span> · You can adjust below</div>
    </div>
    <div class="field"><label>Meal name</label>
      <input class="input" id="aiName" value="${escapeHtml(data.name)}">
    </div>
    <div class="input-grid-4">
      <div class="field"><label>kcal</label><input class="input" id="aiCal" type="number" value="${Math.round(data.cal || 0)}"></div>
      <div class="field"><label>P (g)</label><input class="input" id="aiP" type="number" value="${Math.round(data.p || 0)}"></div>
      <div class="field"><label>C (g)</label><input class="input" id="aiC" type="number" value="${Math.round(data.c || 0)}"></div>
      <div class="field"><label>F (g)</label><input class="input" id="aiF" type="number" value="${Math.round(data.f || 0)}"></div>
    </div>
    <button class="btn" onclick="saveAiMeal()" style="margin-top: 8px;">Add to meals</button>
  `;
}

function saveAiMeal() {
  const name = document.getElementById('aiName').value.trim();
  if (!name) return;
  getDay(state.currentDate).meals.push({
    name,
    cal: +document.getElementById('aiCal').value || 0,
    p: +document.getElementById('aiP').value || 0,
    c: +document.getElementById('aiC').value || 0,
    f: +document.getElementById('aiF').value || 0
  });
  saveState();
  closeModal();
  render();
}

function renderToday() {
  const day = getDay(state.currentDate);
  const t = totals(day.meals);

  const wEl = document.getElementById('weightVal');
  wEl.innerHTML = (day.weight != null ? day.weight : '—') + '<span class="unit">kg</span>';

  const dates = Object.keys(state.days).sort();
  const idx = dates.indexOf(state.currentDate);
  const prev = dates.slice(0, idx).reverse().find(d => state.days[d].weight != null);
  const dEl = document.getElementById('weightDelta');
  if (prev && day.weight != null) {
    const diff = day.weight - state.days[prev].weight;
    dEl.textContent = (diff > 0 ? '+' : '') + diff.toFixed(1) + ' kg';
    dEl.className = diff > 0 ? 'delta up' : 'delta';
  } else { dEl.textContent = '—'; dEl.className = 'delta'; }

  document.getElementById('weightTotal').textContent = day.weight != null
    ? '−' + (CONFIG.startWeight - day.weight).toFixed(1) + ' kg total'
    : 'Tap to log';

  document.getElementById('calVal').textContent = Math.round(t.cal).toLocaleString();
  document.getElementById('calTarget').textContent = CONFIG.targets.calories.toLocaleString();
  document.getElementById('calLeft').textContent = Math.round(Math.max(0, CONFIG.targets.calories - t.cal)) + ' kcal left';
  const calPct = Math.min(1, t.cal / CONFIG.targets.calories);
  document.getElementById('ringFill').setAttribute('stroke-dasharray', (calPct * 289) + ' 289');

  setMacro('protein', t.p, CONFIG.targets.protein);
  setMacro('carbs', t.c, CONFIG.targets.carbs);
  setMacro('fat', t.f, CONFIG.targets.fat);

  const burn = day.trainingMin ? Math.round(day.trainingMin * 13) : 0;
  document.getElementById('netVal').textContent = Math.round(t.cal - burn).toLocaleString();
  document.getElementById('netDetail').textContent = burn ? ('−' + burn + ' kcal training') : 'No training logged';

  renderMeals(day.meals);
  setCheck('sleep', day.sleep, 'hrs');
  setCheck('water', day.water, 'L');
  setCheck('cravings', day.cravings, '/10');
  setCheck('training', day.trainingMin, 'min');
  document.getElementById('trainingTypeLabel').textContent = day.trainingType || 'Tap to log';
}

function setMacro(key, val, target) {
  document.getElementById(key + 'Val').textContent = Math.round(val);
  document.getElementById(key + 'Target').textContent = target;
  const pct = Math.min(100, val / target * 100);
  document.getElementById(key + 'Bar').style.width = pct + '%';
  setPill(key + 'Pill', pct);
}

function setCheck(key, val, unit) {
  const v = document.getElementById(key + 'Val');
  const u = document.getElementById(key + 'Unit');
  v.textContent = val != null ? val : '—';
  if (u) u.textContent = val != null ? unit : '';
}

function renderMeals(meals) {
  const list = document.getElementById('mealList');
  if (meals.length === 0) {
    list.innerHTML = '<div class="empty-state">No meals logged yet</div>';
    return;
  }
  list.innerHTML = meals.map((m, i) => `
    <div class="meal-row">
      <div class="meal-info">
        <div class="name">${escapeHtml(m.name)}</div>
        <div class="macros">P ${m.p}g · C ${m.c}g · F ${m.f}g</div>
      </div>
      <div class="meal-cal">${m.cal} kcal</div>
      <button class="meal-del" onclick="deleteMeal(${i})" aria-label="Delete">×</button>
    </div>
  `).join('');
}

function renderTrends() {
  const dates = Object.keys(state.days).sort();
  const weighed = dates.filter(d => state.days[d].weight != null).map(d => ({ date: d, weight: state.days[d].weight }));

  document.getElementById('totalDays').textContent = dates.length;
  if (weighed.length > 0) {
    const last = weighed[weighed.length - 1];
    document.getElementById('totalLost').textContent = (CONFIG.startWeight - last.weight).toFixed(1);
  }
  if (weighed.length >= 2) {
    const f = weighed[0], l = weighed[weighed.length - 1];
    const days = (isoToDate(l.date) - isoToDate(f.date)) / 86400000;
    if (days > 0) document.getElementById('avgWeekly').textContent = ((f.weight - l.weight) / days * 7).toFixed(2);
  }
  const last7 = dates.slice(-7).map(d => totals(state.days[d].meals).p).filter(p => p > 0);
  if (last7.length) document.getElementById('avgProtein').textContent = Math.round(last7.reduce((a,b) => a+b, 0) / last7.length);

  drawChart(weighed);
  document.getElementById('trendMeta').textContent = weighed.length + ' entries';
}

function drawChart(data) {
  const svg = document.getElementById('weightChart');
  if (data.length < 2) {
    svg.innerHTML = '<text x="200" y="90" text-anchor="middle" fill="rgba(255,255,255,0.5)" font-family="-apple-system, BlinkMacSystemFont, SF Pro Text, sans-serif" font-weight="500" font-size="12">Need 2+ weigh-ins to chart</text>';
    return;
  }
  const w = 400, h = 180, pad = 32;
  const allData = [{ date: '2026-04-15', weight: CONFIG.startWeight }, ...data];
  const weights = allData.map(d => d.weight);
  const minW = Math.min(...weights) - 0.5;
  const maxW = Math.max(...weights) + 0.5;
  const wRange = maxW - minW || 1;
  const xs = allData.map((_, i) => pad + (i / (allData.length - 1)) * (w - pad * 2));
  const ys = allData.map(d => pad + (1 - (d.weight - minW) / wRange) * (h - pad * 2));

  let grid = '';
  for (let i = 0; i <= 2; i++) {
    const y = pad + i * (h - pad * 2) / 2;
    const v = (maxW - i * wRange / 2).toFixed(1);
    grid += `<line x1="${pad}" y1="${y}" x2="${w-pad}" y2="${y}" stroke="rgba(255,255,255,0.1)" stroke-width="0.5"/>`;
    grid += `<text x="${pad - 6}" y="${y + 3}" text-anchor="end" fill="rgba(255,255,255,0.5)" font-family="-apple-system, BlinkMacSystemFont, SF Pro Text, sans-serif" font-weight="500" font-size="10">${v}</text>`;
  }

  let areaPath = `M ${xs[0]} ${h - pad}`;
  xs.forEach((x, i) => { areaPath += ` L ${x} ${ys[i]}`; });
  areaPath += ` L ${xs[xs.length-1]} ${h - pad} Z`;

  let linePath = `M ${xs[0]} ${ys[0]}`;
  for (let i = 1; i < xs.length; i++) {
    const xc = (xs[i-1] + xs[i]) / 2;
    linePath += ` Q ${xs[i-1]} ${ys[i-1]} ${xc} ${(ys[i-1] + ys[i]) / 2}`;
  }
  linePath += ` T ${xs[xs.length-1]} ${ys[xs.length-1]}`;

  let points = '';
  xs.forEach((x, i) => {
    points += `<circle cx="${x}" cy="${ys[i]}" r="5" fill="rgba(15,28,60,0.95)" stroke="#66e1ff" stroke-width="2.5"/>`;
  });

  let xLabels = '';
  [0, Math.floor(allData.length / 2), allData.length - 1].forEach(i => {
    if (i < allData.length) {
      const d = isoToDate(allData[i].date);
      const lbl = d.toLocaleDateString('en-US', { month: 'short', day: 'numeric' });
      xLabels += `<text x="${xs[i]}" y="${h - pad + 16}" text-anchor="middle" fill="rgba(255,255,255,0.6)" font-family="-apple-system, BlinkMacSystemFont, SF Pro Text, sans-serif" font-weight="500" font-size="10">${lbl}</text>`;
    }
  });

  svg.innerHTML = `
    <defs>
      <linearGradient id="areaGrad" x1="0" y1="0" x2="0" y2="1">
        <stop offset="0%" stop-color="#66e1ff" stop-opacity="0.4"/>
        <stop offset="100%" stop-color="#66e1ff" stop-opacity="0"/>
      </linearGradient>
      <filter id="lineGlow">
        <feGaussianBlur stdDeviation="2" result="blur"/>
        <feMerge><feMergeNode in="blur"/><feMergeNode in="SourceGraphic"/></feMerge>
      </filter>
    </defs>
    ${grid}
    <path d="${areaPath}" fill="url(#areaGrad)"/>
    <path d="${linePath}" fill="none" stroke="#66e1ff" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" filter="url(#lineGlow)"/>
    ${points}
    ${xLabels}
  `;
}

function renderSupps() {
  const day = getDay(state.currentDate);
  document.getElementById('suppList').innerHTML = SUPPLEMENTS.map(s => {
    const taken = !!day.supps[s.id];
    return `
      <div class="glass supp-card">
        <div class="supp-info">
          <div class="supp-name">${s.name}</div>
          <div class="supp-detail">${s.dose} · ${s.for}</div>
        </div>
        <button class="supp-toggle ${taken ? 'taken' : ''}" onclick="toggleSupp('${s.id}')" aria-label="${taken ? 'Taken' : 'Mark taken'}">
          ${taken ? '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"/></svg>' : ''}
        </button>
      </div>
    `;
  }).join('');
}

function toggleSupp(id) {
  const day = getDay(state.currentDate);
  if (!day.supps) day.supps = {};
  day.supps[id] = !day.supps[id];
  saveState();
  renderSupps();
}

function renderInsights() {
  const day = getDay(state.currentDate);
  const t = totals(day.meals);
  const items = [];

  const proteinPct = t.p / CONFIG.targets.protein * 100;
  if (proteinPct < 60 && t.cal > 0) {
    items.push({ type: 'danger', title: 'Protein critically low', body: `Only ${Math.round(t.p)}g logged vs 180g target. With low T and a calorie deficit, this drives muscle loss instead of fat loss. Add a 200g chicken or fish portion, or a whey shake.` });
  } else if (proteinPct >= 90) {
    items.push({ type: 'good', title: 'Protein on point', body: `${Math.round(t.p)}g — protecting muscle while you cut. Keep this consistent.` });
  }

  const calPct = t.cal / CONFIG.targets.calories * 100;
  if (calPct > 0 && calPct < 50) {
    items.push({ type: 'warn', title: 'Calories well under', body: `Only ${Math.round(t.cal)} kcal so far. Severe deficits backfire — metabolism slows, hormones drop further, recovery suffers. Aim for the full 1,800.` });
  }

  if (day.sleep != null && day.sleep < 6) {
    items.push({ type: 'warn', title: 'Sleep is too short', body: `${day.sleep} hrs. Below 6 drops testosterone 10–15%, spikes cravings, and stalls fat loss. Sleep matters more than your training intensity.` });
  }

  if (day.cravings != null && day.cravings >= 6) {
    items.push({ type: 'info', title: 'High craving day', body: `${day.cravings}/10 — keep cardio short today, hydrate aggressively, and lean on protein-heavy meals.` });
  }

  if (t.f < 40 && t.cal > 1000) {
    items.push({ type: 'warn', title: 'Fat intake low for T', body: `Only ${Math.round(t.f)}g fat. Men eating under 20% calories from fat see measurable testosterone drops. Add olive oil, eggs, or fatty fish.` });
  }

  const streak = nicotineStreak();
  if (streak >= 1 && streak <= 3) {
    items.push({ type: 'info', title: 'Withdrawal peak window', body: `Day ${streak} nicotine-free. Cravings, irritability, and sleep disruption peak now and fade after day 4. Push through.` });
  } else if (streak >= 7) {
    items.push({ type: 'good', title: `${streak} days nicotine-free`, body: `Past the worst withdrawal. Sperm quality improvements begin around day 74 (one full sperm cycle). Stay locked in.` });
  }

  if (items.length === 0) {
    items.push({ type: 'info', title: 'Log more to see insights', body: 'Add today\'s meals, weight, sleep, and training so I can flag what matters.' });
  }

  document.getElementById('insightList').innerHTML = items.map(i => `
    <div class="glass insight ${i.type}">
      <div class="insight-content">
        <div class="insight-title">${i.title}</div>
        <div class="insight-body">${i.body}</div>
      </div>
    </div>
  `).join('');
}

function openMealModal() {
  showModal(`
    <h3>Add meal</h3>
    <div class="modal-sub">${fmtDay(state.currentDate)}</div>
    <div class="field"><label>What did you eat?</label>
      <input class="input" id="mealName" placeholder="e.g. Grilled chicken 200g" autofocus>
    </div>
    <div class="input-grid-4">
      <div class="field"><label>kcal</label><input class="input" id="mealCal" type="number" inputmode="numeric" placeholder="0"></div>
      <div class="field"><label>P (g)</label><input class="input" id="mealP" type="number" inputmode="numeric" placeholder="0"></div>
      <div class="field"><label>C (g)</label><input class="input" id="mealC" type="number" inputmode="numeric" placeholder="0"></div>
      <div class="field"><label>F (g)</label><input class="input" id="mealF" type="number" inputmode="numeric" placeholder="0"></div>
    </div>
    <div style="font-size: 11px; color: var(--text-faint); margin-top: 4px; line-height: 1.5;">Tip — not sure of macros? Just log name and approximate calories. You can refine later.</div>
    <div class="form-actions">
      <button class="btn btn-glass" onclick="closeModal()">Cancel</button>
      <button class="btn" onclick="saveMeal()">Add meal</button>
    </div>
  `);
}

function saveMeal() {
  const name = document.getElementById('mealName').value.trim();
  if (!name) return;
  getDay(state.currentDate).meals.push({
    name,
    cal: +document.getElementById('mealCal').value || 0,
    p: +document.getElementById('mealP').value || 0,
    c: +document.getElementById('mealC').value || 0,
    f: +document.getElementById('mealF').value || 0
  });
  saveState();
  closeModal();
  render();
}

function deleteMeal(idx) {
  getDay(state.currentDate).meals.splice(idx, 1);
  saveState();
  render();
}

function openCheckModal(type) {
  const day = getDay(state.currentDate);
  const cfg = {
    sleep: { label: 'Hours of sleep', placeholder: '7.5', step: '0.1' },
    water: { label: 'Liters of water', placeholder: '3.5', step: '0.1' },
    cravings: { label: 'Nicotine cravings (0–10)', placeholder: '0', step: '1' },
    training: { label: 'Duration (minutes)', placeholder: '60', step: '1' }
  }[type];

  let extra = type === 'training' ? `
    <div class="field"><label>Type</label>
      <input class="input" id="checkType" value="${day.trainingType || ''}" placeholder="e.g. Weights + cardio">
    </div>
    <div class="field"><label>${cfg.label}</label>
      <input class="input" id="checkVal" type="number" inputmode="numeric" value="${day.trainingMin ?? ''}" placeholder="${cfg.placeholder}">
    </div>` : `
    <div class="field"><label>${cfg.label}</label>
      <input class="input" id="checkVal" type="number" inputmode="decimal" step="${cfg.step}" value="${day[type] ?? ''}" placeholder="${cfg.placeholder}">
    </div>`;

  showModal(`
    <h3>${type[0].toUpperCase() + type.slice(1)}</h3>
    <div class="modal-sub">${fmtDay(state.currentDate)}</div>
    ${extra}
    <div class="form-actions">
      <button class="btn btn-glass" onclick="closeModal()">Cancel</button>
      <button class="btn" onclick="saveCheck('${type}')">Save</button>
    </div>
  `);
}

function saveCheck(type) {
  const day = getDay(state.currentDate);
  const v = document.getElementById('checkVal').value;
  const val = v === '' ? null : +v;
  if (type === 'training') {
    day.trainingMin = val;
    day.trainingType = document.getElementById('checkType').value.trim() || null;
  } else {
    day[type] = val;
  }
  saveState();
  closeModal();
  render();
}

function openWeightModal() {
  const day = getDay(state.currentDate);
  showModal(`
    <h3>Update weight</h3>
    <div class="modal-sub">${fmtDay(state.currentDate)}</div>
    <div class="field"><label>Weight in kg</label>
      <input class="input" id="weightInput" type="number" inputmode="decimal" step="0.1" value="${day.weight ?? ''}" placeholder="112.5" autofocus>
    </div>
    <div class="form-actions">
      <button class="btn btn-glass" onclick="closeModal()">Cancel</button>
      <button class="btn" onclick="saveWeight()">Save</button>
    </div>
  `);
}

function saveWeight() {
  const v = +document.getElementById('weightInput').value;
  if (!v) return;
  getDay(state.currentDate).weight = v;
  saveState();
  closeModal();
  render();
}

function showModal(html) {
  document.getElementById('modalContent').innerHTML = html;
  document.getElementById('modalBg').classList.add('show');
  setTimeout(() => { const i = document.querySelector('.modal input'); if (i) i.focus(); }, 280);
}
function closeModal() { document.getElementById('modalBg').classList.remove('show'); }
function closeOnBg(e) { if (e.target.id === 'modalBg') closeModal(); }

function changeDay(delta) {
  const dates = Object.keys(state.days).sort();
  const idx = dates.indexOf(state.currentDate);
  let target;
  if (delta > 0) {
    target = dates[idx + 1] || nextISO(state.currentDate);
  } else {
    target = dates[idx - 1] || prevISO(state.currentDate);
  }
  state.currentDate = target;
  if (!state.days[target]) state.days[target] = blankDay();
  saveState();
  render();
}

function nextISO(iso) { const d = isoToDate(iso); d.setDate(d.getDate() + 1); return d.toISOString().slice(0, 10); }
function prevISO(iso) { const d = isoToDate(iso); d.setDate(d.getDate() - 1); return d.toISOString().slice(0, 10); }

function setView(v) {
  state.view = v;
  document.querySelectorAll('.tabs .tab').forEach(t => t.classList.toggle('active', t.dataset.tab === v));
  document.querySelectorAll('.tab-bar-item').forEach(t => t.classList.toggle('active', t.dataset.tab === v));
  ['today', 'trends', 'supps', 'insights', 'settings'].forEach(n => {
    const el = document.getElementById('tab-' + n);
    if (el) el.style.display = n === v ? 'block' : 'none';
  });
  // Reset scroll and show header on tab change
  window.scrollTo({ top: 0, behavior: 'instant' });
  const h = document.querySelector('.header');
  if (h) h.classList.remove('hidden');
  render();
}

document.getElementById('prevDay').addEventListener('click', () => changeDay(-1));
document.getElementById('nextDay').addEventListener('click', () => changeDay(1));
document.querySelectorAll('.tabs .tab').forEach(b => b.addEventListener('click', () => setView(b.dataset.tab)));
document.querySelectorAll('.tab-bar-item').forEach(b => b.addEventListener('click', () => setView(b.dataset.tab)));
document.getElementById('heroWeight').addEventListener('click', openWeightModal);

// Scroll-based header hide
let lastScrollY = 0;
let scrollTicking = false;
const headerEl = document.querySelector('.header');

function handleScroll() {
  const currentY = window.scrollY;
  const delta = currentY - lastScrollY;

  if (currentY < 30) {
    // Always show near the top
    headerEl.classList.remove('hidden');
  } else if (delta > 4) {
    // Scrolling down — hide
    headerEl.classList.add('hidden');
  } else if (delta < -4) {
    // Scrolling up — show
    headerEl.classList.remove('hidden');
  }

  lastScrollY = currentY;
  scrollTicking = false;
}

window.addEventListener('scroll', () => {
  if (!scrollTicking) {
    requestAnimationFrame(handleScroll);
    scrollTicking = true;
  }
}, { passive: true });

loadState();
applyTheme(state.settings.theme);
render();
</script>

</body>
</html>
