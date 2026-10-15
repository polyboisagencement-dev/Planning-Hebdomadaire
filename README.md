<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0">
    <title>PolyBois Agencement V10.8</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <style>
        :root { --p: #1e40af; --p-light: #eff6ff; --s: #10b981; --s-light: #ecfdf5; --d: #ef4444; --d-light: #fef2f2; --n: #4b5563; --n-light: #f3f4f6; --txt: #1f2937; --txt-light: #6b7280; --bg: #f3f4f6; --card-bg: #ffffff; --border: #e5e7eb; --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1); }
        body { font-family: 'Inter', sans-serif; background: var(--bg); margin: 0; padding: 10px; color: var(--txt); }
        header { background: #1e3a8a; background: linear-gradient(135deg, #1e3a8a 0%, #2563eb 100%); color: white; padding: 25px 15px; border-radius: 16px; margin-bottom: 20px; text-align: center; }
        .brand-logo { width: 60px; height: 60px; border-radius: 50%; object-fit: cover; border: 3px solid white; background: white; margin-bottom: 10px; }
        .brand-name { margin: 0; font-size: 1.6rem; font-weight: 900; text-transform: uppercase; margin-bottom: 15px; }
        .nav-ctn { display: flex; justify-content: center; gap: 8px; margin-bottom: 15px; }
        .btn-nav { padding: 10px 15px; border-radius: 10px; border: 1px solid rgba(255,255,255,0.2); background: rgba(255,255,255,0.1); color: white; font-weight: 700; font-size: 0.75rem; cursor: pointer; }
        .btn-nav.today { background: white; color: #1e3a8a; border: none; }
        #adminArea { background: var(--card-bg); padding: 20px; border-radius: 16px; margin-bottom: 25px; border: 2px dashed var(--p); display: none; }
        .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(310px, 1fr)); gap: 20px; }
        .col { background: #e5e7eb; border-radius: 16px; padding: 12px; min-height: 250px; }
        .day-h { text-align: center; font-weight: 800; color: #1e40af; padding: 12px; text-transform: uppercase; font-size: 0.9rem; border-bottom: 1px solid #d1d5db; margin-bottom: 10px; }
        .card { background: var(--card-bg); padding: 18px; border-radius: 14px; margin-top: 12px; border-left: 5px solid #d1d5db; box-shadow: var(--shadow); position: relative; }
        .card.done { border-left-color: var(--s); background: #f9fafb; opacity: 0.8; }
        .card.issue { border-left-color: var(--d); }
        .prenom { background: #fee2e2; color: #991b1b; padding: 4px 10px; border-radius: 20px; font-size: 0.75rem; font-weight: 800; display: inline-block; text-transform: uppercase; }
        .title { font-weight: 800; font-size: 1.2rem; display: block; margin: 4px 0; }
        .adr { font-size: 0.85rem; color: var(--txt-light); margin-bottom: 10px; }
        .contact-btns { display: flex; gap: 8px; margin-bottom: 12px; }
        .btn-c { flex: 1; text-decoration: none; text-align: center; padding: 10px; border-radius: 8px; font-size: 0.75rem; font-weight: 800; color: white; text-transform: uppercase; }
        .waze { background: #33ccff; } .call { background: #ff9f43; }
        .footer-btns { display: flex; gap: 5px; margin-top: 15px; border-top: 1px solid var(--border); padding-top: 10px; flex-wrap: wrap; }
        .btn-f { flex: 1; min-width: 65px; padding: 10px 2px; border: none; border-radius: 8px; font-weight: 800; font-size: 0.6rem; cursor: pointer; text-transform: uppercase; }
        .ok { background: var(--s-light); color: var(--s); } .cam { background: var(--p-light); color: var(--p); } .pb { background: var(--d-light); color: var(--d); } .note { background: var(--n-light); color: var(--n); } .admin-btn { background: #eee; color: #333; }
        .btn-start { background: #e0f2fe; color: #0284c7; } .btn-end { background: #fef3c7; color: #d97706; }
        .reorder-btns { position: absolute; top: 10px; right: 10px; display: flex; flex-direction: column; gap: 4px; }
        .btn-re { background: white; border: 1px solid var(--border); border-radius: 6px; padding: 4px 8px; cursor: pointer; font-size: 12px; }
        .overlay { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.9); z-index: 10000; align-items: center; justify-content: center; flex-direction: column; overflow-y: auto; }
        #fullPhoto { max-width: 100%; height: auto; touch-action: pinch-zoom; }
        .overlay-btns { display: flex; gap: 10px; margin-top: 20px; }
        .btn-dl { padding: 12px 20px; background: var(--s); color: white; border: none; border-radius: 10px; font-weight: 800; text-decoration: none; font-size: 0.8rem; }
        .btn-close { padding: 12px 20px; background: var(--d); color: white; border: none; border-radius: 10px; font-weight: 800; font-size: 0.8rem; }
        .badge-note { background: #fefce8; border: 1px solid #fef08a; padding: 8px; border-radius: 8px; margin-top: 8px; font-size: 0.75rem; color: #854d0e; }
        .badge-time { background: #f0fdf4; border: 1px solid #bbf7d0; padding: 8px; border-radius: 8px; margin-top: 8px; font-size: 0.75rem; color: #166534; }
        input, select { padding: 12px; border: 1px solid var(--border); border-radius: 10px; width: 100%; margin-bottom: 10px; font-size: 0.9rem; }
        #fileList { text-align: left; margin: 10px 0; font-size: 0.75rem; }
        .file-item { background: #fff; padding: 5px 10px; border-radius: 5px; margin-bottom: 3px; display: flex; justify-content: space-between; border: 1px solid #eee; }
        
        /* Zone cases à cocher */
        .prenoms-ctn { background: #ffffff; border: 1px solid var(--border); border-radius: 10px; padding: 10px; margin-bottom: 10px; }
        .prenoms-title { font-size: 0.75rem; font-weight: 800; color: var(--txt-light); margin-bottom: 8px; text-transform: uppercase; }
        .checkbox-grid { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 8px; }
        .checkbox-item { background: #f3f4f6; padding: 6px 12px; border-radius: 20px; font-size: 0.8rem; font-weight: 700; display: flex; align-items: center; gap: 6px; cursor: pointer; user-select: none; }
        .checkbox-item input { width: auto; margin: 0; cursor: pointer; }
        .tel-inputs-flex { display: flex; gap: 8px; }
        .tel-inputs-flex input { flex: 1; margin-bottom: 0; }
    </style>
</head>
<body>

<div id="photoOverlay" class="overlay">
    <img id="fullPhoto" src="">
    <div class="overlay-btns">
        <a id="dlLink" href="#" download="" class="btn-dl">📥 TÉLÉCHARGER</a>
        <button class="btn-close" onclick="closePopups()">FERMER</button>
    </div>
</div>

<div id="docOverlay" class="overlay"><iframe id="fullDoc" style="width:95%; height:80vh; background:white; border-radius:12px;"></iframe><button onclick="closePopups()" class="btn-close" style="margin-top:10px;">FERMER</button></div>

<header>
    <img src="https://via.placeholder.com/60" alt="Logo PolyBois" class="brand-logo">
    <h1 class="brand-name">PolyBois Agencement</h1>
    <div class="nav-ctn">
        <button onclick="changeWeek(-7)" class="btn-nav">◄ PRÉC.</button>
        <button onclick="goToday()" class="btn-nav today">AUJOURD'HUI</button>
        <button onclick="changeWeek(7)" class="btn-nav">SUIV. ►</button>
    </div>
    <div id="weekRange" style="font-weight:700;">Chargement...</div>
</header>

<div id="adminArea">
    <strong id="formTitle" style="color:var(--p); display:block; margin-bottom:10px; text-align:center;">AJOUTER UN CHANTIER</strong>
    <input type="hidden" id="editId">
    
    <div class="prenoms-ctn">
        <div class="prenoms-title">Prénom(s) ouvrier(s) :</div>
        <div class="checkbox-grid" id="prenomsCheckboxes"></div>
        <input type="text" id="fPrenomExtra" placeholder="+ Ajouter un nouveau prénom" list="listPrenoms" style="margin-bottom:0;">
        <datalist id="listPrenoms"></datalist>
    </div>

    <input type="text" id="fTitre" placeholder="Nom Client">

    <input type="text" id="fAdr" placeholder="Adresse" list="listAdresses">
    <datalist id="listAdresses"></datalist>

    <div class="prenoms-ctn">
        <div class="prenoms-title">Téléphone(s) / Contact(s) :</div>
        <div class="checkbox-grid" id="telephonesCheckboxes"></div>
        <div class="tel-inputs-flex">
            <input type="text" id="fTelNameExtra" placeholder="Prénom contact">
            <input type="tel" id="fTelExtra" placeholder="Numéro de téléphone" list="listTelephones">
        </div>
        <datalist id="listTelephones"></datalist>
    </div>

    <input type="text" id="fCom" placeholder="Notes initiales">
    
    <select id="fDay">
        <option value="0">Lundi</option><option value="1">Mardi</option><option value="2">Mercredi</option><option value="3">Jeudi</option><option value="4">Vendredi</option><option value="5">Samedi</option><option value="6">Dimanche</option>
    </select>

    <div id="fileZone" style="background:#f9fafb; padding:10px; border-radius:12px; margin-bottom:10px; border:1px dashed #ccc; text-align:center;">
        <label style="cursor:pointer; font-size:0.8rem; font-weight:800; color:var(--p);">📄 AJOUTER DES DOCUMENTS <input type="file" id="fDocInit" style="display:none" multiple onchange="handleFiles(this.files)"></label>
        <div id="fileList"></div>
    </div>
    <button id="btnSubmit" onclick="ajouter()" style="background:var(--p); color:white; width:100%; border:none; padding:15px; border-radius:10px; font-weight:800; cursor:pointer;">VALIDER</button>
    <button id="btnCancel" onclick="resetForm()" style="display:none; width:100%; margin-top:5px; padding:10px; border:none; border-radius:10px; font-weight:700; cursor:pointer;">Annuler</button>
</div>

<div class="grid" id="board"></div>

<input type="file" id="photoInput" accept="image/*" capture="environment" style="display:none" onchange="uploadFile('img')">
<input type="file" id="fileInput" accept=".pdf,.doc,.docx,.xls,.xlsx" style="display:none" onchange="uploadFile('doc')">

<script>
    const firebaseConfig = { apiKey: "AIzaSyBqKxFN6W_2LzI82P4kKrKcfJqQAVh7SYs", authDomain: "planning-chantier-3f4ac.firebaseapp.com", databaseURL: "https://planning-chantier-3f4ac-default-rtdb.europe-west1.firebasedatabase.app/", projectId: "planning-chantier-3f4ac", storageBucket: "planning-chantier-3f4ac.firebasestorage.app", messagingSenderId: "73313634289", appId: "1:73313634289:web:794065392de1411e2c2650" };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
    
    let currentMonday = getMonday(new Date()), activeId = null, isAdmin = new URLSearchParams(window.location.search).get('admin') === '1', multiFiles = [], tousLesChantiers = {};

    if (isAdmin) document.getElementById('adminArea').style.display = 'block';

    function listenDatabaseAutoSuggest() {
        db.ref('base_donnees/prenoms').on('value', snap => {
            const data = snap.val() || {};
            const list = Object.values(data);
            
            const ctn = document.getElementById('prenomsCheckboxes');
            const selected = getSelectedPrenoms();
            ctn.innerHTML = list.map(p => {
                const isChecked = selected.includes(p.toUpperCase()) ? 'checked' : '';
                return `<label class="checkbox-item"><input type="checkbox" value="${p}" ${isChecked}> ${p}</label>`;
            }).join('');

            document.getElementById('listPrenoms').innerHTML = list.map(val => `<option value="${val}">`).join('');
        });
        db.ref('base_donnees/adresses').on('value', snap => {
            const data = snap.val() || {};
            document.getElementById('listAdresses').innerHTML = Object.values(data).map(val => `<option value="${val}">`).join('');
        });
        db.ref('base_donnees/telephones').on('value', snap => {
            const data = snap.val() || {};
            const list = Object.values(data);

            const ctn = document.getElementById('telephonesCheckboxes');
            const selected = getSelectedTelephones();
            ctn.innerHTML = list.map(t => {
                const valStr = typeof t === 'object' ? `${t.num}${t.nom ? ' ('+t.nom+')' : ''}` : t;
                const isChecked = selected.some(s => s.toLowerCase() === valStr.toLowerCase()) ? 'checked' : '';
                return `<label class="checkbox-item"><input type="checkbox" value="${valStr}" ${isChecked}> ${valStr}</label>`;
            }).join('');

            document.getElementById('listTelephones').innerHTML = list.map(t => {
                const valStr = typeof t === 'object' ? t.num : t;
                return `<option value="${valStr}">`;
            }).join('');
        });
    }

    function getSelectedPrenoms() {
        const checkboxes = document.querySelectorAll('#prenomsCheckboxes input[type="checkbox"]:checked');
        const selected = Array.from(checkboxes).map(cb => cb.value.trim().toUpperCase());
        const extra = document.getElementById('fPrenomExtra').value.trim();
        if (extra && !selected.includes(extra.toUpperCase())) {
            selected.push(extra.trim().toUpperCase());
        }
        return selected;
    }

    function setSelectedPrenoms(prenomString) {
        const list = prenomString ? prenomString.split(',').map(s => s.trim().toUpperCase()) : [];
        const checkboxes = document.querySelectorAll('#prenomsCheckboxes input[type="checkbox"]');
        checkboxes.forEach(cb => {
            cb.checked = list.includes(cb.value.trim().toUpperCase());
        });
        document.getElementById('fPrenomExtra').value = "";
    }

    function getSelectedTelephones() {
        const checkboxes = document.querySelectorAll('#telephonesCheckboxes input[type="checkbox"]:checked');
        const selected = Array.from(checkboxes).map(cb => cb.value.trim());
        const extraNum = document.getElementById('fTelExtra').value.trim();
        const extraName = document.getElementById('fTelNameExtra').value.trim();
        if (extraNum) {
            const fullStr = `${extraNum}${extraName ? ' (' + extraName + ')' : ''}`;
            if (!selected.includes(fullStr)) {
                selected.push(fullStr);
            }
        }
        return selected;
    }

    function setSelectedTelephones(telString) {
        const list = telString ? telString.split(',').map(s => s.trim()) : [];
        const checkboxes = document.querySelectorAll('#telephonesCheckboxes input[type="checkbox"]');
        checkboxes.forEach(cb => {
            cb.checked = list.some(l => l.toLowerCase() === cb.value.trim().toLowerCase());
        });
        document.getElementById('fTelExtra').value = "";
        document.getElementById('fTelNameExtra').value = "";
    }

    function saveToDatabase(category, value) {
        if (!value || value.trim() === "") return;
        const cleanVal = value.trim();
        const ref = db.ref('base_donnees/' + category);
        ref.once('value', snap => {
            const data = snap.val() || {};
            const exists = Object.values(data).some(v => {
                const str = typeof v === 'object' ? `${v.num}${v.nom ? ' ('+v.nom+')' : ''}` : v;
                return str.toLowerCase() === cleanVal.toLowerCase();
            });
            if (!exists) {
                ref.push(cleanVal);
            }
        });
    }

    function getMonday(d) { d = new Date(d); let day = d.getDay(), diff = d.getDate() - day + (day == 0 ? -6:1); return new Date(d.setDate(diff)); }
    function getWeekID(date) { let d = new Date(date); d.setHours(0,0,0,0); d.setDate(d.getDate() + 4 - (d.getDay() || 7)); return d.getFullYear() + "-W" + Math.ceil((((d - new Date(d.getFullYear(),0,1)) / 86400000) + 1) / 7); }
    function changeWeek(days) { currentMonday.setDate(currentMonday.getDate() + days); updateCalendar(); }
    function goToday() { currentMonday = getMonday(new Date()); updateCalendar(); }

    function handleFiles(files) {
        for (let file of files) {
            const reader = new FileReader();
            reader.onload = (e) => {
                multiFiles.push({ name: file.name, data: e.target.result });
                updateFileList();
            };
            reader.readAsDataURL(file);
        }
    }

    function updateFileList() {
        const list = document.getElementById('fileList');
        list.innerHTML = multiFiles.map((f, i) => `<div class="file-item"><span>${f.name}</span><span style="color:red; cursor:pointer;" onclick="multiFiles.splice(${i},1);updateFileList()">✖</span></div>`).join('');
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
        const sorted = tasks.sort((a,b) => (a.ord||0)-(b.ord||0));
        sorted.forEach((t) => {
            const list = document.getElementById('list-' + t.day);
            if(!list) return;
            let imgs = ""; if(t.photos) Object.keys(t.photos).forEach(k => imgs += `<img src="${t.photos[k]}" style="width:60px;height:60px;object-fit:cover;border-radius:8px;margin:5px 5px 0 0;cursor:pointer;" onclick="openPhoto('${t.id}','${k}')">`);
            let docs = ""; if(t.files) Object.keys(t.files).forEach(k => docs += `<div style="background:#e0f2fe; padding:8px; border-radius:8px; font-size:0.75rem; margin-top:8px; cursor:pointer; font-weight:800;" onclick="openDoc('${t.id}','${k}')">📄 ${t.files[k].name}</div>`);
            let pbs = (t.reports) ? `<div style="background:var(--d-light); padding:8px; border-radius:8px; margin-top:10px; font-size:0.75rem; color:#9f1239;"><strong>PB :</strong>` + Object.values(t.reports).map(r => `<div>• ${r}</div>`).join('') + `</div>` : "";
            let notesSalaries = (t.notes) ? `<div class="badge-note"><strong>Compte-rendu :</strong>` + Object.values(t.notes).map(n => `<div>• ${n}</div>`).join('') + `</div>` : "";

            let timeBadge = "";
            if (t.startTime || t.endTime) {
                timeBadge = `<div class="badge-time">`;
                if(t.startTime) timeBadge += `<div>🚀 <strong>Début :</strong> ${t.startTime}</div>`;
                if(t.endTime) timeBadge += `<div>🏁 <strong>Fin :</strong> ${t.endTime}</div>`;
                timeBadge += `</div>`;
            }

            const rawTel = (t.tel || '').split(',')[0].trim();
            const cleanCallTel = rawTel.replace(/\([^)]*\)/g, '').trim();

            const card = document.createElement('div');
            card.className = `card ${t.status}`;
            card.innerHTML = `<div class="reorder-btns"><button class="btn-re" onclick="move('${t.id}', -1)">▲</button><button class="btn-re" onclick="move('${t.id}', 1)">▼</button></div>
                <span class="prenom">${t.prenom}</span><span class="title">${t.titre}</span><div class="adr">📍 ${t.adr}</div>
                ${t.tel ? `<div style="font-size:0.8rem; font-weight:700; color:var(--txt-light); margin-bottom:8px;">📞 ${t.tel}</div>` : ''}
                <div class="contact-btns"><a href="waze://?q=${encodeURIComponent(t.adr)}&navigate=yes" class="btn-c waze">GPS</a><a href="tel:${cleanCallTel}" class="btn-c call">APPEL</a></div>
                ${t.com ? `<div style="font-size:0.75rem; background:#f3f4f6; padding:8px; border-radius:8px;"><strong>Note Admin:</strong> ${t.com}</div>` : ''}
                ${notesSalaries}
                ${timeBadge}
                <div style="margin-top:10px;">${imgs}</div> ${docs} ${pbs}
                <div class="footer-btns">
                    <button class="btn-f btn-start" onclick="setChantierTime('${t.id}', 'start', '${t.titre}')">DÉBUT</button>
                    <button class="btn-f btn-end" onclick="setChantierTime('${t.id}', 'end', '${t.titre}')">FIN</button>
                    <button class="btn-f cam" onclick="triggerUpload('${t.id}', 'img')">PHOTO</button>
                    <button class="btn-f note" onclick="addNote('${t.id}', '${t.titre}')">NOTE</button>
                    <button class="btn-f pb" onclick="addPb('${t.id}', '${t.titre}')">PB</button>
                    <button class="btn-f admin-btn" onclick="triggerUpload('${t.id}', 'doc')">📂 DOC</button>
                    ${isAdmin ? `<button class="btn-f admin-btn" onclick="startEdit('${t.id}')">EDIT</button>` : ''}
                </div>
                ${isAdmin ? `<button onclick="delT('${t.id}')" style="margin-top:8px; color:var(--d); border:none; background:none; font-size:0.65rem; cursor:pointer; width:100%; text-align:right;">SUPPRIMER</button>` : ''}`;
            list.appendChild(card);
        });
    }

    function setChantierTime(id, type, titre) {
        const now = new Date();
        const dateStr = now.toLocaleDateString('fr-FR');
        const timeStr = now.toLocaleTimeString('fr-FR', {hour:'2-digit', minute:'2-digit'});
        const fullStamp = `${dateStr} à ${timeStr}`;

        if (type === 'start') {
            db.ref('chantiers/' + id).update({ startTime: fullStamp });
            sendNotif("DÉBUT DE CHANTIER - " + titre, `Intervention démarrée à ${timeStr} le ${dateStr}`);
        } else if (type === 'end') {
            db.ref('chantiers/' + id).update({ endTime: fullStamp });
            sendNotif("FIN DE CHANTIER - " + titre, `Intervention terminée à ${timeStr} le ${dateStr}`);
        }
    }

    function ajouter() {
        const id = document.getElementById('editId').value;
        const selectedPrenoms = getSelectedPrenoms();
        const prenomVal = selectedPrenoms.join(', ');

        const selectedTelephones = getSelectedTelephones();
        const telVal = selectedTelephones.join(', ');

        const adrVal = document.getElementById('fAdr').value;

        selectedPrenoms.forEach(p => saveToDatabase('prenoms', p));
        selectedTelephones.forEach(t => saveToDatabase('telephones', t));
        saveToDatabase('adresses', adrVal);

        const data = { 
            prenom: prenomVal, titre: document.getElementById('fTitre').value, 
            adr: adrVal, tel: telVal, 
            com: document.getElementById('fCom').value, day: document.getElementById('fDay').value,
            weekID: getWeekID(currentMonday)
        };
        if(id) { db.ref('chantiers/'+id).update(data).then(() => resetForm()); } 
        else {
            const ref = db.ref('chantiers').push();
            data.id = ref.key; data.status = 'pending'; data.ord = Date.now();
            ref.set(data).then(() => { 
                if(multiFiles.length > 0) {
                    multiFiles.forEach(f => db.ref('chantiers/'+data.id+'/files').push(f));
                }
                resetForm(); 
            });
        }
    }

    function resetForm() {
        document.getElementById('editId').value = "";
        ["fTitre","fAdr","fCom","fPrenomExtra","fTelExtra","fTelNameExtra"].forEach(i => document.getElementById(i).value="");
        setSelectedPrenoms("");
        setSelectedTelephones("");
        document.getElementById('formTitle').innerText = "AJOUTER UN CHANTIER";
        document.getElementById('btnSubmit').innerText = "VALIDER";
        document.getElementById('btnCancel').style.display = "none";
        document.getElementById('fileZone').style.display = "block";
        document.getElementById('fileList').innerHTML = "";
        multiFiles = [];
    }

    function openPhoto(id, k) { const t = tousLesChantiers[id]; const dataUrl = t.photos[k]; const fileName = `${t.titre.replace(/[^a-z0-req0-9]/gi, '_')}_${new Date().toLocaleDateString('fr-FR').replace(/\//g, '-')}.jpg`; document.getElementById('fullPhoto').src = dataUrl; document.getElementById('dlLink').href = dataUrl; document.getElementById('dlLink').download = fileName; document.getElementById('photoOverlay').style.display='flex'; }
    function move(id, direction) { const task = tousLesChantiers[id]; const sameDay = Object.values(tousLesChantiers).filter(t => t.day == task.day).sort((a,b) => (a.ord||0)-(b.ord||0)); const idx = sameDay.findIndex(t => t.id === id); const newIdx = idx + direction; if (newIdx >= 0 && newIdx < sameDay.length) { const swapTask = sameDay[newIdx]; const tempOrd = task.ord || 0; db.ref('chantiers/'+task.id).update({ ord: swapTask.ord || 0 }); db.ref('chantiers/'+swapTask.id).update({ ord: tempOrd }); } }
    function sendNotif(titre, msg) { fetch('https://ntfy.sh/Alerte_chantier', { method: 'POST', body: titre + "\n" + msg }); }
    function setStatus(id, s, t) { db.ref('chantiers/'+id).update({status:s}); if(s==='done') sendNotif("CHANTIER TERMINE", t); }
    function addPb(id, t) { let m = prompt("Problème ?"); if(m) { let time = new Date().toLocaleTimeString('fr-FR', {hour:'2-digit', minute:'2-digit'}); db.ref('chantiers/'+id+'/reports').push(time + " : " + m); db.ref('chantiers/'+id).update({status:'issue'}); sendNotif("ALERTE PB - " + t, m); } }
    function addNote(id, t) { let m = prompt("Qu'avez-vous fait ?"); if(m) { let time = new Date().toLocaleTimeString('fr-FR', {hour:'2-digit', minute:'2-digit'}); db.ref('chantiers/'+id+'/notes').push(time + " : " + m); sendNotif("NOTE - " + t, m); } }
    function openDoc(id, k) { let f = tousLesChantiers[id].files[k]; document.getElementById('fullDoc').src = f.data; document.getElementById('docOverlay').style.display='flex'; }
    function closePopups() { document.querySelectorAll('.overlay').forEach(o => o.style.display='none'); document.getElementById('fullDoc').src=""; }
    function triggerUpload(id, type) { activeId = id; document.getElementById(type === 'img' ? 'photoInput' : 'fileInput').click(); }
    function startEdit(id) { const t = tousLesChantiers[id]; document.getElementById('editId').value = id; setSelectedPrenoms(t.prenom || ""); setSelectedTelephones(t.tel || ""); document.getElementById('fTitre').value = t.titre || ""; document.getElementById('fAdr').value = t.adr || ""; document.getElementById('fCom').value = t.com || ""; document.getElementById('fDay').value = t.day; document.getElementById('formTitle').innerText = "MODIFIER LE CHANTIER"; document.getElementById('btnSubmit').innerText = "METTRE À JOUR"; document.getElementById('btnCancel').style.display = "block"; document.getElementById('fileZone').style.display = "none"; window.scrollTo({ top: 0, behavior: 'smooth' }); }
    function uploadFile(mode) { const file = document.getElementById(mode === 'img' ? 'photoInput' : 'fileInput').files[0]; if(!file) return; const reader = new FileReader(); reader.onload = (e) => { if(mode === 'img') { const img = new Image(); img.onload = () => { const canvas = document.createElement('canvas'); const MAX = 1200; let w = img.width, h = img.height; if (w > h && w > MAX) { h *= MAX/w; w = MAX; } else if (h > MAX) { w *= MAX/h; h = MAX; } canvas.width = w; canvas.height = h; canvas.getContext('2d').drawImage(img, 0,0,w,h); db.ref('chantiers/'+activeId+'/photos').push(canvas.toDataURL('image/jpeg', 0.8)); }; img.src = e.target.result; } else { db.ref('chantiers/'+activeId+'/files').push({name:file.name, data:e.target.result}); } }; reader.readAsDataURL(file); }
    function delT(id) { if(confirm("Supprimer ?")) db.ref('chantiers/'+id).remove(); }
    
    listenDatabaseAutoSuggest();
    updateCalendar();
</script>
</body>
</html>
