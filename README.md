<!DOCTYPE html>
<html lang="pl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Liczniki — PGE</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
<style>
:root{
  --bg:#f4f3ef;--sf:#fff;--sf2:#eceae3;--bd:#dedad0;--bd2:#b5b2a8;
  --tx:#1a1917;--mt:#6b6960;--ac:#1855a3;--acl:#e8f0fb;
  --gn:#15572f;--gnb:#d9f5e5;--gm:#1a9e4e;--r:9px;--rs:5px;
}
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:'Segoe UI',system-ui,sans-serif;background:var(--bg);color:var(--tx);font-size:13px;}
input[type=file]{display:none;}

/* Top bar */
.topbar{background:#fff;border-bottom:1px solid var(--bd);padding:.75rem 1.5rem;display:flex;align-items:center;gap:14px;position:sticky;top:0;z-index:10;}
.logo{display:flex;align-items:center;gap:8px;font-weight:700;font-size:15px;flex-shrink:0;}
.logo-i{width:28px;height:28px;border-radius:6px;background:#1855a3;color:#fff;display:grid;place-items:center;font-size:16px;}

/* Toolbar inside topbar */
.toolbar{display:flex;align-items:center;gap:8px;flex-wrap:wrap;margin-left:.5rem;}
.sep-v{width:1px;height:20px;background:var(--bd);margin:0 4px;}

/* Drop zone (compact) */
.dz-mini{display:flex;align-items:center;gap:8px;padding:5px 12px;border:1.5px dashed var(--bd2);border-radius:var(--rs);cursor:pointer;transition:all .15s;background:var(--bg);}
.dz-mini:hover,.dz-mini.drag{border-color:var(--ac);background:var(--acl);}
.dz-mini.done{border-style:solid;border-color:var(--gm);background:var(--gnb);}
.dz-mini span{font-size:12px;font-weight:600;color:var(--mt);}
.dz-mini.done span{color:var(--gn);}

/* Buttons */
.btn{padding:6px 13px;border:1px solid var(--bd);border-radius:var(--rs);background:var(--sf);color:var(--tx);font-size:12px;font-weight:600;cursor:pointer;transition:all .15s;font-family:inherit;white-space:nowrap;}
.btn:hover{background:var(--sf2);}.btn:disabled{opacity:.4;cursor:default;}
.btn-blue{background:var(--ac);color:#fff;border:none;}.btn-blue:hover{opacity:.88;background:var(--ac);}
.btn-green{background:var(--gm);color:#fff;border:none;}.btn-green:hover{opacity:.88;}
.btn-sm{padding:4px 10px;font-size:11.5px;}

/* Date chips */
.chip{padding:3px 10px;border:1px solid var(--bd);border-radius:20px;cursor:pointer;font-size:11.5px;font-weight:600;color:var(--mt);transition:all .12s;white-space:nowrap;}
.chip:hover{border-color:var(--ac);color:var(--ac);}
.chip.on{background:var(--ac);color:#fff;border-color:var(--ac);}

/* Log */
.log-wrap{background:#17181b;margin:.5rem 1.5rem 0;border-radius:var(--rs);padding:6px 12px;display:none;}
.ll{font-family:monospace;font-size:11px;padding:.5px 0;line-height:1.5;}
.ok{color:#86efac;}.er{color:#fca5a5;}.nf{color:#93c5fd;}.wn{color:#fcd34d;}

/* Progress */
.prg-line{height:2px;background:var(--ac);width:0%;transition:width .3s;position:fixed;top:0;left:0;z-index:100;}

/* Table area */
.tbl-area{padding:.875rem 1.5rem;}

/* Building block */
.bud-block{margin-bottom:1.5rem;}
.bud-title{font-size:11px;font-weight:700;letter-spacing:.08em;text-transform:uppercase;color:var(--mt);margin-bottom:.5rem;padding-left:2px;}

.tbl-scroll{overflow-x:auto;border:1px solid var(--bd);border-radius:var(--r);}
table{border-collapse:collapse;font-size:12px;white-space:nowrap;width:100%;}

/* Header rows */
thead tr.row-apt th{background:#18181b;color:#aaa;font-size:10px;font-weight:600;padding:4px 8px;text-align:center;border-right:1px solid #fff1;}
thead tr.row-apt th.th-date{background:#18181b;color:#aaa;text-align:left;position:sticky;left:0;z-index:3;}
thead tr.row-meter th{background:#23242a;color:#fff;font-size:10.5px;font-weight:600;padding:5px 8px;text-align:center;border-right:1px solid #ffffff10;font-family:monospace;}
thead tr.row-meter th.th-date{background:#23242a;color:#aaa;text-align:left;font-family:inherit;position:sticky;left:0;z-index:3;font-size:10px;}

/* Body */
tbody tr td{padding:4px 8px;border-bottom:1px solid var(--bd);border-right:1px solid var(--bd);text-align:right;font-family:monospace;font-size:11.5px;}
tbody tr td.td-date{text-align:left;font-family:inherit;font-size:11.5px;color:var(--mt);font-weight:600;position:sticky;left:0;background:var(--sf);z-index:1;padding-left:10px;min-width:90px;}
tbody tr:hover td{background:#f0f3fb;}
tbody tr:hover td.td-date{background:#e8edf8;}
tbody tr td.empty{color:#d0cdc6;text-align:center;font-family:inherit;font-size:11px;}
tbody tr td.new-val{background:#fff8e0;font-weight:700;color:#7c4700;}
tbody tr td.new-val.ok-val{background:var(--gnb);color:var(--gn);}
tbody tr:hover td.new-val{background:#fef0c0;}
tbody tr:hover td.new-val.ok-val{background:#c8f0d8;}

/* Status bar */
.statusbar{font-size:11px;color:var(--mt);padding:.4rem 1.5rem;border-top:1px solid var(--bd);background:var(--sf);}
</style>
</head>
<body>

<div class="prg-line" id="prg"></div>

<div class="topbar">
  <div class="logo"><div class="logo-i">&#9889;</div>Liczniki PGE</div>
  <div class="sep-v"></div>

  <!-- PDF drop zone -->
  <div class="dz-mini" id="z-pdf" onclick="document.getElementById('f-pdf').click()" ondragover="dov(event,this)" ondragleave="dol(this)" ondrop="dropPdf(event,this)">
    <span style="font-size:16px;">&#128196;</span>
    <span id="fn-pdf">Wgraj faktur&#281; PDF&#8230;</span>
  </div>
  <input type="file" id="f-pdf" accept=".pdf" onchange="setPdf(this.files)">

  <button class="btn btn-blue" id="pdf-btn" onclick="processPdf()" disabled>Wczytaj &#8594;</button>

  <div class="sep-v"></div>

  <!-- Date chips — appear after PDF loaded -->
  <div id="chips-wrap" style="display:flex;align-items:center;gap:5px;flex-wrap:wrap;display:none;">
    <span style="font-size:11px;color:var(--mt);font-weight:600;">Wybierz dat&#281;:</span>
    <div id="chips" style="display:flex;gap:5px;flex-wrap:wrap;"></div>
  </div>

  <div class="sep-v" id="sep-copy" style="display:none;"></div>
  <button class="btn btn-sm" id="copy-btn" style="display:none;" onclick="copyTsv()">&#128203; Kopiuj</button>
</div>

<!-- Log -->
<div class="log-wrap" id="log-wrap">
  <div id="log"></div>
</div>

<!-- Tables -->
<div class="tbl-area" id="tbl-area"></div>

<div class="statusbar" id="statusbar">Tabela wskazan&#769; &mdash; energia elektryczna</div>

<script>
pdfjsLib.GlobalWorkerOptions.workerSrc='https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';

const TABLE = {"21": {"meters": [{"apt": "ADM", "meter": "31445196"}, {"apt": "1", "meter": "10020452"}, {"apt": "2", "meter": "10020428"}, {"apt": "3", "meter": "10020418"}, {"apt": "4", "meter": "10020475"}, {"apt": "5", "meter": "10021420"}, {"apt": "6", "meter": "10021257"}, {"apt": "7", "meter": "10021310"}, {"apt": "8", "meter": "10020478"}, {"apt": "9", "meter": "10021358"}, {"apt": "10", "meter": "10021303"}, {"apt": "11", "meter": "10020497"}, {"apt": "12", "meter": "10020424"}], "rows": [{"date": "08.2025", "data": {"31445196": 1231.3, "10020452": 3245.1, "10020428": 1052.8, "10020418": 1736.5, "10020475": 3914.5, "10021420": 11020.7, "10021257": 23092.7, "10021310": 3054.3, "10020478": 1346.8, "10021358": 2145.5, "10021303": 824.4, "10020497": 2964.3, "10020424": 558.2}, "isPdf": false}, {"date": "09.2025", "data": {"31445196": 2805.5, "10020452": 3255.7, "10020428": 1057.3, "10020418": 1745.3, "10020475": 3922.4, "10021420": 11038.4, "10021257": 23106.9, "10021310": 3067.8, "10020478": 1364.9, "10021358": 2160.4, "10021303": 878.5, "10020497": 3054.1, "10020424": 567.1}, "isPdf": false}, {"date": "10.2025", "data": {"31445196": 7637.1, "10020452": 3260.0, "10020428": 1142.9, "10020418": 1749.7, "10020475": 3963.3, "10021420": 11040.3, "10021257": 23107.8, "10021310": 3140.2, "10020478": 1466.2, "10021358": 2220.1, "10021303": 995.2, "10020497": 3157.8, "10020424": 797.0}, "isPdf": false}, {"date": "11.2025", "data": {"31445196": 8295.1, "10020452": 3262.0, "10020428": 1157.9, "10020418": 1751.7, "10020475": 3979.3, "10021420": 11050.3, "10021257": 23120.8, "10021310": 3151.2, "10020478": 1484.2, "10021358": 2236.1, "10021303": 1009.2, "10020497": 3169.8, "10020424": 821.0}, "isPdf": false}, {"date": "12.2025", "data": {"31445196": 14213.7, "10020452": 3282.8, "10020428": 1292.6, "10020418": 1772.9, "10020475": 4122.8, "10021420": 11137.3, "10021257": 23232.6, "10021310": 3250.8, "10020478": 1649.1, "10021358": 2380.4, "10021303": 1133.7, "10020497": 3278.2, "10020424": 1039.8}, "isPdf": false}, {"date": "31/01/2026", "data": {"31445196": 28990.2, "10020452": 3703.7, "10020428": 1592.6, "10020418": 2208.0, "10020475": 4500.6, "10021420": 11369.5, "10021257": 23605.8, "10021310": 3541.8, "10020478": 2015.1, "10021358": 2748.4, "10021303": 1495.9, "10020497": 3518.2, "10020424": 1525.8}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "19": {"meters": [{"apt": "ADM", "meter": "10021273"}, {"apt": "1", "meter": "10021304"}, {"apt": "2", "meter": "10021419"}, {"apt": "3", "meter": "10021363"}, {"apt": "4", "meter": "10020495"}, {"apt": "5", "meter": "10021415"}, {"apt": "6", "meter": "10021402"}, {"apt": "7", "meter": "10021263"}, {"apt": "8", "meter": "10021282"}, {"apt": "9", "meter": "10021278"}, {"apt": "10", "meter": "10020501"}, {"apt": "11", "meter": "10020429"}, {"apt": "12", "meter": "10015853"}], "rows": [{"date": "08.2025", "data": {"10021273": 3208.9, "10021304": 1613.3, "10021419": 3689.8, "10021363": 1314.2, "10020495": 2368.1, "10021415": 1987.8, "10021402": 2553.5, "10021263": 471.5, "10021282": 23730.8, "10021278": 5135.4, "10020501": 4532.8, "10020429": 1472.3, "10015853": 276.8}, "isPdf": false}, {"date": "09.2025", "data": {"10021273": 3633.8, "10021304": 1613.6, "10021419": 3706.9, "10021363": 1320.2, "10020495": 2368.4, "10021415": 1989.2, "10021402": 2566.96, "10021263": 473.0, "10021282": 23733.4, "10021278": 5138.1, "10020501": 4553.6, "10020429": 1483.0, "10015853": 291.6}, "isPdf": false}, {"date": "10.2025", "data": {"10021273": 4073.2, "10021304": 1620.5, "10021419": 3714.1, "10021363": 1335.0, "10020495": 2376.4, "10021415": 1995.5, "10021402": 2572.63, "10021263": 483.1, "10021282": 23741.6, "10021278": 5147.3, "10020501": 4564.2, "10020429": 1493.4, "10015853": 303.5}, "isPdf": false}, {"date": "11.2025", "data": {"10021273": 4199.2, "10021304": 1620.5, "10021419": 3716.1, "10021363": 1336.0, "10020495": 2377.4, "10021415": 1995.5, "10021402": 2578.63, "10021263": 483.1, "10021282": 23742.6, "10021278": 5147.3, "10020501": 4565.2, "10020429": 1493.4, "10015853": 303.5}, "isPdf": false}, {"date": "12.2025", "data": {"10021273": 5333.2, "10021304": 1624.4, "10021419": 3731.5, "10021363": 1341.9, "10020495": 2384.4, "10021415": 1997.9, "10021402": 2628.52, "10021263": 487.2, "10021282": 23746.8, "10021278": 5149.2, "10020501": 4570.7, "10020429": 1497.6, "10015853": 306.1}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021273": 17295.2, "10021304": 2027.1, "10021419": 3783.2, "10021363": 1876.2, "10020495": 2637.8, "10021415": 2072.8, "10021402": 2990.5, "10021263": 578.6, "10021282": 23896.1, "10021278": 5226.4, "10020501": 4660.4, "10020429": 1755.1, "10015853": 418.0}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "17": {"meters": [{"apt": "ADM", "meter": "10021337"}, {"apt": "1", "meter": "31521985"}, {"apt": "2", "meter": "10020420"}, {"apt": "3", "meter": "10021376"}, {"apt": "4", "meter": "10021388"}, {"apt": "5", "meter": "10021264"}, {"apt": "6", "meter": "10021422"}, {"apt": "7", "meter": "10021290"}, {"apt": "8", "meter": "31521964"}, {"apt": "9", "meter": "10020496"}, {"apt": "10", "meter": "10020481"}, {"apt": "11", "meter": "10021311"}, {"apt": "12", "meter": "10020479"}], "rows": [{"date": "08.2025", "data": {"10021337": 853.2, "31521985": 0.0, "10020420": 1823.8, "10021376": 1454.3, "10021388": 941.1, "10021264": 1449.0, "10021422": 809.8, "10021290": 16359.9, "31521964": 0.0, "10020496": 595.3, "10020481": 28.3, "10021311": 2123.2, "10020479": 1439.6}, "isPdf": false}, {"date": "09.2025", "data": {"10021337": 1213.8, "31521985": 6.1, "10020420": 1830.4, "10021376": 1460.4, "10021388": 942.1, "10021264": 1459.3, "10021422": 819.0, "10021290": 16363.2, "31521964": 3.0, "10020496": 599.5, "10020481": 28.4, "10021311": 2123.8, "10020479": 1440.5}, "isPdf": false}, {"date": "10.2025", "data": {"10021337": 1480.2, "31521985": 14.3, "10020420": 1839.7, "10021376": 1472.9, "10021388": 943.3, "10021264": 1471.7, "10021422": 831.9, "10021290": 16372.5, "31521964": 8.9, "10020496": 612.5, "10020481": 43.3, "10021311": 2133.1, "10020479": 1441.0}, "isPdf": false}, {"date": "11.2025", "data": {"10021337": 1516.2, "31521985": 14.3, "10020420": 1839.7, "10021376": 1473.9, "10021388": 943.3, "10021264": 1472.7, "10021422": 832.9, "10021290": 16373.5, "31521964": 8.9, "10020496": 613.5, "10020481": 45.3, "10021311": 2134.1, "10020479": 1441.0}, "isPdf": false}, {"date": "12.2025", "data": {"10021337": 1844.0, "31521985": 18.0, "10020420": 1843.4, "10021376": 1480.3, "10021388": 945.9, "10021264": 1480.3, "10021422": 839.4, "10021290": 16379.0, "31521964": 13.1, "10020496": 620.0, "10020481": 59.5, "10021311": 2143.5, "10020479": 1442.3}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021337": 7457.5, "31521985": 34.1, "10020420": 1859.0, "10021376": 1524.2, "10021388": 961.0, "10021264": 1498.3, "10021422": 855.4, "10021290": 16393.0, "31521964": 28.9, "10020496": 636.0, "10020481": 98.1, "10021311": 2167.3, "10020479": 1449.6}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "11.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "15": {"meters": [{"apt": "ADM", "meter": "10021275"}, {"apt": "1", "meter": "10021319"}, {"apt": "2", "meter": "10015845"}, {"apt": "3", "meter": "10020473"}, {"apt": "4", "meter": "10021259"}, {"apt": "5", "meter": "10021384"}, {"apt": "6", "meter": "10021414"}, {"apt": "7", "meter": "31521986"}, {"apt": "8", "meter": "10020502"}, {"apt": "9", "meter": "10021416"}, {"apt": "10", "meter": "10020503"}, {"apt": "11", "meter": "10021424"}, {"apt": "12", "meter": "10015837"}], "rows": [{"date": "08.2025", "data": {"10021275": 2857.6, "10021319": 1311.7, "10015845": 1400.3, "10020473": 2083.4, "10021259": 1029.9, "10021384": 3007.3, "10021414": 2105.3, "31521986": 0.0, "10020502": 5657.6, "10021416": 1752.6, "10020503": 852.2, "10021424": 2588.1, "10015837": 3895.7}, "isPdf": false}, {"date": "09.2025", "data": {"10021275": 3179.8, "10021319": 1341.6, "10015845": 1407.2, "10020473": 2088.6, "10021259": 1045.4, "10021384": 3014.6, "10021414": 2106.1, "31521986": 9.1, "10020502": 5661.1, "10021416": 1756.6, "10020503": 855.3, "10021424": 2592.3, "10015837": 3898.8}, "isPdf": false}, {"date": "10.2025", "data": {"10021275": 3319.1, "10021319": 1347.2, "10015845": 1414.5, "10020473": 2094.8, "10021259": 1065.1, "10021384": 3189.9, "10021414": 2112.4, "31521986": 15.2, "10020502": 5669.1, "10021416": 1762.5, "10020503": 862.7, "10021424": 2598.8, "10015837": 3904.5}, "isPdf": false}, {"date": "11.2025", "data": {"10021275": 3448.1, "10021319": 1347.2, "10015845": 1414.5, "10020473": 2095.8, "10021259": 1067.1, "10021384": 3200.9, "10021414": 2112.4, "31521986": 15.2, "10020502": 5670.1, "10021416": 1763.5, "10020503": 863.7, "10021424": 2599.8, "10015837": 3904.5}, "isPdf": false}, {"date": "12.2025", "data": {"10021275": 4610.0, "10021319": 1349.0, "10015845": 1417.4, "10020473": 2101.2, "10021259": 1083.3, "10021384": 3301.9, "10021414": 2114.8, "31521986": 19.3, "10020502": 5673.9, "10021416": 1767.1, "10020503": 870.6, "10021424": 2605.3, "10015837": 3908.5}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021275": 12128.6, "10021319": 1368.4, "10015845": 1439.8, "10020473": 2165.9, "10021259": 1115.3, "10021384": 3525.9, "10021414": 2145.7, "31521986": 31.6, "10020502": 5688.2, "10021416": 1781.0, "10020503": 886.6, "10021424": 2630.5, "10015837": 3995.8}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "11.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}};

let pdfFile=null, pdfData={}, pdfDates=[], selectedDates=new Set();

window.addEventListener('load', () => renderAll());

// ── DnD & file ──────────────────────────────────────────────────────────
function dov(e,el){e.preventDefault();el.classList.add('drag');}
function dol(el){el.classList.remove('drag');}
function dropPdf(e,el){e.preventDefault();el.classList.remove('drag');setPdf(e.dataTransfer.files);}
function setPdf(files){
  if(!files[0]) return;
  pdfFile=files[0];
  document.getElementById('fn-pdf').textContent=files[0].name;
  document.getElementById('z-pdf').classList.add('done');
  document.getElementById('pdf-btn').disabled=false;
}

// ── PDF ──────────────────────────────────────────────────────────────────
async function processPdf(){
  const logW=document.getElementById('log-wrap');
  const logEl=document.getElementById('log');
  logW.style.display='block'; logEl.innerHTML='';
  document.getElementById('pdf-btn').disabled=true;
  setP(5);

  const L=(m,c)=>{const d=document.createElement('div');d.className='ll '+(c||'');d.textContent=m;logEl.appendChild(d);logEl.parentElement.scrollTop=99999;};

  try{
    L('Wczytuję PDF...','nf'); setP(15);
    const text=await extractPdfText(pdfFile);
    L('PDF: '+Math.round(text.length/1000)+' tys. znaków','ok'); setP(50);

    const readings=parsePdf(text);
    if(!readings.length){L('Nie znaleziono danych PGE!','er');document.getElementById('pdf-btn').disabled=false;return;}

    pdfData={};
    const ds=new Set();
    for(const r of readings){
      if(!pdfData[r.meter]) pdfData[r.meter]={};
      pdfData[r.meter][r.dateStr]=r.current;
      ds.add(r.dateStr);
    }
    pdfDates=[...ds].sort(cmpDate);
    selectedDates=new Set(pdfDates);

    L('Znaleziono '+readings.length+' odczytów · daty: '+pdfDates.join(', '),'ok');
    setP(80);

    renderChips();
    document.getElementById('chips-wrap').style.display='flex';
    document.getElementById('sep-copy').style.display='block';
    document.getElementById('copy-btn').style.display='block';

    renderAll(); setP(100);
    setTimeout(()=>setP(0),600);
    setTimeout(()=>{logW.style.display='none';},3000);

  }catch(e){L('BŁĄD: '+e.message,'er');console.error(e);}
  document.getElementById('pdf-btn').disabled=false;
}

function parsePdf(text){
  const items=text.split('\n').map(s=>s.trim()).filter(Boolean);
  const results={};
  const starts=[];
  for(let i=0;i<items.length;i++) if(items[i].includes('FAKTURA VAT NR')) starts.push(i);
  const clean=starts.filter((s,k)=>k===0||s-starts[k-1]>5);
  console.log('Invoice blocks:',clean.length);

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
      const zVal=repeated[0];
      const xVal=repeated.find(v=>v<zVal)||null;
      readings[zDate]=zVal;
      if(xVal!=null){
        let xr=null;
        for(const t of block) if(/^\d{2}\/\d{2}\/\d{4}$/.test(t)&&t!==endDate){xr=t;break;}
        if(xr){const xd=fmt(xr);if(xd!==zDate)readings[xd]=xVal;}
      }
    } else {
      readings[zDate]=repeated[0];
    }
    if(Object.keys(readings).length) results[meter]=readings;
  }
  console.log('Parsed:',Object.keys(results).length,'meters');
  return results;
}

function fmtDate(raw){
  const[d,m,y]=raw.split('/');
  return (d==='31'&&m==='01')?`${d}/${m}/${y}`:`${m}.${y}`;
}
function cmpDate(a,b){
  const p=s=>{if(s.includes('/')){const[d,m,y]=s.split('/');return new Date(y,m-1,d);}const[m,y]=s.split('.');return new Date(y,m-1,1);};
  return p(a)-p(b);
}

// ── Chips ────────────────────────────────────────────────────────────────
function renderChips(){
  const el=document.getElementById('chips');el.innerHTML='';
  for(const d of pdfDates){
    const c=document.createElement('div');
    c.className='chip on';c.textContent=d;
    c.onclick=()=>{
      if(selectedDates.has(d)){selectedDates.delete(d);c.classList.remove('on');}
      else{selectedDates.add(d);c.classList.add('on');}
      renderAll();
    };
    el.appendChild(c);
  }
}

// ── Render all buildings ─────────────────────────────────────────────────
function renderAll(){
  const area=document.getElementById('tbl-area');
  area.innerHTML='';
  let totalNew=0;
  for(const bud of ['21','19','17','15']){
    const info=TABLE[bud];
    if(!info) continue;
    const{block,newCount}=buildBlock(bud,info);
    area.appendChild(block);
    totalNew+=newCount;
  }
  const newInfo=totalNew?` · ${totalNew} nowych odczytów z PDF`:'';
  document.getElementById('statusbar').textContent='Wskazanie bieżące — energia elektryczna'+newInfo;
}

function buildBlock(bud,info){
  const wrap=document.createElement('div');
  wrap.className='bud-block';

  const title=document.createElement('div');
  title.className='bud-title';
  title.textContent='Budynek '+bud;
  wrap.appendChild(title);

  const scroll=document.createElement('div');
  scroll.className='tbl-scroll';

  const tbl=document.createElement('table');

  // ── HEADER rows ──────────────────────────────────────────────────────
  const thead=document.createElement('thead');

  // Row 1: apartment numbers
  const rApt=document.createElement('tr');
  rApt.className='row-apt';
  const thA0=document.createElement('th');
  thA0.className='th-date';thA0.textContent='';rApt.appendChild(thA0);
  for(const m of info.meters){
    const th=document.createElement('th');
    th.textContent=m.apt==='ADM'?'ADM':'kw. '+m.apt;
    rApt.appendChild(th);
  }
  thead.appendChild(rApt);

  // Row 2: meter numbers  
  const rMet=document.createElement('tr');
  rMet.className='row-meter';
  const thM0=document.createElement('th');
  thM0.className='th-date';thM0.textContent='nr licznika';rMet.appendChild(thM0);
  for(const m of info.meters){
    const th=document.createElement('th');th.textContent=m.meter;rMet.appendChild(th);
  }
  thead.appendChild(rMet);
  tbl.appendChild(thead);

  // ── BODY rows: each date is a row ────────────────────────────────────
  const tbody=document.createElement('tbody');
  let newCount=0;

  // All dates: existing + PDF selected
  const allDates=getAllDates(info);

  for(const dateStr of allDates){
    const tr=document.createElement('tr');

    // Date cell
    const tdDate=document.createElement('td');
    tdDate.className='td-date';
    tdDate.textContent=dateStr;
    tr.appendChild(tdDate);

    const isNewDate=selectedDates.has(dateStr);

    for(const m of info.meters){
      const td=document.createElement('td');

      // Find existing value
      const existRow=info.rows.find(r=>r.date===dateStr);
      const existVal=existRow?.data?.[m.meter];

      // PDF value
      const pdfVal=pdfData[m.meter]?.[dateStr];

      let displayVal=null;
      let isNew=false;

      if(isNewDate && pdfVal!=null){
        displayVal=pdfVal; isNew=true; newCount++;
      } else if(existVal!=null){
        displayVal=existVal;
      }

      if(displayVal!=null){
        td.className=isNew?'new-val ok-val':'';
        td.textContent=String(displayVal).replace('.',',');
      } else {
        td.className='empty';td.textContent='—';
      }
      tr.appendChild(td);
    }
    tbody.appendChild(tr);
  }
  tbl.appendChild(tbody);
  scroll.appendChild(tbl);
  wrap.appendChild(scroll);
  return{block:wrap,newCount};
}

function getAllDates(info){
  const s=new Set();
  for(const r of info.rows) s.add(r.date);
  for(const d of selectedDates) s.add(d);
  return [...s].sort(cmpDate);
}

// ── Copy as TSV ──────────────────────────────────────────────────────────
async function copyTsv(){
  const lines=[];
  for(const bud of ['21','19','17','15']){
    const info=TABLE[bud];if(!info) continue;
    lines.push(['BUDYNEK '+bud]);
    // Header: apt row
    lines.push(['nr licznika',...info.meters.map(m=>m.apt==='ADM'?'ADM':'kw. '+m.apt)]);
    // Meter row
    lines.push(['nr licznika',...info.meters.map(m=>m.meter)]);
    // Data rows
    const allDates=getAllDates(info);
    for(const dateStr of allDates){
      const row=[dateStr];
      const isNewDate=selectedDates.has(dateStr);
      const existRow=info.rows.find(r=>r.date===dateStr);
      for(const m of info.meters){
        const existVal=existRow?.data?.[m.meter];
        const pdfVal=pdfData[m.meter]?.[dateStr];
        let v='';
        if(isNewDate&&pdfVal!=null) v=String(pdfVal).replace('.',',');
        else if(existVal!=null) v=String(existVal).replace('.',',');
        row.push(v);
      }
      lines.push(row);
    }
    lines.push([]);
  }
  const tsv=lines.map(r=>r.join('\t')).join('\n');
  try{
    await navigator.clipboard.writeText(tsv);
    const btn=document.getElementById('copy-btn');
    const orig=btn.textContent;
    btn.textContent='✓ Skopiowano!';btn.style.background='var(--gnb)';btn.style.color='var(--gn)';
    setTimeout(()=>{btn.textContent=orig;btn.style.background='';btn.style.color='';},2000);
  }catch(e){alert('Błąd: '+e.message);}
}

// ── PDF text ─────────────────────────────────────────────────────────────
async function extractPdfText(file){
  const buf=await file.arrayBuffer();
  const pdf=await pdfjsLib.getDocument({data:buf}).promise;
  let text='';
  for(let i=1;i<=pdf.numPages;i++){
    const p=await pdf.getPage(i);
    const c=await p.getTextContent();
    text+=c.items.map(x=>x.str).join('\n')+'\n';
  }
  return text;
}

function setP(v){document.getElementById('prg').style.width=v+'%';}
</script>
</body>
</html>
