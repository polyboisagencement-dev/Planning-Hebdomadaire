
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Planning Pro V8.5</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <style>
        :root { --p: #2563eb; --s: #10b981; --d: #ef4444; --bg: #f8fafc; }
        body { font-family: sans-serif; background: var(--bg); margin: 0; padding: 10px; }
        header { background: #1e40af; color: white; padding: 15px; border-radius: 12px; text-align: center; margin-bottom: 15px; }
        #adminArea { background: white; padding: 15px; border-radius: 12px; margin-bottom: 20px; border: 3px solid var(--p); display: none; }
        .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 15px; }
        .col { background: #e2e8f0; border-radius: 12px; padding: 10px; min-height: 200px; margin-bottom:10px; }
        .day-h { text-align: center; font-weight: bold; color: #1e40af; padding: 10px; text-transform: uppercase; }
        .card { background: white; padding: 15px; border-radius: 12px; margin-top: 10px; border-left: 6px solid #94a3b8; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        .card.done { border-left-color: var(--s); background: #f0fdf4; }
        .card.issue { border-left-color: var(--d); background: #fef2f2; }
        .title { font-weight: 800; font-size: 1.1rem; display: block; margin: 5px 0; }
        .prenom { background: #fee2e2; color: #991b1b; padding: 3px 8px; border-radius: 5px; font-size: 0.8rem; font-weight: bold; display: inline-block; }
        .file-link { display: flex; align-items: center; background: #e0f2fe; border: 1px solid #7dd3fc; padding: 10px; border-radius: 8px; font-size: 0.85rem; color: #0369a1; font-weight: bold; margin-top: 10px; cursor: pointer; }
        .footer-btns { display: flex; gap: 5px; margin-top: 15px; flex-wrap: wrap; }
        .btn-f { flex: 1; min-width: 80px; padding: 15px 5px; border: none; border-radius: 8px; color: white; font-weight: bold; font-size: 0.7rem; cursor: pointer; text-transform: uppercase; }
        .ok { background: var(--s); } .cam { background: #6366f1; } .pb { background: var(--d); } .admin-file { background: #475569; }
        .report-box { background: #fff1f2; border: 1px solid #fda4af; color: #9f1239; padding: 10px; border-radius: 8px; margin-top: 10px; font-size: 0.85rem; }
        input, select { padding: 12px; border: 1px solid #ddd; border-radius: 8px; width: 100%; margin-bottom: 10px; box-sizing: border-box; }
        .overlay { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.95); z-index: 10000; align-items: center; justify-content: center; flex-direction: column; padding: 10px; }
        #docOverlay iframe { width: 100%; max-width: 800px; height: 80vh; background: white; border-radius: 8px; }
    </style>
</head>
<body>

<div id="photoOverlay" class="overlay" onclick="closePopups()">
    <img id="fullPhoto" src="" style="max-width:95%; max-height:80vh; border:3px solid white; border-radius:8px;">
    <button style="margin-top:20px; padding:10px 20px; background:red; color:white; border:none; border-radius:8px;">FERMER</button>
</div>

<div id="docOverlay" class="overlay">
    <div style="margin-bottom:10px; display:flex; gap:10px;">
        <button onclick="closePopups()" style="background:red; color:white; border:none; padding:10px 20px; border-radius:8px;">FERMER</button>
        <a id="downloadLink" href="" download style="background:var(--p); color:white; text-decoration:none; padding:10px 20px; border-radius:8px;">TELECHARGER</a>
    </div>
    <iframe id="fullDoc" src=""></iframe>
</div>

<header>
    <h2 id="weekRange">Chargement...</h2>
    <div style="display:flex; justify-content: center; gap:10px;">
        <button onclick="changeWeek(-7)" style="padding:10px 20px; border-radius:8px; border:none; background:rgba(255,255,255,0.2); color:white;">◄</button>
        <button onclick="goToday()" style="padding:10px 20px; border-radius:8px; border:none; background:white; color:var(--p); font-weight:bold;">AUJOURD'HUI</button>
        <button onclick="changeWeek(7)" style="padding:10px 20px; border-radius:8px; border:none; background:rgba(255,255,255,0.2); color:white;">►</button>
    </div>
</header>

<div id="adminArea">
    <strong style="color:var(--p);">CRÉATION DE CHANTIER</strong>
    <input type="text" id="fPrenom" placeholder="Prénom de l'ouvrier">
    <input type="text" id="fTitre" placeholder="Nom du Client">
    <input type="text" id="fAdr" placeholder="Adresse complète">
    <input type="tel" id="fTel" placeholder="Téléphone">
    <input type="text" id="fCom" placeholder="Notes / Infos">
    <select id="fDay"><option value="0">Lundi</option><option value="1">Mardi</option><option value="2">Mercredi</option><option value="3">Jeudi</option><option value="4">Vendredi</option><option value="5">Samedi</option><option value="6">Dimanche</option></select>
    <button onclick="ajouter()" style="background:var(--p); color:white; width:100%; border:none; padding:15px; border-radius:8px; font-weight:bold;">CRÉER LE CHANTIER</button>
</div>

<div class="grid" id="board"></div>

<input type="file" id="photoInput" accept="image/*" capture="environment" style="display:none" onchange="uploadFile('img')">
<input type="file" id="fileInput" accept=".pdf,.doc,.docx,.xls,.xlsx" style="display:none" onchange="uploadFile('doc')">

<script>
    const firebaseConfig = {
        apiKey: "AIzaSyBqKxFN6W_2LzI82P4kKrKcfJqQAVh7SYs",
        authDomain: "planning-chantier-3f4ac.firebaseapp.com",
        databaseURL: "https://planning-chantier-3f4ac-default-rtdb.europe-west1.firebasedatabase.app/",
        projectId: "planning-chantier-3f4ac",
        storageBucket: "planning-chantier-3f4ac.firebasestorage.app",
        messagingSenderId: "73313634289",
        appId: "1:73313634289:web:794065392de1411e2c2650"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
    
    const NTFY_TOPIC = "Alerte_chantier"; 

    let currentMonday = getMonday(new Date());
    let activeId = null;
    let isAdmin = new URLSearchParams(window.location.search).get('admin') === '1';
    let tousLesChantiers = {};

    if (isAdmin) document.getElementById('adminArea').style.display = 'block';

    function getMonday(d) { d = new Date(d); let day = d.getDay(), diff = d.getDate() - day + (day == 0 ? -6:1); return new Date(d.setDate(diff)); }
    function getWeekID(date) { let d = new Date(date); d.setHours(0,0,0,0); d.setDate(d.getDate() + 4 - (d.getDay() || 7)); return d.getFullYear() + "-W" + Math.ceil((((d - new Date(d.getFullYear(),0,1)) / 86400000) + 1) / 7); }
    function changeWeek(days) { currentMonday.setDate(currentMonday.getDate() + days); updateCalendar(); }
    function goToday() { currentMonday = getMonday(new Date()); updateCalendar(); }

    function updateCalendar() {
        const jours = ["Lundi","Mardi","Mercredi","Jeudi","Vendredi","Samedi","Dimanche"];
        const weekID = getWeekID(currentMonday);
        document.getElementById('board').innerHTML = jours.map((j, i) => {
            let d = new Date(currentMonday); d.setDate(currentMonday.getDate() + i);
            if(i === 0) document.getElementById('weekRange').innerText = "Semaine du " + d.toLocaleDateString();
            return `<div class="col"><div class="day-h">${j} ${d.getDate()}</div><div class="list" id="list-${i}"></div></div>`;
        }).join('');
        db.ref('chantiers').off();
        db.ref('chantiers').orderByChild('weekID').equalTo(weekID).on('value', snap => {
            tousLesChantiers = snap.val() || {};
            render(Object.values(tousLesChantiers));
        });
    }

    function render(tasks) {
        document.querySelectorAll('.list').forEach(l => l.innerHTML = "");
        tasks.sort((a,b) => (a.ord||0)-(b.ord||0)).forEach(t => {
            const list = document.getElementById('list-' + t.day);
            if(!list) return;

            let imgs = ""; if(t.photos) Object.keys(t.photos).forEach(k => imgs += `<img src="${t.photos[k]}" style="width:60px;height:60px;object-fit:cover;border-radius:6px;margin:5px;cursor:pointer;" onclick="openPhoto('${t.id}','${k}')">`);
            let docs = ""; if(t.files) Object.keys(t.files).forEach(k => docs += `<div class="file-link" onclick="openDoc('${t.id}','${k}')">DOC : ${t.files[k].name}</div>`);
            
            let pbs = ""; 
            if(t.reports) {
                pbs = `<div class="report-box"><strong>RAPPORTS :</strong>` + Object.values(t.reports).map(r => `<div>- ${r}</div>`).join('') + `</div>`;
            }

            const card = document.createElement('div');
            card.className = `card ${t.status}`;
            card.innerHTML = `
                <span class="prenom">${t.prenom || 'Ouvrier'}</span>
                <span class="title">${t.titre}</span>
                <span style="font-size:0.85rem; color:#666;">📍 ${t.adr || ''}</span>
                <div style="display:flex; gap:5px; margin-top:10px;">
                    <a href="waze://?q=${encodeURIComponent(t.adr)}&navigate=yes" style="flex:1;background:#00d4ff;color:white;text-decoration:none;text-align:center;padding:12px;border-radius:8px;font-size:0.75rem;font-weight:bold;">WAZE</a>
                    <a href="tel:${t.tel}" style="flex:1;background:#ff9f43;color:white;text-decoration:none;text-align:center;padding:12px;border-radius:8px;font-size:0.75rem;font-weight:bold;">APPEL</a>
                </div>
                ${t.com ? `<div style="margin-top:10px;font-size:0.8rem;background:#f1f5f9;padding:10px;border-radius:8px;">${t.com}</div>` : ''}
                <div style="margin-top:10px;">${imgs}</div>
                <div>${docs}</div>
                ${pbs}
                <div class="footer-btns">
                    <button class="btn-f ok" onclick="setStatus('${t.id}', 'done', '${t.titre}')">FINI</button>
                    <button class="btn-f cam" onclick="triggerUpload('${t.id}', 'img')">PHOTO</button>
                    ${isAdmin ? `<button class="btn-f admin-file" onclick="triggerUpload('${t.id}', 'file')">DOC</button>` : ''}
                    <button class="btn-f pb" onclick="addPb('${t.id}', '${t.titre}')">PROBLEME</button>
                </div>
                ${isAdmin ? `<button onclick="delT('${t.id}')" style="color:red;border:none;background:none;font-size:0.6rem;margin-top:15px;cursor:pointer;">[SUPPRIMER]</button>` : ''}
            `;
            list.appendChild(card);
        });
    }

    // NOUVELLE FONCTION D'ENVOI IDENTIQUE AU TEST MANUEL
    function sendNotif(titre, msg) {
        const corps = titre + "\n" + msg;
        fetch('https://ntfy.sh/' + NTFY_TOPIC, {
            method: 'POST',
            body: corps,
            headers: {
                'Priority': '5'
            }
        }).catch(e => console.log("Erreur silencieuse notif"));
    }

    function setStatus(id, s, t) { 
        db.ref('chantiers/'+id).update({status:s}); 
        if(s==='done') sendNotif("CHANTIER TERMINE", "Client: " + t); 
    }
    
    function addPb(id, t) { 
        let m = prompt("Quel est le probleme ?"); 
        if(m) { 
            let time = new Date().toLocaleTimeString('fr-FR', {hour:'2-digit', minute:'2-digit'}); 
            db.ref('chantiers/'+id+'/reports').push(time + " : " + m); 
            db.ref('chantiers/'+id).update({status:'issue'}); 
            sendNotif("ALERTE PROBLEME", t + " : " + m); 
        } 
    }
    
    function openPhoto(id, k) { document.getElementById('fullPhoto').src = tousLesChantiers[id].photos[k]; document.getElementById('photoOverlay').style.display='flex'; }
    function openDoc(id, k) { let f = tousLesChantiers[id].files[k]; document.getElementById('fullDoc').src = f.data; document.getElementById('downloadLink').href = f.data; document.getElementById('downloadLink').download = f.name; document.getElementById('docOverlay').style.display='flex'; }
    function closePopups() { document.querySelectorAll('.overlay').forEach(o => o.style.display='none'); document.getElementById('fullDoc').src=""; }
    function triggerUpload(id, type) { activeId = id; document.getElementById(type === 'img' ? 'photoInput' : 'fileInput').click(); }

    function uploadFile(mode) {
        const file = document.getElementById(mode === 'img' ? 'photoInput' : 'fileInput').files[0];
        if(!file) return;
        const reader = new FileReader();
        reader.onload = (e) => {
            if(mode === 'img') {
                const img = new Image();
                img.onload = () => {
                    const canvas = document.createElement('canvas');
                    const MAX = 800; let w = img.width, h = img.height;
                    if (w > h && w > MAX) { h *= MAX/w; w = MAX; } else if (h > MAX) { w *= MAX/h; h = MAX; }
                    canvas.width = w; canvas.height = h;
                    canvas.getContext('2d').drawImage(img, 0,0,w,h);
                    db.ref('chantiers/'+activeId+'/photos').push(canvas.toDataURL('image/jpeg', 0.6));
                };
                img.src = e.target.result;
            } else { db.ref('chantiers/'+activeId+'/files').push({name:file.name, data:e.target.result}); }
        };
        reader.readAsDataURL(file);
    }

    function ajouter() {
        const ref = db.ref('chantiers').push();
        const data = { id:ref.key, day:document.getElementById('fDay').value, prenom:document.getElementById('fPrenom').value, titre:document.getElementById('fTitre').value, adr:document.getElementById('fAdr').value, tel:document.getElementById('fTel').value, com:document.getElementById('fCom').value, weekID:getWeekID(currentMonday), status:'pending', ord:Date.now() };
        ref.set(data).then(() => { ["fTitre","fAdr","fTel","fCom","fPrenom"].forEach(i => document.getElementById(i).value=""); });
    }
    function delT(id) { if(confirm("Supprimer ?")) db.ref('chantiers/'+id).remove(); }
    updateCalendar();
</script>
</body>
</html>
