<html lang="he" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>מדבקות מחיר - מינימרקט</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jsbarcode/3.11.6/JsBarcode.all.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.3.8/html5-qrcode.min.js"></script>
<style>
  :root{
    --ink:#1a1d21; --line:#d4d7dd; --bg:#f0f1f3; --paper:#fff;
    --accent:#0b6e4f; --accent-soft:#e8f3ef; --muted:#6b7178;
  }
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:'Segoe UI',Arial,sans-serif;background:var(--bg);color:var(--ink);line-height:1.5}

  .topbar{background:var(--paper);border-bottom:1px solid var(--line);padding:14px 22px;
    display:flex;align-items:center;justify-content:space-between;gap:16px;flex-wrap:wrap;position:sticky;top:0;z-index:20}
  .topbar h1{font-size:18px;font-weight:700}
  .topbar h1 span{color:var(--accent)}
  .topbar .meta{font-size:13px;color:var(--muted)}
  .actions{display:flex;gap:8px;flex-wrap:wrap}
  button{font-family:inherit;font-size:14px;border-radius:7px;border:1px solid var(--line);
    background:var(--paper);color:var(--ink);padding:9px 15px;cursor:pointer;font-weight:600;transition:.12s}
  button:hover{border-color:var(--accent);color:var(--accent)}
  button.primary{background:var(--accent);color:#fff;border-color:var(--accent)}
  button.primary:hover{background:#095a40;color:#fff}
  button.ghost{background:transparent}
  .layout-pick{display:flex;align-items:center;gap:6px;font-size:13px;color:var(--muted);font-weight:600}
  .layout-pick select{font-family:inherit;font-size:14px;padding:8px 10px;border:1px solid var(--line);border-radius:7px;background:#fff;color:var(--ink);font-weight:600;cursor:pointer}
  .layout-pick select:focus{outline:none;border-color:var(--accent)}
  .bc-cell{display:flex;gap:4px;align-items:center}
  .bc-cell input{flex:1}
  .scan-btn{border:1px solid var(--line);background:var(--accent-soft);border-radius:6px;
    font-size:16px;padding:6px 8px;cursor:pointer;line-height:1;flex-shrink:0}
  .scan-btn:hover{border-color:var(--accent);background:#dcefe2}
  /* ----- SCANNER MODAL ----- */
  .scan-overlay{position:fixed;inset:0;background:rgba(0,0,0,.82);z-index:100;
    display:none;flex-direction:column;align-items:center;justify-content:center;padding:18px}
  .scan-overlay.open{display:flex}
  .scan-box{background:#fff;border-radius:14px;overflow:hidden;width:100%;max-width:440px}
  .scan-box-head{display:flex;justify-content:space-between;align-items:center;padding:14px 16px;border-bottom:1px solid var(--line)}
  .scan-box-head h3{font-size:15px}
  .scan-box-head button{border:none;background:none;font-size:22px;cursor:pointer;color:var(--muted);line-height:1}
  #scanReader{width:100%}
  .scan-hint{padding:12px 16px;font-size:13px;color:var(--muted);text-align:center}
  .scan-hint.err{color:#d23b3b}

  .wrap{max-width:1080px;margin:0 auto;padding:22px}

  /* ----- FORM TABLE ----- */
  .panel{background:var(--paper);border:1px solid var(--line);border-radius:12px;overflow:hidden;margin-bottom:22px}
  .panel-head{padding:14px 18px;border-bottom:1px solid var(--line);display:flex;justify-content:space-between;align-items:center}
  .panel-head h2{font-size:15px}
  .panel-head .hint{font-size:12.5px;color:var(--muted)}
  table.form{width:100%;border-collapse:collapse;font-size:14px}
  table.form th{background:#f7f8f9;text-align:right;padding:10px 12px;font-size:12.5px;color:var(--muted);font-weight:600;border-bottom:1px solid var(--line)}
  table.form td{padding:6px 8px;border-bottom:1px solid #eceef1;vertical-align:middle}
  table.form input{width:100%;border:1px solid transparent;border-radius:6px;padding:8px 9px;font-size:14px;font-family:inherit;background:#fafbfc}
  table.form input:focus{outline:none;border-color:var(--accent);background:#fff}
  table.form input.err{border-color:#d23b3b;background:#fdecec}
  .col-idx{width:34px;text-align:center;color:var(--muted);font-size:13px}
  .col-price,.col-weight{width:110px}
  .col-per100{width:120px;font-weight:700;color:var(--accent);text-align:center;font-size:13.5px}
  .col-del{width:40px;text-align:center}
  .del-btn{border:none;background:none;color:#b9bdc4;font-size:18px;cursor:pointer;padding:2px 6px;line-height:1}
  .del-btn:hover{color:#d23b3b}
  .add-row{padding:12px 18px}
  .add-row button{width:100%;border-style:dashed}
  .unit-toggle{display:flex;gap:6px;align-items:center;font-size:12.5px;color:var(--muted)}
  .unit-toggle select{font-family:inherit;font-size:13px;padding:5px 7px;border:1px solid var(--line);border-radius:6px;background:#fff}

  .note{font-size:12.5px;color:var(--muted);padding:0 18px 14px}

  /* ----- PREVIEW (screen) ----- */
  .preview-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:12px}
  .preview-head h2{font-size:15px}
  .preview-head .count{font-size:12.5px;color:var(--muted)}

  .scaler{width:100%;max-width:480px;margin:0 auto 16px;aspect-ratio:210/297;
    border:1px solid var(--line);border-radius:4px;overflow:hidden;background:#fff;position:relative}
  .sheet{
    position:absolute;top:0;left:0;
    width:210mm;height:297mm;
    transform-origin:top left;
    display:grid;
    grid-template-columns:repeat(var(--cols,2), 1fr);
    grid-template-rows:repeat(var(--rows,8), 1fr);
  }

  .label{
    border:.4mm dashed #e2e2e2;padding:2.5mm 4mm;
    display:flex;flex-direction:column;justify-content:space-between;overflow:hidden;position:relative;
  }
  .label .lname{font-size:calc(11pt * var(--sc,1));font-weight:700;line-height:1.15;max-height:2.6em;overflow:hidden}
  .label .midrow{display:flex;justify-content:space-between;align-items:flex-end;gap:3mm}
  .label .price{font-size:calc(19pt * var(--sc,1));font-weight:800;letter-spacing:-.5px;white-space:nowrap}
  .label .price .ils{font-size:calc(12pt * var(--sc,1));font-weight:600}
  .label .per100{font-size:calc(8pt * var(--sc,1));color:#333;text-align:left;line-height:1.2}
  .label .per100 b{font-size:calc(9.5pt * var(--sc,1))}
  .label .barcode-wrap{display:flex;flex-direction:column;align-items:center;margin-top:1mm}
  .label svg{max-width:100%;max-height:calc(13mm * var(--sc,1));height:auto}
  .label.empty{border-color:#f0f0f0}

  @media (max-width:980px){
    .scaler{max-width:100%}
  }

  /* ----- PRINT ----- */
  @media print{
    @page{size:A4;margin:0}
    body{background:#fff}
    .topbar,.wrap > .panel,.preview-head,.note,.no-print{display:none !important}
    .print-zone{display:block !important}
    .scaler{width:210mm;max-width:none;height:auto;aspect-ratio:auto;border:0;border-radius:0;margin:0;overflow:visible;position:static}
    .sheet{position:static;box-shadow:none;margin:0;transform:none !important;width:210mm;height:297mm;page-break-after:always}
    .label{border:0 !important}
  }
  .print-zone{display:block}
</style>
</head>
<body>

<div class="topbar">
  <div>
    <h1>מדבקות מחיר <span>· מינימרקט</span></h1>
    <div class="meta" id="layoutMeta">A4 · בחר פריסה</div>
  </div>
  <div class="actions">
    <label class="layout-pick">
      פריסה:
      <select id="layoutSel" onchange="setLayout(this.value)"></select>
    </label>
    <button class="ghost" onclick="loadSample()">טען דוגמה</button>
    <button class="ghost" onclick="exportData()">⬇️ גיבוי</button>
    <button class="ghost" onclick="document.getElementById('importFile').click()">⬆️ שחזור</button>
    <input type="file" id="importFile" accept=".json" style="display:none" onchange="importData(this)">
    <button class="ghost" onclick="clearAll()">נקה הכל</button>
    <button class="primary" onclick="window.print()">🖨️ הדפס</button>
  </div>
</div>

<div class="wrap">
  <div class="panel no-print">
    <div class="panel-head">
      <h2>הזנת מוצרים</h2>
      <div class="hint">המחיר ל-100 גרם מחושב אוטומטית</div>
    </div>
    <table class="form">
      <thead>
        <tr>
          <th class="col-idx">#</th>
          <th>שם המוצר</th>
          <th>ברקוד (EAN-13)</th>
          <th class="col-price">מחיר ₪</th>
          <th class="col-weight">משקל / נפח</th>
          <th class="col-per100">ל-100 ג'/מ"ל</th>
          <th class="col-del"></th>
        </tr>
      </thead>
      <tbody id="rows"></tbody>
    </table>
    <div class="add-row"><button onclick="addRow()">+ הוסף שורה</button></div>
    <div class="note">
      טיפ: המשקל מוזן ביחידה שבוחרים (גרם / ק"ג / מ"ל / ליטר) והחישוב מתנרמל ל-100. ברקוד EAN-13 = 13 ספרות.
      הספרה ה-13 (ביקורת) מחושבת אוטומטית אם תזין 12 ספרות.
    </div>
  </div>

  <div class="preview-head no-print">
    <h2>תצוגה מקדימה של הדף</h2>
    <div class="count" id="counter"></div>
  </div>
</div>

<div class="print-zone" id="printZone"></div>

<div class="scan-overlay no-print" id="scanOverlay">
  <div class="scan-box">
    <div class="scan-box-head">
      <h3>סריקת ברקוד</h3>
      <button onclick="closeScanner()" title="סגור">✕</button>
    </div>
    <div id="scanReader"></div>
    <div class="scan-hint" id="scanHint">כוון את המצלמה אל הברקוד</div>
  </div>
</div>

<script>
const UNITS = {g:{label:"גרם",base:1},kg:{label:'ק"ג',base:1000},ml:{label:'מ"ל',base:1},l:{label:"ליטר",base:1000}};

// פריסות נפוצות של דפי מדבקות A4 (210×297 מ"מ). sc = יחס גודל הטקסט.
const LAYOUTS = {
  "2x8": {cols:2, rows:8, sc:1.00, name:"2 × 8 — 16 מדבקות (105 × 37 מ\"מ)"},
  "2x7": {cols:2, rows:7, sc:1.10, name:"2 × 7 — 14 מדבקות (105 × 42 מ\"מ)"},
  "2x6": {cols:2, rows:6, sc:1.20, name:"2 × 6 — 12 מדבקות (105 × 49 מ\"מ)"},
  "2x5": {cols:2, rows:5, sc:1.30, name:"2 × 5 — 10 מדבקות (105 × 59 מ\"מ)"},
  "2x4": {cols:2, rows:4, sc:1.45, name:"2 × 4 — 8 מדבקות (105 × 74 מ\"מ)"},
  "3x8": {cols:3, rows:8, sc:0.80, name:"3 × 8 — 24 מדבקות (70 × 37 מ\"מ)"},
  "3x7": {cols:3, rows:7, sc:0.85, name:"3 × 7 — 21 מדבקות (70 × 42 מ\"מ)"},
  "1x4": {cols:1, rows:4, sc:1.70, name:"1 × 4 — 4 מדבקות גדולות (210 × 74 מ\"מ)"},
};
let currentLayout = "2x8";

let products = [];
let uid = 0;

function blank(){return {id:++uid,name:"",barcode:"",price:"",weight:"",unit:"g"};}

function ean13CheckDigit(d12){
  let sum=0;
  for(let i=0;i<12;i++){sum += (+d12[i]) * (i%2===0?1:3);}
  return (10 - (sum%10))%10;
}
function normalizeBarcode(raw){
  const d=(raw||"").replace(/\D/g,"");
  if(d.length===12) return d + ean13CheckDigit(d);
  return d;
}
function per100(price,weight,unit){
  const p=parseFloat(price), w=parseFloat(weight);
  if(!p||!w||w<=0) return null;
  const grams = w * UNITS[unit].base;
  return (p/grams)*100;
}
function per100Label(unit){
  return (unit==="ml"||unit==="l") ? 'מ"ל' : "ג'";
}

function renderRows(){
  const tb=document.getElementById("rows");
  tb.innerHTML="";
  products.forEach((row,i)=>{
    const tr=document.createElement("tr");
    const pv=per100(row.price,row.weight,row.unit);
    const pvTxt = pv!==null ? `₪${pv.toFixed(2)} / 100${per100Label(row.unit)}` : "—";
    const bc=normalizeBarcode(row.barcode);
    const bcErr = row.barcode && bc.length!==13;
    tr.innerHTML=`
      <td class="col-idx">${i+1}</td>
      <td><input value="${esc(row.name)}" placeholder="לדוגמה: חלב תנובה 3%" oninput="upd(${row.id},'name',this.value)"></td>
      <td><div class="bc-cell"><input value="${esc(row.barcode)}" placeholder="12 או 13 ספרות" class="${bcErr?'err':''}" inputmode="numeric" oninput="upd(${row.id},'barcode',this.value)"><button type="button" class="scan-btn" onclick="openScanner(${row.id})" title="סרוק ברקוד">📷</button></div></td>
      <td class="col-price"><input value="${esc(row.price)}" placeholder="0.00" inputmode="decimal" oninput="upd(${row.id},'price',this.value)"></td>
      <td class="col-weight">
        <div style="display:flex;gap:4px">
          <input value="${esc(row.weight)}" placeholder="0" inputmode="decimal" oninput="upd(${row.id},'weight',this.value)" style="flex:1">
          <select onchange="upd(${row.id},'unit',this.value)" style="border:1px solid var(--line);border-radius:6px;background:#fff;font-family:inherit">
            ${Object.entries(UNITS).map(([k,v])=>`<option value="${k}" ${row.unit===k?'selected':''}>${v.label}</option>`).join("")}
          </select>
        </div>
      </td>
      <td class="col-per100">${pvTxt}</td>
      <td class="col-del"><button class="del-btn" onclick="delRow(${row.id})" title="מחק">✕</button></td>
    `;
    tb.appendChild(tr);
  });
  renderSheet();
}

function esc(s){return String(s).replace(/"/g,"&quot;").replace(/</g,"&lt;");}

function upd(id,field,val){
  const r=products.find(x=>x.id===id);
  if(!r) return;
  r[field]=val;
  // עדכון נקודתי בלי לצייר מחדש את שדות הקלט (כדי שהמקלדת בנייד לא תיסגר)
  const idx=products.findIndex(x=>x.id===id);
  const tr=document.querySelectorAll("#rows tr")[idx];
  if(tr){
    if(field==="price"||field==="weight"||field==="unit"){
      const pv=per100(r.price,r.weight,r.unit);
      const cell=tr.querySelector(".col-per100");
      if(cell) cell.textContent = pv!==null ? `₪${pv.toFixed(2)} / 100${per100Label(r.unit)}` : "—";
    }
    if(field==="barcode"){
      const bc=normalizeBarcode(r.barcode);
      const inp=tr.querySelector("td:nth-child(3) input");
      if(inp) inp.classList.toggle("err", r.barcode && bc.length!==13);
    }
  }
  renderSheet();
  save();
}
function addRow(){products.push(blank()); renderRows(); save();}
function delRow(id){products=products.filter(x=>x.id!==id); if(!products.length)addRow(); renderRows(); save();}
function clearAll(){products=[]; for(let i=0;i<8;i++)products.push(blank()); renderRows(); save();}

function loadSample(){
  products=[
    {id:++uid,name:"קוטג' תנובה 5%",barcode:"729000000016",price:"6.90",weight:"250",unit:"g"},
    {id:++uid,name:"שוקולד פרה חלב",barcode:"7290004127316",price:"5.50",weight:"100",unit:"g"},
    {id:++uid,name:"קולה זירו 1.5 ליטר",barcode:"7290000066318",price:"7.20",weight:"1.5",unit:"l"},
    {id:++uid,name:"במבה אסם",barcode:"7290000042114",price:"4.30",weight:"80",unit:"g"},
    {id:++uid,name:"שמן זית כתית 750 מ\"ל",barcode:"7290011024215",price:"32.90",weight:"750",unit:"ml"},
    {id:++uid,name:"אורז בסמטי 1 ק\"ג",barcode:"7290000123417",price:"12.50",weight:"1",unit:"kg"},
  ];
  renderRows();
  save();
}

function renderSheet(){
  const zone=document.getElementById("printZone");
  zone.innerHTML="";
  const L=LAYOUTS[currentLayout];
  const perPage=L.cols*L.rows;
  const valid = products.filter(p=>p.name||p.barcode||p.price);
  const pages=Math.max(1,Math.ceil(valid.length/perPage));

  for(let pg=0;pg<pages;pg++){
    const scaler=document.createElement("div");
    scaler.className="scaler no-print-scale";
    const sheet=document.createElement("div");
    sheet.className="sheet";
    sheet.style.setProperty("--cols",L.cols);
    sheet.style.setProperty("--rows",L.rows);
    sheet.style.setProperty("--sc",L.sc);
    for(let i=0;i<perPage;i++){
      const item=valid[pg*perPage+i];
      const cell=document.createElement("div");
      cell.className="label"+(item?"":" empty");
      if(item){
        const pv=per100(item.price,item.weight,item.unit);
        const price=parseFloat(item.price);
        const bc=normalizeBarcode(item.barcode);
        cell.innerHTML=`
          <div class="lname">${esc(item.name||" ")}</div>
          <div class="midrow">
            <div class="price">${price?price.toFixed(2):"—"}<span class="ils"> ₪</span></div>
            <div class="per100">${pv!==null?`<b>₪${pv.toFixed(2)}</b><br>ל-100 ${per100Label(item.unit)}`:""}</div>
          </div>
          <div class="barcode-wrap"><svg class="bc" data-code="${bc}"></svg></div>
        `;
      }
      sheet.appendChild(cell);
    }
    scaler.appendChild(sheet);
    zone.appendChild(scaler);
  }

  // render barcodes — בגודל שמתאים לפריסה הנבחרת
  const sc=L.sc;
  document.querySelectorAll("svg.bc").forEach(svg=>{
    const code=svg.getAttribute("data-code");
    if(code && code.length===13){
      try{JsBarcode(svg,code,{format:"EAN13",width:1.6*sc,height:34*sc,fontSize:13*sc,margin:0,displayValue:true});}
      catch(e){svg.parentElement.innerHTML=`<div style="font-size:${8*sc}pt;color:#999">${code}</div>`;}
    }else if(code){
      svg.parentElement.innerHTML=`<div style="font-size:${8*sc}pt;color:#bbb">${code||""}</div>`;
    }
  });

  document.getElementById("counter").textContent=`${valid.length} מוצרים · ${perPage} בדף · ${pages} דף${pages>1?"ים":""}`;
  const meta=document.getElementById("layoutMeta");
  if(meta) meta.textContent="A4 · "+L.name;

  fitSheets();
}

// מתאים את גודל הדף (210mm = ~794px) לרוחב הקונטיינר בפועל, כדי שכל הדף ייראה במסך
function fitSheets(){
  document.querySelectorAll(".scaler").forEach(sc=>{
    const sheet=sc.querySelector(".sheet");
    if(!sheet) return;
    const w=sc.clientWidth || 360;
    sheet.style.transform=`scale(${w/794})`;
  });
}
window.addEventListener("resize",fitSheets);
window.addEventListener("load",fitSheets);

function setLayout(key){
  if(LAYOUTS[key]){currentLayout=key; renderSheet(); save();}
}

// ---- סורק ברקוד דרך המצלמה ----
let scanner=null, scanTargetId=null;
function openScanner(rowId){
  scanTargetId=rowId;
  const overlay=document.getElementById("scanOverlay");
  const hint=document.getElementById("scanHint");
  hint.className="scan-hint"; hint.textContent="מבקש גישה למצלמה…";
  overlay.classList.add("open");

  if(typeof Html5Qrcode==="undefined"){
    hint.className="scan-hint err";
    hint.textContent="ספריית הסריקה לא נטענה (צריך חיבור לאינטרנט בפעם הראשונה).";
    return;
  }
  scanner=new Html5Qrcode("scanReader");
  const config={
    fps:10,
    qrbox:{width:250,height:140},
    formatsToSupport:[
      Html5QrcodeSupportedFormats.EAN_13,
      Html5QrcodeSupportedFormats.EAN_8,
      Html5QrcodeSupportedFormats.UPC_A,
      Html5QrcodeSupportedFormats.UPC_E
    ]
  };
  scanner.start({facingMode:"environment"}, config,
    (decodedText)=>{ onScanSuccess(decodedText); },
    ()=>{} // התעלמות מכישלונות סריקה רגעיים
  ).then(()=>{
    hint.className="scan-hint"; hint.textContent="כוון את המצלמה אל הברקוד";
  }).catch(err=>{
    hint.className="scan-hint err";
    hint.textContent="לא ניתן לפתוח את המצלמה. ודא שהדף נטען מ-HTTPS ושנתת הרשאה.";
  });
}
function onScanSuccess(code){
  const digits=(code||"").replace(/\D/g,"");
  if(!digits) return;
  const r=products.find(x=>x.id===scanTargetId);
  if(r){ r.barcode=digits; }
  closeScanner();
  renderRows();
  save();
  // משוב קצר
  const tr=document.querySelectorAll("#rows tr")[products.findIndex(x=>x.id===scanTargetId)];
  if(tr){const inp=tr.querySelector(".bc-cell input"); if(inp){inp.style.background="#d8f3e3"; setTimeout(()=>inp.style.background="",900);}}
}
function closeScanner(){
  const overlay=document.getElementById("scanOverlay");
  overlay.classList.remove("open");
  if(scanner){
    scanner.stop().then(()=>{scanner.clear();scanner=null;}).catch(()=>{scanner=null;});
  }
}

function initLayoutSelect(){
  const sel=document.getElementById("layoutSel");
  sel.innerHTML=Object.entries(LAYOUTS).map(([k,v])=>
    `<option value="${k}" ${k===currentLayout?'selected':''}>${v.name}</option>`).join("");
}

// ---- שמירה אוטומטית בזיכרון הדפדפן (עובד גם מחוץ לצ'אט, גם אופליין) ----
const STORE_KEY="minimarket_labels_v1";
function save(){
  try{
    localStorage.setItem(STORE_KEY, JSON.stringify({layout:currentLayout, products}));
  }catch(e){/* מצב פרטי/חסום - מתעלמים */}
}
function load(){
  try{
    const raw=localStorage.getItem(STORE_KEY);
    if(!raw) return false;
    const data=JSON.parse(raw);
    if(data && Array.isArray(data.products) && data.products.length){
      products=data.products;
      // משחזרים מזהים תקינים
      products.forEach(p=>{p.id=++uid;});
      if(data.layout && LAYOUTS[data.layout]) currentLayout=data.layout;
      return true;
    }
  }catch(e){}
  return false;
}

// ---- גיבוי/שחזור לקובץ (להעברה בין מכשירים) ----
function exportData(){
  const data={layout:currentLayout, products:products.filter(p=>p.name||p.barcode||p.price)};
  const blob=new Blob([JSON.stringify(data,null,2)],{type:"application/json"});
  const a=document.createElement("a");
  a.href=URL.createObjectURL(blob);
  a.download="מדבקות-מינימרקט.json";
  a.click();
  URL.revokeObjectURL(a.href);
}
function importData(input){
  const file=input.files[0];
  if(!file) return;
  const reader=new FileReader();
  reader.onload=()=>{
    try{
      const data=JSON.parse(reader.result);
      if(data && Array.isArray(data.products)){
        products=data.products.map(p=>({...p,id:++uid}));
        if(!products.length) clearAll();
        if(data.layout && LAYOUTS[data.layout]){currentLayout=data.layout; document.getElementById("layoutSel").value=currentLayout;}
        renderRows(); save();
      }
    }catch(e){alert("הקובץ אינו תקין");}
  };
  reader.readAsText(file);
  input.value="";
}

initLayoutSelect();
if(load()){
  document.getElementById("layoutSel").value=currentLayout;
  renderRows();
}else{
  clearAll();
}
</script>
</body>
</html>
