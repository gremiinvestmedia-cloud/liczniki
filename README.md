<!DOCTYPE html>
<html lang="pl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Liczniki H4P</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
<script src="https://unpkg.com/tesseract.js@5/dist/tesseract.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
<style>
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;600&family=IBM+Plex+Sans:wght@400;500;600;700&display=swap');
:root{
  --bg:#f0ede8;--sf:#fff;--sf2:#e8e4de;--bd:#d8d3cc;--bd2:#b8b3aa;
  --tx:#1a1815;--mt:#6b6560;--ac:#1a4f9c;--acl:#e6edf8;
  --gn:#166534;--gnb:#dcfce7;--gm:#16a34a;
  --or:#92400e;--orb:#fef3c7;--yw:#d97706;
  --r:8px;--rs:5px;
  --font:'IBM Plex Sans',system-ui,sans-serif;
  --mono:'IBM Plex Mono',monospace;
}
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:var(--font);background:var(--bg);color:var(--tx);font-size:13px;}
input[type=file]{display:none;}

/* ── Top bar ── */
.bar{background:#1a1815;padding:.75rem 1.5rem;display:flex;align-items:center;gap:14px;position:sticky;top:0;z-index:20;flex-wrap:wrap;}
.logo{display:flex;align-items:center;gap:9px;flex-shrink:0;}
.logo-i{width:30px;height:30px;border-radius:6px;background:#fff;color:#1a1815;display:grid;place-items:center;font-size:17px;font-weight:700;}
.logo-t{font-size:14px;font-weight:700;color:#fff;letter-spacing:-.2px;}
.logo-s{font-size:11px;color:#ffffff55;margin-left:2px;}

/* Tabs */
.tabs{display:flex;gap:2px;background:#ffffff14;border-radius:6px;padding:2px;}
.tab{padding:5px 14px;border-radius:5px;cursor:pointer;font-size:12px;font-weight:600;color:#ffffff88;transition:all .15s;white-space:nowrap;border:none;background:transparent;font-family:var(--font);}
.tab:hover{color:#fff;background:#ffffff18;}
.tab.active{background:#fff;color:#1a1815;}

/* Buttons */
.btn{padding:6px 13px;border:1px solid var(--bd);border-radius:var(--rs);background:var(--sf);color:var(--tx);font-size:12px;font-weight:600;cursor:pointer;font-family:var(--font);transition:all .15s;white-space:nowrap;}
.btn:hover{background:var(--sf2);}
.btn:disabled{opacity:.4;cursor:default;}
.btn-bl{background:var(--ac);color:#fff;border:none;}.btn-bl:hover{opacity:.88;background:var(--ac);}
.btn-gn{background:var(--gm);color:#fff;border:none;}.btn-gn:hover{background:var(--gn);}
.btn-sm{padding:4px 10px;font-size:11.5px;}

/* Dropzone */
.dz{display:flex;align-items:center;gap:8px;padding:5px 12px;border:1.5px dashed #ffffff44;border-radius:var(--rs);cursor:pointer;transition:all .15s;font-size:12px;font-weight:600;color:#ffffffaa;}
.dz:hover{border-color:#ffffff88;color:#fff;}
.dz.done{border-style:solid;border-color:#86efac;color:#86efac;}

.sep-v{width:1px;height:20px;background:#ffffff22;flex-shrink:0;}
.ml-auto{margin-left:auto;}

/* Log bar */
.logbar{background:#111;border-bottom:1px solid #333;padding:5px 1.5rem;display:none;max-height:70px;overflow-y:auto;}
.ll{font-family:var(--mono);font-size:11px;line-height:1.5;}
.ok{color:#86efac;}.er{color:#fca5a5;}.nf{color:#93c5fd;}.wn{color:#fcd34d;}

/* Progress */
.prg{height:2px;background:var(--ac);width:0%;position:fixed;top:0;left:0;z-index:100;transition:width .3s;}

/* Main */
.main{padding:1.25rem 1.5rem;}
.sec{display:none;}.sec.active{display:block;}

/* Building blocks */
.bud-block{margin-bottom:1.75rem;}
.bud-hd{display:flex;align-items:center;gap:10px;margin-bottom:.5rem;}
.bud-title{font-size:10px;font-weight:700;letter-spacing:.1em;text-transform:uppercase;color:var(--mt);}
.bud-badge{background:#1a1815;color:#fff;font-size:10px;font-weight:700;padding:2px 8px;border-radius:3px;letter-spacing:.05em;}

/* Tables */
.tbl-wrap{overflow-x:auto;border:1px solid var(--bd);border-radius:var(--r);}
table{border-collapse:collapse;white-space:nowrap;font-size:12px;}
thead tr.r1 th{background:#1a1815;color:#9ca3af;font-size:9.5px;font-weight:700;padding:5px 9px;text-align:center;border-right:1px solid #ffffff15;letter-spacing:.05em;text-transform:uppercase;}
thead tr.r1 th.th0{text-align:left;position:sticky;left:0;z-index:3;background:#1a1815;}
thead tr.r2 th{background:#23242a;color:#fff;font-size:10px;font-weight:600;padding:4px 8px;text-align:center;border-right:1px solid #ffffff10;font-family:var(--mono);}
thead tr.r2 th.th0{background:#23242a;color:#aaa;text-align:left;font-family:var(--font);position:sticky;left:0;z-index:3;font-size:10px;min-width:88px;}
tbody td{padding:4px 9px;border-bottom:1px solid var(--bd);border-right:1px solid var(--bd);text-align:right;font-family:var(--mono);font-size:11.5px;}
tbody td.td0{text-align:left;font-family:var(--font);font-size:11.5px;color:var(--mt);font-weight:600;position:sticky;left:0;background:var(--sf);z-index:1;padding-left:10px;}
tbody tr:hover td{background:#f5f2ee;}
tbody tr:hover td.td0{background:#eceae5;}
tbody td.em{color:#ccc;text-align:center;font-family:var(--font);font-size:10px;}
tbody td.pf{background:var(--gnb);color:var(--gn);font-weight:700;}
tbody tr:hover td.pf{background:#c0f0d0;}
tbody td.man{background:var(--orb);color:var(--or);font-weight:700;}
tbody tr:hover td.man{background:#fde68a;}

/* Photo section */
.ph-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:.75rem;margin-top:.875rem;}
.ph-card{background:var(--sf);border:1px solid var(--bd);border-radius:var(--r);overflow:hidden;transition:box-shadow .15s;}
.ph-card:hover{box-shadow:0 4px 16px #0001;}
.ph-card.ok{border-color:var(--gm);}
.ph-card.err{border-color:#ef4444;}
.ph-card.wait{border-color:var(--yw);}
.ph-img{width:100%;height:110px;object-fit:cover;display:block;background:#f0ede8;}
.ph-body{padding:.625rem .75rem;}
.ph-meter{font-family:var(--mono);font-size:10.5px;color:var(--mt);margin-bottom:2px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
.ph-adr{font-size:11px;font-weight:700;color:var(--tx);margin-bottom:.4rem;}
.ph-val-row{display:flex;align-items:center;gap:5px;}
.ph-val{flex:1;font-family:var(--mono);font-size:12px;padding:4px 7px;border:1.5px solid var(--bd);border-radius:4px;background:var(--bg);color:var(--tx);font-weight:600;}
.ph-val:focus{outline:none;border-color:var(--ac);}
.ph-val.auto{border-color:var(--gm);background:var(--gnb);color:var(--gn);}
.ph-val.manual{border-color:var(--yw);background:var(--orb);color:var(--or);}
.ph-status{font-size:10px;margin-top:3px;color:var(--mt);}
.ph-status.ok{color:var(--gn);}
.ph-status.err{color:#ef4444;}
.badge{display:inline-block;font-size:9.5px;font-weight:700;padding:1px 6px;border-radius:3px;text-transform:uppercase;letter-spacing:.04em;}
.bw{background:#dbeafe;color:#1e40af;}.bh{background:#fce7f3;color:#831843;}
.bg{background:#dcfce7;color:#14532d;}.by{background:#fef9c3;color:#854d0e;}

/* Date input */
.ph-topbar{display:flex;align-items:center;gap:10px;flex-wrap:wrap;margin-bottom:.875rem;}
.inp{padding:6px 10px;border:1px solid var(--bd);border-radius:var(--rs);background:var(--bg);color:var(--tx);font-family:var(--font);font-size:12px;}
.inp:focus{outline:none;border-color:var(--ac);}

/* Modal */
#modal{display:none;position:fixed;inset:0;background:#00000066;z-index:200;align-items:center;justify-content:center;}
.modal-box{background:#fff;border-radius:12px;padding:1.5rem;min-width:300px;box-shadow:0 20px 60px #0003;}
.modal-title{font-size:15px;font-weight:700;margin-bottom:.3rem;}
.modal-sub{font-size:12px;color:var(--mt);margin-bottom:1rem;}
.modal-dates{display:flex;flex-direction:column;gap:7px;margin-bottom:1.25rem;}
.modal-dates label{display:flex;align-items:center;gap:10px;padding:9px 12px;border:1.5px solid var(--bd);border-radius:7px;cursor:pointer;transition:border-color .15s;}
.modal-dates label.sel{border-color:var(--gm);}
.modal-dates input[type=radio]{accent-color:var(--gm);width:16px;height:16px;}
.modal-dates span{font-size:14px;font-weight:700;}
.modal-footer{display:flex;gap:8px;justify-content:flex-end;}
</style>
</head>
<body>
<div class="prg" id="prg"></div>


<!-- Settings modal -->
<div id="settings-modal" style="display:none;position:fixed;inset:0;background:#00000066;z-index:300;align-items:center;justify-content:center;">
  <div class="modal-box" style="min-width:380px;">
    <div class="modal-title">&#9881; Ustawienia OCR</div>
    <div class="modal-sub">Klucz API jest zapisany lokalnie w przegl&#261;darce. Pracownicy go nie widz&#261;.</div>
    <div style="margin-bottom:.875rem;">
      <label style="font-size:11px;font-weight:700;color:var(--mt);display:block;margin-bottom:4px;">&#127381; Klucz Google Gemini API <span style="background:#dcfce7;color:#15803d;font-size:9px;padding:1px 5px;border-radius:3px;margin-left:4px;">BEZPŁATNY</span></label>
      <input type="password" id="gemini-key-settings" class="inp" style="width:100%;font-family:var(--mono);font-size:12px;" placeholder="AIza...">
      <div style="font-size:10.5px;color:var(--mt);margin-top:3px;">Pobierz bezp&#322;atnie na <strong>aistudio.google.com</strong> &rarr; Get API key</div>
    </div>
    <div style="margin-bottom:1rem;">
      <label style="font-size:11px;font-weight:700;color:var(--mt);display:block;margin-bottom:4px;">Klucz Anthropic API (opcjonalnie)</label>
      <input type="password" id="api-key-settings" class="inp" style="width:100%;font-family:var(--mono);font-size:12px;" placeholder="sk-ant-...">
    </div>
    <div id="api-status" style="font-size:11px;margin-bottom:.875rem;"></div>
    <div class="modal-footer">
      <button class="btn" onclick="document.getElementById('settings-modal').style.display='none'">Anuluj</button>
      <button class="btn btn-bl" onclick="saveApiKey()">Zapisz</button>
    </div>
  </div>
</div>
<!-- Modal -->
<div id="modal"><div class="modal-box">
  <div class="modal-title">Wybierz dat&#281; odczytu</div>
  <div class="modal-sub">Kt&#243;re wskazania wpisa&#263; do tabeli?</div>
  <div class="modal-dates" id="modal-dates"></div>
  <div class="modal-footer">
    <button class="btn" onclick="closeModal()">Anuluj</button>
    <button class="btn btn-gn" onclick="applyPgeDate()">Wype&#322;nij &#8594;</button>
  </div>
</div></div>

<!-- Top bar -->
<div class="bar">
  <div class="logo">
    <div class="logo-i">H</div>
    <div><div class="logo-t">H4P Liczniki</div><div class="logo-s">System odczyt&#243;w</div></div>
  </div>
  <div class="tabs">
    <button class="tab active" onclick="switchTab('pge',this)">&#9889; Energia PGE</button>
    <button class="tab" onclick="switchTab('woda',this)">&#128167; Woda</button>
    <button class="tab" onclick="switchTab('cieplo',this)">&#128293; Ciep&#322;o</button>
    <button class="tab" onclick="switchTab('gaz',this)">&#128293; Gaz</button>
  </div>
  <!-- PGE controls -->
  <div id="ctrl-pge" style="display:flex;align-items:center;gap:8px;">
    <div class="sep-v"></div>
    <div class="dz" id="dz-pdf" onclick="document.getElementById('fi-pdf').click()">
      <span>&#128196;</span><span id="dz-lb">Wgraj PDF PGE</span>
    </div>
    <input type="file" id="fi-pdf" accept=".pdf" onchange="setPdf(this.files)">
    <button class="btn btn-bl btn-sm" id="pdf-btn" onclick="processPdf()" disabled>Wczytaj</button>
  </div>
  <!-- Photo controls -->
  <div id="ctrl-photo" style="display:none;align-items:center;gap:8px;">
    <div class="sep-v"></div>
    <div class="dz" id="dz-ph" onclick="document.getElementById('fi-ph').click()">
      <span>&#128247;</span><span id="ph-lb">Wgraj zdj&#281;cia</span>
    </div>
    <input type="file" id="fi-ph" accept="image/*" multiple onchange="addPhotos(this.files)">
    <button class="btn btn-sm" onclick="analyzeAll()" id="an-btn" style="display:none;">&#128269; Analizuj OCR</button>
    <button class="btn btn-sm btn-gn" onclick="downloadZip()" id="zip-btn" style="display:none;">&#8595; ZIP z nowymi nazwami</button>
  </div>
  <!-- Copy button -->
  <div class="ml-auto" style="display:flex;gap:7px;align-items:center;">
    <button class="btn btn-sm" onclick="openSettings()" title="Ustawienia OCR" style="background:#ffffff18;border-color:#ffffff33;color:#fff;">&#9881;</button>
    <button class="btn btn-gn btn-sm" onclick="copyTable(event)">&#128203; Kopiuj</button>
  </div>
</div>

<div class="logbar" id="logbar"></div>

<div class="main">
  <div class="sec active" id="sec-pge"></div>
  <div class="sec" id="sec-woda"></div>
  <div class="sec" id="sec-cieplo"></div>
  <div class="sec" id="sec-gaz"></div>
</div>

<script>
pdfjsLib.GlobalWorkerOptions.workerSrc='https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';

// ── DATA ──────────────────────────────────────────────────────────────────
const PGE_BASE={"21": {"meters": [{"apt": "ADM", "meter": "31445196"}, {"apt": "1", "meter": "10020452"}, {"apt": "2", "meter": "10020428"}, {"apt": "3", "meter": "10020418"}, {"apt": "4", "meter": "10020475"}, {"apt": "5", "meter": "10021420"}, {"apt": "6", "meter": "10021257"}, {"apt": "7", "meter": "10021310"}, {"apt": "8", "meter": "10020478"}, {"apt": "9", "meter": "10021358"}, {"apt": "10", "meter": "10021303"}, {"apt": "11", "meter": "10020497"}, {"apt": "12", "meter": "10020424"}], "rows": [{"date": "08.2025", "data": {"31445196": 1231.3, "10020452": 3245.1, "10020428": 1052.8, "10020418": 1736.5, "10020475": 3914.5, "10021420": 11020.7, "10021257": 23092.7, "10021310": 3054.3, "10020478": 1346.8, "10021358": 2145.5, "10021303": 824.4, "10020497": 2964.3, "10020424": 558.2}, "isPdf": false}, {"date": "09.2025", "data": {"31445196": 2805.5, "10020452": 3255.7, "10020428": 1057.3, "10020418": 1745.3, "10020475": 3922.4, "10021420": 11038.4, "10021257": 23106.9, "10021310": 3067.8, "10020478": 1364.9, "10021358": 2160.4, "10021303": 878.5, "10020497": 3054.1, "10020424": 567.1}, "isPdf": false}, {"date": "10.2025", "data": {"31445196": 7637.1, "10020452": 3260.0, "10020428": 1142.9, "10020418": 1749.7, "10020475": 3963.3, "10021420": 11040.3, "10021257": 23107.8, "10021310": 3140.2, "10020478": 1466.2, "10021358": 2220.1, "10021303": 995.2, "10020497": 3157.8, "10020424": 797.0}, "isPdf": false}, {"date": "11.2025", "data": {"31445196": 8295.1, "10020452": 3262.0, "10020428": 1157.9, "10020418": 1751.7, "10020475": 3979.3, "10021420": 11050.3, "10021257": 23120.8, "10021310": 3151.2, "10020478": 1484.2, "10021358": 2236.1, "10021303": 1009.2, "10020497": 3169.8, "10020424": 821.0}, "isPdf": false}, {"date": "12.2025", "data": {"31445196": 14213.7, "10020452": 3282.8, "10020428": 1292.6, "10020418": 1772.9, "10020475": 4122.8, "10021420": 11137.3, "10021257": 23232.6, "10021310": 3250.8, "10020478": 1649.1, "10021358": 2380.4, "10021303": 1133.7, "10020497": 3278.2, "10020424": 1039.8}, "isPdf": false}, {"date": "31/01/2026", "data": {"31445196": 28990.2, "10020452": 3703.7, "10020428": 1592.6, "10020418": 2208.0, "10020475": 4500.6, "10021420": 11369.5, "10021257": 23605.8, "10021310": 3541.8, "10020478": 2015.1, "10021358": 2748.4, "10021303": 1495.9, "10020497": 3518.2, "10020424": 1525.8}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "19": {"meters": [{"apt": "ADM", "meter": "10021273"}, {"apt": "1", "meter": "10021304"}, {"apt": "2", "meter": "10021419"}, {"apt": "3", "meter": "10021363"}, {"apt": "4", "meter": "10020495"}, {"apt": "5", "meter": "10021415"}, {"apt": "6", "meter": "10021402"}, {"apt": "7", "meter": "10021263"}, {"apt": "8", "meter": "10021282"}, {"apt": "9", "meter": "10021278"}, {"apt": "10", "meter": "10020501"}, {"apt": "11", "meter": "10020429"}, {"apt": "12", "meter": "10015853"}], "rows": [{"date": "08.2025", "data": {"10021273": 3208.9, "10021304": 1613.3, "10021419": 3689.8, "10021363": 1314.2, "10020495": 2368.1, "10021415": 1987.8, "10021402": 2553.5, "10021263": 471.5, "10021282": 23730.8, "10021278": 5135.4, "10020501": 4532.8, "10020429": 1472.3, "10015853": 276.8}, "isPdf": false}, {"date": "09.2025", "data": {"10021273": 3633.8, "10021304": 1613.6, "10021419": 3706.9, "10021363": 1320.2, "10020495": 2368.4, "10021415": 1989.2, "10021402": 2566.96, "10021263": 473.0, "10021282": 23733.4, "10021278": 5138.1, "10020501": 4553.6, "10020429": 1483.0, "10015853": 291.6}, "isPdf": false}, {"date": "10.2025", "data": {"10021273": 4073.2, "10021304": 1620.5, "10021419": 3714.1, "10021363": 1335.0, "10020495": 2376.4, "10021415": 1995.5, "10021402": 2572.63, "10021263": 483.1, "10021282": 23741.6, "10021278": 5147.3, "10020501": 4564.2, "10020429": 1493.4, "10015853": 303.5}, "isPdf": false}, {"date": "11.2025", "data": {"10021273": 4199.2, "10021304": 1620.5, "10021419": 3716.1, "10021363": 1336.0, "10020495": 2377.4, "10021415": 1995.5, "10021402": 2578.63, "10021263": 483.1, "10021282": 23742.6, "10021278": 5147.3, "10020501": 4565.2, "10020429": 1493.4, "10015853": 303.5}, "isPdf": false}, {"date": "12.2025", "data": {"10021273": 5333.2, "10021304": 1624.4, "10021419": 3731.5, "10021363": 1341.9, "10020495": 2384.4, "10021415": 1997.9, "10021402": 2628.52, "10021263": 487.2, "10021282": 23746.8, "10021278": 5149.2, "10020501": 4570.7, "10020429": 1497.6, "10015853": 306.1}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021273": 17295.2, "10021304": 2027.1, "10021419": 3783.2, "10021363": 1876.2, "10020495": 2637.8, "10021415": 2072.8, "10021402": 2990.5, "10021263": 578.6, "10021282": 23896.1, "10021278": 5226.4, "10020501": 4660.4, "10020429": 1755.1, "10015853": 418.0}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "17": {"meters": [{"apt": "ADM", "meter": "10021337"}, {"apt": "1", "meter": "31521985"}, {"apt": "2", "meter": "10020420"}, {"apt": "3", "meter": "10021376"}, {"apt": "4", "meter": "10021388"}, {"apt": "5", "meter": "10021264"}, {"apt": "6", "meter": "10021422"}, {"apt": "7", "meter": "10021290"}, {"apt": "8", "meter": "31521964"}, {"apt": "9", "meter": "10020496"}, {"apt": "10", "meter": "10020481"}, {"apt": "11", "meter": "10021311"}, {"apt": "12", "meter": "10020479"}], "rows": [{"date": "08.2025", "data": {"10021337": 853.2, "31521985": 0.0, "10020420": 1823.8, "10021376": 1454.3, "10021388": 941.1, "10021264": 1449.0, "10021422": 809.8, "10021290": 16359.9, "31521964": 0.0, "10020496": 595.3, "10020481": 28.3, "10021311": 2123.2, "10020479": 1439.6}, "isPdf": false}, {"date": "09.2025", "data": {"10021337": 1213.8, "31521985": 6.1, "10020420": 1830.4, "10021376": 1460.4, "10021388": 942.1, "10021264": 1459.3, "10021422": 819.0, "10021290": 16363.2, "31521964": 3.0, "10020496": 599.5, "10020481": 28.4, "10021311": 2123.8, "10020479": 1440.5}, "isPdf": false}, {"date": "10.2025", "data": {"10021337": 1480.2, "31521985": 14.3, "10020420": 1839.7, "10021376": 1472.9, "10021388": 943.3, "10021264": 1471.7, "10021422": 831.9, "10021290": 16372.5, "31521964": 8.9, "10020496": 612.5, "10020481": 43.3, "10021311": 2133.1, "10020479": 1441.0}, "isPdf": false}, {"date": "11.2025", "data": {"10021337": 1516.2, "31521985": 14.3, "10020420": 1839.7, "10021376": 1473.9, "10021388": 943.3, "10021264": 1472.7, "10021422": 832.9, "10021290": 16373.5, "31521964": 8.9, "10020496": 613.5, "10020481": 45.3, "10021311": 2134.1, "10020479": 1441.0}, "isPdf": false}, {"date": "12.2025", "data": {"10021337": 1844.0, "31521985": 18.0, "10020420": 1843.4, "10021376": 1480.3, "10021388": 945.9, "10021264": 1480.3, "10021422": 839.4, "10021290": 16379.0, "31521964": 13.1, "10020496": 620.0, "10020481": 59.5, "10021311": 2143.5, "10020479": 1442.3}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021337": 7457.5, "31521985": 34.1, "10020420": 1859.0, "10021376": 1524.2, "10021388": 961.0, "10021264": 1498.3, "10021422": 855.4, "10021290": 16393.0, "31521964": 28.9, "10020496": 636.0, "10020481": 98.1, "10021311": 2167.3, "10020479": 1449.6}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "11.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "15": {"meters": [{"apt": "ADM", "meter": "10021275"}, {"apt": "1", "meter": "10021319"}, {"apt": "2", "meter": "10015845"}, {"apt": "3", "meter": "10020473"}, {"apt": "4", "meter": "10021259"}, {"apt": "5", "meter": "10021384"}, {"apt": "6", "meter": "10021414"}, {"apt": "7", "meter": "31521986"}, {"apt": "8", "meter": "10020502"}, {"apt": "9", "meter": "10021416"}, {"apt": "10", "meter": "10020503"}, {"apt": "11", "meter": "10021424"}, {"apt": "12", "meter": "10015837"}], "rows": [{"date": "08.2025", "data": {"10021275": 2857.6, "10021319": 1311.7, "10015845": 1400.3, "10020473": 2083.4, "10021259": 1029.9, "10021384": 3007.3, "10021414": 2105.3, "31521986": 0.0, "10020502": 5657.6, "10021416": 1752.6, "10020503": 852.2, "10021424": 2588.1, "10015837": 3895.7}, "isPdf": false}, {"date": "09.2025", "data": {"10021275": 3179.8, "10021319": 1341.6, "10015845": 1407.2, "10020473": 2088.6, "10021259": 1045.4, "10021384": 3014.6, "10021414": 2106.1, "31521986": 9.1, "10020502": 5661.1, "10021416": 1756.6, "10020503": 855.3, "10021424": 2592.3, "10015837": 3898.8}, "isPdf": false}, {"date": "10.2025", "data": {"10021275": 3319.1, "10021319": 1347.2, "10015845": 1414.5, "10020473": 2094.8, "10021259": 1065.1, "10021384": 3189.9, "10021414": 2112.4, "31521986": 15.2, "10020502": 5669.1, "10021416": 1762.5, "10020503": 862.7, "10021424": 2598.8, "10015837": 3904.5}, "isPdf": false}, {"date": "11.2025", "data": {"10021275": 3448.1, "10021319": 1347.2, "10015845": 1414.5, "10020473": 2095.8, "10021259": 1067.1, "10021384": 3200.9, "10021414": 2112.4, "31521986": 15.2, "10020502": 5670.1, "10021416": 1763.5, "10020503": 863.7, "10021424": 2599.8, "10015837": 3904.5}, "isPdf": false}, {"date": "12.2025", "data": {"10021275": 4610.0, "10021319": 1349.0, "10015845": 1417.4, "10020473": 2101.2, "10021259": 1083.3, "10021384": 3301.9, "10021414": 2114.8, "31521986": 19.3, "10020502": 5673.9, "10021416": 1767.1, "10020503": 870.6, "10021424": 2605.3, "10015837": 3908.5}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021275": 12128.6, "10021319": 1368.4, "10015845": 1439.8, "10020473": 2165.9, "10021259": 1115.3, "10021384": 3525.9, "10021414": 2145.7, "31521986": 31.6, "10020502": 5688.2, "10021416": 1781.0, "10020503": 886.6, "10021424": 2630.5, "10015837": 3995.8}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "11.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}};
const WODA_DATA={"21": {"meters": [{"label": "1z", "meter": "230565273A", "col": 2}, {"label": "1c", "meter": "221368706A", "col": 3}, {"label": "2z", "meter": "230565268A", "col": 4}, {"label": "2c", "meter": "221368766A", "col": 5}, {"label": "3z", "meter": "230095926A", "col": 6}, {"label": "3c", "meter": "221368685A", "col": 7}, {"label": "4z", "meter": "230095938A", "col": 8}, {"label": "4c", "meter": "221368785A", "col": 9}, {"label": "5z", "meter": "230095934A", "col": 10}, {"label": "5c", "meter": "221368747A", "col": 11}, {"label": "6z", "meter": "230095907A", "col": 12}, {"label": "6c", "meter": "221368748A", "col": 13}, {"label": "7z", "meter": "230565269A", "col": 14}, {"label": "7c", "meter": "221368715A", "col": 15}, {"label": "8z", "meter": "230095927A", "col": 16}, {"label": "8c", "meter": "221368786A", "col": 17}, {"label": "9z", "meter": "230565250A", "col": 18}, {"label": "9c", "meter": "221368769A", "col": 19}, {"label": "10z", "meter": "230565246A", "col": 20}, {"label": "10c", "meter": "221368771A", "col": 21}, {"label": "11z", "meter": "230095670A", "col": 22}, {"label": "11c", "meter": "221368736A", "col": 23}, {"label": "12z", "meter": "230565266A", "col": 24}, {"label": "12c", "meter": "221368714A", "col": 25}], "rows": [{"date": "01.10.2025", "data": {"230565273A": 1.0, "221368706A": 0.0, "230565268A": 0.0, "221368766A": 0.0, "230095926A": 0.0, "221368685A": 0.0, "230095938A": 0.0, "221368785A": 0.0, "230095934A": 0.0, "221368747A": 0.0, "230095907A": 0.0, "221368748A": 0.0, "230565269A": 0.0, "221368715A": 0.0, "230095927A": 0.0, "221368786A": 0.0, "230565250A": 0.0, "221368769A": 0.0, "230565246A": 5.0, "221368771A": 5.0, "230095670A": 6.0, "221368736A": 5.0, "230565266A": 4.0, "221368714A": 2.0}}, {"date": "01.11.2025", "data": {"230565273A": 3.0, "221368706A": 3.0, "230565268A": 0.0, "221368766A": 0.0, "230095926A": 0.0, "221368685A": 0.0}}, {"date": "01.12.2025", "data": {"230565273A": 11.0, "221368706A": 15.0, "230565268A": 7.0, "221368766A": 10.0, "230095926A": 11.0, "221368685A": 14.0, "230095938A": 15.0, "221368785A": 17.0, "230095934A": 10.0, "221368747A": 10.0, "230095907A": 25.0, "221368748A": 24.0, "230565269A": 41.0, "221368715A": 19.0, "230095927A": 21.0, "221368786A": 21.0, "230565250A": 36.0, "221368769A": 28.0, "230565246A": 31.0, "221368771A": 33.0, "230095670A": 27.0, "221368736A": 27.0, "230565266A": 32.0, "221368714A": 34.0}}, {"date": "13.01.2026", "data": {"230565273A": 12.5, "221368706A": 15.785, "230565268A": 10.547, "221368766A": 15.012, "230095926A": 14.366, "221368685A": 18.91, "230095938A": 17.296, "221368785A": 20.023, "230095934A": 11.623, "221368747A": 12.6, "230095907A": 28.798, "221368748A": 29.354, "230565269A": 44.932, "221368715A": 23.745, "230095927A": 24.906, "221368786A": 24.781, "230565250A": 39.847, "221368769A": 32.819, "230565246A": 35.934, "221368771A": 37.786, "230095670A": 29.331, "221368736A": 29.322, "230565266A": 34.186, "221368714A": 36.633}}, {"date": "30.01.2026", "data": {"230565273A": 17.919, "221368706A": 17.923, "230565268A": 14.809, "221368766A": 20.144, "230095926A": 18.533, "221368685A": 23.504, "230095938A": 20.323, "221368785A": 23.358, "230095934A": 15.492, "221368747A": 14.267, "230095907A": 38.878, "221368748A": 35.828, "230565269A": 56.097, "221368715A": 29.68, "230095927A": 30.796, "221368786A": 29.667, "230565250A": 49.639, "221368769A": 38.782, "230565246A": 41.96, "221368771A": 44.159, "230095670A": 32.201, "221368736A": 31.636, "230565266A": 35.273, "221368714A": 37.979}}, {"date": "03.03.2026", "data": {"230565273A": 31.311, "221368706A": 22.012, "230565268A": 14.822, "221368766A": 20.145, "230095926A": 26.472, "221368685A": 34.182, "230095938A": 24.953, "221368785A": 29.036, "230095934A": 15.816, "221368747A": 17.717, "230095907A": 51.873, "221368748A": 44.875, "230565269A": 69.355, "221368715A": 44.822, "230095927A": 39.256, "221368786A": 43.858, "230565250A": 73.916, "221368769A": 49.26, "230565246A": 52.331, "221368771A": 56.83, "230095670A": 33.983, "221368736A": 33.067, "230565266A": 35.273, "221368714A": 37.979}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]}, "19": {"meters": [{"label": "1z", "meter": "230847103A", "col": 2}, {"label": "1c", "meter": "230073575A", "col": 3}, {"label": "2z", "meter": "230847015A", "col": 4}, {"label": "2c", "meter": "230073561A", "col": 5}, {"label": "3z", "meter": "230847001A", "col": 6}, {"label": "3c", "meter": "230073576A", "col": 7}, {"label": "4z", "meter": "230847081A", "col": 8}, {"label": "4c", "meter": "230073550A", "col": 9}, {"label": "5z", "meter": "230847050A", "col": 10}, {"label": "5c", "meter": "230073549A", "col": 11}, {"label": "6z", "meter": "230847000A", "col": 12}, {"label": "6c", "meter": "230073551A", "col": 13}, {"label": "7z", "meter": "230847104A", "col": 14}, {"label": "7c", "meter": "230073571A", "col": 15}, {"label": "8z", "meter": "230847079A", "col": 16}, {"label": "8c", "meter": "230073513A", "col": 17}, {"label": "9z", "meter": "230847092A", "col": 18}, {"label": "9c", "meter": "230073573A", "col": 19}, {"label": "10z", "meter": "230847060A", "col": 20}, {"label": "10c", "meter": "230013560A", "col": 21}, {"label": "11z", "meter": "230847039A", "col": 22}, {"label": "11c", "meter": "230073558A", "col": 23}, {"label": "12z", "meter": "320847113A", "col": 24}, {"label": "12c", "meter": "230073577A", "col": 25}], "rows": [{"date": "01.12.2025", "data": {"230847103A": 5.0, "230073575A": 2.0, "230847015A": 6.0, "230073561A": 5.0, "230847001A": 1.0, "230073576A": 0.0, "230847081A": 9.0, "230073550A": 14.0, "230847050A": 6.0, "230073549A": 4.0, "230847000A": 0.0, "230073551A": 0.0, "230847104A": 0.0, "230073571A": 0.0, "230847079A": 3.0, "230073513A": 0.0, "230847092A": 0.0, "230073573A": 0.0, "230847060A": 0.0, "230013560A": 0.0, "230847039A": 2.0, "230073558A": 2.0, "320847113A": 0.0, "230073577A": 0.0}}, {"date": "13.01.2026", "data": {"230847103A": 6.911, "230073575A": 4.151, "230847015A": 8.537, "230073561A": 8.39, "230847001A": 1.926, "230073576A": 1.088, "230847081A": 13.178, "230073550A": 20.289, "230847050A": 9.199, "230073549A": 7.137, "230847000A": 0.601, "230073551A": 0.228, "230847104A": 1.853, "230073571A": 1.305, "230847079A": 5.133, "230073513A": 2.039, "230847092A": 1.129, "230073573A": 0.459, "230847060A": 0.647, "230013560A": 0.245, "230847039A": 4.115, "230073558A": 4.109, "320847113A": 0.702, "230073577A": 0.372}}, {"date": "30.01.2026", "data": {"230847103A": 10.176, "230073575A": 6.472, "230847015A": 11.96, "230073561A": 11.644, "230847001A": 2.64, "230073576A": 2.251, "230847081A": 18.814, "230073550A": 27.319, "230847050A": 12.838, "230073549A": 10.468, "230847000A": 3.786, "230073551A": 2.621, "230847104A": 4.54, "230073571A": 3.074, "230847079A": 7.368, "230073513A": 3.5, "230847092A": 2.622, "230073573A": 2.702, "230847060A": 2.752, "230013560A": 1.952, "230847039A": 6.909, "230073558A": 6.872, "320847113A": 3.464, "230073577A": 3.637}}, {"date": "03.03.2026", "data": {"230847103A": 12.83, "230073575A": 8.598, "230847015A": 17.435, "230073561A": 18.002, "230847001A": 4.049, "230073576A": 4.202, "230847081A": 27.764, "230073550A": 37.902, "230847050A": 19.931, "230073549A": 17.245, "230847000A": 8.301, "230073551A": 8.153, "230847104A": 8.53, "230073571A": 7.609, "230847079A": 11.13, "230073513A": 5.63, "230847092A": 5.737, "230073573A": 7.145, "230847060A": 8.181, "230013560A": 5.275, "230847039A": 9.632, "230073558A": 10.263, "320847113A": 12.355, "230073577A": 15.933}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]}, "17": {"meters": [{"label": "1z", "meter": "230846995A", "col": 2}, {"label": "1c", "meter": "230073557A", "col": 3}, {"label": "2z", "meter": "230846979A", "col": 4}, {"label": "2c", "meter": "221368717A", "col": 5}, {"label": "3z", "meter": "230847029A", "col": 6}, {"label": "3c", "meter": "221368738A", "col": 7}, {"label": "4z", "meter": "230847017A", "col": 8}, {"label": "4c", "meter": "221368778A", "col": 9}, {"label": "5z", "meter": "230847016A", "col": 10}, {"label": "5c", "meter": "221368692A", "col": 11}, {"label": "6z", "meter": "230846981A", "col": 12}, {"label": "6c", "meter": "230346378A", "col": 13}, {"label": "7z", "meter": "230847020A", "col": 14}, {"label": "7c", "meter": "230073566A", "col": 15}, {"label": "8z", "meter": "230846980A", "col": 16}, {"label": "8c", "meter": "230039689A", "col": 17}, {"label": "9z", "meter": "230846999A", "col": 18}, {"label": "9c", "meter": "230073542A", "col": 19}, {"label": "10z", "meter": "230847049A", "col": 20}, {"label": "10c", "meter": "230073556A", "col": 21}, {"label": "11z", "meter": "230847040A", "col": 22}, {"label": "11c", "meter": "230073554A", "col": 23}, {"label": "12z", "meter": "230847003A", "col": 24}, {"label": "12c", "meter": "230073544A", "col": 25}], "rows": [{"date": "13.01.2026", "data": {"230846995A": 1.083, "230073557A": 0.314, "230846979A": 0.986, "221368717A": 0.138, "230847029A": 0.569, "221368738A": 0.085, "230847017A": 0.587, "221368778A": 0.111, "230847016A": 0.41, "221368692A": 0.123, "230846981A": 0.651, "230346378A": 0.069, "230847020A": 0.889, "230073566A": 0.027, "230846980A": 0.733, "230039689A": 0.0, "230846999A": 0.873, "230073542A": 0.033, "230847049A": 0.644, "230073556A": 0.065, "230847040A": 0.605, "230073554A": 0.074, "230847003A": 0.546, "230073544A": 0.041}}, {"date": "30.01.2026", "data": {"230846995A": 1.255, "230073557A": 0.315, "230846979A": 0.986, "221368717A": 0.138, "230847029A": 0.865, "221368738A": 0.238, "230847017A": 0.592, "221368778A": 0.115, "230847016A": 0.468, "221368692A": 0.128, "230846981A": 0.715, "230346378A": 0.089, "230847020A": 0.946, "230073566A": 0.027, "230846980A": 0.769, "230039689A": 0.001, "230846999A": 0.885, "230073542A": 0.033, "230847049A": 0.732, "230073556A": 0.125, "230847040A": 0.954, "230073554A": 0.1, "230847003A": 0.629, "230073544A": 0.041}}, {"date": "03.03.2026", "data": {"230846995A": 1.581, "230073557A": 0.318, "230846979A": 1.065, "221368717A": 0.143, "230847029A": 1.08, "221368738A": 0.254, "230847017A": 0.947, "221368778A": 0.395, "230847016A": 1.083, "221368692A": 0.232, "230846981A": 0.741, "230346378A": 0.142, "230847020A": 2.111, "230073566A": 1.476, "230846980A": 2.472, "230039689A": 1.407, "230846999A": 2.671, "230073542A": 1.963, "230847049A": 2.065, "230073556A": 0.895, "230847040A": 4.425, "230073554A": 4.574, "230847003A": 3.986, "230073544A": 5.83}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]}, "15": {"meters": [{"label": "1z", "meter": "230847030A", "col": 2}, {"label": "1c", "meter": "230073516A", "col": 3}, {"label": "2z", "meter": "230847105A", "col": 4}, {"label": "2c", "meter": "230039695A", "col": 5}, {"label": "3z", "meter": "230847002A", "col": 6}, {"label": "3c", "meter": "230073562A", "col": 7}, {"label": "4z", "meter": "230847095A", "col": 8}, {"label": "4c", "meter": "230073503A", "col": 9}, {"label": "5z", "meter": "230847071A", "col": 10}, {"label": "5c", "meter": "230073548A", "col": 11}, {"label": "6z", "meter": "230847093A", "col": 12}, {"label": "6c", "meter": "230073501A", "col": 13}, {"label": "7z", "meter": "230847008A", "col": 14}, {"label": "7c", "meter": "221368770A", "col": 15}, {"label": "8z", "meter": "230847091A", "col": 16}, {"label": "8c", "meter": "230073563A", "col": 17}, {"label": "9z", "meter": "230847019A", "col": 18}, {"label": "9c", "meter": "230073559A", "col": 19}, {"label": "10z", "meter": "230846982A", "col": 20}, {"label": "10c", "meter": "230039698A", "col": 21}, {"label": "11z", "meter": "230847014A", "col": 22}, {"label": "11c", "meter": "230073526A", "col": 23}, {"label": "12z", "meter": "230847031A", "col": 24}, {"label": "12c", "meter": "221368732A", "col": 25}], "rows": [{"date": "13.01.2026", "data": {"230847030A": 1.484, "230073516A": 0.054, "230847105A": 1.272, "230039695A": 0.276, "230847002A": 1.953, "230073562A": 3.084, "230847095A": 2.087, "230073503A": 1.073, "230847071A": 0.555, "230073548A": 0.042, "230847093A": 3.316, "230073501A": 2.428, "230847008A": 0.549, "221368770A": 0.228, "230847091A": 63.107, "230073563A": 0.064, "230847019A": 0.302, "230073559A": 0.055, "230846982A": 0.893, "230039698A": 0.128, "230847014A": 1.185, "230073526A": 0.128, "230847031A": 0.508, "221368732A": 0.127}}, {"date": "30.01.2026", "data": {"230847030A": 1.482, "230073516A": 0.054, "230847105A": 2.13, "230039695A": 1.383, "230847002A": 2.396, "230073562A": 3.331, "230847095A": 2.102, "230073503A": 1.097, "230847071A": 1.169, "230073548A": 0.189, "230847093A": 3.431, "230073501A": 2.535, "230847008A": 0.554, "221368770A": 0.277, "230847091A": 63.131, "230073563A": 0.106, "230847019A": 0.343, "230073559A": 0.102, "230846982A": 0.9, "230039698A": 0.15, "230847014A": 1.217, "230073526A": 0.171, "230847031A": 3.235, "221368732A": 3.938}}, {"date": "03.03.2026", "data": {"230847030A": 5.29, "230073516A": 6.833, "230847105A": 30.928, "230039695A": 18.426, "230847002A": 7.031, "230073562A": 8.5, "230847095A": 5.042, "230073503A": 5.091, "230847071A": 2.885, "230073548A": 0.942, "230847093A": 6.35, "230073501A": 4.621, "230847008A": 4.29, "221368770A": 3.312, "230847091A": 81.428, "230073563A": 9.03, "230847019A": 4.927, "230073559A": 6.662, "230846982A": 2.858, "230039698A": 3.143, "230847014A": 12.5, "230073526A": 13.562, "230847031A": 12.612, "221368732A": 17.701}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]}};
const CIEPLO_DATA={"21": {"meters": [{"label": "kw1", "meter": "5042721", "col": 2}, {"label": "kw2", "meter": "5043366", "col": 3}, {"label": "kw3", "meter": "5042733", "col": 4}, {"label": "kw4", "meter": "5043361", "col": 5}, {"label": "kw5", "meter": "5042727", "col": 6}, {"label": "kw6", "meter": "5042698", "col": 7}, {"label": "kw7", "meter": "5042700", "col": 8}, {"label": "kw8", "meter": "5042724", "col": 9}, {"label": "kw9", "meter": "5043037", "col": 10}, {"label": "kw10", "meter": "5042726", "col": 11}, {"label": "kw11", "meter": "5042728", "col": 12}, {"label": "kw12", "meter": "5043379", "col": 13}], "rows": [{"date": "01.10.2025", "data": {"5042721": 7.7, "5043366": 10.0, "5042733": 6.8, "5043361": 7.7, "5042727": 8.2, "5042698": 5.8, "5042700": 5.0, "5042724": 7.6, "5043037": 5.7, "5042726": 0.0, "5042728": 0.0, "5043379": 0.0}}, {"date": "21.01.2026", "data": {"5042721": 14.98, "5043366": 14.863, "5042733": 11.195, "5043361": 15.318, "5042727": 11.286, "5042698": 10.956, "5042700": 8.4, "5042724": 15.538, "5043037": 10.325, "5042726": 0.345, "5042728": 0.317, "5043379": 0.276}}, {"date": "30.01.2026", "data": {"5042721": 16.187, "5043366": 16.653, "5042733": 12.602, "5043361": 16.946, "5042727": 11.975, "5042698": 12.4, "5042700": 9.09, "5042724": 17.022, "5043037": 11.43, "5042726": 1.156, "5042728": 1.152, "5043379": 0.954}}, {"date": "03.03.2026", "data": {"5042721": 17.934, "5043366": 19.752, "5042733": 14.666, "5043361": 19.097, "5042727": 14.107, "5042698": 13.721, "5042700": 10.333, "5042724": 18.902, "5043037": 12.99, "5042726": 3.086, "5042728": 3.269, "5043379": 2.685}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]}, "19": {"meters": [{"label": "kw1", "meter": "5042994", "col": 2}, {"label": "kw2", "meter": "5069824", "col": 3}, {"label": "kw3", "meter": "5066429", "col": 4}, {"label": "kw4", "meter": "5066433", "col": 5}, {"label": "kw5", "meter": "5066431", "col": 6}, {"label": "kw6", "meter": "5068432", "col": 7}, {"label": "kw9", "meter": "5069880", "col": 8}, {"label": "kw8", "meter": "5070084", "col": 9}, {"label": "kw7", "meter": "5069863", "col": 10}, {"label": "kw10", "meter": "5069825", "col": 11}, {"label": "kw11", "meter": "5059456", "col": 12}, {"label": "kw12", "meter": "5038000", "col": 13}], "rows": [{"date": "21.01.2026", "data": {"5042994": 3.597, "5069824": 5.645, "5066429": 2.893, "5066433": 14.725, "5066431": 14.488, "5068432": 15.629, "5069880": 13.111, "5070084": 11.065, "5069863": 14.038, "5069825": 13.197, "5059456": 17.175, "5038000": 1.727}}, {"date": "30.01.2026", "data": {"5042994": 5.292, "5069824": 7.717, "5066429": 4.23, "5066433": 15.867, "5066431": 16.074, "5068432": 17.129, "5069880": 14.514, "5070084": 11.144, "5069863": 15.464, "5069825": 14.123, "5059456": 19.102, "5038000": 3.069}}, {"date": "03.03.2026", "data": {"5042994": 7.824, "5069824": 10.503, "5066429": 6.292, "5066433": 17.316, "5066431": 18.283, "5068432": 19.313, "5069880": 16.509, "5070084": 11.527, "5069863": 17.558, "5069825": 16.151, "5059456": 21.685, "5038000": 5.488}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]}, "17": {"meters": [{"label": "kw1", "meter": "5078944", "col": 2}, {"label": "kw2", "meter": "5079647", "col": 3}, {"label": "kw3", "meter": "5079393", "col": 4}, {"label": "kw4", "meter": "5079640", "col": 5}, {"label": "kw5", "meter": "5079651", "col": 6}, {"label": "kw6", "meter": "5078948", "col": 7}, {"label": "kw7", "meter": "5079638", "col": 8}, {"label": "kw8", "meter": "5078951", "col": 9}, {"label": "kw9", "meter": "5078942", "col": 10}, {"label": "kw10", "meter": "5079120", "col": 11}, {"label": "kw11", "meter": "5078958", "col": 12}, {"label": "kw12", "meter": "5079122", "col": 13}], "rows": [{"date": "21.01.2026", "data": {"5078944": 4.254, "5079647": 3.255, "5079393": 4.882, "5079640": 6.662, "5079651": 5.657, "5078948": 6.731, "5079638": 0.279, "5078951": 6.238, "5078942": 3.949, "5079120": 6.903, "5078958": 6.197, "5079122": 6.67}}, {"date": "30.01.2026", "data": {"5078944": 5.792, "5079647": 3.548, "5079393": 6.191, "5079640": 8.112, "5079651": 6.775, "5078948": 7.33, "5079638": 0.279, "5078951": 7.33, "5078942": 4.643, "5079120": 8.831, "5078958": 7.497, "5079122": 7.612}}, {"date": "03.03.2026", "data": {"5078944": 7.01, "5079647": 4.557, "5079393": 7.598, "5079640": 8.727, "5079651": 9.072, "5078948": 7.822, "5079638": 1.662, "5078951": 9.104, "5078942": 5.974, "5079120": 9.963, "5078958": 9.595, "5079122": 9.078}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]}, "15": {"meters": [{"label": "kw1", "meter": "5078971", "col": 2}, {"label": "kw2", "meter": "5072842", "col": 3}, {"label": "kw3", "meter": "5077334", "col": 4}, {"label": "kw4", "meter": "5079634", "col": 5}, {"label": "kw5", "meter": "5078963", "col": 6}, {"label": "kw6", "meter": "5078959", "col": 7}, {"label": "kw7", "meter": "5068418", "col": 8}, {"label": "kw8", "meter": "5077065", "col": 9}, {"label": "kw9", "meter": "5069829", "col": 10}, {"label": "kw10", "meter": "5077519", "col": 11}, {"label": "kw11", "meter": "5068500", "col": 12}, {"label": "kw12", "meter": "5069881", "col": 13}], "rows": [{"date": "21.01.2026", "data": {"5078971": 0.308, "5072842": 0.374, "5077334": 0.137, "5079634": 13.544, "5078963": 3.736, "5078959": 0.0, "5068418": 0.103, "5077065": 0.0, "5069829": 0.335, "5077519": 0.15, "5068500": 0.419, "5069881": 0.243}}, {"date": "30.01.2026", "data": {"5078971": 0.469, "5072842": 1.317, "5077334": 0.485, "5079634": 13.544, "5078963": 5.086, "5078959": 0.434, "5068418": 0.289, "5077065": 0.0, "5069829": 0.442, "5077519": 0.394, "5068500": 1.071, "5069881": 1.414}}, {"date": "03.03.2026", "data": {"5078971": 2.587, "5072842": 3.628, "5077334": 2.096, "5079634": 14.784, "5078963": 7.23, "5078959": 2.589, "5068418": 1.741, "5077065": 1.476, "5069829": 2.468, "5077519": 2.556, "5068500": 3.771, "5069881": 3.833}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]}};
const GAZ_DATA={"meters": [{"bud": "21", "meter": "282405307798", "col": 2}, {"bud": "19", "meter": "282405307802", "col": 3}, {"bud": "17", "meter": "282405307754", "col": 4}, {"bud": "15", "meter": "282405307718", "col": 5}], "rows": [{"date": "01.10.2025", "data": {"282405307798": 719.0, "282405307802": 444.0, "282405307754": 162.0, "282405307718": 92.0}}, {"date": "01.11.2025", "data": {}}, {"date": "31.12.2025", "data": {"282405307798": 744.0, "282405307802": 467.0, "282405307754": 258.0, "282405307718": 125.0}}, {"date": "24.01.2026", "data": {"282405307798": 851.0, "282405307802": 515.0, "282405307754": 380.0, "282405307718": 130.0}}, {"date": "30.01.2026", "data": {"282405307798": 911.0, "282405307802": 535.0, "282405307754": 402.0, "282405307718": 141.0}}, {"date": "03.03.2026", "data": {"282405307798": 1080.0, "282405307802": 711.0, "282405307754": 445.0, "282405307718": 338.0}}, {"date": "01.04.2026", "data": {}}, {"date": "01.05.2026", "data": {}}, {"date": "01.06.2026", "data": {}}, {"date": "01.07.2026", "data": {}}, {"date": "01.08.2026", "data": {}}, {"date": "01.09.2026", "data": {}}, {"date": "01.10.2026", "data": {}}, {"date": "01.11.2026", "data": {}}, {"date": "01.12.2026", "data": {}}]};
const REGISTRY={"230565273A": {"bud": "21", "apt": "1z", "type": "woda", "sub": "zimna"}, "221368706A": {"bud": "21", "apt": "1c", "type": "woda", "sub": "ciepla"}, "230565268A": {"bud": "21", "apt": "2z", "type": "woda", "sub": "zimna"}, "221368766A": {"bud": "21", "apt": "2c", "type": "woda", "sub": "ciepla"}, "230095926A": {"bud": "21", "apt": "3z", "type": "woda", "sub": "zimna"}, "221368685A": {"bud": "21", "apt": "3c", "type": "woda", "sub": "ciepla"}, "230095938A": {"bud": "21", "apt": "4z", "type": "woda", "sub": "zimna"}, "221368785A": {"bud": "21", "apt": "4c", "type": "woda", "sub": "ciepla"}, "230095934A": {"bud": "21", "apt": "5z", "type": "woda", "sub": "zimna"}, "221368747A": {"bud": "21", "apt": "5c", "type": "woda", "sub": "ciepla"}, "230095907A": {"bud": "21", "apt": "6z", "type": "woda", "sub": "zimna"}, "221368748A": {"bud": "21", "apt": "6c", "type": "woda", "sub": "ciepla"}, "230565269A": {"bud": "21", "apt": "7z", "type": "woda", "sub": "zimna"}, "221368715A": {"bud": "21", "apt": "7c", "type": "woda", "sub": "ciepla"}, "230095927A": {"bud": "21", "apt": "8z", "type": "woda", "sub": "zimna"}, "221368786A": {"bud": "21", "apt": "8c", "type": "woda", "sub": "ciepla"}, "230565250A": {"bud": "21", "apt": "9z", "type": "woda", "sub": "zimna"}, "221368769A": {"bud": "21", "apt": "9c", "type": "woda", "sub": "ciepla"}, "230565246A": {"bud": "21", "apt": "10z", "type": "woda", "sub": "zimna"}, "221368771A": {"bud": "21", "apt": "10c", "type": "woda", "sub": "ciepla"}, "230095670A": {"bud": "21", "apt": "11z", "type": "woda", "sub": "zimna"}, "221368736A": {"bud": "21", "apt": "11c", "type": "woda", "sub": "ciepla"}, "230565266A": {"bud": "21", "apt": "12z", "type": "woda", "sub": "zimna"}, "221368714A": {"bud": "21", "apt": "12c", "type": "woda", "sub": "ciepla"}, "230847103A": {"bud": "19", "apt": "1z", "type": "woda", "sub": "zimna"}, "230073575A": {"bud": "19", "apt": "1c", "type": "woda", "sub": "ciepla"}, "230847015A": {"bud": "19", "apt": "2z", "type": "woda", "sub": "zimna"}, "230073561A": {"bud": "19", "apt": "2c", "type": "woda", "sub": "ciepla"}, "230847001A": {"bud": "19", "apt": "3z", "type": "woda", "sub": "zimna"}, "230073576A": {"bud": "19", "apt": "3c", "type": "woda", "sub": "ciepla"}, "230847081A": {"bud": "19", "apt": "4z", "type": "woda", "sub": "zimna"}, "230073550A": {"bud": "19", "apt": "4c", "type": "woda", "sub": "ciepla"}, "230847050A": {"bud": "19", "apt": "5z", "type": "woda", "sub": "zimna"}, "230073549A": {"bud": "19", "apt": "5c", "type": "woda", "sub": "ciepla"}, "230847000A": {"bud": "19", "apt": "6z", "type": "woda", "sub": "zimna"}, "230073551A": {"bud": "19", "apt": "6c", "type": "woda", "sub": "ciepla"}, "230847104A": {"bud": "19", "apt": "7z", "type": "woda", "sub": "zimna"}, "230073571A": {"bud": "19", "apt": "7c", "type": "woda", "sub": "ciepla"}, "230847079A": {"bud": "19", "apt": "8z", "type": "woda", "sub": "zimna"}, "230073513A": {"bud": "19", "apt": "8c", "type": "woda", "sub": "ciepla"}, "230847092A": {"bud": "19", "apt": "9z", "type": "woda", "sub": "zimna"}, "230073573A": {"bud": "19", "apt": "9c", "type": "woda", "sub": "ciepla"}, "230847060A": {"bud": "19", "apt": "10z", "type": "woda", "sub": "zimna"}, "230013560A": {"bud": "19", "apt": "10c", "type": "woda", "sub": "ciepla"}, "230847039A": {"bud": "19", "apt": "11z", "type": "woda", "sub": "zimna"}, "230073558A": {"bud": "19", "apt": "11c", "type": "woda", "sub": "ciepla"}, "320847113A": {"bud": "19", "apt": "12z", "type": "woda", "sub": "zimna"}, "230073577A": {"bud": "19", "apt": "12c", "type": "woda", "sub": "ciepla"}, "230846995A": {"bud": "17", "apt": "1z", "type": "woda", "sub": "zimna"}, "230073557A": {"bud": "17", "apt": "1c", "type": "woda", "sub": "ciepla"}, "230846979A": {"bud": "17", "apt": "2z", "type": "woda", "sub": "zimna"}, "221368717A": {"bud": "17", "apt": "2c", "type": "woda", "sub": "ciepla"}, "230847029A": {"bud": "17", "apt": "3z", "type": "woda", "sub": "zimna"}, "221368738A": {"bud": "17", "apt": "3c", "type": "woda", "sub": "ciepla"}, "230847017A": {"bud": "17", "apt": "4z", "type": "woda", "sub": "zimna"}, "221368778A": {"bud": "17", "apt": "4c", "type": "woda", "sub": "ciepla"}, "230847016A": {"bud": "17", "apt": "5z", "type": "woda", "sub": "zimna"}, "221368692A": {"bud": "17", "apt": "5c", "type": "woda", "sub": "ciepla"}, "230846981A": {"bud": "17", "apt": "6z", "type": "woda", "sub": "zimna"}, "230346378A": {"bud": "17", "apt": "6c", "type": "woda", "sub": "ciepla"}, "230847020A": {"bud": "17", "apt": "7z", "type": "woda", "sub": "zimna"}, "230073566A": {"bud": "17", "apt": "7c", "type": "woda", "sub": "ciepla"}, "230846980A": {"bud": "17", "apt": "8z", "type": "woda", "sub": "zimna"}, "230039689A": {"bud": "17", "apt": "8c", "type": "woda", "sub": "ciepla"}, "230846999A": {"bud": "17", "apt": "9z", "type": "woda", "sub": "zimna"}, "230073542A": {"bud": "17", "apt": "9c", "type": "woda", "sub": "ciepla"}, "230847049A": {"bud": "17", "apt": "10z", "type": "woda", "sub": "zimna"}, "230073556A": {"bud": "17", "apt": "10c", "type": "woda", "sub": "ciepla"}, "230847040A": {"bud": "17", "apt": "11z", "type": "woda", "sub": "zimna"}, "230073554A": {"bud": "17", "apt": "11c", "type": "woda", "sub": "ciepla"}, "230847003A": {"bud": "17", "apt": "12z", "type": "woda", "sub": "zimna"}, "230073544A": {"bud": "17", "apt": "12c", "type": "woda", "sub": "ciepla"}, "230847030A": {"bud": "15", "apt": "1z", "type": "woda", "sub": "zimna"}, "230073516A": {"bud": "15", "apt": "1c", "type": "woda", "sub": "ciepla"}, "230847105A": {"bud": "15", "apt": "2z", "type": "woda", "sub": "zimna"}, "230039695A": {"bud": "15", "apt": "2c", "type": "woda", "sub": "ciepla"}, "230847002A": {"bud": "15", "apt": "3z", "type": "woda", "sub": "zimna"}, "230073562A": {"bud": "15", "apt": "3c", "type": "woda", "sub": "ciepla"}, "230847095A": {"bud": "15", "apt": "4z", "type": "woda", "sub": "zimna"}, "230073503A": {"bud": "15", "apt": "4c", "type": "woda", "sub": "ciepla"}, "230847071A": {"bud": "15", "apt": "5z", "type": "woda", "sub": "zimna"}, "230073548A": {"bud": "15", "apt": "5c", "type": "woda", "sub": "ciepla"}, "230847093A": {"bud": "15", "apt": "6z", "type": "woda", "sub": "zimna"}, "230073501A": {"bud": "15", "apt": "6c", "type": "woda", "sub": "ciepla"}, "230847008A": {"bud": "15", "apt": "7z", "type": "woda", "sub": "zimna"}, "221368770A": {"bud": "15", "apt": "7c", "type": "woda", "sub": "ciepla"}, "230847091A": {"bud": "15", "apt": "8z", "type": "woda", "sub": "zimna"}, "230073563A": {"bud": "15", "apt": "8c", "type": "woda", "sub": "ciepla"}, "230847019A": {"bud": "15", "apt": "9z", "type": "woda", "sub": "zimna"}, "230073559A": {"bud": "15", "apt": "9c", "type": "woda", "sub": "ciepla"}, "230846982A": {"bud": "15", "apt": "10z", "type": "woda", "sub": "zimna"}, "230039698A": {"bud": "15", "apt": "10c", "type": "woda", "sub": "ciepla"}, "230847014A": {"bud": "15", "apt": "11z", "type": "woda", "sub": "zimna"}, "230073526A": {"bud": "15", "apt": "11c", "type": "woda", "sub": "ciepla"}, "230847031A": {"bud": "15", "apt": "12z", "type": "woda", "sub": "zimna"}, "221368732A": {"bud": "15", "apt": "12c", "type": "woda", "sub": "ciepla"}, "282405307798": {"bud": "21", "apt": "bud", "type": "gaz", "sub": ""}, "282405307802": {"bud": "19", "apt": "bud", "type": "gaz", "sub": ""}, "282405307754": {"bud": "17", "apt": "bud", "type": "gaz", "sub": ""}, "282405307718": {"bud": "15", "apt": "bud", "type": "gaz", "sub": ""}, "5042721": {"bud": "21", "apt": "kw1", "type": "cieplo", "sub": ""}, "5043366": {"bud": "21", "apt": "kw2", "type": "cieplo", "sub": ""}, "5042733": {"bud": "21", "apt": "kw3", "type": "cieplo", "sub": ""}, "5043361": {"bud": "21", "apt": "kw4", "type": "cieplo", "sub": ""}, "5042727": {"bud": "21", "apt": "kw5", "type": "cieplo", "sub": ""}, "5042698": {"bud": "21", "apt": "kw6", "type": "cieplo", "sub": ""}, "5042700": {"bud": "21", "apt": "kw7", "type": "cieplo", "sub": ""}, "5042724": {"bud": "21", "apt": "kw8", "type": "cieplo", "sub": ""}, "5043037": {"bud": "21", "apt": "kw9", "type": "cieplo", "sub": ""}, "5042726": {"bud": "21", "apt": "kw10", "type": "cieplo", "sub": ""}, "5042728": {"bud": "21", "apt": "kw11", "type": "cieplo", "sub": ""}, "5043379": {"bud": "21", "apt": "kw12", "type": "cieplo", "sub": ""}, "5042994": {"bud": "19", "apt": "kw1", "type": "cieplo", "sub": ""}, "5069824": {"bud": "19", "apt": "kw2", "type": "cieplo", "sub": ""}, "5066429": {"bud": "19", "apt": "kw3", "type": "cieplo", "sub": ""}, "5066433": {"bud": "19", "apt": "kw4", "type": "cieplo", "sub": ""}, "5066431": {"bud": "19", "apt": "kw5", "type": "cieplo", "sub": ""}, "5068432": {"bud": "19", "apt": "kw6", "type": "cieplo", "sub": ""}, "5069880": {"bud": "19", "apt": "kw9", "type": "cieplo", "sub": ""}, "5070084": {"bud": "19", "apt": "kw8", "type": "cieplo", "sub": ""}, "5069863": {"bud": "19", "apt": "kw7", "type": "cieplo", "sub": ""}, "5069825": {"bud": "19", "apt": "kw10", "type": "cieplo", "sub": ""}, "5059456": {"bud": "19", "apt": "kw11", "type": "cieplo", "sub": ""}, "5038000": {"bud": "19", "apt": "kw12", "type": "cieplo", "sub": ""}, "5078944": {"bud": "17", "apt": "kw1", "type": "cieplo", "sub": ""}, "5079647": {"bud": "17", "apt": "kw2", "type": "cieplo", "sub": ""}, "5079393": {"bud": "17", "apt": "kw3", "type": "cieplo", "sub": ""}, "5079640": {"bud": "17", "apt": "kw4", "type": "cieplo", "sub": ""}, "5079651": {"bud": "17", "apt": "kw5", "type": "cieplo", "sub": ""}, "5078948": {"bud": "17", "apt": "kw6", "type": "cieplo", "sub": ""}, "5079638": {"bud": "17", "apt": "kw7", "type": "cieplo", "sub": ""}, "5078951": {"bud": "17", "apt": "kw8", "type": "cieplo", "sub": ""}, "5078942": {"bud": "17", "apt": "kw9", "type": "cieplo", "sub": ""}, "5079120": {"bud": "17", "apt": "kw10", "type": "cieplo", "sub": ""}, "5078958": {"bud": "17", "apt": "kw11", "type": "cieplo", "sub": ""}, "5079122": {"bud": "17", "apt": "kw12", "type": "cieplo", "sub": ""}, "5078971": {"bud": "15", "apt": "kw1", "type": "cieplo", "sub": ""}, "5072842": {"bud": "15", "apt": "kw2", "type": "cieplo", "sub": ""}, "5077334": {"bud": "15", "apt": "kw3", "type": "cieplo", "sub": ""}, "5079634": {"bud": "15", "apt": "kw4", "type": "cieplo", "sub": ""}, "5078963": {"bud": "15", "apt": "kw5", "type": "cieplo", "sub": ""}, "5078959": {"bud": "15", "apt": "kw6", "type": "cieplo", "sub": ""}, "5068418": {"bud": "15", "apt": "kw7", "type": "cieplo", "sub": ""}, "5077065": {"bud": "15", "apt": "kw8", "type": "cieplo", "sub": ""}, "5069829": {"bud": "15", "apt": "kw9", "type": "cieplo", "sub": ""}, "5077519": {"bud": "15", "apt": "kw10", "type": "cieplo", "sub": ""}, "5068500": {"bud": "15", "apt": "kw11", "type": "cieplo", "sub": ""}, "5069881": {"bud": "15", "apt": "kw12", "type": "cieplo", "sub": ""}};

let PGE=JSON.parse(JSON.stringify(PGE_BASE));
let pdfFile=null, pdfData={};
let currentTab='pge';
let photos=[]; // {file, url, meter, val, autoVal, status}

// ── Init ──────────────────────────────────────────────────────────────────
function openSettings(){
  const modal=document.getElementById('settings-modal');
  const inp=document.getElementById('api-key-settings');
  inp.value=localStorage.getItem('claude_api_key')||'';
  const ginp=document.getElementById('gemini-key-settings');
  if(ginp) ginp.value=localStorage.getItem('gemini_api_key')||'';
  const hasClaude=!!inp.value, hasGemini=!!(localStorage.getItem('gemini_api_key'));
  const statusEl=document.getElementById('api-status');
  if(hasClaude) statusEl.innerHTML='<span style="color:var(--gn)">&#10003; Claude API aktywny</span>';
  else if(hasGemini) statusEl.innerHTML='<span style="color:var(--gn)">&#10003; Google Gemini API aktywny (bezp&#322;atny)</span>';
  else statusEl.innerHTML='<span style="color:var(--yw)">&#9888; Brak klucza &mdash; Tesseract OCR (mniejsza dok&#322;adno&#347;&#263;)</span>';
  modal.style.display='flex';
}
function saveApiKey(){
  const claude=document.getElementById('api-key-settings').value.trim();
  const gemini=document.getElementById('gemini-key-settings').value.trim();
  if(claude) localStorage.setItem('claude_api_key',claude); else localStorage.removeItem('claude_api_key');
  if(gemini) localStorage.setItem('gemini_api_key',gemini); else localStorage.removeItem('gemini_api_key');
  document.getElementById('settings-modal').style.display='none';
  const active=claude?'Claude AI':gemini?'Gemini AI (bezp\u0142atny)':'Tesseract OCR';
  L('Zapisano \u2022 aktywny: '+active,'ok');
}
window.addEventListener('load',()=>{
  renderPge(); renderWoda(); renderCieplo(); renderGaz();
});

function setP(v){document.getElementById('prg').style.width=v+'%';}
function L(m,c){const lb=document.getElementById('logbar');lb.style.display='block';const d=document.createElement('div');d.className='ll '+(c||'');d.textContent=m;lb.appendChild(d);lb.scrollTop=9999;}
function closeModal(){document.getElementById('modal').style.display='none';}

// ── Tab switching ─────────────────────────────────────────────────────────
function switchTab(id,el){
  document.querySelectorAll('.sec').forEach(s=>s.classList.remove('active'));
  document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
  document.getElementById('sec-'+id).classList.add('active');
  el.classList.add('active');
  currentTab=id;
  document.getElementById('ctrl-pge').style.display=id==='pge'?'flex':'none';
  document.getElementById('ctrl-photo').style.display=['woda','cieplo','gaz'].includes(id)?'flex':'none';
}

// ── PGE ───────────────────────────────────────────────────────────────────
function setPdf(files){
  if(!files[0])return;
  pdfFile=files[0];
  document.getElementById('dz-lb').textContent=files[0].name;
  document.getElementById('dz-pdf').classList.add('done');
  document.getElementById('pdf-btn').disabled=false;
}

async function processPdf(){
  if(!pdfFile)return;
  document.getElementById('pdf-btn').disabled=true;
  document.getElementById('logbar').innerHTML='';
  setP(5); L('Wczytu\u0119 PDF...','nf');
  try{
    const buf=await pdfFile.arrayBuffer();
    const pdf=await pdfjsLib.getDocument({data:buf}).promise;
    const items=[];
    for(let p=1;p<=pdf.numPages;p++){
      const page=await pdf.getPage(p);
      const c=await page.getTextContent();
      for(const item of c.items){const t=item.str.trim();if(t)items.push(t);}
    }
    L('PDF: '+items.length+' element\u00f3w','ok'); setP(40);
    pdfData=parsePdf(items);
    const cnt=Object.keys(pdfData).length;
    if(!cnt){L('Nie znaleziono danych PGE!','er');document.getElementById('pdf-btn').disabled=false;return;}
    const dates=new Set();
    for(const m of Object.values(pdfData)) for(const d of Object.keys(m)) dates.add(d);
    const sorted=[...dates].sort(cmpDate);
    L('Znaleziono '+cnt+' licznik\u00f3w \u2022 daty: '+sorted.join(', '),'ok');
    setP(90); showModal(sorted);
  }catch(e){L('B\u0141\u0104D: '+e.message,'er');console.error(e);}
  document.getElementById('pdf-btn').disabled=false;
  setTimeout(()=>setP(0),500);
}

function parsePdf(items){
  const results={};
  const starts=[];
  for(let i=0;i<items.length;i++) if(items[i].includes('FAKTURA VAT NR')) starts.push(i);
  const clean=starts.filter((s,k)=>k===0||s-starts[k-1]>5);
  for(let k=0;k<clean.length;k++){
    const s=clean[k], e=clean[k+1]||items.length;
    const block=items.slice(s,e);
    let meter=null;
    for(const t of block){const m=t.match(/Licznik.{0,80}nr[\s\u00a0:]+(\d{7,12})/);if(m){meter=m[1];break;}}
    if(!meter) continue;
    let endDate=null;
    for(const t of block){const m=t.replace(/\u00a0/g,' ').match(/do\s+(\d{2}\/\d{2}\/\d{4})/);if(m){endDate=m[1];break;}}
    if(!endDate) continue;
    const fmt=raw=>{const[d,mo,y]=raw.split('/');return(d==='31'&&mo==='01')?raw:`${mo}.${y}`;};
    const zDate=fmt(endDate);
    const valCounts=new Map();
    for(const t of block){
      let v=null;
      if(/^\d+[,.]\d+$/.test(t)){v=parseFloat(t.replace(',','.'));if(v<=10)v=null;}
      else if(/^\d+$/.test(t)){v=parseInt(t);if(v<=10)v=null;}
      if(v!==null) valCounts.set(v,(valCounts.get(v)||0)+1);
    }
    const repeated=[...valCounts.entries()].filter(([,c])=>c>=3).map(([v])=>v).sort((a,b)=>b-a);
    if(!repeated.length) continue;
    const hasX=block.some(t=>t==='(X)');
    const readings={};
    if(hasX){
      const zVal=repeated[0], xVal=repeated.find(v=>v<zVal)||null;
      readings[zDate]=zVal;
      if(xVal!=null){let xr=null;for(const t of block)if(/^\d{2}\/\d{2}\/\d{4}$/.test(t)&&t!==endDate){xr=t;break;}if(xr){const xd=fmt(xr);if(xd!==zDate)readings[xd]=xVal;}}
    } else readings[zDate]=repeated[0];
    if(Object.keys(readings).length) results[meter]=readings;
  }
  console.log('Parsed:',Object.keys(results).length,'meters');
  return results;
}

function cmpDate(a,b){
  const p=s=>{if(s.includes('/')){const[d,m,y]=s.split('/');return new Date(y,m-1,d);}if(s.includes('.')){const[d,mo,y]=s.split('.');if(d.length===2&&y.length===4)return new Date(y,mo-1,d);}const[m,y]=s.split('.');return new Date(y,m-1,1);};
  return p(a)-p(b);
}

function showModal(dates){
  const c=document.getElementById('modal-dates'); c.innerHTML='';
  dates.forEach((d,i)=>{
    const lbl=document.createElement('label');
    lbl.className='modal-dates'+(i===dates.length-1?' sel':'');
    const r=document.createElement('input'); r.type='radio'; r.name='pdate'; r.value=d;
    if(i===dates.length-1) r.checked=true;
    r.onchange=()=>{document.querySelectorAll('.modal-dates label').forEach(l=>l.classList.remove('sel'));lbl.classList.add('sel');};
    const sp=document.createElement('span'); sp.textContent=d;
    lbl.appendChild(r); lbl.appendChild(sp); c.appendChild(lbl);
  });
  document.getElementById('modal').style.display='flex';
}

function applyPgeDate(){
  const sel=document.querySelector('input[name="pdate"]:checked');
  if(!sel)return;
  const date=sel.value; closeModal();
  PGE=JSON.parse(JSON.stringify(PGE_BASE));
  for(const bud of Object.keys(PGE)){
    for(const row of PGE[bud].rows){
      if(row.date===date){
        row.isPdf=true;
        for(const m of PGE[bud].meters)
          if(pdfData[m.meter]&&pdfData[m.meter][date]!=null)
            row.data[m.meter]=pdfData[m.meter][date];
      }
    }
  }
  document.getElementById('logbar').style.display='none';
  setP(0); renderPge();
}

function renderPge(){
  const sec=document.getElementById('sec-pge'); sec.innerHTML='';
  for(const bud of ['21','19','17','15']){
    const info=PGE[bud]; if(!info) continue;
    sec.appendChild(buildTable(bud,info.meters,info.rows,
      m=>m.apt==='ADM'?'ADM':'kw.\u00a0'+m.apt, m=>m.meter));
  }
}

// ── Generic table builder ─────────────────────────────────────────────────
function buildTable(bud, meters, rows, labelFn, meterFn){
  const block=document.createElement('div'); block.className='bud-block';
  const hd=document.createElement('div'); hd.className='bud-hd';
  const badge=document.createElement('div'); badge.className='bud-badge'; badge.textContent='BUD '+bud;
  hd.appendChild(badge); block.appendChild(hd);
  const wrap=document.createElement('div'); wrap.className='tbl-wrap';
  const tbl=document.createElement('table');
  const thead=document.createElement('thead');
  const r1=document.createElement('tr'); r1.className='r1';
  const r2=document.createElement('tr'); r2.className='r2';
  const h0a=document.createElement('th'); h0a.className='th0'; r1.appendChild(h0a);
  const h0b=document.createElement('th'); h0b.className='th0'; h0b.textContent='nr licznika'; r2.appendChild(h0b);
  for(const m of meters){
    const a=document.createElement('th'); a.textContent=labelFn(m); r1.appendChild(a);
    const b=document.createElement('th'); b.textContent=meterFn(m); r2.appendChild(b);
  }
  thead.appendChild(r1); thead.appendChild(r2); tbl.appendChild(thead);
  const tbody=document.createElement('tbody');
  for(const row of rows){
    const tr=document.createElement('tr');
    const td0=document.createElement('td'); td0.className='td0'; td0.textContent=row.date; tr.appendChild(td0);
    for(const m of meters){
      const td=document.createElement('td');
      const v=row.data[meterFn(m)];
      if(v!=null){
        td.textContent=String(v).replace('.',',');
        if(row.isPdf) td.className='pf';
        else if(row.isManual) td.className='man';
      } else {td.className='em'; td.textContent='\u2014';}
      tr.appendChild(td);
    }
    tbody.appendChild(tr);
  }
  tbl.appendChild(tbody); wrap.appendChild(tbl); block.appendChild(wrap);
  return block;
}

// ── Render Woda ───────────────────────────────────────────────────────────
function renderWoda(){
  const sec=document.getElementById('sec-woda'); sec.innerHTML='';
  for(const bud of ['21','19','17','15']){
    const info=WODA_DATA[bud]; if(!info) continue;
    sec.appendChild(buildTable(bud,info.meters,info.rows,
      m=>m.label, m=>m.meter));
  }
}

// ── Render Cieplo ─────────────────────────────────────────────────────────
function renderCieplo(){
  const sec=document.getElementById('sec-cieplo'); sec.innerHTML='';
  for(const bud of ['21','19','17','15']){
    const info=CIEPLO_DATA[bud]; if(!info) continue;
    sec.appendChild(buildTable(bud,info.meters,info.rows,
      m=>m.label, m=>m.meter));
  }
}

// ── Render Gaz ────────────────────────────────────────────────────────────
function renderGaz(){
  const sec=document.getElementById('sec-gaz'); sec.innerHTML='';
  const block=document.createElement('div'); block.className='bud-block';
  const hd=document.createElement('div'); hd.className='bud-hd';
  const badge=document.createElement('div'); badge.className='bud-badge'; badge.textContent='GAZ';
  hd.appendChild(badge); block.appendChild(hd);
  const wrap=document.createElement('div'); wrap.className='tbl-wrap';
  const tbl=document.createElement('table');
  const thead=document.createElement('thead');
  const r1=document.createElement('tr'); r1.className='r1';
  const r2=document.createElement('tr'); r2.className='r2';
  const h0a=document.createElement('th'); h0a.className='th0'; r1.appendChild(h0a);
  const h0b=document.createElement('th'); h0b.className='th0'; h0b.textContent='nr licznika'; r2.appendChild(h0b);
  for(const m of GAZ_DATA.meters){
    const a=document.createElement('th'); a.textContent='Bud '+m.bud; r1.appendChild(a);
    const b=document.createElement('th'); b.textContent=m.meter; r2.appendChild(b);
  }
  thead.appendChild(r1); thead.appendChild(r2); tbl.appendChild(thead);
  const tbody=document.createElement('tbody');
  for(const row of GAZ_DATA.rows){
    const tr=document.createElement('tr');
    const td0=document.createElement('td'); td0.className='td0'; td0.textContent=row.date; tr.appendChild(td0);
    for(const m of GAZ_DATA.meters){
      const td=document.createElement('td');
      const v=row.data[m.meter];
      if(v!=null){td.textContent=String(v).replace('.',','); if(row.isManual)td.className='man';}
      else{td.className='em';td.textContent='\u2014';}
      tr.appendChild(td);
    }
    tbody.appendChild(tr);
  }
  tbl.appendChild(tbody); wrap.appendChild(tbl); block.appendChild(wrap);
  document.getElementById('sec-gaz').appendChild(block);
}

// ── Photo handling ─────────────────────────────────────────────────────────
function addPhotos(files){
  const newFiles=Array.from(files);
  newFiles.forEach(f=>{
    const idx=photos.length;
    photos.push({file:f,url:'',meter:'',val:'',autoVal:'',status:'wait',info:null});
    // Read as data URL for preview
    const reader=new FileReader();
    reader.onload=e=>{photos[idx].url=e.target.result;renderPhotoGrid();};
    reader.readAsDataURL(f);
  });
  document.getElementById('ph-lb').textContent=photos.length+' zdj\u0119\u0107';
  document.getElementById('dz-ph').classList.add('done');
  document.getElementById('an-btn').style.display='inline-flex';
  renderPhotoGrid();
}

function renderPhotoGrid(){
  const tabId='sec-'+currentTab;
  let grid=document.getElementById('ph-grid-'+currentTab);
  if(!grid){
    const topbar=document.createElement('div'); topbar.className='ph-topbar';
    const dateLabel=document.createElement('span'); dateLabel.textContent='Data odczytu:'; dateLabel.style.cssText='font-size:12px;font-weight:600;color:var(--mt)';
    const dateInp=document.createElement('input'); dateInp.type='date'; dateInp.className='inp';
    dateInp.id='ph-date-'+currentTab;
    const today=new Date(); dateInp.value=today.toISOString().slice(0,10);
    const saveBtn=document.createElement('button');
    saveBtn.className='btn btn-gn btn-sm'; saveBtn.textContent='Zapisz do tabeli';
    saveBtn.onclick=()=>savePhotosToTable(currentTab);
    topbar.appendChild(dateLabel); topbar.appendChild(dateInp); topbar.appendChild(saveBtn);
    grid=document.createElement('div'); grid.className='ph-grid'; grid.id='ph-grid-'+currentTab;
    const sec=document.getElementById(tabId);
    // Prepend
    sec.insertBefore(topbar,sec.firstChild);
    sec.insertBefore(grid,sec.children[1]||null);
  }
  grid.innerHTML='';
  const tabType=currentTab; // 'woda','cieplo','gaz'
  photos.forEach((ph,i)=>{
    const card=document.createElement('div');
    card.className='ph-card '+(ph.status==='ok'?'ok':ph.status==='err'?'err':'wait');
    const img=document.createElement('img'); img.src=ph.url; img.className='ph-img';
    const body=document.createElement('div'); body.className='ph-body';
    const meterEl=document.createElement('div'); meterEl.className='ph-meter';
    meterEl.textContent=ph.meter||'Nie rozpoznano';
    const adrEl=document.createElement('div'); adrEl.className='ph-adr';
    if(ph.info) adrEl.textContent='Bud '+ph.info.bud+' \u2022 '+ph.info.apt;
    else adrEl.textContent='\u2014';
    const valRow=document.createElement('div'); valRow.className='ph-val-row';
    const valInp=document.createElement('input'); valInp.type='number'; valInp.step='0.001';
    valInp.className='ph-val'+(ph.autoVal?' auto':ph.val?' manual':'');
    valInp.placeholder='Wpisz...'; valInp.value=ph.val||ph.autoVal||'';
    valInp.onchange=e=>{photos[i].val=e.target.value;valInp.className='ph-val manual';};
    const statusEl=document.createElement('div'); statusEl.className='ph-status '+(ph.status==='ok'?'ok':ph.status==='err'?'err':'');
    statusEl.textContent=ph.status==='ok'?'OCR gotowy':ph.status==='err'?'B\u0142\u0105d OCR':ph.status==='wait'?'Oczekuje':'';
    valRow.appendChild(valInp);
    body.appendChild(meterEl); body.appendChild(adrEl); body.appendChild(valRow); body.appendChild(statusEl);
    card.appendChild(img); card.appendChild(body);
    grid.appendChild(card);
  });
}

async function analyzeAll(){
  if(!photos.length) return;
  const btn=document.getElementById('an-btn'); btn.disabled=true;
  const apiKey=localStorage.getItem('claude_api_key')||'';
  L(apiKey?'Analiza z Claude Vision AI...':'Analiza Tesseract OCR (bez klucza API)...','nf');
  for(let i=0;i<photos.length;i++){
    L(`[${i+1}/${photos.length}] ${photos[i].file.name}`,'nf');
    try{
      const result=await analyzePhoto(photos[i].file);
      photos[i].meter=result.meter||'';
      photos[i].autoVal=result.value||'';
      photos[i].val=result.value||'';
      photos[i].info=result.meter?REGISTRY[result.meter]||null:null;
      photos[i].status=result.meter?'ok':'err';
      L('  '+(result.meter?'\u2713 '+result.meter+' = '+result.value:'? Nie rozpoznano'),'ok');
    }catch(e){photos[i].status='err';L('  X '+e.message,'er');}
    renderPhotoGrid();
  }
  btn.disabled=false;
  document.getElementById('zip-btn').style.display='inline-flex';
  L('Gotowe! Sprawdź wartości i kliknij "Zapisz do tabeli"','ok');
}

async function analyzePhoto(file){
  const apiKey=localStorage.getItem('claude_api_key')||'';
  const geminiKey=localStorage.getItem('gemini_api_key')||'';

  // Read file as base64
  const b64=await new Promise((res,rej)=>{
    const r=new FileReader();
    r.onload=()=>res(r.result.split(',')[1]);
    r.onerror=rej;
    r.readAsDataURL(file);
  });
  const mediaType=file.type||'image/jpeg';

  const prompt=`This is a utility meter photo. Extract:
1. SERIAL NUMBER: on the label or barcode area (12 digits for gas e.g. 282405307798, or alphanumeric ending with letter for water e.g. 230565268A, or 7 digits for heat)
2. READING: the main number on the LCD display (for Goldcard gas meters: the upper larger number e.g. 338, 1080; for water/heat: decimal number e.g. 14.822)
Respond ONLY with JSON: {"meter":"SERIAL","value":"READING"} - use null if unclear.`;

  // Try Claude API
  if(apiKey){
    try{
      const resp=await fetch('https://api.anthropic.com/v1/messages',{
        method:'POST',
        headers:{'Content-Type':'application/json','x-api-key':apiKey,'anthropic-version':'2023-06-01','anthropic-dangerous-direct-browser-access':'true'},
        body:JSON.stringify({model:'claude-haiku-4-5-20251001',max_tokens:150,
          messages:[{role:'user',content:[
            {type:'image',source:{type:'base64',media_type:mediaType,data:b64}},
            {type:'text',text:prompt}
          ]}]})
      });
      const data=await resp.json();
      if(data.content?.[0]?.text){
        const parsed=JSON.parse(data.content[0].text.replace(/```json|```/g,'').trim());
        console.log('Claude result:',parsed);
        return {meter:parsed.meter||null,value:parsed.value?String(parsed.value):null};
      }
    }catch(e){console.warn('Claude error:',e.message);}
  }

  // Try Gemini API (free tier)
  if(geminiKey){
    try{
      const resp=await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-lite:generateContent?key=${geminiKey}`,{
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body:JSON.stringify({contents:[{parts:[
          {inline_data:{mime_type:mediaType,data:b64}},
          {text:prompt+'\nJSON only, no markdown.'}
        ]}],generationConfig:{maxOutputTokens:100,temperature:0}})
      });
      const data=await resp.json();
      const text=data.candidates?.[0]?.content?.parts?.[0]?.text||'{}';
      const parsed=JSON.parse(text.replace(/```json|```/g,'').trim());
      console.log('Gemini result:',parsed);
      return {meter:parsed.meter||null,value:parsed.value?String(parsed.value):null};
    }catch(e){console.warn('Gemini error:',e.message);}
  }

  // Fallback: Tesseract
  if(!window._tess){
    window._tess=await Tesseract.createWorker('eng',1,{});
  }
  const {data:{text}}=await window._tess.recognize(file);
  const raw=text.replace(/\n/g,' ').replace(/\s+/g,' ').trim();
  console.log('Tesseract raw:',raw.substring(0,150));
  let foundMeter=null;
  const normRaw=raw.replace(/\s/g,'');
  for(const key of Object.keys(REGISTRY).sort((a,b)=>b.length-a.length)){
    if(normRaw.toUpperCase().includes(key.toUpperCase())){foundMeter=key;break;}
  }
  const threeDecimals=[...raw.matchAll(/(\d+)[.,](\d{3})/g)].map(m=>parseFloat(m[1]+'.'+m[2]));
  const integers=[...raw.matchAll(/\b(\d{3,6})\b/g)].map(m=>parseInt(m[1]));
  let value=null;
  if(threeDecimals.length) value=String(threeDecimals.filter(v=>v>0&&v<999999)[0]||'');
  else if(integers.length) value=String(integers.filter(v=>v>=10&&v<999999)[0]||'');
  return {meter:foundMeter,value:value||null};
}

function savePhotosToTable(tabType){
  const dateInp=document.getElementById('ph-date-'+tabType);
  if(!dateInp){alert('Brak daty!');return;}
  const d=new Date(dateInp.value);
  const dateStr=`${String(d.getDate()).padStart(2,'0')}.${String(d.getMonth()+1).padStart(2,'0')}.${d.getFullYear()}`;

  let saved=0;
  for(const ph of photos){
    if(!ph.meter||!ph.val||!ph.info) continue;
    if(ph.info.type!==tabType&&!(tabType==='woda'&&ph.info.type==='woda')) continue;
    const val=parseFloat(ph.val); if(isNaN(val)) continue;

    let dataset=null;
    if(tabType==='woda'&&ph.info) dataset=WODA_DATA[ph.info.bud];
    if(tabType==='cieplo'&&ph.info) dataset=CIEPLO_DATA[ph.info.bud];
    if(tabType==='gaz') dataset=GAZ_DATA;
    if(!dataset) continue;

    // Find or add row
    let row=dataset.rows.find(r=>r.date===dateStr);
    if(!row){row={date:dateStr,data:{},isManual:true};dataset.rows.push(row);dataset.rows.sort((a,b)=>cmpDate(a.date,b.date));}
    row.data[ph.meter]=val;
    row.isManual=true;
    saved++;
  }

  if(tabType==='woda') renderWoda();
  if(tabType==='cieplo') renderCieplo();
  if(tabType==='gaz') renderGaz();
  L(`Zapisano ${saved} odczyt\u00f3w do tabeli (${dateStr})`,'ok');
}

// ── Copy table ────────────────────────────────────────────────────────────
async function downloadZip(){
  if(!photos.length) return;
  const zip=new JSZip();
  const folder=zip.folder('liczniki');
  let count=0;

  for(const ph of photos){
    const ext=ph.file.name.split('.').pop().toLowerCase()||'jpg';
    let newName=ph.file.name; // default: keep original

    if(ph.meter && ph.info){
      const info=ph.info;
      const typeMap={woda:'woda',gaz:'gaz',cieplo:'cieplo'};
      const t=typeMap[info.type]||info.type;

      if(info.type==='gaz'){
        // Gas: one per building → bud21_gaz_282405307798.jpg
        newName=`bud${info.bud}_${t}_${ph.meter}.${ext}`;
      } else if(info.type==='woda'){
        // Water: bud21_kw3_woda_zimna_230565268A.jpg
        const sub=info.sub?`_${info.sub}`:'';
        newName=`bud${info.bud}_${info.apt}_${t}${sub}_${ph.meter}.${ext}`;
      } else {
        // Heat: bud21_kw3_cieplo_5042721.jpg
        newName=`bud${info.bud}_${info.apt}_${t}_${ph.meter}.${ext}`;
      }
    } else if(!ph.meter){
      // Unknown meter - keep original name but add prefix
      newName=`_nieznany_${ph.file.name}`;
    }

    // Handle duplicates
    let finalName=newName;
    let dupIdx=1;
    const usedNames=Object.keys(folder.files||{});
    while(usedNames.includes('liczniki/'+finalName)){
      const base=newName.replace('.'+ext,'');
      finalName=`${base}_${dupIdx}.${ext}`;
      dupIdx++;
    }

    folder.file(finalName, ph.file);
    count++;
  }

  const blob=await zip.generateAsync({type:'blob'});
  const url=URL.createObjectURL(blob);
  const a=document.createElement('a');
  const today=new Date();
  const ds=`${String(today.getDate()).padStart(2,'0')}${String(today.getMonth()+1).padStart(2,'0')}${today.getFullYear()}`;
  a.href=url; a.download=`liczniki_${ds}.zip`; a.click();
  URL.revokeObjectURL(url);
  L(`Pobrano ZIP z ${count} zdj\u0119ciami (przemianowane)`, 'ok');
}

async function copyTable(e){
  const btn=e.target;
  const tables=document.querySelectorAll('#sec-'+currentTab+' table');
  const lines=[];
  tables.forEach(tbl=>{
    for(const row of tbl.rows){
      const cells=[];
      for(const cell of row.cells){
        if(cell.colSpan>1){cells.push(cell.textContent.trim());for(let i=1;i<cell.colSpan;i++)cells.push('');}
        else cells.push(cell.textContent.trim().replace('\u2014',''));
      }
      lines.push(cells.join('\t'));
    }
    lines.push('');
  });
  try{
    await navigator.clipboard.writeText(lines.join('\n'));
    const orig=btn.textContent; btn.textContent='\u2713 Skopiowano!'; btn.style.background='#14532d';
    setTimeout(()=>{btn.textContent=orig;btn.style.background='';},2000);
  }catch(err){alert('B\u0142\u0105d: '+err.message);}
}
</script>
</body>
</html>
