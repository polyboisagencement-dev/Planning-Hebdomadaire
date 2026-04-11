<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Planning Pro V9</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <style>
        :root { 
            --p: #2563eb; --p-light: #eff6ff;
            --s: #10b981; --s-light: #ecfdf5;
            --d: #ef4444; --d-light: #fef2f2;
            --info: #0ea5e9; --info-light: #f0f9ff;
            --warn: #f59e0b; --warn-light: #fffbeb;
            --txt: #1f2937; --txt-light: #6b7280;
            --bg: #f3f4f6; --card-bg: #ffffff;
            --border: #e5e7eb;
            --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
        
        body { font-family: 'Inter', sans-serif; background: var(--bg); margin: 0; padding: 10px; color: var(--txt); -webkit-font-smoothing: antialiased; }
        
        /* HEADER ESTHÉTIQUE */
        header { 
            background: #1e40af; background: linear-gradient(135deg, #1e40af 0%, #2563eb 100%); 
            color: white; padding: 20px 15px; border-radius: 16px; margin-bottom: 20px; box-shadow: var(--shadow);
            display: flex; flex-direction: column; align-items: center; gap: 15px;
        }
        .brand { display: flex; align-items: center; gap: 12px; width: 100%; justify-content: center; }
        .brand img { width: 45px; height: 45px; border-radius: 50%; object-fit: cover; border: 2px solid white; background: white; }
        .brand h1 { margin: 0; font-size: 1.4rem; font-weight: 800; letter-spacing: -0.5px; }
        .nav-ctn { display: flex; justify-content: center; gap: 10px; width: 100%; }
        .btn-nav { padding: 10px 18px; border-radius: 10px; border: 1px solid rgba(255,255,255,0.2); background: rgba(255,255,255,0.1); color: white; font-weight: 600; font-size: 0.85rem; cursor: pointer; transition: all 0.2s; }
        .btn-nav:active { background: rgba(255,255,255,0.2); transform: translateY(1px); }
        .btn-nav.today { background: white; color: var(--p); border: none; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }

        /* ADMIN AREA DESIGN */
        #adminArea { 
            background: var(--card-bg); padding: 20px; border-radius: 16px; margin-bottom: 25px; 
            border: 2px dashed var(--p); box-shadow: var(--shadow); display: none;
        }
        #adminArea strong { color: var(--p); display: block; margin-bottom: 15px; font-size: 1.1rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.5px; text-align: center; }
        
        .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(310px, 1fr)); gap: 20px; }
        .col { background: #e5e7eb; border-radius: 16px; padding: 12px; min-height: 250px; }
        .day-h { text-align: center; font-weight: 800; color: #1e40af; padding: 12px; text-transform: uppercase; font-size: 0.9rem; letter-spacing: 1px; border-bottom: 1px solid #d1d5db; margin-bottom: 10px; }

        /* CARTE CHANTIER ÉPURÉE */
        .card { 
            background: var(--card-bg); padding: 18px; border-radius: 14px; margin-top: 12px; 
            border-left: 5px solid #d1d5db; box-shadow: 0 2px 4px rgba(0,0,0,0.05); transition: transform 0.2s, box-shadow 0.2s;
            position: relative;
        }
        .card:active { transform: scale(0.98); box-shadow: none; }
        .card.done { border-left-color: var(--s); background: #f9fafb; opacity: 0.8; }
        .card.issue { border-left-color: var(--d); }
        
        .prenom { background: #fee2e2; color: #991b1b; padding: 4px 10px; border-radius: 20px; font-size: 0.75rem; font-weight: 700; display: inline-block; text-transform: uppercase; margin-bottom: 6px; }
        .title { font-weight: 800; font-size: 1.2rem; display: block; margin: 4px 0; color: var(--txt); letter-spacing: -0.3px; }
        .adr { font-size: 0.85rem; color: var(--txt-light); display: flex; align-items: center; gap: 5px; margin-top: 4px; }
        
        .com-box { margin-top: 12px; font-size: 0.85rem; background: #f3f4f6; padding: 12px; border-radius: 10px; color: #4b5563; border: 1px solid #e5e7eb; line-height: 1.4; }
        .report-box { background: var(--d-light); border: 1px solid #fda4af; color: #9f1239; padding: 12px; border-radius: 10px; margin-top: 12px; font-size: 0.85rem; }
        .report-box strong { font-weight: 700; display: block; margin-bottom: 4px; text-transform: uppercase; font-size: 0.75rem; }

        /* BOUTONS MODERNES */
        .contact-btns { display: flex; gap: 8px; margin-top: 15px; }
        .btn-c { flex: 1; text-decoration: none; text-align: center; padding: 12px; border-radius: 10px; font-size: 0.8rem; font-weight: 700; color: white; text-transform: uppercase; transition: opacity 0.2s; }
        .btn-c:active { opacity: 0.8; }
        .waze { background: #33ccff; background: linear-gradient(135deg, #33ccff 0%, #0099ff 100%); }
        .call { background: #ff9f43; background: linear-gradient(135deg, #ff9f43 0%, #ff6b6b 100%); }

        .footer-btns { display: flex; gap: 6px; margin-top: 18px; flex-wrap: wrap; border-top: 1px solid var(--border); padding-top: 12px; }
        .btn-f { flex: 1; min-width: 85px; padding: 12px 6px; border: none; border-radius: 9px; color: var(--txt); font-weight: 700; font-size: 0.7rem; cursor: pointer; text-transform: uppercase; transition: all 0.2s; display: flex; align-items: center; justify-content: center; gap: 4px; }
        .btn-f:active { transform: translateY(1px); }
        
        .ok { background: var(--s-light); color: var(--s); }
        .cam { background: var(--p-light); color: var(--p); }
        .pb { background: var(--d-light); color: var(--d); }
        .admin-file { background: #f3f4f6; color: #4b5563; }

        /* INPUTS DESIGN */
        input, select { padding: 14px; border: 1px solid var(--border); border-radius: 10px; width: 100%; margin-bottom: 12px; box-sizing: border-box; font-family: 'Inter', sans-serif; font-size: 0.9rem; transition: border-color 0.2s; }
        input:focus, select:focus { border-color: var(--p); outline: none; box-shadow: 0 0 0 3px var(--p-light); }
        
        /* POPUPS DESIGN */
        .overlay { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(17, 24, 39, 0.9); z-index: 10000; align-items: center; justify-content: center; flex-direction: column; padding: 15px; box-sizing: border-box; backdrop-filter: blur(5px); }
        .ov-header { display: flex; gap: 10px; margin-bottom: 15px; width: 100%; max-width: 800px; justify-content: center; }
        .ov-btn { padding: 12px 20px; border-radius: 10px; border: none; font-weight: 700; font-size: 0.85rem; cursor: pointer; text-transform: uppercase; }
        #fullPhoto { max-width:100%; max-height:75vh; border:4px solid white; border-radius:12px; box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.2); }
        #fullDoc { width: 100%; max-width: 800px; height: 75vh; background: white; border: none; border-radius: 12px; box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.2); }
    </style>
</head>
<body>

<div id="photoOverlay" class="overlay">
    <div class="ov-header"><button class="ov-btn pb" onclick="closePopups()">Fermer</button></div>
    <img id="fullPhoto" src="">
</div>

<div id="docOverlay" class="overlay">
    <div class="ov-header">
        <button class="ov-btn pb" onclick="closePopups()">Fermer</button>
        <a id="downloadLink" href="" download class="ov-btn admin-file" style="text-decoration:none;">Télécharger</a>
    </div>
    <iframe id="fullDoc" src=""></iframe>
</div>

<header>
    <div class="brand">
        <img src="https://github.com/polyboisagencement-dev/Planning-Hebdomadaire/blob/main/icon.png?raw=true" alt="Logo">
        <h1>Planning <span style="font-weight:400; opacity:0.8">Chantiers</span></h1>
    </div>
    <div class="nav-ctn">
        <button onclick="changeWeek(-7)" class="btn-nav">◄ Sem. Préc.</button>
        <button onclick="goToday()" class="btn-nav today">Aujourd'hui</button>
        <button onclick="changeWeek(7)" class="btn-nav">Sem. Suiv. ►</button>
    </div>
    <div id="weekRange" style="font-size:0.9rem; font-weight:600; background:rgba(0,0,0,0.1); padding:5px 15px; border-radius:20px;">Chargement...</div>
</header>

<div id="adminArea">
    <strong>Créer un nouveau Chantier</strong>
    <input type="text" id="fPrenom" placeholder="Prénom de l'ouvrier">
    <input type="text" id="fTitre" placeholder="Nom du Client / Affaire">
    <input type="text" id="fAdr" placeholder="Adresse complète">
    <input type="tel" id="fTel" placeholder="Numéro de téléphone">
    <input type="text" id="fCom" placeholder="Notes (Code portail, infos clés...)">
    <select id="fDay"><option value="0">Lundi</option><option value="1">Mardi</option><option value="2">Mercredi</option><option value="3">Jeudi</option><option value="4">Vendredi</option><option value="5">Samedi</option><option value="6">Dimanche</option></select>
    
    <div style="background:#f9fafb; padding:15px; border-radius:12px; margin-bottom:15px; border:1px dashed #d1d5db; text-align:center;">
        <label style="cursor:pointer; font-size:0.85rem; font-weight:700; color:var(--p);">
            📄 JOINDRE UN DOCUMENT (PDF, PLAN)
            <input type="file" id="fDocInit" style="display:none" onchange="previewFile()">
        </label>
        <div id="fileStatus" style="font-size:0.75rem; margin-top:6px; color:var(--txt-light);">Aucun fichier sélectionné</div>
    </div>
    <button onclick="ajouter()" style="background:var(--p); color:white; width:100%; border:none; padding:16px; border-radius:12px; font-weight:700; font-size:1rem; cursor:pointer;">Ajouter au Planning</button>
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
    let tempFileData = null; let tempFileName = ""; let tousLesChantiers = {};

    if (isAdmin) document.getElementById('adminArea').style.display = 'block';

    function getMonday(d) { d = new Date(d); let day = d.getDay(), diff = d.getDate() - day + (day == 0 ? -6:1); return new Date(d.setDate(diff)); }
    function getWeekID(date) { let d = new Date(date); d.setHours(0,0,0,0); d.setDate(d.getDate() + 4 - (d.getDay() || 7)); return d.getFullYear() + "-W" + Math.ceil((((d - new Date(d.getFullYear(),0,1)) / 86400000) + 1) / 7); }
    function changeWeek(days) { currentMonday.setDate(currentMonday.getDate() + days); updateCalendar(); }
    function goToday() { currentMonday = getMonday(new Date()); updateCalendar(); }

    function previewFile() {
        const file = document.getElementById('fDocInit').files[0];
        if(!file) return;
        const reader = new FileReader();
        reader.onload = (e) => { tempFileData = e.target.result; tempFileName = file.name; document.getElementById('fileStatus').innerText = "✅ Fichier prêt : " + file.name; document.getElementById('fileStatus').style.color = "var(--s)"; };
        reader.readAsDataURL(file);
    }

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

            // Photos miniatures
            let imgs = ""; if(t.photos) Object.keys(t.photos).forEach(k => imgs += `<img src="${t.photos[k]}" style="width:65px;height:65px;object-fit:cover;border-radius:10px;margin:5px 5px 0 0;cursor:pointer;border:1px solid var(--border);" onclick="openPhoto('${t.id}','${k}')">`);
            
            // Liens documents
            let docs = ""; if(t.files) Object.keys(t.files).forEach(k => docs += `<div class="file-link" onclick="openDoc('${t.id}','${k}')">📄 Fichier : ${t.files[k].name}</div>`);
            
            // Rapports problèmes
            let pbs = (t.reports) ? `<div class="report-box"><strong>Rapports de Problèmes :</strong>` + Object.values(t.reports).map(r => `<div>• ${r}</div>`).join('') + `</div>` : "";

            const card = document.createElement('div');
            card.className = `card ${t.status}`;
            card.innerHTML = `
                <div style="display:flex; justify-content:space-between; align-items:flex-start;">
                    <div>
                        <span class="prenom">${t.prenom || 'Ouvrier'}</span>
                        <span class="title">${t.titre}</span>
                        <div class="adr">📍 ${t.adr || 'Adresse non spécifiée'}</div>
                    </div>
                    ${isAdmin ? `<button onclick="delT('${t.id}')" style="color:var(--d);border:none;background:none;font-size:0.75rem;cursor:pointer;opacity:0.5;">🗑️</button>` : ''}
                </div>
                
                <div class="contact-btns">
                    <a href="waze://?q=${encodeURIComponent(t.adr)}&navigate=yes" class="btn-c waze">GPS Waze</a>
                    <a href="tel:${t.tel}" class="btn-c call">Appeler</a>
                </div>
                
                ${t.com ? `<div class="com-box"><strong>Notes Admin :</strong><br>${t.com}</div>` : ''}
                
                <div style="margin-top:12px; display:flex; flex-wrap:wrap;">${imgs}</div>
                <div>${docs}</div>
                ${pbs}
                
                <div class="footer-btns">
                    <button class="btn-f ok" onclick="setStatus('${t.id}', 'done', '${t.titre}')">✔️ Fini</button>
                    <button class="btn-f cam" onclick="triggerUpload('${t.id}', 'img')">📸 Photo</button>
                    ${isAdmin ? `<button class="btn-f admin-file" onclick="triggerUpload('${t.id}', 'file')">📂 Doc.</button>` : ''}
                    <button class="btn-f pb" onclick="addPb('${t.id}', '${t.titre}')">⚠️ PB</button>
                </div>
            `;
            list.appendChild(card);
        });
    }

    // Fonctions techniques inchangées
    function sendNotif(titre, msg) { fetch('https://ntfy.sh/' + NTFY_TOPIC, { method: 'POST', body: titre + "\n" + msg, headers: { 'Priority': '5' } }).catch(e=>{}); }
    function setStatus(id, s, t) { db.ref('chantiers/'+id).update({status:s}); if(s==='done') sendNotif("CHANTIER TERMINE ✅", "Client: " + t); }
    function addPb(id, t) { let m = prompt("Quel est le problème ?"); if(m) { let time = new Date().toLocaleTimeString('fr-FR', {hour:'2-digit', minute:'2-digit'}); db.ref('chantiers/'+id+'/reports').push(time + " : " + m); db.ref('chantiers/'+id).update({status:'issue'}); sendNotif("⚠️ ALERTE PROBLEME", t + " : " + m); } }
    function openPhoto(id, k) { document.getElementById('fullPhoto').src = tousLesChantiers[id].photos[k]; document.getElementById('photoOverlay').style.display='flex'; }
    function openDoc(id, k) { let f = tousLesChantiers[id].files[k]; document.getElementById('fullDoc').src = f.data; document.getElementById('downloadLink').href = f.data; document.getElementById('downloadLink').download = f.name; document.getElementById('docOverlay').style.display='flex'; }
    function closePopups() { document.querySelectorAll('.overlay').forEach(o => o.style.display='none'); document.getElementById('fullDoc').src=""; }
    function triggerUpload(id, type) { activeId = id; document.getElementById(type === 'img' ? 'photoInput' : 'fileInput').click(); }

    function uploadFile(mode) {
        const fileInput = document.getElementById(mode === 'img' ? 'photoInput' : 'fileInput');
        const file = fileInput.files[0];
        if(!file || !activeId) return;
        if(file.size > 2000000) { alert("Fichier trop lourd (max 2Mo)"); fileInput.value=""; return; }
        const reader = new FileReader();
        reader.onload = (e) => {
            if(mode === 'img') {
                const img = new Image();
                img.onload = () => {
                    const canvas = document.createElement('canvas'); const ctx = canvas.getContext('2d');
                    const MAX = 800; let w = img.width, h = img.height;
                    if (w > h && w > MAX) { h *= MAX/w; w = MAX; } else if (h > MAX) { w *= MAX/h; h = MAX; }
                    canvas.width = w; canvas.height = h; ctx.drawImage(img, 0,0,w,h);
                    db.ref('chantiers/'+activeId+'/photos').push(canvas.toDataURL('image/jpeg', 0.6));
                };
                img.src = e.target.result;
            } else { db.ref('chantiers/'+activeId+'/files').push({name:file.name, data:e.target.result}); }
            fileInput.value = "";
        };
        reader.readAsDataURL(file);
    }

    function ajouter() {
        const ref = db.ref('chantiers').push();
        const data = { id:ref.key, day:document.getElementById('fDay').value, prenom:document.getElementById('fPrenom').value, titre:document.getElementById('fTitre').value, adr:document.getElementById('fAdr').value, tel:document.getElementById('fTel').value, com:document.getElementById('fCom').value, weekID:getWeekID(currentMonday), status:'pending', ord:Date.now() };
        ref.set(data).then(() => { 
            if(tempFileData) db.ref('chantiers/'+data.id+'/files').push({name:tempFileName, data:tempFileData});
            ["fTitre","fAdr","fTel","fCom","fPrenom"].forEach(i => document.getElementById(i).value="");
            document.getElementById('fileStatus').innerText = "Aucun fichier sélectionné"; document.getElementById('fileStatus').style.color="var(--txt-light)"; tempFileData = null;
        });
    }
    function delT(id) { if(confirm("Supprimer ce chantier de la base de données ?")) db.ref('chantiers/'+id).remove(); }
    updateCalendar();
</script>
</body>
</html>
