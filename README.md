<!DOCTYPE html>

<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Number River - Genesis</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
@import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&family=Inter:wght@400;500;600;700;800&family=Share+Tech+Mono&display=swap');
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  /* === FINANCIAL TERMINAL THEME ===
     基調: 黒〜炭色 / アクセント: 琥珀(amber) / 上昇: 緑 / 下落: 赤 */
  --P:#f5b73a;          /* プライマリ (琥珀: ティッカー強調) */
  --BG:#0a0a0c;         /* メイン背景 (深い炭) */
  --CARD:#13131a;       /* カード背景 */
  --ACC:#f5b73a;        /* アクセント = プライマリと統一 */
  --DNG:#ef4444;        /* 下落・警告 */
  --WRN:#f5b73a;        /* 警告/注意 */
  --PRP:#fbbf24;        /* セカンダリ強調 */
  --GAIN:#22c55e;       /* 上昇/ホット (緑) */
  --LOSS:#ef4444;       /* 下落/コールド (赤) */
  --BDR:#2a2a33;        /* 罫線 (グリッド) */
  --DIM:#5a5a66;        /* 暗めテキスト */
  --TX:#e8e6df;         /* 本文 (オフホワイト) */
  --TXD:#9a958a;        /* 補助テキスト (落ち着いた灰) */
  --DELTA:#f5b73a;
  --RIVER:#f5b73a;
  --PREDICT:#a855f7;
}
body{font-family:'Inter','Noto Sans JP',sans-serif;background:var(--BG);color:var(--TX);padding:10px;overflow-x:hidden;
background-image:
  radial-gradient(ellipse 60% 40% at 20% 0%,rgba(245,183,58,.05) 0%,transparent 60%),
  radial-gradient(ellipse 50% 30% at 80% 100%,rgba(245,183,58,.03) 0%,transparent 65%),
  linear-gradient(rgba(255,255,255,.018) 1px,transparent 1px),
  linear-gradient(90deg,rgba(255,255,255,.018) 1px,transparent 1px);
background-size:auto,auto,32px 32px,32px 32px;}
.wrap{max-width:1450px;margin:auto;background:linear-gradient(180deg,#13131a,#0d0d12);padding:14px 14px 18px;border-radius:6px;border:1px solid var(--BDR);box-shadow:0 0 0 1px rgba(245,183,58,.08) inset,0 12px 40px rgba(0,0,0,.7);position:relative;}
.wrap::before{content:'';position:absolute;top:-1px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,var(--ACC) 20%,var(--ACC) 80%,transparent);box-shadow:0 0 8px rgba(245,183,58,.4);pointer-events:none;}
h1{text-align:center;font-family:'Inter',sans-serif;color:var(--ACC);letter-spacing:6px;text-transform:uppercase;margin-bottom:10px;font-size:clamp(.85rem,2vw,1.25rem);font-weight:800;text-shadow:none;}
.tabs{display:flex;justify-content:center;gap:4px;margin-bottom:8px;flex-wrap:wrap}
.tab{padding:6px 16px;border:1px solid var(--BDR);border-radius:3px;cursor:pointer;background:#16161c;color:var(--TXD);font-weight:600;font-family:'Inter',sans-serif;font-size:.7rem;letter-spacing:1.5px;transition:all .15s;user-select:none;text-transform:uppercase;}
.tab:hover{border-color:var(--ACC);color:var(--ACC);background:#1c1c24;}
.tab.active{background:var(--ACC);color:#000;border-color:var(--ACC);box-shadow:0 0 0 1px rgba(245,183,58,.3);font-weight:800;}
.dinfo{font-size:.66em;color:var(--DIM);text-align:center;margin-bottom:8px;font-family:'JetBrains Mono',monospace}
.dinfo b{color:var(--P)}
.global-fold-bar{display:flex;justify-content:center;align-items:center;gap:6px;flex-wrap:wrap;margin-bottom:12px;padding:7px 10px;background:#0f0f15;border-radius:4px;border:1px solid var(--BDR);}
.gf-btn{padding:5px 12px;border:1px solid var(--BDR);border-radius:3px;cursor:pointer;background:#16161c;color:var(--TXD);font-size:.65em;font-weight:600;font-family:'JetBrains Mono',monospace;transition:all .12s;letter-spacing:1px;}
.gf-btn:hover{border-color:var(--ACC);color:var(--ACC);background:#1c1c24;}
.gf-btn.gf-pri{background:var(--ACC);color:#000;border-color:var(--ACC);font-weight:700;}
.fold-card{margin-bottom:6px;border:1px solid var(--BDR);border-radius:4px;background:#0f0f15;overflow:hidden;transition:border-color .15s;}
.fold-card.f-open{border-color:rgba(245,183,58,.25);}
.fold-head{display:flex;align-items:center;gap:8px;padding:8px 12px;cursor:pointer;user-select:none;background:#13131a;transition:background .12s;border-left:2px solid transparent;}
.fold-card.f-open .fold-head{border-left-color:var(--ACC);}
.fold-head:hover{background:#1a1a22;}
.fold-icon{font-size:.95em;line-height:1;flex-shrink:0;}
.fold-title{flex:1;font-family:'Inter',sans-serif;font-size:.7em;letter-spacing:1.5px;font-weight:700;color:var(--TX);text-transform:uppercase;}
.fold-card.f-open .fold-title{color:var(--ACC);}
.fold-meta{font-size:.6em;color:var(--DIM);font-family:'JetBrains Mono',monospace;margin-right:6px;}
.fold-arrow{color:var(--TXD);font-size:.7em;transition:transform .2s;}
.fold-card.f-open .fold-arrow{transform:rotate(90deg);color:var(--ACC);}
.fold-body{display:none;padding:10px 12px 12px;border-top:1px solid var(--BDR);}
.fold-card.f-open .fold-body{display:block;animation:fbFade .22s ease;}
@keyframes fbFade{from{opacity:0;transform:translateY(-4px)}to{opacity:1;transform:translateY(0)}}
.csv-zone{border:1px dashed var(--BDR);border-radius:4px;padding:14px 16px;background:#0f0f15;display:flex;align-items:center;gap:12px;flex-wrap:wrap;transition:border-color .2s;cursor:pointer;position:relative;}
.csv-zone.drag-over{border-color:var(--ACC);background:rgba(245,183,58,.05);}
.csv-zone .csv-icon{font-size:1.6rem;flex-shrink:0;}
.csv-zone-text{flex:1;}
.czhead{font-size:.72em;color:var(--ACC);font-family:'Inter',sans-serif;letter-spacing:1.5px;font-weight:700;margin-bottom:3px;text-transform:uppercase;}
.czinfo{font-size:.62em;color:var(--DIM);font-family:'JetBrains Mono',monospace;line-height:1.6;}
#csvFileInput{position:absolute;inset:0;opacity:0;cursor:pointer;}
.csv-status{font-size:.66em;font-family:'JetBrains Mono',monospace;padding:3px 8px;border-radius:3px;margin-left:auto;}
.csv-status.ok{color:#6ee7b7;background:rgba(5,150,105,.12);border:1px solid rgba(5,150,105,.3);}
.csv-status.err{color:#fca5a5;background:rgba(239,68,68,.1);border:1px solid rgba(239,68,68,.3);}
.range-bar{display:flex;align-items:center;gap:10px;padding:8px 12px;background:#0f0f15;border-radius:4px;border:1px solid var(--BDR);flex-wrap:wrap;}
.range-bar label{font-size:.68em;color:var(--TXD);font-family:'JetBrains Mono',monospace;}
.range-bar input[type=range]{flex:1;min-width:120px;height:4px;accent-color:var(--P);cursor:pointer;}
#rangeDisp{font-size:.74em;color:var(--P);font-family:'JetBrains Mono',monospace;min-width:52px;font-weight:700;}
.range-presets{display:flex;gap:4px;flex-wrap:wrap;}
.rpbtn{padding:3px 10px;border:1px solid var(--BDR);border-radius:3px;cursor:pointer;background:#16161c;color:var(--TXD);font-size:.66em;font-weight:600;font-family:'JetBrains Mono',monospace;}
.rpbtn:hover{border-color:var(--ACC);color:var(--ACC);}
.rpbtn.on{background:var(--ACC);color:#000;border-color:var(--ACC);}
.input-sec{display:grid;grid-template-columns:1.6fr 1fr;gap:12px;}
.flabel{display:block;font-size:.7em;color:var(--ACC);margin-bottom:4px;font-weight:700;text-transform:uppercase;letter-spacing:1px}
textarea,input[type=text]{width:100%;background:#0a0a0e;color:var(--TX);border:1px solid var(--BDR);border-radius:3px;padding:7px 9px;font-family:'JetBrains Mono',monospace;font-size:.78em;resize:vertical;outline:none;}
textarea:focus,input[type=text]:focus{border-color:var(--ACC);box-shadow:0 0 0 1px rgba(245,183,58,.2)}
#dataInput{height:160px}
.opts-col{display:flex;flex-direction:column;gap:7px}
.optrow{display:flex;align-items:center;gap:5px;cursor:pointer;color:var(--TXD);font-size:.74em;}
.optrow input[type=checkbox]{accent-color:var(--P);width:13px;height:13px;}
.snote{font-size:.62em;color:var(--DIM);margin-top:2px}
.predict-panel,.type-panel,.bias-panel{border-radius:12px;padding:12px;}
.predict-panel{background:linear-gradient(135deg,rgba(6,182,212,.08),rgba(56,189,248,.05));border:1px solid var(--RIVER);}
.type-panel{background:linear-gradient(135deg,rgba(168,85,247,.08),rgba(244,114,182,.05));border:1px solid var(--PRP);}
.bias-panel{background:linear-gradient(135deg,rgba(245,158,11,.08),rgba(239,68,68,.05));border:1px solid var(--WRN);}
.pp-head,.tp-head,.bp-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:10px;flex-wrap:wrap;gap:8px;}
.pp-title{font-family:'Inter',sans-serif;font-size:.74rem;color:var(--RIVER);letter-spacing:2px;font-weight:900;}
.tp-title{font-family:'Inter',sans-serif;font-size:.74rem;color:var(--PRP);letter-spacing:2px;font-weight:900;}
.bp-title{font-family:'Inter',sans-serif;font-size:.74rem;color:var(--WRN);letter-spacing:2px;font-weight:900;}
.pp-master{display:flex;gap:6px;align-items:center;}
.pp-master-label{font-size:.62em;color:var(--TXD);font-family:'JetBrains Mono',monospace;}
.pp-engines{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:8px;}
.pp-eng{background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:8px;padding:8px 10px;display:flex;align-items:center;gap:8px;}
.pp-eng.on{border-color:var(--RIVER);background:rgba(6,182,212,.08);}
.pp-eng-info{flex:1;min-width:0;}
.pp-eng-name{font-size:.7em;font-weight:700;color:var(--TX);}
.pp-eng-desc{font-size:.56em;color:var(--DIM);font-family:'JetBrains Mono',monospace;line-height:1.3;}
.tp-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:10px;}
.tp-card{background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:8px;padding:10px;}
.tp-card-title{font-family:'Inter',sans-serif;font-size:.62em;color:var(--PRP);letter-spacing:1px;margin-bottom:6px;font-weight:700;}
.tp-current{display:flex;align-items:center;gap:8px;margin-bottom:6px;flex-wrap:wrap;}
.tp-badge{padding:3px 9px;border-radius:6px;font-size:.7em;font-weight:700;font-family:'JetBrains Mono',monospace;}
.tp-bz{background:#7f1d1d;color:#fecaca;border:1px solid var(--DNG);}
.tp-bm{background:#1e3a8a;color:#bfdbfe;border:1px solid #3b82f6;}
.tp-bb{background:#065f46;color:#a7f3d0;border:1px solid var(--ACC);}
.tp-by{background:#78350f;color:#fde68a;border:1px solid var(--WRN);}
.tp-bp{background:#581c87;color:#e9d5ff;border:1px solid var(--PRP);}
.tp-arrow{color:var(--PRP);}
.tp-prob-row{display:flex;align-items:center;gap:6px;font-size:.66em;font-family:'JetBrains Mono',monospace;color:var(--TXD);margin-top:3px;}
.tp-prob-label{min-width:90px;color:var(--TX);font-weight:700;}
.tp-prob-bar{flex:1;height:8px;background:rgba(255,255,255,.05);border-radius:4px;overflow:hidden;}
.tp-prob-fill{height:100%;background:linear-gradient(90deg,var(--PRP),var(--DELTA));}
.tp-prob-pct{min-width:36px;text-align:right;color:var(--PRP);font-weight:700;}
.tp-rough{display:flex;align-items:center;gap:8px;margin-top:5px;}
.tp-rough-meter{flex:1;height:14px;background:rgba(255,255,255,.05);border-radius:7px;overflow:hidden;border:1px solid var(--BDR);}
.tp-rough-fill{height:100%;transition:width .4s;background:linear-gradient(90deg,#10b981 0%,#f59e0b 50%,#ef4444 100%);}
.tp-rough-text{font-size:.7em;font-family:'JetBrains Mono',monospace;font-weight:700;min-width:60px;text-align:right;}
.tp-tail-grid{display:grid;grid-template-columns:repeat(10,1fr);gap:3px;margin-top:4px;}
.tp-tail-cell{aspect-ratio:1;display:flex;align-items:center;justify-content:center;border-radius:4px;font-family:'JetBrains Mono',monospace;font-size:.7em;font-weight:700;border:1px solid var(--BDR);background:#1e293b;color:var(--TXD);}
.tp-tail-cell.hot{background:#7f1d1d;color:#fecaca;border-color:var(--DNG);}
.tp-tail-cell.warm{background:#78350f;color:#fde68a;border-color:var(--WRN);}
.tp-tail-cell.cold{background:#1e3a8a;color:#bfdbfe;border-color:#3b82f6;}
.tp-tail-cell.last{outline:2px solid var(--PRP);outline-offset:1px;}
.tp-recommend{font-size:.65em;color:var(--TXD);margin-top:6px;line-height:1.6;font-family:'JetBrains Mono',monospace;}
.tp-recommend b{color:var(--PRP);}
.bp-stats{display:flex;gap:14px;font-size:.62em;color:var(--TXD);font-family:'JetBrains Mono',monospace;flex-wrap:wrap;margin-bottom:10px;}
.bp-stats b{color:var(--WRN);}
.bp-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:10px;}
.bp-card{background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:8px;padding:10px;}
.bp-card-title{font-family:'Inter',sans-serif;font-size:.62em;letter-spacing:1px;margin-bottom:6px;font-weight:700;}
.bp-hot-title{color:#fca5a5;}
.bp-cold-title{color:#93c5fd;}
.bp-list{display:flex;flex-wrap:wrap;gap:4px;}
.bp-item{display:flex;flex-direction:column;align-items:center;padding:5px 7px;border-radius:6px;font-family:'JetBrains Mono',monospace;min-width:42px;}
.bp-item-hot{background:rgba(220,38,38,.15);border:1px solid #b91c1c;color:#fecaca;}
.bp-item-cold{background:rgba(59,130,246,.15);border:1px solid #2563eb;color:#bfdbfe;}
.bp-item-num{font-size:.78em;font-weight:700;}
.bp-item-pct{font-size:.56em;opacity:.85;}
.bp-empty{font-size:.66em;color:var(--DIM);padding:8px;text-align:center;font-family:'JetBrains Mono',monospace;}
.sw-toggle{position:relative;width:36px;height:20px;flex-shrink:0;}
.sw-toggle input{opacity:0;width:0;height:0;}
.sw-slider{position:absolute;inset:0;background:#1e293b;border-radius:20px;cursor:pointer;transition:.2s;border:1px solid var(--BDR);}
.sw-slider:before{position:absolute;content:'';height:14px;width:14px;left:2px;bottom:2px;background:#64748b;border-radius:50%;transition:.2s;}
input:checked+.sw-slider{background:var(--RIVER);border-color:var(--RIVER);}
input:checked+.sw-slider:before{transform:translateX(15px);background:#fff;}
.ctrls{display:flex;flex-wrap:wrap;gap:7px;justify-content:center;padding:10px 8px 12px;border-top:1px solid var(--BDR);border-bottom:1px solid var(--BDR);background:rgba(10,22,40,.4);border-radius:9px;margin:8px 0;}
.ctrls2{display:flex;flex-wrap:wrap;gap:6px;justify-content:center;padding:6px 0;}
.btn{padding:7px 13px;border:1px solid var(--BDR);border-radius:3px;cursor:pointer;font-weight:600;font-size:.74em;font-family:'Inter','Noto Sans JP',sans-serif;transition:all .12s;white-space:nowrap;letter-spacing:.5px;min-height:32px;display:inline-flex;align-items:center;gap:4px;background:#16161c;color:var(--TX);}
.btn:hover{filter:brightness(1.15);border-color:var(--ACC);}
.btn:active{filter:brightness(.92);}
.b-or{background:#3a1f0a;color:#fbbf24;border-color:#7a420f}
.b-bl{background:#0e1c2a;color:#60a5fa;border-color:#1e3a52}
.b-gy{background:#1a1a22;color:var(--TXD);border-color:var(--BDR)}
.b-gr{background:#0a2620;color:#34d399;border-color:#0e3a30}
.b-pu{background:#1f0e2a;color:#c084fc;border-color:#3a1c52}
.b-pk{background:#2a0e1c;color:#f472b6;border-color:#4a1830}
.b-nb{background:#0e1830;color:#93c5fd;border-color:#1e2e58}
.b-rd{background:#2a0e0e;color:#fca5a5;border-color:#5c1f1f}
.b-cy{background:#0a2030;color:#67e8f9;border-color:#0e3a52}
.b-vio{background:linear-gradient(135deg,#2a1240,#3d1a52);color:#d8b4fe;border-color:#6d28d9;}
.atbar{display:flex;flex-wrap:wrap;gap:5px;align-items:center;justify-content:center;padding:6px 8px;background:#0f0f15;border-radius:4px;border:1px solid var(--BDR);}
.mbadge{padding:3px 8px;border-radius:3px;font-size:.62em;font-weight:700;font-family:'Inter',sans-serif;letter-spacing:1px;white-space:nowrap}
.mbv{background:rgba(245,183,58,.12);color:var(--ACC);border:1px solid rgba(245,183,58,.3)}
.mba{background:rgba(239,68,68,.12);color:#f87171;border:1px solid rgba(239,68,68,.3)}
.mbe{background:rgba(245,158,11,.12);color:var(--WRN);border:1px solid rgba(245,158,11,.3)}
.mbp{background:rgba(168,85,247,.12);color:var(--PREDICT);border:1px solid rgba(168,85,247,.4);}
.swg{display:flex;gap:3px;align-items:center}
.sw{width:16px;height:16px;border-radius:3px;cursor:pointer;border:2px solid transparent;}
.sw.on{border-color:#fff!important}
.sel{background:#1e293b;color:var(--TX);border:1px solid var(--BDR);border-radius:5px;padding:3px 6px;font-size:.68em;cursor:pointer}
.szbar{display:flex;align-items:center;gap:6px;flex-wrap:wrap;padding:5px 8px;background:rgba(10,22,40,.8);border-radius:8px;}
.szbar label{font-size:.66em;color:var(--TXD);font-family:'JetBrains Mono',monospace}
.szbtn{padding:2px 7px;border:1px solid var(--BDR);border-radius:3px;cursor:pointer;background:#16161c;color:var(--TXD);font-size:.63em;font-family:'JetBrains Mono',monospace;}
.szbtn:hover{border-color:var(--ACC);color:var(--ACC)}.szbtn.on{background:var(--ACC);color:#000;border-color:var(--ACC)}
#szdisp{font-size:.7em;color:var(--P);font-family:'JetBrains Mono',monospace;min-width:36px}
#chart-vp{position:relative;width:100vw;max-width:100vw;height:min(72vh,720px);overflow:hidden;background:#0a0a0e;border-radius:0;border:none;margin-left:calc(50% - 50vw);margin-right:calc(50% - 50vw);touch-action:none;cursor:grab;}
#chart-vp:active{cursor:grabbing}
#chart-vp.vp-fs{position:fixed;top:0;left:0;width:100vw;height:100vh;height:100dvh;z-index:9000;border-radius:0;border:none;margin:0;}
#chart-vp.vp-fs .nav-hint{font-size:.7em;}
.vp-tools{position:absolute;top:8px;right:8px;z-index:50;display:flex;flex-direction:column;gap:5px;}
.vp-fs-btn{background:rgba(10,4,30,.88);border:1px solid var(--P);color:var(--P);padding:7px 10px;border-radius:7px;font-family:'JetBrains Mono',monospace;font-size:.62em;cursor:pointer;letter-spacing:1px;backdrop-filter:blur(4px);box-shadow:0 2px 8px rgba(0,0,0,.4);transition:all .15s;min-width:78px;text-align:center;}
.vp-fs-btn:hover{background:rgba(34,211,238,.18);border-color:var(--ACC);color:var(--ACC);transform:translateY(-1px);}
.vp-fs-btn:active{transform:translateY(0);}
.vp-fs-btn.vp-fs-primary{border-color:var(--PREDICT);color:#f0abfc;}
.vp-fs-btn.vp-fs-primary:hover{background:rgba(168,85,247,.2);}
#chart-vp.vp-fs .vp-fs-btn{background:rgba(0,0,0,.78);}
#chart-stage{position:absolute;top:0;left:0;transform-origin:0 0;will-change:transform;backface-visibility:hidden;}
#riverCanvas,#predictCanvas,#arrowCanvas,#plineCanvas{display:block;position:absolute;top:0;left:0;}
#predictCanvas,#arrowCanvas,#plineCanvas{pointer-events:none}
.nav-hint{position:absolute;bottom:6px;right:10px;font-size:.6em;color:rgba(148,163,184,.45);font-family:'JetBrains Mono',monospace;pointer-events:none;z-index:10;}
.pline-hud{position:absolute;top:8px;left:10px;display:none;font-size:.62em;color:var(--PREDICT);background:rgba(10,4,30,.85);border:1px solid var(--PREDICT);padding:5px 9px;border-radius:6px;font-family:'JetBrains Mono',monospace;z-index:11;line-height:1.5;max-width:280px;}
.pline-hud.on{display:block;}
.pline-hud b{color:#f0abfc;}
.heatmap-card,.statchart-card{background:#1e293b;border-radius:12px;padding:12px;border:1px solid var(--BDR);}
.heatmap-card{border-color:var(--RIVER);}
.heatmap-card h3,.statchart-card h3{margin-bottom:8px;font-family:'Inter',sans-serif;font-size:.74rem;letter-spacing:1px;}
.heatmap-card h3{color:var(--RIVER);}
.statchart-card h3{color:var(--P);}
#heatCanvas{display:block;width:100%;height:78px;border-radius:6px;background:#000;}
/* === EV RANKING === */
.ev-rank-grid{display:grid;grid-template-columns:repeat(5,1fr);gap:6px;margin-top:4px;}
@media(max-width:600px){.ev-rank-grid{grid-template-columns:repeat(5,1fr);gap:4px;}}
.ev-rank-card{position:relative;background:linear-gradient(140deg,rgba(10,22,40,.95),rgba(20,40,70,.85));border:1px solid var(--BDR);border-radius:9px;padding:8px 4px 6px;text-align:center;font-family:'JetBrains Mono',monospace;transition:transform .15s;overflow:hidden;}
.ev-rank-card::before{content:'';position:absolute;left:0;right:0;bottom:0;height:3px;background:#3b82f6;}
.ev-rank-card:hover{transform:translateY(-2px);}
.ev-rank-card.r1{background:linear-gradient(140deg,rgba(220,38,38,.25),rgba(127,29,29,.45));border-color:#f87171;box-shadow:0 0 16px rgba(239,68,68,.35),inset 0 1px 0 rgba(255,255,255,.1);}
.ev-rank-card.r1::before{background:linear-gradient(90deg,#fbbf24,#ef4444,#fbbf24);height:4px;box-shadow:0 0 10px #fbbf24;}
.ev-rank-card.r2{background:linear-gradient(140deg,rgba(245,158,11,.18),rgba(146,64,14,.4));border-color:#fbbf24;box-shadow:0 0 12px rgba(251,191,36,.25);}
.ev-rank-card.r2::before{background:#fbbf24;}
.ev-rank-card.r3{background:linear-gradient(140deg,rgba(168,85,247,.18),rgba(91,33,182,.4));border-color:#c084fc;}
.ev-rank-card.r3::before{background:#c084fc;}
.ev-rank-card.r4,.ev-rank-card.r5{background:linear-gradient(140deg,rgba(34,211,238,.12),rgba(14,116,144,.3));border-color:var(--ACC);}
.ev-rank-card.r4::before,.ev-rank-card.r5::before{background:var(--ACC);}
.ev-rank-rank{position:absolute;top:2px;left:4px;font-size:.55em;color:rgba(255,255,255,.55);font-weight:700;letter-spacing:1px;}
.ev-rank-num{font-family:'Inter',sans-serif;font-size:1.55em;font-weight:900;color:#fff;line-height:1.05;margin-top:6px;text-shadow:0 0 6px rgba(0,0,0,.6);}
.ev-rank-card.r1 .ev-rank-num{color:#fef3c7;text-shadow:0 0 12px rgba(251,191,36,.7);}
.ev-rank-card.r2 .ev-rank-num{color:#fde68a;text-shadow:0 0 8px rgba(251,191,36,.5);}
.ev-rank-card.r3 .ev-rank-num{color:#e9d5ff;text-shadow:0 0 8px rgba(168,85,247,.5);}
.ev-rank-score{font-size:.55em;color:rgba(255,255,255,.7);margin-top:2px;letter-spacing:.5px;}
.ev-rank-bar{height:3px;background:rgba(255,255,255,.1);border-radius:2px;overflow:hidden;margin-top:4px;}
.ev-rank-bar-fill{height:100%;background:linear-gradient(90deg,#3b82f6,#fbbf24,#ef4444);}
.ev-rank-badge{position:absolute;top:1px;right:2px;font-size:.55em;line-height:1;}
.ev-rank-summary{display:flex;justify-content:space-between;align-items:center;margin-top:8px;padding:6px 10px;background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:6px;font-size:.62em;color:var(--TXD);font-family:'JetBrains Mono',monospace;flex-wrap:wrap;gap:4px;}
.ev-rank-summary b{color:var(--ACC);}
.ev-rank-picks{display:inline-flex;gap:3px;flex-wrap:wrap;}
.ev-rank-pick-num{display:inline-block;background:rgba(6,182,212,.2);border:1px solid var(--ACC);color:#67e8f9;padding:1px 6px;border-radius:5px;font-weight:700;}
.mosan-panel{background:linear-gradient(135deg,rgba(251,191,36,.10),rgba(168,85,247,.08),rgba(34,211,238,.06));border:1px solid var(--DELTA);border-radius:10px;padding:10px;margin-top:8px;position:relative;overflow:hidden;}
.mosan-panel::before{content:'';position:absolute;top:-2px;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,var(--DELTA),var(--PRP),var(--DELTA),transparent);box-shadow:0 0 14px var(--DELTA);}
.mosan-title{font-family:'Inter',sans-serif;font-size:.8rem;color:var(--DELTA);letter-spacing:2px;font-weight:900;margin-bottom:3px;text-shadow:0 0 10px rgba(253,230,138,.6),0 0 22px rgba(168,85,247,.4);display:flex;align-items:center;gap:8px;flex-wrap:wrap;}
.mosan-sub{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-bottom:8px;line-height:1.6;}
.mosan-sub b{color:var(--DELTA);}
.mosan-empty{font-size:.7em;color:var(--DIM);text-align:center;padding:14px;font-family:'JetBrains Mono',monospace;}
.mosan-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:8px;}
.mosan-card{background:rgba(10,22,40,.8);border:1px solid var(--BDR);border-radius:8px;padding:9px 10px;position:relative;transition:all .2s;}
.mosan-card.rank1{border-color:var(--DELTA);background:linear-gradient(135deg,rgba(251,191,36,.15),rgba(10,22,40,.85));box-shadow:0 0 14px rgba(251,191,36,.25);}
.mosan-card.rank2{border-color:var(--PRP);background:linear-gradient(135deg,rgba(168,85,247,.10),rgba(10,22,40,.85));}
.mosan-card.rank3{border-color:var(--ACC);}
.mosan-rank{position:absolute;top:-7px;right:7px;background:var(--DELTA);color:#000;font-size:.6em;font-weight:900;padding:2px 7px;border-radius:10px;font-family:'JetBrains Mono',monospace;letter-spacing:1px;}
.mosan-card.rank2 .mosan-rank{background:var(--PRP);color:#fff;}
.mosan-card.rank3 .mosan-rank{background:var(--ACC);color:#000;}
.mosan-num{font-family:'Inter',sans-serif;font-size:1.4em;font-weight:900;color:var(--DELTA);text-shadow:0 0 10px rgba(251,191,36,.6);display:inline-block;}
.mosan-card.rank2 .mosan-num{color:var(--PRP);text-shadow:0 0 10px rgba(168,85,247,.6);}
.mosan-card.rank3 .mosan-num{color:var(--ACC);text-shadow:0 0 8px rgba(34,211,238,.5);}
.mosan-pattern{font-family:'JetBrains Mono',monospace;font-size:.78em;color:var(--TX);margin-top:4px;letter-spacing:1px;}
.mosan-pattern .mhi{color:var(--DELTA);font-weight:700;}
.mosan-pattern .mar{color:var(--PRP);font-weight:700;margin:0 3px;}
.mosan-meta{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-top:5px;line-height:1.5;}
.mosan-meta b{color:var(--DELTA);}
.mosan-ex{font-size:.55em;color:var(--DIM);font-family:'JetBrains Mono',monospace;margin-top:4px;padding-top:4px;border-top:1px dashed var(--BDR);line-height:1.6;}
.mosan-ex .exr{color:var(--ACC);}
.mosan-badge{display:inline-block;background:rgba(251,191,36,.2);border:1px solid var(--DELTA);color:var(--DELTA);padding:1px 6px;border-radius:4px;font-size:.55em;font-family:'JetBrains Mono',monospace;font-weight:700;margin-left:4px;}
.month-panel{background:linear-gradient(135deg,rgba(34,211,238,.06),rgba(125,211,252,.04));border:1px solid var(--ACC);border-radius:10px;padding:10px;}
.month-title{font-family:'Inter',sans-serif;font-size:.78rem;color:var(--ACC);letter-spacing:2px;font-weight:900;margin-bottom:5px;text-shadow:0 0 8px rgba(34,211,238,.5);}
.month-sub{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-bottom:8px;line-height:1.6;}
.month-stat-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:6px;margin-bottom:10px;}
.month-stat{background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:6px;padding:6px 9px;}
.month-stat-label{font-size:.55em;color:var(--DIM);font-family:'JetBrains Mono',monospace;letter-spacing:1px;}
.month-stat-val{font-size:.95em;color:var(--ACC);font-family:'JetBrains Mono',monospace;font-weight:700;margin-top:2px;}
.month-stat-cmp{font-size:.55em;font-family:'JetBrains Mono',monospace;margin-top:3px;}
.month-stat-cmp.up{color:#fca5a5;}
.month-stat-cmp.dn{color:#93c5fd;}
.month-stat-cmp.eq{color:var(--DIM);}
.month-tail-row{display:grid;grid-template-columns:repeat(10,1fr);gap:3px;margin-top:5px;}
.month-tail-cell{aspect-ratio:1.2;display:flex;flex-direction:column;align-items:center;justify-content:center;background:#1e293b;border:1px solid var(--BDR);border-radius:4px;font-family:'JetBrains Mono',monospace;font-size:.6em;font-weight:700;}
.month-tail-cell .ttn{color:var(--TXD);font-size:.85em;}
.month-tail-cell .ttc{font-size:1.1em;margin-top:1px;}
.month-tail-cell.t-hot{background:rgba(220,38,38,.18);border-color:#b91c1c;color:#fecaca;}
.month-tail-cell.t-warm{background:rgba(245,158,11,.18);border-color:#d97706;color:#fde68a;}
.month-tail-cell.t-cold{background:rgba(59,130,246,.15);border-color:#2563eb;color:#bfdbfe;}
.month-tail-cell.t-zero{background:rgba(31,41,55,.5);color:#475569;}
.kuse-panel{background:linear-gradient(135deg,rgba(168,85,247,.08),rgba(217,70,239,.04));border:1px solid var(--PRP);border-radius:10px;padding:10px;}
.kuse-title{font-family:'Inter',sans-serif;font-size:.78rem;color:var(--PRP);letter-spacing:2px;font-weight:900;margin-bottom:5px;text-shadow:0 0 8px rgba(168,85,247,.5);}
.kuse-sub{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-bottom:8px;line-height:1.6;}
.kuse-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(45px,1fr));gap:4px;margin-bottom:10px;}
.kuse-cell{aspect-ratio:1;display:flex;align-items:center;justify-content:center;border-radius:5px;background:#1e293b;border:1px solid var(--BDR);color:var(--TXD);cursor:pointer;font-family:'JetBrains Mono',monospace;font-size:.7em;font-weight:700;transition:all .12s;user-select:none;}
.kuse-cell:hover{border-color:var(--PRP);color:var(--PRP);transform:scale(1.05);}
.kuse-cell.k-active{background:var(--PRP);color:#fff;border-color:var(--PRP);box-shadow:0 0 12px rgba(168,85,247,.6);}
.kuse-detail{background:rgba(10,22,40,.85);border:1px solid var(--PRP);border-radius:8px;padding:10px;font-size:.7em;color:var(--TX);line-height:1.7;font-family:'JetBrains Mono',monospace;min-height:80px;}
.kuse-detail h4{font-family:'Inter',sans-serif;color:var(--PRP);font-size:.95em;margin-bottom:6px;letter-spacing:1px;}
.kuse-detail .kk{color:var(--ACC);font-weight:700;}
.kuse-detail .kkh{color:#fca5a5;font-weight:700;}
.kuse-detail .kkc{color:#93c5fd;font-weight:700;}
.kuse-detail .kdiv{height:1px;background:linear-gradient(90deg,transparent,var(--BDR),transparent);margin:5px 0;}
.kuse-empty{color:var(--DIM);text-align:center;padding:20px;font-style:italic;}
.tail-panel{background:linear-gradient(135deg,rgba(245,158,11,.08),rgba(251,191,36,.04));border:1px solid var(--WRN);border-radius:10px;padding:10px;}
.tail-title{font-family:'Inter',sans-serif;font-size:.78rem;color:var(--WRN);letter-spacing:2px;font-weight:900;margin-bottom:5px;text-shadow:0 0 8px rgba(245,158,11,.5);}
.tail-sub{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-bottom:8px;line-height:1.6;}
.tail-bar-row{display:flex;align-items:flex-end;gap:5px;margin-bottom:10px;height:80px;padding:0 4px;}
.tail-bar-col{flex:1;display:flex;flex-direction:column;align-items:center;gap:3px;}
.tail-bar-bar{width:100%;background:linear-gradient(180deg,var(--WRN),#d97706);border-radius:3px 3px 0 0;min-height:2px;transition:height .3s;position:relative;}
.tail-bar-bar.tb-hot{background:linear-gradient(180deg,#ef4444,#b91c1c);}
.tail-bar-bar.tb-cold{background:linear-gradient(180deg,#3b82f6,#1e40af);}
.tail-bar-cnt{position:absolute;top:-14px;left:50%;transform:translateX(-50%);font-size:.55em;color:var(--TX);font-family:'JetBrains Mono',monospace;font-weight:700;}
.tail-bar-lbl{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;}
.tail-summary{font-size:.65em;color:var(--TXD);font-family:'JetBrains Mono',monospace;line-height:1.7;background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:6px;padding:7px 9px;}
.tail-summary b{color:var(--WRN);}
.tail-summary .thot{color:#fca5a5;font-weight:700;}
.tail-summary .tcold{color:#93c5fd;font-weight:700;}
.pair-panel{background:linear-gradient(135deg,rgba(110,231,183,.08),rgba(34,211,238,.04));border:1px solid #6ee7b7;border-radius:10px;padding:10px;}
.pair-title{font-family:'Inter',sans-serif;font-size:.78rem;color:#6ee7b7;letter-spacing:2px;font-weight:900;margin-bottom:5px;text-shadow:0 0 8px rgba(110,231,183,.4);}
.pair-sub{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-bottom:8px;line-height:1.6;}
.pair-tabs{display:flex;gap:4px;margin-bottom:8px;flex-wrap:wrap;}
.pair-tab{padding:3px 10px;border:1px solid var(--BDR);border-radius:5px;cursor:pointer;background:#1e293b;color:#cbd5e1;font-size:.62em;font-weight:700;font-family:'JetBrains Mono',monospace;letter-spacing:1px;}
.pair-tab:hover{border-color:#6ee7b7;color:#6ee7b7;}
.pair-tab.on{background:#6ee7b7;color:#01060f;border-color:#6ee7b7;}
.pair-list{display:flex;flex-direction:column;gap:5px;max-height:280px;overflow-y:auto;}
.pair-row{display:flex;align-items:center;gap:8px;background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:6px;padding:6px 9px;font-family:'JetBrains Mono',monospace;font-size:.7em;}
.pair-row:hover{border-color:#6ee7b7;}
.pair-rank{font-size:.6em;color:var(--DIM);min-width:24px;font-weight:700;}
.pair-nums{display:flex;gap:5px;align-items:center;}
.pair-num{display:inline-block;background:rgba(110,231,183,.15);border:1px solid #6ee7b7;color:#6ee7b7;padding:1px 7px;border-radius:5px;font-weight:700;font-size:.95em;}
.pair-link{color:var(--DIM);font-size:.85em;}
.pair-cnt{margin-left:auto;color:var(--TX);font-weight:700;}
.pair-cnt b{color:#6ee7b7;}
.pair-bar{flex:1;height:5px;background:rgba(255,255,255,.05);border-radius:3px;overflow:hidden;min-width:50px;}
.pair-bar-fill{height:100%;background:linear-gradient(90deg,#6ee7b7,#22d3ee);}
.geo-panel{background:linear-gradient(135deg,rgba(244,114,182,.08),rgba(168,85,247,.06),rgba(34,211,238,.04));border:1px solid #f472b6;border-radius:10px;padding:10px;}
.geo-title{font-family:'Inter',sans-serif;font-size:.78rem;color:#f472b6;letter-spacing:2px;font-weight:900;margin-bottom:5px;text-shadow:0 0 8px rgba(244,114,182,.5);}
.geo-sub{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-bottom:8px;line-height:1.6;}
.geo-tabs{display:flex;gap:4px;margin-bottom:8px;flex-wrap:wrap;}
.geo-tab{padding:3px 10px;border:1px solid var(--BDR);border-radius:5px;cursor:pointer;background:#1e293b;color:#cbd5e1;font-size:.62em;font-weight:700;font-family:'JetBrains Mono',monospace;letter-spacing:1px;transition:all .15s;}
.geo-tab:hover{border-color:#f472b6;color:#f472b6;}
.geo-tab.on{background:#f472b6;color:#01060f;border-color:#f472b6;box-shadow:0 0 10px rgba(244,114,182,.4);}
.geo-section{background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:7px;padding:8px 10px;margin-bottom:8px;}
.geo-section-h{font-family:'Inter',sans-serif;font-size:.7em;color:#f472b6;letter-spacing:1.5px;font-weight:700;margin-bottom:5px;}
.geo-current{background:linear-gradient(90deg,rgba(244,114,182,.12),rgba(34,211,238,.06));border:1px solid #f472b6;border-radius:6px;padding:6px 9px;margin-bottom:6px;font-family:'JetBrains Mono',monospace;font-size:.65em;color:var(--TX);line-height:1.7;}
.geo-current b{color:#f9a8d4;}
.geo-tri{display:inline-flex;align-items:center;background:rgba(244,114,182,.15);border:1px solid #f472b6;color:#fbcfe8;padding:2px 8px;border-radius:5px;font-family:'JetBrains Mono',monospace;font-size:.95em;font-weight:700;margin:2px 3px;}
.geo-tri-arrow{color:var(--DIM);margin:0 3px;font-size:.85em;}
.geo-mid{background:rgba(34,211,238,.18);border:1px solid var(--ACC);color:#67e8f9;padding:1px 6px;border-radius:4px;font-weight:900;}
.geo-pred{display:inline-block;background:rgba(245,158,11,.25);border:1.5px solid var(--WRN);color:#fde68a;padding:2px 9px;border-radius:6px;font-family:'JetBrains Mono',monospace;font-weight:900;margin:2px 3px;font-size:1.05em;}
.geo-rank{display:inline-block;background:#f472b6;color:#01060f;font-size:.55em;font-weight:900;padding:1px 5px;border-radius:8px;font-family:'JetBrains Mono',monospace;margin-right:4px;letter-spacing:1px;}
.geo-cluster-bar{display:flex;height:18px;background:rgba(255,255,255,.04);border-radius:4px;overflow:hidden;margin:4px 0;position:relative;border:1px solid var(--BDR);}
.geo-cluster-cell{flex:1;border-right:1px solid rgba(255,255,255,.05);transition:all .2s;cursor:default;position:relative;}
.geo-cluster-cell.hot{background:linear-gradient(180deg,#ef4444,#b91c1c);}
.geo-cluster-cell.warm{background:linear-gradient(180deg,#f59e0b,#d97706);}
.geo-cluster-cell.cold{background:rgba(59,130,246,.4);}
.geo-empty{color:var(--DIM);text-align:center;padding:14px;font-family:'JetBrains Mono',monospace;font-size:.7em;}
.geomosan-panel{background:linear-gradient(135deg,rgba(34,211,238,.08),rgba(168,85,247,.06));border:1px solid var(--ACC);border-radius:10px;padding:10px;}
.geomosan-title{font-family:'Inter',sans-serif;font-size:.78rem;color:var(--ACC);letter-spacing:2px;font-weight:900;margin-bottom:5px;text-shadow:0 0 8px rgba(34,211,238,.5);}
.geomosan-sub{font-size:.6em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-bottom:8px;line-height:1.6;}
.geomosan-section{background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:7px;padding:8px 10px;margin-bottom:8px;}
.geomosan-h{font-family:'Inter',sans-serif;font-size:.7em;letter-spacing:1.5px;font-weight:700;margin-bottom:6px;display:flex;align-items:center;gap:6px;}
.geomosan-h.h-tri{color:#fde68a;}
.geomosan-h.h-sym{color:#67e8f9;}
.geomosan-h.h-clu{color:#c084fc;}
.geomosan-row{display:flex;align-items:center;gap:5px;flex-wrap:wrap;font-family:'JetBrains Mono',monospace;font-size:.7em;padding:4px 0;border-top:1px dashed rgba(255,255,255,.05);}
.geomosan-row:first-child{border-top:none;}
.geomosan-num{display:inline-block;background:rgba(34,211,238,.15);border:1px solid var(--ACC);color:var(--ACC);padding:1px 7px;border-radius:5px;font-weight:700;font-family:'JetBrains Mono',monospace;}
.geomosan-num.gm-pred{background:rgba(251,191,36,.3);border:1.5px solid var(--DELTA);color:#fde68a;font-size:1.1em;box-shadow:0 0 8px rgba(251,191,36,.4);}
.geomosan-num.gm-tri{background:rgba(245,158,11,.15);border:1px solid var(--WRN);color:var(--WRN);}
.geomosan-num.gm-sym{background:rgba(103,232,249,.15);border:1px solid #67e8f9;color:#67e8f9;}
.geomosan-num.gm-clu{background:rgba(192,132,252,.15);border:1px solid #c084fc;color:#c084fc;}
.geomosan-link{color:var(--DIM);font-size:.85em;}
.geomosan-meta{font-size:.6em;color:var(--TXD);margin-left:auto;}
.geomosan-meta b{color:var(--ACC);}
.geomosan-summary{background:linear-gradient(90deg,rgba(251,191,36,.12),rgba(168,85,247,.08));border:1px solid var(--DELTA);border-radius:7px;padding:8px 10px;margin-top:6px;font-family:'JetBrains Mono',monospace;font-size:.7em;line-height:1.7;}
.geomosan-summary b{color:var(--DELTA);}
.geomosan-empty{font-size:.65em;color:var(--DIM);text-align:center;padding:8px;font-style:italic;}
.heat-labels{display:flex;justify-content:space-between;font-size:.58em;color:var(--DIM);margin-top:4px;font-family:'JetBrains Mono',monospace;padding:0 4px;}
.heat-legend{display:flex;gap:14px;font-size:.6em;color:var(--TXD);margin-top:8px;justify-content:center;font-family:'JetBrains Mono',monospace;flex-wrap:wrap;}
.heat-legend-item{display:flex;align-items:center;gap:4px;}
.heat-dot{width:10px;height:10px;border-radius:50%;}
#statChart{max-height:220px;}
/* === N-S 多層ヒートマップ === */
.ns-empty{font-size:.7em;color:var(--DIM);text-align:center;padding:18px;font-family:'JetBrains Mono',monospace;font-style:italic;}
.ns-status{font-family:'JetBrains Mono',monospace;font-size:.62em;color:#c4b5fd;background:linear-gradient(90deg,rgba(168,85,247,.18),rgba(217,70,239,.06));border:1px solid var(--PRP);border-radius:7px;padding:6px 10px;margin-bottom:8px;line-height:1.6;}
.ns-status b{color:#f0abfc;}
.ns-row{display:grid;grid-template-columns:78px repeat(6,1fr);gap:5px;margin-bottom:5px;align-items:center;}
.ns-row-label{display:flex;align-items:center;gap:5px;font-family:'Inter',sans-serif;font-size:.62em;font-weight:900;letter-spacing:1px;padding:6px 7px;border-radius:6px;text-align:center;justify-content:center;line-height:1.2;}
.ns-row-press .ns-row-label{background:rgba(239,68,68,.18);border:1px solid #ef4444;color:#fecaca;}
.ns-row-visc  .ns-row-label{background:rgba(34,197,94,.18);border:1px solid #22c55e;color:#bbf7d0;}
.ns-row-force .ns-row-label{background:rgba(245,158,11,.18);border:1px solid #f59e0b;color:#fde68a;}
.ns-row-vortex .ns-row-label{background:rgba(168,85,247,.20);border:1px solid #a855f7;color:#e9d5ff;}
.ns-cell{aspect-ratio:1;display:flex;flex-direction:column;align-items:center;justify-content:center;border-radius:7px;font-family:'JetBrains Mono',monospace;font-weight:900;border:1px solid var(--BDR);transition:transform .12s, box-shadow .15s;cursor:default;position:relative;overflow:hidden;}
.ns-cell:hover{transform:translateY(-2px);}
.ns-cell.empty{background:rgba(20,30,50,.4);border-style:dashed;color:var(--DIM);}
.ns-cell .ns-num{font-size:1.05em;line-height:1;color:#fff;text-shadow:0 1px 2px rgba(0,0,0,.6);}
.ns-cell .ns-pct{font-size:.55em;line-height:1;margin-top:3px;opacity:.85;color:#fff;}
.ns-cell.rank1{box-shadow:0 0 12px currentColor, inset 0 0 0 1px rgba(255,255,255,.3);}
.ns-cell.rank1::before{content:'#1';position:absolute;top:1px;left:3px;font-size:.5em;font-weight:900;color:rgba(255,255,255,.85);letter-spacing:.5px;}
.ns-row-press .ns-cell{background:linear-gradient(135deg,#7f1d1d,#450a0a);color:#ef4444;}
.ns-row-press .ns-cell.rank1{background:linear-gradient(135deg,#dc2626,#7f1d1d);}
.ns-row-visc  .ns-cell{background:linear-gradient(135deg,#14532d,#052e16);color:#22c55e;}
.ns-row-visc  .ns-cell.rank1{background:linear-gradient(135deg,#16a34a,#14532d);}
.ns-row-force .ns-cell{background:linear-gradient(135deg,#92400e,#451a03);color:#f59e0b;}
.ns-row-force .ns-cell.rank1{background:linear-gradient(135deg,#d97706,#92400e);}
.ns-row-vortex .ns-cell{background:linear-gradient(135deg,#581c87,#2e1065);color:#a855f7;}
.ns-row-vortex .ns-cell.rank1{background:linear-gradient(135deg,#7e22ce,#581c87);}
/* 既出 (本数字に既に入ってる数字) は枠を金色に */
.ns-cell.in-best{outline:2px solid var(--DELTA);outline-offset:-2px;}
.ns-cell.in-best::after{content:'★';position:absolute;top:1px;right:3px;font-size:.55em;color:var(--DELTA);text-shadow:0 0 4px rgba(0,0,0,.7);}
.ns-summary{display:flex;flex-wrap:wrap;align-items:center;gap:6px;background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:7px;padding:6px 10px;margin-top:8px;font-family:'JetBrains Mono',monospace;font-size:.62em;color:var(--TXD);line-height:1.6;}
.ns-summary b{color:var(--PRP);}
.ns-summary .ns-pick-num{display:inline-block;background:rgba(168,85,247,.2);border:1px solid var(--PRP);color:#e9d5ff;padding:1px 6px;border-radius:5px;font-weight:700;}
/* === N-S ゾーン分け表示 === */
.ns-force-block{margin-bottom:10px;background:rgba(10,22,40,.4);border:1px solid var(--BDR);border-radius:8px;padding:6px 7px 7px;}
.ns-force-block.ns-row-press  {border-left:3px solid #ef4444;}
.ns-force-block.ns-row-visc   {border-left:3px solid #22c55e;}
.ns-force-block.ns-row-force  {border-left:3px solid #f59e0b;}
.ns-force-block.ns-row-vortex {border-left:3px solid #a855f7;}
.ns-force-head{font-family:'Inter',sans-serif;font-size:.62em;letter-spacing:1px;font-weight:900;padding:3px 6px 5px;color:var(--TX);}
.ns-row-press .ns-force-head  {color:#fca5a5;}
.ns-row-visc .ns-force-head   {color:#86efac;}
.ns-row-force .ns-force-head  {color:#fde68a;}
.ns-row-vortex .ns-force-head {color:#c4b5fd;}
.ns-zone-row{display:grid;gap:4px;margin:3px 0;align-items:center;}
.ns-zone-label{font-family:'JetBrains Mono',monospace;font-size:.58em;font-weight:700;letter-spacing:.5px;padding:5px 4px;border-radius:5px;border:1px solid;text-align:center;line-height:1.1;}
.module{margin:12px 0;border:2px solid var(--BDR);border-radius:14px;background:linear-gradient(180deg,rgba(6,24,46,.85),rgba(2,12,26,.92));overflow:hidden;}
.module-head{display:flex;align-items:center;justify-content:space-between;padding:12px 16px;cursor:pointer;user-select:none;background:rgba(10,22,40,.5);}
.module-head:hover{background:rgba(34,211,238,.08);}
.module-head-left{display:flex;align-items:center;gap:11px;}
.module-icon{font-size:1.3em;}
.module-title{font-family:'Inter',sans-serif;font-size:.9em;letter-spacing:2.2px;font-weight:900;}
.module-sub{font-family:'JetBrains Mono',monospace;font-size:.58em;color:var(--DIM);letter-spacing:1px;margin-top:2px;}
.module-arrow{font-size:.95em;transition:transform .25s;color:var(--TXD);}
.module.open .module-arrow{transform:rotate(90deg);color:var(--ACC);}
.module-body{display:none;padding:12px 14px 16px;border-top:1px solid var(--BDR);}
.module.open .module-body{display:block;}
.module.m-grid{border-color:rgba(125,211,252,.4);}
.module.m-grid.open{border-color:var(--P);box-shadow:0 0 18px rgba(125,211,252,.2);}
.module.m-grid .module-title{color:var(--P);}
.module.m-pred{border-color:rgba(34,211,238,.4);}
.module.m-pred.open{border-color:var(--RIVER);box-shadow:0 0 18px rgba(34,211,238,.2);}
.module.m-pred .module-title{color:var(--RIVER);}
.module.m-anal{border-color:rgba(168,85,247,.4);}
.module.m-anal.open{border-color:var(--PRP);box-shadow:0 0 18px rgba(103,232,249,.2);}
.module.m-anal .module-title{color:var(--PRP);}
.module.m-grid{overflow:visible;}
.module.m-grid .module-body{padding-left:0;padding-right:0;padding-bottom:0;}
.module.m-grid .module-body > *{padding-left:14px;padding-right:14px;}
.module.m-grid .module-body > .full-bleed{padding-left:0;padding-right:0;width:100vw;position:relative;left:50%;right:50%;margin-left:-50vw;margin-right:-50vw;max-width:100vw;}
.module.m-grid .full-bleed{border:none;border-radius:0;background:transparent;margin-bottom:0;}
.module.m-grid .full-bleed .fold-body{padding:0;border:none;}
.module.m-grid .full-bleed .fold-head{margin:0;border-radius:0;background:rgba(10,22,40,.65);border-top:1px solid var(--BDR);padding-left:18px;padding-right:18px;}
.module.m-grid .full-bleed #chart-vp{margin-left:0;margin-right:0;width:100%;border:none;}
.pbox,.pbox-delta,.pbox-river,.pbox-ns{background:var(--BG);padding:8px 11px;border-radius:8px;margin-top:6px;font-family:'JetBrains Mono',monospace;font-size:.86em;letter-spacing:2px;cursor:pointer;position:relative;transition:transform .12s;}
.pbox{border-left:4px solid var(--ACC);color:#a7f3d0;}
.pbox-delta{border-left:4px solid var(--DELTA);color:#fbcfe8;}
.pbox-river{border-left:4px solid var(--RIVER);color:#a5f3fc;}
.pbox-ns{border-left:4px solid var(--PRP);color:#e9d5ff;background:linear-gradient(135deg,rgba(168,85,247,.08),var(--BG));}
.pbox:hover,.pbox-delta:hover,.pbox-river:hover,.pbox-ns:hover{transform:translateX(2px);}
.binfo,.binfo-delta,.binfo-river,.binfo-ns{font-size:.66em;display:block;margin-top:3px;font-family:'Noto Sans JP',sans-serif;letter-spacing:0;}
.binfo{color:var(--TXD);}
.binfo-delta{color:#f9a8d4;}
.binfo-river{color:#67e8f9;}
.binfo-ns{color:#c4b5fd;}
.why-block{margin-top:7px;padding-top:7px;border-top:1px dashed rgba(125,211,252,.18);font-family:'Noto Sans JP',sans-serif;letter-spacing:0;font-size:.7em;line-height:1.55;color:var(--TXD);}
.why-row{display:flex;align-items:flex-start;gap:6px;margin:2px 0;flex-wrap:wrap;}
.why-num{display:inline-flex;align-items:center;justify-content:center;width:26px;height:26px;border-radius:6px;background:#0a1628;border:1px solid var(--BDR);font-family:'JetBrains Mono',monospace;font-weight:700;font-size:.95em;color:#fff;flex-shrink:0;}
.why-num.why-fix{border-color:#fbbf24;color:#fbbf24;background:rgba(251,191,36,.1);}
.why-num.why-myp{border-color:#a78bfa;color:#a78bfa;background:rgba(167,139,250,.1);}
.why-num.why-river{border-color:var(--RIVER);color:var(--RIVER);background:rgba(34,211,238,.1);}
.why-num.why-delta{border-color:var(--DELTA);color:var(--DELTA);background:rgba(253,230,138,.1);}
.why-num.why-ns{border-color:var(--PRP);color:#c4b5fd;background:rgba(168,85,247,.15);}
.why-tags{flex:1;display:flex;flex-wrap:wrap;gap:3px;align-items:center;}
.why-tag{display:inline-block;padding:1px 6px;border-radius:9px;font-size:.85em;font-family:'JetBrains Mono',monospace;border:1px solid var(--BDR);background:rgba(10,22,40,.6);}
.why-tag.t-freq{color:#fca5a5;border-color:#7f1d1d;background:rgba(127,29,29,.2);}
.why-tag.t-mom{color:#fdba74;border-color:#9a3412;background:rgba(154,52,18,.2);}
.why-tag.t-dor{color:#93c5fd;border-color:#1e40af;background:rgba(30,64,175,.2);}
.why-tag.t-slide{color:#86efac;border-color:#166534;background:rgba(22,101,52,.2);}
.why-tag.t-fix{color:#fbbf24;border-color:#92400e;background:rgba(146,64,14,.25);}
.why-tag.t-myp{color:#c4b5fd;border-color:#5b21b6;background:rgba(91,33,182,.25);}
.why-tag.t-bias{color:#fde68a;border-color:#854d0e;background:rgba(133,77,14,.25);}
.why-tag.t-river{color:#a5f3fc;border-color:#155e75;background:rgba(21,94,117,.25);}
.why-tag.t-delta{color:#fbcfe8;border-color:#9d174d;background:rgba(157,23,77,.25);}
.why-tag.t-adj{color:#bfdbfe;border-color:#1e3a8a;background:rgba(30,58,138,.2);}
.why-tag.t-press{color:#fca5a5;border-color:#7f1d1d;background:rgba(127,29,29,.3);}
.why-tag.t-visc{color:#86efac;border-color:#14532d;background:rgba(20,83,45,.3);}
.why-tag.t-force{color:#fde68a;border-color:#92400e;background:rgba(146,64,14,.3);}
.why-tag.t-vortex{color:#c4b5fd;border-color:#5b21b6;background:rgba(91,33,182,.35);}
.why-tag.t-wake{color:#f0abfc;border-color:#86198f;background:rgba(134,25,143,.3);}
.why-score{font-size:.78em;color:var(--DIM);font-family:'JetBrains Mono',monospace;margin-left:auto;}
.why-toggle{display:inline-block;padding:2px 8px;background:rgba(125,211,252,.08);border:1px solid var(--BDR);border-radius:5px;font-size:.85em;cursor:pointer;color:var(--P);font-family:'JetBrains Mono',monospace;margin-top:4px;user-select:none;}
.why-toggle:hover{background:rgba(125,211,252,.15);border-color:var(--P);}
.why-collapsed .why-block{display:none;}
.delta-info,.river-info{border-radius:8px;padding:8px 11px;margin-top:8px;font-size:.68em;font-family:'JetBrains Mono',monospace;line-height:1.8;}
.delta-info{background:rgba(244,114,182,.05);border:1px solid rgba(244,114,182,.2);color:#f9a8d4;}
.delta-info b{color:var(--DELTA)}
.river-info{background:rgba(6,182,212,.05);border:1px solid rgba(6,182,212,.25);color:#67e8f9;}
.river-info b{color:var(--RIVER)}
.ttp{position:fixed;background:var(--CARD);border:1px solid var(--P);border-radius:9px;padding:7px 11px;font-family:'JetBrains Mono',monospace;font-size:.71em;pointer-events:none;z-index:9999;display:none;box-shadow:0 4px 20px rgba(0,0,0,.6);max-width:260px;line-height:1.6}
.tst{position:fixed;bottom:22px;right:22px;background:var(--CARD);border:1px solid var(--P);color:var(--P);padding:8px 14px;border-radius:10px;font-family:'JetBrains Mono',monospace;font-size:.76em;z-index:9999;transform:translateY(20px);opacity:0;transition:all .28s;pointer-events:none}
.tst.on{transform:translateY(0);opacity:1}
.side-panel{display:flex;flex-direction:column;gap:10px;}
.tactical-panel,.prompt-panel{border-radius:12px;padding:12px;}
.tactical-panel{background:rgba(5,150,105,.05);border:1px solid var(--ACC);}
.prompt-panel{background:rgba(168,85,247,.05);border:1px solid var(--PRP);}
.panel-title{font-family:'Inter',sans-serif;font-size:.7rem;font-weight:900;margin-bottom:8px;display:flex;align-items:center;gap:8px;letter-spacing:1px;}
.panel-content{font-size:.7rem;line-height:1.7;color:var(--TXD);min-height:60px;}
.panel-content b{color:var(--TX);}
.eval-btn{width:100%;background:var(--ACC);color:#000;border:none;padding:8px;border-radius:6px;font-family:'Inter',sans-serif;font-size:.6rem;font-weight:bold;cursor:pointer;margin-top:8px;letter-spacing:1px;}
.eval-btn:hover{filter:brightness(1.2);}
.eval-btn.purple{background:var(--PRP);}
#aiPromptPreview{width:100%;height:90px;font-size:.55rem;background:rgba(0,0,0,.3);border:1px solid var(--BDR);border-radius:6px;padding:6px;color:var(--TXD);font-family:'JetBrains Mono',monospace;resize:vertical;}
@media(max-width:1100px){.input-sec{grid-template-columns:1fr 1fr;}}
@media(max-width:760px){.input-sec{grid-template-columns:1fr;}#chart-vp{height:min(78vh,78dvh);}}
/* ===== MANUAL HELP ===== */
.help-btn{display:inline-flex;align-items:center;justify-content:center;width:20px;height:20px;border-radius:50%;background:rgba(34,211,238,.15);border:1px solid var(--ACC);color:var(--ACC);font-size:.7em;font-weight:900;font-family:'Inter',sans-serif;cursor:pointer;margin-left:6px;flex-shrink:0;transition:all .15s;line-height:1;padding:0;user-select:none;}
.help-btn:hover{background:var(--ACC);color:#000;transform:scale(1.1);box-shadow:0 0 10px rgba(34,211,238,.5);}
.help-btn:active{transform:scale(.95);}
.help-overlay{position:fixed;inset:0;background:rgba(0,4,15,.85);backdrop-filter:blur(6px);z-index:10000;display:none;align-items:center;justify-content:center;padding:14px;animation:helpFade .2s ease;}
.help-overlay.on{display:flex;}
@keyframes helpFade{from{opacity:0}to{opacity:1}}
.help-modal{background:linear-gradient(180deg,#0a1628,#040a18);border:2px solid var(--ACC);border-radius:14px;max-width:560px;width:100%;max-height:86vh;max-height:86dvh;overflow-y:auto;box-shadow:0 0 30px rgba(34,211,238,.4),0 20px 60px rgba(0,0,0,.7);animation:helpSlide .25s cubic-bezier(.22,.9,.34,1.05);}
@keyframes helpSlide{from{opacity:0;transform:translateY(20px) scale(.96)}to{opacity:1;transform:translateY(0) scale(1)}}
.help-head{display:flex;align-items:center;justify-content:space-between;padding:13px 16px;border-bottom:1px solid var(--BDR);background:linear-gradient(90deg,rgba(34,211,238,.1),transparent);position:sticky;top:0;backdrop-filter:blur(8px);z-index:1;}
.help-title{font-family:'Inter',sans-serif;font-size:.82em;color:var(--ACC);letter-spacing:1.5px;font-weight:900;display:flex;align-items:center;gap:8px;}
.help-close{background:none;border:1px solid var(--BDR);color:var(--TXD);font-size:1.1em;cursor:pointer;width:30px;height:30px;border-radius:50%;display:inline-flex;align-items:center;justify-content:center;line-height:1;padding:0;transition:all .15s;}
.help-close:hover{background:var(--DNG);color:#fff;border-color:var(--DNG);transform:rotate(90deg);}
.help-body{padding:14px 16px 18px;font-family:'Noto Sans JP',sans-serif;font-size:.78em;line-height:1.75;color:var(--TX);}
.help-body h4{font-family:'Inter',sans-serif;font-size:.86em;color:var(--ACC);letter-spacing:1px;margin:14px 0 6px;padding-bottom:4px;border-bottom:1px dashed var(--BDR);display:flex;align-items:center;gap:6px;}
.help-body h4:first-child{margin-top:0;}
.help-body p{margin:6px 0;color:var(--TXD);}
.help-body ul,.help-body ol{margin:6px 0 6px 20px;color:var(--TXD);}
.help-body li{margin:3px 0;}
.help-body b{color:var(--TX);font-weight:700;}
.help-body code{background:rgba(125,211,252,.1);color:var(--P);padding:1px 6px;border-radius:4px;font-family:'JetBrains Mono',monospace;font-size:.92em;border:1px solid rgba(125,211,252,.2);}
.help-body .h-tip{background:rgba(34,211,238,.08);border-left:3px solid var(--ACC);padding:7px 11px;margin:8px 0;border-radius:0 6px 6px 0;color:#a5f3fc;}
.help-body .h-warn{background:rgba(245,158,11,.08);border-left:3px solid var(--WRN);padding:7px 11px;margin:8px 0;border-radius:0 6px 6px 0;color:#fde68a;}
.help-body .h-ex{background:rgba(168,85,247,.08);border-left:3px solid var(--PRP);padding:7px 11px;margin:8px 0;border-radius:0 6px 6px 0;color:#e9d5ff;font-family:'JetBrains Mono',monospace;font-size:.92em;}
.help-body .h-ex b{color:#f0abfc;}
.help-body .h-step{counter-reset:step;list-style:none;padding-left:0;}
.help-body .h-step li{counter-increment:step;padding-left:32px;position:relative;margin:6px 0;}
.help-body .h-step li::before{content:counter(step);position:absolute;left:0;top:1px;width:22px;height:22px;background:var(--ACC);color:#000;border-radius:50%;display:inline-flex;align-items:center;justify-content:center;font-family:'Inter',sans-serif;font-weight:900;font-size:.85em;}
</style>
</head>
<body>
<div class="wrap">
  <h1>🌊 Number River - Genesis</h1>
  <div class="tabs">
    <div class="tab active" data-cl="L7" onclick="setLotto('L7',this)">ロト7 <small style="opacity:.6;font-size:.75em">37球</small></div>
    <div class="tab" data-cl="L6" onclick="setLotto('L6',this)">ロト6 <small style="opacity:.6;font-size:.75em">43球</small></div>
    <div class="tab" data-cl="ML" onclick="setLotto('ML',this)">ミニロト <small style="opacity:.6;font-size:.75em">31球</small></div>
  </div>
  <div class="dinfo" id="dinfo">データ読み込み中…</div>

  <div class="global-fold-bar">
    <span style="font-size:.62em;color:var(--TXD);font-family:'JetBrains Mono',monospace;letter-spacing:1px;">折りたたみ:</span>
    <button class="gf-btn gf-pri" onclick="globalFold('expand')">▼ 全展開</button>
    <button class="gf-btn" onclick="globalFold('collapse')">▶ 全折りたたみ</button>
    <button class="gf-btn" onclick="globalFold('default')">↺ 初期状態</button>
    <span style="font-size:.55em;color:var(--DIM);font-family:'JetBrains Mono',monospace;margin-left:8px;">状態は自動保存されます</span>
  </div>

  <div class="fold-card f-open" data-fold="csv">
    <div class="fold-head" onclick="toggleFold(this)">
      <span class="fold-icon">📂</span><span class="fold-title">CSV / データ読込</span>
      <span class="fold-meta" id="csvMeta">未読込</span><button class="help-btn" onclick="event.stopPropagation();openHelp('csv')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
    </div>
    <div class="fold-body">
      <div class="csv-zone" id="csvZone">
        <input type="file" id="csvFileInput" accept=".csv,.txt">
        <div class="csv-icon">📥</div>
        <div class="csv-zone-text">
          <div class="czhead">CSVファイルをドロップ or クリックして選択</div>
          <div class="czinfo">対応: KYO's LOTO CSV / 銀行系CSV / 手動入力テキスト<br>UTF-8 / Shift-JIS 両対応 | 全開催分(無制限)取り込み可</div>
        </div>
        <div id="csvStatusBadge" class="csv-status" style="display:none"></div>
      </div>
    </div>
  </div>

  <div class="fold-card f-open" data-fold="range">
    <div class="fold-head" onclick="toggleFold(this)">
      <span class="fold-icon">🎚️</span><span class="fold-title">解析範囲スライダー</span>
      <span class="fold-meta" id="rangeMeta">直近50回</span><button class="help-btn" onclick="event.stopPropagation();openHelp('range')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
    </div>
    <div class="fold-body">
      <div class="range-bar">
        <label>表示回数:</label>
        <input type="range" id="rangeSlider" min="5" max="50" value="50" step="1" oninput="onRangeChange(this.value)">
        <span id="rangeDisp">50回</span>
        <div class="range-presets">
          <div class="rpbtn" data-preset="10" onclick="setRange(10)">10</div>
          <div class="rpbtn" data-preset="15" onclick="setRange(15)">15</div>
          <div class="rpbtn" data-preset="24" onclick="setRange(24)">24</div>
          <div class="rpbtn on" data-preset="50" onclick="setRange(50)">50</div>
          <div class="rpbtn" data-preset="100" onclick="setRange(100)">100</div>
          <div class="rpbtn" data-preset="200" onclick="setRange(200)">200</div>
          <div class="rpbtn" data-preset="all" onclick="setRange(-1)" style="background:#7f1d1d;color:#fecaca;border-color:#dc2626">全</div>
        </div>
      </div>
    </div>
  </div>

  <div class="fold-card" data-fold="input">
    <div class="fold-head" onclick="toggleFold(this)">
      <span class="fold-icon">✏️</span><span class="fold-title">入力 / 軸数字 / 除外設定</span>
      <span class="fold-meta">手動データ・条件指定</span><button class="help-btn" onclick="event.stopPropagation();openHelp('input')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
    </div>
    <div class="fold-body">
      <div class="input-sec">
        <div>
          <label class="flabel">当選データ（手動入力・テキスト貼り付け）</label>
          <textarea id="dataInput" placeholder="【対応フォーマット】&#10;回数あり+スラッシュ: 672 07 11 15 16 17 24 33 / 06 09&#10;先頭行=最新回。"></textarea>
          <div class="snote">先頭行=最新回。CSVドロップで上書き更新。</div>
        </div>
        <div class="opts-col">
          <div><label class="flabel">軸数字（必ず含める）</label><input type="text" id="fixedNums" placeholder="例: 11, 23"></div>
          <div><label class="flabel">除外数字</label><input type="text" id="blackList" placeholder="例: 01, 05"></div>
          <div><label class="flabel">マイピック（優先度UP）</label><input type="text" id="myPicks" placeholder="例: 03, 18, 29"></div>
          <div><label class="flabel" style="color:var(--WRN)">ボーナス軸 / 除外</label>
            <input type="text" id="bonusFixed" placeholder="軸: 06" style="margin-bottom:4px">
            <input type="text" id="bonusBlack" placeholder="除外: 01">
            <div class="snote">※ボーナス候補表示と分析サマリに反映</div>
          </div>
          <div><label class="flabel">解析オプション</label>
            <label class="optrow"><input type="checkbox" id="optSlide" checked>スライド理論（±1,±2）</label>
            <label class="optrow"><input type="checkbox" id="optDelta" checked>デルタ理論</label>
            <label class="optrow"><input type="checkbox" id="optZone" checked>ゾーン理論</label>
            <label class="optrow"><input type="checkbox" id="optGold" checked>黄金比フィルタ</label>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="fold-card" data-fold="ai">
    <div class="fold-head" onclick="toggleFold(this)">
      <span class="fold-icon">🛡️</span><span class="fold-title">TACTICAL & AI COMMANDER</span>
      <span class="fold-meta">潮流分析 / プロンプト生成</span><button class="help-btn" onclick="event.stopPropagation();openHelp('ai')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
    </div>
    <div class="fold-body">
      <div class="side-panel" style="flex-direction:row;flex-wrap:wrap">
        <div class="tactical-panel" style="flex:1;min-width:280px;">
          <div class="panel-title" style="color:var(--ACC)"><span>🛡️</span> TACTICAL INTELLIGENCE</div>
          <div id="tacticalContent" class="panel-content">解析を実行すると、潮流分析がここに表示されます。</div>
          <button class="eval-btn" onclick="evaluateMySense()">🧠 矢印予測の精度を自己評価</button>
        </div>
        <div class="prompt-panel" style="flex:1;min-width:280px;">
          <div class="panel-title" style="color:var(--PRP)"><span>🔮</span> AI COMMANDER PROMPT</div>
          <textarea id="aiPromptPreview" readonly placeholder="解析実行後に自動生成..."></textarea>
          <button class="eval-btn purple" onclick="copyAIPrompt()">📋 プロンプトをコピー</button>
        </div>
      </div>
    </div>
  </div>

  <div class="ctrls">
    <button class="btn b-or" onclick="loadBuiltin()">📥 内蔵データロード</button>
    <button class="btn b-bl" onclick="processAll()">🔍 解析＆チャート更新</button>
    <button class="btn b-pu" onclick="exportJSON()">📋 解析JSONコピー</button>
  </div>

  <div class="module m-pred open" id="modPred">
    <div class="module-head" onclick="toggleModule('modPred')">
      <div class="module-head-left"><span class="module-icon">🎯</span>
        <div><div class="module-title">PREDICTION</div><div class="module-sub">5点生成 / 予測エンジン / 型分析 / バイアス</div></div>
      </div>
      <button class="help-btn help-btn-mod" onclick="event.stopPropagation();openHelp('mod-pred')" title="モジュール説明">?</button><span class="module-arrow">▶</span>
    </div>
    <div class="module-body">
      <div class="fold-card f-open" data-fold="five-gen">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">✨</span><span class="fold-title">5点 生成</span>
          <span class="fold-meta">神 / デルタ / リバー</span><button class="help-btn" onclick="event.stopPropagation();openHelp('five-gen')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="ctrls2">
            <button class="btn b-gr" onclick="generateGodFive()">✨ 神の5点</button>
            <button class="btn b-pk" onclick="generateDeltaFive()">🔺 デルタ5点</button>
            <button class="btn b-cy" onclick="generateRiverFive()">🌊 リバー5点</button>
            <button class="btn b-vio" onclick="generateNSFive()">💎 N-S物理5点</button>
          </div>
          <div id="patternsAll" style="margin-top:8px"></div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="engine">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🌊</span><span class="fold-title">PREDICTION ENGINE</span>
          <span class="fold-meta">予測落下点コントロール</span><button class="help-btn" onclick="event.stopPropagation();openHelp('engine')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="predict-panel">
            <div class="pp-head"><div class="pp-title">🌊 PREDICTION ENGINE</div>
              <div class="pp-master"><span class="pp-master-label">マスター:</span><label class="sw-toggle"><input type="checkbox" id="ppMaster" checked onchange="predict.update()"><span class="sw-slider"></span></label></div>
            </div>
            <div id="ppEng_ns" class="pp-eng" style="background:linear-gradient(135deg,rgba(168,85,247,.15),rgba(217,70,239,.06));border:1.5px solid var(--PRP);margin-bottom:8px;padding:10px 12px;">
              <div class="pp-eng-info">
                <div class="pp-eng-name" style="color:#e9d5ff;font-size:.78em;">💎 N-S物理モード <span style="background:#7f1d1d;color:#fecaca;padding:1px 6px;border-radius:4px;font-size:.7em;font-family:'JetBrains Mono',monospace;margin-left:4px;">EXCLUSIVE</span></div>
                <div class="pp-eng-desc" style="color:#c4b5fd;">圧力/粘性/外力/渦の4力で純物理予測。ON時は他全エンジンを強制OFF</div>
              </div>
              <label class="sw-toggle"><input type="checkbox" id="ppT_ns" onchange="predict.update()"><span class="sw-slider"></span></label>
            </div>
            <div class="pp-engines">
              <div class="pp-eng on" id="ppEng_freq"><div class="pp-eng-info"><div class="pp-eng-name">📊 出現頻度</div><div class="pp-eng-desc">過去全期間の出現回数</div></div><label class="sw-toggle"><input type="checkbox" id="ppT_freq" checked onchange="predict.update()"><span class="sw-slider"></span></label></div>
              <div class="pp-eng on" id="ppEng_momentum"><div class="pp-eng-info"><div class="pp-eng-name">🔥 モメンタム</div><div class="pp-eng-desc">直近30%の勢い</div></div><label class="sw-toggle"><input type="checkbox" id="ppT_momentum" checked onchange="predict.update()"><span class="sw-slider"></span></label></div>
              <div class="pp-eng on" id="ppEng_dormant"><div class="pp-eng-info"><div class="pp-eng-name">💤 休眠リバウンド</div><div class="pp-eng-desc">未出現期間からの反発</div></div><label class="sw-toggle"><input type="checkbox" id="ppT_dormant" checked onchange="predict.update()"><span class="sw-slider"></span></label></div>
              <div class="pp-eng on" id="ppEng_adjacent"><div class="pp-eng-info"><div class="pp-eng-name">🔗 隣接連動</div><div class="pp-eng-desc">±1の連動性</div></div><label class="sw-toggle"><input type="checkbox" id="ppT_adjacent" checked onchange="predict.update()"><span class="sw-slider"></span></label></div>
              <div class="pp-eng on" id="ppEng_slide"><div class="pp-eng-info"><div class="pp-eng-name">🎯 スライド理論</div><div class="pp-eng-desc">直前回±1,±2の推移</div></div><label class="sw-toggle"><input type="checkbox" id="ppT_slide" checked onchange="predict.update()"><span class="sw-slider"></span></label></div>
              <div class="pp-eng on" id="ppEng_vshape"><div class="pp-eng-info"><div class="pp-eng-name">🔺 Vパターン折返</div><div class="pp-eng-desc">24→22→24型の三角復帰</div></div><label class="sw-toggle"><input type="checkbox" id="ppT_vshape" checked onchange="predict.update()"><span class="sw-slider"></span></label></div>
              <div class="pp-eng on" id="ppEng_overlay"><div class="pp-eng-info"><div class="pp-eng-name">📈 オーバーレイ描画</div><div class="pp-eng-desc">メイン上に予測山</div></div><label class="sw-toggle"><input type="checkbox" id="ppT_overlay" checked onchange="predict.update()"><span class="sw-slider"></span></label></div>
            </div>
          </div>
        </div>
      </div>
      <div class="fold-card" data-fold="type">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🎯</span><span class="fold-title">NEXT TYPE PREDICTOR</span>
          <span class="fold-meta">型分析 / 遷移確率</span><button class="help-btn" onclick="event.stopPropagation();openHelp('type')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="type-panel">
            <div class="tp-head"><div class="tp-title">🎯 NEXT TYPE PREDICTOR</div>
              <div class="pp-master"><span class="pp-master-label">適用:</span><label class="sw-toggle"><input type="checkbox" id="tpApply" onchange="typePredict.update()"><span class="sw-slider"></span></label></div>
            </div>
            <div class="tp-grid">
              <div class="tp-card"><div class="tp-card-title">📐 ゾーン型 / 直前→次回</div><div id="tpZoneCurrent" class="tp-current"></div><div id="tpZoneProbs"></div></div>
              <div class="tp-card"><div class="tp-card-title">⚖️ 奇偶型 / 直前→次回</div><div id="tpOECurrent" class="tp-current"></div><div id="tpOEProbs"></div></div>
              <div class="tp-card"><div class="tp-card-title">🔢 末尾(下一桁)動向</div><div id="tpTailGrid" class="tp-tail-grid"></div><div id="tpTailRec" class="tp-recommend">--</div></div>
              <div class="tp-card"><div class="tp-card-title">🌪 荒れスコア / 引っ張り</div><div class="tp-rough"><div class="tp-rough-meter"><div id="tpRoughFill" class="tp-rough-fill" style="width:0%"></div></div><span id="tpRoughText" class="tp-rough-text">--</span></div><div id="tpRoughDetail" class="tp-recommend">--</div></div>
            </div>
          </div>
        </div>
      </div>
      <div class="fold-card" data-fold="bias">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">📊</span><span class="fold-title">OCCURRENCE BIAS</span>
          <span class="fold-meta">出現差バイアス検出</span><button class="help-btn" onclick="event.stopPropagation();openHelp('bias')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="bias-panel">
            <div class="bp-head"><div class="bp-title">📊 OCCURRENCE BIAS</div>
              <div class="pp-master"><span class="pp-master-label">適用:</span><label class="sw-toggle"><input type="checkbox" id="biasApply" onchange="biasAnalyzer.update()"><span class="sw-slider"></span></label></div>
            </div>
            <div class="bp-stats">
              <span>サンプル: <b id="bpSampleN">--</b></span>
              <span>期待値: <b id="bpExpected">--</b></span>
              <span>多発: <b id="bpHotN">0</b></span>
              <span>不足: <b id="bpColdN">0</b></span>
              <span>強度: <b id="bpStrength">--</b></span>
            </div>
            <div class="bp-grid">
              <div class="bp-card"><div class="bp-card-title bp-hot-title">🔥 多発 (+10%以上)</div><div id="bpHotList" class="bp-list"></div></div>
              <div class="bp-card"><div class="bp-card-title bp-cold-title">❄️ 不足 (-10%以下)</div><div id="bpColdList" class="bp-list"></div></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="module m-grid open" id="modGrid">
    <div class="module-head" onclick="toggleModule('modGrid')">
      <div class="module-head-left"><span class="module-icon">📊</span>
        <div><div class="module-title">NUMBER GRID</div><div class="module-sub">出目表 / 予測ライン / 矢印 / ホットコールド</div></div>
      </div>
      <button class="help-btn help-btn-mod" onclick="event.stopPropagation();openHelp('mod-grid')" title="モジュール説明">?</button><span class="module-arrow">▶</span>
    </div>
    <div class="module-body">
      <div class="fold-card f-open" data-fold="grid-ops">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🛠️</span><span class="fold-title">出目表 操作</span>
          <span class="fold-meta">表示順 / 線 / リセット</span><button class="help-btn" onclick="event.stopPropagation();openHelp('grid-ops')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="ctrls2">
            <button class="btn b-nb" id="btnOrd" onclick="toggleOrder()">🔄 新しい順</button>
            <button class="btn b-gy" id="btnLines" onclick="toggleLines()">〰 流線:OFF</button>
            <button class="btn b-nb" onclick="resetView()">🏠 リセット</button>
            <button class="btn b-cy" onclick="toggleHC()">🔥❄️ HC詳細</button>
          </div>
          <div id="lineCtrls" style="display:none;margin-top:6px;background:rgba(125,211,252,.06);border:1px solid var(--P);border-radius:7px;padding:6px 8px;">
            <div style="font-size:.6em;color:var(--P);font-family:'JetBrains Mono',monospace;letter-spacing:1px;margin-bottom:5px;font-weight:700;">〰 流線設定</div>
            <div style="display:flex;flex-wrap:wrap;gap:4px;align-items:center;">
              <span style="font-size:.65em;color:var(--TXD);font-family:'JetBrains Mono',monospace;margin-right:3px;">範囲:</span>
              <button class="btn b-gy lr-btn" data-lr="1" onclick="setLineRange(1)" style="font-size:.65em;padding:3px 8px;">±1</button>
              <button class="btn b-gy lr-btn" data-lr="2" onclick="setLineRange(2)" style="font-size:.65em;padding:3px 8px;">±2</button>
              <button class="btn b-gy lr-btn" data-lr="3" onclick="setLineRange(3)" style="font-size:.65em;padding:3px 8px;">±3</button>
              <span style="margin-left:8px;font-size:.65em;color:var(--TXD);font-family:'JetBrains Mono',monospace;">ボーナス:</span>
              <button class="btn b-gy" id="btnLineBonus" onclick="toggleLineBonus()" style="font-size:.65em;padding:3px 8px;">含まない</button>
            </div>
          </div>
          <div class="ctrls2" style="margin-top:6px">
            <button class="btn b-gy" id="btnHotMode" onclick="toggleHotMode()">🔥 HOT強調:OFF</button>
            <button class="btn b-gy" id="btnColdMode" onclick="toggleColdMode()">🔄 復活コールド:OFF</button>
            <button class="btn b-gy" id="btnAllMode" onclick="toggleAllMode()">🌈 全バッジ表示:OFF</button>
          </div>
          <div class="hc-slider-wrap" style="margin-top:8px;padding:8px 10px;background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:8px;">
            <div style="display:flex;align-items:center;justify-content:space-between;gap:8px;margin-bottom:6px;">
              <div style="font-size:.62em;color:var(--P);font-family:'Inter',sans-serif;letter-spacing:1.2px;">🔥❄ 出目表 HOT/COLD 判定</div>
              <button class="btn b-or" id="btnHCMaster" onclick="toggleHCMaster()" style="padding:3px 10px;min-height:0;font-size:.6em;letter-spacing:1px;">色付け:ON</button>
            </div>
            <div style="display:flex;align-items:center;gap:8px;margin-bottom:6px;">
              <label style="font-size:.65em;color:var(--TXD);font-family:'JetBrains Mono',monospace;min-width:78px;">直近 N 回</label>
              <input type="range" id="hcWindowRange" min="3" max="50" step="1" value="10" style="flex:1;min-width:120px;height:4px;accent-color:var(--P);cursor:pointer;" oninput="onHCParamChange()">
              <span id="hcWindowDisp" style="font-size:.72em;color:var(--P);font-family:'JetBrains Mono',monospace;min-width:36px;font-weight:700;text-align:right;">10</span>
            </div>
            <div style="display:flex;align-items:center;gap:8px;">
              <label style="font-size:.65em;color:var(--TXD);font-family:'JetBrains Mono',monospace;min-width:78px;">HOT しきい値</label>
              <input type="range" id="hcHotRange" min="1" max="20" step="1" value="4" style="flex:1;min-width:120px;height:4px;accent-color:#ef4444;cursor:pointer;" oninput="onHCParamChange()">
              <span id="hcHotDisp" style="font-size:.72em;color:#ef4444;font-family:'JetBrains Mono',monospace;min-width:36px;font-weight:700;text-align:right;">4回↑</span>
            </div>
            <div id="hcStatusLine" style="margin-top:6px;font-size:.6em;color:var(--DIM);font-family:'JetBrains Mono',monospace;line-height:1.5;">直近10回で4回以上→🔥HOT  /  0回→❄COLD</div>
          </div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="pline">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🎯</span><span class="fold-title">PREDICTION LINE - 予測ライン</span>
          <span class="fold-meta" id="plineMeta">線0本 / OFF</span><button class="help-btn" onclick="event.stopPropagation();openHelp('pline')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div style="font-size:.62em;color:var(--TXD);font-family:'JetBrains Mono',monospace;line-height:1.7;margin-bottom:8px;background:rgba(168,85,247,.06);padding:8px 10px;border-radius:6px;border:1px solid rgba(168,85,247,.2);">
            <b style="color:var(--PREDICT)">✨ 使い方:</b> 「📐 線を引く」モードで <b>2点クリック</b> → 線が引けます。<br>
            配置後、<b>○ハンドル</b> をドラッグで <b>長さ・角度</b> を自由調整。<br>
            出目表 上部に <b>予測ゾーン3行</b> が出現し、線の延長点に候補数字が <b style="color:#f0abfc">紫色</b> で光ります。<br>
            複数の線が同じ列で交差すると「<b style="color:#f87171">鉄板候補</b>」として赤強調。
          </div>
          <div class="ctrls2">
            <button class="btn b-vio" id="btnPL" onclick="setMode('pline')">📐 線を引く</button>
            <button class="btn b-gy" onclick="clearPLines()">🗑 線を全消去</button>
            <label class="optrow" style="font-size:.66em;background:rgba(10,22,40,.6);padding:5px 9px;border-radius:5px;border:1px solid var(--BDR);">
              <input type="checkbox" id="plExtend" checked onchange="renderPLines()">延長線を予測ゾーンまで伸ばす</label>
            <label class="optrow" style="font-size:.66em;background:rgba(10,22,40,.6);padding:5px 9px;border-radius:5px;border:1px solid var(--BDR);">
              <input type="checkbox" id="plLandPlot" checked onchange="renderPLines()">着地点に候補数字をプロット</label>
            <label class="optrow" style="font-size:.66em;background:rgba(10,22,40,.6);padding:5px 9px;border-radius:5px;border:1px solid var(--BDR);">
              <input type="checkbox" id="plCrossHL" checked onchange="renderPLines()">交差ヒートマップ</label>
          </div>
        </div>
      </div>
      <div class="fold-card" data-fold="arrow">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">→</span><span class="fold-title">矢印モード（自由記録）</span>
          <span class="fold-meta">予想を視覚化</span><button class="help-btn" onclick="event.stopPropagation();openHelp('arrow')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="ctrls2" style="margin-bottom:8px">
            <button class="btn b-gy" id="btnMV" onclick="setMode('view')">👁 閲覧</button>
            <button class="btn b-gy" id="btnMA" onclick="setMode('arrow')">→ 矢印</button>
            <button class="btn b-gy" id="btnME" onclick="setMode('erase')">✕ 消去</button>
            <button class="btn b-rd" onclick="clearArrows()">🗑 矢印全消去</button>
          </div>
          <div class="atbar">
            <span id="mbadge" class="mbadge mbv">👁 閲覧モード</span>
            <span style="font-size:.64em;color:var(--TXD)">色:</span>
            <div class="swg">
              <div class="sw on" style="background:#f43f5e" data-c="#f43f5e" onclick="setAC(this)"></div>
              <div class="sw" style="background:#38bdf8" data-c="#38bdf8" onclick="setAC(this)"></div>
              <div class="sw" style="background:#10b981" data-c="#10b981" onclick="setAC(this)"></div>
              <div class="sw" style="background:#f59e0b" data-c="#f59e0b" onclick="setAC(this)"></div>
              <div class="sw" style="background:#a78bfa" data-c="#a78bfa" onclick="setAC(this)"></div>
              <div class="sw" style="background:#fff" data-c="#fff" onclick="setAC(this)"></div>
            </div>
            <select class="sel" id="aStyle"><option value="solid">実線</option><option value="dashed">破線</option><option value="dotted">点線</option></select>
            <select class="sel" id="aWidth"><option value="1.5">極細</option><option value="2.5" selected>細</option><option value="4">中</option><option value="6">太</option></select>
            <select class="sel" id="aArch"><option value="auto">自動</option><option value="right">右</option><option value="left">左</option><option value="straight">直線</option></select>
          </div>
        </div>
      </div>
      <div class="fold-card f-open full-bleed" data-fold="grid">
        <div class="fold-head" onclick="toggleFold(this)" style="margin:0 16px;border-radius:8px 8px 0 0">
          <span class="fold-icon">🌊</span><span class="fold-title">出目表（数字の川）</span>
          <span class="fold-meta">ピンチ/ホイールでズーム</span><button class="help-btn" onclick="event.stopPropagation();openHelp('grid')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body" style="padding-left:0;padding-right:0">
          <div class="szbar" style="margin:0 0 4px;border-radius:0;border-left:none;border-right:none;">
            <label>ズーム:</label>
            <span id="szdisp">100%</span>
            <div id="szbtnWrap" style="display:flex;gap:4px;flex-wrap:wrap"></div>
            <span style="font-size:.62em;color:var(--DIM);margin-left:4px">| ホイール / ピンチ可</span>
          </div>
          <div id="chart-vp">
            <div id="chart-stage">
              <canvas id="riverCanvas"></canvas>
              <canvas id="predictCanvas"></canvas>
              <canvas id="plineCanvas"></canvas>
              <canvas id="arrowCanvas"></canvas>
            </div>
            <div class="pline-hud" id="plineHUD"></div>
            <div class="vp-tools">
              <button class="vp-fs-btn vp-fs-primary" id="vpFsBtn" onclick="toggleVPFullscreen()">⛶ 最大化</button>
              <button class="vp-fs-btn" id="vpFitBtn" onclick="fitChartWidth()">⊡ フィット</button>
            </div>
            <div class="nav-hint">ドラッグ:移動 / ホイール:ズーム</div>
          </div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="nsheat">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">⚛</span><span class="fold-title">N-S物理 多層ヒートマップ</span>
          <span class="fold-meta" id="nsheatMeta">圧力/粘性/外力/渦 各TOP</span><button class="help-btn" onclick="event.stopPropagation();openHelp('nsheat')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div id="nsheatBody"><div class="ns-empty">N-S物理モードをONにしてください</div></div>
        </div>
      </div>
      <div class="fold-card" data-fold="hc" id="hcFoldCard">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🔥</span><span class="fold-title">ホット / コールド 詳細</span>
          <span class="fold-meta">頻出 / 休眠数字</span><button class="help-btn" onclick="event.stopPropagation();openHelp('hc')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:11px" id="hcGrid">
            <div><h4 id="hotHead" style="font-size:.68em;color:var(--DNG);margin-bottom:6px;font-family:'Inter',sans-serif;letter-spacing:1px">🔥 ホット</h4><div id="hotList"></div></div>
            <div><h4 id="coldHead" style="font-size:.68em;color:#60a5fa;margin-bottom:6px;font-family:'Inter',sans-serif;letter-spacing:1px">❄️ コールド</h4><div id="coldList"></div></div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="module m-anal open" id="modAnal">
    <div class="module-head" onclick="toggleModule('modAnal')">
      <div class="module-head-left"><span class="module-icon">🔧</span>
        <div><div class="module-title">ANALYTICS</div><div class="module-sub">ヒートマップ / 頻度 / モメンタム / サマリ</div></div>
      </div>
      <button class="help-btn help-btn-mod" onclick="event.stopPropagation();openHelp('mod-anal')" title="モジュール説明">?</button><span class="module-arrow">▶</span>
    </div>
    <div class="module-body">
      <div class="fold-card f-open" data-fold="heat">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🔥</span><span class="fold-title">期待値ランキング TOP10</span>
          <span class="fold-meta">本命候補一覧</span><button class="help-btn" onclick="event.stopPropagation();openHelp('heat')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="heatmap-card">
            <h3>🔥 期待値ランキング TOP10</h3>
            <div id="evRankBody"><div class="mosan-empty">データ読み込み待ち…</div></div>
            <div class="mosan-panel">
              <div class="mosan-title">🔺 期待値爆増トライアングル・モサン 💥</div>
              <div class="mosan-sub">直近2回の動きが過去のV字復帰パターン（A→B→A）と一致する数字を検出。<b>戻り先 = 落下点候補</b></div>
              <div id="mosanBody"><div class="mosan-empty">データ読み込み待ち…</div></div>
            </div>
          </div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="stat">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">📊</span><span class="fold-title">出現頻度 + 予測落下線</span>
          <span class="fold-meta">棒+折れ線</span><button class="help-btn" onclick="event.stopPropagation();openHelp('stat')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="statchart-card"><h3>📊 出現頻度 + 予測落下線</h3><canvas id="statChart"></canvas></div>
        </div>
      </div>
      <div class="fold-card" data-fold="mom">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🚀</span><span class="fold-title">モメンタム & 期待値</span>
          <span class="fold-meta">スコア順</span><button class="help-btn" onclick="event.stopPropagation();openHelp('mom')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div id="momList" style="font-size:.79em"></div>
          <div id="metArea" style="font-size:.7em;color:#e2e8f0;margin-top:7px;line-height:1.9;font-family:'JetBrains Mono',monospace"></div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="geomosan">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🔷</span><span class="fold-title">幾何モサン</span>
          <span class="fold-meta">等間隔・対称・クラスタ</span><button class="help-btn" onclick="event.stopPropagation();openHelp('geomosan')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="geomosan-panel">
            <div class="geomosan-title">🔷 GEOMETRIC MOSAN</div>
            <div class="geomosan-sub">直近回の数字配置から幾何学パターンを検出。<b>等間隔3つ組 / 対称ペアの中点 / クラスタ密集帯</b></div>
            <div id="geomosanBody"><div class="geomosan-empty">データ読み込み待ち…</div></div>
          </div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="month">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">📅</span><span class="fold-title">月別クセレポート</span>
          <span class="fold-meta" id="monthFoldMeta">直近 vs 先月相当</span><button class="help-btn" onclick="event.stopPropagation();openHelp('month')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="month-panel">
            <div class="month-title">📅 MONTHLY HABIT REPORT</div>
            <div class="month-sub" id="monthSub">「直近」と「先月」を比較。<b>赤=増加 / 青=減少</b></div>
            <div id="monthBody"><div class="kuse-empty">データ読み込み待ち…</div></div>
          </div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="kuse">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🎯</span><span class="fold-title">数字ごとのクセ票</span>
          <span class="fold-meta">数字をタップで詳細</span><button class="help-btn" onclick="event.stopPropagation();openHelp('kuse')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="kuse-panel">
            <div class="kuse-title">🎯 NUMBER PERSONALITY</div>
            <div class="kuse-sub">各数字のクセを分析。連続性、相方、間隔、月初/月末傾向など。<b>数字をタップで詳細表示</b></div>
            <div id="kuseGrid" class="kuse-grid"></div>
            <div id="kuseDetail" class="kuse-detail"><div class="kuse-empty">数字をタップしてクセを見る</div></div>
          </div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="tail">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🌓</span><span class="fold-title">末尾分析（下1桁）</span>
          <span class="fold-meta">0〜9の出現バランス</span><button class="help-btn" onclick="event.stopPropagation();openHelp('tail')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="tail-panel">
            <div class="tail-title">🌓 TAIL DIGIT ANALYSIS</div>
            <div class="tail-sub">数字の下1桁(0〜9)の偏りを検出。<b>赤=ホット末尾 / 青=コールド末尾</b></div>
            <div id="tailBody"><div class="kuse-empty">データ読み込み待ち…</div></div>
          </div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="geo">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🔺</span><span class="fold-title">ジオメトリ予測</span>
          <span class="fold-meta">数字配置の幾何学</span><button class="help-btn" onclick="event.stopPropagation();openHelp('geo')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="geo-panel">
            <div class="geo-title">🔺 GEOMETRY PREDICTOR</div>
            <div class="geo-sub">数字を数直線上の点として捉え、<b style="color:#f472b6;">等間隔3つ組</b> / <b style="color:#67e8f9;">対称ペア中点</b> / <b style="color:#fde68a;">クラスタ密集帯</b> から次回を予測</div>
            <div class="geo-tabs">
              <div class="geo-tab on" id="geoTabAll" onclick="switchGeoMode('all',this)">📊 全部</div>
              <div class="geo-tab" id="geoTabTri" onclick="switchGeoMode('tri',this)">🔺 等間隔3つ組</div>
              <div class="geo-tab" id="geoTabMid" onclick="switchGeoMode('mid',this)">📏 対称ペア中点</div>
              <div class="geo-tab" id="geoTabClu" onclick="switchGeoMode('clu',this)">🌀 クラスタ</div>
            </div>
            <div id="geoBody"><div class="geo-empty">データ読み込み待ち…</div></div>
          </div>
        </div>
      </div>
      <div class="fold-card f-open" data-fold="pair">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🔁</span><span class="fold-title">ペア共起分析</span>
          <span class="fold-meta">同回出現 / 連続出現</span><button class="help-btn" onclick="event.stopPropagation();openHelp('pair')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <div class="pair-panel">
            <div class="pair-title">🔁 PAIR CO-OCCURRENCE</div>
            <div class="pair-sub">よく一緒に出る数字 / 連続して出る数字を発見</div>
            <div class="pair-tabs">
              <div class="pair-tab on" id="pairTabSame" onclick="switchPairMode('same',this)">🤝 同回ペア</div>
              <div class="pair-tab" id="pairTabSeq" onclick="switchPairMode('seq',this)">➡ 連続ペア</div>
              <div class="pair-tab" id="pairTabAdj" onclick="switchPairMode('adj',this)">🔗 隣接相方</div>
            </div>
            <div id="pairBody"><div class="kuse-empty">データ読み込み待ち…</div></div>
          </div>
        </div>
      </div>
      <div class="fold-card" data-fold="sum">
        <div class="fold-head" onclick="toggleFold(this)">
          <span class="fold-icon">🧠</span><span class="fold-title">解析サマリ（テキスト出力）</span>
          <span class="fold-meta">コピー可</span><button class="help-btn" onclick="event.stopPropagation();openHelp('sum')" title="このセクションの使い方">?</button><span class="fold-arrow">▶</span>
        </div>
        <div class="fold-body">
          <textarea id="sumText" readonly style="width:100%;height:160px;font-size:.68em;background:#020c1b;color:#a5b4fc;border:1px solid #312e81;border-radius:7px;padding:7px;font-family:'JetBrains Mono',monospace"></textarea>
        </div>
      </div>
    </div>
  </div>
</div>

<div class="ttp" id="ttp"></div>
<div class="tst" id="tst"></div>

<div class="help-overlay" id="helpOverlay" onclick="closeHelp(event)">
  <div class="help-modal" onclick="event.stopPropagation()">
    <div class="help-head">
      <div class="help-title" id="helpTitle">📖 マニュアル</div>
      <button class="help-close" onclick="closeHelp()">✕</button>
    </div>
    <div class="help-body" id="helpBody"></div>
  </div>
</div>
<script>
// ============================================================
// DATA
// ============================================================
const DB={
  L7:{label:'ロト7',max:37,mc:7,bc:2,info:'第623〜672回（50回）',draws:[
    {r:672,m:[7,11,15,16,17,24,33],b:[6,9]},{r:671,m:[7,13,16,22,28,33,36],b:[2,25]},
    {r:670,m:[3,4,9,10,18,21,37],b:[15,23]},{r:669,m:[3,5,6,7,9,13,16],b:[11,23]},
    {r:668,m:[1,8,11,14,18,22,29],b:[19,35]},{r:667,m:[9,13,20,22,28,29,33],b:[21,23]},
    {r:666,m:[2,17,18,22,23,25,33],b:[16,34]},{r:665,m:[6,8,14,19,22,25,35],b:[12,17]},
    {r:664,m:[3,6,8,14,21,22,31],b:[17,37]},{r:663,m:[4,6,10,11,13,17,23],b:[25,32]},
    {r:662,m:[4,14,15,21,22,24,37],b:[5,20]},{r:661,m:[7,12,17,22,31,34,35],b:[20,32]},
    {r:660,m:[4,6,12,13,16,17,31],b:[14,20]},{r:659,m:[2,8,9,14,27,34,36],b:[5,18]},
    {r:658,m:[10,12,16,18,19,22,37],b:[11,20]},{r:657,m:[9,11,16,23,27,29,32],b:[6,24]},
    {r:656,m:[1,4,6,20,30,34,37],b:[14,25]},{r:655,m:[4,5,12,13,24,26,33],b:[3,14]},
    {r:654,m:[3,12,25,29,30,32,33],b:[28,31]},{r:653,m:[6,7,12,25,26,30,33],b:[13,15]},
    {r:652,m:[1,16,21,26,27,30,35],b:[6,37]},{r:651,m:[2,13,19,20,24,26,35],b:[29,36]},
    {r:650,m:[1,8,10,14,25,33,35],b:[12,21]},{r:649,m:[12,22,23,26,33,35,37],b:[2,21]},
    {r:648,m:[3,17,19,24,28,29,35],b:[7,13]},{r:647,m:[4,5,9,13,17,22,28],b:[18,31]},
    {r:646,m:[5,12,13,15,18,35,37],b:[11,29]},{r:645,m:[7,10,16,20,26,32,35],b:[24,33]},
    {r:644,m:[1,11,12,14,20,26,29],b:[2,5]},{r:643,m:[1,5,15,16,18,27,34],b:[19,22]},
    {r:642,m:[1,7,22,23,33,34,35],b:[2,24]},{r:641,m:[1,3,7,23,24,33,36],b:[17,30]},
    {r:640,m:[2,7,9,12,13,14,29],b:[15,30]},{r:639,m:[5,9,12,15,30,31,34],b:[13,29]},
    {r:638,m:[1,6,18,19,35,36,37],b:[11,24]},{r:637,m:[1,4,7,8,9,20,21],b:[11,30]},
    {r:636,m:[10,14,17,20,26,27,29],b:[3,11]},{r:635,m:[10,12,20,29,30,31,34],b:[4,15]},
    {r:634,m:[2,12,18,29,32,36,37],b:[5,21]},{r:633,m:[2,5,10,17,21,31,36],b:[20,32]},
    {r:632,m:[5,7,10,11,29,31,37],b:[13,25]},{r:631,m:[2,3,6,17,33,35,36],b:[25,37]},
    {r:630,m:[4,6,8,23,28,29,34],b:[10,19]},{r:629,m:[2,7,9,21,23,24,30],b:[22,29]},
    {r:628,m:[1,8,9,11,18,27,35],b:[16,34]},{r:627,m:[9,15,21,30,33,34,37],b:[10,35]},
    {r:626,m:[1,3,7,12,17,32,34],b:[14,30]},{r:625,m:[3,10,12,18,19,24,32],b:[4,15]},
    {r:624,m:[2,14,15,26,27,34,36],b:[9,24]},{r:623,m:[9,12,22,26,28,31,37],b:[7,30]}
  ]},
  L6:{label:'ロト6',max:43,mc:6,bc:1,info:'第2044〜2093回（50回）',draws:[
    {r:2093,m:[2,10,21,26,29,38],b:[12]},{r:2092,m:[8,13,27,36,37,43],b:[3]},
    {r:2091,m:[6,16,21,25,28,43],b:[9]},{r:2090,m:[2,3,6,9,24,36],b:[5]},
    {r:2089,m:[9,16,18,32,37,43],b:[13]},{r:2088,m:[8,16,18,27,37,39],b:[29]},
    {r:2087,m:[7,10,15,18,26,39],b:[13]},{r:2086,m:[4,11,19,28,39,40],b:[6]},
    {r:2085,m:[6,8,13,26,35,43],b:[14]},{r:2084,m:[8,17,18,19,30,39],b:[9]},
    {r:2083,m:[8,10,13,17,26,29],b:[43]},{r:2082,m:[5,11,14,19,25,40],b:[17]},
    {r:2081,m:[14,31,32,37,41,42],b:[22]},{r:2080,m:[6,12,30,36,37,38],b:[8]},
    {r:2079,m:[5,11,15,17,24,39],b:[31]},{r:2078,m:[1,10,20,24,30,35],b:[41]},
    {r:2077,m:[17,18,23,24,26,43],b:[10]},{r:2076,m:[1,9,20,22,28,41],b:[30]},
    {r:2075,m:[7,10,14,16,35,37],b:[11]},{r:2074,m:[5,6,16,26,27,36],b:[11]},
    {r:2073,m:[2,12,14,24,26,29],b:[8]},{r:2072,m:[8,18,24,36,40,42],b:[43]},
    {r:2071,m:[15,24,29,32,33,35],b:[14]},{r:2070,m:[6,10,25,30,34,36],b:[12]},
    {r:2069,m:[6,17,23,27,33,35],b:[4]},{r:2068,m:[2,10,13,14,29,33],b:[32]},
    {r:2067,m:[3,4,12,15,32,42],b:[31]},{r:2066,m:[8,28,30,32,37,38],b:[5]},
    {r:2065,m:[6,18,21,31,37,40],b:[3]},{r:2064,m:[2,7,24,37,39,41],b:[3]},
    {r:2063,m:[4,28,29,30,38,42],b:[15]},{r:2062,m:[1,9,18,24,35,42],b:[8]},
    {r:2061,m:[5,6,13,21,33,34],b:[40]},{r:2060,m:[8,16,22,40,41,42],b:[10]},
    {r:2059,m:[2,13,26,28,38,43],b:[3]},{r:2058,m:[9,12,14,16,19,42],b:[18]},
    {r:2057,m:[5,7,21,22,38,41],b:[24]},{r:2056,m:[3,7,15,17,19,30],b:[38]},
    {r:2055,m:[11,12,14,17,21,26],b:[2]},{r:2054,m:[1,14,27,30,33,37],b:[23]},
    {r:2053,m:[20,28,31,35,37,41],b:[13]},{r:2052,m:[1,3,18,25,30,34],b:[10]},
    {r:2051,m:[3,15,27,28,31,39],b:[20]},{r:2050,m:[6,11,15,21,25,32],b:[5]},
    {r:2049,m:[10,24,25,39,40,42],b:[19]},{r:2048,m:[8,13,18,32,37,43],b:[27]},
    {r:2047,m:[10,11,16,19,37,39],b:[27]},{r:2046,m:[2,5,10,22,25,38],b:[14]},
    {r:2045,m:[1,4,7,12,16,28],b:[19]},{r:2044,m:[1,6,11,26,33,40],b:[14]}
  ]},
  ML:{label:'ミニロト',max:31,mc:5,bc:1,info:'第1333〜1381回（49回）',draws:[
    {r:1381,m:[2,3,4,20,28],b:[12]},{r:1380,m:[1,10,12,19,21],b:[14]},
    {r:1379,m:[12,15,18,25,28],b:[6]},{r:1378,m:[4,15,18,30,31],b:[23]},
    {r:1377,m:[3,13,19,29,31],b:[30]},{r:1376,m:[1,9,11,22,29],b:[3]},
    {r:1375,m:[1,3,11,16,20],b:[4]},{r:1374,m:[10,16,21,29,30],b:[20]},
    {r:1373,m:[7,8,21,28,29],b:[26]},{r:1372,m:[4,14,21,24,31],b:[5]},
    {r:1371,m:[3,10,12,20,31],b:[11]},{r:1370,m:[1,5,12,24,31],b:[21]},
    {r:1369,m:[1,2,3,5,11],b:[26]},{r:1368,m:[7,8,20,25,26],b:[24]},
    {r:1367,m:[17,21,27,28,29],b:[2]},{r:1366,m:[3,19,20,21,28],b:[13]},
    {r:1365,m:[3,10,20,25,27],b:[24]},{r:1364,m:[1,3,10,27,31],b:[19]},
    {r:1363,m:[6,12,16,19,31],b:[25]},{r:1362,m:[6,12,13,17,26],b:[18]},
    {r:1361,m:[20,25,26,27,30],b:[13]},{r:1360,m:[4,18,20,24,29],b:[31]},
    {r:1359,m:[1,3,15,16,20],b:[13]},{r:1358,m:[1,6,22,27,29],b:[28]},
    {r:1357,m:[7,9,21,25,28],b:[15]},{r:1356,m:[6,7,20,22,24],b:[5]},
    {r:1355,m:[3,6,10,23,27],b:[15]},{r:1354,m:[11,13,15,16,26],b:[2]},
    {r:1353,m:[6,11,13,14,18],b:[5]},{r:1352,m:[5,8,10,11,29],b:[27]},
    {r:1351,m:[4,9,10,19,29],b:[6]},{r:1350,m:[2,3,11,22,28],b:[15]},
    {r:1349,m:[2,3,15,28,29],b:[20]},{r:1348,m:[4,16,20,26,28],b:[29]},
    {r:1347,m:[4,16,18,27,31],b:[24]},{r:1346,m:[5,16,20,23,30],b:[2]},
    {r:1345,m:[3,11,14,30,31],b:[28]},{r:1344,m:[2,5,21,24,28],b:[12]},
    {r:1343,m:[4,13,22,29,30],b:[9]},{r:1342,m:[1,9,13,19,23],b:[11]},
    {r:1341,m:[5,12,16,24,28],b:[31]},{r:1340,m:[8,19,22,24,27],b:[25]},
    {r:1339,m:[6,14,21,23,31],b:[22]},{r:1338,m:[13,18,21,28,31],b:[11]},
    {r:1337,m:[3,11,17,20,22],b:[14]},{r:1336,m:[3,20,21,24,25],b:[26]},
    {r:1335,m:[12,17,18,22,31],b:[30]},{r:1334,m:[3,8,21,27,29],b:[16]},
    {r:1333,m:[7,11,18,25,26],b:[6]}
  ]}
};
</script>
<script>
// ============================================================
// STATE
// ============================================================
let CL='L7', showLines=false, orderNew=true;
let lineRange=3;
let lineIncBonus=false;
let mosanMaxSwing=4;
let hotMode=false, coldMode=false, allMode=false;
let mode='view', aCol='#f43f5e';
// 矢印は r (回号) で保存することで、orderNew切替時のズレを防ぐ
// {sR:回号, sc:列, eR:回号|null, ec:列, isPzoneEnd:bool, pzi:0..2|null, col, w, st, ar}
let arrows=[], drawing=false, dS=null, dE=null;
let lastData=[], scored=[], analysis=null;
let tx=0, ty=0, vpScale=1.0; // viewport zoom; renamed from `scale` to avoid global shadowing
let canvasW=0, canvasH=0;
let displayN=50;
let allDraws=[];

const PZONE_ROWS = 3;
function isPzoneTop(){ return orderNew; }
function pzoneTopY(){
if(isPzoneTop()) return TM2;
let rows = 0;
try{ rows = dispRows().length; }catch(e){ rows = lastData.length; }
return TM2 + rows*CH2;
}
function pzoneRowTopY(zi){ return pzoneTopY() + zi*CH2; }
function pzoneRowCenterY(zi){ return pzoneRowTopY(zi) + CH2*0.5; }
function dataRowTopY(ri){ return isPzoneTop() ? TM2 + (ri+PZONE_ROWS)*CH2 : TM2 + ri*CH2; }
function dataRowCenterY(ri){ return dataRowTopY(ri) + CH2*0.5; }
function dataAreaBottomY(rowsLen){ return isPzoneTop() ? TM2 + (rowsLen+PZONE_ROWS)*CH2 : TM2 + rowsLen*CH2; }
function totalBottomY(rowsLen){ return TM2 + (rowsLen+PZONE_ROWS)*CH2; }
function absRowToZone(rowAll, rowsLen){
if(rowAll<0||rowAll>=rowsLen+PZONE_ROWS)return null;
if(isPzoneTop()){
if(rowAll<PZONE_ROWS) return {zone:'pzone', zi:rowAll};
return {zone:'data', ri:rowAll-PZONE_ROWS};
}else{
if(rowAll<rowsLen) return {zone:'data', ri:rowAll};
return {zone:'pzone', zi:rowAll-rowsLen};
}
}
let pLines = [];
let plDrag = null;
let plPlacing = null;
let plIdSeq = 1;

const SIZES=[300,250,200,150,120,100,80,60,40,25];
const SCALE_MIN=0.08;
// スマホ(タッチ&狭い画面)はメモリ節約のため最大ズーム控えめ
const IS_MOBILE = ('ontouchstart' in window) && (Math.min(window.innerWidth,window.innerHeight) < 900);
const SCALE_MAX = IS_MOBILE ? 2.0 : 6.0;
let CW2=52, CH2=44;
let LM2=64;
const TM2=36;
function cfg(){return DB[CL];}
function p2(n){return ('0'+n).slice(-2);}
function VP(){return document.getElementById('chart-vp');}
function STAGE(){return document.getElementById('chart-stage');}
function GC(){return document.getElementById('riverCanvas');}
function PC(){return document.getElementById('predictCanvas');}
function AC(){return document.getElementById('arrowCanvas');}
function PLC(){return document.getElementById('plineCanvas');}

function toast(m,d){
if(!d)d=2200;
const t=document.getElementById('tst');
t.textContent=m;t.classList.add('on');
setTimeout(function(){t.classList.remove('on');},d);
}
function parseN(id){
const el=document.getElementById(id);
if(!el)return[];
return el.value.split(/[\s,、]+/).filter(function(x){return x.trim()!=='';})
.map(Number).filter(function(n){return !isNaN(n)&&n>0;});
}

// ============================================================
// FOLD CONTROL
// ============================================================
function toggleFold(headEl){
const card=headEl.parentElement;
card.classList.toggle('f-open');
saveFoldState();
}
function setFold(card, open){
if(open) card.classList.add('f-open');
else card.classList.remove('f-open');
}
function globalFold(action){
document.querySelectorAll('.fold-card').forEach(function(c){
if(action==='expand') c.classList.add('f-open');
else if(action==='collapse') c.classList.remove('f-open');
else if(action==='default'){
const k=c.dataset.fold;
const defOpen=['csv','range','five-gen','engine','grid-ops','pline','grid','heat','stat'].indexOf(k)>=0;
c.classList.toggle('f-open', defOpen);
}
});
document.querySelectorAll('.module').forEach(function(m){
if(action==='expand') m.classList.add('open');
else if(action==='collapse') m.classList.remove('open');
else m.classList.add('open');
});
saveFoldState();
setTimeout(function(){try{drawChart();predict.update();}catch(e){}},80);
}
function saveFoldState(){
try{
const st={folds:{}, mods:{}};
document.querySelectorAll('.fold-card').forEach(function(c){
if(c.dataset.fold) st.folds[c.dataset.fold]=c.classList.contains('f-open');
});
document.querySelectorAll('.module').forEach(function(m){
st.mods[m.id]=m.classList.contains('open');
});
localStorage.setItem('nrFold_genesis', JSON.stringify(st));
}catch(e){}
}
function loadFoldState(){
try{
const s=localStorage.getItem('nrFold_genesis');
if(!s) return;
const st=JSON.parse(s);
if(st.folds) Object.keys(st.folds).forEach(function(k){
const c=document.querySelector('.fold-card[data-fold="'+k+'"]');
if(c)setFold(c,st.folds[k]);
});
if(st.mods) Object.keys(st.mods).forEach(function(k){
const m=document.getElementById(k);
if(m){if(st.mods[k])m.classList.add('open');else m.classList.remove('open');}
});
}catch(e){}
}
// === CSV PERSISTENCE ===
function saveCSVToStorage(draws,fileName){
try{
// データを軽量化して保存 (賞金等の重量データは元から含まれていない)
const payload={
lotto:CL,
fileName:fileName||'',
ts:Date.now(),
draws:draws.map(function(d){
const o={r:d.r,m:d.m};
if(d.b)o.b=d.b;
if(d.date)o.date=d.date;
return o;
})
};
localStorage.setItem('nrCSV_'+CL,JSON.stringify(payload));
}catch(e){
console.warn('CSV保存失敗 (容量超過の可能性):',e);
toast('⚠ CSV保存失敗 (データが大きすぎる可能性)',2500);
}
}
function loadCSVFromStorage(){
try{
const s=localStorage.getItem('nrCSV_'+CL);
if(!s)return null;
const payload=JSON.parse(s);
if(payload.lotto!==CL)return null;
if(!Array.isArray(payload.draws)||!payload.draws.length)return null;
return payload;
}catch(e){return null;}
}
function clearCSVStorage(){
try{localStorage.removeItem('nrCSV_'+CL);}catch(e){}
}
function toggleModule(id){
const m=document.getElementById(id);
m.classList.toggle('open');
saveFoldState();
setTimeout(function(){try{drawChart();predict.update();}catch(e){}},80);
}

// ============================================================
// MANUAL / HELP
// ============================================================
const HELP_DATA = {
csv:{title:'📂 CSV / データ読込',body:`

<h4>🎯 何ができる?</h4>
<p>過去の当選データをCSVファイルや手動で読み込み、解析対象にする機能。<b>これがすべての出発点</b>。</p>

<h4>📋 使い方</h4>
<ol class="h-step">
<li>枠内をタップしてCSVファイルを選択 (または直接ドロップ)</li>
<li>自動解析され「✓ 〇〇件読込」と緑バッジが出れば成功</li>
<li>そのまま下の解析範囲スライダーで対象回数を調整</li>
</ol>

<h4>📥 対応フォーマット</h4>
<ul>
<li>KYO's LOTO CSV / 銀行系CSV</li>
<li>手動入力テキスト (回号 + 数字 / ボーナス)</li>
<li>UTF-8 / Shift-JIS 自動判別</li>
</ul>

<div class="h-ex"><b>例:</b><br>672 07 11 15 16 17 24 33 / 06 09<br>671 07 13 16 22 28 33 36 / 02 25</div>

<div class="h-tip">💡 <b>コツ:</b> 全開催分(数百〜数千回)を一気に取り込むとバイアス分析の精度が大幅に上がります。</div>
<div class="h-warn">⚠ 数字列がロト種別と一致しない行は自動スキップされます (ロト6にロト7のデータを読ませた場合など)。</div>
`},
  range:{title:'🎚️ 解析範囲スライダー',body:`
<h4>🎯 何ができる?</h4>
<p>解析・チャート描画に使う「直近何回」を指定する。<b>短期傾向 vs 長期傾向</b>を切り替えるための核心機能。</p>

<h4>📋 使い方</h4>
<ul>
<li>スライダーを動かす → リアルタイムで再解析</li>
<li>プリセット (10/15/24/50/100/200/全) でワンタップ切替</li>
</ul>

<h4>💡 使い分けのコツ</h4>
<ul>
<li><b>10〜24回</b>: 直近の流れ、モメンタム重視。短期予測向き</li>
<li><b>50回</b>: 標準。バランス型</li>
<li><b>100〜200回</b>: 中期傾向。型分析やバイアス検出に有効</li>
<li><b>全</b> (赤ボタン): 最大サンプルでの統計分析。バイアス検出は最も信頼度高い</li>
</ul>

<div class="h-tip">💡 短期(20回)と長期(200回)を切り替えながら<b>両方で出る数字</b>を狙うのが鉄板テクニック。</div>
`},
  input:{title:'✏️ 入力 / 軸数字 / 除外設定',body:`
<h4>🎯 何ができる?</h4>
<p>5点生成に対する<b>個人の意向</b>を反映させるパネル。CSVを読まなくても手動でデータを貼り付けて使うこともできる。</p>

<h4>📋 各欄の意味</h4>
<ul>
<li><b>軸数字</b>: 必ず5点全部に含める数字。出目表で⭐ 黄色マーク</li>
<li><b>除外数字</b>: 5点に絶対入れない数字。出目表で❌ グレー化</li>
<li><b>マイピック</b>: 優先度UP。確率は上がるが必須ではない。出目表で♛ 紫マーク</li>
<li><b>ボーナス軸/除外</b>: 当選表示用ボーナス数字の制御。サマリ・AIプロンプトに反映</li>
</ul>

<h4>📝 入力フォーマット</h4>
<div class="h-ex">数字をスペース・カンマ・読点で区切る<br><b>例:</b> 11, 23 / 03 18 29 / 5、12、20</div>

<h4>💡 解析オプション</h4>
<ul>
<li><b>スライド理論</b>: 直前回の数字±1,±2が次回出やすい傾向を加点</li>
<li><b>デルタ理論</b>: 数字間の差(デルタ)を考慮</li>
<li><b>ゾーン理論</b>: 川上/中域/川下のバランスを考慮</li>
<li><b>黄金比フィルタ</b>: 0.618付近の合計値を優遇</li>
</ul>

<div class="h-warn">⚠ 軸数字を多く指定しすぎると(7点中5点とか)パターンが固定化されて多様性が失われます。1〜2個推奨。</div>
`},
  ai:{title:'🛡️ TACTICAL & AI COMMANDER',body:`
<h4>🎯 何ができる?</h4>
<p>解析結果を<b>AI(ChatGPT等)に渡せる形のプロンプト</b>として自動生成。または自分の矢印予測の的中率を自己評価。</p>

<h4>🛡️ TACTICAL INTELLIGENCE</h4>
<p>AI予測買い目、ゾーン/奇偶予測、荒れスコア、バイアス検出をまとめて表示。<b>パッと見でその回の傾向</b>を把握できる。</p>

<p><b>「🧠 矢印予測の精度を自己評価」</b>ボタン: 自分が出目表に引いた矢印が、終点で実際にその数字が出たかをチェックして的中率を表示。予測ゾーン端の矢印は除外。</p>

<h4>🔮 AI COMMANDER PROMPT</h4>
<p>下のテキストエリアに<b>解析サマリ + 軸/除外/マイピック/ボーナス情報</b>がMarkdown形式で生成される。</p>
<ol class="h-step">
<li>「📋 プロンプトをコピー」ボタンを押す</li>
<li>ChatGPTやClaude等のAIに貼り付け</li>
<li>5パターンの予想 + 根拠を返してくれる</li>
</ol>

<div class="h-tip">💡 <b>裏技:</b> プロンプトの最後に「ただし合計値は140〜170の範囲で」など追加条件を書き足すとより絞れる。</div>
`},
  'five-gen':{title:'✨ 5点 生成',body:`
<h4>📌 何ができる?</h4>
<p>4つの異なるロジックで<b>買い目5パターン</b>を一気に生成。</p>
<h4>🎯 各ロジックの特徴</h4>
<ul>
<li><b>✨ 神の5点</b>：AI予測エンジン(EV)+バイアスを総合した最強モード</li>
<li><b>🔺 デルタ5点</b>：前回からの数字差(±1〜5)に注目した連動型</li>
<li><b>🌊 リバー5点</b>：直近モメンタム重視、勢いに乗る数字を選ぶ</li>
<li><b>💎 N-S物理5点</b>：<b style="color:#c4b5fd;">ナビエ・ストークス方程式</b>による物理シミュレーション。圧力勾配/粘性/外力/渦の4つの力を合成して数字を流体粒子として予測</li>
</ul>
<h4>📋 使い方</h4>
<ol class="h-step">
<li>軸数字・除外を先に設定</li>
<li>4種のうち気分に合うものをタップ</li>
<li>各パターンの「📖 理由を見る」で根拠表示</li>
<li>パターンをタップでクリップボードにコピー</li>
</ol>
<h4>💎 N-S物理5点 詳細</h4>
<p>「理由を見る」を開くと、各数字に <b style="color:#fca5a5;">圧</b>(圧力)/<b style="color:#86efac;">粘</b>(粘性)/<b style="color:#fde68a;">外</b>(外力)/<b style="color:#c4b5fd;">渦</b>のタグと、その数字を導いた「主たる力」が表示される。冒頭には系全体のエネルギーバランス（前回合計値・適温・温度状態・巨大岩位置・ウェイク半径）が出る。</p>
<div class="h-tip">💡 <b>適温</b>は (max+1)/2 × 本数字個数 で自動算出: ロト7=133 / ロト6=132 / ミニロト=80</div>
<div class="h-warn">⚠ 同じボタンを連打すると毎回違うパターンが出る（重み付き乱数のため）</div>`},
  engine:{title:'🌊 PREDICTION ENGINE',body:`
<h4>🎯 何ができる?</h4>
<p>予測アルゴリズムを<b>個別ON/OFF</b>できる司令塔。生成される買い目候補と、出目表上のオーバーレイ表示を制御する。</p>

<h4>💎 N-S物理モード <span style="background:#7f1d1d;color:#fecaca;padding:1px 6px;border-radius:4px;font-size:.7em;">EXCLUSIVE</span></h4>
<p>パネル上部の紫カード。ONにすると<b>他全エンジンを強制OFF</b>にし、純粋にナビエ・ストークス物理モデルだけで予測する。</p>
<ul>
<li><b>圧力勾配 -∇p (35%)</b>: 休眠期間が長い数字ほど高圧で噴出</li>
<li><b>粘性 μ∇²v (25%)</b>: 直近の引っ張り傾向 + 前回出目間の最大空白地帯への流入</li>
<li><b>外力 f (20%)</b>: 前回合計値と適温の差で低/高番台へ偏らせる</li>
<li><b>渦 / ウェイク (20%)</b>: 前回最大数字の直前を吸引（巨大岩の裏側に渦ができる物理現象）</li>
</ul>
<p>ON時は出目表の予測ゾーン・期待値ランキングTOP10・予測折れ線がすべて N-S 計算結果に切替わる。</p>

<h4>🔧 通常6エンジン (N-S OFF時)</h4>
<ul>
<li><b>📊 出現頻度</b>: 過去の出現回数 (基本)</li>
<li><b>🔥 モメンタム</b>: 直近30%の勢い (短期)</li>
<li><b>💤 休眠リバウンド</b>: 長期未出現の反発を狙う</li>
<li><b>🔗 隣接連動</b>: ±1の数字との連動性</li>
<li><b>🎯 スライド理論</b>: 直前回±1,±2の推移パターン</li>
<li><b>🔺 Vパターン折返</b>: A→B→Aの三角復帰 (期待値爆増モサンの基)</li>
<li><b>📈 オーバーレイ描画</b>: メイン出目表に予測山を重ね描き (N-S ON時もこれだけは残る)</li>
</ul>

<h4>💡 マスタースイッチ</h4>
<p>右上の<b>マスタースイッチ</b>でエンジン全体をOFFにすれば、純粋な過去頻度のみで判断できる。</p>

<div class="h-tip">💡 <b>カスタマイズ例:</b><br>・物理予測 → N-S物理モードON（他は自動OFF）<br>・短期狙い → モメンタム + スライド + V折返のみON<br>・長期狙い → 頻度 + 休眠リバウンドのみON</div>
`},
  type:{title:'🎯 NEXT TYPE PREDICTOR',body:`
<h4>🎯 何ができる?</h4>
<p>過去の<b>「型→次回の型」の遷移確率</b>を分析。次回がどのタイプ(ゾーン/奇偶バランス)になりやすいかを予測。</p>

<h4>📐 4つの分析カード</h4>
<ul>
<li><b>ゾーン型</b>: 川上偏重/中域偏重/川下偏重/バランス/混合の5型</li>
<li><b>奇偶型</b>: 全奇/全偶/奇多/偶多/均衡の5型</li>
<li><b>末尾動向</b>: 0〜9の出現バランスとホット/コールド末尾</li>
<li><b>荒れスコア</b>: 連続数字、引っ張り、合計値の偏差から「波乱度」を算出</li>
</ul>

<h4>🔘 「適用」スイッチ</h4>
<p>ONにすると、5点生成時に<b>予測される型と一致する候補</b>が優先される。型予測が外れる可能性もあるので、ベテラン向け機能。</p>

<div class="h-tip">💡 <b>荒れスコア60超</b>のときは連続数字や末尾0出現など普段と違う買い方を検討する目安。</div>
<div class="h-warn">⚠ サンプル数が少ない(100回未満)と遷移確率の信頼性が落ちます。長期データで使うのが◎</div>
`},
  bias:{title:'📊 OCCURRENCE BIAS',body:`
<h4>🎯 何ができる?</h4>
<p>各数字が「期待値からどれだけ偏って出てるか」を数値化。<b>多発バイアス vs 不足バイアス</b>を検出。</p>

<h4>📋 表示項目</h4>
<ul>
<li><b>サンプル</b>: 解析対象回数</li>
<li><b>期待値</b>: 「公平なら何回出るはず」の理論値</li>
<li><b>多発</b>: 期待値+10%以上の数字数</li>
<li><b>不足</b>: 期待値-10%以下の数字数</li>
<li><b>強度</b>: 全体の偏り度合い (低/中/高)</li>
</ul>

<h4>💡 2つの解釈</h4>
<ul>
<li><b>多発派 (順張り):</b> 偏りには「物理的偏り(機械の癖)」がある可能性 → 多発数字は今後も出やすい</li>
<li><b>不足派 (逆張り):</b> 確率は均等回帰する → 不足数字は今後反発で出やすい</li>
</ul>
<p>どちらが正解かは<b>解釈次第</b>。両方の候補をチェックするのが安全。</p>

<div class="h-warn">⚠ サンプル100回未満は信頼度「低」。300回以上で「高」。少数だと偶然の偏りを誤検出しがち。</div>
<div class="h-tip">💡 「適用」スイッチONで5点生成のスコアに加味される (多発+ボーナス、不足-ペナルティ)。</div>
`},
  'grid-ops':{title:'🛠️ 出目表 操作',body:`
<h4>📌 何ができる?</h4>
<p>出目表の<b>表示順・流線・強調モード</b>を切替。</p>
<h4>📋 各ボタン</h4>
<ul>
<li><b>🔄 新しい順 / 古い順</b>：データ並び順切替</li>
<li><b>〰 流線</b>：行をまたぐ数字の連動を線で表示（範囲±1〜±3、ボーナス含む可）</li>
<li><b>🏠 リセット</b>：ズーム/位置を初期化</li>
<li><b>🔥❄️ HC詳細</b>：ホット/コールドの数値リスト</li>
<li><b>🔥 HOT強調</b>：頻出数字を強調</li>
<li><b>🔄 復活コールド</b>：休眠後の復活数字を強調</li>
<li><b>🌈 全バッジ表示</b>：全数字にメタ情報バッジ</li>
</ul>
<div class="h-tip">💡 流線±1だけ表示にすると、隣接の連鎖がきれいに見える</div>`},
  pline:{title:'🎯 PREDICTION LINE',body:`
<h4>🎯 何ができる?</h4>
<p>出目表の上に<b>自分で予測線を引く</b>機能。線の延長点が予測ゾーンに到達して、候補数字をプロットしてくれる。</p>

<h4>📋 使い方</h4>
<ol class="h-step">
<li>「📐 線を引く」ボタンを押してモード切替</li>
<li>出目表の上で<b>2点をクリック</b>(始点 → 終点)</li>
<li>線が引けたら、丸ハンドル(○)をドラッグして長さ・角度を調整</li>
<li>線の延長線上にある予測ゾーンの数字が<b>紫色で光る</b></li>
</ol>

<h4>⚙️ オプション</h4>
<ul>
<li><b>延長線を予測ゾーンまで伸ばす</b>: 線の先を破線で延長</li>
<li><b>着地点に候補数字をプロット</b>: 延長点の数字を表示</li>
<li><b>交差ヒートマップ</b>: 複数の線が同じ列で交差すると<b>鉄板候補</b>として赤強調</li>
</ul>

<h4>💡 使いこなしコツ</h4>
<ul>
<li>過去の出目を結んで「流れ」のラインを引く</li>
<li>3〜5本引いて、交差する列の数字を本命視</li>
<li>軸数字が決まっていれば、そこから線を伸ばすと有効</li>
</ul>

<div class="h-warn">⚠ 線はセッション中のみ保存。リロードで消えます。</div>
`},
  arrow:{title:'→ 矢印モード',body:`
<h4>🎯 何ができる?</h4>
<p>出目表に<b>自由に矢印を描いて</b>自分の予想や気付きをメモる機能。後で「自己評価」で的中率もチェックできる。</p>

<h4>📋 使い方</h4>
<ol class="h-step">
<li>「→ 矢印」ボタンでモード切替 (色・スタイル選択可)</li>
<li>出目表のセルを<b>ドラッグ</b>(始点 → 終点)</li>
<li>「✕ 消去」モードに切替 → 不要な矢印をクリックで削除</li>
<li>「→ 矢印全消去」でまとめて削除</li>
</ol>

<h4>🎨 カスタマイズ</h4>
<ul>
<li><b>色</b>: 6色から選択</li>
<li><b>スタイル</b>: 実線 / 破線 / 点線</li>
<li><b>太さ</b>: 極細 / 細 / 中 / 太</li>
<li><b>カーブ</b>: 自動 / 右 / 左 / 直線</li>
</ul>

<h4>💡 使い方の例</h4>
<ul>
<li>過去の数字 → 予測ゾーンの数字に矢印 = 「次回これ来る」予想</li>
<li>連続パターンを矢印で示してメモ</li>
<li>色で意味を分ける (赤=本命、青=対抗 など)</li>
</ul>

<div class="h-tip">💡 「🛡️ TACTICAL」の<b>「🧠 矢印予測の精度を自己評価」</b>で的中率がわかります。回を重ねるほど自分の精度が見えてくる。</div>
`},
  grid:{title:'🌊 出目表（数字の川）',body:`
<h4>🎯 何ができる?</h4>
<p>過去の当選数字を<b>視覚的に並べたメインビュー</b>。横軸=数字(1〜max)、縦軸=回号。本ツールの心臓部。</p>

<h4>📋 操作方法</h4>
<ul>
<li><b>ピンチ / マウスホイール</b>: ズーム</li>
<li><b>ドラッグ</b>: スクロール</li>
<li><b>マウスホバー</b>: ツールチップで詳細表示</li>
<li><b>⛶ 最大化</b>: フルスクリーン化</li>
<li><b>⊡ フィット</b>: 画面幅に合わせて自動調整</li>
</ul>

<h4>🎨 表示要素</h4>
<ul>
<li><b>赤ドット</b>: 本数字 (当選番号)</li>
<li><b>オレンジドット</b>: ボーナス数字</li>
<li><b>⭐ 黄色枠</b>: 軸数字</li>
<li><b>♛ 紫枠</b>: マイピック</li>
<li><b>❌ グレー</b>: 除外数字</li>
<li><b>白枠+影</b>: AI予測買い目</li>
</ul>

<h4>🌈 上部の予測ゾーン (3行・ゾーン分け)</h4>
<p>出目表の上部に表示される3行の紫帯は、<b>低域/中域/高域</b>のゾーンに分かれて N-S物理予測を表示する：</p>
<ul>
<li><b>1行目 = <span style="color:#22d3ee;">低域TOP3</span></b> (シアン色)</li>
<li><b>2行目 = <span style="color:#a78bfa;">中域TOP3</span></b> (紫色)</li>
<li><b>3行目 = <span style="color:#f472b6;">高域TOP3</span></b> (ピンク色)</li>
</ul>
<p>各ドットの強度は<b>サイズと色の濃さ</b>で表現。#1のみ金色の縁取り。下に強度%が表示される。N-S物理モードがOFFの時は薄い破線円のフォールバック表示になる。</p>

<div class="h-tip">💡 <b>「数字の川」の見方:</b> 縦に並んだドットは「同じ数字が時系列で出てる」流れ。斜めの並びは「+1ずつズレながら出てる」流れ。これを読むのがツールの真髄。</div>
<div class="h-warn">📱 スマホで最大化中はメモリ保護のためズーム上限を低く制限しています。落ちる場合は流線をOFF・データ範囲を絞ると安定します。</div>
`},
  hc:{title:'🔥 ホット / コールド 詳細',body:`
<h4>🎯 何ができる?</h4>
<p>解析期間中の<b>出現回数ランキング</b>を上位/下位で表示。最頻出 = ホット、最少 = コールド。</p>

<h4>📋 表示内容</h4>
<ul>
<li><b>🔥 ホット</b>: 出現回数の多い順 TOP10 (赤背景)</li>
<li><b>❄️ コールド</b>: 出現回数の少ない順 TOP10 (青背景)</li>
<li>各数字の右に「出現〇回 (〇%)」</li>
</ul>

<h4>💡 使い方</h4>
<ul>
<li>ホット = 機械的偏りで継続して出やすい (順張り派)</li>
<li>コールド = そろそろ反発して出る? (逆張り派)</li>
<li>長期(全範囲)と短期(20回)で比較すると面白い変化が見える</li>
</ul>

<div class="h-tip">💡 「短期コールドだが長期ホット」の数字 = <b>休眠中の本命候補</b>。要マーク。</div>
`},
  heat:{title:'🔥 期待値ランキング TOP10',body:`
<h4>🎯 何ができる?</h4>
<p>AI予測スコアが高い数字を<b>TOP10ランキング</b>で大きなカード表示。「<b>次回これが出やすい</b>」が一目瞭然。下に「期待値爆増トライアングル」も表示。</p>

<h4>🏆 ランクの色分け</h4>
<ul>
<li><b>#1</b>: 赤+金グラデの最強カード</li>
<li><b>#2</b>: オレンジ系</li>
<li><b>#3</b>: 紫系</li>
<li><b>#4-5</b>: シアン系</li>
<li><b>#6-10</b>: 標準青</li>
</ul>

<h4>📋 各カードの情報</h4>
<ul>
<li><b>大きな数字</b>: その数字</li>
<li><b>%表記</b>: 期待値スコア</li>
<li><b>下のバー</b>: TOP1を100%とした相対強度</li>
<li><b>⭐マーク</b>: AI選定買い目に入ってる</li>
</ul>

<h4>🔺 期待値爆増トライアングル・モサン</h4>
<p>直近2回の動きが過去の<b>V字復帰パターン (A→B→A)</b> と一致する数字を検出。「<b>戻り先 = 落下点候補</b>」として鉄板候補化。</p>
<ul>
<li><b>振れ幅 ±2〜±6</b>を切替可</li>
<li>ランクカードで具体的な過去事例も確認できる</li>
</ul>

<div class="h-tip">💡 <b>本命選び:</b> #1〜#3の数字 + ⭐マーク付きの数字を中心に組むのが基本。モサン候補とも被ってたら<b>超鉄板</b>。</div>
`},
  stat:{title:'📊 出現頻度 + 予測落下線',body:`
<h4>🎯 何ができる?</h4>
<p>各数字の<b>出現回数(棒グラフ)</b> + <b>AI予測落下線(折れ線)</b>を重ねたチャート。バイアスとAI予測のズレが一目瞭然。</p>

<h4>📋 見方</h4>
<ul>
<li><b>青棒</b>: 過去の出現回数</li>
<li><b>シアン棒</b>: AI選定買い目 (バー色変更)</li>
<li><b>赤折れ線</b>: AI予測 (ピーク = 期待値高)</li>
<li><b>金色マーカー</b>: 折れ線上の選定買い目強調</li>
</ul>

<h4>💡 読み方のコツ</h4>
<ul>
<li><b>棒が低いのに折れ線が高い</b> → コールド反発期待 (逆張り)</li>
<li><b>棒も折れ線も高い</b> → 連続ヒット期待 (順張り)</li>
<li><b>折れ線がフラット</b> → AIの確信度が低い (難しい回)</li>
</ul>
`},
  mom:{title:'🚀 モメンタム & 期待値',body:`
<h4>🎯 何ができる?</h4>
<p>直近30%の期間で<b>勢いのある数字TOP12</b>を表示。下にメトリクス(合計平均・奇数比率)。</p>

<h4>📋 内容</h4>
<ul>
<li><b>モメンタムTOP12</b>: 直近で出現回数が多い数字を黄色カードで表示</li>
<li><b>合計平均</b>: 当選数字の合計値の平均と標準偏差</li>
<li><b>奇数比率</b>: 全数字に対する奇数の割合</li>
</ul>

<h4>💡 使い方</h4>
<ul>
<li>モメンタムTOP3〜5は<b>引っ張り候補</b>として要マーク</li>
<li>合計平均から外れすぎた候補は買い目から除外検討</li>
<li>奇数比率 50%付近が標準。70%超や30%以下は偏り回</li>
</ul>
`},
  geomosan:{title:'🔷 幾何モサン',body:`
<h4>🎯 何ができる?</h4>
<p>直近回の数字配置から<b>幾何学的パターン</b>を検出して次回候補を導く分析。</p>

<h4>🔍 3つの検出パターン</h4>
<ul>
<li><b>🔺 等間隔3つ組</b>: 公差dで a-b-c が並ぶ → 次は ext1=c+d / ext2=a-d</li>
<li><b>📏 対称ペアの中点</b>: 数字aとbの中点mが「次に出やすい」候補に</li>
<li><b>🌀 クラスタ密集帯</b>: 範囲6以内に3個以上の集中 → 穴埋め・帯延長候補</li>
</ul>

<h4>📊 スコア計算</h4>
<ul>
<li>等間隔の延長 = <b>3pt</b></li>
<li>対称中点 = <b>1.5pt × ペア数</b></li>
<li>クラスタ穴 = <b>2pt</b></li>
<li>帯延長 = <b>1pt</b></li>
</ul>

<div class="h-tip">💡 最終的に<b>総合候補TOP</b>として一覧化。複数パターンに跨る数字ほど高スコア=鉄板。</div>
`},
  month:{title:'📅 月別クセレポート',body:`
<h4>🎯 何ができる?</h4>
<p>月単位で当選傾向を比較。<b>「先月と今月で何が変わったか」</b>を統計的に可視化する。</p>

<h4>📋 表示内容 (CSV日付情報あり時)</h4>
<ul>
<li>月選択 + 比較対象選択 (前月 / 全期間平均 / 昨年同月)</li>
<li>平均値 / 合計平均 / 奇数比率 / ゾーン別比率</li>
<li>末尾0〜9の出現回数 (赤=増 / 青=減)</li>
<li>注目ポイント (異常値の自動コメント)</li>
</ul>

<h4>📋 日付情報なし時</h4>
<p>「直近〇回 vs その前〇回」のシンプル比較を表示 (ロト6=8回、その他=4回単位)。</p>

<div class="h-tip">💡 <b>「奇数偏重に変化」</b>などの注目ポイントは、買い目の奇偶バランスを調整する目安に。</div>
<div class="h-warn">⚠ 月別比較は<b>CSV読み込み時に日付列がある</b>必要があります。</div>
`},
  kuse:{title:'🎯 数字ごとのクセ票',body:`
<h4>🎯 何ができる?</h4>
<p>数字をタップすると、その数字の<b>出現クセ詳細</b>を表示。「相方」「次回によく出る数字」など個別分析。</p>

<h4>📋 表示項目</h4>
<ul>
<li><b>出現回数 / 出現率</b></li>
<li><b>最終出現</b>: 何回前に出たか</li>
<li><b>平均出現間隔</b>: 〇回に1度</li>
<li><b>連続出現</b>: 2回連続出た回数 (引っ張り傾向)</li>
<li><b>±1隣人と同時出現</b>: 階段ペアの頻度</li>
<li><b>出現バイアス</b>: 上昇中 / 下降中 / 安定</li>
<li><b>🤝 同回によく出る相方</b>: TOP5</li>
<li><b>➡ 次回によく出る数字</b>: TOP5</li>
</ul>

<div class="h-tip">💡 軸数字を1個決めたら、その数字の<b>「相方」「次回」</b>を見て買い目に組み込むのが王道テクニック。</div>
`},
  tail:{title:'🌓 末尾分析（下1桁）',body:`
<h4>🎯 何ができる?</h4>
<p>当選数字の<b>下1桁(0〜9)の出現バランス</b>を分析。末尾の偏りで予測ヒントを得る。</p>

<h4>📊 表示要素</h4>
<ul>
<li><b>棒グラフ</b>: 末尾0〜9の出現回数 (赤=ホット、青=コールド)</li>
<li><b>偏りスコア(χ²)</b>: 統計的偏り度</li>
<li><b>🌊 末尾フローホイール</b>: スワイプで末尾切替、復活パターン詳細</li>
</ul>

<h4>🌊 フローホイールの見方</h4>
<ul>
<li>中央の末尾 = 「現在〇回ぶり」を表示</li>
<li>過去に同じくらい空いた後の<b>翌回によく来てた末尾TOP5</b>を表示</li>
<li>「短期(2-3回)」「長期(4回以上)」「全体」「該当タイプ」で表示切替</li>
</ul>

<div class="h-tip">💡 末尾0/5は冷えやすく反発のチャンス。複数の末尾から1個ずつ拾うとバランス◎。</div>
`},
  geo:{title:'🔺 ジオメトリ予測',body:`
<h4>🎯 何ができる?</h4>
<p>数字を「数直線上の点」として捉え、<b>等間隔・対称・クラスタ</b>の3つの幾何学的観点から次回を予測。「幾何モサン」とは別の角度から。</p>

<h4>🔍 3つのモード</h4>
<ul>
<li><b>🔺 等間隔3つ組</b>: 公差dで a-b-c が並ぶ → 次は前後の延長点</li>
<li><b>📏 対称ペア中点</b>: 直近で対称ペアになっている数字の中点を予測 + 過去的中率も表示</li>
<li><b>🌀 クラスタ</b>: 密集帯を可視化、重心ドリフトから次回重心予測</li>
</ul>

<h4>📋 「全部」モード</h4>
<p>3つを一括表示。総合的に判断したいとき用。</p>

<div class="h-tip">💡 <b>対称ペア中点</b>で過去的中率15%超なら統計的に有意。狙い目。</div>
<div class="h-warn">⚠ 直近回の数字配置に強く依存。回によって候補数字数が大きく変動します。</div>
`},
  pair:{title:'🔁 ペア共起分析',body:`
<h4>🎯 何ができる?</h4>
<p><b>2つの数字の組み合わせ</b>がどれだけ一緒に出てるかを分析。3種類のペア定義を切替可。</p>

<h4>🔍 3つのモード</h4>
<ul>
<li><b>🤝 同回ペア</b>: 同じ回に一緒に出た回数。<b>組み合わせ買い</b>の参考</li>
<li><b>➡ 連続ペア</b>: 前回出た数字 → 次回出た数字。<b>引っ張り&連動</b>の発見</li>
<li><b>🔗 隣接相方</b>: 同回に「数字差±1」で隣接して出たペア。<b>階段ペア</b></li>
</ul>

<h4>📋 表示内容</h4>
<ul>
<li>TOP15ペアをランキング表示 (バー長さ=出現回数)</li>
<li>各ペアの両数字 + リンク記号 + 出現回数</li>
</ul>

<div class="h-tip">💡 「同回ペア」TOP3に登場する数字は<b>仲良し数字</b>。1個入れたら相方も入れる戦略が有効。</div>
`},
  sum:{title:'🧠 解析サマリ（テキスト出力）',body:`
<h4>🎯 何ができる?</h4>
<p>解析結果すべてを<b>テキスト形式</b>でまとめたサマリ。コピーして他ツールやノートに貼れる。</p>

<h4>📋 含まれる情報</h4>
<ul>
<li>ホット数字 TOP10</li>
<li>コールド数字 TOP10</li>
<li>AI予測買い目</li>
<li>合計値統計 (平均/σ/範囲)</li>
<li>奇数比率</li>
<li>型予測 (ゾーン/奇偶/荒れスコア)</li>
<li>バイアス検出 (多発/不足)</li>
<li>ボーナス設定</li>
</ul>

<div class="h-tip">💡 解析記録を残したい場合はこのテキストをコピーして自分のメモに保存しておくと、後から振り返りやすい。</div>
<div class="h-warn">⚠ AIプロンプトと違ってMarkdownではなくプレーンテキスト。AI送信用には別パネルの「📋 プロンプトをコピー」を使ってください。</div>
`},
  'mod-pred':{title:'🎯 PREDICTION モジュール',body:`
<h4>📌 何ができる?</h4>
<p>次回の数字を予測するためのコア機能群。<b>5点生成</b>、<b>予測エンジン</b>、<b>型分析</b>、<b>バイアス検出</b>を統合し、買い目候補を提案します。</p>
<h4>📋 主な使い方</h4>
<ol class="h-step">
<li>解析実行（CSV読込時に自動）</li>
<li>5点生成（神/デルタ/リバー）で買い目を出す</li>
<li>PREDICTION ENGINEで各エンジンON/OFF調整</li>
<li>型・バイアスの結果と組み合わせて判断</li>
</ol>
<div class="h-tip">💡 最初は「神の5点」だけでOK。慣れたらエンジン切替えで好みに調整。</div>`},
  'mod-grid':{title:'📊 NUMBER GRID モジュール',body:`
<h4>📌 何ができる?</h4>
<p><b>過去の当選数字</b>を時系列で並べた「数字の川」。流線・矢印・予測ラインを重ねて視覚的に分析できる中核モジュール。</p>
<h4>📋 操作</h4>
<ol class="h-step">
<li>ピンチ/ホイールでズーム、ドラッグで移動</li>
<li>「📐 線を引く」で2点クリック → 予測ライン配置</li>
<li>「→ 矢印」モードで自分の予想を視覚化</li>
<li>上3行=予測ゾーン。線・矢印の延長点に候補が浮かぶ</li>
</ol>
<div class="h-tip">💡 赤=本数字 / オレンジ=ボーナス / 白枠=AI予測買い目</div>
<div class="h-warn">⚠ 矢印・予測線は回号(r)で保存されるので、新しい順⇄古い順を切替えてもズレません</div>`},
  'mod-anal':{title:'🔧 ANALYTICS モジュール',body:`
<h4>📌 何ができる?</h4>
<p>多面的な統計分析。<b>ヒートマップ・モメンタム・月別クセ・末尾分析・ペア共起・ジオメトリ</b>など。一つ一つ深掘りして数字の癖を発見します。</p>
<h4>📋 使い方</h4>
<ol class="h-step">
<li>気になるカードを開く（折りたたみ式）</li>
<li>各カードの「?」ボタンで詳細マニュアル参照</li>
<li>「解析サマリ」をコピーしてAIに貼ると深い洞察が得られる</li>
</ol>`},
  nsheat:{title:'⚛ N-S物理 多層ヒートマップ',body:`
<h4>🎯 何ができる?</h4>
<p>ナビエ・ストークス物理エンジンが算出した <b>4つの力</b> をさらに <b>低域/中域/高域 3つのゾーン</b> に分けて表示。「<b>4力 × 3ゾーン × TOP3 = 36数字</b>」が一画面で俯瞰できる。</p>

<h4>📐 ゾーン定義（自動分割）</h4>
<ul>
<li><b style="color:#22d3ee;">低域</b>: 1 〜 max÷3 (ロト7=1〜12 / ロト6=1〜14 / ミニロト=1〜10)</li>
<li><b style="color:#a78bfa;">中域</b>: 残りの中央 (ロト7=13〜24 / ロト6=15〜28 / ミニロト=11〜20)</li>
<li><b style="color:#f472b6;">高域</b>: max÷3×2+1 〜 max (ロト7=25〜37 / ロト6=29〜43 / ミニロト=21〜31)</li>
</ul>

<h4>📋 4つの力</h4>
<ul>
<li><b>💥 圧力 -∇p</b>：休眠期間が長い数字ほど高圧で噴出しやすい</li>
<li><b>🌊 粘性 μ∇²v</b>：直近で出てて引っ張りやすい数字 + 空白地帯への流入</li>
<li><b>🌡 外力 f</b>：前回合計値と適温の差で低/高番台へ偏る力</li>
<li><b>🌀 渦 / 後流</b>：前回最大数字の直前(ウェイク領域)に吸い込まれる数字</li>
</ul>

<h4>🏷 マークの意味</h4>
<ul>
<li><b>#1 ラベル</b>：そのゾーン内で最も強い数字</li>
<li><b>★金枠</b>：現在の予測買い目に既に含まれている数字</li>
<li><b>各セルの%</b>：その力の中での相対強度</li>
<li><b>--</b>：そのゾーンに有効候補がない</li>
</ul>

<h4>🎯 合算ゾーン別TOP3</h4>
<p>カード末尾に、4つの力を全て合成した結果を <b>ゾーン別に3つずつ</b> 表示。バランスよく低/中/高から狙う時の参考に。</p>

<h4>💡 使い方のコツ</h4>
<ul>
<li>4つの力 <b>複数に登場する数字</b> = 物理的に確信度高い候補</li>
<li>力ごとに性格が違うので、<b>各力から1つずつ</b>拾うバランス買いも有効</li>
<li>ゾーン別表示があるので、<b>低/中/高から偏らず選ぶ</b> 戦略がしやすい</li>
<li>渦の行は前回データに依存するので候補が少ない時もある（正常）</li>
</ul>

<div class="h-tip">💡 PREDICTION ENGINE の「N-S物理モード」をONにすると、出目表予測やランキングにも反映される。OFFでも参考表示として常時見られる。</div>`}

};
function openHelp(key){
const data=HELP_DATA[key];
if(!data){console.warn('No help data for',key);return;}
document.getElementById('helpTitle').innerHTML='📖 '+data.title;
document.getElementById('helpBody').innerHTML=data.body;
document.getElementById('helpOverlay').classList.add('on');
document.body.style.overflow='hidden';
}
function closeHelp(e){
if(e&&e.target&&!e.target.classList.contains('help-overlay')&&!e.target.classList.contains('help-close'))return;
document.getElementById('helpOverlay').classList.remove('on');
document.body.style.overflow='';
}
document.addEventListener('keydown',function(e){
if(e.key==='Escape'){
var ov=document.getElementById('helpOverlay');
if(ov&&ov.classList.contains('on'))closeHelp({target:ov});
}
});

// ============================================================
// PREDICTION ENGINE
// ============================================================
const predict = {
statChartInst: null,
lastEV: null,
bestPicks: [],
lastTriangles: [],

calcFreq:function(draws){
const f={};
draws.forEach(function(d){d.m.forEach(function(n){f[n]=(f[n]||0)+1;});});
return f;
},
calcMomentum:function(draws){
const m={};
const split=Math.max(1,Math.floor(draws.length*0.3));
draws.slice(0,split).forEach(function(d){d.m.forEach(function(n){m[n]=(m[n]||0)+1;});});
return m;
},
calcDormant:function(draws){
const ls={};
const total=draws.length;
draws.forEach(function(d,i){d.m.forEach(function(n){if(ls[n]===undefined)ls[n]=i;});});
const c=cfg();const dor={};
for(let n=1;n<=c.max;n++)dor[n]=ls[n]!==undefined?ls[n]:total;
return dor;
},
calcAdjacent:function(draws,freq){
const c=cfg();const a={};
for(let n=1;n<=c.max;n++){
const l=freq[n-1]||0,r=freq[n+1]||0;
a[n]=(l+r)/2;
}
return a;
},
calcSlide:function(draws){
const c=cfg();const s={};
for(let n=1;n<=c.max;n++)s[n]=0;
if(!draws.length)return s;
const lw=draws[0].m;
lw.forEach(function(n){
[-2,-1,1,2].forEach(function(d){
const nb=n+d;
if(nb>=1&&nb<=c.max)s[nb]+=(Math.abs(d)===1?2:1);
});
});
return s;
},
calcVShape:function(draws){
const c=cfg();const v={};
const triangles=[];
for(let n=1;n<=c.max;n++)v[n]=0;
if(draws.length<2){this.lastTriangles=triangles;return v;}
const maxSwing = (typeof mosanMaxSwing==='number'&&mosanMaxSwing>=1)?mosanMaxSwing:4;
const recent = draws[0].m||[];
const prev   = draws[1].m||[];
const movements = [];
prev.forEach(function(p){
let best=null,bd=999;
recent.forEach(function(r){
const d=Math.abs(r-p);
if(d<bd){bd=d;best=r;}
});
if(best!==null && bd<=maxSwing){
movements.push({from:p,to:best,delta:best-p});
}
});
const allRows=draws.map(function(d){return d.m||[];});
movements.forEach(function(mv){
const targetReturn = mv.from;
const swing = Math.abs(mv.delta);
if(swing===0)return;
if(swing>maxSwing)return;
let vCount=0;
const examples=[];
for(let i=0;i<allRows.length-2;i++){
const r0=allRows[i];
const r1=allRows[i+1];
const r2=allRows[i+2];
for(let xi=0;xi<r2.length;xi++){
const X=r2[xi];
if(Math.abs(X-targetReturn)>3)continue;
const midY=r1.find(function(Y){return Math.abs(Math.abs(Y-X)-swing)<=1;});
if(midY===undefined)continue;
const retZ=r0.find(function(Z){return Math.abs(Z-X)<=2;});
if(retZ!==undefined){
vCount++;
if(examples.length<3){
const r2r=(draws[i+2]&&draws[i+2].r)?draws[i+2].r:'?';
const r1r=(draws[i+1]&&draws[i+1].r)?draws[i+1].r:'?';
const r0r=(draws[i]&&draws[i].r)?draws[i].r:'?';
examples.push({rounds:[r2r,r1r,r0r],nums:[X,midY,retZ]});
}
}
}
}
if(vCount>0){
triangles.push({
target:targetReturn,
recentMove:{from:mv.from,to:mv.to,delta:mv.delta},
swing:swing,
direction:mv.delta>0?'↗':'↘',
vCount:vCount,
examples:examples,
pattern:mv.from+(mv.delta>0?'→':'→')+mv.to+'→['+targetReturn+']'
});
}
if(targetReturn>=1 && targetReturn<=c.max){
v[targetReturn]+=vCount;
}
[-2,-1,1,2].forEach(function(off){
const t=targetReturn+off;
if(t>=1&&t<=c.max){
v[t]+=vCount*(Math.abs(off)===1?0.5:0.25);
}
});
});
triangles.sort(function(a,b){return b.vCount-a.vCount;});
this.lastTriangles=triangles;
return v;
},

calcEV:function(){
const c=cfg();
const srcDraws=DB[CL]._viewDraws||DB[CL]._customDraws||cfg().draws||[];
const draws=lastData.map(function(m,i){
const sd=srcDraws[i];
return{m:m, r:(sd&&sd.r)?sd.r:null};
});
const ev={};
for(let n=1;n<=c.max;n++)ev[n]=0;
if(!draws.length){this.lastEV=ev;this.bestPicks=[];return ev;}
const opts=this.getOpts();
const masterOn=document.getElementById('ppMaster').checked;
if(!masterOn){this.lastEV=ev;return ev;}
// === N-S 排他モード: ON時は他全エンジンを無視して純物理計算 ===
const nsEl=document.getElementById('ppT_ns');
if(nsEl&&nsEl.checked){
const total=navierStokes.computeForces();
const v=[];for(let n=1;n<=c.max;n++)v.push(total[n]||0);
const mx=Math.max.apply(null,v.concat([0.001]));
for(let n=1;n<=c.max;n++)ev[n]=(total[n]||0)/mx;
this.lastEV=ev;
const sorted=Object.keys(ev).map(function(k){return[+k,ev[k]];}).sort(function(a,b){return b[1]-a[1];});
this.bestPicks=sorted.slice(0,c.mc).map(function(x){return x[0];}).sort(function(a,b){return a-b;});
return ev;
}
const freq=this.calcFreq(draws);
const mom=this.calcMomentum(draws);
const dor=this.calcDormant(draws);
const adj=this.calcAdjacent(draws,freq);
const sld=this.calcSlide(draws);
const vsh=this.calcVShape(draws);
function norm(o){
const v=[];
for(let n=1;n<=c.max;n++)v.push(o[n]||0);
const mx=Math.max.apply(null,v.concat([1]));
const r={};
for(let n=1;n<=c.max;n++)r[n]=(o[n]||0)/mx;
return r;
}
const nF=norm(freq),nM=norm(mom),nD=norm(dor),nA=norm(adj),nS=norm(sld),nV=norm(vsh);
const W={freq:0.20,momentum:0.25,dormant:0.12,adjacent:0.08,slide:0.15,vshape:0.20};
for(let n=1;n<=c.max;n++){
let s=0,w=0;
if(opts.freq){s+=nF[n]*W.freq;w+=W.freq;}
if(opts.momentum){s+=nM[n]*W.momentum;w+=W.momentum;}
if(opts.dormant){s+=nD[n]*W.dormant;w+=W.dormant;}
if(opts.adjacent){s+=nA[n]*W.adjacent;w+=W.adjacent;}
if(opts.slide){s+=nS[n]*W.slide;w+=W.slide;}
if(opts.vshape){s+=nV[n]*W.vshape;w+=W.vshape;}
ev[n]=w>0?s/w:0;
}
this.lastEV=ev;
const sorted=Object.keys(ev).map(function(k){return[+k,ev[k]];}).sort(function(a,b){return b[1]-a[1];});
this.bestPicks=sorted.slice(0,c.mc).map(function(x){return x[0];}).sort(function(a,b){return a-b;});
return ev;
},

getOpts:function(){
const safeChk=function(id,def){var el=document.getElementById(id);return el?el.checked:def;};
return{
freq:safeChk('ppT_freq',true),
momentum:safeChk('ppT_momentum',true),
dormant:safeChk('ppT_dormant',true),
adjacent:safeChk('ppT_adjacent',true),
slide:safeChk('ppT_slide',true),
vshape:safeChk('ppT_vshape',true)
};
},

drawHeat:function(ev){
const c=cfg();
const body=document.getElementById('evRankBody');
if(!body)return;
const arr=[];
for(let n=1;n<=c.max;n++)arr.push({n:n,v:ev[n]||0});
arr.sort(function(a,b){return b.v-a.v;});
const top=arr.slice(0,10);
const mx=top[0]?top[0].v:1;
const self=this;
const nsEl=document.getElementById('ppT_ns');
const masterEl=document.getElementById('ppMaster');
const nsActive=nsEl&&nsEl.checked&&masterEl&&masterEl.checked;
let html='';
if(nsActive&&navierStokes.lastMeta){
const m=navierStokes.lastMeta;
const tempState=m.tempDiff>10?'🔥 オーバーヒート':(m.tempDiff<-10?'❄️ 過冷却':'⚖ 均衡');
html+='<div style="background:linear-gradient(90deg,rgba(168,85,247,.18),rgba(217,70,239,.08));border:1px solid var(--PRP);border-radius:7px;padding:6px 10px;margin-bottom:6px;font-family:\'JetBrains Mono\',monospace;font-size:.65em;color:#e9d5ff;line-height:1.6;">';
html+='<b style="color:#f0abfc;">⚛ N-S物理モード稼働中</b> ';
html+='<span style="color:#c4b5fd;">| 前回Σ '+m.lastSum+' / 適温 '+m.optimalTemp+' / '+tempState+' | 巨大岩 '+(m.maxLast||'-')+' (ウェイク±'+m.wakeRadius+')</span>';
html+='</div>';
}
html+='<div class="ev-rank-grid">';
top.forEach(function(it,i){
const rank=i+1;
const cls=rank<=5?'r'+rank:'';
const isBest=self.bestPicks.indexOf(it.n)>=0;
const pct=mx>0?(it.v/mx*100):0;
const badge=isBest?'<span class="ev-rank-badge">⭐</span>':'';
let dom='';
if(nsActive){
const d=navierStokes.dominantForce(it.n);
if(d)dom='<div style="font-size:.55em;color:#c4b5fd;margin-top:2px;letter-spacing:.5px;">主:'+d.label+'</div>';
}
html+='<div class="ev-rank-card '+cls+'">'
+'<span class="ev-rank-rank">#'+rank+'</span>'+badge
+'<div class="ev-rank-num">'+p2(it.n)+'</div>'
+'<div class="ev-rank-score">'+(it.v*100).toFixed(0)+'%</div>'
+dom
+'<div class="ev-rank-bar"><div class="ev-rank-bar-fill" style="width:'+pct.toFixed(0)+'%"></div></div>'
+'</div>';
});
html+='</div>';
// サマリ行
if(self.bestPicks&&self.bestPicks.length){
html+='<div class="ev-rank-summary">'
+'<span>🎯 <b>'+(nsActive?'N-S選定買い目':'AI選定買い目')+'</b>:</span>'
+'<span class="ev-rank-picks">'
+self.bestPicks.map(function(n){return'<span class="ev-rank-pick-num">'+p2(n)+'</span>';}).join('')
+'</span></div>';
}
body.innerHTML=html;
},

drawOverlay:function(ev){
const cv=PC();const W=canvasW,H=canvasH;
if(!W||!H)return;
if(cv.width!==W||cv.height!==H){cv.width=W;cv.height=H;}
const ctx=cv.getContext('2d');
ctx.clearRect(0,0,W,H);
if(!document.getElementById('ppT_overlay').checked||!document.getElementById('ppMaster').checked)return;
const c=cfg();
const v=[];for(let n=1;n<=c.max;n++)v.push(ev[n]||0);
const mx=Math.max.apply(null,v),mn=Math.min.apply(null,v),rg=mx-mn||1;
let rowsLen=0;
try{ rowsLen = dispRows().length; }catch(e){ rowsLen = lastData.length; }
const dTop = isPzoneTop() ? (TM2 + PZONE_ROWS*CH2) : TM2;
const dBot = isPzoneTop() ? H : (TM2 + rowsLen*CH2);
const dH = dBot - dTop;
for(let i=1;i<=c.max;i++){
const val=ev[i]||0,t=(val-mn)/rg;
const x=LM2+i*CW2;
if(t>0.4){
const a=(t-0.4)*0.35;
let r,g,b;
if(t<0.65){r=245;g=158;b=11;}else{r=239;g=68;b=68;}
ctx.fillStyle='rgba('+r+','+g+','+b+','+a+')';
ctx.fillRect(x-CW2/2, dTop, CW2, dH);
}
}
this.bestPicks.forEach(function(i){
const x=LM2+i*CW2;
ctx.strokeStyle='rgba(6,182,212,.85)';
ctx.lineWidth=2;ctx.setLineDash([6,4]);
ctx.strokeRect(x-CW2/2+1, dTop+1, CW2-2, dH-2);
ctx.setLineDash([]);
});
},

drawStatChart:function(ev){
const c=cfg();
const ctx=document.getElementById('statChart').getContext('2d');
if(this.statChartInst)this.statChartInst.destroy();
const labels=[];for(let i=1;i<=c.max;i++)labels.push(i);
const draws=lastData.map(function(m){return{m:m};});
const freq=this.calcFreq(draws);
const fd=labels.map(function(n){return freq[n]||0;});
const mxF=Math.max.apply(null,fd.concat([1]));
const evD=labels.map(function(n){return(ev[n]||0)*mxF;});
const self=this;
const cols=labels.map(function(n){return self.bestPicks.indexOf(n)>=0?'#06b6d4':'#1e3a5f';});
const bds=labels.map(function(n){return self.bestPicks.indexOf(n)>=0?'#67e8f9':'#334155';});
const ds=[{type:'bar',label:'出現回数',data:fd,backgroundColor:cols,borderColor:bds,borderWidth:1,order:2}];
if(document.getElementById('ppT_overlay').checked&&document.getElementById('ppMaster').checked){
ds.push({
type:'line',label:'🎯 AI予測',data:evD,
borderColor:'#ef4444',backgroundColor:'rgba(239,68,68,.15)',
borderWidth:2,fill:true,tension:.35,
pointRadius:labels.map(function(n){return self.bestPicks.indexOf(n)>=0?6:2;}),
pointBackgroundColor:labels.map(function(n){return self.bestPicks.indexOf(n)>=0?'#fbbf24':'#ef4444';}),
pointBorderColor:'#fff',order:1
});
}
this.statChartInst=new Chart(ctx,{
data:{labels:labels,datasets:ds},
options:{
responsive:true,maintainAspectRatio:false,
interaction:{mode:'index',intersect:false},
scales:{
y:{beginAtZero:true,grid:{color:'rgba(51,65,85,.4)'},ticks:{color:'#94a3b8',font:{size:10}}},
x:{grid:{display:false},ticks:{color:'#94a3b8',font:{size:9}}}
},
plugins:{
legend:{labels:{color:'#f8fafc',font:{size:10}}},
tooltip:{callbacks:{label:function(ctx){
if(ctx.dataset.label==='出現回数')return'出現: '+ctx.parsed.y+'回';
return'予測: '+(ctx.parsed.y/mxF).toFixed(3);
}}}
}
}
});
},

syncUI:function(){
const m=document.getElementById('ppMaster').checked;
const nsEl=document.getElementById('ppT_ns');
const nsOn=nsEl&&nsEl.checked&&m;
// N-S本体カード
const nsCard=document.getElementById('ppEng_ns');
if(nsCard){
if(nsEl)nsEl.disabled=!m;
nsCard.classList.toggle('on',nsOn);
nsCard.style.opacity=m?'1':'0.4';
}
// 他エンジン: N-S ON時はdisable+暗くする (overlayだけは残す)
['freq','momentum','dormant','adjacent','slide','vshape','overlay'].forEach(function(k){
const el=document.getElementById('ppEng_'+k),tg=document.getElementById('ppT_'+k);
if(el&&tg){
const isOverlay=k==='overlay';
const blocked=nsOn&&!isOverlay;
tg.disabled=!m||blocked;
el.classList.toggle('on',tg.checked&&m&&!blocked);
let op=m?'1':'0.4';
if(blocked)op='0.3';
el.style.opacity=op;
el.style.filter=blocked?'grayscale(60%)':'';
}
});
},

update:function(){
this.syncUI();
if(!lastData.length){
const body=document.getElementById('mosanBody');
if(body)body.innerHTML='<div class="mosan-empty">📂 データを読み込んでください</div>';
const nsBody=document.getElementById('nsheatBody');
if(nsBody)nsBody.innerHTML='<div class="ns-empty">📂 データを読み込んでください</div>';
return;
}
const ev=this.calcEV();
this.drawHeat(ev);
this.drawStatChart(ev);
this.drawOverlay(ev);
this.drawMosan(ev);
drawGeoMosan();
drawMonthReport();
drawKuseGrid();
drawTailAnalysis();
drawPairAnalysis();
drawGeoPanel();
drawNSHeatmap();
renderPLines();
// 予測ゾーンの数字もN-S合算に追従するためチャート再描画 (出目表があるとき)
if(canvasW>0)try{drawChart();}catch(e){}
},
drawMosan:function(ev){
const body=document.getElementById('mosanBody');
if(!body)return;
const tris=this.lastTriangles||[];
const opts=this.getOpts();
const masterOn=document.getElementById('ppMaster').checked;
let headerHtml='<div style="display:flex;align-items:center;flex-wrap:wrap;gap:5px;background:rgba(168,85,247,.06);border:1px solid var(--PRP);border-radius:7px;padding:5px 9px;margin-bottom:8px;">';
headerHtml+='<span style="font-size:.62em;color:var(--PRP);font-family:\'JetBrains Mono\',monospace;letter-spacing:1px;font-weight:700;margin-right:3px;">🔺 振れ幅:</span>';
[2,3,4,5,6].forEach(function(s){
const isActive=mosanMaxSwing===s;
const cls=isActive?'pair-tab on':'pair-tab';
headerHtml+='<div class="'+cls+'" onclick="setMosanMaxSwing('+s+')" style="font-size:.6em;padding:3px 8px;">±'+s+'</div>';
});
headerHtml+='<span style="font-size:.55em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;margin-left:6px;">スライド・デルタ範囲</span>';
headerHtml+='</div>';

if(!masterOn||!opts.vshape){
body.innerHTML=headerHtml+'<div class="mosan-empty">⚠ Vパターンエンジン または マスタースイッチが OFF です</div>';
return;
}
if(!tris.length){
body.innerHTML=headerHtml+'<div class="mosan-empty">🔍 振れ幅±'+mosanMaxSwing+'以内の三角パターンを検出中…<br>過去にV字復帰の前例が見つかりませんでした<br><span style="font-size:.85em;color:var(--DIM);">範囲を広げると見つかるかも</span></div>';
return;
}
const evRank={};
const sorted=Object.keys(ev).map(function(k){return[+k,ev[k]];}).sort(function(a,b){return b[1]-a[1];});
sorted.forEach(function(p,i){evRank[p[0]]=i+1;});
const c=cfg();
const top=tris.slice(0,8);
const self=this;
let html=headerHtml+'<div class="mosan-grid">';
top.forEach(function(t,idx){
const rankCls=idx===0?'rank1':(idx===1?'rank2':(idx===2?'rank3':''));
const rankBadge='#'+(idx+1);
const inBest=self.bestPicks.indexOf(t.target)>=0;
const evPos=evRank[t.target]||'-';
const arrow=t.recentMove.delta>0?'↗':'↘';
const reverseArrow=t.recentMove.delta>0?'↘':'↗';
let pat='<span class="mhi">'+t.recentMove.from+'</span>';
pat+='<span class="mar">'+arrow+'</span>';
pat+='<span class="mhi">'+t.recentMove.to+'</span>';
pat+='<span class="mar">'+reverseArrow+'</span>';
pat+='<span class="mhi">['+t.target+']</span>';
let exHtml='';
if(t.examples&&t.examples.length){
exHtml='<div class="mosan-ex">📚 過去の前例:<br>';
t.examples.forEach(function(ex){
exHtml+='<span class="exr">第'+ex.rounds[0]+'→'+ex.rounds[1]+'→'+ex.rounds[2]+'回</span>: '
+ex.nums[0]+'→'+ex.nums[1]+'→'+ex.nums[2]+'<br>';
});
exHtml+='</div>';
}
html+='<div class="mosan-card '+rankCls+'">'
+'<span class="mosan-rank">'+rankBadge+'</span>'
+'<div><span class="mosan-num">'+t.target+'</span>'
+(inBest?'<span class="mosan-badge">★買い目入り</span>':'')+'</div>'
+'<div class="mosan-pattern">'+pat+'</div>'
+'<div class="mosan-meta">振れ幅: <b>±'+t.swing+'</b> / 過去V字回数: <b>'+t.vCount+'回</b> / EV順位: <b>'+evPos+'位</b></div>'
+exHtml
+'</div>';
});
html+='</div>';
const totalV=tris.reduce(function(a,t){return a+t.vCount;},0);
const topNums=top.slice(0,Math.min(c.mc,top.length)).map(function(t){return t.target;});
html+='<div style="margin-top:10px;padding:7px 10px;background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:6px;font-size:.66em;font-family:\'JetBrains Mono\',monospace;color:var(--TXD);line-height:1.7;">'
+'🎯 <b style="color:var(--DELTA);">爆増候補TOP'+topNums.length+'</b>: '
+topNums.map(function(n){return '<span style="color:var(--DELTA);font-weight:700;">'+n+'</span>';}).join(' / ')
+'<br>📊 検出された三角パターン総数: <b style="color:var(--PRP);">'+tris.length+'</b>件 / 過去V字回数合計: <b style="color:var(--PRP);">'+totalV+'</b>回'
+'</div>';
body.innerHTML=html;

}
};
</script>

<script>
// ============================================================
// 月別/クセ/ペア/ジオ等のヘルパ
// ============================================================
let monthSelected=null;
let monthCompareMode='prev';
function getMonthlyData(){
  const groups={};
  const src=DB[CL]._viewDraws||DB[CL]._customDraws||DB[CL].draws||[];
  src.forEach(function(d){
    if(!d||!d.date)return;
    const k=d.date.y+'-'+(''+d.date.m).padStart(2,'0');
    if(!groups[k])groups[k]={key:k,y:d.date.y,m:d.date.m,rows:[]};
    groups[k].rows.push(d.m||[]);
  });
  return groups;
}
function setMonth(k){monthSelected=k;drawMonthReport();}
function setMonthCompare(m){monthCompareMode=m;drawMonthReport();}

function drawGeoMosan(){
  const body=document.getElementById('geomosanBody');
  if(!body)return;
  if(!lastData.length){body.innerHTML='<div class="geomosan-empty">📂 データを読み込んでください</div>';return;}
  const c=cfg();
  const latest = (lastData[0]||[]).slice();
  if(latest.length<3){body.innerHTML='<div class="geomosan-empty">直近回の数字が少なすぎます</div>';return;}
  const srcDraws=DB[CL]._viewDraws||DB[CL]._customDraws||DB[CL].draws||[];
  const latestRound = srcDraws[0]?srcDraws[0].r:'?';
  const sorted = latest.slice().sort(function(a,b){return a-b;});
  const scoreMap={};
  function addScore(n,pts,reason){
    if(n<1||n>c.max)return;
    if(!scoreMap[n])scoreMap[n]={s:0,r:[]};
    scoreMap[n].s+=pts;
    scoreMap[n].r.push(reason);
  }
  let html='';
  html+='<div style="font-size:.65em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;margin-bottom:6px;">';
  html+='📍 直近第<b style="color:var(--ACC);">'+latestRound+'回</b>: ';
  html+=sorted.map(function(n){return'<span class="geomosan-num">'+p2(n)+'</span>';}).join(' ');
  html+='</div>';
  const triples=[];
  for(let i=0;i<sorted.length-2;i++){
    for(let j=i+1;j<sorted.length-1;j++){
      const diff=sorted[j]-sorted[i];
      if(diff<2)continue;
      const target=sorted[j]+diff;
      const ki=sorted.indexOf(target);
      if(ki>j){
        triples.push({a:sorted[i],b:sorted[j],c:sorted[ki],d:diff});
      }
    }
  }
  html+='<div class="geomosan-section">';
  html+='<div class="geomosan-h h-tri">🔺 等間隔3つ組 (差d で a-b-c が並ぶ)</div>';
  if(triples.length){
    triples.forEach(function(t){
      const ext1=t.c+t.d, ext2=t.a-t.d;
      const ext1V=(ext1>=1&&ext1<=c.max)?ext1:null;
      const ext2V=(ext2>=1&&ext2<=c.max)?ext2:null;
      html+='<div class="geomosan-row">';
      html+='<span class="geomosan-num gm-tri">'+p2(t.a)+'</span>';
      html+='<span class="geomosan-link">―'+t.d+'→</span>';
      html+='<span class="geomosan-num gm-tri">'+p2(t.b)+'</span>';
      html+='<span class="geomosan-link">―'+t.d+'→</span>';
      html+='<span class="geomosan-num gm-tri">'+p2(t.c)+'</span>';
      if(ext1V||ext2V){
        html+='<span class="geomosan-link" style="margin-left:6px;">→次:</span>';
        if(ext1V){html+='<span class="geomosan-num gm-pred">'+p2(ext1V)+'</span>';addScore(ext1V,3,'tri-ext('+t.a+','+t.b+','+t.c+')');}
        if(ext2V){html+='<span class="geomosan-num gm-pred">'+p2(ext2V)+'</span>';addScore(ext2V,3,'tri-ext('+t.a+','+t.b+','+t.c+')');}
      }
      html+='<span class="geomosan-meta">d=<b>'+t.d+'</b></span>';
      html+='</div>';
    });
  }else{
    html+='<div class="geomosan-empty">等間隔の組み合わせが見つかりませんでした</div>';
  }
  html+='</div>';
  const midpoints={};
  for(let i=0;i<sorted.length-1;i++){
    for(let j=i+1;j<sorted.length;j++){
      const sum=sorted[i]+sorted[j];
      if(sum%2!==0)continue;
      const mid=sum/2;
      if(mid<1||mid>c.max)continue;
      if(!midpoints[mid])midpoints[mid]={pairs:[],count:0};
      midpoints[mid].pairs.push([sorted[i],sorted[j]]);
      midpoints[mid].count++;
    }
  }
  const latestSet={};latest.forEach(function(n){latestSet[n]=true;});
  const midList=Object.keys(midpoints).map(function(k){return{m:+k,d:midpoints[k]};})
    .filter(function(p){return !latestSet[p.m];})
    .sort(function(a,b){return b.d.count-a.d.count;});
  html+='<div class="geomosan-section">';
  html+='<div class="geomosan-h h-sym">⚖ 対称ペアの中点</div>';
  if(midList.length){
    const topMids=midList.slice(0,6);
    topMids.forEach(function(p){
      html+='<div class="geomosan-row">';
      html+='<span class="geomosan-num gm-pred">'+p2(p.m)+'</span>';
      html+='<span class="geomosan-link">←中点 of </span>';
      const showPairs = p.d.pairs.slice(0,3);
      showPairs.forEach(function(pr,i){
        if(i>0)html+='<span class="geomosan-link">,</span>';
        html+='<span class="geomosan-num gm-sym">'+p2(pr[0])+'</span>';
        html+='<span class="geomosan-link">↔</span>';
        html+='<span class="geomosan-num gm-sym">'+p2(pr[1])+'</span>';
      });
      if(p.d.pairs.length>3){
        html+='<span class="geomosan-link">他'+(p.d.pairs.length-3)+'組</span>';
      }
      html+='<span class="geomosan-meta">集中度: <b>'+p.d.count+'</b>組</span>';
      html+='</div>';
      addScore(p.m,p.d.count*1.5,'sym('+p.d.count+'pairs)');
    });
  }else{
    html+='<div class="geomosan-empty">対称ペアの中点が見つかりませんでした</div>';
  }
  html+='</div>';
  const clusters=[];
  for(let i=0;i<sorted.length;i++){
    const cluster=[sorted[i]];
    for(let j=i+1;j<sorted.length;j++){
      if(sorted[j]-sorted[i]<=6)cluster.push(sorted[j]);
      else break;
    }
    if(cluster.length>=3)clusters.push(cluster);
  }
  const uniqClusters=[];
  clusters.sort(function(a,b){return b.length-a.length;}).forEach(function(cl){
    const isSubset=uniqClusters.some(function(uc){
      return cl.every(function(n){return uc.indexOf(n)>=0;});
    });
    if(!isSubset)uniqClusters.push(cl);
  });
  html+='<div class="geomosan-section">';
  html+='<div class="geomosan-h h-clu">🎯 クラスタ密集帯 (範囲6以内に3個以上)</div>';
  if(uniqClusters.length){
    uniqClusters.forEach(function(cl){
      const lo=Math.min.apply(null,cl);
      const hi=Math.max.apply(null,cl);
      const range=hi-lo;
      const holes=[];
      for(let n=lo;n<=hi;n++){if(cl.indexOf(n)<0)holes.push(n);}
      const nearby=[];
      [-2,-1,1,2].forEach(function(off){
        const n = off<0 ? lo+off : hi+off;
        if(n>=1&&n<=c.max&&cl.indexOf(n)<0)nearby.push(n);
      });
      html+='<div class="geomosan-row">';
      html+='<span class="geomosan-link">範囲'+lo+'-'+hi+' (幅'+range+'):</span>';
      cl.forEach(function(n){html+='<span class="geomosan-num gm-clu">'+p2(n)+'</span>';});
      html+='<span class="geomosan-meta"><b>'+cl.length+'</b>個密集</span>';
      html+='</div>';
      if(holes.length){
        html+='<div class="geomosan-row" style="padding-left:14px;">';
        html+='<span class="geomosan-link">↳ 穴埋め候補:</span>';
        holes.forEach(function(n){
          html+='<span class="geomosan-num gm-pred">'+p2(n)+'</span>';
          addScore(n,2,'cluster-hole('+lo+'-'+hi+')');
        });
        html+='</div>';
      }
      if(nearby.length){
        html+='<div class="geomosan-row" style="padding-left:14px;">';
        html+='<span class="geomosan-link">↳ 帯延長候補:</span>';
        nearby.forEach(function(n){
          html+='<span class="geomosan-num gm-pred">'+p2(n)+'</span>';
          addScore(n,1,'cluster-nearby('+lo+'-'+hi+')');
        });
        html+='</div>';
      }
    });
  }else{
    html+='<div class="geomosan-empty">クラスタ密集帯が見つかりませんでした</div>';
  }
  html+='</div>';
  const scoreList=Object.keys(scoreMap).map(function(k){return{n:+k,s:scoreMap[k].s,r:scoreMap[k].r};})
    .sort(function(a,b){return b.s-a.s;});
  if(scoreList.length){
    const top=scoreList.slice(0,Math.min(c.mc+2,scoreList.length));
    html+='<div class="geomosan-summary">';
    html+='<div style="color:var(--DELTA);font-weight:700;letter-spacing:1px;font-family:\'Inter\',sans-serif;font-size:.85em;margin-bottom:5px;">🎯 幾何モサン総合候補TOP'+top.length+'</div>';
    html+='<div style="display:flex;flex-wrap:wrap;gap:5px;align-items:center;">';
    top.forEach(function(p,i){
      const sz= i<c.mc ? '1.05em' : '.9em';
      const op= i<c.mc ? '1' : '0.7';
      html+='<span class="geomosan-num gm-pred" style="font-size:'+sz+';opacity:'+op+';">'+p2(p.n)+' <span style="opacity:.7;font-size:.8em;">★'+p.s.toFixed(1)+'</span></span>';
    });
    html+='</div>';
    html+='<div style="font-size:.85em;color:var(--TXD);margin-top:6px;line-height:1.6;">';
    html+='<b style="color:var(--DELTA);">スコア計算</b>: 等間隔の延長=3pt / 対称中点=1.5pt×組数 / クラスタ穴=2pt / 帯延長=1pt';
    html+='</div>';
    html+='</div>';
  }
  body.innerHTML=html;
}

function drawMonthReport(){
  const body=document.getElementById('monthBody');
  if(!body)return;
  if(!lastData.length){body.innerHTML='<div class="kuse-empty">📂 データを読み込んでください</div>';return;}
  const c=cfg();
  const lottoLabel=c.label;
  const freqLabel=(CL==='L6')?'週2回開催':'週1回開催';
  const groups=getMonthlyData();
  const monthKeys=Object.keys(groups).sort().reverse();
  const hasDateInfo = monthKeys.length>0;
  const metaEl=document.getElementById('monthFoldMeta');
  const subEl=document.getElementById('monthSub');
  let html='';
  let recent,prev,recentLabel,prevLabel;
  if(hasDateInfo){
    if(metaEl)metaEl.textContent='月別比較 ('+monthKeys.length+'ヶ月分)';
    html+='<div style="margin-bottom:8px;">';
    html+='<div style="font-size:.6em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;margin-bottom:4px;letter-spacing:1px;">📆 月を選択</div>';
    html+='<div style="display:flex;flex-wrap:wrap;gap:4px;max-height:80px;overflow-y:auto;padding:2px;">';
    const allActive = monthSelected===null?'on':'';
    html+='<div class="pair-tab '+allActive+'" onclick="setMonth(null)" style="font-size:.6em;">📊 直近</div>';
    monthKeys.forEach(function(k){
      const g=groups[k];
      const cls=monthSelected===k?'pair-tab on':'pair-tab';
      html+='<div class="'+cls+'" onclick="setMonth(\''+k+'\')" style="font-size:.6em;">'+g.y+'/'+(''+g.m).padStart(2,'0')+' <span style="opacity:.7;">('+g.rows.length+'回)</span></div>';
    });
    html+='</div></div>';
    html+='<div style="margin-bottom:10px;">';
    html+='<div style="font-size:.6em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;margin-bottom:4px;letter-spacing:1px;">⚖ 比較対象</div>';
    html+='<div style="display:flex;gap:4px;flex-wrap:wrap;">';
    [['prev','前月'],['avg','全期間平均'],['sameLastYear','昨年同月']].forEach(function(p){
      const cls=monthCompareMode===p[0]?'pair-tab on':'pair-tab';
      html+='<div class="'+cls+'" onclick="setMonthCompare(\''+p[0]+'\')" style="font-size:.6em;">'+p[1]+'</div>';
    });
    html+='</div></div>';
    let targetKey=monthSelected;
    if(targetKey===null||!groups[targetKey]){
      targetKey=monthKeys[0];
      monthSelected=null;
    }
    const targetGroup=groups[targetKey];
    if(!targetGroup){
      body.innerHTML=html+'<div class="kuse-empty">月データがありません</div>';
      return;
    }
    recent=targetGroup.rows;
    recentLabel=targetGroup.y+'年'+targetGroup.m+'月';
    if(monthCompareMode==='prev'){
      const idx=monthKeys.indexOf(targetKey);
      const prevKey=monthKeys[idx+1];
      if(prevKey&&groups[prevKey]){
        prev=groups[prevKey].rows;
        prevLabel=groups[prevKey].y+'年'+groups[prevKey].m+'月';
      }
    }else if(monthCompareMode==='sameLastYear'){
      const lastYearKey=(targetGroup.y-1)+'-'+(''+targetGroup.m).padStart(2,'0');
      if(groups[lastYearKey]){
        prev=groups[lastYearKey].rows;
        prevLabel=groups[lastYearKey].y+'年'+groups[lastYearKey].m+'月';
      }
    }else if(monthCompareMode==='avg'){
      const allRows=[];
      monthKeys.forEach(function(k){if(k!==targetKey)allRows.push.apply(allRows,groups[k].rows);});
      if(allRows.length){
        prev=allRows;
        prevLabel='全期間平均('+(allRows.length)+'回)';
      }
    }
    if(subEl)subEl.innerHTML=lottoLabel+'は'+freqLabel+'。日付情報あり。月単位で詳細分析できます。<b style="color:#fca5a5;">赤=増加</b> / <b style="color:#93c5fd;">青=減少</b>';
    if(!prev||prev.length<3){
      body.innerHTML=html+'<div class="kuse-empty">📂 比較対象データが不足しています（'+(prevLabel||'比較対象')+'）</div>';
      return;
    }
  }else{
    const monthSize=(CL==='L6')?8:4;
    if(metaEl)metaEl.textContent='直近'+monthSize+'回 vs その前'+monthSize+'回';
    if(subEl)subEl.innerHTML=lottoLabel+'は'+freqLabel+'。<b style="color:var(--WRN);">CSVに日付列があれば月単位分析が可能</b>。<br>「直近<b>'+monthSize+'回</b>」と「その前<b>'+monthSize+'回</b>」を比較。<b style="color:#fca5a5;">赤=増加</b> / <b style="color:#93c5fd;">青=減少</b>';
    recent=lastData.slice(0,monthSize);
    prev=lastData.slice(monthSize,monthSize*2);
    recentLabel='直近'+monthSize+'回';
    prevLabel='その前'+monthSize+'回';
    const minNeeded=Math.max(3,Math.floor(monthSize*0.6));
    if(prev.length<minNeeded){
      body.innerHTML='<div class="kuse-empty">📂 比較に必要なデータが不足しています（最低'+(monthSize*2)+'回）</div>';
      return;
    }
  }
  function stats(rows){
    const all=[];rows.forEach(function(m){m.forEach(function(n){all.push(n);});});
    if(!all.length)return null;
    const avg=all.reduce(function(a,b){return a+b;},0)/all.length;
    const sums=rows.map(function(m){return m.reduce(function(a,b){return a+b;},0);});
    const sumAvg=sums.reduce(function(a,b){return a+b;},0)/sums.length;
    const odd=all.filter(function(n){return n%2===1;}).length;
    const oddRatio=odd/all.length;
    const z=Math.floor(c.max/3);
    const z1=all.filter(function(n){return n<=z;}).length/all.length;
    const z2=all.filter(function(n){return n>z&&n<=z*2;}).length/all.length;
    const z3=all.filter(function(n){return n>z*2;}).length/all.length;
    const tail={};for(let i=0;i<10;i++)tail[i]=0;
    all.forEach(function(n){tail[n%10]++;});
    return{avg:avg,sumAvg:sumAvg,oddRatio:oddRatio,z1:z1,z2:z2,z3:z3,tail:tail,total:all.length,nrows:rows.length};
  }
  const sR=stats(recent),sP=stats(prev);
  if(!sR||!sP){
    body.innerHTML=html+'<div class="kuse-empty">統計計算に失敗しました</div>';
    return;
  }
  html+='<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px;font-size:.65em;font-family:\'JetBrains Mono\',monospace;color:var(--TX);background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:6px;padding:6px 9px;">';
  html+='<div>📍 <b style="color:var(--ACC);">'+recentLabel+'</b> ('+sR.nrows+'回)</div><div style="color:var(--DIM);">vs</div><div>🆚 <b style="color:var(--TXD);">'+prevLabel+'</b> ('+sP.nrows+'回)</div>';
  html+='</div>';
  function cmpHTML(now,then,fmt){
    const diff=now-then;
    const cls=Math.abs(diff)<0.001?'eq':(diff>0?'up':'dn');
    const arrow=diff>0.001?'▲':(diff<-0.001?'▼':'＝');
    return'<div class="month-stat-cmp '+cls+'">'+arrow+' '+(diff>=0?'+':'')+fmt(diff)+'</div>';
  }
  const f1=function(v){return v.toFixed(1);};
  const fp=function(v){return (v*100).toFixed(1)+'%';};
  html+='<div class="month-stat-grid">';
  html+='<div class="month-stat"><div class="month-stat-label">📊 平均値</div><div class="month-stat-val">'+sR.avg.toFixed(1)+'</div>'+cmpHTML(sR.avg,sP.avg,f1)+'</div>';
  html+='<div class="month-stat"><div class="month-stat-label">🎯 合計平均</div><div class="month-stat-val">'+sR.sumAvg.toFixed(1)+'</div>'+cmpHTML(sR.sumAvg,sP.sumAvg,f1)+'</div>';
  html+='<div class="month-stat"><div class="month-stat-label">🟦 奇数比率</div><div class="month-stat-val">'+(sR.oddRatio*100).toFixed(1)+'%</div>'+cmpHTML(sR.oddRatio,sP.oddRatio,fp)+'</div>';
  html+='<div class="month-stat"><div class="month-stat-label">🌊 川上(1-'+Math.floor(c.max/3)+')</div><div class="month-stat-val">'+(sR.z1*100).toFixed(0)+'%</div>'+cmpHTML(sR.z1,sP.z1,fp)+'</div>';
  html+='<div class="month-stat"><div class="month-stat-label">🌊 中域</div><div class="month-stat-val">'+(sR.z2*100).toFixed(0)+'%</div>'+cmpHTML(sR.z2,sP.z2,fp)+'</div>';
  html+='<div class="month-stat"><div class="month-stat-label">🌊 川下('+(Math.floor(c.max/3)*2+1)+'-'+c.max+')</div><div class="month-stat-val">'+(sR.z3*100).toFixed(0)+'%</div>'+cmpHTML(sR.z3,sP.z3,fp)+'</div>';
  html+='</div>';
  html+='<div style="font-size:.62em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;margin-bottom:3px;">📍 末尾0〜9 出現回数（'+recentLabel+' vs '+prevLabel+'）</div>';
  html+='<div class="month-tail-row">';
  const periodScale = sR.total>0&&sP.total>0 ? (sR.total/sP.total) : 1;
  for(let d=0;d<10;d++){
    const r=sR.tail[d];
    const pAdj=sP.tail[d]*periodScale;
    let cls='';
    if(r===0)cls='t-zero';
    else if(r-pAdj>=2)cls='t-hot';
    else if(pAdj-r>=2)cls='t-cold';
    else if(r>=pAdj)cls='t-warm';
    const dlt=r-sP.tail[d];
    const dltStr=dlt>0?'+'+dlt:dlt;
    html+='<div class="month-tail-cell '+cls+'"><div class="ttn">末'+d+'</div><div class="ttc">'+r+'<span style="font-size:.55em;opacity:.7;">回</span></div><div style="font-size:.5em;opacity:.7;">'+(dlt===0?'＝':dltStr)+'</div></div>';
  }
  html+='</div>';
  const comments=[];
  for(let d=0;d<10;d++){
    const pAdj=sP.tail[d]*periodScale;
    if(sR.tail[d]===0&&pAdj>=2)comments.push('💡 末尾<b>'+d+'</b>が'+recentLabel+'で0回（比較対象'+sP.tail[d]+'回）→ 反発に注目');
    else if(sR.tail[d]>=4)comments.push('🔥 末尾<b>'+d+'</b>が'+recentLabel+'<b>'+sR.tail[d]+'回</b>と異常出現');
  }
  if(sR.oddRatio-sP.oddRatio>=0.15)comments.push('📈 奇数偏重に変化（+'+((sR.oddRatio-sP.oddRatio)*100).toFixed(0)+'%）');
  else if(sP.oddRatio-sR.oddRatio>=0.15)comments.push('📉 偶数偏重に変化');
  if(sR.z1-sP.z1>=0.10)comments.push('🌊 川上偏重に変化');
  if(sR.z3-sP.z3>=0.10)comments.push('🌊 川下偏重に変化');
  if(comments.length){
    html+='<div style="margin-top:8px;font-size:.65em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;line-height:1.8;background:rgba(10,22,40,.7);border:1px solid var(--BDR);border-radius:6px;padding:7px 9px;">';
    html+='<div style="color:var(--ACC);font-weight:700;margin-bottom:3px;">📌 注目ポイント</div>';
    comments.slice(0,5).forEach(function(c){html+='・'+c+'<br>';});
    html+='</div>';
  }
  body.innerHTML=html;
}

let kuseSelected=null;
function drawKuseGrid(){
  const grid=document.getElementById('kuseGrid');
  if(!grid)return;
  const c=cfg();
  let html='';
  for(let n=1;n<=c.max;n++){
    const cls=kuseSelected===n?'kuse-cell k-active':'kuse-cell';
    html+='<div class="'+cls+'" onclick="selectKuse('+n+')">'+p2(n)+'</div>';
  }
  grid.innerHTML=html;
  if(kuseSelected!==null){drawKuseDetail(kuseSelected);}
}
function selectKuse(n){
  kuseSelected=n;
  drawKuseGrid();
  drawKuseDetail(n);
}
function drawKuseDetail(n){
  const det=document.getElementById('kuseDetail');
  if(!det)return;
  if(!lastData.length){det.innerHTML='<div class="kuse-empty">データを読み込んでください</div>';return;}
  const c=cfg();
  const draws=lastData;
  const total=draws.length;
  const apps=[];
  draws.forEach(function(m,i){if(m.indexOf(n)>=0)apps.push(i);});
  if(!apps.length){
    det.innerHTML='<h4>数字 '+p2(n)+'</h4><div class="kuse-empty">この期間に出現していません</div>';
    return;
  }
  const gaps=[];
  for(let i=0;i<apps.length-1;i++)gaps.push(apps[i+1]-apps[i]);
  const avgGap=gaps.length?gaps.reduce(function(a,b){return a+b;},0)/gaps.length:0;
  let consec=0;
  for(let i=0;i<apps.length-1;i++)if(apps[i+1]-apps[i]===1)consec++;
  const lastApp=apps[0];
  const partner={};
  for(let i=1;i<=c.max;i++)partner[i]=0;
  draws.forEach(function(m){
    if(m.indexOf(n)<0)return;
    m.forEach(function(o){if(o!==n)partner[o]++;});
  });
  const topPartners=Object.keys(partner).map(function(k){return[+k,partner[k]];}).sort(function(a,b){return b[1]-a[1];}).slice(0,5);
  const nextWith={};
  for(let i=1;i<=c.max;i++)nextWith[i]=0;
  apps.forEach(function(idx){
    if(idx===0)return;
    const nextRow=draws[idx-1];
    nextRow.forEach(function(o){if(o!==n)nextWith[o]++;});
  });
  const topNext=Object.keys(nextWith).map(function(k){return[+k,nextWith[k]];}).sort(function(a,b){return b[1]-a[1];}).slice(0,5);
  const adj1Count=apps.filter(function(idx){
    const r=draws[idx];
    return r.indexOf(n-1)>=0||r.indexOf(n+1)>=0;
  }).length;
  const half=Math.floor(total/2);
  const earlyCount=apps.filter(function(i){return i>=half;}).length;
  const lateCount=apps.filter(function(i){return i<half;}).length;
  let html='<h4>数字 '+p2(n)+' のクセ</h4>';
  html+='・出現: <span class="kk">'+apps.length+'回 / '+total+'回</span> （出現率 <span class="kk">'+(apps.length/total*100).toFixed(1)+'%</span>）<br>';
  html+='・最終出現: <span class="kk">'+(lastApp===0?'最新回':lastApp+'回前')+'</span><br>';
  html+='・平均出現間隔: <span class="kk">'+avgGap.toFixed(1)+'回</span>に1度<br>';
  html+='・連続出現(2回連): <span class="'+(consec>=3?'kkh':'kk')+'">'+consec+'回</span>'+(consec>=3?' 👈 引っ張り傾向':'')+'<br>';
  html+='・±1の隣人と同時出現: <span class="kk">'+adj1Count+'回</span> ('+(adj1Count/apps.length*100).toFixed(0)+'%)<br>';
  html+='・出現バイアス: 直近'+lateCount+'回 vs 過去'+earlyCount+'回 ';
  if(lateCount>earlyCount*1.4)html+='<span class="kkh">📈 上昇中</span>';
  else if(earlyCount>lateCount*1.4)html+='<span class="kkc">📉 下降中</span>';
  else html+='<span class="kk">→ 安定</span>';
  html+='<div class="kdiv"></div>';
  if(topPartners[0]&&topPartners[0][1]>0){
    html+='・🤝 同回によく出る相方: ';
    html+=topPartners.filter(function(p){return p[1]>0;}).map(function(p){return'<span class="kk">'+p2(p[0])+'</span>('+p[1]+')';}).join(' / ');
    html+='<br>';
  }
  if(topNext[0]&&topNext[0][1]>0){
    html+='・➡ 次回によく出る数字: ';
    html+=topNext.filter(function(p){return p[1]>0;}).map(function(p){return'<span class="kk">'+p2(p[0])+'</span>('+p[1]+')';}).join(' / ');
    html+='<br>';
  }
  det.innerHTML=html;
}

let pairMode='same';
function switchPairMode(m,el){
  pairMode=m;
  document.querySelectorAll('.pair-tab').forEach(function(t){t.classList.remove('on');});
  if(el)el.classList.add('on');
  drawPairAnalysis();
}
function drawPairAnalysis(){
  const body=document.getElementById('pairBody');
  if(!body)return;
  if(!lastData.length){body.innerHTML='<div class="kuse-empty">📂 データを読み込んでください</div>';return;}
  const c=cfg();
  const pairs={};
  function addPair(a,b){
    if(a===b)return;
    const k=Math.min(a,b)+'-'+Math.max(a,b);
    pairs[k]=(pairs[k]||0)+1;
  }
  if(pairMode==='same'){
    lastData.forEach(function(m){
      for(let i=0;i<m.length;i++)for(let j=i+1;j<m.length;j++)addPair(m[i],m[j]);
    });
  }else if(pairMode==='seq'){
    for(let i=0;i<lastData.length-1;i++){
      const newR=lastData[i],oldR=lastData[i+1];
      oldR.forEach(function(a){newR.forEach(function(b){addPair(a,b);});});
    }
  }else if(pairMode==='adj'){
    lastData.forEach(function(m){
      for(let i=0;i<m.length;i++)for(let j=i+1;j<m.length;j++){
        if(Math.abs(m[i]-m[j])===1)addPair(m[i],m[j]);
      }
    });
  }
  const sorted=Object.keys(pairs).map(function(k){
    const sp=k.split('-');
    return{a:+sp[0],b:+sp[1],c:pairs[k]};
  }).sort(function(x,y){return y.c-x.c;});
  if(!sorted.length||sorted[0].c<2){
    body.innerHTML='<div class="kuse-empty">有意なペアが見つかりませんでした</div>';
    return;
  }
  const top=sorted.slice(0,15);
  const maxC=top[0].c;
  let html='<div class="pair-list">';
  top.forEach(function(p,i){
    const w=(p.c/maxC*100).toFixed(0);
    const link=pairMode==='seq'?'➡':(pairMode==='adj'?'＋':'＆');
    html+='<div class="pair-row"><span class="pair-rank">#'+(i+1)+'</span>'
      +'<div class="pair-nums"><span class="pair-num">'+p2(p.a)+'</span>'
      +'<span class="pair-link">'+link+'</span>'
      +'<span class="pair-num">'+p2(p.b)+'</span></div>'
      +'<div class="pair-bar"><div class="pair-bar-fill" style="width:'+w+'%;"></div></div>'
      +'<span class="pair-cnt"><b>'+p.c+'</b>回</span>'
      +'</div>';
  });
  html+='</div>';
  const modeDesc={
    same:'同じ回に一緒に出たペア。買い目で組合わせ買いの参考に',
    seq:'前回出た数字の次の回に再度出た数字。引っ張り＆連動',
    adj:'同じ回に「数字差±1」で隣接して出たペア。階段ペア'
  };
  html+='<div style="margin-top:8px;font-size:.6em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;line-height:1.6;">📖 '+modeDesc[pairMode]+'</div>';
  body.innerHTML=html;
}
</script>

<script>
// ============================================================
// 末尾分析(tail) + ホイール
// ============================================================
function drawTailAnalysis(){
  const body=document.getElementById('tailBody');
  if(!body)return;
  if(!lastData.length){body.innerHTML='<div class="kuse-empty">📂 データを読み込んでください</div>';return;}
  const tail={};for(let i=0;i<10;i++)tail[i]=0;
  let total=0;
  lastData.forEach(function(m){m.forEach(function(n){tail[n%10]++;total++;});});
  const avg=total/10;
  let chi=0;
  for(let i=0;i<10;i++){chi+=Math.pow(tail[i]-avg,2)/(avg||1);}
  let max=0;for(let i=0;i<10;i++){if(tail[i]>max)max=tail[i];}
  if(max<1)max=1;
  let html='<div class="tail-bar-row">';
  for(let d=0;d<10;d++){
    const cnt=tail[d];
    const h=Math.max(2,(cnt/max)*70);
    let cls='';
    if(cnt>=avg*1.25)cls='tb-hot';
    else if(cnt<=avg*0.75)cls='tb-cold';
    html+='<div class="tail-bar-col"><div class="tail-bar-bar '+cls+'" style="height:'+h+'px;"><span class="tail-bar-cnt">'+cnt+'</span></div><div class="tail-bar-lbl">末'+d+'</div></div>';
  }
  html+='</div>';
  const sortedT=[];for(let i=0;i<10;i++)sortedT.push([i,tail[i]]);
  sortedT.sort(function(a,b){return b[1]-a[1];});
  const hot=sortedT.slice(0,2);
  const cold=sortedT.slice(-2).reverse();
  html+='<div class="tail-summary">';
  html+='📊 全'+lastData.length+'回・出現数字'+total+'個 / 末尾平均 <b>'+avg.toFixed(1)+'回</b><br>';
  html+='🔥 ホット末尾: '+hot.map(function(p){return'<span class="thot">末'+p[0]+'</span>('+p[1]+'回)';}).join(' / ')+'<br>';
  html+='❄ コールド末尾: '+cold.map(function(p){return'<span class="tcold">末'+p[0]+'</span>('+p[1]+'回)';}).join(' / ')+'<br>';
  const skewLevel=chi<10?'均等':(chi<20?'やや偏り':(chi<35?'偏りあり':'強い偏り'));
  html+='📈 偏りスコア(χ²): <b>'+chi.toFixed(1)+'</b> → '+skewLevel;
  if(cold[0]&&cold[0][1]<=avg*0.7){
    html+='<br>💡 末尾<b>'+cold[0][0]+'</b>は冷え気味（'+cold[0][1]+'回）→ そろそろ反発の可能性';
  }
  html+='</div>';
  // ホイール準備
  const chrono = lastData.slice().reverse();
  const srcDraws=DB[CL]._viewDraws||DB[CL]._customDraws||DB[CL].draws||[];
  const srcView=srcDraws.slice(0,lastData.length).slice().reverse();
  const tailsPerRow = chrono.map(function(m){
    const s={};m.forEach(function(n){s[n%10]=true;});
    return s;
  });
  const dryStreak={};
  for(let d=0;d<10;d++){
    let cnt=0;
    for(let i=tailsPerRow.length-1;i>=0;i--){
      if(tailsPerRow[i][d])break;
      cnt++;
    }
    dryStreak[d]=cnt;
  }
  function fmtDate(dt){
    if(!dt)return null;
    return dt.y+'/'+(''+dt.m).padStart(2,'0')+'/'+(''+dt.d).padStart(2,'0');
  }
  function getRebirthFollowups(d){
    const distShort={};const distLong={};
    for(let i=0;i<10;i++){distShort[i]=0;distLong[i]=0;}
    const out={};for(let i=0;i<10;i++)out[i]=0;
    let totalEvents=0,shortEvents=0,longEvents=0;
    let lastSeen=-1;
    const examples=[];
    for(let i=0;i<tailsPerRow.length;i++){
      if(tailsPerRow[i][d]){
        const gap=lastSeen>=0?(i-lastSeen-1):i;
        if(gap>=2 && i+1<tailsPerRow.length){
          const nextSet=tailsPerRow[i+1];
          const nextTails=[];
          const isShort=gap<=3;
          for(let dd=0;dd<10;dd++){
            if(nextSet[dd]){
              out[dd]++;
              nextTails.push(dd);
              if(isShort)distShort[dd]++;
              else distLong[dd]++;
            }
          }
          if(isShort)shortEvents++;else longEvents++;
          const rebirthDraw=srcView[i];
          const nextDraw=srcView[i+1];
          const rebirthMatchedNums=rebirthDraw?(rebirthDraw.m||[]).filter(function(n){return n%10===d;}):[];
          const nextNumsByTail={};
          if(nextDraw){
            (nextDraw.m||[]).forEach(function(n){
              const t=n%10;
              if(!nextNumsByTail[t])nextNumsByTail[t]=[];
              nextNumsByTail[t].push(n);
            });
          }
          examples.push({
            gap:gap,isShort:isShort,
            rebirthRound:rebirthDraw?rebirthDraw.r:null,
            rebirthDate:rebirthDraw?fmtDate(rebirthDraw.date):null,
            rebirthMatchedNums:rebirthMatchedNums,
            rebirthAllNums:rebirthDraw?(rebirthDraw.m||[]).slice():[],
            nextRound:nextDraw?nextDraw.r:null,
            nextDate:nextDraw?fmtDate(nextDraw.date):null,
            nextTails:nextTails,
            nextNumsByTail:nextNumsByTail,
            nextAllNums:nextDraw?(nextDraw.m||[]).slice():[]
          });
          totalEvents++;
        }
        lastSeen=i;
      }
    }
    examples.reverse();
    return{
      events:totalEvents,dist:out,examples:examples,
      shortEvents:shortEvents,longEvents:longEvents,
      distShort:distShort,distLong:distLong
    };
  }
  const wheelData=[];
  for(let d=0;d<10;d++){
    wheelData.push({d:d,streak:dryStreak[d],fr:getRebirthFollowups(d)});
  }
  let initIdx=0,maxS=-1;
  wheelData.forEach(function(w,i){if(w.streak>maxS){maxS=w.streak;initIdx=i;}});
  html+='<div style="margin-top:10px;background:rgba(10,22,40,.85);border:1px solid var(--WRN);border-radius:8px;padding:9px 10px;">';
  html+='<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px;">';
  html+='<div style="font-family:\'Inter\',sans-serif;font-size:.7em;color:var(--WRN);letter-spacing:1px;font-weight:700;">🌊 末尾フローホイール</div>';
  html+='<div style="font-size:.55em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;">スワイプ / クリック</div>';
  html+='</div>';
  html+='<div id="tailWheel" data-idx="'+initIdx+'" style="position:relative;background:rgba(0,0,0,.3);border-radius:10px;padding:8px 0;overflow:hidden;">';
  html+='<button onclick="rotateTailWheel(-1)" style="position:absolute;left:5px;top:50%;transform:translateY(-50%);background:rgba(245,158,11,.2);border:1px solid var(--WRN);color:var(--WRN);border-radius:50%;width:28px;height:28px;cursor:pointer;font-size:1em;font-weight:700;z-index:5;line-height:1;padding:0;">‹</button>';
  html+='<button onclick="rotateTailWheel(1)" style="position:absolute;right:5px;top:50%;transform:translateY(-50%);background:rgba(245,158,11,.2);border:1px solid var(--WRN);color:var(--WRN);border-radius:50%;width:28px;height:28px;cursor:pointer;font-size:1em;font-weight:700;z-index:5;line-height:1;padding:0;">›</button>';
  html+='<div id="tailWheelTrack" style="display:flex;align-items:center;justify-content:center;gap:6px;padding:0 38px;transition:opacity .15s;"></div>';
  html+='</div>';
  html+='<div id="tailWheelDetail" style="margin-top:8px;"></div>';
  html+='<div id="tailWheelDots" style="display:flex;justify-content:center;gap:5px;margin-top:8px;"></div>';
  html+='<div style="font-size:.55em;color:var(--DIM);margin-top:6px;font-family:\'JetBrains Mono\',monospace;line-height:1.5;text-align:center;">📖 過去に同じくらい空いた後復活した時、翌回によく来てた末尾</div>';
  html+='</div>';
  window._tailWheelData = wheelData;
  body.innerHTML=html;
  renderTailWheel();
}
function renderTailWheel(){
  const wheel=document.getElementById('tailWheel');
  if(!wheel)return;
  const data=window._tailWheelData||[];
  if(!data.length)return;
  let idx=parseInt(wheel.dataset.idx,10);
  if(isNaN(idx))idx=0;
  idx=((idx%10)+10)%10;
  wheel.dataset.idx=idx;
  const track=document.getElementById('tailWheelTrack');
  if(track){
    let h='';
    [-2,-1,0,1,2].forEach(function(off){
      const i=((idx+off)%10+10)%10;
      const w=data[i];
      const isCenter=off===0;
      const opacity=isCenter?1:(Math.abs(off)===1?0.5:0.25);
      const sc=isCenter?1:(Math.abs(off)===1?0.75:0.55);
      const fontSize=isCenter?'1.8em':'1.2em';
      const bg=isCenter?'linear-gradient(135deg,rgba(245,158,11,.35),rgba(251,191,36,.15))':'rgba(30,41,59,.5)';
      const bd=isCenter?'2px solid var(--WRN)':'1px solid var(--BDR)';
      const shadow=isCenter?'0 0 16px rgba(245,158,11,.4)':'none';
      const streakColor=isCenter?'var(--DELTA)':'var(--TXD)';
      h+='<div onclick="setTailWheelIdx('+i+')" style="cursor:pointer;flex-shrink:0;width:'+(isCenter?'100px':(Math.abs(off)===1?'70px':'50px'))+';height:'+(isCenter?'100px':(Math.abs(off)===1?'70px':'50px'))+';';
      h+='display:flex;flex-direction:column;align-items:center;justify-content:center;background:'+bg+';border:'+bd+';border-radius:14px;';
      h+='opacity:'+opacity+';transform:scale('+sc+');transition:all .25s ease;box-shadow:'+shadow+';font-family:\'JetBrains Mono\',monospace;">';
      h+='<div style="font-size:'+fontSize+';font-weight:900;color:'+(isCenter?'#fde68a':'var(--TX)')+';line-height:1;">末'+w.d+'</div>';
      if(isCenter){
        h+='<div style="font-size:.55em;color:'+streakColor+';margin-top:4px;font-weight:700;">'+w.streak+'回ぶり</div>';
      }
      h+='</div>';
    });
    track.innerHTML=h;
  }
  const det=document.getElementById('tailWheelDetail');
  if(det){
    const w=data[idx];
    let h='';
    if(!w.fr.events){
      h='<div style="text-align:center;padding:14px;font-size:.7em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;">過去に類似の復活データなし</div>';
    }else{
      const isCurrentShort = w.streak<=3;
      const matchTypeLabel = isCurrentShort ? '短期(2-3回ぶり)' : '長期(4回以上ぶり)';
      const matchEvents = isCurrentShort ? w.fr.shortEvents : w.fr.longEvents;
      const matchDist = isCurrentShort ? w.fr.distShort : w.fr.distLong;
      h+='<div style="display:flex;align-items:center;justify-content:space-between;background:linear-gradient(90deg,rgba(168,85,247,.15),rgba(245,158,11,.1));border:1px solid var(--PRP);border-radius:7px;padding:5px 9px;margin-bottom:7px;font-family:\'JetBrains Mono\',monospace;font-size:.65em;flex-wrap:wrap;gap:4px;">';
      h+='<span style="color:var(--TX);">📍 現在: <b style="color:#fde68a;">'+w.streak+'回ぶり</b> = ';
      h+='<b style="color:'+(isCurrentShort?'#67e8f9':'#a855f7')+';">'+matchTypeLabel+'</b>タイプ</span>';
      h+='<span style="color:var(--DIM);font-size:.85em;">該当前例: <b style="color:var(--TX);">'+matchEvents+'件</b>/全'+w.fr.events+'件</span>';
      h+='</div>';
      h+='<div style="display:flex;gap:3px;margin-bottom:6px;flex-wrap:wrap;">';
      ['match','all','short','long'].forEach(function(mode){
        const labels={match:'🎯 該当タイプ',all:'全体',short:'短期(2-3)',long:'長期(4+)'};
        const isActive=(window._tailDetailMode||'match')===mode;
        const cls=isActive?'pair-tab on':'pair-tab';
        h+='<div class="'+cls+'" onclick="setTailDetailMode(\''+mode+'\')" style="font-size:.55em;">'+labels[mode]+'</div>';
      });
      h+='</div>';
      const dispMode = window._tailDetailMode||'match';
      let useDist, useEvents, useLabel;
      if(dispMode==='match'){useDist=matchDist;useEvents=matchEvents;useLabel=matchTypeLabel;}
      else if(dispMode==='short'){useDist=w.fr.distShort;useEvents=w.fr.shortEvents;useLabel='短期';}
      else if(dispMode==='long'){useDist=w.fr.distLong;useEvents=w.fr.longEvents;useLabel='長期';}
      else{useDist=w.fr.dist;useEvents=w.fr.events;useLabel='全体';}
      if(useEvents===0){
        h+='<div style="text-align:center;padding:10px;font-size:.65em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;">'+useLabel+'の前例なし</div>';
      }else{
        const sorted=Object.keys(useDist).map(function(k){return[+k,useDist[k]];})
          .filter(function(s){return s[1]>0;})
          .sort(function(a,b){return b[1]-a[1];})
          .slice(0,5);
        h+='<div style="text-align:center;font-size:.6em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;margin-bottom:6px;">';
        h+='<b style="color:var(--WRN);">'+useLabel+'</b>復活時の翌回TOP5 <span style="color:var(--DIM);">('+useEvents+'件)</span></div>';
        h+='<div style="display:flex;justify-content:center;align-items:center;gap:5px;flex-wrap:wrap;font-family:\'JetBrains Mono\',monospace;">';
        sorted.forEach(function(s,i){
          const pct=(s[1]/useEvents*100).toFixed(0);
          const isTop=i===0;const isSecond=i===1;
          const bg=isTop?'rgba(245,158,11,.3)':(isSecond?'rgba(245,158,11,.15)':'rgba(245,158,11,.08)');
          const bd=isTop?'var(--WRN)':'var(--BDR)';
          const col=isTop?'#fde68a':(isSecond?'var(--WRN)':'var(--TXD)');
          const sz=isTop?'.95em':(isSecond?'.85em':'.78em');
          h+='<span style="background:'+bg+';border:1.5px solid '+bd+';color:'+col+';padding:4px 9px;border-radius:7px;font-weight:700;font-size:'+sz+';">末'+s[0]+' <span style="opacity:.75;font-size:.85em;">'+pct+'%</span></span>';
        });
        h+='</div>';
      }
    }
    det.innerHTML=h;
  }
  const dots=document.getElementById('tailWheelDots');
  if(dots){
    let h='';
    for(let i=0;i<10;i++){
      const w=data[i];
      const isActive=i===idx;
      const isHot=w.streak>=2;
      const sz=isActive?10:6;
      const bg=isActive?'var(--WRN)':(isHot?'rgba(59,130,246,.7)':'rgba(100,116,139,.4)');
      h+='<div onclick="setTailWheelIdx('+i+')" style="width:'+sz+'px;height:'+sz+'px;border-radius:50%;background:'+bg+';cursor:pointer;transition:all .2s;"></div>';
    }
    dots.innerHTML=h;
  }
}
function rotateTailWheel(dir){
  const wheel=document.getElementById('tailWheel');
  if(!wheel)return;
  let idx=parseInt(wheel.dataset.idx,10);
  if(isNaN(idx))idx=0;
  idx=((idx+dir)%10+10)%10;
  wheel.dataset.idx=idx;
  renderTailWheel();
}
function setTailWheelIdx(i){
  const wheel=document.getElementById('tailWheel');
  if(!wheel)return;
  wheel.dataset.idx=i;
  renderTailWheel();
}
function setTailDetailMode(m){
  window._tailDetailMode=m;
  renderTailWheel();
}
let _tailSwipeAttached=false;
function setupTailWheelSwipe(){
  if(_tailSwipeAttached)return;
  _tailSwipeAttached=true;
  let startX=null,startIdx=null;
  document.addEventListener('touchstart',function(e){
    const wheel=e.target.closest&&e.target.closest('#tailWheel');
    if(!wheel)return;
    if(e.target.tagName==='BUTTON')return;
    startX=e.touches[0].clientX;
    startIdx=parseInt(wheel.dataset.idx,10);
  },{passive:true});
  document.addEventListener('touchmove',function(e){
    if(startX===null)return;
    const wheel=document.getElementById('tailWheel');
    if(!wheel)return;
    const dx=e.touches[0].clientX-startX;
    if(Math.abs(dx)>40){
      const dir=dx<0?1:-1;
      let idx=((startIdx+dir)%10+10)%10;
      wheel.dataset.idx=idx;
      renderTailWheel();
      startX=e.touches[0].clientX;
      startIdx=idx;
    }
  },{passive:true});
  document.addEventListener('touchend',function(){startX=null;startIdx=null;});
}

// ============================================================
// ジオメトリ予測
// ============================================================
let geoMode='all';
function switchGeoMode(m,el){
  geoMode=m;
  document.querySelectorAll('.geo-tab').forEach(function(t){t.classList.remove('on');});
  if(el)el.classList.add('on');
  drawGeoPanel();
}
function findTriplets(nums){
  const set={};nums.forEach(function(n){set[n]=true;});
  const sorted=nums.slice().sort(function(a,b){return a-b;});
  const triplets=[];
  for(let i=0;i<sorted.length;i++){
    for(let j=i+1;j<sorted.length;j++){
      const a=sorted[i],b=sorted[j];
      const diff=b-a;
      if(diff<2)continue;
      const c=b+diff;
      if(set[c])triplets.push({a:a,b:b,c:c,d:diff});
    }
  }
  return triplets;
}
function findMidpoints(nums){
  const sorted=nums.slice().sort(function(a,b){return a-b;});
  const set={};nums.forEach(function(n){set[n]=true;});
  const mids=[];
  for(let i=0;i<sorted.length;i++){
    for(let j=i+1;j<sorted.length;j++){
      const a=sorted[i],b=sorted[j];
      const sum=a+b;
      if(sum%2!==0)continue;
      const m=sum/2;
      if(m===a||m===b)continue;
      mids.push({a:a,b:b,m:m,present:!!set[m]});
    }
  }
  return mids;
}
function findClusters(nums, maxN){
  const density=[];
  for(let n=1;n<=maxN;n++){
    let cnt=0;
    nums.forEach(function(x){if(Math.abs(x-n)<=3)cnt++;});
    density.push({n:n,cnt:cnt});
  }
  return density;
}
function drawGeoPanel(){
  const body=document.getElementById('geoBody');
  if(!body)return;
  if(!lastData.length){body.innerHTML='<div class="geo-empty">📂 データを読み込んでください</div>';return;}
  const c=cfg();
  const maxN=c.max;
  const recent=lastData[0]||[];
  const recentSorted=recent.slice().sort(function(a,b){return a-b;});
  let html='';
  html+='<div class="geo-current">';
  html+='<b>📍 直近回の数字</b>: ';
  html+=recentSorted.map(function(n){return p2(n);}).join(' / ');
  html+='</div>';
  if(geoMode==='all'||geoMode==='tri'){
    const recentTri=findTriplets(recent);
    const diffFreq={};
    lastData.forEach(function(m){
      findTriplets(m).forEach(function(t){
        diffFreq[t.d]=(diffFreq[t.d]||0)+1;
      });
    });
    html+='<div class="geo-section">';
    html+='<div class="geo-section-h">🔺 等間隔3つ組（公差d）</div>';
    if(recentTri.length){
      html+='<div style="font-size:.65em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;line-height:1.8;margin-bottom:5px;">直近回の三角:</div>';
      recentTri.forEach(function(t){
        html+='<span class="geo-tri">'+p2(t.a)+'<span class="geo-tri-arrow">→</span>'+p2(t.b)+'<span class="geo-tri-arrow">→</span>'+p2(t.c)+' <span style="color:var(--DIM);font-size:.85em;">d='+t.d+'</span></span>';
      });
      const extPreds={};
      recentTri.forEach(function(t){
        const ext=t.c+t.d;
        if(ext>=1&&ext<=maxN)extPreds[ext]=(extPreds[ext]||0)+1;
        const back=t.a-t.d;
        if(back>=1&&back<=maxN)extPreds[back]=(extPreds[back]||0)+1;
      });
      const topPreds=Object.keys(extPreds).map(function(k){return[+k,extPreds[k]];})
        .sort(function(a,b){return b[1]-a[1];}).slice(0,6);
      if(topPreds.length){
        html+='<div style="margin-top:7px;font-size:.65em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;line-height:1.8;">📐 三角の<b style="color:var(--WRN);">延長点</b>（前後に+d/-d）:</div>';
        topPreds.forEach(function(p){
          html+='<span class="geo-pred">'+p2(p[0])+(p[1]>1?' ×'+p[1]:'')+'</span>';
        });
      }
    }else{
      html+='<div style="font-size:.65em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;">直近回には等間隔3つ組なし</div>';
    }
    const sortedD=Object.keys(diffFreq).map(function(k){return[+k,diffFreq[k]];})
      .sort(function(a,b){return b[1]-a[1];}).slice(0,5);
    if(sortedD.length){
      html+='<div style="margin-top:7px;padding-top:5px;border-top:1px dashed var(--BDR);font-size:.6em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;line-height:1.7;">';
      html+='📊 過去によく出る公差: ';
      html+=sortedD.map(function(p){return'<b style="color:#f9a8d4;">d='+p[0]+'</b>('+p[1]+'回)';}).join(' / ');
      html+='</div>';
    }
    html+='</div>';
  }
  if(geoMode==='all'||geoMode==='mid'){
    const recentMids=findMidpoints(recent);
    let totalMidEvents=0,hitMidEvents=0;
    for(let i=0;i<lastData.length-1;i++){
      const cur=lastData[i+1];
      const nxt=lastData[i];
      const mids=findMidpoints(cur);
      mids.forEach(function(mm){
        if(mm.present)return;
        totalMidEvents++;
        if(nxt.indexOf(mm.m)>=0)hitMidEvents++;
      });
    }
    const hitRate=totalMidEvents>0?(hitMidEvents/totalMidEvents*100):0;
    html+='<div class="geo-section">';
    html+='<div class="geo-section-h">📏 対称ペアの中点</div>';
    const futureMids={};
    recentMids.forEach(function(mm){
      if(!mm.present)futureMids[mm.m]=(futureMids[mm.m]||0)+1;
    });
    const topMids=Object.keys(futureMids).map(function(k){return[+k,futureMids[k]];})
      .sort(function(a,b){return b[1]-a[1];}).slice(0,8);
    if(topMids.length){
      html+='<div style="font-size:.65em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;line-height:1.8;">';
      html+='次回候補（複数ペアの中点になってる数字 = 強い予測）:';
      html+='</div>';
      topMids.forEach(function(p){
        html+='<span class="geo-pred">'+p2(p[0])+(p[1]>1?' ('+p[1]+'ペア)':'')+'</span>';
      });
      const top=topMids[0];
      if(top){
        const exPair=recentMids.filter(function(mm){return mm.m===top[0]&&!mm.present;})[0];
        if(exPair){
          html+='<div style="margin-top:5px;font-size:.6em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;line-height:1.6;">';
          html+='例: '+p2(exPair.a)+' ↔ <span class="geo-mid">'+p2(exPair.m)+'</span> ↔ '+p2(exPair.b);
          html+='</div>';
        }
      }
    }else{
      html+='<div style="font-size:.65em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;">直近回内に整数中点ペアが無いか、全て既に出ています</div>';
    }
    if(totalMidEvents>0){
      html+='<div style="margin-top:7px;padding-top:5px;border-top:1px dashed var(--BDR);font-size:.6em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;line-height:1.7;">';
      html+='📈 過去的中率: <b style="color:'+(hitRate>=15?'var(--WRN)':'var(--DIM)')+';">'+hitRate.toFixed(1)+'%</b> ('+hitMidEvents+'/'+totalMidEvents+'件)';
      html+='</div>';
    }
    html+='</div>';
  }
  if(geoMode==='all'||geoMode==='clu'){
    const dens=findClusters(recent,maxN);
    const maxDens=Math.max.apply(null,dens.map(function(d){return d.cnt;}).concat([1]));
    const centers=lastData.map(function(m){
      if(!m.length)return null;
      return m.reduce(function(a,b){return a+b;},0)/m.length;
    });
    const recentCenter=centers[0];
    const prevCenter=centers[1];
    const driftAvg=centers.length>=10
      ? (centers.slice(0,5).reduce(function(a,b){return a+b;},0)/5 - centers.slice(5,10).reduce(function(a,b){return a+b;},0)/5)
      : 0;
    html+='<div class="geo-section">';
    html+='<div class="geo-section-h">🌀 クラスタ密集帯</div>';
    html+='<div class="geo-cluster-bar">';
    dens.forEach(function(d){
      let cls='';
      if(d.cnt>=Math.max(3,maxDens*0.75))cls='hot';
      else if(d.cnt>=Math.max(2,maxDens*0.5))cls='warm';
      else if(d.cnt>=1)cls='cold';
      const title='数字'+d.n+': ±3範囲内に'+d.cnt+'個';
      html+='<div class="geo-cluster-cell '+cls+'" title="'+title+'"></div>';
    });
    html+='</div>';
    html+='<div style="display:flex;justify-content:space-between;font-size:.55em;color:var(--DIM);font-family:\'JetBrains Mono\',monospace;margin-bottom:5px;"><span>1</span><span>'+Math.floor(maxN/2)+'</span><span>'+maxN+'</span></div>';
    const hotCells=dens.filter(function(d){return d.cnt>=Math.max(3,maxDens*0.75);});
    if(hotCells.length){
      const peaks=[];
      hotCells.forEach(function(d){
        const isPeak=hotCells.every(function(d2){
          if(d2.n===d.n)return true;
          if(Math.abs(d2.n-d.n)<=4)return d2.cnt<=d.cnt;
          return true;
        });
        if(isPeak)peaks.push(d);
      });
      html+='<div style="font-size:.65em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;line-height:1.8;margin-top:4px;">';
      html+='🔥 ホットクラスタの中心:';
      html+='</div>';
      peaks.slice(0,5).forEach(function(p){
        html+='<span class="geo-pred">'+p2(p.n)+' <span style="font-size:.75em;opacity:.8;">×'+p.cnt+'</span></span>';
      });
    }
    html+='<div style="margin-top:7px;padding-top:5px;border-top:1px dashed var(--BDR);font-size:.62em;color:var(--TXD);font-family:\'JetBrains Mono\',monospace;line-height:1.7;">';
    if(recentCenter!==null&&prevCenter!==null){
      const drift=recentCenter-prevCenter;
      const arrow=drift>0.5?'⬆ 上昇':(drift<-0.5?'⬇ 下降':'➡ 横ばい');
      html+='📐 重心: 直近 <b style="color:#f9a8d4;">'+recentCenter.toFixed(1)+'</b> / 前回 '+prevCenter.toFixed(1)+' '+arrow+'<br>';
    }
    if(centers.length>=10){
      const direction=driftAvg>0.5?'<b style="color:#fca5a5;">上昇トレンド</b>':(driftAvg<-0.5?'<b style="color:#93c5fd;">下降トレンド</b>':'<b style="color:var(--DIM);">安定</b>');
      const projectedNext=recentCenter+driftAvg*0.5;
      html+='📈 5回平均ドリフト: '+(driftAvg>=0?'+':'')+driftAvg.toFixed(2)+' / '+direction+'<br>';
      html+='🎯 次回重心予測: <b style="color:var(--WRN);">'+projectedNext.toFixed(1)+'</b> 付近';
    }
    html+='</div>';
    html+='</div>';
  }
  body.innerHTML=html;
}

// ============================================================
// TYPE PREDICTOR
// ============================================================
const typePredict = {
  last:null,
  classifyZone:function(nums){
    const c=cfg();
    const z=Math.floor(c.max/3);
    const z1=nums.filter(function(n){return n<=z;}).length;
    const z2=nums.filter(function(n){return n>z&&n<=z*2;}).length;
    const z3=nums.filter(function(n){return n>z*2;}).length;
    const half=c.mc*0.5;
    if(z1>=half)return'L_HEAVY';
    if(z3>=half)return'H_HEAVY';
    if(z2>=half)return'M_HEAVY';
    if(Math.abs(z1-z3)<=1&&z2>=1)return'BALANCED';
    return'MIXED';
  },
  classifyOE:function(nums){
    const c=cfg();
    const odd=nums.filter(function(n){return n%2===1;}).length;
    if(odd===0)return'ALL_EVEN';
    if(odd===c.mc)return'ALL_ODD';
    if(odd>=c.mc*0.7)return'ODD_HEAVY';
    if(odd<=c.mc*0.3)return'EVEN_HEAVY';
    return'BALANCED_OE';
  },
  labelZone:function(t){
    const m={L_HEAVY:'川上偏重',M_HEAVY:'中域偏重',H_HEAVY:'川下偏重',BALANCED:'バランス',MIXED:'混合'};
    return m[t]||t;
  },
  labelOE:function(t){
    const m={ALL_ODD:'全奇',ALL_EVEN:'全偶',ODD_HEAVY:'奇多',EVEN_HEAVY:'偶多',BALANCED_OE:'均衡'};
    return m[t]||t;
  },
  badgeClass:function(t){
    const m={L_HEAVY:'tp-bz',H_HEAVY:'tp-bz',M_HEAVY:'tp-by',BALANCED:'tp-bb',MIXED:'tp-bm',
      ALL_ODD:'tp-bz',ALL_EVEN:'tp-bz',ODD_HEAVY:'tp-by',EVEN_HEAVY:'tp-by',BALANCED_OE:'tp-bb'};
    return m[t]||'tp-bm';
  },
  buildTransitions:function(draws){
    const chrono=draws.slice().reverse();
    const self=this;
    const zT=chrono.map(function(d){return self.classifyZone(d.m||d);});
    const oT=chrono.map(function(d){return self.classifyOE(d.m||d);});
    const zTr={},oTr={};
    for(let i=0;i<zT.length-1;i++){
      const sz=zT[i],dz=zT[i+1];
      if(!zTr[sz])zTr[sz]={};
      zTr[sz][dz]=(zTr[sz][dz]||0)+1;
      const so=oT[i],dom=oT[i+1];
      if(!oTr[so])oTr[so]={};
      oTr[so][dom]=(oTr[so][dom]||0)+1;
    }
    return{zoneTrans:zTr,oeTrans:oTr};
  },
  topProbs:function(tr,src){
    if(!tr[src])return[];
    const e=Object.keys(tr[src]).map(function(k){return[k,tr[src][k]];});
    const tot=e.reduce(function(s,x){return s+x[1];},0);
    return e.map(function(x){return{type:x[0],count:x[1],prob:x[1]/tot};})
      .sort(function(a,b){return b.prob-a.prob;}).slice(0,3);
  },
  analyzeTail:function(draws){
    const f=new Array(10).fill(0);
    draws.forEach(function(d){(d.m||d).forEach(function(n){f[n%10]++;});});
    const recent=draws.slice(0,Math.min(5,draws.length));
    const rs={};
    recent.forEach(function(d){(d.m||d).forEach(function(n){rs[n%10]=true;});});
    const cold=[];for(let i=0;i<10;i++)if(!rs[i])cold.push(i);
    const lt=(draws[0].m||draws[0]).map(function(n){return n%10;});
    const ls={};lt.forEach(function(x){ls[x]=true;});
    let cs=0,cnt=0;
    for(let i=0;i<draws.length-1;i++){
      const a={},b={};
      (draws[i].m||draws[i]).forEach(function(n){a[n%10]=true;});
      (draws[i+1].m||draws[i+1]).forEach(function(n){b[n%10]=true;});
      let int=0;
      Object.keys(a).forEach(function(x){if(b[x])int++;});
      cs+=int;cnt++;
    }
    const ca=cnt>0?cs/cnt:0;
    return{freq:f,cold:cold,lastSet:ls,lastTails:lt,carryAvg:ca};
  },
  calcRoughScore:function(draws){
    if(draws.length<5)return{score:0,reasons:[]};
    const reasons=[];let score=0;
    const sums=draws.map(function(d){return(d.m||d).reduce(function(a,b){return a+b;},0);});
    const avg=sums.reduce(function(a,b){return a+b;},0)/sums.length;
    const sd=Math.sqrt(sums.reduce(function(s,v){return s+(v-avg)*(v-avg);},0)/sums.length);
    const ld=Math.abs(sums[0]-avg)/(sd||1);
    if(ld>1.5){score+=25;reasons.push('Σ偏差'+ld.toFixed(1)+'σ');}
    else if(ld>1.0){score+=12;reasons.push('Σやや偏('+ld.toFixed(1)+'σ)');}
    const ln=(draws[0].m||draws[0]).slice().sort(function(a,b){return a-b;});
    let cs=0;
    for(let i=1;i<ln.length;i++)if(ln[i]-ln[i-1]===1)cs++;
    if(cs>=3){score+=30;reasons.push('連続'+(cs+1)+'個');}
    else if(cs>=2){score+=15;reasons.push('連続'+(cs+1)+'個');}
    if(draws.length>=2){
      const a={},b={};
      (draws[0].m||draws[0]).forEach(function(n){a[n]=true;});
      (draws[1].m||draws[1]).forEach(function(n){b[n]=true;});
      let cr=0;
      Object.keys(a).forEach(function(n){if(b[n])cr++;});
      if(cr>=4){score+=25;reasons.push('引っ張り'+cr+'個');}
      else if(cr===0){score+=10;reasons.push('引っ張り0(切断)');}
    }
    return{score:Math.min(100,score),reasons:reasons};
  },
  run:function(){
    const c=cfg();
    const src=DB[CL]._viewDraws||DB[CL]._customDraws||c.draws;
    if(!src.length||src.length<3){this.last=null;return null;}
    const ld=src[0];
    const lz=this.classifyZone(ld.m),lo=this.classifyOE(ld.m);
    const tr=this.buildTransitions(src);
    const zn=this.topProbs(tr.zoneTrans,lz);
    const on=this.topProbs(tr.oeTrans,lo);
    const tail=this.analyzeTail(src);
    const rough=this.calcRoughScore(src);
    const cars=[];
    for(let i=0;i<src.length-1;i++){
      const a={},b={};
      src[i].m.forEach(function(n){a[n]=true;});
      src[i+1].m.forEach(function(n){b[n]=true;});
      let int=0;
      Object.keys(a).forEach(function(n){if(b[n])int++;});
      cars.push(int);
    }
    const ca=cars.length?cars.reduce(function(a,b){return a+b;},0)/cars.length:0;
    let cm=0;
    if(cars.length){
      const cnt={};cars.forEach(function(v){cnt[v]=(cnt[v]||0)+1;});
      cm=+Object.keys(cnt).sort(function(a,b){return cnt[b]-cnt[a];})[0];
    }
    this.last={lastZone:lz,lastOE:lo,zoneNext:zn,oeNext:on,tail:tail,rough:rough,carryAvg:ca,carryMode:cm};
    return this.last;
  },
  update:function(){
    const r=this.run();
    if(!r)return;
    const self=this;
    const zCur=document.getElementById('tpZoneCurrent');
    const zNext=r.zoneNext[0]?r.zoneNext[0].type:'?';
    zCur.innerHTML='<span class="tp-badge '+self.badgeClass(r.lastZone)+'">'+self.labelZone(r.lastZone)+
      '</span><span class="tp-arrow">→</span><span class="tp-badge '+self.badgeClass(zNext)+'">'+self.labelZone(zNext)+'</span>';
    document.getElementById('tpZoneProbs').innerHTML=r.zoneNext.map(function(p){
      return'<div class="tp-prob-row"><span class="tp-prob-label">'+self.labelZone(p.type)+
        '</span><div class="tp-prob-bar"><div class="tp-prob-fill" style="width:'+(p.prob*100).toFixed(0)+
        '%"></div></div><span class="tp-prob-pct">'+(p.prob*100).toFixed(0)+'%</span></div>';
    }).join('');
    const oCur=document.getElementById('tpOECurrent');
    const oNext=r.oeNext[0]?r.oeNext[0].type:'?';
    oCur.innerHTML='<span class="tp-badge '+self.badgeClass(r.lastOE)+'">'+self.labelOE(r.lastOE)+
      '</span><span class="tp-arrow">→</span><span class="tp-badge '+self.badgeClass(oNext)+'">'+self.labelOE(oNext)+'</span>';
    document.getElementById('tpOEProbs').innerHTML=r.oeNext.map(function(p){
      return'<div class="tp-prob-row"><span class="tp-prob-label">'+self.labelOE(p.type)+
        '</span><div class="tp-prob-bar"><div class="tp-prob-fill" style="width:'+(p.prob*100).toFixed(0)+
        '%"></div></div><span class="tp-prob-pct">'+(p.prob*100).toFixed(0)+'%</span></div>';
    }).join('');
    const grid=document.getElementById('tpTailGrid');
    const mxF=Math.max.apply(null,r.tail.freq.concat([1]));
    let h='';
    for(let i=0;i<10;i++){
      const f=r.tail.freq[i];
      const ratio=f/(mxF||1);
      let cls='cold';
      if(ratio>=0.85)cls='hot';else if(ratio>=0.65)cls='warm';
      const isLast=r.tail.lastSet[i]?' last':'';
      h+='<div class="tp-tail-cell '+cls+isLast+'">'+i+'</div>';
    }
    grid.innerHTML=h;
    document.getElementById('tpTailRec').innerHTML='直前末尾: <b>'+r.tail.lastTails.join(',')+
      '</b><br>コールド末尾: <b>'+(r.tail.cold.length?r.tail.cold.join(','):'なし')+
      '</b><br>引っ張り平均: <b>'+r.tail.carryAvg.toFixed(1)+'個</b>';
    document.getElementById('tpRoughFill').style.width=r.rough.score+'%';
    let lab='安定';
    if(r.rough.score>=60)lab='荒れ警報';else if(r.rough.score>=35)lab='注意';
    document.getElementById('tpRoughText').textContent=r.rough.score+'/'+lab;
    document.getElementById('tpRoughDetail').innerHTML='要因: <b>'+
      (r.rough.reasons.length?r.rough.reasons.join(' / '):'定型')+
      '</b><br>引っ張り推奨: <b>'+r.carryMode+'個</b>';
  },
  filterByPrediction:function(cand){
    if(!document.getElementById('tpApply').checked)return true;
    if(!this.last)return true;
    const top=this.last.zoneNext[0];
    if(!top||top.prob<0.35)return true;
    const cz=this.classifyZone(cand);
    return this.last.zoneNext.slice(0,2).map(function(p){return p.type;}).indexOf(cz)>=0;
  }
};

// ============================================================
// BIAS ANALYZER
// ============================================================
const biasAnalyzer = {
  last:null,
  compute:function(){
    const c=cfg();
    const src=DB[CL]._customDraws||c.draws;
    if(!src.length)return null;
    const N=src.length;
    const exp=N*c.mc/c.max;
    const f={};for(let n=1;n<=c.max;n++)f[n]=0;
    src.forEach(function(d){(d.m||[]).forEach(function(n){if(f[n]!=null)f[n]++;});});
    const bias=[];
    for(let n=1;n<=c.max;n++){
      const dev=(f[n]-exp)/(exp||1);
      bias.push({num:n,freq:f[n],dev:dev,devPct:dev*100});
    }
    const hot=bias.filter(function(b){return b.devPct>=10;}).sort(function(a,b){return b.devPct-a.devPct;});
    const cold=bias.filter(function(b){return b.devPct<=-10;}).sort(function(a,b){return a.devPct-b.devPct;});
    const str=bias.reduce(function(s,b){return s+Math.abs(b.devPct);},0)/bias.length;
    let rel='low';
    if(N>=300)rel='high';else if(N>=100)rel='mid';
    return{N:N,expected:exp,bias:bias,hot:hot,cold:cold,strength:str,reliability:rel};
  },
  biasScoreFor:function(num){
    if(!this.last)return 0;
    if(!document.getElementById('biasApply').checked)return 0;
    if(this.last.reliability==='low')return 0;
    const it=this.last.bias.filter(function(b){return b.num===num;})[0];
    if(!it)return 0;
    return it.devPct/10;
  },
  update:function(){
    const r=this.compute();
    this.last=r;
    if(!r)return;
    document.getElementById('bpSampleN').textContent=r.N+'回';
    document.getElementById('bpExpected').textContent=r.expected.toFixed(1)+'回';
    document.getElementById('bpHotN').textContent=r.hot.length;
    document.getElementById('bpColdN').textContent=r.cold.length;
    let s=r.strength.toFixed(1)+'%';
    if(r.reliability==='low')s+=' (低)';
    else if(r.reliability==='mid')s+=' (中)';
    else s+=' (高)';
    document.getElementById('bpStrength').textContent=s;
    const hl=document.getElementById('bpHotList');
    if(!r.hot.length)hl.innerHTML='<div class="bp-empty">該当なし</div>';
    else hl.innerHTML=r.hot.slice(0,12).map(function(b){
      return'<div class="bp-item bp-item-hot"><span class="bp-item-num">'+p2(b.num)+
        '</span><span class="bp-item-pct">+'+b.devPct.toFixed(1)+'%</span></div>';
    }).join('');
    const cl=document.getElementById('bpColdList');
    if(!r.cold.length)cl.innerHTML='<div class="bp-empty">該当なし</div>';
    else cl.innerHTML=r.cold.slice(0,12).map(function(b){
      return'<div class="bp-item bp-item-cold"><span class="bp-item-num">'+p2(b.num)+
        '</span><span class="bp-item-pct">'+b.devPct.toFixed(1)+'%</span></div>';
    }).join('');
  }
};
</script>

<script>
// ============================================================
// CSVパーサ
// ============================================================
function detectEncoding(buffer){
  const arr=new Uint8Array(buffer);
  if(arr.length>=3&&arr[0]===0xEF&&arr[1]===0xBB&&arr[2]===0xBF)return 'utf-8';
  let sjisScore=0,utf8Score=0;
  for(let i=0;i<Math.min(arr.length,2048);i++){
    const b=arr[i];
    if(b>=0x81&&b<=0x9F)sjisScore++;
    if(b>=0xE0&&b<=0xFC)sjisScore++;
    if((b&0xE0)===0xC0||(b&0xF0)===0xE0)utf8Score++;
  }
  return sjisScore>utf8Score?'shift-jis':'utf-8';
}
function parseCSV(text){
  const draws=[];
  const lines=text.split(/\r?\n/).filter(function(l){return l.trim();});
  if(!lines.length)return draws;
  const c=cfg();
  const expectedNums=c.mc;
  function parseDate(s){
    if(!s)return null;
    s=s.replace(/['"]/g,'').trim();
    let m=s.match(/(\d{4})[\/\-年](\d{1,2})[\/\-月](\d{1,2})/);
    if(m)return{y:+m[1],m:+m[2],d:+m[3]};
    m=s.match(/(\d{1,2})[\/\-](\d{1,2})[\/\-](\d{4})/);
    if(m)return{y:+m[3],m:+m[1],d:+m[2]};
    return null;
  }
  function isHeaderLine(line){
    // 日本語ヘッダ or 英語ヘッダ の典型語を含めばヘッダとみなしてスキップ
    return /開催回|抽選日|第\d数字|ボーナス|round|date|number|bonus|当選|回号/i.test(line);
  }
  function splitLine(line){
    // タブ優先、次にカンマ、最後にスペース
    if(line.indexOf('\t')>=0)return line.split('\t');
    if(line.indexOf(',')>=0)return line.split(',');
    return line.split(/\s+/);
  }
  // === STRATEGY A: 固定列マッピング (loto-life.net 等) ===
  // 期待形: [回号, 日付, 数字1..mc, ボーナス1..bc, ...賞金等]
  function tryFixedLayout(){
    const out=[];
    for(let li=0;li<lines.length;li++){
      const line=lines[li];
      if(isHeaderLine(line))continue;
      const fields=splitLine(line).map(function(f){return f.replace(/['"]/g,'').trim();});
      if(fields.length<2+expectedNums)continue;
      const round=parseInt(fields[0],10);
      if(isNaN(round)||round<1)continue;
      const date=parseDate(fields[1]);
      if(!date)continue; // 2列目が日付でなければこのレイアウトではない
      // 3列目から expectedNums 個の本数字
      const nums=[];
      for(let i=2;i<2+expectedNums;i++){
        const n=parseInt(fields[i],10);
        if(isNaN(n)||n<1||n>c.max){nums.length=0;break;}
        nums.push(n);
      }
      if(nums.length!==expectedNums)continue;
      // 重複チェック
      const u={};let dup=false;
      nums.forEach(function(n){if(u[n])dup=true;u[n]=true;});
      if(dup)continue;
      // 続く bc 列をボーナス候補
      const bonus=[];
      for(let i=2+expectedNums;i<2+expectedNums+c.bc&&i<fields.length;i++){
        const b=parseInt(fields[i],10);
        if(!isNaN(b)&&b>=1&&b<=c.max&&!u[b])bonus.push(b);
      }
      const obj={r:round, m:nums.sort(function(a,b){return a-b;}), date:date};
      if(bonus.length)obj.b=bonus;
      out.push(obj);
    }
    return out;
  }
  // === STRATEGY B: 緩い解析 (スラッシュ区切り or 手入力テキスト) ===
  function tryLooseLayout(){
    const out=[];
    for(let li=0;li<lines.length;li++){
      const line=lines[li];
      if(isHeaderLine(line))continue;
      const fields=splitLine(line).map(function(f){return f.replace(/['"\s]/g,'').trim();});
      let round=null,date=null;
      let nums=[];
      let bonus=[];
      let foundSlash=false;
      for(let i=0;i<fields.length;i++){
        const f=fields[i];
        if(!f)continue;
        if(f==='/'){foundSlash=true;continue;}
        if(/^\d{4}[\/\-年]\d{1,2}/.test(f)||/^\d{1,2}[\/\-]\d{1,2}[\/\-]\d{4}/.test(f)){
          if(!date)date=parseDate(f);
          continue;
        }
        const n=+f;
        if(isNaN(n))continue;
        if(round===null && n>=100 && n<=99999 && nums.length===0){round=n;continue;}
        if(n>=1 && n<=99){
          if(foundSlash){bonus.push(n);}
          else if(nums.length<expectedNums){nums.push(n);}
          else{bonus.push(n);}
        }
      }
      if(nums.length===expectedNums){
        const u={};let dup=false;
        nums.forEach(function(n){if(u[n])dup=true;u[n]=true;});
        if(!dup){
          const validBonus=bonus.filter(function(n){return n>=1&&n<=c.max&&!u[n];}).slice(0,c.bc);
          const obj={r:round||(out.length+1),m:nums.sort(function(a,b){return a-b;})};
          if(date)obj.date=date;
          if(validBonus.length)obj.b=validBonus;
          out.push(obj);
        }
      }
    }
    return out;
  }
  // 固定列レイアウトを最優先で試す。十分なヒットがあれば採用
  const fixed=tryFixedLayout();
  if(fixed.length>=Math.max(3,Math.floor(lines.length*0.5))){
    fixed.sort(function(a,b){return b.r-a.r;});
    return fixed;
  }
  // フォールバック: 緩い解析
  const loose=tryLooseLayout();
  loose.sort(function(a,b){return b.r-a.r;});
  // 固定でも一部取れていたらマージ (重複は回号でユニーク化)
  if(fixed.length){
    const seen={};
    loose.forEach(function(d){seen[d.r]=d;});
    fixed.forEach(function(d){if(!seen[d.r])seen[d.r]=d;});
    const merged=Object.values(seen);
    merged.sort(function(a,b){return b.r-a.r;});
    return merged;
  }
  return loose;
}
function parseTextareaToDraws(text){
  return parseCSV(text);
}
function setupCSVDrop(){
  const zone=document.getElementById('csvZone');
  const inp=document.getElementById('csvFileInput');
  if(!zone||!inp)return;
  ['dragover','dragenter'].forEach(function(ev){
    zone.addEventListener(ev,function(e){e.preventDefault();e.stopPropagation();zone.classList.add('drag-over');});
  });
  ['dragleave','drop'].forEach(function(ev){
    zone.addEventListener(ev,function(e){e.preventDefault();e.stopPropagation();zone.classList.remove('drag-over');});
  });
  zone.addEventListener('drop',function(e){
    const f=e.dataTransfer.files[0];
    if(f)loadCSVFile(f);
  });
  inp.addEventListener('change',function(){
    if(inp.files[0])loadCSVFile(inp.files[0]);
  });
}
function loadCSVFile(file){
  const r=new FileReader();
  r.onload=function(){
    try{
      const buf=r.result;
      const enc=detectEncoding(buf);
      const dec=new TextDecoder(enc);
      const text=dec.decode(buf);
      const draws=parseCSV(text);
      if(!draws.length){
        showCSVStatus('解析失敗（'+cfg().label+'と一致せず）','err');
        return;
      }
      DB[CL]._customDraws=draws;
      DB[CL]._viewDraws=null;
      DB[CL]._fileName=file.name;
      allDraws=draws.slice();
      // CSV読込時はdisplayNを全件にリセット (ユーザーが意図的に絞ってない限り全部表示)
      // 表示件数: デフォルトは最大200件 (それ以上はスライダーで広げる)
      displayN=Math.min(draws.length,200);
      updateSliderMax();
      reflectToTextarea();
      applyRangeAndAnalyze();
      // localStorageに保存 (ブラウザ閉じても残るように)
      saveCSVToStorage(draws,file.name);
      showCSVStatus('✓ '+file.name+' '+draws.length+'件読込','ok');
      document.getElementById('csvMeta').textContent='読込済 '+draws.length+'件 (保存済)';
    }catch(e){
      showCSVStatus('読込エラー: '+e.message,'err');
      console.error(e);
    }
  };
  r.readAsArrayBuffer(file);
}
function showCSVStatus(msg,type){
  const b=document.getElementById('csvStatusBadge');
  if(!b)return;
  b.textContent=msg;
  b.className='csv-status '+type;
  b.style.display='inline-block';
  setTimeout(function(){b.style.display='none';},5000);
}
function reflectToTextarea(){
  const draws=DB[CL]._customDraws||DB[CL].draws;
  const ta=document.getElementById('dataInput');
  if(!ta||!draws)return;
  const text=draws.slice(0,Math.min(draws.length,100)).map(function(d){
    let s=d.r+' '+d.m.map(p2).join(' ');
    if(d.b&&d.b.length)s+=' / '+d.b.map(p2).join(' ');
    return s;
  }).join('\n');
  ta.value=text;
}
function updateSliderMax(){
  const total=(DB[CL]._customDraws||DB[CL].draws||[]).length;
  const sl=document.getElementById('rangeSlider');
  if(!sl)return;
  sl.max=Math.max(5,total);
  if(displayN===-1||displayN>total||displayN<1)displayN=total;
  sl.value=displayN;
  document.getElementById('rangeDisp').textContent=displayN+'回';
  const meta=document.getElementById('rangeMeta');
  if(meta)meta.textContent='直近'+displayN+'回 / 全'+total+'件';
  updateRangePresets();
}
function onRangeChange(v){
  displayN=parseInt(v,10);
  document.getElementById('rangeDisp').textContent=displayN+'回';
  updateRangePresets();
  applyRangeAndAnalyze();
}
function setRange(n){
  const total=(DB[CL]._customDraws||DB[CL].draws||[]).length;
  if(n===-1)displayN=total;
  else displayN=Math.min(n,total);
  const sl=document.getElementById('rangeSlider');
  if(sl)sl.value=displayN;
  document.getElementById('rangeDisp').textContent=displayN+'回';
  updateRangePresets();
  applyRangeAndAnalyze();
}
function updateRangePresets(){
  document.querySelectorAll('.rpbtn').forEach(function(b){
    const p=b.dataset.preset;
    let isOn=false;
    if(p==='all'){
      const total=(DB[CL]._customDraws||DB[CL].draws||[]).length;
      isOn=(displayN===total);
    }else{
      isOn=(displayN===parseInt(p,10));
    }
    b.classList.toggle('on',isOn);
  });
}
function applyRangeAndAnalyze(){
  const src=DB[CL]._customDraws||DB[CL].draws||[];
  DB[CL]._viewDraws=src.slice(0,displayN);
  processAll();
}

// ============================================================
// VIEWPORT
// ============================================================
function applyTransform(){
  STAGE().style.transform='translate('+tx+'px,'+ty+'px) scale('+vpScale+')';
}
function clampPan(){
  const vp=VP();
  const sw=Math.max(1,canvasW*vpScale), sh=Math.max(1,canvasH*vpScale);
  const vw=vp.clientWidth, vh=vp.clientHeight;
  const minX=Math.min(0,vw-sw), maxX=Math.max(0,vw-sw);
  const minY=Math.min(0,vh-sh), maxY=Math.max(0,vh-sh);
  if(sw<=vw)tx=(vw-sw)/2;
  else tx=Math.min(maxX,Math.max(minX,tx));
  if(sh<=vh)ty=(vh-sh)/2;
  else ty=Math.min(maxY,Math.max(minY,ty));
}
// ズーム/パン用のRAFスロットラ (グローバル)
let _vpRafPending=false;
function scheduleApplyGlobal(){
  if(_vpRafPending)return;
  _vpRafPending=true;
  requestAnimationFrame(function(){
    _vpRafPending=false;
    clampPan();
    applyTransform();
  });
}
function zoomAt(cx,cy,factor){
  const oldS=vpScale;
  let ns=Math.max(SCALE_MIN,Math.min(SCALE_MAX,vpScale*factor));
  // メモリ保護: 表示後Canvas面積が画面の◯倍を超えないよう上限制御
  // (拡大しすぎるとGPUテクスチャ枯渇でアプリクラッシュ)
  const vp=VP();
  const vw=vp.clientWidth, vh=vp.clientHeight;
  const screenArea=vw*vh;
  const projArea=(canvasW*ns)*(canvasH*ns);
  const maxAreaRatio = IS_MOBILE ? 30 : 80; // スマホは画面の30倍まで (1枚~50MB相当)
  if(projArea>screenArea*maxAreaRatio){
    const safeNs=Math.sqrt(screenArea*maxAreaRatio/(canvasW*canvasH));
    if(safeNs<ns){
      ns=Math.max(SCALE_MIN,safeNs);
    }
  }
  const ratio=ns/oldS;
  tx=cx-(cx-tx)*ratio;
  ty=cy-(cy-ty)*ratio;
  vpScale=ns;
  scheduleApplyGlobal();
  document.getElementById('szdisp').textContent=Math.round(vpScale*100)+'%';
}
function resetView(){
  vpScale=1.0; tx=0; ty=0;
  const vp=VP();
  if(canvasW<vp.clientWidth)tx=(vp.clientWidth-canvasW)/2;
  applyTransform();
  document.getElementById('szdisp').textContent='100%';
}
function fitChartWidth(){
  const vp=VP();
  if(!canvasW)return;
  const ns=vp.clientWidth/canvasW;
  vpScale=Math.max(SCALE_MIN,Math.min(SCALE_MAX,ns));
  tx=0; ty=0;
  clampPan();
  applyTransform();
  document.getElementById('szdisp').textContent=Math.round(vpScale*100)+'%';
}
function toggleVPFullscreen(){
  const vp=VP();
  const wasFs=vp.classList.contains('vp-fs');
  vp.classList.toggle('vp-fs');
  const btn=document.getElementById('vpFsBtn');
  if(vp.classList.contains('vp-fs')){
    btn.textContent='⛶ 閉じる';
    document.body.style.overflow='hidden';
    // スマホかつ大きすぎるズーム率は最大化時にリセット (メモリ保護)
    if(IS_MOBILE && vpScale>1.3){
      vpScale=1.0;tx=0;ty=0;
      document.getElementById('szdisp').textContent='100%';
    }
  }else{
    btn.textContent='⛶ 最大化';
    document.body.style.overflow='';
    // 最大化解除時もズーム率を控えめに (GPUメモリ解放)
    if(IS_MOBILE && vpScale>1.5){
      vpScale=1.0;tx=0;ty=0;
      document.getElementById('szdisp').textContent='100%';
    }
  }
  setTimeout(function(){clampPan();applyTransform();},120);
}
function initSz(){
  const wrap=document.getElementById('szbtnWrap');
  if(!wrap)return;
  wrap.innerHTML='';
  [['80%',0.8],['100%',1.0],['125%',1.25],['150%',1.5],['200%',2.0]].forEach(function(p){
    const b=document.createElement('div');
    b.className='szbtn';
    b.textContent=p[0];
    b.onclick=function(){
      const vp=VP();
      vpScale=p[1];
      tx=0;ty=0;
      if(canvasW*vpScale<vp.clientWidth)tx=(vp.clientWidth-canvasW*vpScale)/2;
      applyTransform();
      document.getElementById('szdisp').textContent=Math.round(vpScale*100)+'%';
    };
    wrap.appendChild(b);
  });
}

// ============================================================
// VPハンドラ
// ============================================================
function attachVPHandlers(){
  const vp=VP();
  if(vp._handlersAttached)return;
  vp._handlersAttached=true;
  let dragging=false, lastX=0, lastY=0;
  let rafPending=false;
  function scheduleApply(){
    if(rafPending)return;
    rafPending=true;
    requestAnimationFrame(function(){
      rafPending=false;
      clampPan();
      applyTransform();
    });
  }
  vp.addEventListener('wheel',function(e){
    e.preventDefault();
    const r=vp.getBoundingClientRect();
    const cx=e.clientX-r.left, cy=e.clientY-r.top;
    const f=e.deltaY<0?1.12:1/1.12;
    zoomAt(cx,cy,f);
  },{passive:false});
  vp.addEventListener('mousedown',function(e){
    if(mode==='arrow'||mode==='erase'||mode==='pline'){
      handleGridDown(e);
      return;
    }
    if(plDrag){return;}
    dragging=true;
    lastX=e.clientX;lastY=e.clientY;
    vp.style.cursor='grabbing';
  });
  window.addEventListener('mousemove',function(e){
    if(plDrag){
      e.preventDefault();
      bindPLineDrag(e);
      return;
    }
    if(!dragging)return;
    const dx=e.clientX-lastX, dy=e.clientY-lastY;
    lastX=e.clientX;lastY=e.clientY;
    tx+=dx;ty+=dy;
    scheduleApply();
  });
  window.addEventListener('mouseup',function(){
    if(plDrag){plDrag=null;return;}
    dragging=false;
    vp.style.cursor='';
  });
  attachTouchHandlers(vp,scheduleApply);
}
function attachTouchHandlers(vp,scheduleApply){
  // ピンチ用: 開始時の原点(canvas座標)を記録、移動中は数式で正しく再計算
  let pinch=null, panT=null;
  vp.addEventListener('touchstart',function(e){
    if(e.touches.length===2){
      const r=vp.getBoundingClientRect();
      const a=e.touches[0], b=e.touches[1];
      const cx=(a.clientX+b.clientX)/2-r.left;
      const cy=(a.clientY+b.clientY)/2-r.top;
      pinch={
        d:Math.hypot(a.clientX-b.clientX,a.clientY-b.clientY),
        cx:cx, cy:cy,
        s:vpScale,
        // ピンチ中心のcanvas内座標 (これを固定点として保つ)
        canvasX:(cx-tx)/vpScale,
        canvasY:(cy-ty)/vpScale
      };
      panT=null;
    }else if(e.touches.length===1){
      if(mode!=='view'){return;}
      panT={x:e.touches[0].clientX,y:e.touches[0].clientY};
    }
  },{passive:true});
  vp.addEventListener('touchmove',function(e){
    if(pinch&&e.touches.length===2){
      e.preventDefault();
      const a=e.touches[0], b=e.touches[1];
      const r=vp.getBoundingClientRect();
      const d=Math.hypot(a.clientX-b.clientX,a.clientY-b.clientY);
      const cx=(a.clientX+b.clientX)/2-r.left;
      const cy=(a.clientY+b.clientY)/2-r.top;
      const newScale=Math.max(SCALE_MIN,Math.min(SCALE_MAX,pinch.s*(d/pinch.d)));
      vpScale=newScale;
      // ピンチ中心のcanvas座標(canvasX,canvasY)が現在のスクリーン位置(cx,cy)に来るようtxを逆算
      tx=cx-pinch.canvasX*vpScale;
      ty=cy-pinch.canvasY*vpScale;
      scheduleApply();
      document.getElementById('szdisp').textContent=Math.round(vpScale*100)+'%';
    }else if(panT&&e.touches.length===1){
      e.preventDefault();
      const t=e.touches[0];
      tx+=t.clientX-panT.x;
      ty+=t.clientY-panT.y;
      panT.x=t.clientX;panT.y=t.clientY;
      scheduleApply();
    }
  },{passive:false});
  vp.addEventListener('touchend',function(e){
    if(e.touches.length<2)pinch=null;
    if(e.touches.length===0)panT=null;
  });
}
function vpToCanvas(clientX,clientY){
  const vp=VP();
  const r=vp.getBoundingClientRect();
  return{x:(clientX-r.left-tx)/vpScale, y:(clientY-r.top-ty)/vpScale};
}
function getCell(canvasX,canvasY){
  if(canvasX<LM2)return null;
  const cidx=Math.floor((canvasX-LM2)/CW2)+1;
  const c=cfg();
  if(cidx<1||cidx>c.max)return null;
  let rowsLen=0;
  try{rowsLen=dispRows().length;}catch(e){rowsLen=lastData.length;}
  if(canvasY<TM2)return null;
  const rAll=Math.floor((canvasY-TM2)/CH2);
  const tot=rowsLen+PZONE_ROWS;
  if(rAll<0||rAll>=tot)return null;
  const z=absRowToZone(rAll,rowsLen);
  if(!z)return null;
  if(z.zone==='pzone'){
    return{x:LM2+cidx*CW2, y:pzoneRowCenterY(z.zi), c:cidx, isPzone:true, pzi:z.zi, r:null};
  }else{
    const dr=dispRows();
    const rowR = dr[z.ri]?dr[z.ri].r:null;
    return{x:LM2+cidx*CW2, y:dataRowCenterY(z.ri), c:cidx, isPzone:false, ri:z.ri, r:rowR};
  }
}
function getCellFromPt(pt){return getCell(pt.x,pt.y);}

function handleGridDown(e){
  const pt=vpToCanvas(e.clientX,e.clientY);
  if(mode==='pline'){
    e.preventDefault();
    handlePLineClick(pt);
    return;
  }
  const hit=hitTestPLineHandle(pt);
  if(hit){
    plDrag=hit;
    e.preventDefault();
    return;
  }
  const cl=getCellFromPt(pt);
  if(!cl)return;
  if(mode==='arrow'){
    e.preventDefault();
    drawing=true;dS=cl;dE=cl;
    renderArrows();
    document.addEventListener('mousemove',aMove);
    document.addEventListener('mouseup',aUp);
  }else if(mode==='erase'){
    e.preventDefault();
    eraseAt(cl);
  }
}
function aMove(e){
  if(!drawing)return;
  const pt=vpToCanvas(e.clientX,e.clientY);
  const cl=getCellFromPt(pt);
  if(cl){dE=cl;renderArrows();}
}
function aUp(){
  if(drawing&&dS&&dE)commitA();
  drawing=false;dS=null;dE=null;
  document.removeEventListener('mousemove',aMove);
  document.removeEventListener('mouseup',aUp);
}
</script>

<script>
// ============================================================
// PREDICTION LINE
// ============================================================
function getRowLabel(ri){
  if(ri==='pz')return '予測ゾーン';
  const dr=dispRows();
  return dr[ri]?'第'+dr[ri].r+'回':'?';
}
function rToRowIdx(r){
  if(r==null)return -1;
  const dr=dispRows();
  for(let i=0;i<dr.length;i++)if(dr[i].r===r)return i;
  return -1;
}
function handlePLineClick(pt){
  const cl=getCellFromPt(pt);
  if(!cl)return;
  if(!plPlacing){
    plPlacing={s:cl, e:null};
    showHUD('📐 開始点: '+(cl.isPzone?'予測ゾーン':getRowLabel(cl.ri))+' / 数字'+cl.c+'<br><b>もう一度クリックで終点</b>');
    drawPLPlacing();
  }else{
    plPlacing.e=cl;
    const id=plIdSeq++;
    pLines.push({
      id:id,
      sR: plPlacing.s.isPzone ? null : (plPlacing.s.r||null),
      sPzi: plPlacing.s.isPzone ? plPlacing.s.pzi : null,
      sc: plPlacing.s.c,
      eR: plPlacing.e.isPzone ? null : (plPlacing.e.r||null),
      ePzi: plPlacing.e.isPzone ? plPlacing.e.pzi : null,
      ec: plPlacing.e.c
    });
    plPlacing=null;
    hideHUD();
    setMode('view');
    renderPLines();
    updatePLMeta();
    toast('予測線を追加 (計'+pLines.length+'本)',1600);
  }
}
function plineEndpoints(pl){
  let sx,sy,ex,ey;
  sx = LM2 + pl.sc * CW2;
  if(pl.sR!=null){
    const ri=rToRowIdx(pl.sR);
    sy = (ri>=0)?dataRowCenterY(ri):TM2;
  }else{
    sy = pzoneRowCenterY(pl.sPzi||0);
  }
  ex = LM2 + pl.ec * CW2;
  if(pl.eR!=null){
    const ri=rToRowIdx(pl.eR);
    ey = (ri>=0)?dataRowCenterY(ri):TM2;
  }else{
    ey = pzoneRowCenterY(pl.ePzi||0);
  }
  return{sx:sx,sy:sy,ex:ex,ey:ey};
}
function hitTestPLineHandle(pt){
  for(let i=pLines.length-1;i>=0;i--){
    const pl=pLines[i];
    const ep=plineEndpoints(pl);
    if(Math.hypot(pt.x-ep.sx,pt.y-ep.sy)<=10) return{pl:pl,end:'s'};
    if(Math.hypot(pt.x-ep.ex,pt.y-ep.ey)<=10) return{pl:pl,end:'e'};
  }
  return null;
}
function bindPLineDrag(e){
  const pt=vpToCanvas(e.clientX,e.clientY);
  const cl=getCellFromPt(pt);
  if(!cl)return;
  const pl=plDrag.pl;
  if(plDrag.end==='s'){
    pl.sR = cl.isPzone ? null : cl.r;
    pl.sPzi = cl.isPzone ? cl.pzi : null;
    pl.sc = cl.c;
  }else{
    pl.eR = cl.isPzone ? null : cl.r;
    pl.ePzi = cl.isPzone ? cl.pzi : null;
    pl.ec = cl.c;
  }
  renderPLines();
}
function clearPLines(){
  if(!pLines.length){toast('予測線なし',1200);return;}
  pLines=[];renderPLines();updatePLMeta();
  toast('予測線を全消去',1500);
}
function updatePLMeta(){
  const m=document.getElementById('plineMeta');
  if(!m)return;
  m.textContent='線'+pLines.length+'本 / '+(mode==='pline'?'配置中':'OFF');
}
function showHUD(html){
  const h=document.getElementById('plineHUD');
  if(!h)return;
  h.innerHTML=html;h.classList.add('on');
}
function hideHUD(){
  const h=document.getElementById('plineHUD');
  if(h)h.classList.remove('on');
}
function drawPLPlacing(){
  if(!plPlacing||!plPlacing.s)return;
  const cv=PLC();
  const ctx=cv.getContext('2d');
  const ep={
    sx: LM2+plPlacing.s.c*CW2,
    sy: plPlacing.s.isPzone? pzoneRowCenterY(plPlacing.s.pzi):dataRowCenterY(plPlacing.s.ri)
  };
  ctx.save();
  ctx.beginPath();
  ctx.arc(ep.sx,ep.sy,8,0,Math.PI*2);
  ctx.fillStyle='rgba(168,85,247,.4)';
  ctx.fill();
  ctx.strokeStyle='#a855f7';ctx.lineWidth=2;ctx.stroke();
  ctx.restore();
}
function renderPLines(){
  const cv=PLC();
  if(canvasW<1||canvasH<1)return;
  if(cv.width!==canvasW||cv.height!==canvasH){cv.width=canvasW;cv.height=canvasH;}
  const ctx=cv.getContext('2d');
  ctx.clearRect(0,0,canvasW,canvasH);
  const extend=document.getElementById('plExtend')?document.getElementById('plExtend').checked:true;
  const plotLand=document.getElementById('plLandPlot')?document.getElementById('plLandPlot').checked:true;
  const crossHL=document.getElementById('plCrossHL')?document.getElementById('plCrossHL').checked:true;
  const c=cfg();
  let rowsLen=0;try{rowsLen=dispRows().length;}catch(e){rowsLen=lastData.length;}
  // Crossing column count
  const colCnt={};
  pLines.forEach(function(pl){
    const ep=plineEndpoints(pl);
    if(extend){
      // Project line into pzone area
      const dy=ep.ey-ep.sy;
      const dx=ep.ex-ep.sx;
      const targetY = isPzoneTop()? pzoneRowCenterY(0) : pzoneRowCenterY(0);
      if(Math.abs(dy)>0.1){
        const t=(targetY-ep.sy)/dy;
        const x=ep.sx+dx*t;
        const col=Math.round((x-LM2)/CW2);
        if(col>=1&&col<=c.max)colCnt[col]=(colCnt[col]||0)+1;
      }
    }
    const sCol=Math.round((ep.sx-LM2)/CW2);
    const eCol=Math.round((ep.ex-LM2)/CW2);
    if(sCol>=1&&sCol<=c.max)colCnt[sCol]=(colCnt[sCol]||0)+1;
    if(eCol>=1&&eCol<=c.max)colCnt[eCol]=(colCnt[eCol]||0)+1;
  });
  if(crossHL){
    Object.keys(colCnt).forEach(function(col){
      const cnt=colCnt[col];
      if(cnt>=2){
        const x=LM2+col*CW2;
        const top=pzoneTopY();
        ctx.save();
        ctx.fillStyle=cnt>=3?'rgba(239,68,68,.18)':'rgba(168,85,247,.13)';
        ctx.fillRect(x-CW2/2, top, CW2, CH2*PZONE_ROWS);
        ctx.restore();
      }
    });
  }
  pLines.forEach(function(pl){
    const ep=plineEndpoints(pl);
    ctx.save();
    ctx.strokeStyle='rgba(168,85,247,.85)';
    ctx.lineWidth=2;
    ctx.beginPath();
    ctx.moveTo(ep.sx,ep.sy);
    ctx.lineTo(ep.ex,ep.ey);
    ctx.stroke();
    if(extend){
      const dy=ep.ey-ep.sy, dx=ep.ex-ep.sx;
      const targetY=pzoneRowCenterY(0);
      if(Math.abs(dy)>0.1){
        const t=(targetY-ep.sy)/dy;
        if(t>1){
          const x=ep.sx+dx*t;
          ctx.setLineDash([4,4]);
          ctx.strokeStyle='rgba(168,85,247,.45)';
          ctx.beginPath();
          ctx.moveTo(ep.ex,ep.ey);
          ctx.lineTo(x,targetY);
          ctx.stroke();
          ctx.setLineDash([]);
          if(plotLand){
            const col=Math.round((x-LM2)/CW2);
            if(col>=1&&col<=c.max){
              ctx.fillStyle='rgba(240,171,252,.92)';
              ctx.font="bold 12px 'JetBrains Mono',monospace";
              ctx.textAlign='center';
              ctx.fillText(p2(col), LM2+col*CW2, targetY+4);
            }
          }
        }
      }
    }
    [['s',ep.sx,ep.sy],['e',ep.ex,ep.ey]].forEach(function(h){
      ctx.beginPath();
      ctx.arc(h[1],h[2],5,0,Math.PI*2);
      ctx.fillStyle='#a855f7';
      ctx.fill();
      ctx.strokeStyle='#fff';ctx.lineWidth=1.5;
      ctx.stroke();
    });
    ctx.restore();
  });
  if(plPlacing)drawPLPlacing();
}

// ============================================================
// ARROWS - 回号(r)で保存することで表示順切替に追従
// ============================================================
function commitA(){
  if(!dS||!dE)return;
  const ar=document.getElementById('aArch').value;
  arrows.push({
    sR: dS.isPzone? null : dS.r,
    sPzi: dS.isPzone? dS.pzi : null,
    sc: dS.c,
    eR: dE.isPzone? null : dE.r,
    ePzi: dE.isPzone? dE.pzi : null,
    ec: dE.c,
    col: aCol,
    w: parseFloat(document.getElementById('aWidth').value),
    st: document.getElementById('aStyle').value,
    ar: ar
  });
  renderArrows();
}
function eraseAt(cl){
  // 矢印を消去 (hit test by current screen position)
  const pt={x:cl.x,y:cl.y};
  for(let i=arrows.length-1;i>=0;i--){
    const a=arrows[i];
    const ep=arrowEndpoints(a);
    if(!ep)continue;
    const d=pointToSegmentDist(pt.x,pt.y,ep.sx,ep.sy,ep.ex,ep.ey);
    if(d<=10){
      arrows.splice(i,1);
      renderArrows();
      toast('矢印を1本消去',1200);
      return;
    }
  }
}
function arrowEndpoints(a){
  let sx=LM2+a.sc*CW2, ex=LM2+a.ec*CW2, sy, ey;
  if(a.sR!=null){
    const ri=rToRowIdx(a.sR);
    if(ri<0)return null;
    sy=dataRowCenterY(ri);
  }else{
    sy=pzoneRowCenterY(a.sPzi||0);
  }
  if(a.eR!=null){
    const ri=rToRowIdx(a.eR);
    if(ri<0)return null;
    ey=dataRowCenterY(ri);
  }else{
    ey=pzoneRowCenterY(a.ePzi||0);
  }
  return{sx:sx,sy:sy,ex:ex,ey:ey};
}
function pointToSegmentDist(px,py,x1,y1,x2,y2){
  const dx=x2-x1, dy=y2-y1;
  const len2=dx*dx+dy*dy;
  if(len2===0)return Math.hypot(px-x1,py-y1);
  let t=((px-x1)*dx+(py-y1)*dy)/len2;
  t=Math.max(0,Math.min(1,t));
  return Math.hypot(px-(x1+t*dx),py-(y1+t*dy));
}
function clearArrows(){
  if(!arrows.length){toast('矢印なし',1200);return;}
  arrows=[];renderArrows();
  toast('矢印を全消去',1500);
}
function renderArrows(){
  const cv=AC();
  if(canvasW<1||canvasH<1)return;
  if(cv.width!==canvasW||cv.height!==canvasH){cv.width=canvasW;cv.height=canvasH;}
  const ctx=cv.getContext('2d');
  ctx.clearRect(0,0,canvasW,canvasH);
  arrows.forEach(function(a){
    const ep=arrowEndpoints(a);
    if(!ep)return;
    drawArrowOn(ctx,ep.sx,ep.sy,ep.ex,ep.ey,a);
  });
  if(drawing&&dS&&dE){
    const sx=LM2+dS.c*CW2;
    const sy=dS.isPzone?pzoneRowCenterY(dS.pzi):dataRowCenterY(dS.ri);
    const ex=LM2+dE.c*CW2;
    const ey=dE.isPzone?pzoneRowCenterY(dE.pzi):dataRowCenterY(dE.ri);
    drawArrowOn(ctx,sx,sy,ex,ey,{col:aCol,w:parseFloat(document.getElementById('aWidth').value),st:document.getElementById('aStyle').value,ar:document.getElementById('aArch').value});
  }
}
function drawArrowOn(ctx,sx,sy,ex,ey,a){
  ctx.save();
  ctx.strokeStyle=a.col;
  ctx.lineWidth=a.w;
  ctx.fillStyle=a.col;
  if(a.st==='dashed')ctx.setLineDash([8,5]);
  else if(a.st==='dotted')ctx.setLineDash([2,4]);
  let cpx,cpy;
  const mx=(sx+ex)/2, my=(sy+ey)/2;
  const dx=ex-sx, dy=ey-sy;
  const dist=Math.hypot(dx,dy);
  const arch=a.ar||'auto';
  let off=Math.min(40,dist*0.2);
  if(arch==='straight'){
    ctx.beginPath();ctx.moveTo(sx,sy);ctx.lineTo(ex,ey);ctx.stroke();
  }else{
    let dir=0;
    if(arch==='right')dir=1;
    else if(arch==='left')dir=-1;
    else dir=(ex>sx?1:-1);
    cpx=mx-dy*off/dist*dir;
    cpy=my+dx*off/dist*dir;
    ctx.beginPath();ctx.moveTo(sx,sy);ctx.quadraticCurveTo(cpx,cpy,ex,ey);ctx.stroke();
  }
  ctx.setLineDash([]);
  // 矢じり
  let angle;
  if(arch==='straight'||!cpx){angle=Math.atan2(ey-sy,ex-sx);}
  else angle=Math.atan2(ey-cpy,ex-cpx);
  const ah=Math.max(8,a.w*3);
  ctx.beginPath();
  ctx.moveTo(ex,ey);
  ctx.lineTo(ex-ah*Math.cos(angle-Math.PI/6),ey-ah*Math.sin(angle-Math.PI/6));
  ctx.lineTo(ex-ah*Math.cos(angle+Math.PI/6),ey-ah*Math.sin(angle+Math.PI/6));
  ctx.closePath();
  ctx.fill();
  ctx.restore();
}

// Tooltip helpers
function showTip(x,y,html){
  const t=document.getElementById('ttp');
  t.innerHTML=html;
  t.style.display='block';
  t.style.left=Math.min(window.innerWidth-280,x+12)+'px';
  t.style.top=Math.min(window.innerHeight-100,y+12)+'px';
}
function hideTip(){
  const t=document.getElementById('ttp');
  t.style.display='none';
}
</script>

<script>
// ============================================================
// LOAD / PROCESS
// ============================================================
function loadBuiltin(){
  const c=cfg();
  DB[CL]._customDraws=null;
  DB[CL]._viewDraws=null;
  DB[CL]._fileName=null;
  clearCSVStorage();
  allDraws=c.draws.slice();
  displayN=Math.min(50,c.draws.length);
  updateSliderMax();
  reflectToTextarea();
  applyRangeAndAnalyze();
  toast('内蔵データロード ('+c.draws.length+'件)',1500);
  document.getElementById('csvMeta').textContent='内蔵 '+c.draws.length+'件';
}
// 起動時/CL切替時に保存CSVを自動復元
function tryRestoreCSV(){
  const payload=loadCSVFromStorage();
  if(!payload)return false;
  DB[CL]._customDraws=payload.draws;
  DB[CL]._viewDraws=null;
  DB[CL]._fileName=payload.fileName||'';
  allDraws=payload.draws.slice();
  displayN=Math.min(payload.draws.length,200);
  updateSliderMax();
  reflectToTextarea();
  applyRangeAndAnalyze();
  document.getElementById('csvMeta').textContent='復元 '+payload.draws.length+'件 ('+payload.fileName+')';
  toast('💾 前回のCSVを復元 ('+payload.draws.length+'件)',2000);
  return true;
}
function dispRows(){
  const src=DB[CL]._viewDraws||DB[CL]._customDraws||cfg().draws||[];
  const slc=src.slice(0,Math.min(displayN,src.length));
  return orderNew?slc.slice():slc.slice().reverse();
}
// 直近N回の出現回数キャッシュ (drawChart前にbuildHCRecent()で更新)
let _hcRecentCount=null;
let _hcWindow=10;     // 直近N回 (スライダー連動)
let _hcHotThr=4;      // HOTしきい値 (スライダー連動)
let _hcMasterOn=true; // HOT/COLD色付けマスタースイッチ
function buildHCRecent(){
  if(!_hcMasterOn){_hcRecentCount=null;return;}
  _hcRecentCount={};
  const recent=lastData.slice(0,_hcWindow);
  recent.forEach(function(m){m.forEach(function(n){_hcRecentCount[n]=(_hcRecentCount[n]||0)+1;});});
}
function toggleHCMaster(){
  _hcMasterOn=!_hcMasterOn;
  const b=document.getElementById('btnHCMaster');
  if(b){
    b.textContent='色付け:'+(_hcMasterOn?'ON':'OFF');
    b.classList.toggle('b-or',_hcMasterOn);
    b.classList.toggle('b-gy',!_hcMasterOn);
  }
  // スライダー類を視覚的に無効化
  ['hcWindowRange','hcHotRange'].forEach(function(id){
    const el=document.getElementById(id);
    if(el){el.disabled=!_hcMasterOn;el.style.opacity=_hcMasterOn?'1':'.4';}
  });
  try{drawChart();}catch(e){}
}
function onHCParamChange(){
  const w=document.getElementById('hcWindowRange');
  const t=document.getElementById('hcHotRange');
  if(w){_hcWindow=parseInt(w.value,10)||10;document.getElementById('hcWindowDisp').textContent=_hcWindow;}
  if(t){_hcHotThr=parseInt(t.value,10)||4;document.getElementById('hcHotDisp').textContent=_hcHotThr+'回↑';}
  // しきい値が窓サイズを超えないようクランプ表示
  const tEl=document.getElementById('hcHotRange');
  if(tEl){tEl.max=_hcWindow;if(_hcHotThr>_hcWindow){_hcHotThr=_hcWindow;tEl.value=_hcWindow;document.getElementById('hcHotDisp').textContent=_hcHotThr+'回↑';}}
  const s=document.getElementById('hcStatusLine');
  if(s)s.innerHTML='直近'+_hcWindow+'回で<b style="color:#ef4444;">'+_hcHotThr+'回以上</b>→🔥HOT  /  <b style="color:#60a5fa;">0回</b>→❄COLD';
  try{drawChart();}catch(e){}
}
function getDotStyle(num,row,allRows){
  const fixed=parseN('fixedNums');
  const black=parseN('blackList');
  const myp=parseN('myPicks');
  if(black.indexOf(num)>=0)return{fill:'#1e293b',stroke:'#475569',text:'#64748b',badge:'❌'};
  if(fixed.indexOf(num)>=0)return{fill:'#fbbf24',stroke:'#f59e0b',text:'#000',badge:'★'};
  if(myp.indexOf(num)>=0)return{fill:'#a855f7',stroke:'#c084fc',text:'#fff',badge:'♛'};
  // 直近N回HOT/COLD (マスター&ユーザー指定優先で上書きしない)
  if(_hcMasterOn && _hcRecentCount && lastData.length>=1){
    const cnt=_hcRecentCount[num]||0;
    if(cnt>=_hcHotThr)return{fill:'#ef4444',stroke:'#fca5a5',text:'#fff',badge:'🔥'};
    if(cnt===0)return{fill:'#2a2a33',stroke:'#5a5a66',text:'#7a7a86',badge:'❄'};
  }
  return null;
}
function processAll(){
  const src=DB[CL]._viewDraws||DB[CL]._customDraws||cfg().draws||[];
  lastData=src.slice(0,Math.min(displayN,src.length)).map(function(d){return d.m||[];});
  if(!lastData.length){toast('データなし',1500);return;}
  // 各処理を独立にtry-catch (一つ失敗しても他は実行する)
  try{ runAnalysis(); }catch(e){ console.error('runAnalysis:',e); }
  try{ drawChart(); }catch(e){ console.error('drawChart:',e); toast('チャート描画エラー: '+e.message,3000); }
  try{ predict.update(); }catch(e){ console.error('predict.update:',e); }
  try{ typePredict.update(); }catch(e){ console.error('typePredict.update:',e); }
  try{ biasAnalyzer.update(); }catch(e){ console.error('biasAnalyzer.update:',e); }
  try{ buildHC(); }catch(e){ console.error('buildHC:',e); }
  try{ updateMomList(); }catch(e){ console.error('updateMomList:',e); }
  try{ updateTactical(); }catch(e){ console.error('updateTactical:',e); }
  try{ updateAIPrompt(); }catch(e){ console.error('updateAIPrompt:',e); }
  try{ updateSummary(); }catch(e){ console.error('updateSummary:',e); }
  setTimeout(function(){try{clampPan();applyTransform();}catch(e){}},80);
}
function drawChart(){
  const c=cfg();
  const rows=dispRows();
  const N=rows.length;
  if(!N){return;}
  // セル高: 件数に応じて段階的に圧縮 (基本)
  // スマホ判定時はメモリ節約のため一段階圧縮
  if(IS_MOBILE){
    if(N<=80)CH2=40;
    else if(N<=150)CH2=30;
    else if(N<=300)CH2=22;
    else if(N<=600)CH2=16;
    else if(N<=1200)CH2=11;
    else CH2=8;
  }else{
    if(N<=100)CH2=44;
    else if(N<=200)CH2=36;
    else if(N<=400)CH2=28;
    else if(N<=800)CH2=22;
    else if(N<=1500)CH2=14;
    else CH2=10;
  }
  const totalRows=N+PZONE_ROWS;
  canvasW=LM2+(c.max+1)*CW2;
  let proposedH=TM2+totalRows*CH2;
  // iOS Safari の Canvas最大ピクセル高は約 16384px。
  // スマホ=8000 / PC=12000 に制限 (重ねCanvas4枚分のGPUメモリ確保のため)
  const MAX_CANVAS_H = IS_MOBILE ? 8000 : 12000;
  if(proposedH>MAX_CANVAS_H){
    CH2=Math.max(4,Math.floor((MAX_CANVAS_H-TM2)/totalRows));
    proposedH=TM2+totalRows*CH2;
  }
  canvasH=proposedH;
  const cv=GC();
  cv.width=canvasW;cv.height=canvasH;
  cv.style.width=canvasW+'px';
  cv.style.height=canvasH+'px';
  STAGE().style.width=canvasW+'px';
  STAGE().style.height=canvasH+'px';
  const ctx=cv.getContext('2d');
  ctx.fillStyle='#0a0a0e';
  ctx.fillRect(0,0,canvasW,canvasH);
  // ヘッダ - 数字
  ctx.font="bold 15px 'JetBrains Mono',monospace";
  ctx.textAlign='center';ctx.textBaseline='middle';
  for(let n=1;n<=c.max;n++){
    const x=LM2+n*CW2;
    ctx.fillStyle='#5a5a66';
    ctx.fillText(n,x,TM2/2);
  }
  // 予測ゾーン背景 (ゾーン別ラベルは drawPzonePicks 内で描画)
  const pzTop=pzoneTopY();
  ctx.fillStyle='rgba(168,85,247,.03)';
  ctx.fillRect(0,pzTop,canvasW,CH2*PZONE_ROWS);
  for(let zi=0;zi<PZONE_ROWS;zi++){
    const y=pzTop+zi*CH2;
    ctx.strokeStyle='rgba(168,85,247,.14)';
    ctx.lineWidth=1;
    ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(canvasW,y);ctx.stroke();
  }
  // データ行 + グリッド
  rows.forEach(function(d,ri){
    const y=dataRowTopY(ri);
    if(ri%2===0){ctx.fillStyle='rgba(255,255,255,.012)';ctx.fillRect(0,y,canvasW,CH2);}
    // 行下端の罫線
    ctx.strokeStyle='rgba(255,255,255,.04)';
    ctx.lineWidth=1;
    ctx.beginPath();ctx.moveTo(0,y+CH2);ctx.lineTo(canvasW,y+CH2);ctx.stroke();
    ctx.font="10px 'JetBrains Mono',monospace";
    ctx.fillStyle='#7a7a86';ctx.textAlign='left';
    ctx.fillText('第'+d.r+'回',4,y+CH2/2+3);
  });
  // 縦罫線 (各数字列)
  ctx.strokeStyle='rgba(255,255,255,.025)';
  ctx.lineWidth=1;
  for(let n=1;n<=c.max;n++){
    const x=LM2+n*CW2;
    ctx.beginPath();ctx.moveTo(x,TM2);ctx.lineTo(x,canvasH);ctx.stroke();
  }
  // 流線
  if(showLines){
    const lr=lineRange||3;
    rows.forEach(function(d,ri){
      if(ri===rows.length-1)return;
      const next=rows[ri+1];
      const cy=dataRowCenterY(ri);
      const ny=dataRowCenterY(ri+1);
      d.m.forEach(function(n){
        next.m.forEach(function(m){
          if(Math.abs(n-m)<=lr){
            ctx.strokeStyle='rgba(245,183,58,.45)';
            ctx.lineWidth=1.4;
            ctx.beginPath();
            ctx.moveTo(LM2+n*CW2,cy);
            ctx.lineTo(LM2+m*CW2,ny);
            ctx.stroke();
          }
        });
      });
      if(lineIncBonus&&d.b){
        d.b.forEach(function(n){
          next.m.forEach(function(m){
            if(Math.abs(n-m)<=lr){
              ctx.strokeStyle='rgba(245,158,11,.40)';
              ctx.lineWidth=1.2;
              ctx.setLineDash([3,3]);
              ctx.beginPath();
              ctx.moveTo(LM2+n*CW2,cy);
              ctx.lineTo(LM2+m*CW2,ny);
              ctx.stroke();
              ctx.setLineDash([]);
            }
          });
        });
      }
    });
  }
  // セル (本数字) - 長方形スタイル
  buildHCRecent();
  rows.forEach(function(d,ri){
    const y=dataRowCenterY(ri);
    d.m.forEach(function(n){
      const x=LM2+n*CW2;
      const style=getDotStyle(n,d,rows);
      const isBest=predict.bestPicks&&predict.bestPicks.indexOf(n)>=0;
      // 直近N回出現回数（モード判定用）
      const recentCnt=(_hcRecentCount&&_hcRecentCount[n])||0;
      const isHot=recentCnt>=_hcHotThr;
      const isCold=recentCnt===0;
      // 長方形セルサイズ: 列幅・行高を絶対に超えない (95%以下にクランプ)
      const cellW=Math.min(34, CW2*0.92);
      const cellH=Math.min(38, CH2*0.88);
      const fontSz=Math.max(10, Math.min(24, cellH*0.62));
      const rx=x-cellW/2, ry=y-cellH/2;
      // 塗り色決定
      let fillC,strokeC,textC,badge=null;
      if(style){
        fillC=style.fill;strokeC=style.stroke;textC=style.text;badge=style.badge||null;
      }else{
        // 通常本数字: 琥珀(マスタード)
        fillC=isBest?'#f5b73a':'#d99a2b';
        strokeC=isBest?'#fde68a':'#a16715';
        textC='#0a0a0e';
      }
      // === 3モードによる強調・減光 (マスターON時のみ) ===
      // HOT強調モード: ON時、HOT以外を薄暗くする
      // 復活コールドモード: ON時、COLDを強調枠付き、それ以外を薄暗く
      let dim=false, extraStroke=null;
      if(_hcMasterOn){
        if(hotMode && !isHot) dim=true;
        if(coldMode){
          if(isCold){
            extraStroke={color:'#60a5fa', width:2.5};
            dim=false;
          }else if(!hotMode || !isHot){
            dim=true;
          }
        }
      }
      // 影 (ほのか)
      ctx.fillStyle='rgba(0,0,0,.45)';
      ctx.fillRect(rx+1, ry+1, cellW, cellH);
      // 本体
      ctx.globalAlpha=dim?0.22:1;
      ctx.fillStyle=fillC;
      ctx.fillRect(rx, ry, cellW, cellH);
      // 縁
      ctx.strokeStyle=strokeC;
      ctx.lineWidth=isBest?1.6:1;
      ctx.strokeRect(rx+0.5, ry+0.5, cellW-1, cellH-1);
      ctx.globalAlpha=1;
      // 強調枠 (復活コールド時のCOLDセルなど)
      if(extraStroke){
        ctx.strokeStyle=extraStroke.color;
        ctx.lineWidth=extraStroke.width;
        ctx.strokeRect(rx-1.5, ry-1.5, cellW+3, cellH+3);
      }
      // 数字
      ctx.globalAlpha=dim?0.35:1;
      ctx.fillStyle=textC;
      ctx.font="700 "+fontSz+"px 'Inter','JetBrains Mono',monospace";
      ctx.textAlign='center';ctx.textBaseline='middle';
      ctx.fillText(p2(n),x,y+0.5);
      ctx.globalAlpha=1;
      // バッジ (HOT/COLD/固定/除外/MyPick) - セル右上に小角
      if(badge && !dim){
        const bgW=Math.min(13,cellW*0.5), bgH=Math.min(11,cellH*0.4);
        ctx.fillStyle=textC;
        ctx.fillRect(rx+cellW-bgW, ry, bgW, bgH);
        ctx.fillStyle=fillC;
        ctx.font="bold 7px sans-serif";
        ctx.textBaseline='middle';
        ctx.fillText(badge, rx+cellW-bgW/2, ry+bgH/2+0.5);
      }
      // 全バッジ表示モード: 通常本数字にも出現回数バッジ表示
      if(allMode && !badge && !dim && recentCnt>0){
        const bgW=Math.min(13,cellW*0.5), bgH=Math.min(11,cellH*0.4);
        ctx.fillStyle='rgba(0,0,0,.7)';
        ctx.fillRect(rx+cellW-bgW, ry, bgW, bgH);
        ctx.fillStyle='#fde68a';
        ctx.font="bold 8px 'JetBrains Mono',monospace";
        ctx.textBaseline='middle';
        ctx.textAlign='center';
        ctx.fillText(String(recentCnt), rx+cellW-bgW/2, ry+bgH/2+0.5);
      }
    });
    // ボーナス数字 - 細めの琥珀枠付き長方形
    if(d.b&&d.b.length){
      d.b.forEach(function(n){
        const x=LM2+n*CW2;
        const cellW=Math.min(30, CW2*0.82);
        const cellH=Math.min(32, CH2*0.74);
        const fontSz=Math.max(9, Math.min(20, cellH*0.6));
        const rx=x-cellW/2, ry=y-cellH/2;
        // ボーナスもHOT/COLD強調モードの対象 (マスターON時のみ)
        const recentCnt=(_hcRecentCount&&_hcRecentCount[n])||0;
        const isHot=recentCnt>=_hcHotThr;
        const isCold=recentCnt===0;
        let dim=false;
        if(_hcMasterOn){
          if(hotMode && !isHot) dim=true;
          if(coldMode && !isCold && (!hotMode || !isHot)) dim=true;
        }
        ctx.fillStyle='rgba(0,0,0,.45)';
        ctx.fillRect(rx+1, ry+1, cellW, cellH);
        ctx.globalAlpha=dim?0.22:1;
        ctx.fillStyle='#7a5210';
        ctx.fillRect(rx, ry, cellW, cellH);
        ctx.strokeStyle='#f5b73a';
        ctx.lineWidth=1;
        ctx.strokeRect(rx+0.5, ry+0.5, cellW-1, cellH-1);
        ctx.fillStyle='#fde68a';
        ctx.font="700 "+fontSz+"px 'Inter','JetBrains Mono',monospace";
        ctx.textAlign='center';ctx.textBaseline='middle';
        ctx.fillText(p2(n),x,y+0.5);
        ctx.globalAlpha=1;
      });
    }
  });
  // 予測ゾーン: ゾーン別3行 (1行目=低域TOP3 / 2行目=中域TOP3 / 3行目=高域TOP3)
  // (N-S物理モード ON時は派手な紫テーマ、OFFはbestPicksにフォールバック)
  (function drawPzonePicks(){
    const nsEl=document.getElementById('ppT_ns');
    const masterEl=document.getElementById('ppMaster');
    const nsActive=nsEl&&nsEl.checked&&masterEl&&masterEl.checked;
    if(!lastData.length)return;
    let totalScore={};
    let isNS=false;
    try{
      const P=navierStokes.calcPressure();
      const V=navierStokes.calcViscosity();
      const F=navierStokes.calcExternalForce();
      const W=navierStokes.calcVortex();
      for(let n=1;n<=c.max;n++){
        totalScore[n]=(P[n]||0)*0.35+(V[n]||0)*0.25+(F[n]||0)*0.20+(W[n]||0)*0.20;
      }
      isNS=true;
    }catch(e){
      totalScore={};
    }
    // フォールバック: N-S失敗時はbestPicksから疑似スコアを構築
    if(!isNS && predict.bestPicks && predict.bestPicks.length){
      predict.bestPicks.forEach(function(n,i){totalScore[n]=1-i*0.05;});
    }
    if(!Object.keys(totalScore).length)return;
    const zones=navierStokes.zones();
    // 各ゾーンTOP3を取得 (中央行=中域なので zones[1])
    const PER_ZONE=3;
    // 1行目=低域(zones[0]), 2行目=中域(zones[1]), 3行目=高域(zones[2])
    // 全体の最大値を取って強度正規化
    let globalMax=0;
    Object.keys(totalScore).forEach(function(k){if(totalScore[k]>globalMax)globalMax=totalScore[k];});
    if(globalMax<=0)globalMax=1;
    zones.forEach(function(zone, ziIdx){
      const y=pzoneRowCenterY(ziIdx);
      const arr=navierStokes.topInZone(totalScore, zone, PER_ZONE);
      // ゾーンバンド (背景色帯)
      ctx.save();
      const bandTop=pzoneRowTopY(ziIdx);
      ctx.fillStyle=zone.cssCol+'0.06)';
      ctx.fillRect(0, bandTop, canvasW, CH2);
      ctx.restore();
      // ゾーンラベル (左端)
      ctx.save();
      ctx.font="bold 9px 'Inter',sans-serif";
      ctx.fillStyle=zone.color;
      ctx.textAlign='left';
      ctx.textBaseline='middle';
      ctx.fillText(zone.label+' '+zone.from+'-'+zone.to, 4, y);
      ctx.restore();
      // ドット描画
      const baseR=Math.min(CW2*0.46, CH2*0.95);
      arr.forEach(function(p,idx){
        if(p.v<=0.001)return; // ゾーン内に有効な値がなければスキップ
        const x=LM2+p.n*CW2;
        const intensity=Math.max(0.6, p.v/globalMax);
        const finalR = baseR * (0.78 + intensity*0.22);
        // グロー (ゾーン色)
        if(nsActive){
          const g=ctx.createRadialGradient(x,y,0, x,y,finalR*1.7);
          g.addColorStop(0, zone.cssCol+'0.55)');
          g.addColorStop(0.5, zone.cssCol+'0.20)');
          g.addColorStop(1, zone.cssCol+'0)');
          ctx.fillStyle=g;
          ctx.beginPath();ctx.arc(x,y,finalR*1.7,0,Math.PI*2);ctx.fill();
        }
        // 本体ドット
        ctx.beginPath();
        ctx.arc(x,y,finalR,0,Math.PI*2);
        if(nsActive){
          const grad=ctx.createRadialGradient(x,y-finalR*0.3,1, x,y,finalR);
          // ゾーン色にグラデ
          grad.addColorStop(0, '#ffffff');
          grad.addColorStop(0.4, zone.color);
          grad.addColorStop(1, zone.cssCol+'0.85)');
          ctx.fillStyle=grad;
        }else{
          ctx.fillStyle=zone.cssCol+'0.45)';
        }
        ctx.fill();
        // 縁取り (#1=金、他=ゾーン色)
        ctx.strokeStyle = (idx===0 && nsActive) ? '#fde047' : zone.color;
        ctx.lineWidth = (idx===0 && nsActive) ? 2.8 : 1.5;
        if(!nsActive){ctx.setLineDash([3,2]);}
        ctx.stroke();
        ctx.setLineDash([]);
        // 数字
        const fontSize = Math.max(13, finalR*0.85);
        ctx.font="bold "+fontSize.toFixed(0)+"px 'JetBrains Mono',monospace";
        ctx.fillStyle='#fff';
        ctx.shadowColor='rgba(0,0,0,.6)';
        ctx.shadowBlur=3;
        ctx.textAlign='center';
        ctx.textBaseline='middle';
        ctx.fillText(p2(p.n), x, y+1);
        ctx.shadowBlur=0;
        // 強度% (ドット下、小さく)
        if(nsActive){
          ctx.font="bold 8px 'JetBrains Mono',monospace";
          ctx.fillStyle='rgba(255,255,255,.85)';
          ctx.fillText((p.v*100).toFixed(0)+'%', x, y + finalR + 7);
        }
      });
    });
  })();
  renderArrows();
  renderPLines();
  if(predict.lastEV)predict.drawOverlay(predict.lastEV);
}

// ============================================================
// 解析
// ============================================================
function runAnalysis(){
  const c=cfg();
  const draws=lastData.map(function(m){return{m:m};});
  if(!draws.length){analysis=null;return;}
  const freq={};for(let n=1;n<=c.max;n++)freq[n]=0;
  draws.forEach(function(d){d.m.forEach(function(n){freq[n]++;});});
  const sums=draws.map(function(d){return d.m.reduce(function(a,b){return a+b;},0);});
  const sumAvg=sums.reduce(function(a,b){return a+b;},0)/sums.length;
  // ホット/コールド
  const fArr=Object.keys(freq).map(function(k){return{n:+k,c:freq[k]};});
  fArr.sort(function(a,b){return b.c-a.c;});
  const hot=fArr.slice(0,Math.min(10,fArr.length));
  const cold=fArr.slice().sort(function(a,b){return a.c-b.c;}).slice(0,Math.min(10,fArr.length));
  // ボーナス情報読み込み (B1修正)
  const bonusFixedArr=parseN('bonusFixed');
  const bonusBlackArr=parseN('bonusBlack');
  analysis={
    freq:freq,sumAvg:sumAvg,hot:hot,cold:cold,N:draws.length,
    bonus:{fixed:bonusFixedArr,black:bonusBlackArr}
  };
  scored=fArr;
}
function analyzeSum(){
  if(!lastData.length)return null;
  const sums=lastData.map(function(m){return m.reduce(function(a,b){return a+b;},0);});
  const avg=sums.reduce(function(a,b){return a+b;},0)/sums.length;
  const sd=Math.sqrt(sums.reduce(function(s,v){return s+(v-avg)*(v-avg);},0)/sums.length);
  return{avg:avg,sd:sd,min:Math.min.apply(null,sums),max:Math.max.apply(null,sums)};
}
function analyzeOE(){
  if(!lastData.length)return null;
  let odd=0,total=0;
  lastData.forEach(function(m){m.forEach(function(n){if(n%2===1)odd++;total++;});});
  return{odd:odd,total:total,oddRatio:odd/total};
}

// ============================================================
// 5点生成
// ============================================================
function calcDeltaScore(){
  const c=cfg();
  const out={};
  for(let n=1;n<=c.max;n++)out[n]=0;
  if(lastData.length<2)return out;
  for(let i=0;i<lastData.length-1;i++){
    const a=lastData[i],b=lastData[i+1];
    a.forEach(function(n1){b.forEach(function(n2){
      const d=Math.abs(n1-n2);
      if(d>=1&&d<=5){out[n1]+=(6-d);}
    });});
  }
  return out;
}
function wSample(weights,k){
  const c=cfg();
  const arr=[];
  for(let n=1;n<=c.max;n++)arr.push({n:n,w:Math.max(0.001,weights[n]||0.001)});
  const out=[];
  while(out.length<k&&arr.length){
    const tot=arr.reduce(function(s,x){return s+x.w;},0);
    let r=Math.random()*tot;
    let pick=0;
    for(let i=0;i<arr.length;i++){r-=arr[i].w;if(r<=0){pick=i;break;}}
    out.push(arr[pick].n);
    arr.splice(pick,1);
  }
  return out.sort(function(a,b){return a-b;});
}
function buildReasonsForNum(n,kind){
  const reasons=[];
  if(!analysis)return reasons;
  const fixed=parseN('fixedNums'),myp=parseN('myPicks');
  if(fixed.indexOf(n)>=0)reasons.push({tag:'軸',cls:'t-fix'});
  if(myp.indexOf(n)>=0)reasons.push({tag:'マイP',cls:'t-myp'});
  // N-S kindの場合は物理4力を主軸に表示
  if(kind==='ns'&&navierStokes.lastForces&&navierStokes.lastForces[n]){
    const b=navierStokes.lastForces[n];
    if(b.p>=0.6)reasons.push({tag:'圧'+(b.p*100).toFixed(0),cls:'t-press'});
    else if(b.p>=0.3)reasons.push({tag:'圧',cls:'t-press'});
    if(b.v>=0.5)reasons.push({tag:'粘'+(b.v*100).toFixed(0),cls:'t-visc'});
    else if(b.v>=0.25)reasons.push({tag:'粘',cls:'t-visc'});
    if(b.f>=0.5)reasons.push({tag:'外'+(b.f*100).toFixed(0),cls:'t-force'});
    else if(b.f>=0.2)reasons.push({tag:'外',cls:'t-force'});
    if(b.w>=0.5)reasons.push({tag:'渦'+(b.w*100).toFixed(0),cls:'t-vortex'});
    else if(b.w>=0.25)reasons.push({tag:'渦',cls:'t-vortex'});
    // 主たる力の判定
    const dom=navierStokes.dominantForce(n);
    if(dom)reasons.push({tag:'主:'+dom.label,cls:'t-wake'});
  }
  if(predict.lastEV){
    const ev=predict.lastEV[n]||0;
    if(ev>=0.7)reasons.push({tag:'EV高',cls:'t-river'});
    else if(ev>=0.5)reasons.push({tag:'EV中',cls:'t-river'});
  }
  const top=Object.keys(analysis.freq).sort(function(a,b){return analysis.freq[b]-analysis.freq[a];}).slice(0,7).map(Number);
  if(top.indexOf(n)>=0)reasons.push({tag:'頻',cls:'t-freq'});
  if(biasAnalyzer.last){
    const bs=biasAnalyzer.biasScoreFor(n);
    if(bs>=0.5)reasons.push({tag:'多発',cls:'t-bias'});
    else if(bs<=-0.5)reasons.push({tag:'反発',cls:'t-bias'});
  }
  if(predict.bestPicks&&predict.bestPicks.indexOf(n)>=0)reasons.push({tag:'★',cls:'t-river'});
  return reasons;
}
function buildContext(line,kind){
  return{
    sum: line.reduce(function(a,b){return a+b;},0),
    odd: line.filter(function(n){return n%2===1;}).length,
    even: line.filter(function(n){return n%2===0;}).length
  };
}
function classifyNumBadge(kind){
  if(kind==='fix')return'why-fix';
  if(kind==='myp')return'why-myp';
  if(kind==='river')return'why-river';
  if(kind==='delta')return'why-delta';
  return'';
}
function renderWhyBlock(line,kind){
  let html='<div class="why-block">';
  // N-S用: 系全体のエネルギーバランス情報を先頭に
  if(kind==='ns'&&navierStokes.lastMeta){
    const m=navierStokes.lastMeta;
    const tempState=m.tempDiff>10?'🔥オーバーヒート':(m.tempDiff<-10?'❄️過冷却':'⚖均衡');
    const tempArrow=m.tempDiff>10?'低番台へ流れる':(m.tempDiff<-10?'高番台へ流れる':'方向中立');
    html+='<div style="background:rgba(168,85,247,.1);border:1px solid var(--PRP);border-radius:6px;padding:6px 9px;margin-bottom:6px;font-size:.92em;line-height:1.7;color:#e9d5ff;">';
    html+='<b style="color:var(--PRP);">⚛ 系のエネルギーバランス</b><br>';
    html+='前回合計値 <b>'+m.lastSum+'</b> / 適温 <b>'+m.optimalTemp+'</b> / 差 <b>'+(m.tempDiff>=0?'+':'')+m.tempDiff+'</b> → '+tempState+' → '+tempArrow+'<br>';
    if(m.maxLast)html+='巨大岩(前回最大数字): <b>'+m.maxLast+'</b> / ウェイク半径: <b>±'+m.wakeRadius+'</b>';
    html+='</div>';
  }
  line.forEach(function(n){
    const reasons=buildReasonsForNum(n,kind);
    if(!reasons.length)return;
    const fixed=parseN('fixedNums'),myp=parseN('myPicks');
    let cls='';
    if(fixed.indexOf(n)>=0)cls='why-fix';
    else if(myp.indexOf(n)>=0)cls='why-myp';
    else if(kind==='ns')cls='why-ns';
    else if(kind==='river')cls='why-river';
    else if(kind==='delta')cls='why-delta';
    html+='<div class="why-row"><span class="why-num '+cls+'">'+p2(n)+'</span><div class="why-tags">';
    reasons.forEach(function(r){html+='<span class="why-tag '+r.cls+'">'+r.tag+'</span>';});
    html+='</div></div>';
  });
  html+='</div>';
  return html;
}
function pickFiveLines(scoreFn,boxClass,binfoClass,labelPrefix){
  const c=cfg();
  const lines=[];
  for(let i=0;i<5;i++){
    let attempts=0;
    let line=null;
    while(attempts<30){
      attempts++;
      const sc=scoreFn();
      const fixed=parseN('fixedNums').filter(function(n){return n>=1&&n<=c.max;});
      const black=parseN('blackList');
      black.forEach(function(n){sc[n]=0;});
      // 軸を必ず含める
      const need=c.mc-fixed.length;
      if(need<=0){line=fixed.slice(0,c.mc).sort(function(a,b){return a-b;});break;}
      const fixedSet={};fixed.forEach(function(n){fixedSet[n]=true;});
      const tmpW={};Object.keys(sc).forEach(function(k){if(!fixedSet[+k])tmpW[+k]=sc[k];});
      const picked=wSample(tmpW,need);
      const cand=fixed.concat(picked).sort(function(a,b){return a-b;});
      if(typePredict.filterByPrediction(cand)){line=cand;break;}
      if(attempts>=15){line=cand;break;}
    }
    if(line)lines.push(line);
  }
  return lines;
}
function generateGodFive(){
  if(!lastData.length){toast('データなし',1500);return;}
  const lines=pickFiveLines(function(){
    const sc={};
    const c=cfg();
    if(predict.lastEV){
      for(let n=1;n<=c.max;n++)sc[n]=(predict.lastEV[n]||0)*5+0.1;
    }else{
      for(let n=1;n<=c.max;n++)sc[n]=(analysis.freq[n]||0)+0.1;
    }
    if(biasAnalyzer.last){
      for(let n=1;n<=c.max;n++)sc[n]=Math.max(0.05,sc[n]+biasAnalyzer.biasScoreFor(n));
    }
    return sc;
  },'pbox','binfo','✨ 神');
  generateAndShowPatterns(lines,'pbox','binfo','✨ 神','river');
}
function generateDeltaFive(){
  if(!lastData.length){toast('データなし',1500);return;}
  const lines=pickFiveLines(function(){
    const ds=calcDeltaScore();
    const c=cfg();
    const sc={};
    for(let n=1;n<=c.max;n++)sc[n]=(ds[n]||0)+0.1;
    if(predict.lastEV){
      for(let n=1;n<=c.max;n++)sc[n]+=(predict.lastEV[n]||0)*2;
    }
    return sc;
  },'pbox-delta','binfo-delta','🔺 デルタ','delta');
  generateAndShowPatterns(lines,'pbox-delta','binfo-delta','🔺 デルタ','delta');
}
function generateRiverFive(){
  if(!lastData.length){toast('データなし',1500);return;}
  const lines=pickFiveLines(function(){
    const sc={};
    const c=cfg();
    const mom=predict.calcMomentum(lastData.map(function(m){return{m:m};}));
    for(let n=1;n<=c.max;n++)sc[n]=(mom[n]||0)+0.1;
    if(predict.lastEV){
      for(let n=1;n<=c.max;n++)sc[n]+=(predict.lastEV[n]||0)*3;
    }
    return sc;
  },'pbox-river','binfo-river','🌊 リバー','river');
  generateAndShowPatterns(lines,'pbox-river','binfo-river','🌊 リバー','river');
}

// ============================================================
// NAVIER-STOKES PHYSICS ENGINE (ロト7/6/ミニロト 共通)
// 数字を流体粒子として捉え、3つの力(圧力勾配/粘性/外力)+渦効果で予測
// ============================================================
const navierStokes = {
  lastForces: null,
  lastMeta: null,
  // 適温 = (max+1)/2 × mc (ロト7=133 / ロト6=132 / ミニロト=80)
  optimalTemp: function(){
    const c=cfg();
    return (c.max+1)/2 * c.mc;
  },
  wakeRadius: function(){
    const c=cfg();
    // 球数に応じてウェイク半径をスケール (ロト7,6=±3 / ミニロト=±2)
    return c.max>=37 ? 3 : 2;
  },
  // ゾーン定義: 低域/中域/高域 (max÷3)
  zones: function(){
    const c=cfg();
    const z=Math.floor(c.max/3);
    return [
      {key:'low',  label:'低域', from:1,     to:z,       color:'#22d3ee', cssCol:'rgba(34,211,238,'},
      {key:'mid',  label:'中域', from:z+1,   to:z*2,     color:'#a78bfa', cssCol:'rgba(167,139,250,'},
      {key:'high', label:'高域', from:z*2+1, to:c.max,   color:'#f472b6', cssCol:'rgba(244,114,182,'}
    ];
  },
  // ゾーン別TOPNを取得 (scoreMap, n) → [{n,v},...]
  topInZone: function(scoreMap, zone, n){
    const arr=[];
    for(let num=zone.from;num<=zone.to;num++){
      arr.push({n:num,v:scoreMap[num]||0});
    }
    arr.sort(function(a,b){return b.v-a.v;});
    return arr.slice(0,n);
  },
  // 1) 圧力勾配 -∇p : 未出現期間(休眠)が長いほど噴出力が大きい
  calcPressure: function(){
    const c=cfg();
    const lastSeen={};
    for(let n=1;n<=c.max;n++)lastSeen[n]=lastData.length;
    for(let i=0;i<lastData.length;i++){
      lastData[i].forEach(function(n){
        if(lastSeen[n]===lastData.length)lastSeen[n]=i;
      });
    }
    const out={};
    const mx=Math.max.apply(null,Object.values(lastSeen).concat([1]));
    for(let n=1;n<=c.max;n++){
      // 0~1正規化、休眠長いほど高い
      out[n]=lastSeen[n]/mx;
    }
    return out;
  },
  // 2) 粘性力 μ∇²v : 引っ張り傾向 + 空白地帯への流入
  calcViscosity: function(){
    const c=cfg();
    const lookback=Math.min(100,lastData.length);
    // 引っ張り回数: i→i+1 で連続で出た回数
    const carryCnt={};
    for(let n=1;n<=c.max;n++)carryCnt[n]=0;
    for(let i=0;i<lookback-1;i++){
      lastData[i].forEach(function(n){
        if(lastData[i+1].indexOf(n)>=0)carryCnt[n]++;
      });
    }
    // 直近100回での引っ張り率 × 数字の大きさ
    const out={};
    const maxCnt=Math.max.apply(null,Object.values(carryCnt).concat([1]));
    for(let n=1;n<=c.max;n++){
      const carryRate=carryCnt[n]/(maxCnt||1);
      // 数字の大きさで重み (大きい数字ほど粘性が高い、流れにくい)
      const sizeWeight=n/c.max;
      out[n]=carryRate*0.7+sizeWeight*0.3;
    }
    // 直近で出ていない数字は粘性ゼロ扱い（停滞しようがない）
    const recentSet={};
    if(lastData.length>0)lastData[0].forEach(function(n){recentSet[n]=true;});
    for(let n=1;n<=c.max;n++){
      if(!recentSet[n])out[n]*=0.3; // 出てない数字は粘性70%減
    }
    // 連続の式: 前回の出目間の最大空白地帯
    const wakeIn={};
    for(let n=1;n<=c.max;n++)wakeIn[n]=0;
    if(lastData.length>0){
      const last=lastData[0].slice().sort(function(a,b){return a-b;});
      let maxGap=0, gapStart=0, gapEnd=0;
      // 1〜last[0] の隙間
      if(last[0]-1>maxGap){maxGap=last[0]-1;gapStart=1;gapEnd=last[0]-1;}
      for(let i=0;i<last.length-1;i++){
        const g=last[i+1]-last[i]-1;
        if(g>maxGap){maxGap=g;gapStart=last[i]+1;gapEnd=last[i+1]-1;}
      }
      // last[last.length-1]+1 ~ c.max
      const tailGap=c.max-last[last.length-1];
      if(tailGap>maxGap){maxGap=tailGap;gapStart=last[last.length-1]+1;gapEnd=c.max;}
      // 最大空白地帯への流入加点
      if(maxGap>=2){
        for(let n=gapStart;n<=gapEnd;n++){
          if(n>=1&&n<=c.max)wakeIn[n]=0.5;
        }
      }
    }
    for(let n=1;n<=c.max;n++)out[n]+=wakeIn[n];
    // 正規化
    const mx=Math.max.apply(null,Object.values(out).concat([0.001]));
    for(let n=1;n<=c.max;n++)out[n]=out[n]/mx;
    return out;
  },
  // 3) 外力 f : 前回合計値と適温(132等)の差で低/高番台へ偏らせる
  calcExternalForce: function(){
    const c=cfg();
    const opt=this.optimalTemp();
    const out={};
    for(let n=1;n<=c.max;n++)out[n]=0;
    if(lastData.length===0)return out;
    const lastSum=lastData[0].reduce(function(a,b){return a+b;},0);
    const diff=lastSum-opt; // 正=オーバーヒート(低番台へ) / 負=過冷却(高番台へ)
    const strength=Math.min(1,Math.abs(diff)/(opt*0.3)); // 30%以上ズレで最大
    const mid=(c.max+1)/2;
    for(let n=1;n<=c.max;n++){
      const dist=(n-mid)/mid; // -1〜+1
      // diff>0なら低番台優遇 (dist<0で正の力)
      const force=-Math.sign(diff)*dist*strength;
      out[n]=Math.max(0,force); // 負方向は0clip
    }
    return out;
  },
  // 4) 渦/ウェイク効果: 前回最大数字の裏側(直前の数字)に吸引
  calcVortex: function(){
    const c=cfg();
    const out={};
    for(let n=1;n<=c.max;n++)out[n]=0;
    if(lastData.length===0)return out;
    const last=lastData[0];
    const maxNum=Math.max.apply(null,last);
    const radius=this.wakeRadius();
    // maxNum-radius 〜 maxNum-1 が渦の領域(出目自身は除く)
    for(let off=1;off<=radius;off++){
      const n=maxNum-off;
      if(n>=1&&n<=c.max&&last.indexOf(n)<0){
        out[n]=1-(off-1)/radius; // 近いほど強い
      }
    }
    return out;
  },
  // 5本の力を合成
  computeForces: function(){
    const c=cfg();
    const P=this.calcPressure();
    const V=this.calcViscosity();
    const F=this.calcExternalForce();
    const W=this.calcVortex();
    const total={};
    const breakdown={};
    // 重み: 圧力35%, 粘性25%, 外力20%, 渦20%
    const wP=0.35, wV=0.25, wF=0.20, wW=0.20;
    for(let n=1;n<=c.max;n++){
      const p=P[n]||0, v=V[n]||0, f=F[n]||0, w=W[n]||0;
      total[n]=p*wP+v*wV+f*wF+w*wW;
      breakdown[n]={p:p,v:v,f:f,w:w,total:total[n]};
    }
    const lastSum=lastData.length?lastData[0].reduce(function(a,b){return a+b;},0):0;
    this.lastForces=breakdown;
    this.lastMeta={
      optimalTemp:this.optimalTemp(),
      lastSum:lastSum,
      tempDiff:lastSum-this.optimalTemp(),
      wakeRadius:this.wakeRadius(),
      maxLast:lastData.length?Math.max.apply(null,lastData[0]):null
    };
    return total;
  },
  // 各数字について「主たる力」を判定
  dominantForce: function(n){
    if(!this.lastForces||!this.lastForces[n])return null;
    const b=this.lastForces[n];
    const arr=[
      {key:'press',val:b.p*0.35,label:'圧力'},
      {key:'visc',val:b.v*0.25,label:'粘性'},
      {key:'force',val:b.f*0.20,label:'外力'},
      {key:'vortex',val:b.w*0.20,label:'渦'}
    ];
    arr.sort(function(a,b){return b.val-a.val;});
    return arr[0];
  }
};
function generateNSFive(){
  if(!lastData.length){toast('データなし',1500);return;}
  navierStokes.computeForces(); // 力を事前計算
  const lines=pickFiveLines(function(){
    const sc={};
    const c=cfg();
    const total=navierStokes.computeForces();
    for(let n=1;n<=c.max;n++)sc[n]=(total[n]||0)*5+0.05;
    return sc;
  },'pbox-ns','binfo-ns','💎 N-S','ns');
  generateAndShowPatterns(lines,'pbox-ns','binfo-ns','💎 N-S','ns');
}

// ============================================================
// N-S 多層ヒートマップ (出目表下に表示)
// 圧力/粘性/外力/渦 各TOP6を視覚化
// ============================================================
function drawNSHeatmap(){
  const body=document.getElementById('nsheatBody');
  const meta=document.getElementById('nsheatMeta');
  if(!body)return;
  if(!lastData.length){
    body.innerHTML='<div class="ns-empty">📂 データを読み込んでください</div>';
    if(meta)meta.textContent='待機中';
    return;
  }
  const c=cfg();
  // 4つの力を計算 (常に計算: スイッチOFFでも見られるように)
  const P=navierStokes.calcPressure();
  const V=navierStokes.calcViscosity();
  const F=navierStokes.calcExternalForce();
  const W=navierStokes.calcVortex();
  // navierStokes.lastMeta も更新するため computeForces を一度呼ぶ
  navierStokes.computeForces();
  // 上位 c.mc 個の数字を取得 (ロト7=7, ロト6=6, ミニロト=5)
  const PICK_N=c.mc;
  function topN(scoreMap){
    const arr=[];
    for(let n=1;n<=c.max;n++)arr.push({n:n,v:scoreMap[n]||0});
    arr.sort(function(a,b){return b.v-a.v;});
    return arr.slice(0,PICK_N);
  }
  const tP=topN(P), tV=topN(V), tF=topN(F), tW=topN(W);
  // ゾーン定義
  const zones=navierStokes.zones();
  const PER_ZONE=3;
  // N-S Best Picks (現在の選定買い目) を取得して "★" マーク
  const bestSet={};
  if(predict.bestPicks)predict.bestPicks.forEach(function(n){bestSet[n]=true;});
  // 状態判定
  const nsEl=document.getElementById('ppT_ns');
  const masterEl=document.getElementById('ppMaster');
  const nsActive=nsEl&&nsEl.checked&&masterEl&&masterEl.checked;
  let html='';
  // ステータスバー
  const m=navierStokes.lastMeta;
  if(m){
    const tempState=m.tempDiff>10?'🔥オーバーヒート':(m.tempDiff<-10?'❄️過冷却':'⚖均衡');
    const modeBadge=nsActive
      ? '<b style="color:#86efac;">●</b> <b>稼働中</b> (出目表予測に反映)'
      : '<b style="color:#94a3b8;">○</b> 参考表示 (PREDICTION ENGINE で N-S物理モードをONにすると予測に反映)';
    html+='<div class="ns-status">';
    html+='⚛ <b>'+cfg().label+' / N-S物理ヒートマップ</b> &nbsp;|&nbsp; '+modeBadge+'<br>';
    html+='前回Σ <b>'+m.lastSum+'</b> / 適温 <b>'+m.optimalTemp+'</b> / 差 <b>'+(m.tempDiff>=0?'+':'')+m.tempDiff+'</b> → '+tempState+' &nbsp;|&nbsp; ';
    html+='巨大岩 <b>'+(m.maxLast||'-')+'</b> (ウェイク半径±'+m.wakeRadius+') &nbsp;|&nbsp; ';
    html+='ゾーン: <b style="color:'+zones[0].color+'">'+zones[0].label+' '+zones[0].from+'-'+zones[0].to+'</b> / ';
    html+='<b style="color:'+zones[1].color+'">'+zones[1].label+' '+zones[1].from+'-'+zones[1].to+'</b> / ';
    html+='<b style="color:'+zones[2].color+'">'+zones[2].label+' '+zones[2].from+'-'+zones[2].to+'</b>';
    html+='</div>';
  }
  // 1つの力 = 1ヘッダ + 3ゾーンサブ行
  function renderForce(label, iconHtml, rowClass, scoreMap){
    let blk='<div class="ns-force-block '+rowClass+'">';
    // 力ヘッダ
    blk+='<div class="ns-force-head">'+iconHtml+' <b>'+label+'</b></div>';
    // ゾーン3行
    zones.forEach(function(zone){
      const top=navierStokes.topInZone(scoreMap, zone, PER_ZONE);
      const maxV=top[0]?top[0].v:0;
      blk+='<div class="ns-zone-row" style="grid-template-columns:64px repeat('+PER_ZONE+',1fr);">';
      blk+='<div class="ns-zone-label" style="color:'+zone.color+';border-color:'+zone.color+';background:'+zone.cssCol+'0.12);">'+zone.label+' '+zone.from+'-'+zone.to+'</div>';
      for(let i=0;i<PER_ZONE;i++){
        const it=top[i];
        if(!it||it.v<=0.001){
          blk+='<div class="ns-cell empty"><div class="ns-num">--</div></div>';
          continue;
        }
        const rankCls=(i===0?'rank1':'');
        const inBestCls=(bestSet[it.n]?'in-best':'');
        const pct=(it.v*100).toFixed(0);
        blk+='<div class="ns-cell '+rankCls+' '+inBestCls+'" title="数字'+it.n+' / 強度'+pct+'%">';
        blk+='<div class="ns-num">'+p2(it.n)+'</div>';
        blk+='<div class="ns-pct">'+pct+'%</div>';
        blk+='</div>';
      }
      blk+='</div>';
    });
    blk+='</div>';
    return blk;
  }
  html+=renderForce('圧力 -∇p','💥','ns-row-press', P);
  html+=renderForce('粘性 μ∇²v','🌊','ns-row-visc', V);
  html+=renderForce('外力 f','🌡','ns-row-force', F);
  html+=renderForce('渦 / 後流','🌀','ns-row-vortex', W);
  // 全力合算でのトップ候補をゾーン別に表示
  const total={};
  for(let n=1;n<=c.max;n++){
    total[n]=(P[n]||0)*0.35+(V[n]||0)*0.25+(F[n]||0)*0.20+(W[n]||0)*0.20;
  }
  html+='<div class="ns-summary">';
  html+='<span>🎯 <b>合算ゾーン別TOP3</b>:</span>';
  zones.forEach(function(zone){
    const top=navierStokes.topInZone(total, zone, PER_ZONE);
    html+='<span style="display:inline-flex;align-items:center;gap:4px;margin-left:6px;">';
    html+='<b style="color:'+zone.color+';font-size:.85em;">['+zone.label+']</b>';
    top.forEach(function(t){
      if(t.v<=0.001)return;
      html+='<span class="ns-pick-num" style="border-color:'+zone.color+';color:'+zone.color+';background:'+zone.cssCol+'0.18);">'+p2(t.n)+'</span>';
    });
    html+='</span>';
  });
  html+='<br><span style="color:var(--DIM);">★=現在の予測買い目に含まれる数字</span>';
  html+='</div>';
  body.innerHTML=html;
  if(meta){
    meta.textContent=nsActive?('稼働中 / 4力 × 3ゾーン × TOP3'):('参考表示 / 4力 × 3ゾーン × TOP3');
  }
}
function toggleWhy(id){
  const el=document.getElementById(id);
  if(!el)return;
  el.classList.toggle('why-collapsed');
}
function generateAndShowPatterns(lines,boxCls,binfoCls,prefix,kind){
  const cont=document.getElementById('patternsAll');
  let html='<div style="margin-top:6px;">';
  html+='<div style="font-family:\'Inter\',sans-serif;font-size:.7rem;color:var(--ACC);letter-spacing:1.5px;margin-bottom:5px;font-weight:700;">'+prefix+' 5点 ('+lines.length+'パターン)</div>';
  lines.forEach(function(line,i){
    const ctx=buildContext(line,kind);
    const id='why_'+kind+'_'+Date.now()+'_'+i;
    html+='<div id="'+id+'" class="why-collapsed">';
    html+='<div class="'+boxCls+'" onclick="copyPattern(\''+line.map(p2).join(' ')+'\')">';
    html+='#'+(i+1)+': '+line.map(p2).join(' ');
    html+='<span class="'+binfoCls+'">合計:'+ctx.sum+' / 奇'+ctx.odd+':'+ctx.even+'偶</span>';
    html+='</div>';
    html+='<div class="why-toggle" onclick="toggleWhy(\''+id+'\')">📖 理由を見る</div>';
    html+=renderWhyBlock(line,kind);
    html+='</div>';
  });
  html+='</div>';
  cont.innerHTML=html;
  toast(prefix+' 5点生成完了',1500);
}
function copyPattern(s){
  navigator.clipboard.writeText(s).then(function(){toast('コピー: '+s,1500);}).catch(function(){toast('コピー失敗',1500);});
}
</script>

<script>
// ============================================================
// HC / サマリ / プロンプト
// ============================================================
function buildHC(){
  const c=cfg();
  if(!analysis){
    document.getElementById('hotList').innerHTML='<div style="color:#475569;font-size:.7em;">解析未実行</div>';
    document.getElementById('coldList').innerHTML='<div style="color:#475569;font-size:.7em;">解析未実行</div>';
    return;
  }
  const total=analysis.N;
  let h='';
  analysis.hot.slice(0,10).forEach(function(it){
    const pct=(it.c/total*100).toFixed(0);
    h+='<div style="display:flex;align-items:center;gap:6px;padding:3px 5px;border-radius:4px;background:rgba(220,38,38,.08);margin:2px 0;font-family:\'JetBrains Mono\',monospace;font-size:.78em;">';
    h+='<span style="display:inline-block;background:#7f1d1d;color:#fecaca;padding:1px 7px;border-radius:5px;font-weight:700;">'+p2(it.n)+'</span>';
    h+='<span style="color:var(--TXD);font-size:.85em;">'+it.c+'回 ('+pct+'%)</span>';
    h+='</div>';
  });
  document.getElementById('hotList').innerHTML=h;
  let cl='';
  analysis.cold.slice(0,10).forEach(function(it){
    const pct=(it.c/total*100).toFixed(0);
    cl+='<div style="display:flex;align-items:center;gap:6px;padding:3px 5px;border-radius:4px;background:rgba(59,130,246,.08);margin:2px 0;font-family:\'JetBrains Mono\',monospace;font-size:.78em;">';
    cl+='<span style="display:inline-block;background:#1e3a8a;color:#bfdbfe;padding:1px 7px;border-radius:5px;font-weight:700;">'+p2(it.n)+'</span>';
    cl+='<span style="color:var(--TXD);font-size:.85em;">'+it.c+'回 ('+pct+'%)</span>';
    cl+='</div>';
  });
  document.getElementById('coldList').innerHTML=cl;
  document.getElementById('hotHead').textContent='🔥 ホット ('+cfg().label+' 直近'+total+'回)';
  document.getElementById('coldHead').textContent='❄️ コールド';
}
function updateMomList(){
  const c=cfg();
  if(!lastData.length){document.getElementById('momList').innerHTML='';return;}
  const mom=predict.calcMomentum(lastData.map(function(m){return{m:m};}));
  const arr=[];
  for(let n=1;n<=c.max;n++)arr.push({n:n,m:mom[n]||0});
  arr.sort(function(a,b){return b.m-a.m;});
  const top=arr.slice(0,12);
  let h='<div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(70px,1fr));gap:4px;">';
  top.forEach(function(it){
    h+='<div style="background:rgba(245,158,11,.1);border:1px solid var(--WRN);border-radius:6px;padding:4px 6px;font-family:\'JetBrains Mono\',monospace;text-align:center;">';
    h+='<div style="font-weight:700;color:var(--WRN);font-size:1.1em;">'+p2(it.n)+'</div>';
    h+='<div style="font-size:.7em;color:var(--TXD);">直近'+it.m+'回</div>';
    h+='</div>';
  });
  h+='</div>';
  document.getElementById('momList').innerHTML=h;
  // メトリクス
  const sumA=analyzeSum(),oeA=analyzeOE();
  let met='';
  if(sumA){met+='合計平均: <b style="color:var(--ACC);">'+sumA.avg.toFixed(1)+'</b> (σ'+sumA.sd.toFixed(1)+', '+sumA.min+'〜'+sumA.max+')<br>';}
  if(oeA){met+='奇数比率: <b style="color:var(--ACC);">'+(oeA.oddRatio*100).toFixed(1)+'%</b> ('+oeA.odd+'/'+oeA.total+')<br>';}
  document.getElementById('metArea').innerHTML=met;
}
function updateTactical(){
  const tc=document.getElementById('tacticalContent');
  if(!analysis){tc.textContent='解析未実行';return;}
  const c=cfg();
  let html='';
  if(predict.bestPicks&&predict.bestPicks.length){
    html+='<b>🎯 AI予測買い目:</b> '+predict.bestPicks.map(p2).join(' / ')+'<br>';
  }
  if(typePredict.last){
    html+='<b>📐 ゾーン予測:</b> '+typePredict.labelZone(typePredict.last.lastZone)+' → '+(typePredict.last.zoneNext[0]?typePredict.labelZone(typePredict.last.zoneNext[0].type):'?')+'<br>';
    html+='<b>⚖ 奇偶予測:</b> '+typePredict.labelOE(typePredict.last.lastOE)+' → '+(typePredict.last.oeNext[0]?typePredict.labelOE(typePredict.last.oeNext[0].type):'?')+'<br>';
    html+='<b>🌪 荒れスコア:</b> '+typePredict.last.rough.score+'/100<br>';
  }
  if(biasAnalyzer.last&&biasAnalyzer.last.hot.length){
    html+='<b>📊 バイアス:</b> 多発'+biasAnalyzer.last.hot.length+'個 / 不足'+biasAnalyzer.last.cold.length+'個<br>';
  }
  if(analysis.bonus){
    if(analysis.bonus.fixed.length)html+='<b style="color:var(--WRN);">🎯 ボーナス軸:</b> '+analysis.bonus.fixed.map(p2).join(', ')+'<br>';
    if(analysis.bonus.black.length)html+='<b style="color:var(--WRN);">🚫 ボーナス除外:</b> '+analysis.bonus.black.map(p2).join(', ')+'<br>';
  }
  tc.innerHTML=html||'解析データを集計中...';
}
function updateAIPrompt(){
  if(!analysis)return;
  const c=cfg();
  let p='# '+c.label+' 予測コマンド\n\n';
  p+='## 直近サマリ ('+analysis.N+'回)\n';
  p+='- ホット数字: '+analysis.hot.slice(0,7).map(function(x){return p2(x.n)+'('+x.c+')';}).join(', ')+'\n';
  p+='- コールド数字: '+analysis.cold.slice(0,7).map(function(x){return p2(x.n);}).join(', ')+'\n';
  if(predict.bestPicks&&predict.bestPicks.length){
    p+='- AI予測買い目: '+predict.bestPicks.map(p2).join(', ')+'\n';
  }
  if(typePredict.last){
    p+='- 直前型: ゾーン='+typePredict.labelZone(typePredict.last.lastZone)+' / 奇偶='+typePredict.labelOE(typePredict.last.lastOE)+'\n';
    if(typePredict.last.zoneNext[0]){p+='- 次回ゾーン予測: '+typePredict.labelZone(typePredict.last.zoneNext[0].type)+' ('+(typePredict.last.zoneNext[0].prob*100).toFixed(0)+'%)\n';}
    p+='- 荒れスコア: '+typePredict.last.rough.score+'/100\n';
  }
  if(biasAnalyzer.last&&biasAnalyzer.last.hot.length){
    p+='- 多発バイアス: '+biasAnalyzer.last.hot.slice(0,5).map(function(b){return p2(b.num)+'('+b.devPct.toFixed(0)+'%)';}).join(', ')+'\n';
  }
  const fixed=parseN('fixedNums'),black=parseN('blackList'),myp=parseN('myPicks');
  if(fixed.length)p+='- 軸数字: '+fixed.map(p2).join(', ')+'\n';
  if(black.length)p+='- 除外: '+black.map(p2).join(', ')+'\n';
  if(myp.length)p+='- マイピック: '+myp.map(p2).join(', ')+'\n';
  if(analysis.bonus){
    if(analysis.bonus.fixed.length)p+='- ボーナス軸: '+analysis.bonus.fixed.map(p2).join(', ')+'\n';
    if(analysis.bonus.black.length)p+='- ボーナス除外: '+analysis.bonus.black.map(p2).join(', ')+'\n';
  }
  p+='\n## 依頼\n上記の傾向を踏まえ、次回の'+c.mc+'個の予想を5パターン提案して、各パターンの根拠を簡潔に説明してください。';
  document.getElementById('aiPromptPreview').value=p;
}
function copyAIPrompt(){
  const ta=document.getElementById('aiPromptPreview');
  ta.select();
  navigator.clipboard.writeText(ta.value).then(function(){toast('プロンプトをコピー',1500);});
}
function updateSummary(){
  if(!analysis){document.getElementById('sumText').value='';return;}
  const c=cfg();
  let s='='.repeat(50)+'\n';
  s+='【'+c.label+' 解析サマリ】 N='+analysis.N+'回\n';
  s+='='.repeat(50)+'\n\n';
  s+='■ ホット数字 (出現回数順)\n';
  analysis.hot.slice(0,10).forEach(function(it){s+='  '+p2(it.n)+': '+it.c+'回\n';});
  s+='\n■ コールド数字\n';
  analysis.cold.slice(0,10).forEach(function(it){s+='  '+p2(it.n)+': '+it.c+'回\n';});
  if(predict.bestPicks&&predict.bestPicks.length){
    s+='\n■ AI予測買い目\n  '+predict.bestPicks.map(p2).join(' ')+'\n';
  }
  const sumA=analyzeSum();
  if(sumA)s+='\n■ 合計値: 平均'+sumA.avg.toFixed(1)+' / σ'+sumA.sd.toFixed(1)+' / 範囲'+sumA.min+'〜'+sumA.max+'\n';
  const oeA=analyzeOE();
  if(oeA)s+='■ 奇数比率: '+(oeA.oddRatio*100).toFixed(1)+'% ('+oeA.odd+'/'+oeA.total+')\n';
  if(typePredict.last){
    s+='\n■ 型予測\n';
    s+='  ゾーン: '+typePredict.labelZone(typePredict.last.lastZone)+' → '+(typePredict.last.zoneNext[0]?typePredict.labelZone(typePredict.last.zoneNext[0].type)+'('+(typePredict.last.zoneNext[0].prob*100).toFixed(0)+'%)':'?')+'\n';
    s+='  奇偶: '+typePredict.labelOE(typePredict.last.lastOE)+' → '+(typePredict.last.oeNext[0]?typePredict.labelOE(typePredict.last.oeNext[0].type)+'('+(typePredict.last.oeNext[0].prob*100).toFixed(0)+'%)':'?')+'\n';
    s+='  荒れスコア: '+typePredict.last.rough.score+'/100\n';
  }
  if(biasAnalyzer.last&&biasAnalyzer.last.hot.length){
    s+='\n■ バイアス検出\n';
    s+='  多発('+biasAnalyzer.last.hot.length+'個): '+biasAnalyzer.last.hot.slice(0,5).map(function(b){return p2(b.num)+'(+'+b.devPct.toFixed(0)+'%)';}).join(', ')+'\n';
    if(biasAnalyzer.last.cold.length)s+='  不足('+biasAnalyzer.last.cold.length+'個): '+biasAnalyzer.last.cold.slice(0,5).map(function(b){return p2(b.num)+'('+b.devPct.toFixed(0)+'%)';}).join(', ')+'\n';
  }
  if(analysis.bonus){
    if(analysis.bonus.fixed.length||analysis.bonus.black.length){
      s+='\n■ ボーナス設定\n';
      if(analysis.bonus.fixed.length)s+='  軸: '+analysis.bonus.fixed.map(p2).join(', ')+'\n';
      if(analysis.bonus.black.length)s+='  除外: '+analysis.bonus.black.map(p2).join(', ')+'\n';
    }
  }
  document.getElementById('sumText').value=s;
}
function evaluateMySense(){
  if(!arrows.length){toast('矢印が記録されていません',1500);return;}
  if(!lastData.length){toast('データなし',1500);return;}
  const dr=dispRows();
  let hit=0,total=0;
  arrows.forEach(function(a){
    // 予測ゾーン終端は自己評価できない (未来は分からない) → スキップ
    if(a.eR===null||a.eR===undefined)return;
    const targetRowIdx=rToRowIdx(a.eR);
    if(targetRowIdx<0)return; // 表示範囲外
    const targetRow=dr[targetRowIdx];
    if(!targetRow)return;
    if((targetRow.m||[]).indexOf(a.ec)>=0)hit++;
    total++;
  });
  if(total===0){
    toast('評価可能な矢印がありません (予測ゾーン端のみ)',2200);
    return;
  }
  const pct=(hit/total*100).toFixed(1);
  toast('🧠 矢印的中率: '+hit+'/'+total+' ('+pct+'%)',3000);
}
function exportJSON(){
  if(!analysis){toast('解析未実行',1500);return;}
  const data={
    lotto:CL,N:analysis.N,
    hot:analysis.hot.slice(0,10),
    cold:analysis.cold.slice(0,10),
    bestPicks:predict.bestPicks,
    typePredict:typePredict.last,
    bias:biasAnalyzer.last?{hot:biasAnalyzer.last.hot.slice(0,5),cold:biasAnalyzer.last.cold.slice(0,5),strength:biasAnalyzer.last.strength}:null,
    bonus:analysis.bonus,
    timestamp:new Date().toISOString()
  };
  navigator.clipboard.writeText(JSON.stringify(data,null,2)).then(function(){toast('JSONをコピー',1500);});
}

// ============================================================
// CONTROLS
// ============================================================
function setLotto(c,el){
  CL=c;
  document.querySelectorAll('.tab').forEach(function(t){t.classList.remove('active');});
  if(el)el.classList.add('active');
  arrows=[];pLines=[];
  // 切替時に必ず_viewDrawsをクリア (前回の値が残ると整合性崩れる)
  DB[CL]._viewDraws=null;
  // 保存CSVがあれば復元、なければ内蔵
  let restored=false;
  try{ restored=tryRestoreCSV(); }catch(e){ console.error('CSV復元エラー:',e); restored=false; }
  if(!restored){
    // 内蔵データに戻す
    DB[CL]._customDraws=null;
    const builtIn=DB[CL].draws||[];
    allDraws=builtIn.slice();
    displayN=Math.min(50,builtIn.length||50);
    if(displayN<1)displayN=Math.max(1,builtIn.length);
    updateSliderMax();
    reflectToTextarea();
    try{ applyRangeAndAnalyze(); }catch(e){ console.error('解析エラー:',e); toast('表示エラー: '+e.message,3000); }
    document.getElementById('csvMeta').textContent='内蔵 '+builtIn.length+'件';
  }
  document.getElementById('dinfo').innerHTML='<b>'+DB[CL].label+'</b> | '+DB[CL].info;
}
function toggleLines(){
  showLines=!showLines;
  const b=document.getElementById('btnLines');
  b.textContent='〰 流線:'+(showLines?'ON':'OFF');
  b.classList.toggle('b-gr',showLines);
  b.classList.toggle('b-gy',!showLines);
  document.getElementById('lineCtrls').style.display=showLines?'block':'none';
  updateLineUI();
  drawChart();
}
function updateLineUI(){
  document.querySelectorAll('.lr-btn').forEach(function(b){
    b.classList.toggle('b-or',+b.dataset.lr===lineRange);
    b.classList.toggle('b-gy',+b.dataset.lr!==lineRange);
  });
  const bb=document.getElementById('btnLineBonus');
  bb.textContent=lineIncBonus?'含む':'含まない';
}
function setLineRange(r){lineRange=r;updateLineUI();drawChart();}
function toggleLineBonus(){lineIncBonus=!lineIncBonus;updateLineUI();drawChart();}
function setMosanMaxSwing(s){
  mosanMaxSwing=s;
  predict.update();
}
function toggleHotMode(){
  hotMode=!hotMode;
  const b=document.getElementById('btnHotMode');
  b.textContent='🔥 HOT強調:'+(hotMode?'ON':'OFF');
  b.classList.toggle('b-rd',hotMode);
  b.classList.toggle('b-gy',!hotMode);
  drawChart();
}
function toggleColdMode(){
  coldMode=!coldMode;
  const b=document.getElementById('btnColdMode');
  b.textContent='🔄 復活コールド:'+(coldMode?'ON':'OFF');
  b.classList.toggle('b-bl',coldMode);
  b.classList.toggle('b-gy',!coldMode);
  drawChart();
}
function toggleAllMode(){
  allMode=!allMode;
  const b=document.getElementById('btnAllMode');
  b.textContent='🌈 全バッジ表示:'+(allMode?'ON':'OFF');
  b.classList.toggle('b-pu',allMode);
  b.classList.toggle('b-gy',!allMode);
  drawChart();
}
function syncHCButtons(){}
function toggleOrder(){
  orderNew=!orderNew;
  document.getElementById('btnOrd').textContent='🔄 '+(orderNew?'新しい順':'古い順');
  drawChart();
  predict.update();
}
function setMode(m){
  mode=m;
  ['btnMV','btnMA','btnME','btnPL'].forEach(function(id){
    const el=document.getElementById(id);if(!el)return;
    el.classList.remove('b-or','b-vio','b-rd','b-gy');
    el.classList.add('b-gy');
  });
  const map={view:'btnMV',arrow:'btnMA',erase:'btnME',pline:'btnPL'};
  const cur=document.getElementById(map[m]);
  if(cur){
    cur.classList.remove('b-gy');
    if(m==='view')cur.classList.add('b-or');
    else if(m==='arrow')cur.classList.add('b-or');
    else if(m==='erase')cur.classList.add('b-rd');
    else if(m==='pline')cur.classList.add('b-vio');
  }
  const mb=document.getElementById('mbadge');
  if(mb){
    const labels={view:['👁 閲覧モード','mbv'],arrow:['→ 矢印モード','mba'],erase:['✕ 消去モード','mbe'],pline:['📐 線配置モード','mbp']};
    const lbl=labels[m];
    mb.textContent=lbl[0];
    mb.className='mbadge '+lbl[1];
  }
  if(m!=='pline'){plPlacing=null;hideHUD();}
  updatePLMeta();
}
function setAC(el){
  document.querySelectorAll('.sw').forEach(function(s){s.classList.remove('on');});
  el.classList.add('on');
  aCol=el.dataset.c;
}
function toggleHC(){
  const card=document.getElementById('hcFoldCard');
  card.classList.toggle('f-open');
  saveFoldState();
}

// ============================================================
// INIT
// ============================================================
window.addEventListener('DOMContentLoaded',function(){
  loadFoldState();
  setupCSVDrop();
  initSz();
  attachVPHandlers();
  setupTailWheelSwipe();
  // 保存CSVがあれば復元、なければ内蔵データ
  if(!tryRestoreCSV()){
    loadBuiltin();
  }
  document.getElementById('dinfo').innerHTML='<b>'+DB[CL].label+'</b> | '+DB[CL].info;
  setMode('view');
  // tooltip
  const vp=VP();
  vp.addEventListener('mousemove',function(e){
    if(mode!=='view')return;
    const pt=vpToCanvas(e.clientX,e.clientY);
    const cl=getCellFromPt(pt);
    if(!cl){hideTip();return;}
    if(cl.isPzone){
      showTip(e.clientX,e.clientY,'<b>予測ゾーン</b><br>数字 '+cl.c);
    }else{
      const dr=dispRows();
      const row=dr[cl.ri];
      if(row&&row.m.indexOf(cl.c)>=0){
        showTip(e.clientX,e.clientY,'<b>第'+row.r+'回</b><br>数字 '+p2(cl.c)+' (出現)');
      }else{
        showTip(e.clientX,e.clientY,'第'+(row?row.r:'?')+'回 / 数字 '+cl.c);
      }
    }
  });
  vp.addEventListener('mouseleave',function(){hideTip();});
});
window.addEventListener('resize',function(){
  setTimeout(function(){clampPan();applyTransform();},100);
});
</script>

</body>
</html>
