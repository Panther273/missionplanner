<!doctype html>
<html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover"><title>Survey Flight Planner</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Barlow+Semi+Condensed:wght@500;600;700&family=IBM+Plex+Sans:wght@400;500;600&family=IBM+Plex+Mono:wght@400;500&display=swap">
<style>
/* Layout: full-height app. Left = step-by-step mission panel, right = map. Phone: map on top, panel below. */
:root{
  --bg:#e9eeec; --panel:#fbfcfb; --panel2:#f0f4f2; --ink:#15221e; --muted:#586661; --line:#d3dbd7;
  --accent:#0d6b5b; --accent-ink:#ffffff; --accent-soft:#d8eee8;
  --shot:#ffd43b; --area:#e8590c; --route:#1c64d8; --ok:#2b8a3e; --warn:#b45309; --warn-soft:#fdf0d9; --bad:#c92a2a;
  --font-display:'Barlow Semi Condensed','Arial Narrow',system-ui,sans-serif;
  --font-body:'IBM Plex Sans',system-ui,-apple-system,'Segoe UI',sans-serif;
  --font-mono:'IBM Plex Mono',ui-monospace,Menlo,Consolas,monospace;
  --shadow:0 1px 2px rgba(10,30,25,.12),0 4px 14px rgba(10,30,25,.10);
}
@media (prefers-color-scheme: dark){:root:not([data-theme="light"]){
  --bg:#0e1412; --panel:#151d1a; --panel2:#1c2723; --ink:#e3ece8; --muted:#97a8a1; --line:#2b3a35;
  --accent:#4cc3a8; --accent-ink:#06201a; --accent-soft:#1d3a33;
  --area:#ff8a3d; --route:#6ea8ff; --ok:#6fcf86; --warn:#f0b35a; --warn-soft:#33270f; --bad:#ff7b7b;
  --shadow:0 1px 2px rgba(0,0,0,.5),0 4px 14px rgba(0,0,0,.4); color-scheme:dark}}
:root[data-theme="dark"]{
  --bg:#0e1412; --panel:#151d1a; --panel2:#1c2723; --ink:#e3ece8; --muted:#97a8a1; --line:#2b3a35;
  --accent:#4cc3a8; --accent-ink:#06201a; --accent-soft:#1d3a33;
  --area:#ff8a3d; --route:#6ea8ff; --ok:#6fcf86; --warn:#f0b35a; --warn-soft:#33270f; --bad:#ff7b7b;
  --shadow:0 1px 2px rgba(0,0,0,.5),0 4px 14px rgba(0,0,0,.4); color-scheme:dark}

*{box-sizing:border-box}
[hidden]{display:none!important}
html,body{height:100%}
body{margin:0;background:var(--bg);color:var(--ink);font:14px/1.45 var(--font-body);overflow:hidden}
button,input,select{font:inherit;color:inherit}
h1,h2,h3{font-family:var(--font-display);margin:0;text-wrap:balance;letter-spacing:.005em}
.num{font-family:var(--font-mono);font-variant-numeric:tabular-nums}

