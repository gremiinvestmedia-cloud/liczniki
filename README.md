<!DOCTYPE html>
<html lang="pl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Liczniki PGE</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
<style>
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:'Segoe UI',system-ui,sans-serif;background:#f4f3ef;color:#1a1917;font-size:13px;}
input[type=file]{display:none;}
.bar{background:#fff;border-bottom:1px solid #dedad0;padding:.75rem 1.5rem;display:flex;align-items:center;gap:10px;position:sticky;top:0;z-index:10;flex-wrap:wrap;}
.logo{display:flex;align-items:center;gap:8px;font-weight:700;font-size:15px;flex-shrink:0;}
.logo-i{width:28px;height:28px;border-radius:6px;background:#1855a3;color:#fff;display:grid;place-items:center;font-size:16px;}
.sep{width:1px;height:20px;background:#dedad0;flex-shrink:0;}
.dz{display:flex;align-items:center;gap:7px;padding:5px 12px;border:1.5px dashed #b5b2a8;border-radius:5px;cursor:pointer;transition:all .15s;background:#f4f3ef;font-size:12px;font-weight:600;color:#6b6960;}
.dz:hover{border-color:#1855a3;color:#1855a3;background:#e8f0fb;}
.dz.done{border-style:solid;border-color:#16a34a;background:#dcfce7;color:#15803d;}
.btn{padding:6px 13px;border:1px solid #dedad0;border-radius:5px;background:#fff;color:#1a1917;font-size:12px;font-weight:600;cursor:pointer;font-family:inherit;transition:all .15s;white-space:nowrap;}
.btn:hover{background:#eceae3;}
.btn:disabled{opacity:.4;cursor:default;}
.btn-bl{background:#1855a3;color:#fff;border:none;}.btn-bl:hover{background:#1346a3;}
.btn-gn{background:#16a34a;color:#fff;border:none;}.btn-gn:hover{background:#15803d;}
.status{font-size:11.5px;color:#6b6960;margin-left:auto;}
.log{background:#17181b;border-radius:6px;padding:8px 12px;margin:.5rem 1.5rem 0;display:none;max-height:80px;overflow-y:auto;}
.ll{font-family:monospace;font-size:11px;line-height:1.5;}
.ok{color:#86efac;}.er{color:#fca5a5;}.nf{color:#93c5fd;}
.prg{height:2px;background:#1855a3;width:0%;position:fixed;top:0;left:0;z-index:100;transition:width .3s;}
.main{padding:1.25rem 1.5rem;}
.bud-block{margin-bottom:1.75rem;}
.bud-title{font-size:11px;font-weight:700;letter-spacing:.08em;text-transform:uppercase;color:#6b6960;margin-bottom:.5rem;padding-left:2px;}
.tbl-wrap{overflow-x:auto;border:1px solid #dedad0;border-radius:9px;}
table{border-collapse:collapse;white-space:nowrap;}
thead tr.r1 th{background:#18181b;color:#9ca3af;font-size:10px;font-weight:600;padding:5px 9px;text-align:center;border-right:1px solid #ffffff18;}
thead tr.r1 th.th0{text-align:left;position:sticky;left:0;z-index:3;background:#18181b;}
thead tr.r2 th{background:#23242a;color:#fff;font-size:10.5px;font-weight:600;padding:5px 9px;text-align:center;border-right:1px solid #ffffff12;font-family:'Courier New',monospace;}
thead tr.r2 th.th0{background:#23242a;color:#9ca3af;text-align:left;font-family:inherit;position:sticky;left:0;z-index:3;font-size:10px;min-width:90px;}
tbody td{padding:5px 9px;border-bottom:1px solid #dedad0;border-right:1px solid #dedad0;text-align:right;font-family:'Courier New',monospace;font-size:11.5px;}
tbody td.td0{text-align:left;font-family:inherit;font-size:11.5px;color:#6b6960;font-weight:600;position:sticky;left:0;background:#fff;z-index:1;padding-left:10px;}
tbody tr:hover td{background:#f0f3fb;}
tbody tr:hover td.td0{background:#e8edf8;}
tbody td.em{color:#d1d5db;text-align:center;font-family:inherit;font-size:11px;}
tbody td.pf{background:#dcfce7;color:#15803d;font-weight:700;}
tbody tr:hover td.pf{background:#bbf7d0;}
</style>
</head>
<body>
<div class="prg" id="prg"></div>
<div class="bar">
  <div class="logo"><div class="logo-i">&#9889;</div>Liczniki PGE</div>
  <div class="sep"></div>
  <div class="dz" id="dz" onclick="document.getElementById('fi').click()">
    <span>&#128196;</span><span id="dz-lb">Wgraj faktur&#281; PDF</span>
  </div>
  <input type="file" id="fi" accept=".pdf" onchange="loadPdf(this.files)">
  <button class="btn btn-bl" id="go-btn" onclick="processPdf()" disabled>Wczytaj &#8594;</button>
  <div class="sep"></div>
  <button class="btn btn-gn" onclick="copyAll(event)">&#128203; Kopiuj do Excela</button>
  <div class="status" id="status">Dane z tabeli &#8226; wgraj PDF aby uzupe&#322;ni&#263;</div>
</div>
<div class="log" id="log"></div>

<!-- Date picker modal -->
<div id="modal" style="display:none;position:fixed;inset:0;background:rgba(0,0,0,0.5);z-index:200;align-items:center;justify-content:center;">
  <div style="background:#fff;border-radius:12px;padding:1.5rem;min-width:300px;box-shadow:0 20px 60px rgba(0,0,0,0.3);">
    <div style="font-size:15px;font-weight:700;margin-bottom:.4rem;">Wybierz dat&#281; odczytu</div>
    <div style="font-size:12px;color:#6b6960;margin-bottom:1rem;">Kt&#243;re wskazania wpisa&#263; do tabeli?</div>
    <div id="modal-dates" style="display:flex;flex-direction:column;gap:7px;margin-bottom:1.25rem;"></div>
    <div style="display:flex;gap:8px;justify-content:flex-end;">
      <button onclick="document.getElementById('modal').style.display='none'" style="padding:7px 14px;border:1px solid #dedad0;border-radius:5px;background:#fff;font-size:12px;font-weight:600;cursor:pointer;font-family:inherit;">Anuluj</button>
      <button onclick="applyDate()" style="padding:7px 14px;border:none;border-radius:5px;background:#16a34a;color:#fff;font-size:12px;font-weight:600;cursor:pointer;font-family:inherit;">Wype&#322;nij &#8594;</button>
    </div>
  </div>
</div>
<div class="main" id="main"></div>

<script>
pdfjsLib.GlobalWorkerOptions.workerSrc='https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';

// Embedded table data
const BASE={"21": {"meters": [{"apt": "ADM", "meter": "31445196"}, {"apt": "1", "meter": "10020452"}, {"apt": "2", "meter": "10020428"}, {"apt": "3", "meter": "10020418"}, {"apt": "4", "meter": "10020475"}, {"apt": "5", "meter": "10021420"}, {"apt": "6", "meter": "10021257"}, {"apt": "7", "meter": "10021310"}, {"apt": "8", "meter": "10020478"}, {"apt": "9", "meter": "10021358"}, {"apt": "10", "meter": "10021303"}, {"apt": "11", "meter": "10020497"}, {"apt": "12", "meter": "10020424"}], "rows": [{"date": "08.2025", "data": {"31445196": 1231.3, "10020452": 3245.1, "10020428": 1052.8, "10020418": 1736.5, "10020475": 3914.5, "10021420": 11020.7, "10021257": 23092.7, "10021310": 3054.3, "10020478": 1346.8, "10021358": 2145.5, "10021303": 824.4, "10020497": 2964.3, "10020424": 558.2}, "isPdf": false}, {"date": "09.2025", "data": {"31445196": 2805.5, "10020452": 3255.7, "10020428": 1057.3, "10020418": 1745.3, "10020475": 3922.4, "10021420": 11038.4, "10021257": 23106.9, "10021310": 3067.8, "10020478": 1364.9, "10021358": 2160.4, "10021303": 878.5, "10020497": 3054.1, "10020424": 567.1}, "isPdf": false}, {"date": "10.2025", "data": {"31445196": 7637.1, "10020452": 3260.0, "10020428": 1142.9, "10020418": 1749.7, "10020475": 3963.3, "10021420": 11040.3, "10021257": 23107.8, "10021310": 3140.2, "10020478": 1466.2, "10021358": 2220.1, "10021303": 995.2, "10020497": 3157.8, "10020424": 797.0}, "isPdf": false}, {"date": "11.2025", "data": {"31445196": 8295.1, "10020452": 3262.0, "10020428": 1157.9, "10020418": 1751.7, "10020475": 3979.3, "10021420": 11050.3, "10021257": 23120.8, "10021310": 3151.2, "10020478": 1484.2, "10021358": 2236.1, "10021303": 1009.2, "10020497": 3169.8, "10020424": 821.0}, "isPdf": false}, {"date": "12.2025", "data": {"31445196": 14213.7, "10020452": 3282.8, "10020428": 1292.6, "10020418": 1772.9, "10020475": 4122.8, "10021420": 11137.3, "10021257": 23232.6, "10021310": 3250.8, "10020478": 1649.1, "10021358": 2380.4, "10021303": 1133.7, "10020497": 3278.2, "10020424": 1039.8}, "isPdf": false}, {"date": "31/01/2026", "data": {"31445196": 28990.2, "10020452": 3703.7, "10020428": 1592.6, "10020418": 2208.0, "10020475": 4500.6, "10021420": 11369.5, "10021257": 23605.8, "10021310": 3541.8, "10020478": 2015.1, "10021358": 2748.4, "10021303": 1495.9, "10020497": 3518.2, "10020424": 1525.8}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "19": {"meters": [{"apt": "ADM", "meter": "10021273"}, {"apt": "1", "meter": "10021304"}, {"apt": "2", "meter": "10021419"}, {"apt": "3", "meter": "10021363"}, {"apt": "4", "meter": "10020495"}, {"apt": "5", "meter": "10021415"}, {"apt": "6", "meter": "10021402"}, {"apt": "7", "meter": "10021263"}, {"apt": "8", "meter": "10021282"}, {"apt": "9", "meter": "10021278"}, {"apt": "10", "meter": "10020501"}, {"apt": "11", "meter": "10020429"}, {"apt": "12", "meter": "10015853"}], "rows": [{"date": "08.2025", "data": {"10021273": 3208.9, "10021304": 1613.3, "10021419": 3689.8, "10021363": 1314.2, "10020495": 2368.1, "10021415": 1987.8, "10021402": 2553.5, "10021263": 471.5, "10021282": 23730.8, "10021278": 5135.4, "10020501": 4532.8, "10020429": 1472.3, "10015853": 276.8}, "isPdf": false}, {"date": "09.2025", "data": {"10021273": 3633.8, "10021304": 1613.6, "10021419": 3706.9, "10021363": 1320.2, "10020495": 2368.4, "10021415": 1989.2, "10021402": 2566.96, "10021263": 473.0, "10021282": 23733.4, "10021278": 5138.1, "10020501": 4553.6, "10020429": 1483.0, "10015853": 291.6}, "isPdf": false}, {"date": "10.2025", "data": {"10021273": 4073.2, "10021304": 1620.5, "10021419": 3714.1, "10021363": 1335.0, "10020495": 2376.4, "10021415": 1995.5, "10021402": 2572.63, "10021263": 483.1, "10021282": 23741.6, "10021278": 5147.3, "10020501": 4564.2, "10020429": 1493.4, "10015853": 303.5}, "isPdf": false}, {"date": "11.2025", "data": {"10021273": 4199.2, "10021304": 1620.5, "10021419": 3716.1, "10021363": 1336.0, "10020495": 2377.4, "10021415": 1995.5, "10021402": 2578.63, "10021263": 483.1, "10021282": 23742.6, "10021278": 5147.3, "10020501": 4565.2, "10020429": 1493.4, "10015853": 303.5}, "isPdf": false}, {"date": "12.2025", "data": {"10021273": 5333.2, "10021304": 1624.4, "10021419": 3731.5, "10021363": 1341.9, "10020495": 2384.4, "10021415": 1997.9, "10021402": 2628.52, "10021263": 487.2, "10021282": 23746.8, "10021278": 5149.2, "10020501": 4570.7, "10020429": 1497.6, "10015853": 306.1}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021273": 17295.2, "10021304": 2027.1, "10021419": 3783.2, "10021363": 1876.2, "10020495": 2637.8, "10021415": 2072.8, "10021402": 2990.5, "10021263": 578.6, "10021282": 23896.1, "10021278": 5226.4, "10020501": 4660.4, "10020429": 1755.1, "10015853": 418.0}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "17": {"meters": [{"apt": "ADM", "meter": "10021337"}, {"apt": "1", "meter": "31521985"}, {"apt": "2", "meter": "10020420"}, {"apt": "3", "meter": "10021376"}, {"apt": "4", "meter": "10021388"}, {"apt": "5", "meter": "10021264"}, {"apt": "6", "meter": "10021422"}, {"apt": "7", "meter": "10021290"}, {"apt": "8", "meter": "31521964"}, {"apt": "9", "meter": "10020496"}, {"apt": "10", "meter": "10020481"}, {"apt": "11", "meter": "10021311"}, {"apt": "12", "meter": "10020479"}], "rows": [{"date": "08.2025", "data": {"10021337": 853.2, "31521985": 0.0, "10020420": 1823.8, "10021376": 1454.3, "10021388": 941.1, "10021264": 1449.0, "10021422": 809.8, "10021290": 16359.9, "31521964": 0.0, "10020496": 595.3, "10020481": 28.3, "10021311": 2123.2, "10020479": 1439.6}, "isPdf": false}, {"date": "09.2025", "data": {"10021337": 1213.8, "31521985": 6.1, "10020420": 1830.4, "10021376": 1460.4, "10021388": 942.1, "10021264": 1459.3, "10021422": 819.0, "10021290": 16363.2, "31521964": 3.0, "10020496": 599.5, "10020481": 28.4, "10021311": 2123.8, "10020479": 1440.5}, "isPdf": false}, {"date": "10.2025", "data": {"10021337": 1480.2, "31521985": 14.3, "10020420": 1839.7, "10021376": 1472.9, "10021388": 943.3, "10021264": 1471.7, "10021422": 831.9, "10021290": 16372.5, "31521964": 8.9, "10020496": 612.5, "10020481": 43.3, "10021311": 2133.1, "10020479": 1441.0}, "isPdf": false}, {"date": "11.2025", "data": {"10021337": 1516.2, "31521985": 14.3, "10020420": 1839.7, "10021376": 1473.9, "10021388": 943.3, "10021264": 1472.7, "10021422": 832.9, "10021290": 16373.5, "31521964": 8.9, "10020496": 613.5, "10020481": 45.3, "10021311": 2134.1, "10020479": 1441.0}, "isPdf": false}, {"date": "12.2025", "data": {"10021337": 1844.0, "31521985": 18.0, "10020420": 1843.4, "10021376": 1480.3, "10021388": 945.9, "10021264": 1480.3, "10021422": 839.4, "10021290": 16379.0, "31521964": 13.1, "10020496": 620.0, "10020481": 59.5, "10021311": 2143.5, "10020479": 1442.3}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021337": 7457.5, "31521985": 34.1, "10020420": 1859.0, "10021376": 1524.2, "10021388": 961.0, "10021264": 1498.3, "10021422": 855.4, "10021290": 16393.0, "31521964": 28.9, "10020496": 636.0, "10020481": 98.1, "10021311": 2167.3, "10020479": 1449.6}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "11.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}, "15": {"meters": [{"apt": "ADM", "meter": "10021275"}, {"apt": "1", "meter": "10021319"}, {"apt": "2", "meter": "10015845"}, {"apt": "3", "meter": "10020473"}, {"apt": "4", "meter": "10021259"}, {"apt": "5", "meter": "10021384"}, {"apt": "6", "meter": "10021414"}, {"apt": "7", "meter": "31521986"}, {"apt": "8", "meter": "10020502"}, {"apt": "9", "meter": "10021416"}, {"apt": "10", "meter": "10020503"}, {"apt": "11", "meter": "10021424"}, {"apt": "12", "meter": "10015837"}], "rows": [{"date": "08.2025", "data": {"10021275": 2857.6, "10021319": 1311.7, "10015845": 1400.3, "10020473": 2083.4, "10021259": 1029.9, "10021384": 3007.3, "10021414": 2105.3, "31521986": 0.0, "10020502": 5657.6, "10021416": 1752.6, "10020503": 852.2, "10021424": 2588.1, "10015837": 3895.7}, "isPdf": false}, {"date": "09.2025", "data": {"10021275": 3179.8, "10021319": 1341.6, "10015845": 1407.2, "10020473": 2088.6, "10021259": 1045.4, "10021384": 3014.6, "10021414": 2106.1, "31521986": 9.1, "10020502": 5661.1, "10021416": 1756.6, "10020503": 855.3, "10021424": 2592.3, "10015837": 3898.8}, "isPdf": false}, {"date": "10.2025", "data": {"10021275": 3319.1, "10021319": 1347.2, "10015845": 1414.5, "10020473": 2094.8, "10021259": 1065.1, "10021384": 3189.9, "10021414": 2112.4, "31521986": 15.2, "10020502": 5669.1, "10021416": 1762.5, "10020503": 862.7, "10021424": 2598.8, "10015837": 3904.5}, "isPdf": false}, {"date": "11.2025", "data": {"10021275": 3448.1, "10021319": 1347.2, "10015845": 1414.5, "10020473": 2095.8, "10021259": 1067.1, "10021384": 3200.9, "10021414": 2112.4, "31521986": 15.2, "10020502": 5670.1, "10021416": 1763.5, "10020503": 863.7, "10021424": 2599.8, "10015837": 3904.5}, "isPdf": false}, {"date": "12.2025", "data": {"10021275": 4610.0, "10021319": 1349.0, "10015845": 1417.4, "10020473": 2101.2, "10021259": 1083.3, "10021384": 3301.9, "10021414": 2114.8, "31521986": 19.3, "10020502": 5673.9, "10021416": 1767.1, "10020503": 870.6, "10021424": 2605.3, "10015837": 3908.5}, "isPdf": false}, {"date": "31/01/2026", "data": {"10021275": 12128.6, "10021319": 1368.4, "10015845": 1439.8, "10020473": 2165.9, "10021259": 1115.3, "10021384": 3525.9, "10021414": 2145.7, "31521986": 31.6, "10020502": 5688.2, "10021416": 1781.0, "10020503": 886.6, "10021424": 2630.5, "10015837": 3995.8}, "isPdf": false}, {"date": "02.2026", "data": {}, "isPdf": false}, {"date": "03.2026", "data": {}, "isPdf": false}, {"date": "04.2026", "data": {}, "isPdf": false}, {"date": "05.2026", "data": {}, "isPdf": false}, {"date": "06.2026", "data": {}, "isPdf": false}, {"date": "07.2026", "data": {}, "isPdf": false}, {"date": "08.2026", "data": {}, "isPdf": false}, {"date": "09.2026", "data": {}, "isPdf": false}, {"date": "10.2026", "data": {}, "isPdf": false}, {"date": "11.2026", "data": {}, "isPdf": false}, {"date": "12.2026", "data": {}, "isPdf": false}]}};

let pdfFile=null, pdfData={};

// Clone base data for display
let TABLE=JSON.parse(JSON.stringify(BASE));

function setP(v){document.getElementById('prg').style.width=v+'%';}
function L(m,c){const log=document.getElementById('log');log.style.display='block';const d=document.createElement('div');d.className='ll '+(c||'');d.textContent=m;log.appendChild(d);log.scrollTop=9999;}

function loadPdf(files){
  if(!files[0])return;
  pdfFile=files[0];
  document.getElementById('dz-lb').textContent=files[0].name;
  document.getElementById('dz').classList.add('done');
  document.getElementById('go-btn').disabled=false;
}

async function processPdf(){
  if(!pdfFile)return;
  document.getElementById('go-btn').disabled=true;
  document.getElementById('log').innerHTML='';
  setP(5);
  L('Wczytuę PDF...','nf');
  try{
    const buf=await pdfFile.arrayBuffer();
    const pdf=await pdfjsLib.getDocument({data:buf}).promise;
    const items=[];
    for(let p=1;p<=pdf.numPages;p++){
      const page=await pdf.getPage(p);
      const content=await page.getTextContent();
      for(const item of content.items){const t=item.str.trim();if(t)items.push(t);}
    }
    L('PDF: '+items.length+' elementów','ok');
    setP(40);
    pdfData=parsePdf(items);
    const count=Object.keys(pdfData).length;
    if(!count){L('Nie znaleziono danych PGE!','er');document.getElementById('go-btn').disabled=false;return;}
    const allDates=new Set();
    for(const m of Object.values(pdfData)) for(const d of Object.keys(m)) allDates.add(d);
    const sortedDates=[...allDates].sort(cmpDate);
    L('Znaleziono '+count+' liczników • daty: '+sortedDates.join(', '),'ok');
    setP(90);
    showDatePicker(sortedDates);
  }catch(e){L('BŁĄD: '+e.message,'er');console.error(e);}
  document.getElementById('go-btn').disabled=false;
  setTimeout(()=>setP(0),500);
}

function cmpDate(a,b){
  const p=s=>{if(s.includes('/')){const[d,m,y]=s.split('/');return new Date(y,m-1,d);}const[m,y]=s.split('.');return new Date(y,m-1,1);};
  return p(a)-p(b);
}

function showDatePicker(dates){
  const modal=document.getElementById('modal');
  const container=document.getElementById('modal-dates');
  container.innerHTML='';
  dates.forEach((d,i)=>{
    const label=document.createElement('label');
    label.style.cssText='display:flex;align-items:center;gap:10px;padding:10px 14px;border:1.5px solid #dedad0;border-radius:7px;cursor:pointer;transition:border-color .15s;';
    const radio=document.createElement('input');
    radio.type='radio';radio.name='pick-date';radio.value=d;
    radio.style.accentColor='#16a34a';radio.style.width='16px';radio.style.height='16px';
    if(i===dates.length-1){radio.checked=true;label.style.borderColor='#16a34a';}
    radio.addEventListener('change',()=>{
      document.querySelectorAll('#modal-dates label').forEach(l=>l.style.borderColor='#dedad0');
      label.style.borderColor='#16a34a';
    });
    const txt=document.createElement('span');
    txt.style.cssText='font-size:14px;font-weight:700;';txt.textContent=d;
    label.appendChild(radio);label.appendChild(txt);
    container.appendChild(label);
  });
  modal.style.display='flex';
}

function applyDate(){
  const sel=document.querySelector('input[name="pick-date"]:checked');
  if(!sel)return;
  const date=sel.value;
  document.getElementById('modal').style.display='none';
  // Merge data for selected date
  TABLE=JSON.parse(JSON.stringify(BASE));
  for(const bud of Object.keys(TABLE)){
    for(const row of TABLE[bud].rows){
      if(row.date===date){
        row.isPdf=true;
        for(const m of TABLE[bud].meters){
          if(pdfData[m.meter]&&pdfData[m.meter][date]!=null)
            row.data[m.meter]=pdfData[m.meter][date];
        }
      }
    }
  }
  document.getElementById('status').textContent='Dane z PDF • '+date+' • komórki na zielono';
  document.getElementById('log').style.display='none';
  setP(0);
  render();
}

function parsePdf(items){
  const results={};

  const starts=[];
  for(let i=0;i<items.length;i++){
    if(items[i].includes('FAKTURA VAT NR')) starts.push(i);
  }
  const cleanStarts=starts.filter((s,k)=>k===0||s-starts[k-1]>5);
  console.log('Invoice blocks:',cleanStarts.length);

  for(let k=0;k<cleanStarts.length;k++){
    const s=cleanStarts[k];
    const e=cleanStarts[k+1]||items.length;
    const block=items.slice(s,e);

    // Find meter
    let meter=null;
    for(const t of block){
      const m=t.match(/Licznik.{0,80}nr[\s\u00a0:]+(\d{7,12})/);
      if(m){meter=m[1];break;}
    }
    if(!meter) continue;

    // Find reading dates (standalone dd/mm/yyyy items)
    const allDates=[...new Set(block.filter(t=>/^\d{2}\/\d{2}\/\d{4}$/.test(t)))];
    if(!allDates.length) continue;

    // Collect big numbers (>100) — both decimals and integers
    const bigVals=[];
    for(const t of block){
      if(/^\d+[,.]\d+$/.test(t)){const v=parseFloat(t.replace(',','.'));if(v>100)bigVals.push(v);}
      else if(/^\d+$/.test(t)){const v=parseInt(t);if(v>100)bigVals.push(v);}
    }
    const uniqueVals=[...new Map(bigVals.map(v=>[v,v])).values()];
    if(!uniqueVals.length) continue;

    const hasX=block.some(t=>t==='(X)');
    const readings={};

    const fmtDate=raw=>{const[d,mo,y]=raw.split('/');return(d==='31'&&mo==='01')?raw:`${mo}.${y}`;};

    if(hasX&&allDates.length>=2){
      // 2-period: X date gets first unique val, Z date gets first val > X val
      const xVal=uniqueVals[0];
      const zVal=uniqueVals.find(v=>v>xVal)||null;
      readings[fmtDate(allDates[0])]=xVal;
      if(zVal) readings[fmtDate(allDates[1])]=zVal;
    } else {
      // Single period: first unique val = bieżące
      readings[fmtDate(allDates[0])]=uniqueVals[0];
    }

    if(Object.keys(readings).length) results[meter]=readings;
  }

  console.log('Parsed:',Object.keys(results).length,'meters');
  return results;
}



function render(){
  const main=document.getElementById('main');
  main.innerHTML='';
  for(const bud of ['21','19','17','15']){
    const info=TABLE[bud];if(!info)continue;
    const block=document.createElement('div');block.className='bud-block';
    const title=document.createElement('div');title.className='bud-title';
    title.textContent='Budynek '+bud;block.appendChild(title);
    const wrap=document.createElement('div');wrap.className='tbl-wrap';
    const tbl=document.createElement('table');
    const thead=document.createElement('thead');
    const r1=document.createElement('tr');r1.className='r1';
    const r2=document.createElement('tr');r2.className='r2';
    const h0a=document.createElement('th');h0a.className='th0';r1.appendChild(h0a);
    const h0b=document.createElement('th');h0b.className='th0';h0b.textContent='nr licznika';r2.appendChild(h0b);
    for(const m of info.meters){
      const a=document.createElement('th');a.textContent=(m.apt==='ADM'?'ADM':'kw.\u00a0'+m.apt);r1.appendChild(a);
      const b=document.createElement('th');b.textContent=m.meter;r2.appendChild(b);
    }
    thead.appendChild(r1);thead.appendChild(r2);tbl.appendChild(thead);
    const tbody=document.createElement('tbody');
    for(const row of info.rows){
      const tr=document.createElement('tr');
      const td0=document.createElement('td');td0.className='td0';td0.textContent=row.date;tr.appendChild(td0);
      for(const m of info.meters){
        const td=document.createElement('td');
        const v=row.data[m.meter];
        if(v!=null){
          td.textContent=String(v).replace('.',',');
          if(row.isPdf) td.className='pf';
        }else{td.className='em';td.textContent='\u2014';}
        tr.appendChild(td);
      }
      tbody.appendChild(tr);
    }
    tbl.appendChild(tbody);wrap.appendChild(tbl);block.appendChild(wrap);main.appendChild(block);
  }
}

async function copyAll(e){
  const btn=e.target;
  const lines=[];
  for(const bud of ['21','19','17','15']){
    const info=TABLE[bud];if(!info)continue;
    lines.push('BUDYNEK '+bud);
    lines.push('\t'+info.meters.map(m=>m.apt==='ADM'?'ADM':'kw. '+m.apt).join('\t'));
    lines.push('nr licznika\t'+info.meters.map(m=>m.meter).join('\t'));
    for(const row of info.rows){
      const cells=[row.date];
      for(const m of info.meters){
        const v=row.data[m.meter];cells.push(v!=null?String(v).replace('.',','):'');
      }
      lines.push(cells.join('\t'));
    }
    lines.push('');
  }
  try{
    await navigator.clipboard.writeText(lines.join('\n'));
    const orig=btn.textContent;
    btn.textContent='\u2713 Skopiowano!';btn.style.background='#14532d';
    setTimeout(()=>{btn.textContent=orig;btn.style.background='';},2000);
  }catch(err){alert('Blad: '+err.message);}
}

render();
</script>
</body>
</html>
