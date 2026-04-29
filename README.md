<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Fit Diet App AI</title>

<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>

<style>
:root{
  --bg:#020617;--card:#0f172a;--soft:#1e293b;--line:#334155;
  --green:#22c55e;--blue:#38bdf8;--yellow:#facc15;--red:#ef4444;
  --text:#f8fafc;--muted:#94a3b8;
}
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:radial-gradient(circle at top,#1e293b,#020617 55%);color:var(--text)}
.app-header{max-width:1150px;margin:auto;padding:22px 16px;display:flex;justify-content:space-between;align-items:center;gap:14px}
h1{margin:0;font-size:30px}
p{color:var(--muted)}
.container{max-width:1150px;margin:auto;padding:16px}
.profile-button{width:auto;min-width:170px;padding:12px 16px;border-radius:999px;background:#111827;color:white;border:1px solid var(--line);cursor:pointer}
.tabs{display:grid;grid-template-columns:repeat(3,1fr);gap:8px;margin-bottom:16px}
.tab{background:#111827;border:1px solid var(--line);padding:13px;border-radius:14px;text-align:center;cursor:pointer}
.tab.active{background:var(--green);color:#052e16;font-weight:bold}
.card{background:rgba(15,23,42,.96);border:1px solid var(--line);border-radius:22px;padding:18px;margin-bottom:16px}
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(250px,1fr));gap:14px}
input,select,textarea,button{width:100%;padding:13px;border-radius:14px;border:1px solid var(--line);background:#020617;color:white;margin-top:8px;font-size:15px}
textarea{min-height:180px}
button{background:var(--green);color:#052e16;border:none;font-weight:bold;cursor:pointer}
button.secondary{background:#334155;color:white}
button.blue{background:var(--blue);color:#082f49}
button.red{background:var(--red);color:white}
.week-tabs{display:flex;gap:8px;overflow-x:auto;padding-bottom:14px}
.week-tabs button{min-width:105px;background:#111827;color:white;border:1px solid var(--line)}
.week-tabs button.active{background:var(--green);color:#052e16}
.meal{background:var(--soft);padding:15px;border-radius:16px;margin-top:12px}
.meal strong{color:var(--yellow)}
.badge{display:inline-block;padding:7px 11px;border-radius:999px;background:#064e3b;color:#bbf7d0;font-size:13px;margin-bottom:8px}
.small{font-size:13px;color:var(--muted)}
.kcal{color:var(--yellow);font-weight:bold}
.notice{border-left:4px solid var(--blue);padding:12px;background:#082f4933;border-radius:12px;margin-top:10px}
.shopping-item{background:#1f2937;padding:12px;border-radius:14px;margin-bottom:10px}
.shopping-actions{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.have{text-decoration:line-through;opacity:.5}
.day-choice{display:grid;grid-template-columns:repeat(auto-fit,minmax(120px,1fr));gap:8px;margin-top:10px}
.day-choice label{background:#111827;border:1px solid var(--line);padding:10px;border-radius:12px}
@media(max-width:700px){.app-header{flex-direction:column;align-items:flex-start}.profile-button{width:100%}}
</style>
</head>

<body>

<header class="app-header">
  <div>
    <h1>💪 Fit Diet App AI</h1>
    <p>Menu fit, calorie automatiche e import dieta PDF</p>
  </div>
  <button class="profile-button" onclick="apriProfilo()">👤 Il mio profilo</button>
</header>

<div class="container">

  <div class="tabs">
    <div class="tab active" onclick="apriTab('oggi',this)">Oggi</div>
    <div class="tab" onclick="apriTab('settimana',this)">Settimana</div>
    <div class="tab" onclick="apriTab('spesa',this)">Spesa</div>
  </div>

  <div id="oggi">
    <div class="week-tabs" id="weekTabs"></div>
    <div class="card">
      <h2 id="titoloGiorno">Menu del giorno</h2>
      <div id="riepilogoCalorie"></div>
      <button onclick="generaGiorno()">Rigenera menu</button>
      <div id="menuGiorno"></div>
    </div>
  </div>

  <div id="settimana" style="display:none">
    <div class="card">
      <h2>Settimana completa</h2>
      <button onclick="generaSettimana()">Rigenera settimana</button>
      <div id="settimanaBox" class="grid"></div>
    </div>
  </div>

  <div id="spesa" style="display:none">
    <div class="card">
      <h2>Lista spesa</h2>
      <button onclick="generaListaSpesa()">Genera lista spesa</button>
      <div id="listaSpesa"></div>
    </div>
  </div>

  <div id="profilo" style="display:none">

    <div class="card">
      <h2>👤 Il mio profilo</h2>
      <div class="grid">
        <input id="clienteNome" type="text" placeholder="Nome">
        <input id="clienteCognome" type="text" placeholder="Cognome">
        <input id="clienteEta" type="number" placeholder="Età">
        <input id="clienteAltezza" type="number" placeholder="Altezza cm">
        <input id="clientePeso" type="number" placeholder="Peso kg">

        <select id="clienteSesso">
          <option value="M">Uomo</option>
          <option value="F">Donna</option>
        </select>

        <select id="clienteObiettivo">
          <option value="dimagrimento">Dimagrimento</option>
          <option value="mantenimento">Mantenimento</option>
          <option value="massa">Massa muscolare</option>
        </select>

        <select id="clienteAttivita">
          <option value="1.2">Sedentario</option>
          <option value="1.375">Leggermente attivo</option>
          <option value="1.55" selected>Allenamento 3-5 volte/settimana</option>
          <option value="1.725">Molto attivo</option>
        </select>
      </div>

      <h3>Giorni di allenamento</h3>
      <p class="small">Spunta i giorni ON. Quelli non spuntati saranno OFF.</p>
      <div id="giorniAllenamentoBox" class="day-choice"></div>

      <button onclick="calcolaCalorie()">Calcola calorie</button>
      <button class="secondary" onclick="salvaProfilo()">Salva profilo</button>
      <div id="calorieBox" class="notice">Inserisci i dati e calcola.</div>
    </div>

    <div class="card">
      <h2>Import dieta PDF / TXT</h2>
      <p class="small">Carica un PDF dieta o incolla il testo. L’app prova a riconoscere pasti e grammature.</p>
      <input type="file" id="fileInput" accept=".pdf,.txt,.json" onchange="importaFile(event)">
      <textarea id="testoDieta" placeholder="Oppure incolla qui il testo della dieta..."></textarea>
      <button class="blue" onclick="analizzaDieta()">Analizza dieta</button>
      <button class="secondary" onclick="caricaDietaAugusto()">Carica dieta Augusto demo</button>
      <button class="red" onclick="resetApp()">Reset</button>
      <div id="importStatus" class="notice">Nessuna dieta importata.</div>
    </div>

  </div>

</div>

<script>
pdfjsLib.GlobalWorkerOptions.workerSrc="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js";

let giorni=[
  {id:"lunedi",nome:"Lunedì",tipo:"ON"},
  {id:"martedi",nome:"Martedì",tipo:"ON"},
  {id:"mercoledi",nome:"Mercoledì",tipo:"OFF"},
  {id:"giovedi",nome:"Giovedì",tipo:"ON"},
  {id:"venerdi",nome:"Venerdì",tipo:"ON"},
  {id:"sabato",nome:"Sabato",tipo:"OFF"},
  {id:"domenica",nome:"Domenica",tipo:"OFF"}
];

let giornoAttivo="lunedi";
let targetKcal=2500;
let menuCorrente={};

const alimenti={
  carneBianca:["pollo","tacchino","coniglio"],
  carneRossa:["vitella","manzo magro","bovino magro"],
  pesceMagro:["merluzzo","nasello","palombo","persico"],
  pesceFresco:["merluzzo","pesce spada","palombo","persico","orata","branzino"],
  pesceGrasso:["salmone","sgombro","sardine","alici"],
  legumi:["ceci","fagioli","lenticchie","piselli","fave"],
  verdure:["lattuga","valeriana","rughetta","broccoli","zucchine","pomodori","cetrioli","fagiolini"]
};

let dieta={
  colazione:[
    "2 uova + pane di segale 60g + avocado 50g",
    "Albume 250g + pane di segale 60g + avocado 50g",
    "Yogurt greco 250g + avena 40g + mirtilli 100g",
    "Yogurt greco 250g + avena 40g + kiwi 100g"
  ],
  spuntinoMattina:[
    "Yogurt vaccino 200g + kiwi 100g + semi di chia 5g",
    "Prosciutto cotto 60g + Wasa 30g",
    "Prosciutto crudo 60g + gallette di riso 30g"
  ],
  pranzoON:[
    "{carneBianca} 250g + riso bianco 100g + {verdure} 200g + olio EVO 10g",
    "{pesceFresco} 250g + pasta 100g + insalata 200g + olio EVO 10g",
    "{carneRossa} 200g + cous cous 100g + verdure 200g + olio EVO 10g",
    "Tonno naturale 150g + patate 400g + pomodori 200g + olio EVO 10g"
  ],
  pranzoOFF:[
    "{carneBianca} 250g + riso bianco 50g + {verdure} 200g + olio EVO 10g",
    "{pesceFresco} 250g + pasta 50g + insalata 200g + olio EVO 10g",
    "{carneRossa} 200g + cous cous 50g + verdure 200g + olio EVO 10g",
    "Tonno naturale 150g + patate 200g + pomodori 200g + olio EVO 10g"
  ],
  spuntinoPomeriggio:[
    "Yogurt vaccino 200g + mela 150g + mandorle 10g",
    "Yogurt vaccino 200g + banana 120g + noci 10g",
    "Uovo 60g + Wasa 30g",
    "Ricotta 50g + Wasa 30g"
  ],
  cena:[
    "{carneBianca} 250g + patate 250g + {verdure} 200g + olio EVO 10g",
    "{carneRossa} 200g + patate 250g + verdure 200g",
    "{pesceMagro} 250g + riso integrale 70g + misticanza 200g + olio EVO 10g",
    "Salmone 200g + riso integrale 70g + verdure 200g",
    "{legumi} 150g + pane integrale 50g + verdure 200g + olio EVO 10g"
  ]
};

const kcal100={
  uova:143,uovo:143,albume:52,"pane di segale":230,"pane integrale":245,pane:250,
  avocado:160,"yogurt greco":70,"yogurt vaccino":60,avena:389,mirtilli:57,kiwi:61,mela:52,banana:89,
  pollo:165,tacchino:135,coniglio:150,vitella:170,"manzo magro":180,"bovino magro":180,
  "riso bianco":360,"riso integrale":350,riso:360,pasta:360,"cous cous":360,patate:77,
  merluzzo:82,nasello:82,palombo:90,persico:90,orata:120,branzino:120,"pesce spada":140,
  tonno:110,"tonno naturale":110,salmone:208,sgombro:205,sardine:200,alici:130,
  ceci:120,fagioli:110,lenticchie:116,piselli:80,fave:88,
  ricotta:146,mandorle:579,noci:654,"semi di chia":486,wasa:350,gallette:380,
  "olio evo":884,olio:884,verdure:30,insalata:20,pomodori:18,cetrioli:15,zucchine:17,broccoli:35,lattuga:15
};

function apriTab(id,el){
  document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
  if(el) el.classList.add("active");
  ["oggi","settimana","spesa","profilo"].forEach(x=>document.getElementById(x).style.display=x===id?"block":"none");
}

function apriProfilo(){
  document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
  ["oggi","settimana","spesa","profilo"].forEach(x=>document.getElementById(x).style.display=x==="profilo"?"block":"none");
}

function scegli(arr){return arr[Math.floor(Math.random()*arr.length)]}

function espandi(t){
  return t.replaceAll("{carneBianca}",scegli(alimenti.carneBianca))
    .replaceAll("{carneRossa}",scegli(alimenti.carneRossa))
    .replaceAll("{pesceMagro}",scegli(alimenti.pesceMagro))
    .replaceAll("{pesceFresco}",scegli(alimenti.pesceFresco))
    .replaceAll("{pesceGrasso}",scegli(alimenti.pesceGrasso))
    .replaceAll("{legumi}",scegli(alimenti.legumi))
    .replaceAll("{verdure}",scegli(alimenti.verdure));
}

function tipoGiorno(id){return giorni.find(g=>g.id===id).tipo}

function creaTabsGiorni(){
  let html="";
  giorni.forEach(g=>{
    html+=`<button class="${g.id===giornoAttivo?'active':''}" onclick="selezionaGiorno('${g.id}')">${g.nome}<br><span class="small">${g.tipo}</span></button>`;
  });
  document.getElementById("weekTabs").innerHTML=html;
}

function selezionaGiorno(id){giornoAttivo=id;creaTabsGiorni();generaGiorno()}

function generaMenu(id){
  const off=tipoGiorno(id)==="OFF";
  return {
    colazione:espandi(scegli(dieta.colazione)),
    spuntinoMattina:espandi(scegli(dieta.spuntinoMattina)),
    pranzo:espandi(scegli(off?dieta.pranzoOFF:dieta.pranzoON)),
    spuntinoPomeriggio:espandi(scegli(dieta.spuntinoPomeriggio)),
    cena:espandi(scegli(dieta.cena))
  };
}

function stimaKcal(testo){
  let totale=0;
  const lower=testo.toLowerCase();
  Object.keys(kcal100).forEach(nome=>{
    if(lower.includes(nome)){
      const pos=lower.indexOf(nome);
      const sub=lower.slice(pos, pos+40);
      const match=sub.match(/(\d+)\s?g/);
      const grammi=match?parseInt(match[1]):100;
      totale+=(kcal100[nome]*grammi)/100;
    }
  });
  return Math.round(totale||250);
}

function kcalMenu(menu){return Object.values(menu).reduce((s,p)=>s+stimaKcal(p),0)}

function blocco(titolo,testo,id){
  return `<div class="meal"><strong>${titolo}</strong><p>${testo}</p><p class="small">Stima: <span class="kcal">${stimaKcal(testo)} kcal</span></p><button class="secondary" onclick="cambiaPasto('${id}')">Non mi piace, cambia</button></div>`;
}

function generaGiorno(){
  menuCorrente=generaMenu(giornoAttivo);
  const g=giorni.find(x=>x.id===giornoAttivo);
  const kcal=kcalMenu(menuCorrente);

  document.getElementById("titoloGiorno").innerText=`${g.nome} - ${g.tipo}`;
  document.getElementById("riepilogoCalorie").innerHTML=`<div class="notice">Target: <span class="kcal">${targetKcal} kcal</span><br>Menu: <span class="kcal">${kcal} kcal</span><br>Differenza: <span class="kcal">${kcal-targetKcal} kcal</span></div>`;
  document.getElementById("menuGiorno").innerHTML=
    blocco("Colazione",menuCorrente.colazione,"colazione")+
    blocco("Spuntino mattina",menuCorrente.spuntinoMattina,"spuntinoMattina")+
    blocco("Pranzo",menuCorrente.pranzo,"pranzo")+
    blocco("Spuntino pomeriggio",menuCorrente.spuntinoPomeriggio,"spuntinoPomeriggio")+
    blocco("Cena",menuCorrente.cena,"cena");
}

function cambiaPasto(id){
  const nuovo=generaMenu(giornoAttivo);
  menuCorrente[id]=nuovo[id];
  document.getElementById("menuGiorno").innerHTML=
    blocco("Colazione",menuCorrente.colazione,"colazione")+
    blocco("Spuntino mattina",menuCorrente.spuntinoMattina,"spuntinoMattina")+
    blocco("Pranzo",menuCorrente.pranzo,"pranzo")+
    blocco("Spuntino pomeriggio",menuCorrente.spuntinoPomeriggio,"spuntinoPomeriggio")+
    blocco("Cena",menuCorrente.cena,"cena");
  document.getElementById("riepilogoCalorie").innerHTML=`<div class="notice">Target: <span class="kcal">${targetKcal} kcal</span><br>Menu: <span class="kcal">${kcalMenu(menuCorrente)} kcal</span></div>`;
}

function generaSettimana(){
  let html="";
  giorni.forEach(g=>{
    const m=generaMenu(g.id);
    html+=`<div class="card"><span class="badge">${g.nome} - ${g.tipo}</span>
    <p><strong>Colazione:</strong> ${m.colazione}</p>
    <p><strong>Spuntino:</strong> ${m.spuntinoMattina}</p>
    <p><strong>Pranzo:</strong> ${m.pranzo}</p>
    <p><strong>Spuntino:</strong> ${m.spuntinoPomeriggio}</p>
    <p><strong>Cena:</strong> ${m.cena}</p>
    <p class="kcal">${kcalMenu(m)} kcal stimate</p></div>`;
  });
  document.getElementById("settimanaBox").innerHTML=html;
}

function creaSelettoreGiorniProfilo(){
  let html="";
  giorni.forEach((g,i)=>{
    html+=`<label><input type="checkbox" ${g.tipo==="ON"?"checked":""} onchange="giorni[${i}].tipo=this.checked?'ON':'OFF'"> ${g.nome} ON</label>`;
  });
  document.getElementById("giorniAllenamentoBox").innerHTML=html;
}

function calcolaCalorie(){
  const p=Number(document.getElementById("clientePeso").value);
  const h=Number(document.getElementById("clienteAltezza").value);
  const a=Number(document.getElementById("clienteEta").value);
  const s=document.getElementById("clienteSesso").value;
  const f=Number(document.getElementById("clienteAttivita").value);
  const obj=document.getElementById("clienteObiettivo").value;

  if(!p||!h||!a){document.getElementById("calorieBox").innerText="Inserisci peso, altezza ed età.";return;}

  const bmr=s==="M"?(10*p+6.25*h-5*a+5):(10*p+6.25*h-5*a-161);
  const tdee=bmr*f;
  if(obj==="dimagrimento") targetKcal=Math.round(tdee-500);
  if(obj==="mantenimento") targetKcal=Math.round(tdee);
  if(obj==="massa") targetKcal=Math.round(tdee+300);

  document.getElementById("calorieBox").innerHTML=`Metabolismo basale: <span class="kcal">${Math.round(bmr)} kcal</span><br>Fabbisogno: <span class="kcal">${Math.round(tdee)} kcal</span><br>Target: <span class="kcal">${targetKcal} kcal</span>`;
  generaGiorno();
}

function salvaProfilo(){
  const profilo={
    nome:document.getElementById("clienteNome").value,
    cognome:document.getElementById("clienteCognome").value,
    eta:document.getElementById("clienteEta").value,
    altezza:document.getElementById("clienteAltezza").value,
    peso:document.getElementById("clientePeso").value,
    sesso:document.getElementById("clienteSesso").value,
    obiettivo:document.getElementById("clienteObiettivo").value,
    attivita:document.getElementById("clienteAttivita").value,
    targetKcal,
    giorni
  };
  localStorage.setItem("profiloFitAI",JSON.stringify(profilo));
  creaTabsGiorni();
  creaSelettoreGiorniProfilo();
  generaGiorno();
  generaSettimana();
  document.getElementById("calorieBox").innerHTML+="<br>Profilo salvato.";
}

function caricaProfilo(){
  const raw=localStorage.getItem("profiloFitAI");
  if(!raw)return;
  const p=JSON.parse(raw);
  document.getElementById("clienteNome").value=p.nome||"";
  document.getElementById("clienteCognome").value=p.cognome||"";
  document.getElementById("clienteEta").value=p.eta||"";
  document.getElementById("clienteAltezza").value=p.altezza||"";
  document.getElementById("clientePeso").value=p.peso||"";
  document.getElementById("clienteSesso").value=p.sesso||"M";
  document.getElementById("clienteObiettivo").value=p.obiettivo||"dimagrimento";
  document.getElementById("clienteAttivita").value=p.attivita||"1.55";
  targetKcal=p.targetKcal||2500;
  if(p.giorni) giorni=p.giorni;
}

async function importaFile(event){
  const file=event.target.files[0];
  if(!file)return;

  if(file.type==="application/pdf"){
    document.getElementById("importStatus").innerText="Sto leggendo il PDF...";
    const buffer=await file.arrayBuffer();
    const pdf=await pdfjsLib.getDocument({data:buffer}).promise;
    let testo="";
    for(let i=1;i<=pdf.numPages;i++){
      const page=await pdf.getPage(i);
      const content=await page.getTextContent();
      testo+=content.items.map(item=>item.str).join(" ")+"\n";
    }
    document.getElementById("testoDieta").value=testo;
    analizzaDieta();
  }else{
    const reader=new FileReader();
    reader.onload=e=>{document.getElementById("testoDieta").value=e.target.result;analizzaDieta();};
    reader.readAsText(file);
  }
}

function analizzaDieta(){
  const testo=document.getElementById("testoDieta").value;
  if(!testo.trim()){document.getElementById("importStatus").innerText="Inserisci o importa prima una dieta.";return;}

  const pulito=testo.replace(/\s+/g," ")
    .replace(/COLAZIONE/gi,"\nCOLAZIONE ")
    .replace(/SPUNTINO/gi,"\nSPUNTINO ")
    .replace(/PRANZO/gi,"\nPRANZO ")
    .replace(/CENA/gi,"\nCENA ");

  const righe=pulito.split(/\n|•|;/).map(x=>x.trim()).filter(x=>x.length>8);

  function trova(keys){return righe.filter(r=>keys.some(k=>r.toLowerCase().includes(k))).slice(0,10).map(formattaImport);}

  const nuova={
    colazione:trova(["colazione","uova","albume","yogurt greco","avena"]),
    spuntinoMattina:trova(["spuntino mattina","prosciutto","wasa","gallette","semi di chia"]),
    pranzoON:trova(["pranzo","riso","pasta","cous cous","patate"]),
    pranzoOFF:trova(["pranzo","riso","pasta","cous cous","patate"]),
    spuntinoPomeriggio:trova(["spuntino pomeriggio","mandorle","noci","ricotta","yogurt vaccino"]),
    cena:trova(["cena","pesce magro","salmone","legumi","carne rossa","riso integrale"])
  };

  Object.keys(nuova).forEach(k=>{if(nuova[k].length)dieta[k]=nuova[k];});

  document.getElementById("importStatus").innerHTML=`Dieta importata.<br>Colazioni: ${dieta.colazione.length}<br>Pranzi: ${dieta.pranzoON.length}<br>Cene: ${dieta.cena.length}`;
  generaGiorno();
  generaSettimana();
}

function formattaImport(t){
  return t.replace(/carne bianca/gi,"{carneBianca}")
    .replace(/carne rossa/gi,"{carneRossa}")
    .replace(/pesce magro/gi,"{pesceMagro}")
    .replace(/pesce fresco/gi,"{pesceFresco}")
    .replace(/legumi/gi,"{legumi}")
    .replace(/verdure/gi,"{verdure}");
}

function caricaDietaAugusto(){
  document.getElementById("testoDieta").value=`
COLAZIONE: 2 uova o albume 250g, pane segale 60g, avocado 50g.
COLAZIONE: yogurt greco 200-250g, avena 40g, mirtilli o kiwi.
SPUNTINO MATTINA: yogurt vaccino 200g, frutta, semi di chia.
SPUNTINO MATTINA: prosciutto cotto 50-60g, 2-3 Wasa.
PRANZO: carne bianca 200-250g, riso bianco 50-100g, verdure, olio EVO.
PRANZO: pesce fresco 250g, pasta 50-100g, insalata, olio EVO.
PRANZO: vitella 200g, cous cous integrale 50-100g, verdure.
PRANZO: tonno o salmone naturale 150g, patate 200-400g.
SPUNTINO POMERIGGIO: yogurt vaccino 150-200g, frutta, mandorle o noci 10g.
SPUNTINO POMERIGGIO: uovo, prosciutto cotto 60g o ricotta 50g, Wasa.
CENA: carne bianca o carne rossa 200-250g, patate 150-300g, verdure.
CENA: pesce magro 200-250g, tonno o salmone, riso integrale 50-80g.
CENA: legumi 150g, pane integrale 50g o cereali 30g, verdure.`;
  analizzaDieta();
}

const listaSpesaBase=["Uova","Albume","Yogurt greco","Yogurt vaccino","Avena","Pane di segale","Pane integrale","Wasa","Gallette di riso","Riso bianco","Riso integrale","Pasta","Cous cous","Patate","Pollo","Tacchino","Coniglio","Vitella","Manzo magro","Merluzzo","Nasello","Palombo","Persico","Orata","Branzino","Pesce spada","Tonno","Salmone","Ceci","Fagioli","Lenticchie","Mirtilli","Kiwi","Mele","Banane","Avocado","Mandorle","Noci","Semi di chia","Verdure","Olio EVO","Limone","Spezie"];

function generaListaSpesa(){
  let html="";
  listaSpesaBase.forEach((x,i)=>{
    html+=`<div class="shopping-item"><strong id="item${i}">${x}</strong><div class="shopping-actions"><label><input type="radio" name="s${i}" onchange="segna(${i},false)"> Da comprare</label><label><input type="radio" name="s${i}" onchange="segna(${i},true)"> Ce l’ho</label></div></div>`;
  });
  document.getElementById("listaSpesa").innerHTML=html;
}

function segna(i,have){document.getElementById("item"+i).classList.toggle("have",have)}

function resetApp(){localStorage.clear();location.reload()}

caricaProfilo();
creaTabsGiorni();
creaSelettoreGiorniProfilo();
generaGiorno();
generaSettimana();
</script>

</body>
</html>