.app{height:100%;display:grid;grid-template-columns:minmax(340px,420px) minmax(0,1fr)}
.panel{display:flex;flex-direction:column;min-height:0;min-width:0;background:var(--panel);border-right:1px solid var(--line);z-index:2}
.ph{padding:14px 16px 0;border-bottom:1px solid var(--line)}
.brand{display:flex;gap:10px;align-items:center;margin-bottom:12px}
.brand svg{flex:none;color:var(--accent)}
.brand h1{font-size:22px;font-weight:700;line-height:1}
.brand p{margin:3px 0 0;font-size:12px;color:var(--muted)}
.steps{display:grid;grid-template-columns:repeat(6,1fr);gap:2px}
.steps button{background:none;border:0;border-bottom:3px solid var(--line);padding:6px 2px 8px;text-align:left;cursor:pointer;color:var(--muted);min-width:0}
.steps button .n{display:block;font-family:var(--font-mono);font-size:11px}
.steps button .l{display:block;font-family:var(--font-display);font-weight:600;font-size:13px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.steps button.done{border-bottom-color:var(--accent-soft);color:var(--ink)}
.steps button.on{border-bottom-color:var(--accent);color:var(--ink)}
.steps button:disabled{cursor:not-allowed;opacity:.5}
.steps button:focus-visible,.btn:focus-visible,.card:focus-visible,.seg button:focus-visible,.mbtn:focus-visible{outline:2px solid var(--accent);outline-offset:2px}
.pb{flex:1;min-height:0;overflow:auto;padding:16px;display:flex;flex-direction:column;gap:14px}
.pf{display:flex;gap:10px;padding:12px 16px;border-top:1px solid var(--line);background:var(--panel)}
.pb h2{font-size:24px;font-weight:700}
.lead{margin:0;color:var(--muted)}

.btn{border:1px solid var(--line);background:var(--panel2);padding:9px 14px;border-radius:6px;cursor:pointer;font-weight:500;display:inline-flex;gap:8px;align-items:center;justify-content:center}
.btn:hover:not(:disabled){border-color:var(--accent)}
.btn:disabled{opacity:.45;cursor:not-allowed}
.btn.primary{background:var(--accent);color:var(--accent-ink);border-color:var(--accent);flex:1}
.btn.ghost{background:none}
.btn.big{padding:12px 16px;font-size:15px;width:100%}
.row{display:flex;gap:8px;flex-wrap:wrap}

.tools{display:grid;grid-template-columns:repeat(3,1fr);gap:8px}
.tool{display:flex;flex-direction:column;align-items:center;gap:6px;padding:12px 6px;border:1px solid var(--line);border-radius:8px;background:var(--panel2);cursor:pointer;font-weight:500;font-size:13px}
.tool svg{color:var(--area)}
.tool.on{border-color:var(--accent);background:var(--accent-soft);box-shadow:inset 0 0 0 1px var(--accent)}
.hint{padding:10px 12px;border-radius:6px;background:var(--panel2);color:var(--muted);font-size:13px}
.stats{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:1px;background:var(--line);border:1px solid var(--line);border-radius:8px;overflow:hidden}
.stat{background:var(--panel);padding:10px 12px;min-width:0}
.stat b{display:block;font-family:var(--font-mono);font-weight:500;font-size:18px;font-variant-numeric:tabular-nums}
.stat span{font-size:11px;letter-spacing:.06em;text-transform:uppercase;color:var(--muted)}
.stat.wide{grid-column:1/-1}

.cards{display:flex;flex-direction:column;gap:10px}
.card{display:flex;flex-direction:column;gap:6px;text-align:left;padding:12px 14px;border:1px solid var(--line);border-radius:8px;background:var(--panel2);cursor:pointer;width:100%}
.card:hover{border-color:var(--accent)}
.card.on{border-color:var(--accent);background:var(--accent-soft);box-shadow:inset 0 0 0 1px var(--accent)}
.card h3{font-size:19px;font-weight:700;display:flex;justify-content:space-between;gap:8px;align-items:baseline}
.card h3 small{font-family:var(--font-mono);font-weight:400;font-size:11px;color:var(--muted)}
.card p{margin:0;color:var(--muted);font-size:13px}
.specs{display:flex;flex-wrap:wrap;gap:4px 12px;font-size:12px;color:var(--muted)}
.specs b{font-weight:500;color:var(--ink);font-family:var(--font-mono)}
.tag{display:inline-block;padding:1px 7px;border-radius:99px;background:var(--panel);border:1px solid var(--line);font-size:11px;color:var(--muted)}

.fields{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:10px 12px}
.field{display:flex;flex-direction:column;gap:4px;min-width:0}
.field.full{grid-column:1/-1}
.field label{font-size:11px;letter-spacing:.06em;text-transform:uppercase;color:var(--muted)}
.field input,.field select{width:100%;padding:8px 9px;border:1px solid var(--line);border-radius:6px;background:var(--panel);min-width:0}
.field input:focus-visible,.field select:focus-visible{outline:2px solid var(--accent);outline-offset:1px}
.field small{color:var(--muted);font-size:11px}
.reco{display:flex;flex-direction:column;gap:6px;padding:10px 12px;border-radius:8px;background:var(--accent-soft)}
.reco b{font-family:var(--font-display);font-size:15px}
.reco ul{margin:0;padding-left:18px;font-size:13px}
.calc{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:6px 12px;font-size:13px}
.calc div{display:flex;justify-content:space-between;gap:8px;border-bottom:1px dotted var(--line);padding-bottom:3px}
.calc span{color:var(--muted)}
.warns{display:flex;flex-direction:column;gap:6px}
.warn{padding:8px 10px;border-radius:6px;background:var(--warn-soft);color:var(--warn);font-size:13px}
.sect{display:flex;flex-direction:column;gap:8px}
.sect h3{font-size:13px;letter-spacing:.08em;text-transform:uppercase;color:var(--muted);font-family:var(--font-body);font-weight:500}
.batt{display:flex;gap:4px;margin-top:2px}
.batt i{flex:1;height:10px;border-radius:2px;background:var(--accent);position:relative;overflow:hidden}
.batt i.part{background:var(--line)}
.batt i.part u{position:absolute;inset:0 auto 0 0;background:var(--accent)}
.wptable{max-height:190px;overflow:auto;border:1px solid var(--line);border-radius:8px}
.wptable table{width:100%;border-collapse:collapse;font-size:12px}
.wptable th,.wptable td{padding:4px 8px;text-align:right;white-space:nowrap}
.wptable th{position:sticky;top:0;background:var(--panel2);font-weight:500;color:var(--muted);font-size:11px;text-transform:uppercase;letter-spacing:.05em}
.wptable td:first-child,.wptable th:first-child{text-align:left}
.place{font-family:var(--font-display);font-size:18px;font-weight:600}
.note{font-size:12px;color:var(--muted)}

/* map */
.map{position:relative;overflow:hidden;min-width:0;min-height:0;touch-action:none;cursor:grab;user-select:none;-webkit-user-select:none;
  background-color:var(--bg);
  background-image:linear-gradient(var(--line) 1px,transparent 1px),linear-gradient(90deg,var(--line) 1px,transparent 1px);background-size:64px 64px}
.map.drawing{cursor:crosshair}
.map.panning{cursor:grabbing}
.tiles{position:absolute;inset:0;overflow:hidden}
.tile{position:absolute;left:0;top:0;max-width:none;user-select:none;-webkit-user-drag:none;pointer-events:none}
.tile.err{visibility:hidden}
.base{position:absolute;left:0;top:0;width:100%;height:100%;pointer-events:none}
.chips{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:6px}
.chip{display:flex;flex-direction:column;align-items:flex-start;gap:1px;padding:7px 10px;border:1px solid var(--line);border-radius:6px;background:var(--panel2);cursor:pointer;text-align:left}
.chip b{font-family:var(--font-display);font-size:17px;font-weight:600}
.chip span{font-family:var(--font-mono);font-size:11px;color:var(--muted)}
.chip:hover{border-color:var(--accent)}
.chip.on{border-color:var(--accent);background:var(--accent-soft);box-shadow:inset 0 0 0 1px var(--accent)}
.chip:focus-visible{outline:2px solid var(--accent);outline-offset:2px}
.notes{margin:0;padding-left:18px;display:flex;flex-direction:column;gap:5px;font-size:13px;color:var(--ink)}
.wptable.tall{max-height:300px}
.wptable tr[data-act]{cursor:pointer}
.wptable tr[data-act]:hover td{background:var(--accent-soft)}
.shotinfo{flex-wrap:wrap}
.shotinfo button{border:1px solid var(--line);background:var(--panel2);border-radius:99px;padding:2px 10px;font-size:12px;cursor:pointer}
.shotinfo button:hover{border-color:var(--accent)}
.shotinfo button:focus-visible{outline:2px solid var(--accent);outline-offset:2px}
.shotinfo{top:60px;right:12px;display:flex;align-items:center;gap:7px;background:var(--panel);padding:7px 12px;border-radius:99px;box-shadow:var(--shadow);font-size:13px;max-width:calc(100% - 24px)}
.shotinfo i{width:11px;height:11px;border-radius:50%;background:var(--shot);border:1px solid #1b1b1b;flex:none}
.shotinfo b{font-weight:500}
.shotinfo span{color:var(--muted);font-size:12px}
.notice{left:12px;bottom:76px;max-width:min(430px,calc(100% - 24px));background:var(--warn-soft);color:var(--warn);padding:8px 12px;border-radius:8px;font-size:12px;box-shadow:var(--shadow);line-height:1.4}
.ov{position:absolute;inset:0;width:100%;height:100%;overflow:visible;pointer-events:none}
.ov .vh{cursor:move;pointer-events:all}
.ov .cpm{cursor:grab;pointer-events:all}
.cpsw{display:flex;gap:10px;align-items:flex-start;padding:8px 10px;border:1px solid var(--line);border-radius:8px;background:var(--panel2);cursor:pointer}
.cpsw small{display:block;color:var(--muted);font-size:12px}
.seg2{display:flex;border:1px solid var(--line);border-radius:8px;overflow:hidden}
.seg2 button{flex:1;padding:8px;background:var(--panel);border:0;cursor:pointer;font-size:13px}
.seg2 button.on{background:var(--accent-soft);box-shadow:inset 0 0 0 1px var(--accent);font-weight:600}
.cpn{display:flex;gap:8px;align-items:center;flex-wrap:wrap}
.cpn input{width:72px;padding:8px;border:1px solid var(--line);border-radius:6px;background:var(--panel);text-align:center}
.cpn .btn{min-width:40px}
.mini{padding:3px 8px;font-size:12px;border:1px solid var(--line);border-radius:6px;background:var(--panel);cursor:pointer}
.mini.on{border-color:var(--ok);color:var(--ok)}
.cpb{display:flex;gap:4px;white-space:nowrap;justify-content:flex-end}.cpb .mini{padding:3px 6px}
.wptable tr.cpsel td{background:var(--accent-soft)}
.cpdot{display:inline-block;width:8px;height:8px;border-radius:50%;margin-left:5px;background:#f08c00}.cpdot.bad{background:#e03131}
.cpsel-box{margin-top:8px;font-size:12px}
.cpchips{display:flex;flex-wrap:wrap;gap:5px;margin-top:4px}.cpchips span{background:var(--panel2);border:1px solid var(--line);border-radius:99px;padding:2px 8px}
.ctl{position:absolute;z-index:3;cursor:default}
.zoom{top:12px;left:12px;display:flex;flex-direction:column;background:var(--panel);border-radius:8px;box-shadow:var(--shadow);overflow:hidden}
.zoom button{width:40px;height:40px;border:0;background:none;font-size:22px;cursor:pointer;color:var(--ink)}
.zoom button+button{border-top:1px solid var(--line)}
.zoom button:hover{background:var(--panel2)}
.search{top:12px;left:64px;right:12px;max-width:380px;display:flex;background:var(--panel);border-radius:8px;box-shadow:var(--shadow);overflow:hidden}
.search input{flex:1;min-width:0;border:0;background:none;padding:0 12px;height:40px}
.search input:focus-visible{outline:2px solid var(--accent);outline-offset:-2px}
.search button{border:0;border-left:1px solid var(--line);background:none;padding:0 12px;cursor:pointer;font-weight:500}
.seg{display:flex;background:var(--panel);border-radius:8px;box-shadow:var(--shadow);overflow:hidden;right:12px;bottom:34px}
.seg button{border:0;background:none;padding:8px 12px;cursor:pointer;font-size:13px}
.seg button+button{border-left:1px solid var(--line)}
.seg button.on{background:var(--accent);color:var(--accent-ink)}
.readout{left:12px;bottom:12px;display:flex;gap:12px;align-items:flex-end;font-family:var(--font-mono);font-size:11px;background:var(--panel);padding:5px 8px;border-radius:6px;box-shadow:var(--shadow);color:var(--muted)}
.scale{display:flex;flex-direction:column;gap:2px;align-items:flex-start}
.scale i{display:block;height:5px;border:1.5px solid var(--ink);border-top:0}
.attr{right:0;bottom:0;font-size:10px;background:color-mix(in srgb,var(--panel) 85%,transparent);padding:2px 6px;color:var(--muted);border-top-left-radius:6px;max-width:70%}
.banner{top:62px;left:50%;transform:translateX(-50%);background:var(--ink);color:var(--bg);padding:7px 14px;border-radius:99px;font-size:13px;box-shadow:var(--shadow);white-space:nowrap;max-width:92%;overflow:hidden;text-overflow:ellipsis}
.toast{position:fixed;left:50%;bottom:18px;transform:translateX(-50%);background:var(--ink);color:var(--bg);padding:10px 16px;border-radius:8px;box-shadow:var(--shadow);z-index:20;max-width:90%;font-size:13px}

@media (max-width:760px){
  .app{grid-template-columns:minmax(0,1fr);grid-template-rows:42% minmax(0,1fr)}
  .map{order:-1}
  .panel{border-right:0;border-top:1px solid var(--line)}
  .search{max-width:none}
  .brand p{display:none}
  .attr{display:none}
}
@media (prefers-reduced-motion:reduce){*{transition:none!important;animation:none!important}}
.wpdlg{width:272px;background:var(--panel);border:1px solid var(--line);border-radius:10px;box-shadow:var(--shadow);padding:12px;display:flex;flex-direction:column;gap:9px;max-height:calc(100% - 70px);overflow:auto;font-size:13px}
.wpdlg .dh{display:flex;align-items:baseline;gap:8px}
.wpdlg .dh b{font-family:var(--font-display);font-size:20px;font-weight:700;white-space:nowrap}
.wpdlg .dh span{color:var(--muted);font-size:12px;flex:1;min-width:0}
.wpdlg .x{border:0;background:none;font-size:22px;line-height:1;cursor:pointer;color:var(--muted);padding:0 4px}
.wpdlg .x:hover{color:var(--ink)}
.wpdlg .df{display:flex;flex-direction:column;gap:3px}
.wpdlg label{font-size:11px;letter-spacing:.06em;text-transform:uppercase;color:var(--muted)}
.wpdlg input,.wpdlg select{width:100%;min-width:0;padding:6px 8px;border:1px solid var(--line);border-radius:6px;background:var(--panel)}
.wpdlg .ig{display:flex;align-items:center;gap:8px}
.wpdlg .ig span{flex:none;width:84px;color:var(--muted);font-size:12px}
.wpdlg small{color:var(--muted);font-size:11px;min-height:14px}
.wpdlg .nav{display:flex;gap:6px}
.wpdlg .nav button{flex:none;width:34px;border:1px solid var(--line);background:var(--panel2);border-radius:6px;cursor:pointer;font-size:18px;line-height:1}
.wpdlg .nav button:hover{border-color:var(--accent)}
.wpdlg input:focus-visible,.wpdlg select:focus-visible,.wpdlg button:focus-visible{outline:2px solid var(--accent);outline-offset:1px}
.wpdlg .dfoot{display:flex;gap:8px}
.wpdlg .dfoot .btn{padding:7px 10px;flex:1}
.legend{display:flex;flex-wrap:wrap;gap:4px 12px;font-size:12px;color:var(--muted)}
.legend span{display:inline-flex;align-items:center;gap:5px}
.legend i{width:11px;height:11px;border-radius:50%;border:1px solid #1b1b1b;display:inline-block}
.legend i.ring{background:none;border:2px solid var(--area)}
.wptable tr.ed td{background:var(--warn-soft)}
@media (max-width:760px){.wpdlg{left:8px!important;right:8px;top:auto!important;bottom:8px;width:auto;max-height:72%}}
.tfinfo{font-size:12px;color:var(--muted);background:var(--panel2);border-radius:6px;padding:5px 8px}
.prof{width:100%;height:auto;display:block;background:var(--panel);border:1px solid var(--line);border-radius:8px}
.proft{margin-top:4px}
.sfsec h4{margin:8px 0 2px;font-size:12px;letter-spacing:.06em;text-transform:uppercase;color:var(--muted);font-weight:500}
.tgs{display:flex;flex-direction:column;gap:2px}
.tg{display:flex;align-items:center;gap:8px;padding:5px 0;font-size:13px;cursor:pointer}
.tg input{width:16px;height:16px;accent-color:var(--accent);flex:none}
.tg span{flex:1}.tg em{font-style:normal;font-family:var(--font-mono,monospace);font-size:11px;color:var(--muted)}
.sfall{border-radius:8px;padding:10px 12px;display:flex;flex-direction:column;gap:3px;border:1px solid var(--line);background:var(--panel2)}
.sfall b{font-size:14px}.sfall span{font-size:12px;color:var(--muted)}
.sfall.ok{border-color:var(--ok);background:color-mix(in srgb,var(--ok) 12%,var(--panel))}
.sfall.warn{border-color:#f08c00;background:color-mix(in srgb,#f08c00 14%,var(--panel))}
.sfall.bad{border-color:#e03131;background:color-mix(in srgb,#e03131 14%,var(--panel))}
.sfrow{display:flex;gap:9px;padding:7px 0;border-bottom:1px solid var(--line);font-size:13px}
.sfrow>i{flex:none;width:11px;height:11px;border-radius:50%;margin-top:4px;background:#868e96}
.sfrow.ok>i{background:var(--ok)}.sfrow.warn>i{background:#f08c00}.sfrow.bad>i{background:#e03131}.sfrow.off>i{background:none;border:2px solid #868e96}
.sfrow>div{display:flex;flex-direction:column;gap:2px;min-width:0}
.sfrow span{color:var(--muted);font-size:12px}
.sfrow ul,.sfcam{margin:4px 0 0;padding-left:16px;font-size:12px;display:flex;flex-direction:column;gap:3px}
.sfrow li.bad,.sfcam li.bad{color:#e03131}.sfrow li.warn,.sfcam li.warn{color:var(--warn)}.sfrow li.more{color:var(--muted);list-style:none;margin-left:-16px}
.sfcam li.info{color:var(--ink)}
.lnk{background:none;border:0;padding:0;font:inherit;color:inherit;text-align:left;cursor:pointer;text-decoration:underline dotted}
.sfmap{display:flex;flex-direction:column;gap:6px;margin-top:8px}
.sfmap label{font-size:11px;letter-spacing:.06em;text-transform:uppercase;color:var(--muted)}
.sfmap select{padding:7px 8px;border:1px solid var(--line);border-radius:6px;background:var(--panel)}
.sfmap small{color:var(--muted);font-size:11px}
.sfmap a.btn{text-decoration:none;display:inline-flex;align-items:center}
.mrow{display:flex;gap:8px;align-items:flex-end;flex-wrap:wrap;padding:6px 0;font-size:12px}
.mrow label{display:flex;flex-direction:column;gap:2px;color:var(--muted);flex:1;min-width:70px}
.mrow input{width:100%;padding:6px 7px;border:1px solid var(--line);border-radius:6px;background:var(--panel)}
.ack{display:flex;gap:8px;align-items:flex-start;font-size:13px;padding:8px 0}.ack input{margin-top:3px;accent-color:var(--accent)}
.btn:disabled{opacity:.5;cursor:not-allowed}
.btn.on{border-color:var(--accent);background:var(--accent-soft)}
.bpsec .calc{margin-bottom:8px}
.bpsec input[type=range]{width:100%;accent-color:var(--accent)}
.bpsec output{float:right;font-family:var(--font-mono,monospace);color:var(--ink);text-transform:none;letter-spacing:0}
.bptot{display:grid;grid-template-columns:repeat(auto-fit,minmax(96px,1fr));gap:8px;margin:10px 0 4px}
.bptot div{background:var(--panel2);border-radius:8px;padding:8px 10px;display:flex;flex-direction:column;gap:2px}
.bptot span{font-size:11px;color:var(--muted)}.bptot b{font-size:18px}
.fcard{border:1px solid var(--line);border-left:5px solid var(--fc);border-radius:8px;padding:9px 11px;margin:8px 0;display:flex;flex-direction:column;gap:6px;background:var(--panel)}
.fh{display:flex;align-items:center;gap:8px}.fh i{width:10px;height:10px;border-radius:50%;background:var(--fc)}.fh span{color:var(--muted);font-size:12px;flex:1}
.fh em{font-style:normal;font-size:11px;color:#fff;background:#e03131;border-radius:4px;padding:1px 6px}
.fgrid{display:grid;grid-template-columns:repeat(auto-fit,minmax(78px,1fr));gap:6px}
.fgrid div{display:flex;flex-direction:column;gap:1px}.fgrid span{font-size:11px;color:var(--muted)}
.fsub{font-size:11px;color:var(--muted)}
.fcard .row{display:flex;flex-wrap:wrap;gap:6px}.fcard .btn{padding:5px 9px;font-size:12px}
.btn.unit{flex:none;min-width:44px}
.bpsec .ig{display:flex;gap:6px;align-items:center}.bpsec .ig input{min-width:0;flex:1}
</style>

<div class="app">
  <aside class="panel">
    <header class="ph">
      <div class="brand">
        <svg width="34" height="34" viewBox="0 0 34 34" fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true"><path d="M5 25 L12 9 L27 12 L29 27 Z" stroke-dasharray="3 3"/><circle cx="12" cy="9" r="3" fill="currentColor"/><circle cx="27" cy="12" r="3" fill="currentColor"/><circle cx="29" cy="27" r="3" fill="currentColor"/><circle cx="5" cy="25" r="3" fill="currentColor"/></svg>
        <div><h1>Survey Flight Planner</h1><p>Area mapping missions for DJI drones · v1.8.1</p></div>
      </div>
      <nav class="steps" id="steps" aria-label="Planning steps"></nav>
    </header>
    <div class="pb" id="pbody"></div>
    <footer class="pf"><button class="btn" id="back" type="button">Back</button><button class="btn primary" id="next" type="button">Next</button></footer>
  </aside>

  <main class="map" id="map" aria-label="Map">
    <div class="tiles" id="tiles"><canvas class="base" id="base"></canvas></div>
    <svg class="ov" id="ov" aria-hidden="true"></svg>
    <div class="ctl zoom"><button type="button" id="zin" aria-label="Zoom in">+</button><button type="button" id="zout" aria-label="Zoom out">&minus;</button></div>
    <form class="ctl search" id="searchForm"><input id="q" type="text" placeholder="Search a place or type lat, lng" aria-label="Search location" autocomplete="off"><button type="submit">Go</button></form>
    <div class="ctl banner" id="banner" hidden></div>
    <div class="ctl notice" id="notice" hidden></div>
    <div class="ctl shotinfo" id="shotinfo" hidden></div>
    <div class="ctl wpdlg" id="wpdlg" role="dialog" aria-label="Edit waypoint" hidden style="z-index:5"></div>
    <div class="ctl seg" id="layers" role="group" aria-label="Map layer"></div>
    <div class="ctl readout"><div class="scale"><span id="scaleTxt">100 m</span><i id="scaleBar" style="width:100px"></i></div><span id="coord">&nbsp;</span><span id="zl">z13</span></div>
    <div class="ctl attr" id="attr"></div>
  </main>
</div>
<div class="toast" id="toast" hidden></div>

<script>
(()=>{
'use strict';
const $=(s,r=document)=>r.querySelector(s);
const RAD=Math.PI/180;
const clamp=(v,a,b)=>Math.min(b,Math.max(a,v));
const fmt=(n,d=0)=>Number(n).toLocaleString('en-US',{minimumFractionDigits:d,maximumFractionDigits:d});

/* ---------- data ---------- */
const DRONES=[
 {id:'mini4',name:'DJI Mini 4 Pro',weight:'249 g class',flight:34,maxMap:10,enum:[91,0],
  sensor:'1/1.3-inch CMOS',max:'48 MP · 8064 × 6048',lens:'24 mm equiv. · f/1.7 fixed',formats:'JPEG, DNG (RAW)',
  res:{'12':{label:'12 MP',w:4032,h:3024,int:2,intervals:[2,3,5,7,10,15,20,30,60]},'48':{label:'48 MP',w:8064,h:6048,int:5,intervals:[5,7,10,15,20,30,60]}},
  note:'Timed photos: 12 MP every 2 s, 48 MP every 5 s at best.'},
 {id:'mini5',name:'DJI Mini 5 Pro',weight:'249 g class',flight:36,maxMap:12,enum:[91,0],
  sensor:'1-inch CMOS',max:'50 MP · 8192 × 6144',lens:'24 mm equiv. · f/1.8 fixed',formats:'JPEG, DNG (RAW)',
  res:{'12':{label:'12 MP (binned)',w:4096,h:3072,int:2,intervals:[2,3,5,7,10,15,20,30,60]},'48':{label:'50 MP',w:8192,h:6144,int:5,intervals:[5,7,10,15,20,30,60]}},
  note:'Photo intervals are assumed equal to the Mini 4 Pro until confirmed.'},
 {id:'air3s',name:'DJI Air 3S',weight:'724 g',flight:45,maxMap:15,enum:[91,0],
  sensor:'1-inch CMOS (main wide camera)',max:'50 MP · 8192 × 6144',lens:'24 mm equiv. · f/1.8 fixed',formats:'JPEG, DNG (RAW)',
  res:{'12':{label:'12 MP (binned)',w:4096,h:3072,int:2,intervals:[2,3,5,7,10,15,20,30,60]},'48':{label:'50 MP',w:8192,h:6144,int:5,intervals:[5,7,10,15,20,30,60]}},
  note:'Longest flights of the small drones. Photo intervals are assumed.'},
 {id:'mavic3',name:'DJI Mavic 3',weight:'895 g',flight:46,maxMap:15,enum:[77,0],
  sensor:'Four Thirds CMOS (Hasselblad)',max:'20 MP · 5280 × 3956',lens:'24 mm equiv. · f/2.8 to f/11 variable',formats:'JPEG, DNG (RAW)',
  res:{'20':{label:'20 MP',w:5280,h:3956,int:2,intervals:[2,3,5,7,10,15,20,30,60]}},
  note:'Largest sensor, 20 MP only. Variable aperture, so fix the exposure for mapping.'}
];
/* Ground footprint = height x 1.5 across track and x 1.125 along track (24 mm equivalent, 4:3). Reproduces the GSD tables for all four drones. */
const K_ACROSS=1.5,K_ALONG=1.125;
const lerp=(t,h)=>{if(h<=t[0][0])return t[0][1];for(let i=1;i<t.length;i++)if(h<=t[i][0]){const a=t[i-1],b=t[i];return a[1]+(b[1]-a[1])*(h-a[0])/(b[0]-a[0])}return t[t.length-1][1]};
const SPEED_2D=[[20,2],[30,3],[50,4],[75,4.5],[100,4.5],[120,4.5]];
const SPEED_3D=[[20,1.5],[30,2],[50,3],[75,3.5],[100,3.5],[120,3.5]];
const ALT_PRESETS=[20,30,50,70,100,120];
const ALT_NOTES={
 20:'Very small objects, detailed stockpile surface, small construction detail. Use only where safe and legally permitted. Expect very many images on a large area.',
 30:'Small sites, detailed volume surveys, compact construction areas. Excellent detail, but a slower mission.',
 50:'Stockpiles, earthworks, construction sites, detailed orthophotos. Best general-purpose starting altitude.',
 75:'Larger sites, mixed terrain, regular orthophoto work. Good balance of coverage and detail.',
 100:'Large areas and general mapping. Efficient when very fine ground detail is not required.',
 120:'Large-area mapping where permitted. Use when coverage matters more than maximum detail.'};
const altNote=h=>{const k=Object.keys(ALT_NOTES).map(Number).reduce((a,b)=>Math.abs(b-h)<Math.abs(a-h)?b:a);return{k,t:ALT_NOTES[k]}};
const bigSite=g=>g.area>200000;
const MISSIONS=[
 {id:'ortho',name:'Orthophoto base map',tag:'2D map',front:80,side:75,pitch:-90,obl:0,sp:SPEED_2D,pat:'Single grid',altTxt:'30–50 m detailed, 75–100 m large',
  pattern:()=> 'grid',alt:g=>g.area<=100000?50:g.area<=500000?75:100,
  desc:'Georeferenced image of the site, used as a base map for architecture and projects.',out:'Orthomosaic (GeoTIFF)',
  why:()=> 'A single nadir lawnmower grid is enough for a flat, textured site.',
  notes:['Camera straight down (-90°), parallel grid, distance-based photo trigger.','Keep the same camera and exposure settings for the whole dataset. Fix exposure and white balance.','Low-texture surfaces such as uniform fields, sand or snow: raise overlap and consider a cross-grid. Pix4D suggests at least 80 % for agricultural fields and 85 % for forests and dense vegetation.']},
 {id:'area',name:'Area measurement',tag:'2D',front:80,side:75,pitch:-90,obl:0,sp:SPEED_2D,pat:'Single grid',altTxt:'30–100 m',
  pattern:()=> 'grid',alt:g=>g.area<=100000?50:g.area<=500000?75:100,
  desc:'Areas, lengths and boundaries measured on an orthophoto.',out:'Orthomosaic, measured polygons',
  why:()=> 'Area is measured on a 2D orthophoto, so a single nadir grid is enough.',
  notes:['Same collection as an orthophoto: nadir, single grid, 80 / 75 % overlap.','Use ground control points and independent checkpoints where the measurement accuracy matters.']},
 {id:'stockpile',name:'Stockpile volume',tag:'3D · volume',front:85,side:80,pitch:-90,obl:1,oblPitch:-70,sp:SPEED_3D,pat:'Double grid + oblique orbit',altTxt:'30–50 m',
  pattern:()=> 'double',alt:g=>g.area<=10000?30:50,
  desc:'Stockpiles of gravel, sand and soil. Needs strong overlap and views of the steep sides.',out:'Point cloud, DSM, volume',
  why:()=> 'A nadir double grid sees the top; the oblique orbit (-75° to -65°) reconstructs the steep sides.',
  notes:['Shutter 1/1000 to 1/1600 s. Speed 2–4 m/s.','A nadir image sees the top of a stockpile well but represents steep sides poorly. Oblique images reduce holes in the 3D model.','Take extra images around the perimeter and from different viewing angles.','Use GCPs and independent checkpoints where volume accuracy matters.']},
 {id:'earthworks',name:'Earthworks volume',tag:'3D · volume',front:85,side:80,pitch:-90,obl:0,sp:SPEED_3D,pat:'Double grid',altTxt:'30–50 m',
  pattern:()=> 'double',alt:g=>g.area<=20000?30:50,
  desc:'Cut and fill, excavations and graded areas.',out:'Point cloud, DSM, volume',
  why:()=> 'Perpendicular grids give the surface strong geometry for volume work.',
  notes:['Double grid with perpendicular flight lines, nadir camera.','Add oblique images where walls or steep cuts create occlusions.','Use GCPs and independent checkpoints where volume accuracy matters.']},
 {id:'dem',name:'DEM',tag:'Terrain',front:85,side:80,pitch:-90,obl:0,sp:SPEED_3D,pat:'Single grid (add cross-grid if needed)',altTxt:'50–75 m',
  pattern:()=> 'grid',alt:g=>bigSite(g)?70:50,
  desc:'Elevation raster of open, bare terrain. DEM is often used as a general term; tools differ in meaning.',out:'Elevation raster, contours',
  why:()=> 'A nadir grid at 85 / 80 % works for open, textured ground. Switch to a double grid for complex terrain.',
  notes:['Use a second perpendicular grid for complex terrain or when elevation quality is important.','Add oblique imagery where steep slopes, walls or buildings create occlusions.','No lidar and no GNSS base station: absolute accuracy depends on GCPs and checkpoints.']},
 {id:'dsm',name:'DSM',tag:'Surface',front:85,side:80,pitch:-90,obl:0,sp:SPEED_3D,pat:'Single grid (add cross-grid if needed)',altTxt:'50–75 m',
  pattern:()=> 'grid',alt:g=>bigSite(g)?70:50,
  desc:'Digital Surface Model: the top of everything visible, including trees, buildings and other objects.',out:'DSM raster',
  why:()=> 'A DSM is the reconstructed top surface, so the DEM collection settings apply.',
  notes:['DSM includes canopy, roofs and objects. It is not a bare-ground model.','Nadir grid, 85 / 80 % overlap, 3–4 m/s. Add a cross-grid when the surface is complex.']},
 {id:'dtm',name:'DTM',tag:'Bare ground',front:85,side:85,pitch:-90,obl:0,sp:SPEED_3D,pat:'Double grid',altTxt:'50–75 m',
  pattern:()=> 'double',alt:g=>bigSite(g)?70:50,
  desc:'Estimate of the bare ground after objects are removed or classified. For low vegetation with visible ground.',out:'DTM raster (after ground classification)',
  why:()=> 'Higher side overlap and a double grid give more views of the ground between plants.',
  notes:['A photogrammetric DTM is only as good as the ground points that can be observed and classified.','Under dense vegetation the drone reconstructs the canopy, not the soil. Fly in leaf-off or low-vegetation conditions.','No lidar for now, so results in vegetation will be limited.']},
 {id:'building',name:'Building 3D model',tag:'3D · structures',front:85,side:80,pitch:-90,obl:2,oblPitch:-60,sp:SPEED_3D,pat:'Double grid + oblique orbits',altTxt:'30–50 m',
  pattern:()=> 'double',alt:()=>40,
  desc:'Buildings and construction sites, including façades and roofs.',out:'Textured 3D model, point cloud',
  why:()=> 'Nadir double grid for the site plus oblique orbits at two heights for façades.',
  notes:['Fly the oblique orbits at a safe stand-off distance from the structure. Keep high overlap between consecutive images.','Use more than one orbit height for tall buildings.','Add ground-level images if façades or hidden areas are important. Oblique orbits do not replace a nadir grid.']}
];
const LAYERS={
 sat:{name:'Satellite',max:19,url:(z,x,y)=>`https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/${z}/${y}/${x}`,
  attr:'Imagery © Esri, Maxar, Earthstar Geographics'},
 terrain:{name:'Terrain',max:19,url:(z,x,y)=>`https://server.arcgisonline.com/ArcGIS/rest/services/World_Topo_Map/MapServer/tile/${z}/${y}/${x}`,
  attr:'Terrain © Esri, HERE, Garmin, USGS, NGA, OpenStreetMap contributors'}
};
/* Offline world outline (lng,lat rings), drawn under the tiles so the map is never blank. Coarse by design. */
const WORLD=[
'-168,65.6 -164,68.9 -156.5,71.3 -143,70.1 -128,70.2 -115,68.5 -108,68 -98,68 -95,71.8 -90,69 -85,69.8 -82,67 -87,64.8 -94,61.5 -94.5,58.8 -91,57.2 -85,55.3 -82,52.9 -79.5,51.5 -79,54.5 -76.7,56.5 -78,59 -77.5,62.3 -73,62 -70,61 -66,58.5 -64,60.3 -61.5,57 -60,55 -57,53 -56,51.5 -60,50.3 -66,50.2 -70.5,47.5 -65,48.8 -64.5,46.2 -61,45.7 -65.5,43.5 -67,44.8 -70,43.7 -70.5,41.8 -74,40.5 -75.5,38 -76,35.3 -78,33.8 -81,31.5 -80,27 -80.2,25.2 -82.5,27.5 -84,30 -86.5,30.3 -89,30.2 -90,29.1 -93.5,29.7 -97,27.8 -97.5,24 -97.8,21 -96,19 -94.5,18.2 -91,18.8 -90.4,21 -87,21.5 -87.5,18 -88.5,16 -84,15.8 -83.3,12.5 -83.8,10.5 -81.5,9 -79.5,9.5 -77.4,8.7 -77.9,7.3 -80,7.5 -82.8,8.2 -85.7,10 -87.5,13 -91.5,14 -94.5,16.2 -98,16 -103,18.2 -105.5,20.5 -105.5,23 -108.5,25.5 -111,27.8 -113,31 -114.8,31.8 -114.2,29.5 -112.5,27.5 -110.5,24.2 -112,24.5 -114.5,27.8 -115.8,30.3 -117.1,32.5 -118.5,34 -120.6,34.6 -122.5,37.5 -124,40.3 -124.3,43 -124,46.2 -124.7,48.4 -123,48.5 -125,50 -128,51 -130.5,54.5 -134,57.5 -138,59 -144,60 -148,60.5 -152,59.2 -154,57.5 -158,56 -162,55 -165,54.5 -161,56.5 -158,58.7 -162,59.8 -165,60.5 -165,62.5 -161,64.4 -166,64.6',
'-73,78.5 -62,82 -40,83.5 -25,81.5 -19,78 -20,74.5 -22,70.5 -27,68.2 -33,66.5 -40,65 -42,62.5 -43.5,60 -48,60.8 -51,64 -53.5,67 -54,70 -58,75.5 -67,76',
'-90,73.5 -80,73.7 -72,71.5 -66,69 -62,66.5 -64.5,63.3 -69,62.7 -74,64.5 -78,64.5 -73,67.5 -77,68.5 -85,70',
'-118,72.5 -105,73 -101,70 -107,68.8 -115,69.5','-90,76.5 -78,76.5 -68,80 -62,82.5 -80,83 -95,81','-125,72 -117,74 -115,72.5 -122,71.5',
'-59,47.7 -55.5,51.5 -53,49.5 -52.7,47.5 -55,46.8','-85,22 -80,23.2 -74.2,20.2 -77.5,19.9 -82,22.5','-74.4,18.5 -72,19.9 -68.4,18.6 -71,17.7',
'-24,65.5 -22,66.4 -16,66.5 -13.5,65 -18,63.4 -22.5,63.8',
'-77,8.5 -75.5,10.8 -72,12 -71.5,10.5 -68,10.5 -62,10.7 -60,8.5 -57,6 -53,5.5 -51,4.2 -50,1.5 -50,-0.2 -47,-1 -44.5,-2.5 -40,-2.8 -35.2,-5.2 -34.8,-8 -37,-11.5 -39,-14.5 -39.2,-18 -41,-22 -44,-23 -48.5,-26 -48.7,-28.5 -52,-32 -54,-34.5 -57,-35 -56,-37.5 -58,-38.3 -62,-39 -62.5,-41 -65,-41 -65,-45 -67.5,-46.5 -66,-48 -69,-51 -68.4,-52.5 -66.5,-54.5 -68,-55.1 -71.5,-53.9 -74,-52 -75.5,-47 -73.8,-42.5 -73.5,-38 -71.6,-33 -71.5,-30 -70.3,-24 -70.2,-18.4 -75,-15.5 -77.5,-12 -79.5,-8 -81.2,-5.5 -80,-3.2 -80.9,-1 -80,1 -78.8,1.8 -77.3,4 -77.6,6.5',
'-5.9,35.8 -2,35.1 3,36.8 10.2,37.2 11,35.2 10.1,34 15,32.3 19,30.3 20,32.5 24,32.2 29,30.9 32.3,31.3 34.2,31.3 34.9,29.5 34.3,27.8 33.5,28.3 32.6,29.9 34,26.5 35.5,24 37,21 38.5,18.2 40,15.5 43.3,12.7 43.3,11.7 44.5,10.4 48,11.2 51.2,11.8 51,10.4 49.5,7 47.8,4.5 44,0.5 41.5,-2 39.5,-4.8 39,-7 39.7,-10.5 40.5,-14.5 34.8,-19.8 35.5,-22.5 32.8,-25.8 32.9,-28 30.5,-31.2 27,-33.5 22.5,-34 20,-34.8 18.5,-34.1 17.5,-30 15,-26.5 14.5,-22.5 11.8,-17.2 13.5,-12.2 13.2,-8.5 12.2,-6 9,-1 9.5,3.8 8.7,4.5 6,4.3 4.5,6.3 1,6 -2.5,4.8 -7.5,4.4 -9.5,5.5 -13,8.5 -15,11 -16.7,12.5 -17.5,14.8 -16.5,19.5 -16,21.5 -13.5,27.5 -10,29.5 -9.7,31.5 -8.5,33.2 -6.8,34',
'49.3,-12 50.4,-15.5 49,-19.5 47.3,-24.8 44,-24.8 43.3,-21.5 44.4,-16.5 47,-15',
'-9,37 -8.8,41.5 -9.5,43 -4,43.5 -1.5,43.5 -1.3,46.2 -4.7,48.4 -1.5,48.7 1.5,50.2 4,51.5 8.5,53.7 8.2,55.5 8.6,57.1 10.6,57.7 10.5,56 9.7,54.8 12,54.1 14,54 19.5,54.5 21,56.5 23.5,57 24,59.3 28.5,59.5 30,60.1 28,60.5 23,59.9 21.5,60.8 21.5,63 25,65 22,65.8 17.5,62.8 18.5,60.3 16.5,57.5 14.5,56 12.8,55.5 11,58.9 8,58 5.5,59 5,62 10,64 14,67.5 19,70 25,71 31,70.2 33,69.4 41,67 40,66 34,66.5 33,64.5 38,64.5 40.5,64.5 44,66.3 44,68.4 53,68.8 59,68.5 67,69 66.5,70.5 68,73 73,72 75,69.8 78,72.2 81,73.7 87,75.2 96,76.2 104,77.7 113,73.7 119,73 128,72.5 133,71.4 140,72.5 150,71.5 160,70.3 170,69.8 180,69 180,65.5 177,64.7 179,62.5 172,61 164,60 163,57.5 162,54.5 156.5,51 156,57 160,61.5 154,59.4 143,59.3 137.5,54 141,53 140.5,48.5 135.5,43.5 131.5,42.7 129.7,40.8 128,38.5 129.4,36 126.7,34.5 126.2,37.5 124.7,38 125,40 121.5,40.8 119,39 118.5,38.5 122,37 119.5,35 121.9,31.5 121.8,30 122,29 120.5,26.8 118,24.3 114,22.3 110.5,21 108.5,21.7 106.5,20 105.8,19 108.5,15.5 109.3,12 107,10.4 105,8.7 104.8,10.3 103,11.5 100.5,13.3 99.5,10 100.3,8 102.2,6.2 103.5,4.5 103.4,1.4 101.5,2.7 100.3,5.5 98.3,8.3 98.5,12 97.8,16 94.5,16.3 94.3,19 91.5,22.5 90,22 87,21.5 85.5,20 82.5,16.8 80.3,15.5 80,13 79.8,10.3 77.5,8.1 76,10.5 74.5,14.8 73,17.5 72.8,20.5 70,21 68.8,23.3 66.5,25.3 61.5,25.2 57.3,25.7 56.5,27.1 54,26.7 51,28 48.5,30 50,26.5 50.8,25 51.6,24.3 54,24 56,26 56.3,24.8 58.8,23.5 59.8,22.5 58.7,20.4 57.8,19 55,17.2 52.2,16 49,14.5 45,12.8 43.2,13.5 42.7,16.5 41,19.5 39,22 37.2,25.5 35,28.1 34.9,29.5 34.3,31.3 35.1,32.8 35.9,34.5 36.2,36.6 32.5,36.1 30.5,36.5 28,36.7 26.5,38.3 26.2,39.5 26.7,40.1 29,41.2 31,41.1 35,42 38,41 41.5,41.5 41.5,43 37.5,44.8 38,46.2 35,45.3 33,45.1 32.5,46 30,45.5 29.7,43.8 28,43 28,41.9 26,41.4 24,40.9 23,40.2 24,38 22.5,36.5 21.5,38 19.4,40 19.5,42 16,43.5 13.6,45.7 12.3,45.2 12.5,44 14,42.5 16,41.4 18.5,40.1 17,39 16,38 15.7,40 13,41.2 10.5,43 8.8,44.4 7,43.6 3.2,43.2 3,42 0.7,40.7 0,39 -0.3,38.5 -2,36.8 -5.3,36 -6.3,36.9 -7.4,37.2',
'-180,69 -172,67 -169,66 -171,64.5 -175,65.2 -180,65.5',
'79.8,9.5 81.8,7.5 80.5,6 79.8,8','120.2,23 121.5,25.2 122,24.5 120.8,22',
'130.9,31 132,33 135,33.5 137,34.6 139,35 140.8,35.7 141,38.5 141.5,41.5 140,40.5 139.8,38 137,37 136,35.7 133,35.5 131,34.4',
'140,42 141.5,45.4 145.5,44 143.5,42 141,42.4','142,46 143.5,50 142.7,54 142,50',
'120,18.5 122.3,18.4 122,14.5 124,13 121.5,13.8 120.5,15','122,7 126.5,9 126.2,6.5 124,6.5',
'109,1.5 111,2.2 114,4.5 117,7 119,5 118,2 117.5,0.5 116.5,-3.5 114.5,-4 111,-3 110,-1.5',
'95.3,5.6 98,4 104,-1 106,-3.5 105.8,-5.8 102,-4 99,0 97,3','105.2,-6.8 108.5,-6.5 111,-6.5 114.5,-7.8 110,-8.2 106,-7.4',
'119.5,0.8 121,1.4 125,1.5 121,0.5 123,-0.8 122,-3 121,-3 120.2,-5.5 119.5,-3.5 118.8,-2.8',
'131,-0.8 134,-0.8 138,-1.7 141,-2.6 145.5,-4.6 147.5,-6 150,-10.5 147,-10.2 143.5,-9 141,-9.1 138,-8.3 137.8,-5.3 133.5,-4 132.5,-3.3',
'114,-21.7 114,-26.5 115,-30 115.2,-33.5 118,-35 123,-34 126,-32.2 131,-31.5 134,-32.7 135.5,-34.8 138,-35 140,-38 144,-38.4 146.2,-39 148,-37.8 150,-37 151.3,-33.8 153,-30 153.5,-27 152.5,-24 150,-22 146.5,-19 145.5,-15 143.5,-14 142.5,-10.7 141.5,-13.5 141.5,-16.5 139.5,-17.5 136.5,-15.8 136.8,-12.2 132.5,-11.5 130,-13 129,-15 125.5,-14.5 122.5,-17.3 121,-19.5 117,-20.5',
'144.7,-40.7 148,-40.8 148,-43 146,-43.6','172.7,-34.4 175.5,-37 178,-37.6 175.8,-41.5 174.5,-41.5 174.7,-39 173.5,-35.5','172.7,-40.5 174.3,-41.7 172.5,-43.5 170.5,-46 167,-46.2 168.5,-44 171,-42.5',
'-5.5,50 1.5,51 1.7,52.7 0,53.5 -1.5,55.5 -2.5,56.2 -2,57.6 -3.5,58.6 -5,58.6 -6,57 -5.5,55 -3,54.8 -4.5,53.4 -3,51.6','-10,51.7 -6,52.2 -6,54.5 -8,55.3 -10,54',
'11,78.5 17,80 27,80 20,77.5 15,77.3','52,71 57,70.8 69,76.7 60,76.2 55,73.5','12.5,38 15.6,38.2 15.1,36.7 12.5,37.6','8.2,40.9 9.7,41 9.6,39 8.5,39',
'-180,-78.5 -165,-78 -158,-77.5 -150,-76.5 -130,-74.5 -115,-74.2 -102,-74 -98,-72 -85,-73 -72,-72 -68,-70 -65,-68 -62,-66 -60,-64.2 -57,-63.3 -61,-65 -64,-67 -67,-69 -70,-71 -62,-73.5 -58,-75.5 -45,-78 -35,-79 -30,-78.2 -20,-77.5 -10,-72 0,-70 10,-70.5 20,-70 30,-69.8 45,-67.5 55,-66.7 68,-67.7 70,-69.5 75,-69.5 85,-66.3 100,-66.4 115,-66.5 130,-66 145,-67.5 155,-69.5 165,-71.5 170,-72.5 168,-77.5 180,-78.5 180,-85.05 -180,-85.05'
].map(r=>r.split(' ').map(p=>p.split(',').map(Number)));
const WATER=[ '47,45 51,47 53,45 51.5,43 53,41.5 53.8,39 54,37 50.5,36.8 49,38.5 49.5,40.5 47.5,43' ].map(r=>r.split(' ').map(p=>p.split(',').map(Number)));
const BASE_STYLE={
 sat:{sea:'#0b2440',land:'#3f5b37',line:'#7f9e6e',grid:'rgba(255,255,255,.17)',txt:'rgba(255,255,255,.78)'},
 terrain:{sea:'#a8cbe2',land:'#e4e7c9',line:'#8c9b7b',grid:'rgba(30,50,40,.2)',txt:'rgba(20,40,30,.75)'}
};
const STEPS=['Area','Drone','Mission','Settings','Control','Summary'];

/* ---------- state ---------- */
const S={step:0,mode:null,draw:{pts:[],a:null,cur:null},area:null,droneId:null,missionId:null,s:null,dirty:false,layer:'sat',place:null,edits:{},sel:null,editSig:'',tf:'off',tfTol:15,takeoff:null,launchPick:null,cp:{on:true,mode:'auto',n:5,items:[],sel:null,add:false,sig:'',nid:1,rec:null,meas:{on:false,picks:[],type:'both',mode:'chain'}},bp:{cap:100,reserve:25,usableMan:null,wind:0,unit:'ha',areaMan:null,swap:4,lmode:'single',launch:{},cuts:'',cache:null},obsMode:false,safe:{o:{terrain:true,obst:true,lines:true,restr:true,airport:true,temp:true},margin:15,obstH:'',manual:[],obs:{st:'idle',key:'',err:'',list:[],trunc:false},air:{st:'idle',key:'',err:'',list:[]},cc:'',ccAuto:'',ccSig:'',ack:false,ver:0,cache:null},terr:{src:'online',status:'idle',grid:null,sig:'',err:'',ver:0,plane:{z0:0,slope:0,dir:0}}};
const V={lat:25,lng:10,zoom:2,w:800,h:600};
const TS={ok:0,err:0};
const baseEl=document.getElementById('base'),bctx=baseEl.getContext('2d');
let TL={x:0,y:0};
const mapEl=$('#map'),tilesEl=$('#tiles'),ov=$('#ov');
const tiles=new Map();

/* ---------- projection ---------- */
const proj=(lat,lng,z)=>{const n=256*2**z,s=Math.sin(clamp(lat,-85.05,85.05)*RAD);return{x:(lng+180)/360*n,y:(0.5-Math.log((1+s)/(1-s))/(4*Math.PI))*n}};
const unproj=(x,y,z)=>{const n=256*2**z;return{lat:Math.atan(Math.sinh(Math.PI*(1-2*y/n)))/RAD,lng:x/n*360-180}};
function updateTL(){const c=proj(V.lat,V.lng,V.zoom);TL={x:c.x-V.w/2,y:c.y-V.h/2}}
const toScreen=p=>{const w=proj(p.lat,p.lng,V.zoom);return{x:w.x-TL.x,y:w.y-TL.y}};
const fromScreen=(x,y)=>unproj(x+TL.x,y+TL.y,V.zoom);

/* ---------- geometry & planning ---------- */
function analyse(pts){
  const lat0=pts.reduce((a,p)=>a+p.lat,0)/pts.length,lng0=pts.reduce((a,p)=>a+p.lng,0)/pts.length;
  const mx=111320*Math.cos(lat0*RAD),my=110574;
  const xy=pts.map(p=>({x:(p.lng-lng0)*mx,y:(p.lat-lat0)*my}));
  let A=0,cx=0,cy=0,per=0;
  for(let i=0;i<xy.length;i++){const a=xy[i],b=xy[(i+1)%xy.length],cr=a.x*b.y-b.x*a.y;A+=cr;cx+=(a.x+b.x)*cr;cy+=(a.y+b.y)*cr;per+=Math.hypot(b.x-a.x,b.y-a.y)}
  A/=2;
  const c=Math.abs(A)>1e-6?{x:cx/(6*A),y:cy/(6*A)}:{x:0,y:0};
  const R=Math.max(...xy.map(p=>Math.hypot(p.x-c.x,p.y-c.y)));
  return{lat0,lng0,mx,my,xy,area:Math.abs(A),per,c,R,Rorb:Math.max(R+6,15),toLL:(x,y)=>({lat:lat0+y/my,lng:lng0+x/mx})};
}
function metrics(s,g,d){
  const cam=d.res[s.res],circle=s.pattern==='circle';
  const D=circle?Math.hypot(s.height,g.Rorb):s.height;
  const across=D*K_ACROSS,along=D*K_ALONG;
  const gsd=across/cam.w*100;
  const lineSp=across*(1-s.side/100);
  const photoSp=(circle?across:along)*(1-s.front/100);
  const safeSpeed=Math.min(d.maxMap,photoSp/cam.int);
  const ok=cam.intervals.filter(x=>x<=photoSp/Math.max(s.speed,.1)+1e-9);
  return{across,along,gsd,lineSp,photoSp,safeSpeed,cam,D,timed:ok.length?ok[ok.length-1]:cam.int};
}
const gsdAt=(d,res,h)=>h*K_ACROSS/d.res[res].w*100;
function pickSpeed(d,m,g,s){
  const base=lerp(m.sp,s.height),safe=metrics(s,g,d).safeSpeed;
  return clamp(Math.floor(Math.min(base,safe*.9,d.maxMap)*10)/10,.5,d.maxMap);
}
function pickRes(d,m,g,s){
  const keys=Object.keys(d.res);
  if(keys.length===1)return{res:keys[0],why:`${d.res[keys[0]].label} is the only mode on this camera.`};
  if(bigSite(g))return{res:'12',why:'12 MP is recommended: the area is large, so fewer photos mean a shorter flight and faster processing.'};
  const t={...s,res:'48'},safe=metrics(t,g,d).safeSpeed,target=lerp(m.sp,s.height);
  if(safe<target*.8)return{res:'12',why:`12 MP is recommended: ${d.res['48'].label} needs at least ${d.res['48'].int} s between photos, which would limit speed to ${fmt(safe,1)} m/s at this height and overlap (target ${fmt(target,1)} m/s).`};
  return{res:'48',why:`${d.res['48'].label} is recommended: the photo interval does not limit the speed at this height, so you get the finest detail.`};
}
/* sloping-terrain starting presets (target AGL m, overlap %, pattern) */
const TF_PRESET={ortho:{alt:60,front:80,side:75,pat:'grid',obl:0},area:{alt:60,front:80,side:75,pat:'grid',obl:0},
 stockpile:{alt:40,front:85,side:80,pat:'double',obl:1},earthworks:{alt:50,front:85,side:80,pat:'double',obl:0},
 dem:{alt:60,front:85,side:80,pat:'grid',obl:0},dsm:{alt:60,front:85,side:80,pat:'grid',obl:0},
 dtm:{alt:50,front:85,side:85,pat:'double',obl:0},building:{alt:40,front:85,side:80,pat:'grid',obl:2}};
const tfPreset=m=>S.tf!=='off'?TF_PRESET[m.id]:null;
function recommend(d,m,g,resOv,hOv){
  const tp=tfPreset(m);
  const s={res:null,pattern:tp?tp.pat:m.pattern(g),height:hOv||(tp?tp.alt:m.alt(g)),front:tp?tp.front:m.front,side:tp?tp.side:m.side,pitch:m.pitch,bearing:null,obl:tp?tp.obl:m.obl,oblPitch:m.oblPitch||-70,rings:3,battMin:30,speed:3};
  const pr=pickRes(d,m,g,s);s.res=resOv||pr.res;s.why=resOv?'':pr.why;
  s.speed=pickSpeed(d,m,g,s);
  return s;
}
const ACTIONS=[['none','No Action'],['photo','Take Picture'],['startRec','Start Recording'],['stopRec','Stop Recording']];
const ACT_COL={photo:'#ffd43b',none:'#e9ecef',startRec:'#ff6b6b',stopRec:'#9775fa'};
const ringHeights=s=>[1,.6,1.4].slice(0,s.rings).map(k=>clamp(Math.round(s.height*k),10,120));
const patName=s=>(s.pattern==='circle'?`Circle flying, ${s.rings} ring${s.rings>1?'s':''}`:s.pattern==='double'?'Square flying, double grid':'Square flying, single grid')+(s.pattern!=='circle'&&s.obl>0?` + ${s.obl} oblique orbit${s.obl>1?'s':''} at ${s.oblPitch}°`:'');

function gridPass(g,theta,lineSp){
  const c=Math.cos(theta),sn=Math.sin(theta);
  const P=g.xy.map(p=>({x:p.x*c+p.y*sn,y:-p.x*sn+p.y*c}));
  const ys=P.map(p=>p.y),y0=Math.min(...ys),y1=Math.max(...ys),ext=y1-y0;
  const L=Math.max(1,Math.ceil(ext/lineSp)),out=[];
  for(let k=0;k<L;k++){
    const yy=y0+(k+.5)*ext/L,xs=[];
    for(let i=0;i<P.length;i++){const a=P[i],b=P[(i+1)%P.length];
      if((a.y<=yy)!==(b.y<=yy))xs.push(a.x+(yy-a.y)*(b.x-a.x)/(b.y-a.y))}
    xs.sort((p,q)=>p-q);
    let row=[];
    for(let i=0;i+1<xs.length;i+=2)if(xs[i+1]-xs[i]>1)row.push({a:{x:xs[i],y:yy},b:{x:xs[i+1],y:yy}});
    if(k%2===1){row=row.reverse().map(sg=>({a:sg.b,b:sg.a}))}
    out.push(...row);
  }
  return out.map(sg=>({a:{x:sg.a.x*c-sg.a.y*sn,y:sg.a.x*sn+sg.a.y*c},b:{x:sg.b.x*c-sg.b.y*sn,y:sg.b.x*sn+sg.b.y*c}}));
}
function bestTheta(g){
  let best=0,bw=Infinity;
  for(let deg=0;deg<180;deg+=5){const t=deg*RAD,c=Math.cos(t),sn=Math.sin(t);
    const ys=g.xy.map(p=>-p.x*sn+p.y*c),w=Math.max(...ys)-Math.min(...ys);
    if(w<bw-1e-6){bw=w;best=t}}
  return best;
}
/* ---------- terrain model (for Terrain following) ---------- */
const areaSig=()=>JSON.stringify(S.area);
function terrAvail(){const t=S.terr;return !!S.area&&(t.src==='plane'||(t.status==='ready'&&t.sig===areaSig()&&!!t.grid))}
function terrReady(){return S.tf!=='off'&&terrAvail()}
const terrWanted=()=>S.tf!=='off'||S.safe.o.terrain;
function terrKey(){const t=S.terr;if(S.tf==='off')return'off';
  return t.src==='plane'?['p',t.plane.z0,t.plane.slope,t.plane.dir].join():['o',t.status,t.sig===areaSig(),t.ver].join()}
function groundAt(lat,lng){
  const t=S.terr;
  if(t.src==='plane'){const g=geo(),c=g.toLL(g.c.x,g.c.y),p=t.plane,
    dx=(lng-c.lng)*111320*Math.cos(c.lat*RAD),dy=(lat-c.lat)*110574;
    return p.z0+p.slope/100*(dx*Math.sin(p.dir*RAD)+dy*Math.cos(p.dir*RAD))}
  const G=t.grid,fy=clamp((lat-G.lat0)/G.dlat,0,G.ny-1),fx=clamp((lng-G.lng0)/G.dlng,0,G.nx-1);
  const j=Math.min(Math.floor(fy),G.ny-2),i=Math.min(Math.floor(fx),G.nx-2),u=fx-i,v=fy-j,z=(a,b)=>G.z[(j+b)*G.nx+i+a];
  return z(0,0)*(1-u)*(1-v)+z(1,0)*u*(1-v)+z(0,1)*(1-u)*v+z(1,1)*u*v;
}
const TERR_MSG={idle:'',loading:'Loading elevation data…',ready:'',error:''};
function terrStatusText(){
  const t=S.terr;if(!terrWanted())return'';
  if(t.src==='plane')return'Using a flat sloping plane that you set below.';
  if(t.status==='loading')return'Loading elevation data…';
  if(t.status==='error')return t.err;
  if(terrReady()){const G=t.grid,z=G.z;return`Elevation model loaded: ${G.nx} × ${G.ny} points, ${fmt(Math.min(...z),0)} to ${fmt(Math.max(...z),0)} m above sea level. Coarse (about 90 m grid): good for general slope, not for small bumps.`}
  return'Elevation data not loaded yet.';
}
function updTerr(){
  const el=$('#tfstatus');if(el)el.textContent=terrStatusText();
  planCache.key='';
  if(S.step===3){currentPlan();updateCalc();drawOverlay()}else if(S.step>=4)renderPanel(true);else drawOverlay();
}
async function loadTerrain(force){
  const t=S.terr;if(!terrWanted()||t.src!=='online'||!S.area)return;
  const sig=areaSig();
  if(!force&&t.sig===sig&&(t.status==='ready'||t.status==='loading'||t.status==='error'))return;
  t.sig=sig;t.status='loading';t.err='';t.grid=null;updTerr();
  const lats=S.area.map(p=>p.lat),lngs=S.area.map(p=>p.lng);
  let la0=Math.min(...lats),la1=Math.max(...lats),ln0=Math.min(...lngs),ln1=Math.max(...lngs);
  const mx=(ln1-ln0)*111320*Math.cos((la0+la1)/2*RAD),my=(la1-la0)*110574;
  const cell=Math.max(30,Math.sqrt(Math.max(mx*my,1)/64));
  const nx=clamp(Math.ceil(mx/cell)+1,2,10),ny=clamp(Math.ceil(my/cell)+1,2,10);
  const pLa=[],pLn=[];
  for(let j=0;j<ny;j++)for(let i=0;i<nx;i++){pLa.push((la0+(la1-la0)*j/(ny-1)).toFixed(6));pLn.push((ln0+(ln1-ln0)*i/(nx-1)).toFixed(6))}
  try{
    const ctl=new AbortController(),to=setTimeout(()=>ctl.abort(),9000);
    const r=await fetch(`https://api.open-meteo.com/v1/elevation?latitude=${pLa.join(',')}&longitude=${pLn.join(',')}`,{signal:ctl.signal});
    clearTimeout(to);if(!r.ok)throw new Error('The elevation service answered '+r.status+'.');
    const j=await r.json();
    if(!j||!Array.isArray(j.elevation)||j.elevation.length!==nx*ny||j.elevation.some(v=>typeof v!=='number'))throw new Error('The elevation service sent unexpected data.');
    if(sig!==areaSig())return;
    t.grid={lat0:la0,lng0:ln0,dlat:(la1-la0)/(ny-1)||1e-6,dlng:(ln1-ln0)/(nx-1)||1e-6,nx,ny,z:j.elevation};t.status='ready';t.ver++;
  }catch(e){
    if(sig!==areaSig())return;
    t.status='error';t.err=(e&&e.message&&!/fetch|abort|network|load/i.test(e.message)?e.message+' ':'')+'Elevation service not reachable from this view (the preview blocks network access, or you are offline). Open the standalone file in a normal browser, or switch Terrain data to Manual slope.';
  }
  updTerr();
}
const ensureTerrain=()=>{if(S.step>=3&&terrWanted()&&S.terr.src==='online')loadTerrain(false)};
/* keep only the vertices needed to follow the ground profile within tol metres */
function dpKeep(ss,tol){
  const n=ss.length,keep=new Uint8Array(n),dd=[0];keep[0]=keep[n-1]=1;
  for(let i=1;i<n;i++)dd.push(dd[i-1]+Math.hypot(ss[i].x-ss[i-1].x,ss[i].y-ss[i-1].y));
  const st=[[0,n-1]];
  while(st.length){const[a,b]=st.pop();if(b-a<2)continue;let mx=0,mi=-1;
    for(let i=a+1;i<b;i++){const t=(dd[i]-dd[a])/((dd[b]-dd[a])||1),e=Math.abs(ss[i].h-(ss[a].h+(ss[b].h-ss[a].h)*t));if(e>mx){mx=e;mi=i}}
    if(mx>tol){keep[mi]=1;st.push([a,mi],[mi,b])}}
  const out=[];keep.forEach((k,i)=>{if(k)out.push(i)});return out;
}
/* ---------- Safety & Airspace Check (planning aid only) ---------- */
const COUNTRIES=[['AT','Austria','https://utm.dronespace.at/avm/#p=5/47.74/13.23'],['BE','Belgium','https://map.droneguide.be/'],['BG','Bulgaria','https://www.caa.bg/bg/category/633/7062'],['HR','Croatia','https://amc-en.crocontrol.hr/Current-situation-anonymous-users'],['CY','Cyprus','https://drones.gov.cy/'],['CZ','Czechia','https://dronemap.gov.cz/index.php?lang=2'],['DK','Denmark','https://droneregler.dk/dronezoner'],['EE','Estonia','https://aim.eans.ee/droonikaart/'],['FI','Finland','https://aviamaps.com/map?drone#p=4.89/65.69/26'],['FR','France','https://www.geoportail.gouv.fr/donnees/restrictions-uas-categorie-ouverte-et-aeromodelisme'],['DE','Germany','https://maptool-dipul.dfs.de/?language=de'],['GR','Greece','https://dagr.hasp.gov.gr/#map_page'],['HU','Hungary','https://terkep.legter.hu/#7/47.2/19.5'],['IE','Ireland','https://www.iaa.ie/general-aviation/drones/uas-geographic-zones'],['IT','Italy','https://www.d-flight.it/web-app/'],['LV','Latvia','https://www.airspace.lv/drones'],['LT','Lithuania','https://utm.ans.lt/avm/'],['LU','Luxembourg','https://map.geoportail.lu/'],['MT','Malta','https://tmcad.idronect.com/users/sign-in'],['NL','Netherlands','https://map.godrone.nl/'],['NO','Norway','https://www.safetofly.no/'],['PL','Poland','https://dronemap.pansa.pl/'],['PT','Portugal','https://uas.anac.pt/registry/explore'],['RO','Romania','https://flightplan.romatsa.ro/init/drones'],['SK','Slovakia','https://gis.lps.sk/vfrm/index.html'],['SI','Slovenia','https://caa-slovenia.maps.arcgis.com/apps/webappviewer/index.html?id=25ba69037c264c5faa5381174f76f861'],['ES','Spain','https://drones.enaire.es/'],['SE','Sweden','https://daim.lfv.se/echarts/dronechart/'],['CH','Switzerland','https://app.swissuspace.ch/']];
const EASA_NAA='https://www.easa.europa.eu/en/domains/civil-drones/naa';
const AIR_R={aerodrome:8000,heliport:2000,near:300}; /* illustrative search distances in metres, not legal zone sizes */
const SF_RANK={bad:3,warn:2,nodata:1,ok:0,off:-1};
const SF_COL={bad:'#e03131',warn:'#f08c00',none:'#868e96'};
const SF_LBL={bad:'Known conflict or prohibited area',warn:'Potential obstacle or restriction',ok:'No known conflict detected',nodata:'Not checked: data missing',off:'Check switched off'};
const loc=(lat,lng)=>{const g=geo();return{x:(lng-g.lng0)*g.mx,y:(lat-g.lat0)*g.my}};
const numTag=v=>{if(v==null)return null;const m=String(v).replace(',','.').match(/-?\d+(\.\d+)?/);return m?parseFloat(m[0]):null};
const bumpSafe=()=>{S.safe.ver++};
function safeUpdated(){bumpSafe();if(S.step===3){currentPlan();updateCalc();drawOverlay()}else if(S.step>=4)renderPanel(true);else drawOverlay()}
async function ovp(q){
  const ctl=new AbortController(),to=setTimeout(()=>ctl.abort(),25000);
  try{const r=await fetch('https://overpass-api.de/api/interpreter?data='+encodeURIComponent(q),{signal:ctl.signal});
    if(!r.ok)throw new Error('HTTP '+r.status);const j=await r.json();return j.elements||[]}
  finally{clearTimeout(to)}
}
const OVP_ERR='OpenStreetMap data service not reachable from this view (the preview blocks network access, or you are offline). Open the standalone file in a normal browser, or enter obstacles by hand.';
function areaBox(buf){
  const la=S.area.map(p=>p.lat),ln=S.area.map(p=>p.lng),c=(Math.min(...la)+Math.max(...la))/2,dy=buf/110574,dx=buf/(111320*Math.cos(c*RAD));
  return{s:Math.min(...la)-dy,n:Math.max(...la)+dy,w:Math.min(...ln)-dx,e:Math.max(...ln)+dx};
}
function parseObs(els){
  const out=[];
  for(const e of els){
    const t=e.tags||{};
    const geom=e.geometry?e.geometry.map(p=>({lat:p.lat,lng:p.lon})):(e.lat!=null?[{lat:e.lat,lng:e.lon}]:null);
    if(!geom||!geom.length)continue;
    const hT=numTag(t.height),closed=geom.length>3&&geom[0].lat===geom[geom.length-1].lat&&geom[0].lng===geom[geom.length-1].lng;
    let type,kind,h,est='',wire=false,cat='bt';
    const dflt=(v,why)=>{if(hT!=null)return hT;est=why;return v};
    if(t.building){type='Building';kind='poly';const lv=numTag(t['building:levels']);
      if(hT!=null)h=hT;else if(lv){h=lv*3;est=`from ${lv} levels`}else{h=8;est='assumed 8 m'}}
    else if(t.natural==='wood'||t.landuse==='forest'){type='Trees';kind='poly';h=dflt(20,'assumed 20 m')}
    else if(t.natural==='tree_row'){type='Tree row';kind='line';h=dflt(12,'assumed 12 m')}
    else if(t.natural==='tree'){type='Tree';kind='point';h=dflt(12,'assumed 12 m')}
    else if(t.power==='line'||t.power==='minor_line'||t.power==='cable'){type='Power line';kind='line';wire=true;cat='pl';h=dflt(t.power==='minor_line'?10:25,'assumed '+(t.power==='minor_line'?10:25)+' m')}
    else if(t.power==='tower'){type='Pylon';kind='point';cat='pl';h=dflt(40,'assumed 40 m')}
    else if(t.power==='pole'){type='Pole';kind='point';cat='pl';h=dflt(10,'assumed 10 m')}
    else if(t['generator:source']==='wind'){type='Wind turbine';kind='point';cat='pl';h=dflt(100,'assumed 100 m')}
    else if(t.man_made){type=t.man_made[0].toUpperCase()+t.man_made.slice(1).replace(/_/g,' ');cat='pl';kind=e.type==='way'?(closed?'poly':'line'):'point';h=dflt(40,'assumed 40 m')}
    else continue;
    const pts=geom.map(p=>loc(p.lat,p.lng));
    out.push({id:e.type[0]+e.id,type,kind,h,known:hT!=null,est,wire,cat,name:t.name||'',ll:geom,pts});
  }
  return out;
}
async function loadObs(force){
  const sf=S.safe,o=sf.o,ob=sf.obs;
  if(!S.area||!(o.obst||o.lines)){ob.st='off';return}
  const buf=Math.max(100,Math.ceil((+sf.margin||0)/50)*50),key=[areaSig(),o.obst,o.lines,buf].join('|');
  if(!force&&ob.key===key&&ob.st!=='idle')return;
  ob.key=key;ob.st='loading';ob.err='';ob.list=[];safeUpdated();
  const b=areaBox(buf),bb=`(${b.s.toFixed(6)},${b.w.toFixed(6)},${b.n.toFixed(6)},${b.e.toFixed(6)})`;
  let q='';
  if(o.obst)q+=`way["building"]${bb};way["natural"="wood"]${bb};way["landuse"="forest"]${bb};way["natural"="tree_row"]${bb};node["natural"="tree"]${bb};`;
  if(o.lines)q+=`way["power"~"^(line|minor_line|cable)$"]${bb};node["power"~"^(tower|pole)$"]${bb};node["man_made"~"^(tower|mast|chimney|communications_tower|crane)$"]${bb};way["man_made"~"^(tower|mast|chimney|communications_tower|crane)$"]${bb};node["generator:source"="wind"]${bb};`;
  try{const els=await ovp(`[out:json][timeout:25];(${q});out tags geom 2500;`);
    if(ob.key!==key)return;ob.list=parseObs(els);ob.trunc=els.length>=2500;ob.st='ready'}
  catch(e){if(ob.key!==key)return;ob.st='error';ob.err=OVP_ERR}
  safeUpdated();
}
function parseAir(els){
  const out=[];
  for(const e of els){const t=e.tags||{};let kind,label;
    if(t.aeroway==='aerodrome'){kind='aerodrome';label=t.aerodrome==='international'||t.aerodrome==='public'?'Airport':'Aerodrome'}
    else if(t.aeroway==='heliport'){kind='heliport';label='Heliport'}
    else if(t.landuse==='military'||t.military){kind='restr';label='Military area'}
    else if(t.amenity==='prison'){kind='restr';label='Prison'}
    else if(t.power==='plant'){kind='restr';label='Nuclear power plant'}
    else if(t.boundary==='protected_area'||t.boundary==='national_park'){kind='restr';label=t.boundary==='national_park'?'National park':'Protected area'}
    else continue;
    const bb=e.bounds?{s:e.bounds.minlat,w:e.bounds.minlon,n:e.bounds.maxlat,e:e.bounds.maxlon}:e.lat!=null?{s:e.lat,n:e.lat,w:e.lon,e:e.lon}:e.center?{s:e.center.lat,n:e.center.lat,w:e.center.lon,e:e.center.lon}:null;
    if(!bb)continue;
    out.push({id:e.type[0]+e.id,kind,label,name:t.name||t['name:en']||'',bb,c:e.center?{lat:e.center.lat,lng:e.center.lon}:{lat:(bb.s+bb.n)/2,lng:(bb.w+bb.e)/2},point:e.type==='node'});
  }
  return out;
}
async function loadAir(force){
  const sf=S.safe,o=sf.o,ar=sf.air;
  if(!S.area||!(o.restr||o.airport)){ar.st='off';return}
  const key=[areaSig(),o.restr,o.airport].join('|');
  if(!force&&ar.key===key&&ar.st!=='idle')return;
  ar.key=key;ar.st='loading';ar.err='';ar.list=[];safeUpdated();
  const g=geo(),c=g.toLL(g.c.x,g.c.y),R=Math.ceil(g.R+AIR_R.near+50),RA=Math.ceil(g.R+AIR_R.aerodrome+50);
  let q='';
  if(o.airport)q+=`nwr["aeroway"~"^(aerodrome|heliport)$"](around:${RA},${c.lat.toFixed(6)},${c.lng.toFixed(6)});`;
  if(o.restr)q+=`nwr["landuse"="military"](around:${R},${c.lat.toFixed(6)},${c.lng.toFixed(6)});nwr["military"](around:${R},${c.lat.toFixed(6)},${c.lng.toFixed(6)});nwr["amenity"="prison"](around:${R},${c.lat.toFixed(6)},${c.lng.toFixed(6)});nwr["power"="plant"]["plant:source"="nuclear"](around:${R},${c.lat.toFixed(6)},${c.lng.toFixed(6)});nwr["boundary"~"^(protected_area|national_park)$"](around:${R},${c.lat.toFixed(6)},${c.lng.toFixed(6)});`;
  try{const els=await ovp(`[out:json][timeout:25];(${q});out center bb tags 400;`);
    if(ar.key!==key)return;ar.list=parseAir(els);ar.st='ready'}
  catch(e){if(ar.key!==key)return;ar.st='error';ar.err=OVP_ERR}
  safeUpdated();
}
async function detectCountry(){
  const sf=S.safe,sig=areaSig();if(!S.area||sf.ccSig===sig)return;sf.ccSig=sig;
  try{const g=geo(),c=g.toLL(g.c.x,g.c.y);
    const r=await fetch(`https://nominatim.openstreetmap.org/reverse?format=jsonv2&zoom=3&addressdetails=1&lat=${c.lat}&lon=${c.lng}`);
    const j=await r.json(),cc=j&&j.address&&j.address.country_code;
    if(cc&&sf.ccSig===sig){sf.ccAuto=cc.toUpperCase();safeUpdated()}}
  catch(e){}
}
function ensureSafety(){if(S.step<3||!S.area)return;loadObs(false);loadAir(false);detectCountry()}
const effCC=()=>S.safe.cc||S.safe.ccAuto;
/* distance helpers in local metres */
function dPt(a,b){return Math.hypot(a.x-b.x,a.y-b.y)}
function dSeg(p,a,b){const dx=b.x-a.x,dy=b.y-a.y,l2=dx*dx+dy*dy,t=l2?clamp(((p.x-a.x)*dx+(p.y-a.y)*dy)/l2,0,1):0;return Math.hypot(p.x-a.x-t*dx,p.y-a.y-t*dy)}
function inPoly(p,P){let c=false;for(let i=0,j=P.length-1;i<P.length;j=i++){if((P[i].y>p.y)!==(P[j].y>p.y)&&p.x<(P[j].x-P[i].x)*(p.y-P[i].y)/(P[j].y-P[i].y)+P[i].x)c=!c}return c}
function dPoly(p,P){if(inPoly(p,P))return 0;let m=1e9;for(let i=0;i<P.length;i++)m=Math.min(m,dSeg(p,P[i],P[(i+1)%P.length]));return m}
function dLine(p,P){let m=1e9;if(P.length===1)return dPt(p,P[0]);for(let i=1;i<P.length;i++)m=Math.min(m,dSeg(p,P[i-1],P[i]));return m}
function terrCtx(p){
  const have=terrAvail(),s0=p.shots[0];
  const T=S.takeoff!=null&&S.takeoff!==''?+S.takeoff:(have&&s0?groundAt(s0.lat,s0.lng):0);
  return{have,T,rel:(lat,lng)=>have?groundAt(lat,lng)-T:0};
}
const ptsCentroid=P=>({x:P.reduce((a,q)=>a+q.x,0)/P.length,y:P.reduce((a,q)=>a+q.y,0)/P.length});
function rectDist(a,b){ /* distance in metres between two lat/lng boxes, 0 if they overlap */
  const g=geo(),dLat=Math.max(0,a.s-b.n,b.s-a.n)*g.my,dLng=Math.max(0,a.w-b.e,b.w-a.e)*g.mx;return Math.hypot(dLat,dLng);
}
function safetyEval(p){
  const sf=S.safe,o=sf.o,margin=Math.max(0,+sf.margin||0),g=geo(),s=S.s;
  const key=JSON.stringify([o,margin,sf.obstH,sf.manual,sf.obs.key,sf.obs.st,sf.air.key,sf.air.st,sf.ver,terrKey(),S.terr.ver,S.terr.status,S.takeoff,S.terr.src,S.terr.plane]);
  if(sf.cache&&sf.cache.p===p&&sf.cache.key===key)return sf.cache.r;
  const ctx=terrCtx(p),sh=p.shots,areaXY=g.xy;
  /* sample the whole route every few metres, with interpolated altitude */
  let total=0;for(let i=1;i<sh.length;i++)total+=Math.hypot(sh[i].x-sh[i-1].x,sh[i].y-sh[i-1].y);
  const step=clamp(total/5000,3,50),sm=[];
  const mk=(a,b,t,i)=>{const lat=a.lat+(b.lat-a.lat)*t,lng=a.lng+(b.lng-a.lng)*t;return{x:a.x+(b.x-a.x)*t,y:a.y+(b.y-a.y)*t,h:a.h+(b.h-a.h)*t,lat,lng,i:t<.5?i:i+1}};
  sh.forEach((a,i)=>{sm.push(mk(a,a,0,i));const b=sh[i+1];if(b){const n=Math.floor(Math.hypot(b.x-a.x,b.y-a.y)/step);for(let k=1;k<=n;k++)sm.push(mk(a,b,k/(n+1),i))}});
  sm.forEach(q=>{q.gr=ctx.rel(q.lat,q.lng)});
  const sevShot=new Array(sh.length).fill(null),mark=(i,v)=>{if(!sevShot[i]||SF_RANK[v]>SF_RANK[sevShot[i]])sevShot[i]=v};
  const cats=[],obsSev={};
  const pushCat=(id,group,label,status,msg,items,count)=>cats.push({id,group,label,status,msg,items:items||[],count:count||0});
  /* terrain */
  if(!o.terrain)pushCat('terrain','Obstacle checking','Terrain elevation','off','');
  else if(!ctx.have)pushCat('terrain','Obstacle checking','Terrain elevation','nodata',S.terr.status==='loading'?'Elevation data is loading. Until then flat ground at take-off level is assumed.':'No elevation data. Flat ground at take-off level is assumed. Load online elevation or use Manual slope in Terrain following.');
  else{let min=1e9,at=0;sm.forEach(q=>{const c=q.h-q.gr;if(c<min){min=c;at=q.i}});
    sm.forEach(q=>{const c=q.h-q.gr;if(c<=0)mark(q.i,'bad');else if(c<margin)mark(q.i,'warn')});
    const st=min<=0?'bad':min<margin?'warn':'ok';
    pushCat('terrain','Obstacle checking','Terrain elevation',st,st==='ok'?`Lowest height above the modelled ground is ${fmt(min,0)} m.`:min<=0?`The route goes into the modelled ground near Waypoint ${at+1}.`:`The route comes within ${fmt(min,0)} m of the modelled ground near Waypoint ${at+1}, less than your ${margin} m margin.`,
      st==='ok'?[]:[{sev:st,text:`Closest approach to the ground: ${fmt(min,0)} m, near Waypoint ${at+1}`,i:at}],st==='ok'?0:1)}
  /* obstacles from OpenStreetMap + manual */
  const groups=[['bt','Buildings and trees','obst'],['pl','Power lines and towers','lines']];
  const evalObs=(list,own)=>{const hits=[];
    for(const ob of list){
      const P=ob.pts,mnx=Math.min(...P.map(q=>q.x))-margin-6,mxx=Math.max(...P.map(q=>q.x))+margin+6,mny=Math.min(...P.map(q=>q.y))-margin-6,mxy=Math.max(...P.map(q=>q.y))+margin+6;
      const cL=ptsCentroid(P),cll={lat:g.lat0+cL.y/g.my,lng:g.lng0+cL.x/g.mx},top=ctx.rel(cll.lat,cll.lng)+ob.h;
      let best=null;
      for(const q of sm){
        if(q.x<mnx||q.x>mxx||q.y<mny||q.y>mxy)continue;
        let dh=ob.kind==='poly'?dPoly(q,P):ob.kind==='line'?dLine(q,P):dPt(q,P[0]);
        if(ob.r)dh=Math.max(0,dh-ob.r);
        if(dh>margin)continue;
        const dv=ob.wire?Math.abs(q.h-top):Math.max(0,q.h-top),d3=Math.hypot(dh,dv);
        let sev=null;
        if(ob.wire){if(d3<5)sev=ob.known?'bad':'warn'}else if(d3<.5)sev=ob.known?'bad':'warn';
        if(!sev&&d3<margin)sev='warn';
        if(sev&&(!best||SF_RANK[sev]>SF_RANK[best.sev]||(sev===best.sev&&d3<best.d3)))best={sev,d3,i:q.i};
        if(sev)mark(q.i,sev);
      }
      if(best){obsSev[ob.id]=best.sev;hits.push({sev:best.sev,i:best.i,text:`${ob.type}${ob.name?' "'+ob.name+'"':''}, ${fmt(ob.h,0)} m${ob.est?' ('+ob.est+')':''}: ${best.d3<.5?'the route passes through it':'closest approach '+fmt(best.d3,0)+' m'} near Waypoint ${best.i+1}`,d3:best.d3})}
    }
    return hits.sort((a,b)=>SF_RANK[b.sev]-SF_RANK[a.sev]||a.d3-b.d3);
  };
  for(const[cat,label,opt]of groups){
    if(!o[opt]){pushCat(cat,'Obstacle checking',label,'off','');continue}
    if(sf.obs.st!=='ready'){pushCat(cat,'Obstacle checking',label,'nodata',sf.obs.st==='loading'?'Loading obstacle data from OpenStreetMap…':(sf.obs.err||'Obstacle data not loaded yet.'));continue}
    const list=sf.obs.list.filter(x=>x.cat===cat),hits=evalObs(list);
    const st=hits.some(x=>x.sev==='bad')?'bad':hits.length?'warn':'ok';
    pushCat(cat,'Obstacle checking',label,st,st==='ok'?`${list.length} mapped object${list.length===1?'':'s'} checked. OpenStreetMap is incomplete, and tree and building heights are often missing or estimated.`:`${hits.length} of ${list.length} mapped objects are close to the route.${sf.obs.trunc?' Data was capped, so some objects may be missing.':''}`,hits,hits.length);
  }
  /* your own obstacles */
  const own=sf.manual.map(m=>({id:m.id,type:m.name||'Your obstacle',kind:'point',h:+m.h||0,known:true,est:'',wire:false,name:'',r:+m.r||0,pts:[loc(m.lat,m.lng)]}));
  let ownHits=evalObs(own);
  const H=parseFloat(sf.obstH);
  if(H>0){let best=null;
    for(const q of sm){const dh=dPoly(q,areaXY);if(dh>margin)continue;const dv=q.h-(q.gr+H),d3=dv>0?Math.hypot(dh,dv):dh,sev=d3<.5?'bad':d3<margin?'warn':null;
      if(sev){mark(q.i,sev);if(!best||SF_RANK[sev]>SF_RANK[best.sev]||(sev===best.sev&&d3<best.d3))best={sev,d3,i:q.i,clr:dv}}}
    if(best)ownHits.unshift({sev:best.sev,i:best.i,text:`Tallest obstacle ${fmt(H,0)} m above ground: ${best.clr<=0?'the route is below its top':'only '+fmt(best.clr,0)+' m of clearance'} near Waypoint ${best.i+1}`,d3:best.d3});
  }
  if(!sf.manual.length&&!(H>0))pushCat('own','Obstacle checking','Your own obstacles','ok','None entered. Add the tallest obstacle height, or place obstacles on the map.',[],0);
  else{const st=ownHits.some(x=>x.sev==='bad')?'bad':ownHits.length?'warn':'ok';
    pushCat('own','Obstacle checking','Your own obstacles',st,st==='ok'?'Your obstacles are clear of the route by more than the margin.':`${ownHits.length} of your obstacles ${ownHits.length===1?'is':'are'} too close to the route.`,ownHits,ownHits.length)}
  /* airspace */
  const mb=areaBox(0),airHits={restr:[],airport:[]};
  if(sf.air.st==='ready')for(const a of sf.air.list){
    const d=rectDist(mb,a.bb);let lim=a.kind==='aerodrome'?AIR_R.aerodrome:a.kind==='heliport'?AIR_R.heliport:AIR_R.near,grp=a.kind==='restr'?'restr':'airport';
    if(d>lim)continue;
    const nm=`${a.label}${a.name?' "'+a.name+'"':''}`;
    const txt=d<1?`${nm}: the mission area overlaps its mapped area${a.point?'':' (bounding box, approximate)'}`:`${nm}: ${d>=1000?fmt(d/1000,1)+' km':fmt(d,0)+' m'} from the mission area`;
    airHits[grp].push({sev:'bad',text:txt+(grp==='airport'?'. Controlled and restricted zones often extend several km around airports.':''),d,lat:a.c.lat,lng:a.c.lng,kind:a.kind,bb:a.bb});
  }
  const airCat=(id,label,opt,arr)=>{
    if(!o[opt])return pushCat(id,'Airspace checking',label,'off','');
    if(sf.air.st!=='ready')return pushCat(id,'Airspace checking',label,'nodata',sf.air.st==='loading'?'Loading airspace-related data from OpenStreetMap…':(sf.air.err||'Not loaded yet.'));
    arr.sort((a,b)=>a.d-b.d);
    pushCat(id,'Airspace checking',label,arr.length?'bad':'ok',arr.length?`${arr.length} possible restriction${arr.length>1?'s':''} found nearby. Confirm on the official map.`:'Nothing found in OpenStreetMap near the mission area. This is not the official airspace database.',arr,arr.length);
  };
  airCat('restr','Restricted zones (military, prisons, nuclear, protected areas)','restr',airHits.restr);
  airCat('airport','Airport and heliport zones','airport',airHits.airport);
  if(!o.temp)pushCat('temp','Airspace checking','Temporary restrictions','off','');
  else pushCat('temp','Airspace checking','Temporary restrictions','nodata','This app cannot check NOTAMs or temporary restrictions. Check them on the official map and with your aviation authority before every flight.');
  /* camera and photo checks */
  const cam=[],m=p.m;
  const need=s&&s.speed>0?m.gsd/100*.5/s.speed:0,Ns=need?1/need:0,shut=[250,500,1000,2000,4000,8000].find(v=>v>=Ns)||8000;
  if(need)cam.push({sev:Ns>2000?'warn':'info',text:`Set the shutter to 1/${shut} s or faster so motion blur stays under half a pixel at ${fmt(s.speed,1)} m/s and ${m.gsd.toFixed(2)} cm/px.${Ns>2000?' That needs bright light. Lower the speed or fly higher.':''}`});
  let hmax=Math.max(H>0?H:0,...sf.manual.map(m=>+m.h||0),0),hsrc=hmax>0?'your entry':'';
  if(sf.obs.st==='ready')for(const ob of sf.obs.list){if(ob.cat==='bt'&&ob.kind==='poly'&&inPoly(ptsCentroid(ob.pts),areaXY)&&ob.h>hmax){hmax=ob.h;hsrc=ob.type.toLowerCase()+(ob.est?' ('+ob.est+')':'')}}
  if(hmax>0&&s&&s.height>hmax){const r=(s.height-hmax)/s.height,sE=Math.round(100-(100-s.side)/r),fE=Math.round(100-(100-s.front)/r);
    cam.push({sev:(sE<65||fE<70)?'warn':'info',text:`Tallest object in the area is about ${fmt(hmax,0)} m (${hsrc}). On its top the real overlap is about ${fE} / ${sE} % instead of ${s.front} / ${s.side} %, because the camera is closer.${(sE<65||fE<70)?' Raise the overlap or fly higher.':''}`})}
  else if(hmax>0&&s&&s.height<=hmax)cam.push({sev:'warn',text:`Tallest object in the area (${fmt(hmax,0)} m) is as tall as or taller than the flight height (${s.height} m). Raise the flight height.`});
  if(s&&s.pattern!=='circle'&&s.obl>0&&hmax>0)cam.push({sev:'info',text:'Oblique orbits go around the area, so check the orbit path against the obstacles listed above.'});
  /* overall */
  let overall='ok';for(const c of cats){if(SF_RANK[c.status]>SF_RANK[overall])overall=c.status}
  if(cats.every(c=>c.status==='off'))overall='off';
  const r={cats,cam,overall,sevShot,obsSev,airHits:airHits.restr.concat(airHits.airport),hmax,ctx};
  sf.cache={p,key,r};return r;
}
const esc2=t=>String(t).replace(/&/g,'&amp;').replace(/</g,'&lt;');
function safetyHtml(r,full){
  const sf=S.safe,cc=effCC(),co=COUNTRIES.find(c=>c[0]===cc);
  const head={bad:'Known conflict or prohibited area found',warn:'Potential obstacle or restriction found',nodata:'Check incomplete: some data is missing',ok:'No known conflict detected',off:'All checks are switched off'}[r.overall];
  const rows=['Obstacle checking','Airspace checking'].map(gr=>`<h4>${gr}</h4>`+r.cats.filter(c=>c.group===gr).map(c=>{
    const items=c.items.slice(0,full?8:4).map(it=>`<li class="${it.sev}">${it.lat!=null?`<button type="button" class="lnk" data-act="sfair" data-lat="${it.lat}" data-lng="${it.lng}">${esc2(it.text)}</button>`:it.i!=null?`<button type="button" class="lnk" data-act="sfgoto" data-i="${it.i}">${esc2(it.text)}</button>`:esc2(it.text)}</li>`).join('');
    const more=c.items.length>(full?8:4)?`<li class="more">+ ${c.items.length-(full?8:4)} more</li>`:'';
    return `<div class="sfrow ${c.status}"><i></i><div><b>${c.label}</b><span>${SF_LBL[c.status]}${c.msg?' · '+esc2(c.msg):''}</span>${items||more?`<ul>${items}${more}</ul>`:''}</div></div>`}).join('')).join('');
  const camRows=r.cam.length?`<h4>Camera and photo checks</h4><ul class="sfcam">${r.cam.map(c=>`<li class="${c.sev}">${esc2(c.text)}</li>`).join('')}</ul>`:'';
  const opts=`<option value="">Other / not listed</option>`+COUNTRIES.map(c=>`<option value="${c[0]}" ${c[0]===cc?'selected':''}>${c[1]}</option>`).join('');
  const links=`<div class="sfmap"><label for="sf-cc">Official drone map</label><select id="sf-cc" data-cc="1">${opts}</select>
    <div class="row">${co?`<a class="btn" href="${co[2]}" target="_blank" rel="noopener">Open ${co[1]} drone map</a>`:''}<a class="btn" href="${EASA_NAA}" target="_blank" rel="noopener">${co?'EASA: national authorities':'EASA: find your national authority'}</a></div>
    <small>${sf.ccAuto&&!sf.cc?'Country detected from the map position. ':''}Links come from a third-party list of national maps; confirm on your aviation authority's site. This app does not use official airspace data.</small></div>`;
  return `<div class="sfall ${r.overall}"><b>${head}</b><span>Planning aid only. A green result is not proof that the flight is legal or free of obstacles. Check current official information and look at the site yourself.</span></div>${rows}${camRows}${links}`;
}
function safetyOverlay(plan){
  if(S.step<3||!S.area)return'';
  const sf=S.safe,r=safetyEval(plan);let h='';
  const col=id=>SF_COL[r.obsSev[id]]||SF_COL.none;
  const shapes=sf.obs.st==='ready'?sf.obs.list.filter(x=>(x.cat==='bt'?sf.o.obst:sf.o.lines)).slice(0,1500):[];
  for(const ob of shapes){const c=col(ob.id),hit=!!r.obsSev[ob.id],op=hit?.45:.22,pts=ob.ll.map(pt).join(' ');
    if(ob.kind==='poly')h+=`<polygon points="${pts}" fill="${c}" fill-opacity="${op}" stroke="${c}" stroke-width="${hit?2:1}"/>`;
    else if(ob.kind==='line')h+=`<polyline points="${pts}" fill="none" stroke="${c}" stroke-opacity="${hit?1:.6}" stroke-width="${hit?3:1.5}" stroke-dasharray="${ob.wire?'6 3':''}"/>`;
    else{const q=toScreen(ob.ll[0]);h+=`<circle cx="${q.x.toFixed(1)}" cy="${q.y.toFixed(1)}" r="${hit?5:3}" fill="${c}" fill-opacity="${hit?.9:.5}" stroke="#fff" stroke-width="1"/>`}}
  for(const m of sf.manual){const q=toScreen(m),mpp=156543.03392*Math.cos(m.lat*RAD)/2**V.zoom,rp=Math.max(6,(+m.r||0)/mpp),c=SF_COL[r.obsSev[m.id]]||'#7048e8';
    h+=`<circle cx="${q.x.toFixed(1)}" cy="${q.y.toFixed(1)}" r="${rp.toFixed(1)}" fill="${c}" fill-opacity=".3" stroke="${c}" stroke-width="2"/><text x="${q.x.toFixed(1)}" y="${(q.y-rp-4).toFixed(1)}" font-size="10" font-family="IBM Plex Mono,monospace" text-anchor="middle" fill="#111" stroke="#fff" stroke-width="3" paint-order="stroke">${fmt(+m.h||0,0)} m</text>`}
  for(const a of r.airHits){
    if(a.kind==='aerodrome'||a.kind==='heliport'){const q=toScreen(a),mpp=156543.03392*Math.cos(a.lat*RAD)/2**V.zoom,R=(a.kind==='aerodrome'?AIR_R.aerodrome:AIR_R.heliport)/mpp;
      h+=`<circle cx="${q.x.toFixed(1)}" cy="${q.y.toFixed(1)}" r="${R.toFixed(1)}" fill="#e03131" fill-opacity=".07" stroke="#e03131" stroke-width="2" stroke-dasharray="10 6"/><circle cx="${q.x.toFixed(1)}" cy="${q.y.toFixed(1)}" r="5" fill="#e03131" stroke="#fff" stroke-width="1.5"/>`}
    else{const a1=toScreen({lat:a.bb.n,lng:a.bb.w}),a2=toScreen({lat:a.bb.s,lng:a.bb.e});
      h+=`<rect x="${a1.x.toFixed(1)}" y="${a1.y.toFixed(1)}" width="${Math.max(2,a2.x-a1.x).toFixed(1)}" height="${Math.max(2,a2.y-a1.y).toFixed(1)}" fill="#e03131" fill-opacity=".1" stroke="#e03131" stroke-width="2" stroke-dasharray="10 6"/>`}}
  return h;
}
function safetySection(){
  const sf=S.safe,o=sf.o;
  const tg=(k,l)=>`<label class="tg"><input type="checkbox" data-so="${k}" ${o[k]?'checked':''}><span>${l}</span><em>${o[k]?'ON':'OFF'}</em></label>`;
  const man=sf.manual.map((m,i)=>`<div class="mrow"><b>Obstacle ${i+1}</b><label>Height (m)<input type="number" min="0" max="500" step="1" data-mf="h" data-mi="${i}" value="${m.h}"></label><label>Radius (m)<input type="number" min="0" max="200" step="1" data-mf="r" data-mi="${i}" value="${m.r}"></label><button type="button" class="btn" data-act="sfdel" data-i="${i}" aria-label="Remove obstacle ${i+1}">Remove</button></div>`).join('');
  return `<div class="sect sfsec"><h3>Safety &amp; Airspace Check</h3>
   <p class="note">Planning preview. This check runs for every mission and is repeated on the summary page.</p>
   <div class="tgs"><h4>Obstacle checking</h4>${tg('terrain','Check terrain elevation')}${tg('obst','Check buildings and trees')}${tg('lines','Check power lines and towers')}
   <h4>Airspace checking</h4>${tg('restr','Check restricted zones')}${tg('airport','Check airport zones')}${tg('temp','Check temporary restrictions')}</div>
   <div class="fields">
    <div class="field"><label for="f-margin">Obstacle clearance margin (m)</label><input id="f-margin" data-sm="margin" type="number" min="0" max="200" step="1" value="${sf.margin}"><small>Illustrative configurable planning margin, not a legally defined safe distance.</small></div>
    <div class="field"><label for="f-oh">Tallest obstacle (m above ground)</label><input id="f-oh" data-sm="obstH" type="number" min="0" max="500" step="1" placeholder="none" value="${sf.obstH}"><small>Buildings, trees or structures in or near the area.</small></div>
   </div>
   ${man}
   <div class="row"><button type="button" class="btn ${S.obsMode?'on':''}" data-act="sfobs">${S.obsMode?'Click the map… (Esc to cancel)':'Add obstacle on map'}</button><button type="button" class="btn" data-act="sfload">Check again</button></div>
   <div id="safe"></div>
   <div class="legend"><span><i style="background:#2b8a3e"></i>No known conflict</span><span><i style="background:#f08c00"></i>Potential</span><span><i style="background:#e03131"></i>Known conflict or prohibited area</span><span><i style="background:#868e96"></i>Not checked</span></div></div>`;
}
/* ---------- Battery mission planner (automatic split into flights) ---------- */
const WIND_RES={mini4:10.7,mini5:12,air3s:12,mavic3:12}; /* m/s, DJI spec sheets (Mavic 3 from memory) */
const FLIGHT_COL=['#1c7ed6','#7048e8','#0ca678','#c2255c','#1098ad','#4263eb','#74b816','#ae3ec9'];
const CLIMB=3,DESC=3,OVH=15,TURN_S=4; /* m/s, m/s, s fixed take-off + landing handling, s per line end */
const fm=sec=>fmt(sec/60,1);
const bpRated=()=>drone().flight;
const bpUsableAuto=()=>Math.max(3,bpRated()*(S.bp.cap-S.bp.reserve)/100);
const bpUsable=()=>S.bp.usableMan!=null?S.bp.usableMan:bpUsableAuto();
const bpWindLimitKmh=()=>(WIND_RES[S.droneId]||10.7)*3.6;
/* wind raises battery drain: 1 at no wind, about 1.9 at the drone's wind limit (planning assumption) */
const bpWindF=()=>1+.9*Math.pow(Math.min(Math.max(S.bp.wind,0)/bpWindLimitKmh(),1.5),2);
const bpAreaM2=()=>S.bp.areaMan!=null?S.bp.areaMan:geo().area;
function flightOf(i){const p=currentPlan(),sp=p&&S.s?splitPlan(p):null;return sp?sp.flights.find(f=>i>=f.a&&i<f.b):null}
function splitPlan(p){
  const bp=S.bp,s=S.s,d=drone(),g=geo(),sh=p.shots,n=sh.length;
  const key=JSON.stringify([bp.cap,bp.reserve,bp.usableMan,bp.wind,bp.swap,bp.lmode,bp.launch,S.droneId,s.speed,terrKey(),S.terr.ver,S.terr.status,S.terr.src,S.terr.plane,S.tf]);
  if(bp.cache&&bp.cache.p===p&&bp.cache.key===key)return bp.cache.r;
  const ctx=terrCtx(p),vt=Math.min(s.speed+2,d.maxMap),wf=bpWindF(),usable=bpUsable(),Bmax=usable*60/wf;
  const ll=(x,y)=>({lat:g.lat0+y/g.my,lng:g.lng0+x/g.mx});
  /* prefix sums of travel time along the route, line ends, and units that may be cut apart */
  const tr=new Float64Array(n),segStart=new Uint8Array(n),SX=new Float64Array(n+1),SY=new Float64Array(n+1);
  for(let i=0;i<n;i++){SX[i+1]=SX[i]+sh[i].x;SY[i+1]=SY[i]+sh[i].y}
  for(let i=1;i<n;i++){const a=sh[i-1],b=sh[i];tr[i]=tr[i-1]+Math.hypot(b.x-a.x,b.y-a.y,b.h-a.h)/Math.max(b.speed,.1)}
  p.segs.forEach(sg=>{segStart[sg.s0]=1});
  const cs=new Int32Array(n+1);for(let i=0;i<n;i++)cs[i+1]=cs[i]+segStart[i];
  const units=[];let nxt=0;
  p.segs.forEach(sg=>{if(sg.s0===nxt){units.push({a:sg.s0,b:sg.s0+sg.n});nxt=sg.s0+sg.n}});
  for(let i=nxt;i<n;i++)units.push({a:i,b:i+1});
  units.forEach(u=>{let m=-1e9;for(let i=u.a;i<u.b;i++)m=Math.max(m,sh[i].h);u.mx=m});
  const groundRel=pt=>ctx.have?ctx.rel(pt.lat,pt.lng):0;
  const wms=Math.max(S.bp.wind,0)/3.6,vb=Math.max(vt-wms,1.5); /* return leg flies into the wind at worst */
  /* if low battery forces return-to-home at ANY waypoint, the drone must still land with the reserve: check every waypoint */
  const timeFor=(a,b,mx,L)=>{
    const f=sh[a],gl=groundRel(L),rth=clamp(mx-gl+20,60,500);
    const out=Math.max(20,f.h-gl)/CLIMB+Math.hypot(f.x-L.x,f.y-L.y)/vt;
    const backAt=i=>{const q=sh[i];return Math.max(0,rth-(q.h-gl))/CLIMB+Math.hypot(q.x-L.x,q.y-L.y)/vb+rth/DESC+8};
    let peak=0,pi=a;
    for(let i=a;i<b;i++){const t=OVH+out+tr[i]-tr[a]+TURN_S*(cs[i+1]-cs[a])+backAt(i);if(t>peak){peak=t;pi=i}}
    const back=backAt(b-1),surv=tr[b-1]-tr[a]+TURN_S*(cs[b]-cs[a]);
    return{out,back,surv,air:OVH+out+surv+back,peak,pi,rth,gl};
  };
  const sc=n?{x:SX[n]/n,y:SY[n]/n}:{x:0,y:0},sq=ll(sc.x,sc.y),one=bp.launch.all?(()=>{const q=loc(bp.launch.all.lat,bp.launch.all.lng);return{x:q.x,y:q.y,lat:bp.launch.all.lat,lng:bp.launch.all.lng}})():{x:sc.x,y:sc.y,lat:sq.lat,lng:sq.lng};
  const autoL=(a,b)=>{if(bp.lmode==='single')return one;const f=sh[a],l=sh[b-1],c=bp.lmode==='center',x=c?(SX[b]-SX[a])/(b-a):(f.x+l.x)/2,y=c?(SY[b]-SY[a])/(b-a):(f.y+l.y)/2,q=ll(x,y);return{x,y,lat:q.lat,lng:q.lng}};
  const greedy=B=>{
    const out=[];let a=null,b=null,mx=0;
    for(const u of units){
      if(a==null){a=u.a;b=u.b;mx=u.mx;continue}
      const nm=Math.max(mx,u.mx);
      if(timeFor(a,u.b,nm,autoL(a,u.b)).peak<=B){b=u.b;mx=nm}
      else{out.push({a,b});a=u.a;b=u.b;mx=u.mx}
    }
    if(a!=null)out.push({a,b});return out;
  };
  let ranges=n?greedy(Bmax):[];
  if(ranges.length>1){ /* balance: smallest budget that still needs the same number of flights */
    const N0=ranges.length;let lo=Bmax*.2,hi=Bmax;
    for(let k=0;k<22;k++){const mid=(lo+hi)/2;if(greedy(mid).length<=N0)hi=mid;else lo=mid}
    ranges=greedy(hi);
  }
  const cuts=JSON.stringify(ranges.map(r=>r.a));
  if(bp.cuts!==cuts){bp.cuts=cuts;const all=bp.launch.all;bp.launch={};if(all)bp.launch.all=all}
  const rated=bpRated();
  const flights=ranges.map((r,k)=>{
    const cu=bp.lmode==='single'?bp.launch.all:bp.launch[k],auto=!cu;let L;
    L=bp.lmode==='single'?one:cu?(()=>{const q=loc(cu.lat,cu.lng);return{x:q.x,y:q.y,lat:cu.lat,lng:cu.lng}})():autoL(r.a,r.b);
    let mx=-1e9,photos=0,far=0,dist=0;
    for(let i=r.a;i<r.b;i++){mx=Math.max(mx,sh[i].h);if(sh[i].action==='photo')photos++;far=Math.max(far,Math.hypot(sh[i].x-L.x,sh[i].y-L.y));if(i>r.a)dist+=Math.hypot(sh[i].x-sh[i-1].x,sh[i].y-sh[i-1].y,sh[i].h-sh[i-1].h)}
    const t=timeFor(r.a,r.b,mx,L),energy=t.air*wf,used=energy/(rated*60)*100,left=bp.cap-used,peakE=t.peak*wf,tight=bp.cap-peakE/(rated*60)*100;
    const abs=ctx.have?groundAt(L.lat,L.lng):ctx.T;
    return{k:k+1,a:r.a,b:r.b,n:r.b-r.a,photos,L,auto,far,dist,out:t.out,surv:t.surv,back:t.back,air:t.air,energy,used,left,peakE,tight,pi:t.pi,rth:t.rth,dh:-t.gl,elev:abs,over:peakE>usable*60+1,col:FLIGHT_COL[k%FLIGHT_COL.length]};
  });
  const air=flights.reduce((a,f)=>a+f.air,0),energy=flights.reduce((a,f)=>a+f.energy,0);
  /* quick estimate from area alone, for comparison */
  const m=p.m,fixed=OVH+60,perFlight=Math.max(0,Bmax-fixed)*s.speed*m.lineSp*(s.pattern==='double'?.5:1)*.85;
  const quick=perFlight>0?Math.max(1,Math.ceil(bpAreaM2()/perFlight)):null;
  const r={flights,air,energy,wf,usable,Bmax,vt,quick,swapSec:Math.max(0,flights.length-1)*bp.swap*60,photos:flights.reduce((a,f)=>a+f.photos,0),dist:flights.reduce((a,f)=>a+f.dist,0)};
  r.onSite=air+r.swapSec;
  bp.cache={p,key,r};return r;
}
function bpWarnings(sp){
  const w=[],s=S.s,wl=bpWindLimitKmh(),bw=S.bp.wind;
  if(bw>=wl)w.push(`Wind of ${bw} km/h is at or above this drone's limit (${fmt(wl,0)} km/h). Do not fly.`);
  else if(bw>wl*.7)w.push(`Wind of ${bw} km/h is close to this drone's limit (${fmt(wl,0)} km/h). Expect much shorter flights and rough footage.`);
  if(bw/3.6>s.speed)w.push(`Wind (${fmt(bw/3.6,1)} m/s) is stronger than the survey speed (${fmt(s.speed,1)} m/s). Ground speed and photo spacing will vary.`);
  sp.flights.forEach(f=>{
    if(f.over)w.push(`Mission ${f.k} needs ${fm(f.peakE)} min of battery (including a return home from its tightest point) but only ${fmt(sp.usable,1)} min is usable. A single line or point group is too long; lower the reserve, move the take-off point closer or shorten the area.`);
    if(f.far>500)w.push(`Mission ${f.k}: the farthest waypoint is ${fmt(f.far,0)} m from its take-off point. Keep the aircraft in sight, or move the take-off point closer.`);
  });
  if(S.bp.usableMan!=null&&Math.abs(S.bp.usableMan-bpUsableAuto())>.05)w.push('You set the usable time by hand, so battery capacity and reserve no longer change it.');
  return w;
}
function bpFlightHtml(f,full){
  const nm=sp=>sp;
  return `<div class="fcard" style="--fc:${f.col}"><div class="fh"><i></i><b>Mission ${f.k}</b><span>Battery ${f.k}</span>${f.over?'<em>over budget</em>':''}</div>
   <div class="fgrid"><div><span>Waypoints</span><b class="num">${f.a+1} to ${f.b}</b></div><div><span>Photos</span><b class="num">${fmt(f.photos)}</b></div><div><span>Air time</span><b class="num">${fm(f.air)} min</b></div><div><span>Lands with</span><b class="num">≈ ${fmt(Math.max(f.left,0),0)} %</b></div></div>
   <div class="fsub num">Take-off and climb ${fm(f.out)} · survey ${fm(f.surv)} · return and landing ${fm(f.back)} min</div>
   <div class="fsub num">Tightest point if low battery sent the drone home: Waypoint ${f.pi+1}. It would still land with ≈ ${fmt(Math.max(f.tight,0),0)} % (reserve ${S.bp.reserve} %).</div>
   <div class="fsub num">Take-off and landing: ${f.L.lat.toFixed(5)}, ${f.L.lng.toFixed(5)}${f.elev!=null&&terrAvail()?` · ${fmt(f.elev,0)} m`:''} · ${f.auto?(S.bp.lmode==='single'?'same point for every mission':'auto'):'set by you'} · farthest waypoint ${fmt(f.far,0)} m</div>
   <div class="row"><button type="button" class="btn" data-act="bpshow" data-i="${f.k-1}">Show</button><button type="button" class="btn ${S.launchPick===f.k-1?'on':''}" data-act="bplaunch" data-i="${f.k-1}">${S.launchPick===f.k-1?'Click the map… (Esc)':'Set take-off point'}</button>${f.auto?'':`<button type="button" class="btn" data-act="bpauto" data-i="${f.k-1}">Auto point</button>`}${full?`<button type="button" class="btn primary" data-act="dlflight" data-i="${f.k}" data-needack="1" ${S.safe.ack?'':'disabled'}>Download mission ${f.k}</button>`:''}</div></div>`;
}
function bpHtml(sp,full){
  const n=sp.flights.length,wp=Math.round((sp.wf-1)*100);
  const tot=`<div class="bptot"><div><span>Missions = batteries</span><b class="num">${n}</b></div><div><span>Total flight time</span><b class="num">${fm(sp.air)} min</b></div><div><span>Total on site</span><b class="num">${fm(sp.onSite)} min</b></div><div><span>Photos</span><b class="num">${fmt(sp.photos)}</b></div></div>
   <p class="note">Total flight time covers take-off, flying and taking pictures, return and landing for all ${n} mission${n>1?'s':''}. Photos are taken while flying, so they add no time. Total on site adds ${n>1?(n-1)+' battery change'+(n>2?'s':'')+' of '+S.bp.swap+' min':'no battery change'}.${wp>0?` Wind adds about ${wp} % battery use.`:''}</p>`;
  const q=sp.quick!=null?`<p class="note">Quick estimate from the area alone: about ${sp.quick} mission${sp.quick>1?'s':''}. ${sp.quick!==n?`The route-based split above (${n}) is the one to use, because it counts line turns, climbs, terrain, wind and the take-off and landing points.`:'It matches the route-based split.'}</p>`:'';
  const w=bpWarnings(sp).map(t=>`<div class="warn">${t}</div>`).join('');
  return tot+sp.flights.map(f=>bpFlightHtml(f,full)).join('')+q+w;
}
function bpSection(){
  const bp=S.bp,d=drone(),g=geo(),ha=bp.unit==='ha',aV=ha?bpAreaM2()/10000:bpAreaM2();
  return `<div class="sect bpsec"><h3>Battery mission planner</h3>
   <div class="calc"><div><span>Drone</span><b>${d.name}</b></div><div><span>Rated flight time</span><b class="num">${d.flight} min</b></div><div><span>Wind limit</span><b class="num">${fmt(WIND_RES[d.id]||10.7,1)} m/s · ${fmt(bpWindLimitKmh(),0)} km/h</b></div></div>
   <div class="fields">
    <div class="field full"><label for="bp-cap">Battery capacity available for mission <output id="bp-cap-o">${bp.cap} %</output></label><input id="bp-cap" data-bp="cap" type="range" min="20" max="100" step="1" value="${bp.cap}"><small>100 % = fully charged at take-off.</small></div>
    <div class="field full"><label for="bp-res">Landing reserve <output id="bp-res-o">${bp.reserve} %</output></label><input id="bp-res" data-bp="reserve" type="range" min="10" max="40" step="1" value="${bp.reserve}"><small>Battery that must remain when the drone lands.</small></div>
    <div class="field"><label for="bp-use">Usable flight time (min)</label><input id="bp-use" data-bp="usable" type="number" min="3" max="120" step="0.5" value="${fmt(bpUsable(),1)}"><small id="bp-use-h"></small></div>
    <div class="field"><label for="bp-wind">Wind (km/h)</label><input id="bp-wind" data-bp="wind" type="number" min="0" max="80" step="1" value="${bp.wind}"><small id="bp-wind-h"></small></div>
    <div class="field"><label for="bp-area">Survey area</label><div class="ig"><input id="bp-area" data-bp="area" type="number" min="0" step="${ha?.01:1}" value="${ha?fmt(aV,2).replace(/,/g,''):Math.round(aV)}"><button type="button" class="btn unit" data-act="bpunit" aria-label="Switch between hectares and square metres">${ha?'ha':'m²'}</button></div><small>${bp.areaMan!=null?'Changed by you. Used only for the quick estimate. <button type="button" class="lnk" data-act="bparea">Use drawn area</button>':'From the drawn area. Used for the quick estimate only.'}</small></div>
    <div class="field full"><label for="bp-lm">Take-off and landing point</label><select id="bp-lm" data-bpm="lmode"><option value="single" ${bp.lmode==='single'?'selected':''}>One point for all missions (each continues where the last ended)</option><option value="center" ${bp.lmode==='center'?'selected':''}>Centre of each section (keeps the drone closer)</option><option value="ends" ${bp.lmode==='ends'?'selected':''}>Between first and last waypoint (shortest transit)</option></select><small>You can also set the point on the map.</small></div>
    <div class="field"><label for="bp-swap">Battery change (min)</label><input id="bp-swap" data-bp="swap" type="number" min="0" max="30" step="1" value="${bp.swap}"><small>Adds to total time on site.</small></div>
   </div>
   <div id="bp"></div>
   <p class="note">Each mission is cut between line ends and balanced so the batteries are used evenly. Mission 2 continues with the line after mission 1's last line, taking off from the same point (landing point of mission 1) unless you choose another mode. At every waypoint the app checks that, if low battery sent the drone home right there, it would still land with your reserve. Times are estimates: climb ${CLIMB} m/s, ${TURN_S} s per line end, return at ${'the transit speed'}, wind as extra battery use.</p></div>`;
}
function bpRefresh(){
  const p=currentPlan();if(!p||S.step!==3)return;
  const sp=splitPlan(p),bp=S.bp;
  const e=$('#bp');if(e)e.innerHTML=bpHtml(sp,false);
  const u=$('#bp-use-h');if(u)u.textContent=bp.usableMan!=null?`Set by hand. Auto: ${fmt(bpUsableAuto(),1)} min`:`Auto: ${bpRated()} × ${bp.cap-bp.reserve} % = ${fmt(bpUsableAuto(),1)} min`;
  const w=$('#bp-wind-h');if(w)w.textContent=bp.wind>0?`Battery use +${Math.round((bpWindF()-1)*100)} % (planning estimate)`:'No wind';
}
function setLaunch(l){
  const k=S.launchPick;if(k==null)return;const g=fromScreen(l.x,l.y);
  if(S.bp.lmode==='single')S.bp.launch.all={lat:g.lat,lng:g.lng};else S.bp.launch[k]={lat:g.lat,lng:g.lng};S.launchPick=null;S.bp.cache=null;setLaunchBanner();bpUpdate();
}
function setLaunchBanner(){
  const bn=$('#banner');
  if(S.launchPick!=null){bn.textContent=`Click the map to set the take-off and landing point for Mission ${S.launchPick+1} · Esc to cancel`;bn.hidden=false;mapEl.classList.add('drawing')}
  else{mapEl.classList.toggle('drawing',!!S.mode||S.obsMode);if(S.obsMode){bn.textContent='Click the map to place an obstacle · Esc to cancel';bn.hidden=false}else updateBanner()}
}
function bpUpdate(){if(S.step===3){currentPlan();updateCalc();drawOverlay()}else if(S.step>=4)renderPanel(true);else drawOverlay()}
function bpOverlay(plan){
  if(S.step<3)return'';const sp=splitPlan(plan);if(!sp.flights.length)return'';
  const multi=sp.flights.length>1,sh=plan.shots;let h='';
  for(const f of sp.flights){
    const P=sh.slice(f.a,f.b).map(pt);if(!P.length)continue;
    const A=P[0].split(','),Z=P[P.length-1].split(','),L=toScreen(f.L);
    if(multi)h+=`<polyline points="${P.join(' ')}" fill="none" stroke="${f.col}" stroke-width="3.4" stroke-linejoin="round" stroke-linecap="round" opacity=".95"/>`;
    h+=`<polyline points="${A.join(',')} ${L.x.toFixed(1)},${L.y.toFixed(1)} ${Z.join(',')}" fill="none" stroke="${f.col}" stroke-width="2" stroke-dasharray="6 5" opacity=".9"/>`;
    if(S.bp.lmode==='single'){if(f.k===1)h+=`<g transform="translate(${L.x.toFixed(1)},${L.y.toFixed(1)})"><circle r="12" fill="#212529" stroke="#fff" stroke-width="2.5"/><text y="4" text-anchor="middle" font-size="12" font-weight="700" font-family="IBM Plex Mono,monospace" fill="#fff">H</text></g>`}
    else h+=`<g transform="translate(${L.x.toFixed(1)},${L.y.toFixed(1)})"><circle r="11" fill="${f.col}" stroke="#fff" stroke-width="2.5"/><text y="3.8" text-anchor="middle" font-size="11" font-weight="700" font-family="IBM Plex Mono,monospace" fill="#fff">${multi?f.k:'H'}</text></g>`;
  }
  return h;
}
const bearingOf=(dx,dy)=>((90-Math.atan2(dy,dx)/RAD)%360+360)%360;
function makePlan(s,g,d,edits,tfc){
  const m=metrics(s,g,d),wps=[],segs=[];let bearing=null;
  const addOrbit=(h,R,ang0,dir,pitch,spacing,nmin,nmax)=>{
    const n=clamp(Math.ceil(2*Math.PI*R/spacing),nmin,nmax);
    for(let k=0;k<n;k++){const a=ang0+dir*k*2*Math.PI/n;
      wps.push({x:g.c.x+R*Math.cos(a),y:g.c.y+R*Math.sin(a),h,pitch,kind:'orbit'})}
  };
  if(s.pattern==='circle'){
    const R=g.Rorb;
    ringHeights(s).forEach((h,ri)=>{
      const D=Math.hypot(h,R),sp=D*K_ACROSS*(1-s.front/100);
      addOrbit(h,R,0,ri%2?-1:1,-Math.round(Math.atan2(h,R)/RAD),sp,8,150);
    });
  }else{
    const theta=s.bearing==null||s.bearing===''?bestTheta(g):(90-s.bearing)*RAD;
    bearing=((90-theta/RAD)%180+180)%180;
    let list=gridPass(g,theta,m.lineSp);
    if(s.pattern==='double'){
      let l2=gridPass(g,theta+Math.PI/2,m.lineSp);
      if(list.length&&l2.length){const e=list[list.length-1].b;
        if(Math.hypot(e.x-l2[0].a.x,e.y-l2[0].a.y)>Math.hypot(e.x-l2[l2.length-1].b.x,e.y-l2[l2.length-1].b.y))
          l2=l2.reverse().map(sg=>({a:sg.b,b:sg.a}))}
      list=list.concat(l2);
    }
    list.forEach(sg=>{const i0=wps.length,len=Math.hypot(sg.b.x-sg.a.x,sg.b.y-sg.a.y);
      wps.push({x:sg.a.x,y:sg.a.y,h:s.height,pitch:s.pitch,kind:'grid'});wps.push({x:sg.b.x,y:sg.b.y,h:s.height,pitch:s.pitch,kind:'grid'});
      const n=Math.max(2,Math.ceil(len/m.photoSp)+1);segs.push({i0,i1:i0+1,len,n,step:len/(n-1)})});
    const p=Math.abs(s.oblPitch||70);
    for(let i=0;i<(s.obl||0);i++){
      const h=clamp(Math.round(s.height*(1+.4*i)),10,120),R=g.R+h/Math.tan(p*RAD),D=h/Math.sin(p*RAD);
      const last=wps[wps.length-1],ang0=last?Math.atan2(last.y-g.c.y,last.x-g.c.x):0;
      addOrbit(h,R,ang0,i%2?-1:1,-p,D*K_ACROSS*(1-s.front/100),8,400);
    }
  }
  wps.forEach(w=>Object.assign(w,g.toLL(w.x,w.y)));
  /* waypoints: every place where the drone stops or fires, in flight order, with its own altitude, speed, gimbal, heading and action */
  const shots=[];
  segs.forEach((sg,si)=>{const a=wps[sg.i0],b=wps[sg.i1],hd=bearingOf(b.x-a.x,b.y-a.y);sg.s0=shots.length;
    for(let k=0;k<sg.n;k++){const t=k/(sg.n-1);
      shots.push({lat:a.lat+(b.lat-a.lat)*t,lng:a.lng+(b.lng-a.lng)*t,x:a.x+(b.x-a.x)*t,y:a.y+(b.y-a.y)*t,h:s.height,pitch:s.pitch,speed:s.speed,heading:hd,action:'photo',kind:'grid',seg:si})}});
  wps.forEach(w=>{if(w.kind==='orbit')shots.push({lat:w.lat,lng:w.lng,x:w.x,y:w.y,h:w.h,pitch:w.pitch,speed:s.speed,heading:bearingOf(g.c.x-w.x,g.c.y-w.y),action:'photo',kind:'orbit'})});
  /* terrain: ground elevation under each waypoint, and the height above ground (AGL) the drone really has there */
  const tfOn=!!(tfc&&tfc.ready);let T=0;
  shots.forEach(x=>{x.nom=x.h});
  if(tfOn){
    shots.forEach(x=>{x.gz=groundAt(x.lat,x.lng)});
    T=tfc.takeoff!=null&&tfc.takeoff!==''?+tfc.takeoff:(shots.length?shots[0].gz:0);
    if(tfc.mode==='follow')shots.forEach(x=>{x.h=Math.round((x.gz+x.nom-T)*10)/10});
  }
  let nEdited=0;
  shots.forEach((x,i)=>{x.def={h:x.h,speed:x.speed,pitch:x.pitch,heading:x.heading,action:x.action};
    const e=edits&&edits[i];if(e){Object.assign(x,e);x.edited=true;x.hdSet=e.heading!=null;nEdited++}});
  let tf=null;
  if(tfOn&&shots.length){
    let minA=1e9,maxA=-1e9,gMin=1e9,gMax=-1e9,rmin=1e9,rmax=-1e9,flagged=0,minRel=1e9,steep=0,vmax=0;
    shots.forEach((x,i)=>{x.agl=T+x.h-x.gz;x.tfFlag=Math.abs(x.agl-x.nom)/x.nom*100>tfc.tol+1e-9;if(x.tfFlag)flagged++;
      minA=Math.min(minA,x.agl);maxA=Math.max(maxA,x.agl);gMin=Math.min(gMin,x.gz);gMax=Math.max(gMax,x.gz);
      const r=x.agl/x.nom;rmin=Math.min(rmin,r);rmax=Math.max(rmax,r);minRel=Math.min(minRel,x.h);
      const q=shots[i-1];if(q&&(x.kind==='orbit'||q.seg===x.seg)){const dh=Math.abs(x.h-q.h),dx=Math.hypot(x.x-q.x,x.y-q.y);
        if(dx>.5){steep=Math.max(steep,dh/dx*100);vmax=Math.max(vmax,x.speed*dh/Math.hypot(dx,dh))}}});
    tf={mode:tfc.mode,follow:tfc.mode==='follow',tol:tfc.tol,T,auto:tfc.takeoff==null||tfc.takeoff==='',gzMin:gMin,gzMax:gMax,minAgl:minA,maxAgl:maxA,rmin,rmax,flagged,minRel,steep,vmax};
  }
  const photos=shots.filter(x=>x.action==='photo').length;
  let dist=0,move=0;
  for(let i=1;i<shots.length;i++){const a=shots[i-1],b=shots[i],d3=Math.hypot(b.x-a.x,b.y-a.y,b.h-a.h);dist+=d3;move+=d3/Math.max(b.speed,.1)}
  const maxH=Math.max(...shots.map(x=>x.h),0);
  const timeSec=move+segs.length*4+30+maxH*2/3;
  const batteries=Math.max(1,Math.ceil(timeSec/60/Math.max(s.battMin,1)));
  const mb=s.res==='12'?5:s.res==='20'?10:18;
  return{m,wps,segs,shots,dist,photos,timeSec,batteries,bearing,maxH,nEdited,tf,storageGB:photos*mb/1024};
}
let planCache={key:'',val:null};
function currentPlan(){
  if(!S.area||!S.droneId||!S.s)return null;
  const sig=JSON.stringify([S.area,S.s,S.droneId,S.missionId,S.tf,S.tfTol,S.takeoff,terrKey()]);
  if(S.editSig!==sig){S.editSig=sig;S.safe.ack=false;
    if(Object.keys(S.edits).length){S.edits={};S.sel=null;const dl=$('#wpdlg');if(dl)dl.hidden=true;toast('Waypoint edits were reset because the plan changed.')}}
  const key=sig+JSON.stringify(S.edits);
  if(planCache.key!==key)planCache={key,val:makePlan(S.s,geo(),drone(),S.edits,{mode:S.tf,tol:S.tfTol,takeoff:S.takeoff,ready:terrReady()})};
  return planCache.val;
}
let geoCache={key:'',val:null};
function geo(){const key=JSON.stringify(S.area);if(geoCache.key!==key)geoCache={key,val:analyse(S.area)};return geoCache.val}
const drone=()=>DRONES.find(d=>d.id===S.droneId);
const mission=()=>MISSIONS.find(m=>m.id===S.missionId);
function ensureSettings(){if(!S.area||!S.droneId||!S.missionId)return;if(!S.dirty||!S.s)S.s=recommend(drone(),mission(),geo())}

/* ---------- map rendering ---------- */
function renderTiles(){
  const L=LAYERS[S.layer],tz=clamp(Math.round(V.zoom),0,L.max),sc=2**(V.zoom-tz),ts=256*sc,n=2**tz;
  const x0=Math.floor(TL.x/ts),y0=Math.floor(TL.y/ts),x1=Math.floor((TL.x+V.w)/ts),y1=Math.floor((TL.y+V.h)/ts);
  const need=new Set();
  for(let y=y0;y<=y1;y++){if(y<0||y>=n)continue;
    for(let x=x0;x<=x1;x++){
      const key=S.layer+'/'+tz+'/'+x+'/'+y;need.add(key);
      let img=tiles.get(key);
      if(!img){const lay=S.layer;img=new Image();img.className='tile';img.draggable=false;img.alt='';
        img.onload=()=>{if(lay===S.layer){TS.ok++;checkTiles()}};
        img.onerror=()=>{img.classList.add('err');if(lay===S.layer){TS.err++;checkTiles()}};
        img.src=L.url(tz,((x%n)+n)%n,y);tilesEl.appendChild(img);tiles.set(key,img)}
      img.style.transform=`translate(${x*ts-TL.x}px,${y*ts-TL.y}px)`;img.style.width=img.style.height=(ts+.6)+'px';
    }}
  for(const [k,img] of tiles)if(!need.has(k)){img.remove();tiles.delete(k)}
}
const pt=p=>{const s=toScreen(p);return s.x.toFixed(1)+','+s.y.toFixed(1)};
function drawOverlay(){
  let h='';
  const area=S.area;
  if(area)h+=`<polygon points="${area.map(pt).join(' ')}" fill="var(--area)" fill-opacity=".16" stroke="var(--area)" stroke-width="2.5" stroke-linejoin="round"/>`;
  const plan=S.step>=3?currentPlan():null;
  if(plan)h+=safetyOverlay(plan);
  if(plan&&plan.wps.length){
    const line=plan.wps.map(pt).join(' ');
    h+=`<g opacity="${S.step===4?.28:1}">`;
    h+=`<polyline points="${line}" fill="none" stroke="#fff" stroke-width="5" stroke-linejoin="round" stroke-linecap="round" opacity=".9"/>`;
    h+=`<polyline points="${line}" fill="none" stroke="var(--route)" stroke-width="2.4" stroke-linejoin="round" stroke-linecap="round"/></g>`;
    if(S.step!==4)h+=bpOverlay(plan);
    const sh=plan.shots,N=sh.length;
    if(N&&S.step!==4){
      const P=sh.map(toScreen),dd=[];
      for(let i=1;i<N;i++){const d=Math.hypot(P[i].x-P[i-1].x,P[i].y-P[i-1].y);if(d>.3)dd.push(d)}
      dd.sort((x,y)=>x-y);const dm=dd.length?dd[dd.length>>1]:50;
      const r=dm>=10?3.8:dm>=6?2.8:dm>=3.5?1.8:0;
      const SR=safetyEval(plan).sevShot;
      sh.forEach((x,i)=>{const p=P[i];if(p.x<-20||p.y<-20||p.x>V.w+20||p.y>V.h+20)return;
        const sv=SR[i],rr=r||(x.edited||x.tfFlag||sv||i===S.sel?2.6:0),cx=p.x.toFixed(1),cy=p.y.toFixed(1);
        if(rr)h+=`<circle cx="${cx}" cy="${cy}" r="${rr}" fill="${ACT_COL[x.action]||ACT_COL.photo}" stroke="${x.tfFlag||sv==='bad'?'#e03131':sv==='warn'?'#f08c00':'#1b1b1b'}" stroke-opacity="${x.tfFlag||sv?1:.75}" stroke-width="${x.tfFlag||sv?2.6:1}"/>`;
        if(x.edited)h+=`<circle cx="${cx}" cy="${cy}" r="${rr+3.2}" fill="none" stroke="var(--area)" stroke-width="2"/>`;
        if(i===S.sel)h+=`<circle cx="${cx}" cy="${cy}" r="${rr+6.5}" fill="none" stroke="#fff" stroke-width="5"/><circle cx="${cx}" cy="${cy}" r="${rr+6.5}" fill="none" stroke="var(--route)" stroke-width="2.5"/>`;
      });
      const verbose=dm>=64,k=[1,2,5,10,20,25,50,100,200,500,1000].find(v=>v*dm>=(verbose?84:36))||1000;
      h+=`<g font-family="IBM Plex Mono,monospace" font-size="10" font-weight="500" text-anchor="middle" paint-order="stroke" stroke="#fff" stroke-width="3" stroke-linejoin="round" fill="#111">`;
      const placed=[],tryLabel=(n,force)=>{
        const p=P[n-1];if(p.x<-60||p.y<-20||p.x>V.w+60||p.y>V.h+20)return;
        const t=verbose||n-1===S.sel?`Waypoint ${n}`:`${n}`,w=t.length*6.2+8,x=p.x,y=p.y-(n-1===S.sel?14:7);
        if(!force&&!S.lblAll&&placed.some(q=>Math.abs(q.x-x)<(q.w+w)/2&&Math.abs(q.y-y)<13))return;
        placed.push({x,y,w});h+=`<text x="${x.toFixed(1)}" y="${y.toFixed(1)}">${t}</text>`};
      if(S.sel!=null)tryLabel(S.sel+1,true);
      tryLabel(1,N>0&&S.sel!==0);if(N>1)tryLabel(N,S.sel!==N-1);
      for(let n=2;n<N;n++){if(n-1===S.sel)continue;if(S.lblAll)tryLabel(n,true);else if(n%k===0)tryLabel(n,false)}
      h+='</g>';
    }
    const a=toScreen(plan.wps[0]),z=toScreen(plan.wps[plan.wps.length-1]);
    h+=`<g font-family="IBM Plex Mono,monospace" font-size="10" font-weight="500" text-anchor="middle"><line x1="${a.x}" y1="${a.y}" x2="${a.x-14}" y2="${a.y+14}" stroke="#fff" stroke-width="2"/><circle cx="${a.x-14}" cy="${a.y+14}" r="8" fill="var(--ok)" stroke="#fff" stroke-width="2"/><text x="${a.x-14}" y="${a.y+17.5}" fill="#fff">S</text><line x1="${z.x}" y1="${z.y}" x2="${z.x+14}" y2="${z.y+14}" stroke="#fff" stroke-width="2"/><circle cx="${z.x+14}" cy="${z.y+14}" r="8" fill="var(--bad)" stroke="#fff" stroke-width="2"/><text x="${z.x+14}" y="${z.y+17.5}" fill="#fff">E</text></g>`;
  }
  if(plan&&S.step>=4)h+=cpOverlay();
  const si=$('#shotinfo');
  if(S.step===4)cpInfo(si);
  else if(plan&&plan.shots.length){const n=plan.shots.length;si.hidden=false;
    si.innerHTML=`<i></i><b class="num">${fmt(n)}</b> waypoints <span>Waypoint 1 to ${fmt(n)} · ${fmt(plan.photos)} photos${plan.nEdited?` · ${plan.nEdited} edited`:''}</span><button type="button" data-lbl="1">Numbers: ${S.lblAll?'all':'auto'}</button>`}
  else si.hidden=true;
  positionDlg();
  const dr=S.draw;
  if(S.mode==='polygon'&&dr.pts.length){
    const pts=dr.pts.map(pt);if(dr.cur)pts.push(pt(dr.cur));
    h+=`<polyline points="${pts.join(' ')}" fill="var(--area)" fill-opacity=".12" stroke="var(--area)" stroke-width="2.5" stroke-dasharray="6 4"/>`;
    dr.pts.forEach((p,i)=>{const s=toScreen(p);h+=`<circle cx="${s.x}" cy="${s.y}" r="${i===0&&dr.pts.length>=3?8:5}" fill="#fff" stroke="var(--area)" stroke-width="2.5"/>`});
  }
  if((S.mode==='rect'||S.mode==='square')&&dr.a&&dr.cur){
    const q=rectPts(dr.a,dr.cur,S.mode==='square');
    if(q)h+=`<polygon points="${q.map(pt).join(' ')}" fill="var(--area)" fill-opacity=".14" stroke="var(--area)" stroke-width="2.5" stroke-dasharray="6 4"/>`;
  }
  if(area&&S.step===0&&!S.mode)area.forEach((p,i)=>{const s=toScreen(p);h+=`<circle class="vh" data-vi="${i}" cx="${s.x}" cy="${s.y}" r="7" fill="#fff" stroke="var(--area)" stroke-width="3"/>`});
  ov.innerHTML=h;
}
function updateReadouts(){
  const mpp=156543.03392*Math.cos(V.lat*RAD)/2**V.zoom,target=mpp*110;
  const pow=10**Math.floor(Math.log10(target)),nice=[1,2,5,10].map(k=>k*pow).filter(v=>v<=target).pop()||pow;
  $('#scaleBar').style.width=(nice/mpp).toFixed(0)+'px';
  $('#scaleTxt').textContent=nice>=1000?nice/1000+' km':nice+' m';
  $('#zl').textContent='z'+V.zoom.toFixed(1);
}
/* offline base map: ocean, land outlines and a degree grid, so the map is never blank */
function drawBase(){
  const st=BASE_STYLE[S.layer],dpr=window.devicePixelRatio||1,W=256*2**V.zoom;
  bctx.setTransform(dpr,0,0,dpr,0,0);bctx.fillStyle=st.sea;bctx.fillRect(0,0,V.w,V.h);
  const k0=Math.floor(TL.x/W),k1=Math.floor((TL.x+V.w)/W);
  const trace=ring=>{ring.forEach(([lng,lat],i)=>{const w=proj(lat,lng,V.zoom),x=w.x+(trace.k*W)-TL.x,y=w.y-TL.y;i?bctx.lineTo(x,y):bctx.moveTo(x,y)});bctx.closePath()};
  bctx.lineWidth=1;bctx.lineJoin='round';
  for(const [list,fill] of [[WORLD,st.land],[WATER,st.sea]]){
    bctx.fillStyle=fill;
    for(const ring of list)for(let k=k0;k<=k1;k++){trace.k=k;bctx.beginPath();trace(ring);bctx.fill();if(fill===st.land){bctx.strokeStyle=st.line;bctx.stroke()}}
  }
  const steps=[90,30,10,5,2,1,.5,.1,.05,.01,.005,.001],step=steps.find(s=>s*W/360>=80)||.001,dec=step>=1?0:Math.max(0,Math.ceil(-Math.log10(step)));
  const a=fromScreen(0,0),b=fromScreen(V.w,V.h);
  bctx.strokeStyle=st.grid;bctx.fillStyle=st.txt;bctx.font='10px IBM Plex Mono,monospace';bctx.lineWidth=1;bctx.beginPath();
  for(let g=Math.ceil(a.lng/step)*step,c=0;g<=b.lng&&c<80;g+=step,c++){const x=Math.round(proj(0,g,V.zoom).x-TL.x)+.5;bctx.moveTo(x,0);bctx.lineTo(x,V.h)}
  for(let g=Math.ceil(b.lat/step)*step,c=0;g<=a.lat&&c<80;g+=step,c++){const y=Math.round(proj(g,0,V.zoom).y-TL.y)+.5;bctx.moveTo(0,y);bctx.lineTo(V.w,y)}
  bctx.stroke();
  for(let g=Math.ceil(a.lng/step)*step,c=0;g<=b.lng&&c<80;g+=step,c++){const x=proj(0,g,V.zoom).x-TL.x;const L=((g+180)%360+360)%360-180;bctx.fillText(L.toFixed(dec)+'°',x+3,V.h-48)}
  for(let g=Math.ceil(b.lat/step)*step,c=0;g<=a.lat&&c<80;g+=step,c++){const y=proj(g,0,V.zoom).y-TL.y;bctx.fillText(g.toFixed(dec)+'°',70,y-3)}
}
function checkTiles(){
  const n=$('#notice');
  if(TS.ok>0){n.hidden=true;return}
  if(TS.err>=3){n.hidden=false;n.textContent='Map tiles are not loading in this view, so an offline world outline is shown. Open the standalone file in a normal browser tab for satellite and terrain imagery.'}
}
let saveT;
function saveSoon(){clearTimeout(saveT);saveT=setTimeout(()=>{try{localStorage.setItem('sfp-map',JSON.stringify({layer:S.layer,lat:V.lat,lng:V.lng,zoom:V.zoom}))}catch(e){}},400)}
function render(){updateTL();drawBase();renderTiles();drawOverlay();updateReadouts();saveSoon()}
function setView(lat,lng,zoom){V.lat=clamp(lat,-84,84);V.lng=lng;V.zoom=clamp(zoom,1,19.5);render()}
function zoomAt(sx,sy,nz){
  nz=clamp(nz,1,19.5);const geoP=fromScreen(sx,sy),w=proj(geoP.lat,geoP.lng,nz);
  const c=unproj(w.x-(sx-V.w/2),w.y-(sy-V.h/2),nz);V.lat=c.lat;V.lng=c.lng;V.zoom=nz;render();
}
function fitPoints(pts,pad=60){
  if(!pts.length)return;
  const w=pts.map(p=>proj(p.lat,p.lng,0)),xs=w.map(p=>p.x),ys=w.map(p=>p.y);
  const dx=Math.max(...xs)-Math.min(...xs),dy=Math.max(...ys)-Math.min(...ys);
  const availW=Math.max(V.w-pad*2,80),availH=Math.max(V.h-pad*2,80);
  const z=Math.min(dx>0?Math.log2(availW/dx):19,dy>0?Math.log2(availH/dy):19,19);
  const c=unproj((Math.min(...xs)+Math.max(...xs))/2,(Math.min(...ys)+Math.max(...ys))/2,0);
  setView(c.lat,c.lng,z);
}
function resize(){const r=mapEl.getBoundingClientRect(),dpr=window.devicePixelRatio||1;V.w=r.width;V.h=r.height;
  baseEl.width=Math.round(V.w*dpr);baseEl.height=Math.round(V.h*dpr);ov.setAttribute('viewBox',`0 0 ${V.w} ${V.h}`);render()}
new ResizeObserver(resize).observe(mapEl);

/* ---------- Control points: ground scale-check markers (v1.8) ---------- */
const CP_MF={ortho:1,area:1,stockpile:.8,earthworks:.8,dem:.9,dsm:.9,dtm:.75,building:.8},CP_CAP=30,CP_MAX=60;
let cpCache={key:'',val:null};
const cpH=(a,b)=>Math.hypot(a.x-b.x,a.y-b.y);
const cpFmtM=d=>d>=1000?(d/1000).toFixed(3)+' km':d.toFixed(1)+' m';
const cpFmtD=d=>d>=1000?(d/1000).toFixed(2)+' km':Math.round(d)+' m';
function cpSeg(p,a,b){const dx=b.x-a.x,dy=b.y-a.y,l=dx*dx+dy*dy;let t=l?((p.x-a.x)*dx+(p.y-a.y)*dy)/l:0;t=clamp(t,0,1);return Math.hypot(p.x-a.x-t*dx,p.y-a.y-t*dy)}
function cpRing(p,R,closed){let d=1e9;const n=closed?R.length:R.length-1;for(let i=0;i<n;i++)d=Math.min(d,cpSeg(p,R[i],R[(i+1)%R.length]));return d}
function cpCtx(){
  const plan=currentPlan();if(!plan||!S.s||!S.area)return null;
  const g=geo(),s=S.s,sf=S.safe,H=Math.max(10,+s.height||60),hasZ=terrAvail();
  const{speed,...rest}=s;
  const key=JSON.stringify([S.area,rest,S.droneId,S.missionId,S.tf,S.tfTol,S.takeoff,hasZ?[S.terr.src,S.terr.ver,S.terr.plane]:0,sf.o.obst,sf.o.lines,sf.obs.st,sf.obs.list.length,sf.manual.map(m=>[m.lat,m.lng,m.r])]);
  if(cpCache.key===key&&cpCache.val)return cpCache.val;
  const mf=CP_MF[S.missionId]||1,mo=Math.min(+s.front||75,+s.side||75),ovF=clamp(1+(mo-75)*.01,.85,1.15);
  const Sp=clamp(2.5*H*mf*ovF,100,350),Rcov=.8*Sp,P=g.xy;
  let sa=0;for(let i=0;i<P.length;i++){const a=P[i],b=P[(i+1)%P.length];sa+=a.x*b.y-b.x*a.y}const sgn=sa>=0?1:-1;
  const xs=P.map(p=>p.x),ys=P.map(p=>p.y),x0=Math.min(...xs),x1=Math.max(...xs),y0=Math.min(...ys),y1=Math.max(...ys);
  const cell=Math.max(Math.sqrt((x1-x0)*(y1-y0)/2200),3),ins=Math.max(4,Math.min(clamp(.5*H,8,30),.12*Math.sqrt(g.area))),bandMax=ins*2.4+cell*.5;
  const cands=[],samp=[];
  const add=(x,y)=>{const p={x,y};if(!inPoly(p,P))return;const dB=cpRing(p,P,true);if(dB<ins*.9)return;cands.push({x,y,dB,band:dB<=bandMax,ex:0,z:null,vis:0,w:1})};
  for(let x=x0+cell/2;x<x1;x+=cell)for(let y=y0+cell/2;y<y1;y+=cell){const p={x,y};if(inPoly(p,P)){samp.push(p);add(x,y)}}
  const nrm=i=>{const a=P[i],b=P[(i+1)%P.length],L=Math.hypot(b.x-a.x,b.y-a.y)||1;return{x:-(b.y-a.y)/L*sgn,y:(b.x-a.x)/L*sgn}};
  for(let i=0;i<P.length;i++){
    const a=P[i],b=P[(i+1)%P.length],L=Math.hypot(b.x-a.x,b.y-a.y);if(L<1e-6)continue;const n=nrm(i),m=Math.max(1,Math.ceil(L/cell));
    for(let k=0;k<=m;k++){const t=k/m;add(a.x+t*(b.x-a.x)+n.x*ins*1.05,a.y+t*(b.y-a.y)+n.y*ins*1.05)}
    const pn=nrm((i+P.length-1)%P.length),dn=1+pn.x*n.x+pn.y*n.y;
    if(dn>.2)add(a.x+(pn.x+n.x)/dn*ins*1.05,a.y+(pn.y+n.y)/dn*ins*1.05);
  }
  /* flight coverage: how many camera positions are within reach of each candidate */
  const R=1.1*H,hash=new Map();
  plan.shots.forEach(q=>{const k=Math.floor(q.x/R)+','+Math.floor(q.y/R);let a=hash.get(k);if(!a){a=[];hash.set(k,a)}a.push(q)});
  const vis=(x,y)=>{let n=0;const cx=Math.floor(x/R),cy=Math.floor(y/R);for(let i=-1;i<=1;i++)for(let j=-1;j<=1;j++){const a=hash.get((cx+i)+','+(cy+j));if(a)for(const q of a)if((q.x-x)*(q.x-x)+(q.y-y)*(q.y-y)<=R*R)n++}return n};
  /* mapped obstacles to keep away from (buildings, trees, wires) */
  const haz=[];
  if(sf.obs.st==='ready')for(const ob of sf.obs.list.slice(0,1500)){if(!(ob.cat==='bt'?sf.o.obst:sf.o.lines)||!ob.pts||!ob.pts.length)continue;haz.push({pts:ob.pts,kind:ob.pts.length===1?'point':ob.kind==='poly'?'poly':'line',r:+ob.r||0,buf:ob.cat==='bt'?8:6,type:ob.type||'obstacle'})}
  sf.manual.forEach(m=>{haz.push({pts:[loc(m.lat,m.lng)],kind:'point',r:+m.r||0,buf:8,type:m.name||'your obstacle'})});
  haz.forEach(o=>{o.x0=Math.min(...o.pts.map(q=>q.x))-o.buf-o.r-2;o.x1=Math.max(...o.pts.map(q=>q.x))+o.buf+o.r+2;o.y0=Math.min(...o.pts.map(q=>q.y))-o.buf-o.r-2;o.y1=Math.max(...o.pts.map(q=>q.y))+o.buf+o.r+2});
  const hazard=p=>{let best=null;for(const o of haz){if(p.x<o.x0||p.x>o.x1||p.y<o.y0||p.y>o.y1)continue;
    const d=o.kind==='point'?cpH(p,o.pts[0])-o.r:o.kind==='poly'?(inPoly(p,o.pts)?0:cpRing(p,o.pts,true)):cpRing(p,o.pts,false);
    if(d<o.buf&&(!best||d<best.d))best={d,type:o.type}}return best};
  const zAt=(x,y)=>{const l=g.toLL(x,y);return groundAt(l.lat,l.lng)};
  let zmin=null,zmax=null;
  for(const q of cands){q.vis=vis(q.x,q.y);if(hasZ){q.z=zAt(q.x,q.y);zmin=zmin==null?q.z:Math.min(zmin,q.z);zmax=zmax==null?q.z:Math.max(zmax,q.z)}}
  if(hasZ)for(const q of samp.filter((_,i)=>i%3===0)){const z=zAt(q.x,q.y);zmin=Math.min(zmin??z,z);zmax=Math.max(zmax??z,z)}
  if(hasZ)for(const q of cands){const dx=zAt(q.x+6,q.y)-zAt(q.x-6,q.y),dy=zAt(q.x,q.y+6)-zAt(q.x,q.y-6);q.slope=Math.hypot(dx,dy)/12}
  const vs=cands.map(q=>q.vis).sort((a,b)=>a-b),med=vs.length?vs[vs.length>>1]:0;
  let nHaz=0,nLow=0;
  for(const q of cands){if(hazard(q)){q.ex=1;nHaz++}else if(med>0&&q.vis<.25*med){q.ex=2;nLow++}q.w=(med>0?.7+.3*Math.min(1,q.vis/(.7*med)):1)*(q.slope>.25?.6:q.slope>.12?.85:1)}
  if(cands.filter(q=>!q.ex).length<8)cands.forEach(q=>{if(q.ex===2)q.ex=0});
  const pool=cands.filter(q=>!q.ex),relief=hasZ&&zmin!=null?zmax-zmin:0;
  const cent=g.c,ctx={key,plan,g,P,H,Sp,Rcov,ins,bandMax,cell,cands,pool,samp,med,vis,hazard,hasZ,zAt,zmin,zmax,relief,mf,ovF,cent,nHaz,nLow,per:g.per,area:g.area};
  /* how many markers are needed so that every part of the polygon is within Rcov of one */
  let nCover=0;
  if(pool.length){
    const mp=new Float64Array(pool.length).fill(Infinity),ms=new Float64Array(samp.length).fill(Infinity);let first=true;
    while(nCover<CP_CAP){
      let bi=-1,bs=-1;
      if(first){pool.forEach((q,i)=>{const d=Math.hypot(q.x-cent.x,q.y-cent.y)*q.w;if(d>bs){bs=d;bi=i}});first=false}
      else pool.forEach((q,i)=>{const d=mp[i]*q.w;if(d>bs){bs=d;bi=i}});
      if(bi<0)break;const c=pool[bi];nCover++;
      for(let i=0;i<pool.length;i++){const d=Math.hypot(pool[i].x-c.x,pool[i].y-c.y);if(d<mp[i])mp[i]=d}
      let mx=0;for(let i=0;i<samp.length;i++){const d=Math.hypot(samp[i].x-c.x,samp[i].y-c.y);if(d<ms[i])ms[i]=d;if(ms[i]>mx)mx=ms[i]}
      if(mx<=Rcov)break;
    }
  }
  ctx.nCover=nCover;ctx.nPer=Math.ceil(g.per/(2*Sp));ctx.relExtra=relief>=60?3:relief>=30?2:relief>=10?1:0;ctx.minN=g.area<3000?4:5;
  ctx.nRec=clamp(Math.max(ctx.minN,nCover,ctx.nPer)+ctx.relExtra,ctx.minN,CP_CAP);
  cpCache={key,val:ctx};return ctx;
}
/* choose positions: elevation extremes, then the perimeter, then the interior, always the point farthest from those already chosen */
function cpSuggest(c,N,fixed){
  const ch=fixed.map(f=>({x:f.x,y:f.y,z:c.hasZ?c.zAt(f.x,f.y):null,b:cpRing(f,c.P,true)<=c.bandMax})),out=[],pool=c.pool;
  let slots=N-ch.length;if(slots<=0||!pool.length)return out;
  const mind=new Float64Array(pool.length).fill(Infinity),upd=q=>{for(let i=0;i<pool.length;i++){const d=Math.hypot(pool[i].x-q.x,pool[i].y-q.y);if(d<mind[i])mind[i]=d}};
  ch.forEach(upd);
  const zc=ch.map(q=>q.z).filter(z=>z!=null),terc=c.relief>=5?[0,0,0]:null,tz=z=>clamp(Math.floor((z-c.zmin)/(c.relief/3+1e-9)),0,2);
  if(terc)zc.forEach(z=>terc[tz(z)]++);
  const take=i=>{const q=pool[i];out.push({x:q.x,y:q.y});ch.push({x:q.x,y:q.y,b:q.band,z:q.z});upd(q);mind[i]=0;slots--;if(terc&&q.z!=null)terc[tz(q.z)]++};
  if(c.relief>=5&&slots>=4){
    const idx=pool.map((_,i)=>i).filter(i=>pool[i].z!=null).sort((a,b)=>pool[a].z-pool[b].z);
    for(const list of[idx,idx.slice().reverse()]){const k=list.find(i=>mind[i]>.4*c.Sp);if(k!=null&&slots>0)take(k)}
  }
  const N0=N,perT=Math.max(Math.min(4,N0),N0<=5?N0-1:Math.round(N0*.7));
  const zb=q=>{if(!terc||q.z==null)return 1;const lo=Math.min(...terc);return terc[tz(q.z)]===lo&&lo<Math.max(...terc)?1.3:1};
  const best=(onlyBand)=>{let bi=-1,bs=-1;
    if(!ch.length){pool.forEach((q,i)=>{if(onlyBand&&!q.band)return;const d=Math.hypot(q.x-c.cent.x,q.y-c.cent.y)*q.w;if(d>bs){bs=d;bi=i}});return bi}
    pool.forEach((q,i)=>{if(onlyBand&&!q.band)return;const d=mind[i]*q.w*zb(q);if(d>bs){bs=d;bi=i}});return bs>0?bi:-1};
  while(slots>0&&ch.filter(q=>q.b).length<perT){const i=best(true);if(i<0)break;take(i)}
  while(slots>0){const i=best(false);if(i<0)break;take(i)}
  return out;
}
/* a few long cross-site baselines in different directions, so scale is checked across the site and not only along the edge */
function cpDiagonals(c,M){
  const n=M.length;if(n<4)return[];
  const want=n<6?1:n<12?2:3,pairs=[];
  for(let i=0;i<n;i++)for(let j=i+2;j<n;j++){
    if(i===0&&j===n-1&&n<=3)continue;
    const d=cpH(M[i],M[j]);let ok=true;
    for(let k=1;k<9;k++){const t=k/9;if(!inPoly({x:M[i].x+(M[j].x-M[i].x)*t,y:M[i].y+(M[j].y-M[i].y)*t},c.P)){ok=false;break}}
    if(ok)pairs.push({a:i,b:j,d,ang:Math.atan2(M[j].y-M[i].y,M[j].x-M[i].x)})}
  pairs.sort((p,q)=>q.d-p.d);const out=[];
  const angD=(a,b)=>{let d=Math.abs(a-b)%Math.PI;return Math.min(d,Math.PI-d)};
  for(const p of pairs){if(out.length>=want)break;
    if(out.every(o=>angD(o.ang,p.ang)>=Math.PI/5&&o.a!==p.a&&o.b!==p.b&&o.a!==p.b&&o.b!==p.a))out.push(p);
    else if(!out.length)out.push(p)}
  if(out.length<want)for(const p of pairs){if(out.length>=want)break;if(!out.includes(p)&&out.every(o=>angD(o.ang,p.ang)>=Math.PI/10))out.push(p)}
  return out.map(o=>({...o,A:M[o.a],B:M[o.b]}));
}
function cpOrder(c,items){
  const pts=items.map(it=>({it,...loc(it.lat,it.lng)}));if(pts.length<3)return items.slice();
  const len=a=>a.reduce((t,p,i)=>t+(i?cpH(a[i-1],p):0),0),s0=c.plan.shots[0]||c.cent;
  /* a tidy serpentine through bands across the site, compared with a nearest-neighbour chain; the shorter-looking one wins */
  const xs=pts.map(p=>p.x),ys=pts.map(p=>p.y),w=Math.max(...xs)-Math.min(...xs),h=Math.max(...ys)-Math.min(...ys),ax=w>=h?'x':'y',cr=w>=h?'y':'x';
  const k=clamp(Math.round(Math.sqrt(pts.length*Math.min(w,h)/Math.max(w,h,1))),1,pts.length),by=pts.slice().sort((a,b)=>a[cr]-b[cr]),serp=[];
  for(let b=0;b<k;b++){const part=by.slice(Math.round(b*pts.length/k),Math.round((b+1)*pts.length/k)).sort((a,b2)=>a[ax]-b2[ax]);if(b%2)part.reverse();serp.push(...part)}
  if(cpH(serp[serp.length-1],s0)<cpH(serp[0],s0))serp.reverse();
  const nn=cpNN(c,pts,s0);
  return(len(serp)<=len(nn)*1.3?serp:nn).map(p=>p.it);
}
function cpNN(c,pts,s0){
  let cur=pts.reduce((b,p)=>cpH(p,s0)<cpH(b,s0)?p:b);const left=pts.filter(p=>p!==cur),path=[cur];
  while(left.length){let b=0;for(let k=1;k<left.length;k++)if(cpH(left[k],cur)<cpH(left[b],cur))b=k;cur=left.splice(b,1)[0];path.push(cur)}
  let imp=true,guard=0;
  while(imp&&guard++<60){imp=false;for(let i=1;i<path.length-1;i++)for(let j=i+1;j<path.length;j++){
    const a=path[i-1],b=path[i],e=path[j],d=path[j+1];
    const before=cpH(a,b)+(d?cpH(e,d):0),after=cpH(a,e)+(d?cpH(b,d):0);
    if(after<before-1e-6){const seg=path.slice(i,j+1).reverse();path.splice(i,j-i+1,...seg);imp=true}}}
  return path;
}
const cpSigNow=c=>JSON.stringify([c.key,S.cp.on,S.cp.mode,S.cp.mode==='manual'?S.cp.n:0]);
function cpReset(){S.cp.meas.picks=[];S.cp.meas.on=false;S.cp.items=[];S.cp.sig='';S.cp.sel=null;S.cp.add=false;S.cp.mode='auto'}
function cpEnsure(){
  const c=cpCtx();if(!c)return null;const cp=S.cp;
  if(!cp.on)return c;
  const sig=cpSigNow(c);if(cp.sig===sig)return c;
  const N=clamp(cp.mode==='manual'?cp.n:c.nRec,1,CP_MAX);
  const keep=cp.items.filter(i=>i.lock||i.man),fixed=keep.map(it=>({...loc(it.lat,it.lng)}));
  const fresh=cpSuggest(c,N,fixed).map(q=>{const ll=c.g.toLL(q.x,q.y);return{id:cp.nid++,lat:ll.lat,lng:ll.lng,lock:false,man:false}});
  cp.items=cpOrder(c,keep.concat(fresh));cp.sig=sig;cp.n=cp.items.length;
  if(!cp.items.some(i=>i.id===cp.sel))cp.sel=null;
  return c;
}
function cpSync(){const c=cpCtx();if(c)S.cp.sig=cpSigNow(c)}
function cpXY(c){return S.cp.items.map((it,i)=>{const p=loc(it.lat,it.lng);return{...it,i,x:p.x,y:p.y,z:c.hasZ?c.zAt(p.x,p.y):null}})}
function cpDist(a,b){const d=cpH(a,b);return{h:d,s:a.z!=null&&b.z!=null?Math.hypot(d,a.z-b.z):null}}
function cpStats(c){
  const M=cpXY(c),n=M.length;let nn=[],path=0,mn=1e9;
  M.forEach((a,i)=>{let b=1e9;M.forEach((q,j)=>{if(i!==j)b=Math.min(b,cpH(a,q))});if(n>1){nn.push(b);mn=Math.min(mn,b)}if(i)path+=cpH(M[i-1],a)});
  const per=M.filter(m=>cpRing(m,c.P,true)<=c.bandMax*1.5&&inPoly(m,c.P)).length;
  const diag=cpDiagonals(c,M);let ext=0;M.forEach(a=>M.forEach(b=>{ext=Math.max(ext,cpH(a,b))}));
  return{n,M,avg:nn.length?nn.reduce((a,b)=>a+b,0)/nn.length:0,min:n>1?mn:0,path,per,inner:n-per,diag,ext};
}
function cpQuality(c){
  const M=cpXY(c),n=M.length,items=[],flag={},mark=(id,s)=>{if(!flag[id]||s==='bad')flag[id]=s};
  const push=(sev,t)=>items.push({sev,t});
  if(!n){push('warn','No markers yet. Use "Add marker" or turn automatic placement on.');return{items,flag,sev:'warn'}}
  if(n<3)push('bad',`Only ${n} marker${n===1?'':'s'}. Three or more are needed to check scale in more than one direction.`);
  else if(n<5)push('warn',`Only ${n} markers. At least 5 are commonly recommended, with some inside the area.`);
  const tooClose=Math.max(8,Math.min(Math.max(15,.4*c.Sp),.25*Math.sqrt(c.area))),close=[];
  for(let i=0;i<n;i++)for(let j=i+1;j<n;j++){const d=cpH(M[i],M[j]);if(d<tooClose){close.push(`CP${i+1}–CP${j+1} ${cpFmtD(d)}`);mark(M[i].id,'warn');mark(M[j].id,'warn')}}
  if(close.length)push('warn',`Markers too close together (under ${cpFmtD(tooClose)}): ${close.slice(0,4).join(', ')}${close.length>4?' …':''}. Close pairs add little for a scale check.`);
  const legs=[];for(let i=1;i<n;i++){const d=cpH(M[i-1],M[i]);if(d>2.5*c.Sp){legs.push(`CP${i}–CP${i+1} ${cpFmtD(d)}`);mark(M[i].id,'warn');mark(M[i-1].id,'warn')}}
  if(legs.length)push('warn',`Long gaps in the numbered chain: ${legs.slice(0,3).join(', ')}. Use "Renumber all" or add a marker between them so every leg is a short, easy distance to measure.`);
  if(n>=4){const dg=cpDiagonals(c,M),ext=Math.max(...M.flatMap(a=>M.map(b=>cpH(a,b))));
    if(!dg.length)push('warn','No marker pair has a clear line across the site, so there is no diagonal distance to check. Move a marker to the far side.');
    else if(dg[0].d<.6*ext)push('warn','The longest cross-site distance is short compared with the site size. Add a marker near the opposite corner.')}
  let unc=0;for(const q of c.samp){let b=1e9;for(const m of M){const d=cpH(q,m);if(d<b)b=d}if(b>c.Rcov)unc++}
  const fr=c.samp.length?unc/c.samp.length:0;
  if(fr>.35)push('bad',`About ${Math.round(fr*100)}% of the area is more than ${cpFmtD(c.Rcov)} from any marker.`);
  else if(fr>.12)push('warn',`About ${Math.round(fr*100)}% of the area is more than ${cpFmtD(c.Rcov)} from any marker. Add one in the empty part.`);
  const out=M.filter(m=>!inPoly(m,c.P));
  if(out.length){out.forEach(m=>mark(m.id,'warn'));push('warn',`${out.map(m=>'CP'+(m.i+1)).join(', ')} ${out.length===1?'is':'are'} outside the survey area, so the flight may not cover ${out.length===1?'it':'them'} well.`)}
  const lowv=M.filter(m=>inPoly(m,c.P)&&c.med>0&&c.vis(m.x,m.y)<.25*c.med);
  if(lowv.length){lowv.forEach(m=>mark(m.id,'warn'));push('warn',`${lowv.map(m=>'CP'+(m.i+1)).join(', ')} ${lowv.length===1?'is':'are'} seen by few photos. Move it nearer the flight lines.`)}
  const hz=M.map(m=>({m,h:c.hazard(m)})).filter(x=>x.h);
  if(hz.length){hz.forEach(x=>mark(x.m.id,'warn'));push('warn',`${hz.slice(0,4).map(x=>'CP'+(x.m.i+1)+' near '+String(x.h.type).toLowerCase()).join(', ')}: mapped object within a few metres. Pick open ground.`)}
  if(c.hasZ){
    const steep=[];for(let i=1;i<n;i++){const d=cpDist(M[i-1],M[i]),dz=Math.abs(M[i].z-M[i-1].z);if(dz>=20||(d.h>5&&dz/d.h>=.12)){steep.push(`CP${i}–CP${i+1} ${Math.round(dz)} m`);mark(M[i].id,'warn');mark(M[i-1].id,'warn')}}
    if(steep.length)push('warn',`Large height change between consecutive markers: ${steep.slice(0,4).join(', ')}${steep.length>4?' …':''}. Distances on the map are horizontal, so the true slope distance is longer.`);
    const zs=M.map(m=>m.z),zr=Math.max(...zs)-Math.min(...zs);
    if(c.relief>=5&&n>=3&&zr<.5*c.relief)push('warn',`The site spans ${Math.round(c.relief)} m of height but the markers only span ${Math.round(zr)} m. Add markers on the highest and lowest parts.`);
  }else push('warn','Elevation is not loaded, so height differences between markers are not checked. Turn on terrain following or load the elevation in Settings.');
  const sev=items.some(x=>x.sev==='bad')?'bad':items.length?'warn':'ok';
  if(sev==='ok')push('ok','No problems found by these checks. This is a planning aid only. Check the ground on site.');
  return{items,flag,sev};
}
function cpLegs(c){
  const m=S.cp.meas,M=cpXY(c),by=id=>M.find(x=>x.id===id),P=m.picks.map(by).filter(Boolean),legs=[];
  if(P.length<2)return{legs,P,tot:null};
  const pairs=m.mode==='chain'?P.slice(1).map((q,i)=>[P[i],q]):P.slice(1).map(q=>[P[0],q]);
  for(const[a,b]of pairs){const d=cpDist(a,b),dz=a.z!=null&&b.z!=null?b.z-a.z:null;legs.push({a,b,h:d.h,s:d.s,dz,slope:dz!=null&&d.h>0?dz/d.h*100:null,brg:bearingOf(b.x-a.x,b.y-a.y)})}
  const tot={h:legs.reduce((t,l)=>t+l.h,0),s:legs.every(l=>l.s!=null)?legs.reduce((t,l)=>t+l.s,0):null};
  return{legs,P,tot};
}
const cpLegTxt=l=>{const t=S.cp.meas.type;if(l.s==null||t==='h')return cpFmtM(l.h);if(t==='s')return cpFmtM(l.s);return`H ${cpFmtM(l.h)} · 3D ${cpFmtM(l.s)}`};
function cpTogglePick(id){const m=S.cp.meas,k=m.picks.indexOf(id);if(k>=0)m.picks.splice(k,1);else m.picks.push(id)}
function cpOverlay(){
  const cp=S.cp;if(!cp.items.length||!S.area||!S.s)return'';
  const c=cpCtx();if(!c)return'';const live=S.step===4,q=cpQuality(c),M=cpXY(c);let h='';
  const P=M.map(m=>({m,s:toScreen(c.g.toLL(m.x,m.y))}));
  if(P.length>1){
    const pl=P.map(p=>p.s.x.toFixed(1)+','+p.s.y.toFixed(1)).join(' ');
    h+=`<polyline points="${pl}" fill="none" stroke="#fff" stroke-width="3.4" stroke-linejoin="round" opacity=".85"/><polyline points="${pl}" fill="none" stroke="#1971c2" stroke-width="1.4" stroke-linejoin="round"/>`;
    h+=`<g font-family="IBM Plex Mono,monospace" font-size="10.5" font-weight="500" text-anchor="middle" paint-order="stroke" stroke="#fff" stroke-width="3.2" stroke-linejoin="round" fill="#0b4f8a">`;
    for(let i=1;i<P.length;i++){const a=P[i-1].s,b=P[i].s;if(Math.hypot(a.x-b.x,a.y-b.y)<44)continue;
      const mx=(a.x+b.x)/2,my=(a.y+b.y)/2;if(mx<-40||my<-20||mx>V.w+40||my>V.h+20)continue;
      h+=`<text x="${mx.toFixed(1)}" y="${(my-4).toFixed(1)}">${cpFmtD(cpH(M[i-1],M[i]))}</text>`}
    h+='</g>';
  }
  if(live&&cp.meas.picks.length){
    const L=cpLegs(c);h+=`<g font-family="IBM Plex Mono,monospace" font-size="11.5" font-weight="600" text-anchor="middle" paint-order="stroke" stroke="#fff" stroke-width="3.6" stroke-linejoin="round" fill="#a13b00">`;
    L.legs.forEach(l=>{const a=toScreen(c.g.toLL(l.a.x,l.a.y)),b=toScreen(c.g.toLL(l.b.x,l.b.y));
      h+=`<line x1="${a.x.toFixed(1)}" y1="${a.y.toFixed(1)}" x2="${b.x.toFixed(1)}" y2="${b.y.toFixed(1)}" stroke="#fff" stroke-width="6" stroke-linecap="round"/><line x1="${a.x.toFixed(1)}" y1="${a.y.toFixed(1)}" x2="${b.x.toFixed(1)}" y2="${b.y.toFixed(1)}" stroke="#f76707" stroke-width="3" stroke-linecap="round"/>`;
      h+=`<text x="${((a.x+b.x)/2).toFixed(1)}" y="${((a.y+b.y)/2-8).toFixed(1)}">${cpLegTxt(l)}</text>`});
    h+='</g>';
  }
  const dg=cpStats(c).diag;
  if(dg.length){h+=`<g font-family="IBM Plex Mono,monospace" font-size="10.5" font-weight="500" text-anchor="middle" paint-order="stroke" stroke="#fff" stroke-width="3.2" stroke-linejoin="round" fill="#5f3dc4">`;
    dg.forEach((o,k)=>{const a=toScreen(c.g.toLL(o.A.x,o.A.y)),b=toScreen(c.g.toLL(o.B.x,o.B.y));
      h+=`<line x1="${a.x.toFixed(1)}" y1="${a.y.toFixed(1)}" x2="${b.x.toFixed(1)}" y2="${b.y.toFixed(1)}" stroke="#fff" stroke-width="3.2" opacity=".8" stroke-dasharray="7 5"/><line x1="${a.x.toFixed(1)}" y1="${a.y.toFixed(1)}" x2="${b.x.toFixed(1)}" y2="${b.y.toFixed(1)}" stroke="#7048e8" stroke-width="1.5" stroke-dasharray="7 5"/>`;
      const mx=(a.x+b.x)/2,my=(a.y+b.y)/2;if(Math.hypot(a.x-b.x,a.y-b.y)>50)h+=`<text x="${mx.toFixed(1)}" y="${(my+13).toFixed(1)}">D${k+1} ${cpFmtD(o.d)}</text>`});
    h+='</g>'}
  P.forEach(({m,s})=>{
    if(s.x<-40||s.y<-40||s.x>V.w+40||s.y>V.h+40)return;
    const col=m.lock||m.man?'#2f9e44':'#1c7ed6',fl=q.flag[m.id],sel=cp.sel===m.id;
    h+=`<g ${live?`class="cpm" data-cp="${m.id}"`:''} transform="translate(${s.x.toFixed(1)},${s.y.toFixed(1)})">`;
    if(live)h+=`<circle cy="-10" r="15" fill="transparent"/>`;
    const pk=live?cp.meas.picks.indexOf(m.id):-1;
    if(pk>=0)h+=`<circle r="11" fill="none" stroke="#fff" stroke-width="6"/><circle r="11" fill="none" stroke="#f76707" stroke-width="3"/><g transform="translate(-14,-30)"><circle r="8" fill="#f76707" stroke="#fff" stroke-width="2"/><text y="3.8" text-anchor="middle" font-size="10.5" font-weight="700" fill="#fff" font-family="IBM Plex Mono,monospace">${pk+1}</text></g>`;
    if(fl)h+=`<circle r="9" fill="none" stroke="${fl==='bad'?'#e03131':'#f08c00'}" stroke-width="2.6"/>`;
    if(sel)h+=`<circle r="12" fill="none" stroke="#fff" stroke-width="5"/><circle r="12" fill="none" stroke="var(--route)" stroke-width="2.4"/>`;
    h+=`<ellipse rx="4.5" ry="2" cy="1" fill="#000" opacity=".28"/><line y2="-23" stroke="#fff" stroke-width="4.4" stroke-linecap="round"/><line y2="-23" stroke="#263238" stroke-width="1.8" stroke-linecap="round"/>`;
    h+=`<polygon points="0,-23 15,-17.5 0,-12" fill="#fff" stroke="#fff" stroke-width="3.4" stroke-linejoin="round"/><polygon points="0,-23 15,-17.5 0,-12" fill="${col}"/>`;
    if(m.lock||m.man)h+=`<g transform="translate(0,6)"><path d="M-2.2 0v-2a2.2 2.2 0 0 1 4.4 0v2" fill="none" stroke="#fff" stroke-width="3.2"/><path d="M-2.2 0v-2a2.2 2.2 0 0 1 4.4 0v2" fill="none" stroke="#2b8a3e" stroke-width="1.4"/><rect x="-4" y="0" width="8" height="5.6" rx="1.2" fill="#2b8a3e" stroke="#fff" stroke-width="1"/></g>`;
    h+=`<text x="18" y="-12" font-family="IBM Plex Mono,monospace" font-size="11.5" font-weight="700" paint-order="stroke" stroke="#fff" stroke-width="3.4" stroke-linejoin="round" fill="#0b2a4a">CP${m.i+1}</text></g>`;
  });
  return h;
}
function cpInfo(si){
  const c=S.cp.items.length?cpCtx():null;if(!c){si.hidden=true;return}
  const st=cpStats(c);si.hidden=false;
  si.innerHTML=`<i></i><b class="num">${st.n}</b> control marker${st.n===1?'':'s'} <span>recommended ${c.nRec} · ${st.per} around the edge, ${st.inner} inside${st.n>1?` · ${cpFmtD(st.avg)} average spacing`:''}</span>`;
}
function cpRefresh(){cpSync();renderPanel(true)}
function cpAddAt(l){
  const g=fromScreen(l.x,l.y),cp=S.cp;cp.items.push({id:cp.nid++,lat:g.lat,lng:g.lng,lock:true,man:true});
  cp.mode='manual';cp.n=cp.items.length;cp.sel=cp.items[cp.items.length-1].id;cpRefresh();
}
function cpSetAdd(b){
  if(b&&S.cp.meas.on)S.cp.meas.on=false;
  S.cp.add=b;mapEl.classList.toggle('drawing',b||!!S.mode||S.obsMode);
  const bn=$('#banner');if(b){bn.textContent='Click the map to place a control marker · Esc to cancel';bn.hidden=false}else updateBanner();
  if(S.step===4)renderPanel(true);
}
function cpDelete(id){
  const cp=S.cp,k=cp.items.findIndex(i=>i.id===id);if(k<0)return;
  cp.items.splice(k,1);cp.meas.picks=cp.meas.picks.filter(x=>x!==id);cp.mode='manual';cp.n=cp.items.length;if(cp.sel===id)cp.sel=null;cpRefresh();
}
function cpRenumber(){const c=cpCtx();if(!c)return;S.cp.items=cpOrder(c,S.cp.items);cpRefresh()}
function cpResetSuggested(){const cp=S.cp;cp.items=[];cp.mode='auto';cp.sel=null;cp.sig='';renderPanel(true)}
function cpSetN(v){const cp=S.cp;cp.mode='manual';cp.n=clamp(Math.round(v)||1,1,CP_MAX);cp.sig='';renderPanel(true)}
function cpMeasHtml(c){
  const m=S.cp.meas,L=cpLegs(c),hz=c.hasZ;
  const seg=(act,cur,opts)=>`<div class="seg2" role="group">${opts.map(([v,l])=>`<button type="button" class="${cur===v?'on':''}" data-act="${act}" data-v="${v}">${l}</button>`).join('')}</div>`;
  const rows=L.legs.map(l=>`<tr><td><b>CP${l.a.i+1}→CP${l.b.i+1}</b></td><td>${cpFmtM(l.h)}</td><td>${l.s!=null?cpFmtM(l.s):'–'}</td><td>${l.dz!=null?(l.dz>=0?'+':'')+l.dz.toFixed(1)+' m':'–'}</td><td>${l.slope!=null?(l.slope>=0?'+':'')+l.slope.toFixed(1)+' %':'–'}</td><td>${Math.round(l.brg)}°</td></tr>`).join('');
  return`<div class="row"><button type="button" class="btn ${m.on?'on':''}" data-act="cpmeas">${m.on?'Click markers… (Esc to stop)':'Measure between markers'}</button><button type="button" class="btn" data-act="cpmclear" ${m.picks.length?'':'disabled'}>Clear picks</button></div>
  ${seg('cpmtype',m.type,[['h','Horizontal'],['s','Real (slope)'],['both','Both']])}
  ${seg('cpmmode',m.mode,[['chain','Chain 1→2→3'],['first','From first pick']])}
  ${m.picks.length?`<div class="note">Picked: ${m.picks.map((id,k)=>{const x=S.cp.items.findIndex(i=>i.id===id);return x<0?'':`<b>${k+1}</b> = CP${x+1}`}).join(' · ')}. Click a picked marker again to un-pick it.</div>`:`<div class="hint">${m.on?'Click two or more markers on the map, or rows in the table below.':'Turn measuring on, then click markers on the map to see the distance between them.'}</div>`}
  ${L.legs.length?`<div class="wptable"><table><thead><tr><th>Leg</th><th>Horiz.</th><th>Real</th><th>Δh</th><th>Slope</th><th>Dir.</th></tr></thead><tbody>${rows}${L.legs.length>1?`<tr><td><b>Total</b></td><td><b>${cpFmtM(L.tot.h)}</b></td><td><b>${L.tot.s!=null?cpFmtM(L.tot.s):'–'}</b></td><td></td><td></td><td></td></tr>`:''}</tbody></table></div>`:''}
  <div class="note">${hz?'Real distance is the straight line between the two ground points, using the loaded elevation: √(horizontal² + height difference²). A tape laid along the surface can read a little more.':'Elevation is not loaded, so only horizontal distance is shown. Turn on terrain following or load elevation in Settings to see real (slope) distance.'}</div>`;
}
function stepControl(){
  const c=cpEnsure(),cp=S.cp,m=mission(),s=S.s;
  if(!c)return`<div><h2>Control points</h2><p class="lead">Choose the area, drone and mission first.</p></div>`;
  const st=cpStats(c),q=cpQuality(c),selM=st.M.find(x=>x.id===cp.sel);
  const hz=c.hasZ;
  const why=[
    `<b>${c.nCover}</b> to cover the shape: every part of the polygon within ${cpFmtD(c.Rcov)} of a marker`,
    `<b>${c.nPer}</b> to keep edge markers at most ${cpFmtD(2*c.Sp)} apart along the ${cpFmtD(c.per)} boundary`,
    `minimum <b>${c.minN}</b> for any site`,
    c.relExtra?`<b>+${c.relExtra}</b> because the ground varies by about ${Math.round(c.relief)} m`:'',
  ].filter(Boolean).map(t=>`<li>${t}</li>`).join('');
  const rows=st.M.map((x,i)=>{const nx=st.M[i+1],d=nx?cpDist(x,nx):null,fl=q.flag[x.id];
    return`<tr class="${cp.sel===x.id?'cpsel':''}" data-act="cpsel" data-id="${x.id}"><td><b>CP${i+1}</b>${fl?`<i class="cpdot ${fl}"></i>`:''}</td><td>${hz&&x.z!=null?Math.round(x.z)+' m':'–'}</td><td>${d?cpFmtD(d.h)+(d.s!=null&&Math.abs(d.s-d.h)>=1?` <small>(${cpFmtD(d.s)} on slope)</small>`:''):'–'}</td><td class="cpb"><button type="button" class="mini ${x.lock||x.man?'on':''}" data-act="cplock" data-id="${x.id}" title="Lock position" aria-pressed="${!!(x.lock||x.man)}">${x.lock||x.man?'Locked':'Lock'}</button><button type="button" class="mini" data-act="cpdel" data-id="${x.id}" aria-label="Delete CP${i+1}" title="Delete marker">Del</button></td></tr>`}).join('');
  const sel=selM?`<div class="cpsel-box"><b>CP${selM.i+1}: distance to each marker</b><div class="cpchips">${st.M.filter(o=>o.id!==selM.id).map(o=>{const d=cpDist(selM,o);return`<span>CP${o.i+1} <b>${cpFmtD(d.h)}</b></span>`}).join('')||'<span>No other markers</span>'}</div></div>`:'';
  return`<div><h2>Control points</h2><p class="lead">Suggested ground markers for checking scale in the finished model. Paint or place them before you fly. They are local distance-check references, not surveyed GCPs and not formal photogrammetric checkpoints. They help judge relative scale and geometry. They cannot independently prove absolute accuracy.</p></div>
  <div class="sect"><h3>Control Point Planner</h3>
   <label class="cpsw"><input type="checkbox" data-cpf="on" ${cp.on?'checked':''}> <span><b>Automatic placement</b><small>${cp.on?'Markers update when the area, settings, height or overlap change. Locked markers stay.':'Off. Nothing moves by itself. You can still add and drag markers.'}</small></span></label>
   <div class="seg2" role="group" aria-label="Number of markers"><button type="button" class="${cp.mode==='auto'?'on':''}" data-act="cpmode" data-v="auto">Automatic number</button><button type="button" class="${cp.mode==='manual'?'on':''}" data-act="cpmode" data-v="manual">Manual number</button></div>
   <div class="cpn"><button type="button" class="btn" data-act="cpdec" aria-label="Fewer markers" ${cp.items.length<=1?'disabled':''}>−</button><input type="number" min="1" max="${CP_MAX}" step="1" data-cpf="n" value="${cp.items.length}" aria-label="Number of markers"><button type="button" class="btn" data-act="cpinc" aria-label="More markers" ${cp.items.length>=CP_MAX?'disabled':''}>+</button><span class="note">Recommended <b>${c.nRec}</b></span></div>
   <div class="note">Based on: ${m.name} · ${s.height} m ${S.tf==='off'?'':'above ground '}· overlap ${s.front}/${s.side} % · ${S.safe.obs.st==='ready'?'mapped obstacles avoided':'obstacle data not loaded'} · ${hz?'terrain used':'no terrain data'}. Target spacing about ${cpFmtD(c.Sp)}.</div>
   <div class="row"><button type="button" class="btn ${cp.add?'on':''}" data-act="cpadd">${cp.add?'Click the map… (Esc)':'Add marker'}</button><button type="button" class="btn" data-act="cprenum">Renumber all</button><button type="button" class="btn" data-act="cpreset">Reset to suggested</button></div>
   <div class="note">Drag a flag on the map to move it. A moved or added marker is locked. Select a marker and press Delete to remove it.</div>
  </div>
  <div class="stats"><div class="stat"><b>${st.n}</b><span>Markers</span></div><div class="stat"><b>${c.nRec}</b><span>Recommended</span></div><div class="stat"><b>${st.per} / ${st.inner}</b><span>Edge / inside</span></div><div class="stat"><b>${st.n>1?cpFmtD(st.avg):'–'}</b><span>Average spacing</span></div><div class="stat"><b>${st.n>1?cpFmtD(st.min):'–'}</b><span>Closest pair</span></div><div class="stat"><b>${st.n>1?cpFmtD(st.ext):'–'}</b><span>Longest baseline</span></div></div>
  <div class="sect"><h3>Placement checks</h3><div class="sfall ${q.sev}"><b>${q.sev==='ok'?'No problems found':q.sev==='bad'?'Fix before flying':'Check these'}</b><span>These checks cannot see water, dense vegetation, roads or access. Look at the ground before you place a marker.</span></div>${q.items.filter(x=>x.sev!=='ok'||q.sev==='ok').map(x=>`<div class="sfrow ${x.sev}"><i></i><div><span>${x.t}</span></div></div>`).join('')}</div>
  <div class="sect"><h3>Measure distances</h3>${cpMeasHtml(c)}</div>
  <div class="sect"><h3>Diagonal checks</h3>${st.diag.length?`<div class="cpchips">${st.diag.map((o,k)=>{const d=cpDist(o.A,o.B);return`<span>D${k+1} CP${o.a+1}–CP${o.b+1} <b>${cpFmtD(d.h)}</b>${d.s!=null&&Math.abs(d.s-d.h)>=1?` <small>(${cpFmtD(d.s)} on slope)</small>`:''}</span>`}).join('')}</div><div class="note">Dashed purple lines on the map. They cross the site so scale is checked in different directions, not only along the edge.</div>`:'<div class="hint">Needs at least 4 markers with a clear line between two of them.</div>'}</div>
  <div class="sect"><h3>Markers and distances</h3><div class="note">Distances are horizontal, as on the orthophoto${S.cp.items.length&&c.hasZ?'; slope distance is shown where it differs':''}.</div>${st.n?`<div class="wptable"><table><thead><tr><th>Marker</th><th>Ground</th><th>To next</th><th></th></tr></thead><tbody>${rows}</tbody></table></div>`:'<div class="hint">No markers.</div>'}${sel}</div>
  <div class="sect"><h3>How the number and places were chosen</h3><ul class="notes">${why}</ul>
   <ul class="notes"><li>Markers sit ${cpFmtD(c.ins)} or more inside the boundary: corners and edge first, then the inside. Spots seen by many flight photos and on flat ground are preferred, and ${hz?'high, middle and low ground are all used on a slope':'height spread is used when elevation is loaded'}.</li><li>Numbers follow a short walking chain, so each leg is an easy distance to measure. A few long diagonals are added across the site.</li><li>${hz?'The highest and lowest ground get a marker when the site is sloped.':'With elevation data, the highest and lowest ground would get a marker.'}</li><li>Places seen by few photos${c.nLow?` (${c.nLow} spots skipped)`:''} and spots near mapped buildings, trees or wires${c.nHaz?` (${c.nHaz} spots skipped)`:''} are avoided.</li></ul>
   <details class="note"><summary>Where these rules come from</summary>Guidance from <a href="https://help.propelleraero.com/hc/en-us/articles/19384158185495" target="_blank" rel="noopener">Propeller</a> (at least 5 points, roughly twice the flight height apart, spread evenly, include high and low ground, the edge of the markers is the edge of the accuracy), <a href="https://wingtra.com/surveying-gis/ground-control-points/" target="_blank" rel="noopener">Wingtra</a> (even spread, extra points in weak or vegetated zones, high-contrast targets) and <a href="https://www.pix4d.com/blog/GCP-accuracy-drone-maps" target="_blank" rel="noopener">Pix4D</a>. This tool relaxes the spacing to 2.5 times the flight height because the markers are scale checks, not adjustment points. Treat the numbers as a plan, not a rule.</details></div>`;
}

/* ---------- map interaction ---------- */
const ptrs=new Map();let drag=null,pinch=null;
const local=e=>{const r=mapEl.getBoundingClientRect();return{x:e.clientX-r.left,y:e.clientY-r.top}};
function rectPts(a,b,square){
  const A=toScreen(a),B=toScreen(b);let dx=B.x-A.x,dy=B.y-A.y;
  if(square){const s=Math.max(Math.abs(dx),Math.abs(dy));dx=(dx<0?-1:1)*s;dy=(dy<0?-1:1)*s}
  if(Math.abs(dx)<6||Math.abs(dy)<6)return null;
  return[[A.x,A.y],[A.x+dx,A.y],[A.x+dx,A.y+dy],[A.x,A.y+dy]].map(p=>fromScreen(p[0],p[1]));
}
mapEl.addEventListener('pointerdown',e=>{
  if(e.target.closest('.ctl'))return;
  mapEl.setPointerCapture(e.pointerId);ptrs.set(e.pointerId,local(e));
  if(ptrs.size===2){const [p,q]=[...ptrs.values()];pinch={d:Math.hypot(p.x-q.x,p.y-q.y),z:V.zoom};drag=null;return}
  const l=local(e),vh=e.target.closest&&e.target.closest('[data-vi]'),ch=S.step===4&&e.target.closest&&e.target.closest('[data-cp]');
  drag={x0:l.x,y0:l.y,moved:false,cp:ch?+ch.dataset.cp:null,vertex:vh?+vh.dataset.vi:null,c0:proj(V.lat,V.lng,V.zoom),z0:V.zoom,aBefore:!!S.draw.a};
  if((S.mode==='rect'||S.mode==='square')&&!S.draw.a){S.draw.a=fromScreen(l.x,l.y);S.draw.cur=S.draw.a}
});
mapEl.addEventListener('pointermove',e=>{
  const l=local(e);
  const g=fromScreen(l.x,l.y);$('#coord').textContent=g.lat.toFixed(5)+', '+g.lng.toFixed(5);
  if(ptrs.has(e.pointerId))ptrs.set(e.pointerId,l);
  if(pinch&&ptrs.size===2){const [p,q]=[...ptrs.values()],d=Math.hypot(p.x-q.x,p.y-q.y);
    zoomAt((p.x+q.x)/2,(p.y+q.y)/2,pinch.z+Math.log2(d/pinch.d));return}
  if(!drag){if(S.mode&&(S.draw.pts.length||S.draw.a)){S.draw.cur=g;drawOverlay()}return}
  const dx=l.x-drag.x0,dy=l.y-drag.y0;
  if(!drag.moved&&Math.hypot(dx,dy)>5)drag.moved=true;
  if(drag.cp!=null){if(drag.moved){const it=S.cp.items.find(x=>x.id===drag.cp);if(it){it.lat=g.lat;it.lng=g.lng;it.lock=true;S.cp.sel=it.id;drawOverlay()}}return}
  if(drag.vertex!=null){if(drag.moved){S.area[drag.vertex]=g;S.dirty=S.dirty;render();refreshStats()}return}
  if(S.mode==='rect'||S.mode==='square'){S.draw.cur=g;drawOverlay();return}
  if(drag.moved){mapEl.classList.add('panning');
    const c=unproj(drag.c0.x-dx,drag.c0.y-dy,drag.z0);V.lat=c.lat;V.lng=c.lng;render()}
  else if(S.mode==='polygon'&&S.draw.pts.length){S.draw.cur=g;drawOverlay()}
});
function endPointer(e){
  const l=local(e);ptrs.delete(e.pointerId);mapEl.classList.remove('panning');
  if(pinch){if(ptrs.size<2)pinch=null;drag=null;return}
  if(!drag)return;
  const d=drag;drag=null;
  if(d.cp!=null){if(d.moved)cpRefresh();else{if(S.cp.meas.on)cpTogglePick(d.cp);else S.cp.sel=d.cp;renderPanel(true)}return}
  if(d.vertex!=null){if(d.moved){planCache.key='';ensureSettings();renderPanel()}return}
  if(S.mode==='polygon'&&!d.moved)polyClick(l);
  else if(S.launchPick!=null&&!d.moved)setLaunch(l);
  else if(S.obsMode&&!d.moved)addManual(l);
  else if(S.cp.add&&S.step===4&&!d.moved)cpAddAt(l);
  else if(!S.mode&&!d.moved&&(S.step===3||S.step===5)&&S.area&&S.s)pickShot(l);
  else if((S.mode==='rect'||S.mode==='square')){
    if(d.moved||d.aBefore){const q=rectPts(S.draw.a,fromScreen(l.x,l.y),S.mode==='square');
      if(q)finishArea(q);else{S.draw.a=null;S.draw.cur=null;drawOverlay()}}
  }
}
mapEl.addEventListener('pointerup',endPointer);
mapEl.addEventListener('pointercancel',e=>{ptrs.delete(e.pointerId);drag=null;pinch=null;mapEl.classList.remove('panning')});
mapEl.addEventListener('wheel',e=>{
  if(e.target.closest('.ctl'))return;e.preventDefault();const l=local(e);
  zoomAt(l.x,l.y,V.zoom-clamp(e.deltaY,-120,120)*.004);
},{passive:false});
mapEl.addEventListener('dblclick',e=>{
  if(e.target.closest('.ctl')||e.target.closest('[data-vi]'))return;
  const l=local(e);
  if(!S.mode)zoomAt(l.x,l.y,V.zoom+1);
});
$('#zin').onclick=()=>zoomAt(V.w/2,V.h/2,Math.round(V.zoom)+1);
$('#zout').onclick=()=>zoomAt(V.w/2,V.h/2,Math.round(V.zoom)-1);
function polyClick(l){
  const p=S.draw.pts,g=fromScreen(l.x,l.y),now=performance.now(),last=S.draw.last;
  S.draw.last={t:now,x:l.x,y:l.y};
  if(last&&now-last.t<400&&Math.hypot(last.x-l.x,last.y-l.y)<10){if(p.length>=3)finishArea(p.slice());return} /* double click finishes */
  if(p.length>=3){const f=toScreen(p[0]);if(Math.hypot(f.x-l.x,f.y-l.y)<12){finishArea(p.slice());return}}
  p.push(g);S.draw.cur=g;drawOverlay();renderPanel();
}
function finishArea(pts){
  S.area=pts;S.mode=null;S.draw={pts:[],a:null,cur:null};planCache.key='';cpReset();
  mapEl.classList.remove('drawing');updateBanner();ensureSettings();
  fitPoints(pts,Math.min(90,V.w/5));renderPanel();
}
function startTool(m){
  S.mode=m;S.draw={pts:[],a:null,cur:null};mapEl.classList.add('drawing');
  if(S.area&&S.step===0){S.area=null;S.s=null;S.dirty=false;planCache.key='';cpReset()}
  updateBanner();drawOverlay();renderPanel();
}
function cancelTool(){S.mode=null;S.draw={pts:[],a:null,cur:null};mapEl.classList.remove('drawing');updateBanner();drawOverlay();renderPanel()}
function updateBanner(){
  const b=$('#banner');if(!S.mode){b.hidden=true;return}
  const t={polygon:S.draw.pts.length>=3?'Click the first point or double-click to finish':'Click the map to add corner points',
    rect:S.draw.a?'Click the opposite corner':'Press and drag, or click two corners',
    square:S.draw.a?'Click to set the size':'Press and drag, or click the first corner'}[S.mode];
  b.textContent=t;b.hidden=false;
}
document.addEventListener('keydown',e=>{
  if(e.target.matches('input,select,textarea'))return;
  if(e.key==='Escape'&&S.launchPick!=null){S.launchPick=null;setLaunchBanner();bpUpdate()}
  else if(e.key==='Escape'&&S.cp.add)cpSetAdd(false);
  else if(e.key==='Escape'&&S.step===4&&S.cp.meas.on){S.cp.meas.on=false;mapEl.classList.toggle('drawing',!!S.mode||S.obsMode);renderPanel(true)}
  else if(e.key==='Escape'&&S.obsMode)setObsMode(false);
  else if(e.key==='Escape'&&S.mode)cancelTool();
  else if(e.key==='Escape'&&S.sel!=null){S.sel=null;renderDlg();drawOverlay()}
  if((e.key==='Delete'||e.key==='Backspace')&&S.step===4&&S.cp.sel!=null&&!S.mode){e.preventDefault();cpDelete(S.cp.sel)}
  if(e.key==='Enter'&&S.mode==='polygon'&&S.draw.pts.length>=3)finishArea(S.draw.pts.slice());
  if(e.key==='Backspace'&&S.mode==='polygon'&&S.draw.pts.length){S.draw.pts.pop();drawOverlay();renderPanel();updateBanner()}
});

/* layers + search */
function renderLayers(){
  $('#layers').innerHTML=Object.entries(LAYERS).map(([k,l])=>`<button type="button" class="mbtn ${k===S.layer?'on':''}" data-layer="${k}">${l.name}</button>`).join('');
  $('#attr').textContent=LAYERS[S.layer].attr;
}
$('#layers').addEventListener('click',e=>{const b=e.target.closest('[data-layer]');if(!b)return;
  S.layer=b.dataset.layer;for(const img of tiles.values())img.remove();tiles.clear();TS.ok=0;TS.err=0;$('#notice').hidden=true;renderLayers();render()});
$('#searchForm').addEventListener('submit',async e=>{
  e.preventDefault();const q=$('#q').value.trim();if(!q)return;
  const m=q.match(/^\s*(-?\d+(?:\.\d+)?)\s*[,;\s]\s*(-?\d+(?:\.\d+)?)\s*$/);
  if(m&&Math.abs(+m[1])<=90&&Math.abs(+m[2])<=180){setView(+m[1],+m[2],16);return}
  try{
    const r=await fetch('https://nominatim.openstreetmap.org/search?format=json&limit=1&q='+encodeURIComponent(q));
    const j=await r.json();
    if(j[0]){const bb=j[0].boundingbox;
      fitPoints([{lat:+bb[0],lng:+bb[2]},{lat:+bb[1],lng:+bb[3]}],20);if(V.zoom>17)setView(V.lat,V.lng,17)}
    else toast('No place found. Try lat, lng instead.');
  }catch(err){toast('Place search is not reachable here. Type coordinates as lat, lng.')}
});

/* ---------- panel ---------- */
const ICON={
 polygon:'<svg width="30" height="30" viewBox="0 0 30 30" fill="none" stroke="currentColor" stroke-width="2"><path d="M5 22 L9 7 L22 5 L26 18 L15 26 Z"/><g fill="currentColor"><circle cx="5" cy="22" r="2"/><circle cx="9" cy="7" r="2"/><circle cx="22" cy="5" r="2"/><circle cx="26" cy="18" r="2"/><circle cx="15" cy="26" r="2"/></g></svg>',
 rect:'<svg width="30" height="30" viewBox="0 0 30 30" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="8" width="24" height="14"/></svg>',
 square:'<svg width="30" height="30" viewBox="0 0 30 30" fill="none" stroke="currentColor" stroke-width="2"><rect x="6" y="6" width="18" height="18"/></svg>'};
const pbody=$('#pbody');
function maxStep(){return !S.area?0:!S.droneId?1:!S.missionId?2:5}
function renderSteps(){
  const ms=maxStep();
  $('#steps').innerHTML=STEPS.map((n,i)=>`<button type="button" data-step="${i}" class="${i===S.step?'on':i<S.step?'done':''}" ${i>ms?'disabled':''} ${i===S.step?'aria-current="step"':''}><span class="n">${i+1}</span><span class="l">${n}</span></button>`).join('');
  $('#back').style.visibility=S.step===0?'hidden':'visible';
  const nx=$('#next');nx.hidden=S.step===5;nx.disabled=S.step>=ms;
  nx.textContent=['Next: choose drone','Next: choose mission','Next: review settings','Next: control points','Next: summary',''][S.step];
}
function areaStatsHtml(){
  if(!S.area)return `<div class="hint">No area yet. Pick a tool above, or load an example field.</div>`;
  const g=geo();
  return `<div class="stats"><div class="stat"><b>${fmt(g.area/10000,2)} ha</b><span>Area</span></div><div class="stat"><b>${fmt(g.area)} m²</b><span>Area</span></div><div class="stat"><b>${fmt(g.per)} m</b><span>Perimeter</span></div><div class="stat"><b>${S.area.length}</b><span>Corner points</span></div></div><p class="note">Drag the white corner dots on the map to adjust the area.</p>`;
}
function refreshStats(){const el=$('#areastats');if(el)el.innerHTML=areaStatsHtml()}
function stepArea(){
  const t=(m,l)=>`<button type="button" class="tool ${S.mode===m?'on':''}" data-act="tool" data-v="${m}">${ICON[m]}${l}</button>`;
  const hint={polygon:'Click the map to place corner points. Click the first point, double-click or press Enter to close the shape. Backspace removes the last point.',
    rect:'Press and drag on the map, or click two opposite corners.',square:'Press and drag on the map, or click the first corner and then click to set the size.'}[S.mode];
  return `<div><h2>Mission area</h2><p class="lead">Zoom the map to your site, then mark the area the drone should cover.</p></div>
  <div class="tools">${t('polygon','Polygon')}${t('rect','Rectangle')}${t('square','Square')}</div>
  ${S.mode?`<div class="hint">${hint}</div><div class="row">${S.mode==='polygon'&&S.draw.pts.length>=3?'<button type="button" class="btn" data-act="finish">Finish shape</button>':''}<button type="button" class="btn ghost" data-act="cancel">Cancel drawing</button></div>`:''}
  <div id="areastats">${areaStatsHtml()}</div>
  <div class="row">${S.area?'<button type="button" class="btn" data-act="clear">Clear area</button>':''}<button type="button" class="btn ghost" data-act="example">Use an example field here</button></div>`;
}
function stepDrone(){
  return `<div><h2>Choose drone</h2><p class="lead">Camera, photo interval and speed limits come from the aircraft you pick.</p></div>
  <div class="cards">${DRONES.map(d=>`<button type="button" class="card ${d.id===S.droneId?'on':''}" data-act="drone" data-v="${d.id}">
    <h3>${d.name}<small>${d.weight}</small></h3>
    <div class="specs"><span>Sensor <b>${d.sensor}</b></span><span>Max still <b>${d.max}</b></span><span>Lens <b>${d.lens}</b></span><span>Files <b>${d.formats}</b></span>
    <span>Min photo interval <b>${Object.values(d.res).map(r=>r.label.split(' (')[0]+': '+r.int+' s').join(' · ')}</b></span><span>Flight <b>${d.flight} min</b></span></div>
    <p>${d.note}</p></button>`).join('')}</div>`;
}
function stepMission(){
  return `<div><h2>Choose mission</h2><p class="lead">Each mission loads overlap, flight pattern, camera angle, height and speed from photogrammetry practice. You can change all of them next.</p></div>
  <div class="cards">${MISSIONS.map(m=>`<button type="button" class="card ${m.id===S.missionId?'on':''}" data-act="mission" data-v="${m.id}">
    <h3>${m.name}<small>${m.tag}</small></h3><p>${m.desc}</p>
    <div class="specs"><span>Output <b>${m.out}</b></span><span>Height <b>${m.altTxt}</b></span><span>Overlap <b>${m.front} / ${m.side} %</b></span><span>Pattern <b>${m.pat}</b></span></div></button>`).join('')}</div>`;
}
const TF_TXT={off:'Constant height above the take-off point. Best for flat sites. This is the default.',
 warn:'Keeps the planned altitude and flags waypoints where the height above ground drifts too far from the target.',
 follow:'Raises and lowers every waypoint so the drone keeps the selected height above the ground beneath it.'};
function tfSection(s,m){
  const t=S.terr,on=S.tf!=='off',tp=TF_PRESET[m.id];
  const same=tp&&s.height===tp.alt&&s.front===tp.front&&s.side===tp.side&&s.pattern===tp.pat&&s.obl===tp.obl;
  return `<div class="sect"><h3>Terrain following</h3><div class="fields">
   <div class="field full"><label for="f-tf">Mode</label><select id="f-tf" data-g="tf">
    <option value="off" ${S.tf==='off'?'selected':''}>Off (constant altitude)</option>
    <option value="warn" ${S.tf==='warn'?'selected':''}>Warning only</option>
    <option value="follow" ${S.tf==='follow'?'selected':''}>Follow terrain</option></select>
    <small>${TF_TXT[S.tf]}</small></div>
   ${on?`<div class="field full"><label for="f-tsrc">Terrain data</label><select id="f-tsrc" data-t="src">
    <option value="online" ${t.src==='online'?'selected':''}>Online elevation model (about 90 m grid)</option>
    <option value="plane" ${t.src==='plane'?'selected':''}>Manual slope (one tilted plane)</option></select><small id="tfstatus">${terrStatusText()}</small></div>
   ${t.src==='plane'?`<div class="field"><label for="f-z0">Ground at centre (m)</label><input id="f-z0" data-t="z0" type="number" step="0.5" value="${t.plane.z0}"><small>Elevation above sea level.</small></div>
    <div class="field"><label for="f-slope">Slope (%)</label><input id="f-slope" data-t="slope" type="number" min="-60" max="60" step="0.5" value="${t.plane.slope}"><small>10 % = 10 m rise per 100 m.</small></div>
    <div class="field full"><label for="f-dir">Ground rises toward (° from N)</label><input id="f-dir" data-t="dir" type="number" min="0" max="359" step="1" value="${t.plane.dir}"><small>0 = north, 90 = east, 180 = south, 270 = west.</small></div>`:''}
   <div class="field"><label for="f-to">Take-off elevation (m)</label><input id="f-to" data-g="takeoff" type="number" step="0.5" placeholder="auto" value="${S.takeoff==null?'':S.takeoff}"><small id="tohint"></small></div>
   <div class="field"><label for="f-tol">Warn above ±(%)</label><input id="f-tol" data-g="tfTol" type="number" min="3" max="60" step="1" value="${S.tfTol}"><small>Change of height above ground.</small></div>`:''}
  </div>
  ${on?`<div class="row">${t.src==='online'?'<button type="button" class="btn" data-act="tload">Reload elevation</button>':''}${same?'':'<button type="button" class="btn" data-act="reset">Apply sloping-terrain preset</button>'}</div>
  ${tp?`<p class="note">Sloping-terrain starting point for ${m.name}: ${tp.alt} m above ground, overlap ${tp.front} / ${tp.side} %, ${tp.pat==='double'?'double grid':'single grid'}${tp.obl?` + ${tp.obl} oblique orbit${tp.obl>1?'s':''}`:''}. Adjust for terrain complexity, camera resolution and vegetation.</p>`:''}
  <div id="tfprof"></div>`:''}</div>`;
}
function stepSettings(){
  ensureSettings();const s=S.s,d=drone(),m=mission(),g=geo(),circle=s.pattern==='circle';
  const rec=recommend(d,m,g),rm=metrics(rec,g,d);
  const resOpts=Object.entries(d.res).map(([k,r])=>`<option value="${k}" ${s.res===k?'selected':''}>${r.label}${r.w?' · '+r.w+' × '+r.h:''}</option>`).join('');
  const chips=ALT_PRESETS.map(h=>`<button type="button" class="chip ${s.height===h?'on':''}" data-act="alt" data-v="${h}"><b>${h} m</b><span>${gsdAt(d,s.res,h).toFixed(2)} cm/px</span></button>`).join('');
  const an=altNote(s.height);
  const gsdRows=ALT_PRESETS.map(h=>`<tr class="num"><td>${h} m</td>${Object.keys(d.res).map(k=>`<td>${gsdAt(d,k,h).toFixed(2)}</td>`).join('')}</tr>`).join('');
  const notes=m.notes.concat(Object.keys(d.res).length>1?[`12 MP: timed photos from ${d.res['12'].int} s. ${d.res['48'].label}: from ${d.res['48'].int} s. Linear detail of the high-resolution mode is about twice the 12 MP detail at the same height.`]:[]);
  return `<div><h2>Flight settings</h2><p class="lead">Calculated for ${d.name}, ${m.name}. Change anything you want.</p></div>
  <div class="reco"><b>Recommended for ${m.name}</b>
   <ul><li>${patName(rec)}, ${rec.height} m, ${d.res[rec.res].label}, camera ${rec.pitch}°.</li>
   <li>Overlap ${rec.front} / ${rec.side} %, speed ${fmt(rec.speed,1)} m/s, about ${rm.gsd.toFixed(2)} cm/px.</li>
   <li>${m.why(g)}</li>${rec.why?`<li>${rec.why}</li>`:''}</ul></div>
  <div class="sect"><h3>Flight height (above ground, metric)</h3><div class="chips">${chips}</div>
   <p class="note"><b>${an.k} m:</b> ${an.t}</p></div>
  <div class="fields">
    <div class="field full"><label for="f-pattern">Flight pattern</label><select id="f-pattern" data-k="pattern">
      <option value="grid" ${s.pattern==='grid'?'selected':''}>Square flying: single grid</option>
      <option value="double" ${s.pattern==='double'?'selected':''}>Square flying: double grid</option>
      <option value="circle" ${circle?'selected':''}>Circle flying: orbits around the area</option></select></div>
    <div class="field"><label for="f-res">Camera resolution</label><select id="f-res" data-k="res">${resOpts}</select></div>
    <div class="field"><label for="f-height">${S.tf==="off"?"Flight height (m)":"Height above ground (m)"}</label><input id="f-height" data-k="height" type="number" min="10" max="500" step="1" value="${s.height}"></div>
    <div class="field"><label for="f-speed">Speed (m/s)</label><input id="f-speed" data-k="speed" type="number" min="0.5" max="${d.maxMap}" step="0.1" value="${s.speed}"><small id="speedhint"></small></div>
    ${circle?`<div class="field"><label for="f-rings">Orbit rings</label><input id="f-rings" data-k="rings" type="number" min="1" max="3" step="1" value="${s.rings}"><small>Camera aims at the centre.</small></div>`
    :`<div class="field"><label for="f-pitch">Camera angle (°)</label><input id="f-pitch" data-k="pitch" type="number" min="-90" max="-20" step="1" value="${s.pitch}"><small>-90 straight down. -75 to -65 oblique.</small></div>`}
    <div class="field"><label for="f-front">Front overlap (%)</label><input id="f-front" data-k="front" type="number" min="50" max="95" step="1" value="${s.front}"></div>
    <div class="field"><label for="f-side">Side overlap (%)</label><input id="f-side" data-k="side" type="number" min="40" max="95" step="1" value="${s.side}"></div>
    ${circle?'':`<div class="field"><label for="f-bearing">Flight direction (° from N)</label><input id="f-bearing" data-k="bearing" type="number" min="0" max="179" step="1" placeholder="auto" value="${s.bearing==null?'':s.bearing}"><small id="bearhint"></small></div>
    <div class="field"><label for="f-obl">Oblique orbits</label><input id="f-obl" data-k="obl" type="number" min="0" max="3" step="1" value="${s.obl}"><small>0 = nadir grid only.</small></div>
    <div class="field"><label for="f-oblp">Oblique camera angle (°)</label><input id="f-oblp" data-k="oblPitch" type="number" min="-80" max="-30" step="1" value="${s.oblPitch}"><small>-75 to -65 slopes, -60 to -45 façades.</small></div>`}
  </div>
  ${tfSection(s,m)}
  ${safetySection()}
  <div class="sect"><h3>Calculated from these settings</h3><div class="calc" id="calc"></div>
   <p class="note"><b>Click any waypoint on the map to edit it.</b> You can change its altitude, speed, gimbal angle, heading and action, or jump to another waypoint by number.</p>
   <div class="legend"><span><i style="background:#ffd43b"></i>Take picture</span><span><i style="background:#e9ecef"></i>No action</span><span><i style="background:#ff6b6b"></i>Start recording</span><span><i style="background:#9775fa"></i>Stop recording</span><span><i class="ring"></i>Edited</span></div></div>
  <div class="warns" id="warns"></div>
  ${bpSection()}
  <div class="sect"><h3>Ground resolution by height (cm/px)</h3><div class="wptable"><table><thead><tr><th>Height</th>${Object.keys(d.res).map(k=>`<th>${d.res[k].label.split(' (')[0]}</th>`).join('')}</tr></thead><tbody>${gsdRows}</tbody></table></div></div>
  <div class="sect"><h3>Capture notes</h3><ul class="notes">${notes.map(t=>`<li>${t}</li>`).join('')}</ul></div>
  <div class="row"><button type="button" class="btn" data-act="reset">Reset to recommended</button></div>`;
}
function profileSvg(p){
  if(!p.tf)return'';
  const sh=p.shots,n=sh.length,st=Math.max(1,Math.ceil(n/180)),idx=[];
  for(let i=0;i<n;i+=st)idx.push(i);if(idx[idx.length-1]!==n-1)idx.push(n-1);
  const T=p.tf.T,gr=idx.map(i=>sh[i].gz),fl=idx.map(i=>T+sh[i].h),lo0=Math.min(...gr,...fl,T),hi0=Math.max(...gr,...fl,T),pad=Math.max(2,(hi0-lo0)*.1),lo=lo0-pad,hi=hi0+pad;
  const W=340,H=130,L=38,R=6,Tp=8,B=18,X=k=>L+k/Math.max(idx.length-1,1)*(W-L-R),Y=v=>Tp+(hi-v)/(hi-lo)*(H-Tp-B);
  const ground=`M${L},${H-B} `+gr.map((v,k)=>`L${X(k).toFixed(1)},${Y(v).toFixed(1)}`).join(' ')+` L${W-R},${H-B} Z`;
  const line=fl.map((v,k)=>`${X(k).toFixed(1)},${Y(v).toFixed(1)}`).join(' ');
  const dots=idx.map((i,k)=>sh[i].tfFlag?`<circle cx="${X(k).toFixed(1)}" cy="${Y(fl[k]).toFixed(1)}" r="2.6" fill="var(--bad)"/>`:'').join('');
  return `<h3 class="proft">Route profile</h3><svg class="prof" viewBox="0 0 ${W} ${H}" role="img" aria-label="Ground elevation and drone altitude along the route"><path d="${ground}" fill="var(--line)" stroke="var(--muted)" stroke-width="1"/>
   <line x1="${L}" x2="${W-R}" y1="${Y(T).toFixed(1)}" y2="${Y(T).toFixed(1)}" stroke="var(--ok)" stroke-dasharray="4 3"/>
   <polyline points="${line}" fill="none" stroke="var(--route)" stroke-width="2" stroke-linejoin="round"/>${dots}
   <g font-family="IBM Plex Mono,monospace" font-size="9" fill="var(--muted)"><text x="2" y="${Tp+6}">${fmt(hi0,0)} m</text><text x="2" y="${H-B-2}">${fmt(lo0,0)} m</text><text x="${L}" y="${H-4}">Waypoint 1</text><text x="${W-R}" y="${H-4}" text-anchor="end">Waypoint ${fmt(n)}</text></g></svg>
   <div class="legend"><span><i style="background:var(--line);border-color:var(--muted)"></i>Ground</span><span><i style="background:var(--route)"></i>Drone</span><span><i style="background:var(--ok)"></i>Take-off level</span>${p.tf.flagged?'<span><i style="background:var(--bad)"></i>Flagged</span>':''}</div>`;
}
function updateCalc(){
  const el=$('#calc');if(!el||S.step!==3)return;
  const p=currentPlan(),m=p.m,s=S.s;
  el.innerHTML=`<div><span>Ground resolution</span><b class="num">${m.gsd.toFixed(2)} cm/px</b></div>
   <div><span>Image footprint</span><b class="num">${fmt(m.across,1)} × ${fmt(m.along,1)} m</b></div>
   <div><span>${s.pattern==='circle'?'Photo spacing':'Line spacing'}</span><b class="num">${fmt(s.pattern==='circle'?m.photoSp:m.lineSp,1)} m</b></div>
   <div><span>Photo every</span><b class="num">${fmt(m.photoSp,1)} m · ${fmt(m.photoSp/Math.max(s.speed,.1),1)} s</b></div>
   <div><span>Timed interval to set</span><b class="num">${m.timed} s</b></div>
   <div><span>Waypoints / photos</span><b class="num">${fmt(p.shots.length)} / ${fmt(p.photos)}</b></div>
   <div><span>Flight time (all missions)</span><b class="num">${fm(splitPlan(p).air)} min</b></div>
   <div><span>Missions = batteries</span><b class="num">${splitPlan(p).flights.length}</b></div>`;
  if(p.tf){const t=p.tf,gl=m.gsd;
    el.insertAdjacentHTML('beforeend',`<div><span>Height above ground</span><b class="num">${fmt(t.minAgl,0)} to ${fmt(t.maxAgl,0)} m</b></div>
     <div><span>Ground elevation</span><b class="num">${fmt(t.gzMin,0)} to ${fmt(t.gzMax,0)} m</b></div>
     <div><span>Resolution range</span><b class="num">${(gl*t.rmin).toFixed(2)} to ${(gl*t.rmax).toFixed(2)} cm/px</b></div>
     <div><span>Waypoints flagged</span><b class="num">${t.flagged}</b></div>`)}
  const tph=$('#tfprof');if(tph)tph.innerHTML=profileSvg(p);
  bpRefresh();
  const sfe=$('#safe');if(sfe&&document.activeElement!==sfe.querySelector('select'))sfe.innerHTML=safetyHtml(safetyEval(p));
  const toh=$('#tohint');if(toh)toh.textContent=p.tf?(p.tf.auto?'Auto: '+fmt(p.tf.T,1)+' m, ground at Waypoint 1':'Heights are relative to this'):'';
  const sh=$('#speedhint');if(sh)sh.textContent='Max '+fmt(m.safeSpeed,1)+' m/s with a '+m.cam.int+' s minimum interval';
  const bh=$('#bearhint');if(bh&&p.bearing!=null)bh.textContent=(s.bearing==null||s.bearing===''?'Auto: ':'Using: ')+Math.round(p.bearing)+'°';
  $('#warns').innerHTML=warnings(p).map(t=>`<div class="warn">${t}</div>`).join('');
}
function warnings(p){
  const s=S.s,d=drone(),m=mission(),g=geo(),w=[];
  const top=p.tf?p.tf.maxAgl:p.maxH;
  if(top>120)w.push('Flight height is above 120 m'+(p.tf?' above ground':'')+'. Check the altitude limit that applies where you fly.');
  if(S.tf!=='off'&&!p.tf)w.push('Terrain data is not available yet, so heights are constant above the take-off point. '+terrStatusText());
  if(p.tf){const t=p.tf,f=metrics(s,g,drone());
    if(S.terr.src==='online'&&g.R<75)w.push('The online elevation model has a grid of about 90 m, so on a site this small it only shows the general slope. For better accuracy use Manual slope.');
    if(t.mode==='warn'&&t.flagged){const lo=Math.min(t.rmin,1),oF=Math.round(100-(100-s.front)/lo),oS=Math.round(100-(100-s.side)/lo),nF=Math.min(95,Math.ceil(100-(100-s.front)*lo)),nS=Math.min(95,Math.ceil(100-(100-s.side)*lo));
      w.push(`Height above ground ranges from ${fmt(t.minAgl,0)} to ${fmt(t.maxAgl,0)} m on a ${s.height} m plan, and ${t.flagged} waypoint${t.flagged>1?'s differ':' differs'} by more than ${t.tol} %. At the lowest point overlap drops to about ${oF} / ${oS} % and detail changes from ${(f.gsd*t.rmin).toFixed(2)} to ${(f.gsd*t.rmax).toFixed(2)} cm/px. Switch to Follow terrain, or raise overlap to about ${nF} / ${nS} %.`)}
    if(t.mode==='follow'&&t.flagged)w.push(`${t.flagged} edited waypoint${t.flagged>1?'s are':' is'} more than ${t.tol} % away from the target height above ground.`);
    if(t.minRel<0)w.push(`Some waypoints are ${fmt(-t.minRel,0)} m below the take-off point. Take off from the lowest ground, or check that your drone and app accept negative relative heights.`);
    if(t.mode==='follow'&&t.vmax>3)w.push(`The route climbs or descends up to ${fmt(t.vmax,1)} m/s at ${s.speed} m/s (${fmt(t.steep,0)} % grade). Lower the speed so the drone can follow the terrain.`);
    if(t.mode==='follow')w.push('Follow terrain: the KMZ stores heights relative to the take-off point. Take off at '+fmt(t.T,1)+' m above sea level, or enter your real take-off elevation above.');
  }
  const fast=p.shots.filter(x=>x.edited&&x.action==='photo'&&x.speed>p.m.safeSpeed+.05).length;
  if(fast)w.push(`${fast} edited waypoint${fast>1?'s are':' is'} faster than ${fmt(p.m.safeSpeed,1)} m/s, so photos there will be spaced wider than planned.`);
  if(p.shots.some(x=>x.edited&&(x.agl!=null?x.agl:x.h)>120))w.push('An edited waypoint is above 120 m. Check the altitude limit that applies where you fly.');
  if(s.speed>p.m.safeSpeed+.05)w.push(`Speed is above ${fmt(p.m.safeSpeed,1)} m/s. The camera cannot keep up, so photos will be spaced wider than the overlap you set. Lower the speed, lower the overlap, fly higher or use 12 MP.`);
  if(s.pattern!=='circle'&&s.pitch>-85)w.push('Calculations assume a straight-down camera. At this angle the real overlap and resolution will differ.');
  if(s.pattern!=='circle'&&s.obl>0&&g.R>120)w.push('The oblique orbit goes around the whole area, so it is long for a site this size. Set oblique orbits to 0 or split the site.');
  if(m.id==='dtm'||m.id==='dem')w.push('No lidar and no GNSS base station: use ground control points and checkpoints. Under dense vegetation the model will show the canopy, not the ground.');
  {const sp=splitPlan(p);if(sp.flights.length>1)w.push(`This mission is split into ${sp.flights.length} missions, one per battery. Mission 2 continues where mission 1 ended, and each mission has its own file.`)}
  if(p.photos>2000)w.push(`${fmt(p.photos)} photos is a lot to process. Consider 12 MP, a higher flight or less overlap.`);
  if(g.area>1000000)w.push('This area is over 100 ha. Consider splitting it into several missions.');
  return w;
}
function stepSummary(){
  const p=currentPlan(),s=S.s,d=drone(),m=mission(),g=geo();
  const c=g.toLL(g.c.x,g.c.y);
  const sp=splitPlan(p),mins=sp.air/60;
  const bars=sp.flights.map(f=>`<i class="part"><u style="width:${clamp(f.energy/(sp.usable*60)*100,5,100)}%"></u></i>`).join('');
  const rows=p.shots.map((w,i)=>`<tr class="num ${w.edited?'ed':''}" data-act="goto" data-i="${i}"><td>Waypoint ${i+1}${w.edited?' ✎':''}</td><td>${w.lat.toFixed(6)}</td><td>${w.lng.toFixed(6)}</td><td>${w.h}</td><td>${fmt(w.speed,1)}</td><td>${w.pitch}</td><td>${Math.round(w.heading)}</td><td>${(ACTIONS.find(a=>a[0]===w.action)||[])[1]}</td></tr>`).join('');
  return `<div><h2>Mission summary</h2><p class="lead">Dots on the map are the waypoints, numbered in flight order. Click one to see or change it. S is the start, E is the end.</p></div>
  <div class="sect sfsec"><h3>Safety &amp; Airspace Check</h3><div id="safe">${safetyHtml(safetyEval(p),true)}</div>
   <label class="ack"><input type="checkbox" data-ack="1" ${S.safe.ack?'checked':''}><span>I understand this check is a planning aid only. I will check current official airspace information and look at the site before flying.</span></label></div>
  <div class="sect"><h3>Location</h3><div class="place" id="place">${S.place||'Selected area'}</div>
   <div class="num note">${c.lat.toFixed(5)}, ${c.lng.toFixed(5)} · ${fmt(g.area/10000,2)} ha · ${fmt(g.per)} m perimeter</div></div>
  <div class="sect"><h3>Mission</h3><div class="calc"><div><span>Drone</span><b>${d.name}</b></div><div><span>Type</span><b>${m.name}</b></div><div><span>Pattern</span><b>${patName(s)}</b></div><div><span>Camera</span><b class="num">${d.res[s.res].label}, ${s.pitch}°</b></div><div><span>Height</span><b class="num">${s.height} m${p.tf?' above ground':''}</b></div>${p.tf?`<div><span>Terrain following</span><b>${p.tf.follow?'Follow terrain':'Warning only'}</b></div><div><span>Take-off elevation</span><b class="num">${fmt(p.tf.T,1)} m</b></div><div><span>Ground range</span><b class="num">${fmt(p.tf.gzMin,0)} to ${fmt(p.tf.gzMax,0)} m</b></div><div><span>Height above ground</span><b class="num">${fmt(p.tf.minAgl,0)} to ${fmt(p.tf.maxAgl,0)} m</b></div>`:''}<div><span>Speed</span><b class="num">${fmt(s.speed,1)} m/s</b></div><div><span>Overlap</span><b class="num">${s.front} / ${s.side} %</b></div><div><span>Resolution</span><b class="num">${p.m.gsd.toFixed(2)} cm/px</b></div><div><span>Photo every</span><b class="num">${fmt(p.m.photoSp,1)} m</b></div><div><span>Timed interval</span><b class="num">${p.m.timed} s</b></div></div></div>
  <div class="stats"><div class="stat"><b>${fmt(mins,0)} min</b><span>Total flight time</span></div><div class="stat"><b>${fmt(p.photos)}</b><span>Photo waypoints</span></div>
   <div class="stat"><b>${sp.flights.length}</b><span>Missions = batteries</span></div><div class="stat"><b>${fmt(sp.onSite/60,0)} min</b><span>Total on site</span></div><div class="stat"><b>${fmt(p.dist/1000,2)} km</b><span>Route length</span></div>
   <div class="stat"><b>${fmt(kmzPoints(p,s).pts.length)}</b><span>Path points in KMZ${p.nEdited?` · ${p.nEdited} edited`:''}</span></div><div class="stat"><b>≈ ${fmt(p.storageGB,1)} GB</b><span>Card space</span></div>
   <div class="stat wide"><span>Battery use per flight (of usable)</span><div class="batt">${bars}</div></div></div>
  <div class="warns">${warnings(p).map(t=>`<div class="warn">${t}</div>`).join('')}</div>
  <div class="sect bpsec"><h3>Missions (${sp.flights.length})</h3>${bpHtml(sp,true)}</div>
  <div class="sect"><h3>Photo waypoints (${fmt(p.shots.length)})</h3><div class="wptable tall"><table><thead><tr><th>Waypoint</th><th>Lat</th><th>Lng</th><th>Alt m</th><th>m/s</th><th>Gimbal °</th><th>Heading °</th><th>Action</th></tr></thead><tbody>${rows}</tbody></table></div><p class="note">Select a row to centre the map on that waypoint.</p></div>
  <button type="button" id="dlbtn" class="btn primary big" data-act="download" data-needack="1" ${S.safe.ack?'':'disabled'}>${sp.flights.length>1?`Download all ${sp.flights.length} missions (.zip, numbered 1 to ${sp.flights.length})`:'Download mission (.kmz)'}</button>
  <p class="note">Heights in each file are relative to that mission's take-off point, so take off exactly where the marker is. Check the route against obstacles before flying.</p>`;
}
function renderPanel(keep){
  const st=pbody.scrollTop;renderSteps();
  pbody.innerHTML=[stepArea,stepDrone,stepMission,stepSettings,stepControl,stepSummary][S.step]();
  if(S.step===3)updateCalc();
  ensureTerrain();ensureSafety();
  pbody.scrollTop=keep?st:0;if(S.step<3||S.step===4)S.sel=null;drawOverlay();renderDlg();
}
function go(n){
  n=clamp(n,0,maxStep());if(n===S.step)return;
  if(S.mode)cancelTool();
  if(S.cp.add)cpSetAdd(false);S.step=n;if(n<3||n===4)S.sel=null;if(n>=3)ensureSettings();
  renderPanel();
  if(n===5){fitPoints(currentPlan().wps.concat(S.area),70);reverseGeocode()}
  else if(n>=3&&S.area)fitPoints(S.area.concat(currentPlan().wps),Math.min(70,V.w/6));
}
$('#back').onclick=()=>go(S.step-1);
$('#next').onclick=()=>go(S.step+1);
$('#steps').addEventListener('click',e=>{const b=e.target.closest('[data-step]');if(b&&!b.disabled)go(+b.dataset.step)});

pbody.addEventListener('click',e=>{
  const t=e.target.closest('[data-act]');if(!t)return;const v=t.dataset.v;
  switch(t.dataset.act){
    case'tool':S.mode===v?cancelTool():startTool(v);break;
    case'finish':finishArea(S.draw.pts.slice());break;
    case'cancel':cancelTool();break;
    case'clear':cpReset();S.area=null;S.s=null;S.dirty=false;planCache.key='';renderPanel();break;
    case'example':makeExample();break;
    case'drone':S.droneId=v;S.dirty=false;S.s=null;renderPanel();break;
    case'mission':S.missionId=v;S.dirty=false;S.s=null;renderPanel();break;
    case'reset':S.dirty=false;S.s=null;ensureSettings();renderPanel();break;
    case'cpmode':S.cp.mode=v;if(v==='manual')S.cp.n=S.cp.items.length||5;S.cp.sig='';renderPanel(true);break;
    case'cpdec':cpSetN(S.cp.items.length-1);break;
    case'cpinc':cpSetN(S.cp.items.length+1);break;
    case'cpadd':cpSetAdd(!S.cp.add);break;
    case'cprenum':cpRenumber();break;
    case'cpreset':cpResetSuggested();break;
    case'cpsel':if(S.cp.meas.on)cpTogglePick(+t.dataset.id);else S.cp.sel=+t.dataset.id;renderPanel(true);break;
    case'cpmeas':S.cp.meas.on=!S.cp.meas.on;if(S.cp.meas.on&&S.cp.add)cpSetAdd(false);mapEl.classList.toggle('drawing',S.cp.meas.on||!!S.mode||S.obsMode);renderPanel(true);break;
    case'cpmtype':S.cp.meas.type=v;renderPanel(true);break;
    case'cpmmode':S.cp.meas.mode=v;renderPanel(true);break;
    case'cpmclear':S.cp.meas.picks=[];renderPanel(true);break;
    case'cplock':{const it=S.cp.items.find(i=>i.id===+t.dataset.id);if(it){const cur=it.lock||it.man;it.lock=!cur;it.man=false;renderPanel(true)}break}
    case'cpdel':cpDelete(+t.dataset.id);break;
    case'tload':loadTerrain(true);break;
    case'bpunit':S.bp.unit=S.bp.unit==='ha'?'m2':'ha';renderPanel(true);break;
    case'bparea':S.bp.areaMan=null;S.bp.cache=null;renderPanel(true);break;
    case'bpshow':{const sp=splitPlan(currentPlan()),f=sp.flights[+t.dataset.i];if(f){const pts=currentPlan().shots.slice(f.a,f.b).concat([f.L]);fitPoints(pts,70)}break}
    case'bplaunch':S.launchPick=S.launchPick===+t.dataset.i?null:+t.dataset.i;setLaunchBanner();bpUpdate();break;
    case'bpauto':if(S.bp.lmode==='single')delete S.bp.launch.all;else delete S.bp.launch[+t.dataset.i];S.bp.cache=null;bpUpdate();break;
    case'dlflight':downloadKmz(+t.dataset.i);break;
    case'sfload':loadObs(true);loadAir(true);S.safe.ccSig='';detectCountry();break;
    case'sfobs':setObsMode(!S.obsMode);break;
    case'sfdel':S.safe.manual.splice(+t.dataset.i,1);if(S.step===3)renderPanel(true);safeUpdated();break;
    case'sfgoto':{selectShot(+t.dataset.i,true);const w=currentPlan().shots[+t.dataset.i];if(w)setView(w.lat,w.lng,Math.max(V.zoom,17.5));break}
    case'sfair':setView(+t.dataset.lat,+t.dataset.lng,Math.min(V.zoom,13));break;
    case'download':downloadKmz();break;
    case'alt':{S.s.height=+v;S.dirty=true;afterEdit('height');renderPanel(true);break}
    case'goto':{const w=currentPlan().shots[+t.dataset.i];if(w){selectShot(+t.dataset.i);setView(w.lat,w.lng,Math.max(V.zoom,18.5))}break}
  }
});
function makeExample(){
  if(S.mode)cancelTool();
  const w=Math.min(V.w,V.h)*.32,pts=[[-w*1.3,-w*.8],[w*1.3,-w*.8],[w*1.3,w*.8],[-w*1.3,w*.8]].map(q=>fromScreen(V.w/2+q[0],V.h/2+q[1]));
  if(V.zoom<15||V.zoom>17.5)setView(V.lat,V.lng,16.2);
  const z=V.zoom;const c=proj(V.lat,V.lng,z);
  finishArea([[-210,-120],[210,-120],[210,120],[-210,120]].map(([dx,dy])=>{ // metres around the view centre
    const mpp=156543.03392*Math.cos(V.lat*RAD)/2**z;return unproj(c.x+dx/mpp,c.y-dy/mpp,z)}));
}
pbody.addEventListener('input',e=>{
  const el=e.target.closest('[data-k]');if(!el||el.tagName==='SELECT')return;
  const k=el.dataset.k,s=S.s;let v=el.value===''?null:parseFloat(el.value);
  if(k==='bearing'){s.bearing=v==null?null:clamp(v,0,179)}
  else if(v==null||isNaN(v))return;
  else s[k]=v;
  S.dirty=true;afterEdit(k);
});
pbody.addEventListener('change',e=>{
  const el=e.target.closest('[data-k]');if(!el||el.tagName!=='SELECT')return;
  const k=el.dataset.k,s=S.s;S.dirty=true;
  s[k]=el.value;
  if(k==='pattern'&&s.pattern==='circle'&&s.height<15)s.height=15;
  afterEdit(k);renderPanel(true);
});
pbody.addEventListener('input',e=>{
  const el=e.target.closest('[data-g],[data-t]');if(!el||el.tagName==='SELECT')return;
  let v=el.value===''?null:parseFloat(el.value);
  if(el.dataset.g){const k=el.dataset.g;if(k==='takeoff')S.takeoff=v==null||isNaN(v)?null:v;else if(v==null||isNaN(v))return;else S[k]=clamp(v,3,60);}
  else{if(v==null||isNaN(v))return;const k=el.dataset.t,p=S.terr.plane;p[k]=k==='slope'?clamp(v,-60,60):k==='dir'?((v%360)+360)%360:v}
  afterEdit('terr');
});
pbody.addEventListener('change',e=>{
  const el=e.target.closest('select[data-g],select[data-t]');if(!el)return;
  if(el.dataset.g==='tf'){setTf(el.value)}
  else if(el.dataset.t==='src'){S.terr.src=el.value;S.terr.ver++;planCache.key='';ensureTerrain();renderPanel(true);afterEdit('terr')}
});
function setObsMode(b){
  S.obsMode=b;mapEl.classList.toggle('drawing',b||!!S.mode);
  const bn=$('#banner');if(b){bn.textContent='Click the map to place an obstacle · Esc to cancel';bn.hidden=false}else updateBanner();
  if(S.step===3)renderPanel(true);
}
function addManual(l){
  const g=fromScreen(l.x,l.y);S.safe.manual.push({id:'m'+Date.now(),lat:g.lat,lng:g.lng,h:30,r:5});
  setObsMode(false);safeUpdated();
}
pbody.addEventListener('input',e=>{
  const el=e.target;if(!el.dataset)return;
  if(el.dataset.sm){const k=el.dataset.sm;if(k==='margin'){const v=parseFloat(el.value);if(isNaN(v))return;S.safe.margin=clamp(v,0,200);const need=Math.max(100,Math.ceil(S.safe.margin/50)*50);S.safe.ver++;if(S.safe.obs.key&&!S.safe.obs.key.endsWith('|'+need))loadObs(true);else safeUpdated()}
    else{S.safe.obstH=el.value===''?'':clamp(parseFloat(el.value)||0,0,500);safeUpdated()}}
  else if(el.dataset.mf){const m=S.safe.manual[+el.dataset.mi],v=parseFloat(el.value);if(m&&!isNaN(v)){m[el.dataset.mf]=clamp(v,0,500);safeUpdated()}}
});
pbody.addEventListener('change',e=>{
  const el=e.target;if(!el.dataset)return;
  if(el.dataset.so){S.safe.o[el.dataset.so]=el.checked;S.safe.ver++;const em=el.parentNode.querySelector('em');if(em)em.textContent=el.checked?'ON':'OFF';
    if(el.dataset.so==='terrain')ensureTerrain();ensureSafety();safeUpdated()}
  else if(el.dataset.cc){S.safe.cc=el.value;if(!el.value)S.safe.cc='';safeUpdated()}
  else if(el.dataset.ack){S.safe.ack=el.checked;document.querySelectorAll('[data-needack]').forEach(b=>{b.disabled=!el.checked})}
});
pbody.addEventListener('input',e=>{
  const el=e.target;if(!el.dataset||!el.dataset.bp)return;const k=el.dataset.bp,bp=S.bp;let v=parseFloat(el.value);
  if(isNaN(v))return;
  if(k==='cap'){bp.cap=clamp(v,20,100);bp.usableMan=null;$('#bp-cap-o').textContent=bp.cap+' %';const u=$('#bp-use');if(u)u.value=fmt(bpUsable(),1)}
  else if(k==='reserve'){bp.reserve=clamp(v,10,40);bp.usableMan=null;$('#bp-res-o').textContent=bp.reserve+' %';const u=$('#bp-use');if(u)u.value=fmt(bpUsable(),1)}
  else if(k==='usable')bp.usableMan=clamp(v,3,120);
  else if(k==='wind')bp.wind=clamp(v,0,80);
  else if(k==='swap')bp.swap=clamp(v,0,30);
  else if(k==='area')bp.areaMan=Math.max(0,bp.unit==='ha'?v*10000:v);
  bp.cache=null;bpUpdate();
});
pbody.addEventListener('change',e=>{const el=e.target;if(el.dataset&&el.dataset.cpf){if(el.dataset.cpf==='on'){S.cp.on=el.checked;S.cp.sig='';renderPanel(true)}else if(el.dataset.cpf==='n')cpSetN(parseFloat(el.value));return}
  if(el.dataset&&el.dataset.bpm==='lmode'){S.bp.lmode=el.value;S.bp.launch={};S.bp.cache=null;bpUpdate()}});
function setTf(v){
  S.tf=v;planCache.key='';
  if(!S.dirty)S.s=recommend(drone(),mission(),geo());
  ensureTerrain();renderPanel(true);afterEdit('pattern');
}
function afterEdit(k){
  const s=S.s,d=drone(),g=geo(),m=mission();
  if(k==='height'||k==='res'||k==='pattern')s.speed=pickSpeed(d,m,g,s);
  else if(k==='front'||k==='side'){const sp=metrics(s,g,d).safeSpeed;if(s.speed>sp)s.speed=Math.max(.5,Math.floor(sp*10)/10)}
  planCache.key='';currentPlan();updateCalc();drawOverlay();
  if(['pattern','rings','obl','oblPitch'].includes(k))fitPoints(S.area.concat(currentPlan().wps),Math.min(70,V.w/6));
  if(['height','res','front','side','pattern'].includes(k)){const f=$('#f-speed');if(f&&document.activeElement!==f)f.value=s.speed}
}
$('#shotinfo').addEventListener('click',e=>{if(e.target.closest('[data-lbl]')){S.lblAll=!S.lblAll;drawOverlay()}});
/* ---------- waypoint editor ---------- */
var dlg=$('#wpdlg');
dlg.addEventListener('pointerdown',e=>e.stopPropagation());dlg.addEventListener('wheel',e=>e.stopPropagation(),{passive:true});dlg.addEventListener('dblclick',e=>e.stopPropagation());
function selShot(){const p=S.sel==null?null:currentPlan();return p&&p.shots[S.sel]?{p,x:p.shots[S.sel]}:null}
function nearShot(l){
  if(S.step<3||!S.area||!S.s||S.mode||S.obsMode||S.launchPick!=null)return-1;
  const p=currentPlan();if(!p)return-1;let best=-1,bd=1e9;
  p.shots.forEach((x,i)=>{const s=toScreen(x),d=Math.hypot(s.x-l.x,s.y-l.y);if(d<bd){bd=d;best=i}});
  return bd<=12?best:-1;
}
function pickShot(l){
  const i=nearShot(l);
  if(i>=0){S.sel=i;renderDlg();drawOverlay()}
  else if(S.sel!=null){S.sel=null;renderDlg();drawOverlay()}
}
function selectShot(i,pan){
  const p=currentPlan();if(!p)return;
  S.sel=clamp(Math.round(i),0,p.shots.length-1);renderDlg();
  const s=toScreen(p.shots[S.sel]);
  if(pan&&(s.x<30||s.y<60||s.x>V.w-30||s.y>V.h-30))setView(p.shots[S.sel].lat,p.shots[S.sel].lng,V.zoom);else drawOverlay();
}
function dlgField(id,label,unit,k,x,min,max,step,hint){
  return `<div class="df"><label for="${id}">${label}</label><div class="ig"><input id="${id}" data-f="${k}" type="number" min="${min}" max="${max}" step="${step}" value="${k==='heading'?Math.round(x[k]):x[k]}"><span>${unit}</span></div><small id="${id}-h">${hint}</small></div>`;
}
function dlgHints(x,d){
  const df=x.def,ch=(k,u,dp)=>Math.abs(x[k]-df[k])>(k==='heading'?.5:1e-9)?`Plan: ${fmt(df[k],dp)} ${u}`:'';
  return{h:ch('h','m',0),speed:ch('speed','m/s',1),pitch:ch('pitch','°',0),
    heading:x.hdSet?`Route: ${Math.round(df.heading)}°`:(x.kind==='orbit'?'Auto: faces the centre':'Auto: follows the route')};
}
function renderDlg(){
  const c=selShot();
  if(!c||S.step<3){dlg.hidden=true;return}
  const{p,x}=c,i=S.sel,N=p.shots.length,d=drone(),hn=dlgHints(x,d);
  dlg.innerHTML=`<div class="dh"><b>Waypoint ${i+1}</b><span>of ${fmt(N)} · ${x.kind==='orbit'?'oblique orbit':'grid line'}${(()=>{const f=flightOf(i);return f&&splitPlan(p).flights.length>1?' · Flight '+f.k:''})()}</span><button type="button" class="x" data-dx="close" aria-label="Close waypoint editor">×</button></div>
  <div class="df"><label for="wd-n">Waypoint number</label><div class="nav"><button type="button" data-dx="prev" aria-label="Previous waypoint">‹</button><input id="wd-n" data-f="n" type="number" min="1" max="${N}" step="1" value="${i+1}"><button type="button" data-dx="next" aria-label="Next waypoint">›</button></div></div>
  ${x.gz!=null?`<div class="tfinfo" id="wd-tf"></div>`:''}
  ${dlgField('wd-h','Altitude','meters','h',x,-200,900,1,hn.h)}
  ${dlgField('wd-speed','Speed','meters/s','speed',x,.5,d.maxMap,.1,hn.speed)}
  ${dlgField('wd-pitch','Gimbal angle','degrees','pitch',x,-90,30,1,hn.pitch)}
  ${dlgField('wd-heading','Heading','degrees north','heading',x,0,360,1,hn.heading)}
  <div class="df"><label for="wd-action">Action</label><select id="wd-action" data-f="action">${ACTIONS.map(([k,l])=>`<option value="${k}" ${x.action===k?'selected':''}>${l}</option>`).join('')}</select><small id="wd-action-h">${x.action!==x.def.action?'Plan: Take Picture':''}</small></div>
  <div class="dfoot"><button type="button" class="btn" data-dx="reset" ${x.edited?'':'disabled'}>Reset waypoint</button><button type="button" class="btn primary" data-dx="close">Done</button></div>`;
  dlg.hidden=false;const tl=$('#wd-tf');if(tl)tl.textContent=tfLine(x);positionDlg();
}
function positionDlg(){
  if(!dlg||dlg.hidden)return;const c=selShot();if(!c)return;
  if(window.matchMedia('(max-width:760px)').matches)return;
  const p=toScreen(c.x),w=dlg.offsetWidth||272,h=dlg.offsetHeight||380;
  let left=p.x+20;if(left+w>V.w-8)left=p.x-w-20;left=clamp(left,8,Math.max(8,V.w-w-8));
  dlg.style.left=left+'px';dlg.style.top=clamp(p.y-50,56,Math.max(56,V.h-h-52))+'px';
}
function tfLine(x){return x.gz==null?'':`Ground ${fmt(x.gz,1)} m · height above ground ${fmt(x.agl,1)} m${x.tfFlag?' ⚠':''}`}
function refreshDlgMeta(){
  const c=selShot();if(!c||dlg.hidden)return;const{x}=c,hn=dlgHints(x,drone());
  const tl=$('#wd-tf');if(tl)tl.textContent=tfLine(x);
  for(const k of ['h','speed','pitch','heading']){const el=$('#wd-'+k+'-h');if(el)el.textContent=hn[k]}
  const a=$('#wd-action-h');if(a)a.textContent=x.action!==x.def.action?'Plan: Take Picture':'';
  const r=dlg.querySelector('[data-dx=reset]');if(r)r.disabled=!x.edited;
}
function setEdit(k,v){
  const c=selShot();if(!c)return;const df=c.x.def[k],i=S.sel;
  const o=S.edits[i]||(S.edits[i]={});
  if(k==='heading'?Math.abs(v-df)<.5:v===df)delete o[k];else o[k]=v;
  if(!Object.keys(o).length)delete S.edits[i];
  currentPlan();drawOverlay();refreshDlgMeta();if(S.step===3)updateCalc();
}
dlg.addEventListener('input',e=>{
  const f=e.target.dataset&&e.target.dataset.f;if(!f||f==='n'||f==='action')return;
  let v=parseFloat(e.target.value);if(isNaN(v))return;
  const lim={h:[-200,900],speed:[.5,drone().maxMap],pitch:[-90,30],heading:[0,360]}[f];
  if(v<lim[0]||v>lim[1])return;
  if(f==='heading')v=v%360;
  setEdit(f,v);
});
dlg.addEventListener('change',e=>{
  const f=e.target.dataset&&e.target.dataset.f;
  if(f==='action')setEdit('action',e.target.value);
  else if(f==='n'){const v=parseFloat(e.target.value);if(!isNaN(v))selectShot(v-1,true)}
});
dlg.addEventListener('click',e=>{
  const b=e.target.closest('[data-dx]');if(!b)return;
  switch(b.dataset.dx){
    case'close':S.sel=null;renderDlg();drawOverlay();break;
    case'prev':selectShot(S.sel-1,true);break;
    case'next':selectShot(S.sel+1,true);break;
    case'reset':delete S.edits[S.sel];currentPlan();renderDlg();drawOverlay();if(S.step===3)updateCalc();break;
  }
});
dlg.addEventListener('keydown',e=>{if(e.key==='Escape'){S.sel=null;renderDlg();drawOverlay()}});
async function reverseGeocode(){
  S.place=null;const g=geo(),c=g.toLL(g.c.x,g.c.y);
  try{
    const r=await fetch(`https://nominatim.openstreetmap.org/reverse?format=jsonv2&zoom=16&lat=${c.lat}&lon=${c.lng}`);
    const j=await r.json();
    if(j&&j.display_name){S.place=j.display_name.split(',').slice(0,3).join(',');const el=$('#place');if(el&&S.step===5)el.textContent=S.place}
  }catch(e){}
}

/* ---------- KMZ export (DJI WPML) ---------- */
const esc=s=>String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;');
const N=(v,dp=8)=>Number(v).toFixed(dp);
const STOP='toPointAndStopWithDiscontinuityCurvature',PASS='toPointAndPassWithContinuityCurvature';
/* KMZ waypoints: untouched grid lines stay two points with a distance-based photo trigger; lines with an edited waypoint, and all orbit points, become explicit waypoints */
function kmzPoints(p,s,fl){
  const pts=[],SH=p.shots,dh=fl?fl.dh:0;
  p.segs.forEach(sg=>{
    if(fl&&(sg.s0<fl.a||sg.s0>=fl.b))return;
    const ss=SH.slice(sg.s0,sg.s0+sg.n);
    if(!ss.some(x=>x.edited)){
      const a=p.wps[sg.i0],b=p.wps[sg.i1];
      if(p.tf&&p.tf.follow){const keep=dpKeep(ss,1);
        keep.forEach((k,j)=>{const x=ss[k];pts.push({lat:x.lat,lng:x.lng,h:x.h+dh,speed:null,head:{mode:'follow'},turn:(j===0||j===keep.length-1)?STOP:PASS,pitch:s.pitch,kind:j===0?'segStart':'segMid',step:sg.step,span:keep.length-1})})}
      else{
      pts.push({lat:a.lat,lng:a.lng,h:s.height+dh,speed:null,head:{mode:'follow'},turn:STOP,pitch:s.pitch,kind:'segStart',step:sg.step,span:1});
      pts.push({lat:b.lat,lng:b.lng,h:s.height+dh,speed:null,head:{mode:'follow'},turn:STOP,pitch:s.pitch,kind:'segEnd'});}
    }else ss.forEach((x,k)=>pts.push({lat:x.lat,lng:x.lng,h:x.h+dh,speed:x.speed,head:x.hdSet?{mode:'fixed',angle:x.heading}:{mode:'follow'},turn:(k===0||k===ss.length-1)?STOP:PASS,pitch:x.pitch,action:x.action,kind:'exp'}));
  });
  SH.forEach((x,ix)=>{if(x.kind==='orbit'&&(!fl||(ix>=fl.a&&ix<fl.b)))pts.push({lat:x.lat,lng:x.lng,h:x.h+dh,speed:x.speed,head:x.hdSet?{mode:'fixed',angle:x.heading}:{mode:'poi'},turn:PASS,pitch:x.pitch,action:x.action,kind:'exp'})});
  const groups=new Map();let id=0,lastPitch=null;
  const add=(i,xml)=>{if(!groups.has(i))groups.set(i,[]);groups.get(i).push(xml)};
  const gimbal=(a,pitch)=>`<wpml:action><wpml:actionId>${a}</wpml:actionId><wpml:actionActuatorFunc>gimbalRotate</wpml:actionActuatorFunc><wpml:actionActuatorFuncParam><wpml:gimbalHeadingYawBase>north</wpml:gimbalHeadingYawBase><wpml:gimbalRotateMode>absoluteAngle</wpml:gimbalRotateMode><wpml:gimbalPitchRotateEnable>1</wpml:gimbalPitchRotateEnable><wpml:gimbalPitchRotateAngle>${pitch}</wpml:gimbalPitchRotateAngle><wpml:gimbalRollRotateEnable>0</wpml:gimbalRollRotateEnable><wpml:gimbalRollRotateAngle>0</wpml:gimbalRollRotateAngle><wpml:gimbalYawRotateEnable>0</wpml:gimbalYawRotateEnable><wpml:gimbalYawRotateAngle>0</wpml:gimbalYawRotateAngle><wpml:gimbalRotateTimeEnable>0</wpml:gimbalRotateTimeEnable><wpml:gimbalRotateTime>0</wpml:gimbalRotateTime><wpml:payloadPositionIndex>0</wpml:payloadPositionIndex></wpml:actionActuatorFuncParam></wpml:action>`;
  const fn=(a,name)=>`<wpml:action><wpml:actionId>${a}</wpml:actionId><wpml:actionActuatorFunc>${name}</wpml:actionActuatorFunc><wpml:actionActuatorFuncParam><wpml:payloadPositionIndex>0</wpml:payloadPositionIndex></wpml:actionActuatorFuncParam></wpml:action>`;
  const grp=(i0,i1,trigType,trigParam,actions)=>`<wpml:actionGroup><wpml:actionGroupId>${id++}</wpml:actionGroupId><wpml:actionGroupStartIndex>${i0}</wpml:actionGroupStartIndex><wpml:actionGroupEndIndex>${i1}</wpml:actionGroupEndIndex><wpml:actionGroupMode>sequence</wpml:actionGroupMode><wpml:actionTrigger><wpml:actionTriggerType>${trigType}</wpml:actionTriggerType>${trigParam!=null?`<wpml:actionTriggerParam>${N(trigParam,1)}</wpml:actionTriggerParam>`:''}</wpml:actionTrigger>${actions}</wpml:actionGroup>`;
  const FN={photo:'takePhoto',startRec:'startRecord',stopRec:'stopRecord'};
  pts.forEach((q,i)=>{
    if(q.kind==='segStart'){
      if(lastPitch!==q.pitch){add(i,grp(i,i,'reachPoint',null,gimbal(0,q.pitch)));lastPitch=q.pitch}
      add(i,grp(i,i+(q.span||1),'multipleDistance',q.step,fn(0,'takePhoto')));
    }else if(q.kind==='exp'){
      const acts=[];
      if(lastPitch!==q.pitch){acts.push(gimbal(acts.length,q.pitch));lastPitch=q.pitch}
      if(FN[q.action])acts.push(fn(acts.length,FN[q.action]));
      if(acts.length)add(i,grp(i,i,'reachPoint',null,acts.join('')));
    }
  });
  return{pts,groups};
}
function buildKml(fl){
  const p=currentPlan(),s=S.s,d=drone(),now=Date.now(),{pts,groups}=kmzPoints(p,s,fl);
  const g=geo(),c=g.toLL(g.c.x,g.c.y),gturn=pts.some(q=>q.turn===STOP)?STOP:PASS;
  const headXml=h=>{
    if(h.mode==='poi')return `<wpml:waypointHeadingMode>towardPOI</wpml:waypointHeadingMode><wpml:waypointHeadingAngle>0</wpml:waypointHeadingAngle><wpml:waypointPoiPoint>${N(c.lat,6)},${N(c.lng,6)},0.000000</wpml:waypointPoiPoint><wpml:waypointHeadingAngleEnable>0</wpml:waypointHeadingAngleEnable><wpml:waypointHeadingPathMode>followBadArc</wpml:waypointHeadingPathMode>`;
    if(h.mode==='fixed'){const a=Math.round(((h.angle+180)%360+360)%360-180);
      return `<wpml:waypointHeadingMode>fixed</wpml:waypointHeadingMode><wpml:waypointHeadingAngle>${a}</wpml:waypointHeadingAngle><wpml:waypointPoiPoint>0.000000,0.000000,0.000000</wpml:waypointPoiPoint><wpml:waypointHeadingAngleEnable>1</wpml:waypointHeadingAngleEnable><wpml:waypointHeadingPathMode>followBadArc</wpml:waypointHeadingPathMode>`}
    return `<wpml:waypointHeadingMode>followWayline</wpml:waypointHeadingMode><wpml:waypointHeadingAngle>0</wpml:waypointHeadingAngle><wpml:waypointPoiPoint>0.000000,0.000000,0.000000</wpml:waypointPoiPoint><wpml:waypointHeadingAngleEnable>0</wpml:waypointHeadingAngleEnable><wpml:waypointHeadingPathMode>followBadArc</wpml:waypointHeadingPathMode>`;
  };
  const turnXml=q=>`<wpml:waypointTurnParam><wpml:waypointTurnMode>${q.turn}</wpml:waypointTurnMode><wpml:waypointTurnDampingDist>2</wpml:waypointTurnDampingDist></wpml:waypointTurnParam>`;
  const rth=fl?Math.round(fl.rth):Math.min(Math.max(p.maxH+20,60),500);
  const mission_cfg=`<wpml:missionConfig><wpml:flyToWaylineMode>safely</wpml:flyToWaylineMode><wpml:finishAction>goHome</wpml:finishAction><wpml:exitOnRCLost>executeLostAction</wpml:exitOnRCLost><wpml:executeRCLostAction>goBack</wpml:executeRCLostAction><wpml:takeOffSecurityHeight>20</wpml:takeOffSecurityHeight><wpml:globalTransitionalSpeed>${N(Math.min(s.speed+2,d.maxMap),1)}</wpml:globalTransitionalSpeed><wpml:globalRTHHeight>${rth}</wpml:globalRTHHeight><wpml:droneInfo><wpml:droneEnumValue>${d.enum[0]}</wpml:droneEnumValue><wpml:droneSubEnumValue>${d.enum[1]}</wpml:droneSubEnumValue></wpml:droneInfo></wpml:missionConfig>`;
  const head=`<?xml version="1.0" encoding="UTF-8"?>\n<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:wpml="http://www.dji.com/wpmz/1.0.6">\n<Document>\n<wpml:author>Survey Flight Planner</wpml:author><wpml:createTime>${now}</wpml:createTime><wpml:updateTime>${now}</wpml:updateTime>\n`;
  const tmpl=head+mission_cfg+`\n<Folder><wpml:templateType>waypoint</wpml:templateType><wpml:templateId>0</wpml:templateId><wpml:waylineCoordinateSysParam><wpml:coordinateMode>WGS84</wpml:coordinateMode><wpml:heightMode>relativeToStartPoint</wpml:heightMode></wpml:waylineCoordinateSysParam><wpml:autoFlightSpeed>${N(s.speed,1)}</wpml:autoFlightSpeed><wpml:globalHeight>${N(s.height,1)}</wpml:globalHeight><wpml:caliFlightEnable>0</wpml:caliFlightEnable><wpml:gimbalPitchMode>usePointSetting</wpml:gimbalPitchMode><wpml:globalWaypointHeadingParam><wpml:waypointHeadingMode>followWayline</wpml:waypointHeadingMode><wpml:waypointHeadingAngle>0</wpml:waypointHeadingAngle><wpml:waypointPoiPoint>0.000000,0.000000,0.000000</wpml:waypointPoiPoint><wpml:waypointHeadingPathMode>followBadArc</wpml:waypointHeadingPathMode></wpml:globalWaypointHeadingParam><wpml:globalWaypointTurnMode>${gturn}</wpml:globalWaypointTurnMode><wpml:globalUseStraightLine>1</wpml:globalUseStraightLine>\n`
   +pts.map((q,i)=>{const gs=q.speed==null||q.speed===s.speed,gh=q.head.mode==='follow',gt=q.turn===gturn;
     return `<Placemark><Point><coordinates>${N(q.lng)},${N(q.lat)}</coordinates></Point><wpml:index>${i}</wpml:index><wpml:ellipsoidHeight>${N(q.h,1)}</wpml:ellipsoidHeight><wpml:height>${N(q.h,1)}</wpml:height><wpml:useGlobalHeight>0</wpml:useGlobalHeight><wpml:useGlobalSpeed>${gs?1:0}</wpml:useGlobalSpeed>${gs?'':`<wpml:waypointSpeed>${N(q.speed,1)}</wpml:waypointSpeed>`}<wpml:useGlobalHeadingParam>${gh?1:0}</wpml:useGlobalHeadingParam>${gh?'':`<wpml:waypointHeadingParam>${headXml(q.head)}</wpml:waypointHeadingParam>`}<wpml:useGlobalTurnParam>${gt?1:0}</wpml:useGlobalTurnParam>${gt?'':turnXml(q)}<wpml:useStraightLine>1</wpml:useStraightLine>${(groups.get(i)||[]).join('')}</Placemark>`}).join('\n')
   +`\n<wpml:payloadParam><wpml:payloadPositionIndex>0</wpml:payloadPositionIndex><wpml:focusMode>firstPoint</wpml:focusMode><wpml:meteringMode>average</wpml:meteringMode><wpml:returnMode>singleReturnFirst</wpml:returnMode><wpml:samplingRate>240000</wpml:samplingRate><wpml:scanningMode>nonRepetitive</wpml:scanningMode><wpml:imageFormat>visable</wpml:imageFormat></wpml:payloadParam></Folder>\n</Document>\n</kml>\n`;
  const way=head+mission_cfg+`\n<Folder><wpml:templateId>0</wpml:templateId><wpml:executeHeightMode>relativeToStartPoint</wpml:executeHeightMode><wpml:waylineId>0</wpml:waylineId><wpml:distance>${N(fl?fl.dist:p.dist,1)}</wpml:distance><wpml:duration>${N(fl?fl.air:p.timeSec,1)}</wpml:duration><wpml:autoFlightSpeed>${N(s.speed,1)}</wpml:autoFlightSpeed>\n`
   +pts.map((q,i)=>`<Placemark><Point><coordinates>${N(q.lng)},${N(q.lat)}</coordinates></Point><wpml:index>${i}</wpml:index><wpml:executeHeight>${N(q.h,1)}</wpml:executeHeight><wpml:waypointSpeed>${N(q.speed==null?s.speed:q.speed,1)}</wpml:waypointSpeed><wpml:waypointHeadingParam>${headXml(q.head)}</wpml:waypointHeadingParam>${turnXml(q)}<wpml:useStraightLine>1</wpml:useStraightLine>${(groups.get(i)||[]).join('')}</Placemark>`).join('\n')
   +`\n</Folder>\n</Document>\n</kml>\n`;
  return{tmpl,way};
}
const CRC=(()=>{const t=new Uint32Array(256);for(let n=0;n<256;n++){let c=n;for(let k=0;k<8;k++)c=c&1?0xEDB88320^(c>>>1):c>>>1;t[n]=c>>>0}return t})();
const crc32=b=>{let c=0xFFFFFFFF;for(let i=0;i<b.length;i++)c=CRC[(c^b[i])&255]^(c>>>8);return(c^0xFFFFFFFF)>>>0};
function zip(files){
  const enc=new TextEncoder(),dt=new Date();
  const time=(dt.getHours()<<11)|(dt.getMinutes()<<5)|(dt.getSeconds()>>1),date=((dt.getFullYear()-1980)<<9)|((dt.getMonth()+1)<<5)|dt.getDate();
  const parts=[],cd=[];let off=0;
  for(const f of files){
    const nm=enc.encode(f.name),data=typeof f.data==='string'?enc.encode(f.data):f.data,crc=crc32(data);
    const lh=new DataView(new ArrayBuffer(30));
    lh.setUint32(0,0x04034b50,true);lh.setUint16(4,20,true);lh.setUint16(6,0x0800,true);lh.setUint16(8,0,true);lh.setUint16(10,time,true);lh.setUint16(12,date,true);
    lh.setUint32(14,crc,true);lh.setUint32(18,data.length,true);lh.setUint32(22,data.length,true);lh.setUint16(26,nm.length,true);lh.setUint16(28,0,true);
    parts.push(new Uint8Array(lh.buffer),nm,data);
    const ch=new DataView(new ArrayBuffer(46));
    ch.setUint32(0,0x02014b50,true);ch.setUint16(4,20,true);ch.setUint16(6,20,true);ch.setUint16(8,0x0800,true);ch.setUint16(10,0,true);ch.setUint16(12,time,true);ch.setUint16(14,date,true);
    ch.setUint32(16,crc,true);ch.setUint32(20,data.length,true);ch.setUint32(24,data.length,true);ch.setUint16(28,nm.length,true);ch.setUint32(42,off,true);
    cd.push(new Uint8Array(ch.buffer),nm);
    off+=30+nm.length+data.length;
  }
  const cdSize=cd.reduce((a,b)=>a+b.length,0),end=new DataView(new ArrayBuffer(22));
  end.setUint32(0,0x06054b50,true);end.setUint16(8,files.length,true);end.setUint16(10,files.length,true);end.setUint32(12,cdSize,true);end.setUint32(16,off,true);
  return new Blob([...parts,...cd,new Uint8Array(end.buffer)],{type:'application/vnd.google-earth.kmz'});
}
let toastT;
function toast(t){const el=$('#toast');el.textContent=t;el.hidden=false;clearTimeout(toastT);toastT=setTimeout(()=>el.hidden=true,6000)}
async function saveBlob(blob,name,asZip){
  if(window.claude&&typeof window.claude.use==='function'){
    let cap=null;try{cap=await window.claude.use('downloads')}catch(e){}
    if(!cap){toast('Downloads are not available in this view.');return}
    const fn=asZip?name.replace(/\.kmz$/,'.zip'):name;
    try{await cap.save({filename:fn,data:blob});toast(/\.kmz$/.test(name)?'Saved as .zip. Rename it to .kmz before importing to the drone app.':'Saved '+fn)}
    catch(err){if(!err||err.code!=='declined')toast('The file could not be saved here.')}
    return;
  }
  const a=document.createElement('a');a.href=URL.createObjectURL(blob);a.download=name;document.body.appendChild(a);a.click();a.remove();
  setTimeout(()=>URL.revokeObjectURL(a.href),4000);toast('Saved '+name);
}
function flightKmz(f){const{tmpl,way}=buildKml(f);return zip([{name:'wpmz/template.kml',data:tmpl},{name:'wpmz/waylines.wpml',data:way}])}
async function downloadKmz(k){
  if(!S.safe.ack){toast('Tick the safety confirmation first.');return}
  const p=currentPlan(),sp=splitPlan(p),n=sp.flights.length;
  const base=`${mission().id}-${drone().id}-${new Date().toISOString().slice(0,10)}`;
  const pad=v=>String(v).padStart(String(n).length,'0'),nm=f=>`mission-${pad(f.k)}.kmz`;
  if(n===1){await saveBlob(flightKmz(sp.flights[0]),base+'.kmz',true);return}
  if(k!=null){const f=sp.flights.find(x=>x.k===k);if(f)await saveBlob(flightKmz(f),`${base}-${nm(f)}`,true);return}
  const dir=`${base}-missions/`,files=[];
  for(const f of sp.flights)files.push({name:dir+nm(f),data:new Uint8Array(await flightKmz(f).arrayBuffer())});
  const csv='mission,takeoff_landing_lat,takeoff_landing_lng,ground_elevation_m,first_waypoint,last_waypoint,air_time_min,battery_left_at_landing_pct,tightest_return_home_pct\n'+sp.flights.map(f=>[f.k,f.L.lat.toFixed(6),f.L.lng.toFixed(6),f.elev!=null&&terrAvail()?f.elev.toFixed(1):'',f.a+1,f.b,(f.air/60).toFixed(1),Math.max(f.left,0).toFixed(0),Math.max(f.tight,0).toFixed(0)].join(',')).join('\n')+'\n';
  files.push({name:dir+'launch-points.csv',data:csv});
  await saveBlob(zip(files),`${base}-missions.zip`,false);
}
window.__planner={cpCtx,cpEnsure,cpQuality,cpStats,cpOrder,S,V,splitPlan,bpUsable,safetyEval,loadObs,loadAir,parseObs,parseAir,groundAt,terrReady,loadTerrain,setTf,dpKeep,toScreen,selectShot,kmzPoints,buildKml,zip,currentPlan,go,DRONES,MISSIONS,gsdAt,recommend,geo,metrics};

/* ---------- boot ---------- */
try{const sv=JSON.parse(localStorage.getItem('sfp-map')||'null');
  if(sv&&LAYERS[sv.layer]&&isFinite(sv.lat)&&isFinite(sv.lng)&&isFinite(sv.zoom)){S.layer=sv.layer;V.lat=sv.lat;V.lng=sv.lng;V.zoom=sv.zoom;S.saved=true}}catch(e){}
renderLayers();resize();
if(!S.saved)setView(25,10,clamp(Math.log2(V.w/256),1,3)); /* first open: whole world */
renderPanel();
})();
</script>
</html>
