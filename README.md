<html lang="uk">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Адреси: дах, ключі, драбина</title>
<style>
:root{box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px);
--bg:#f4f6fa;--card:#fff;--text:#17202e;--muted:#667085;--line:#dde2ea;--accent:#1565d8;--accent-t:#fff;--warn:#c2410c;--warn-bg:#fff1e6;--ok:#15803d;--ok-bg:#e8f7ee}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#10141b;--card:#1a212c;--text:#e8edf5;--muted:#93a0b4;--line:#2b3442;--accent:#5a9bff;--accent-t:#0b1220;--warn:#fdba74;--warn-bg:#3a2415;--ok:#86efac;--ok-bg:#14301f}}
:root[data-theme="dark"]{--bg:#10141b;--card:#1a212c;--text:#e8edf5;--muted:#93a0b4;--line:#2b3442;--accent:#5a9bff;--accent-t:#0b1220;--warn:#fdba74;--warn-bg:#3a2415;--ok:#86efac;--ok-bg:#14301f}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--text);font:16px/1.4 system-ui,-apple-system,"Segoe UI",Roboto,sans-serif}
header{position:sticky;top:env(safe-area-inset-top,0px);background:var(--bg);padding:12px 14px 8px;z-index:5;border-bottom:1px solid var(--line)}
h1{font-size:18px;margin:0 0 8px}
.row{display:flex;gap:8px;flex-wrap:wrap}
input,textarea,select{width:100%;padding:10px 12px;border:1px solid var(--line);border-radius:10px;background:var(--card);color:var(--text);font:inherit}
textarea{min-height:64px;resize:vertical}
button{padding:10px 14px;border:0;border-radius:10px;background:var(--accent);color:var(--accent-t);font:inherit;font-weight:600;cursor:pointer}
button.ghost{background:transparent;color:var(--accent);border:1px solid var(--line)}
button.danger{background:transparent;color:var(--warn);border:1px solid var(--line)}
.search{flex:1;min-width:160px}
main{padding:10px 10px 80px;max-width:1200px;margin:0 auto}
#list{display:grid;grid-template-columns:repeat(auto-fill,minmax(170px,1fr));gap:10px}
.empty{grid-column:1/-1}
.card{background:var(--card);border:1px solid var(--line);border-radius:14px;padding:10px;display:flex;flex-direction:column;min-height:180px;min-width:0}
.addr{font-weight:700;font-size:15px;overflow-wrap:anywhere}
.sub{color:var(--muted);font-size:14px}
.item{margin-top:6px;font-size:14px;line-height:1.3;overflow-wrap:anywhere}
.item b{display:block;font-size:12px;color:var(--muted);text-transform:uppercase;letter-spacing:.04em}
.badge{display:inline-block;padding:2px 8px;border-radius:99px;font-size:13px;font-weight:600;margin-top:6px}
.badge.warn{background:var(--warn-bg);color:var(--warn)}
.badge.ok{background:var(--ok-bg);color:var(--ok)}
.actions{display:flex;gap:6px;margin-top:auto;padding-top:8px}
.actions button{flex:1;padding:6px 8px;font-size:13px}
.empty,.note{color:var(--muted);text-align:center;padding:24px 8px;font-size:14px}
dialog{border:1px solid var(--line);border-radius:16px;background:var(--bg);color:var(--text);width:min(560px,94vw);padding:16px;max-height:92vh;overflow:auto}
dialog::backdrop{background:rgba(0,0,0,.5)}
label{display:block;font-size:13px;color:var(--muted);margin:10px 0 4px}
.fab{position:fixed;right:16px;bottom:calc(16px + env(safe-area-inset-bottom,0px));border-radius:99px;padding:14px 20px;box-shadow:0 4px 14px rgba(0,0,0,.3)}
.chk{display:flex;align-items:center;gap:10px;margin-top:12px}
.chk input{width:22px;height:22px}
</style>
</head>
<body>
<header>
  <h1>🏢 Адреси: дах · ключ · драбина</h1>
  <div class="row">
    <input class="search" id="q" type="search" placeholder="Пошук за адресою, вулицею…">
    <select id="f" style="width:auto"><option value="">Усі</option><option value="ladder">Потрібна драбина</option><option value="noladder">Без драбини</option></select>
  </div>
</header>
<main>
  <div id="status" class="note"></div>
  <div id="list"></div>
</main>
<button class="fab" id="add">+ Додати адресу</button>

<dialog id="dlg">
  <h2 id="dt" style="margin:0 0 4px;font-size:18px">Адреса</h2>
  <label>Адреса *</label><input id="a" placeholder="вул. Шевченка 12, під'їзд 2">
  <label>Де вихід на дах</label><textarea id="r" placeholder="Напр.: 9 поверх, двері за ліфтом, люк"></textarea>
  <label>Де взяти ключ</label><textarea id="k" placeholder="Напр.: кв. 5, пані Оксана, тел. … / ОСББ / охорона"></textarea>
  <div class="chk"><input type="checkbox" id="l"><span>Потрібна драбина</span></div>
  <label>Де драбина / коментар</label><textarea id="n" placeholder="Напр.: люк високо, потрібна драбина 3 м"></textarea>
  <div class="row" style="margin-top:14px">
    <button id="save">Зберегти</button>
    <button class="ghost" id="cancel">Скасувати</button>
  </div>
  <div id="err" class="sub" style="color:var(--warn);margin-top:8px"></div>
</dialog>

<script>
const $ = id => document.getElementById(id);
let rows = [], editId = null, col = null, uid = '', canWrite = true, local = false;

function render(){
  const q = $('q').value.trim().toLowerCase(), f = $('f').value;
  const list = rows.filter(r => {
    if (f==='ladder' && !r.ladder) return false;
    if (f==='noladder' && r.ladder) return false;
    return !q || [r.address,r.roof,r.key,r.note].join(' ').toLowerCase().includes(q);
  }).sort((a,b)=>(a.address||'').localeCompare(b.address||'','uk'));
  const el = $('list'); el.textContent='';
  if (!list.length){ const d=document.createElement('div'); d.className='empty'; d.textContent=rows.length?'Нічого не знайдено':'Поки немає адрес. Натисніть «+ Додати адресу».'; el.append(d); return; }
  for (const r of list){
    const c=document.createElement('div'); c.className='card';
    const add=(cls,txt)=>{const d=document.createElement('div');d.className=cls;d.textContent=txt;c.append(d);return d;};
    add('addr',r.address);
    const b=document.createElement('span'); b.className='badge '+(r.ladder?'warn':'ok'); b.textContent=r.ladder?'🪜 Потрібна драбина':'Драбина не потрібна'; c.append(b);
    for (const [lab,val] of [['Вихід на дах',r.roof],['Ключ',r.key],['Коментар',r.note]]){
      if(!val) continue; const it=document.createElement('div'); it.className='item';
      const bb=document.createElement('b'); bb.textContent=lab; const s=document.createElement('span'); s.textContent=val; it.append(bb,s); c.append(it);
    }
    if (r.updated) add('sub','Оновлено: '+new Date(r.updated).toLocaleDateString('uk-UA')).style.marginTop='8px';
    const ac=document.createElement('div'); ac.className='actions';
    const e=document.createElement('button'); e.className='ghost'; e.textContent='Редагувати'; e.onclick=()=>openDlg(r);
    const x=document.createElement('button'); x.className='danger'; x.textContent='Видалити'; x.onclick=()=>del(r);
    ac.append(e,x); c.append(ac); el.append(c);
  }
}
function openDlg(r){
  editId = r?r.id:null; $('dt').textContent = r?'Редагувати адресу':'Нова адреса'; $('err').textContent='';
  $('a').value=r?.address||''; $('r').value=r?.roof||''; $('k').value=r?.key||''; $('l').checked=!!r?.ladder; $('n').value=r?.note||'';
  $('dlg').showModal();
}
async function save(){
  const data={address:$('a').value.trim(),roof:$('r').value.trim(),key:$('k').value.trim(),ladder:$('l').checked,note:$('n').value.trim(),updated:Date.now(),by:uid};
  if(!data.address){ $('err').textContent='Вкажіть адресу.'; return; }
  try{
    if (local){
      if(editId){ rows=rows.map(r=>r.id===editId?{...data,id:editId}:r); } else rows.push({...data,id:'l'+Date.now()});
      try{localStorage.setItem('addr',JSON.stringify(rows));}catch(e){}
      render();
    } else if (editId) await col.doc(editId).set(data); else await col.add(data);
    $('dlg').close();
  }catch(e){ $('err').textContent='Не вдалося зберегти (немає прав запису або помилка мережі).'; }
}
async function del(r){
  if(!confirm('Видалити «'+r.address+'»?')) return;
  try{
    if(local){ rows=rows.filter(x=>x.id!==r.id); try{localStorage.setItem('addr',JSON.stringify(rows));}catch(e){} render(); }
    else await col.doc(r.id).delete();
  }catch(e){ alert('Не вдалося видалити.'); }
}
$('add').onclick=()=>openDlg(null); $('save').onclick=save; $('cancel').onclick=()=>$('dlg').close();
$('q').oninput=render; $('f').onchange=render;

(async function init(){
  render();
  let db=null, user=null;
  try{ db=await claude.use('db'); user=await claude.use('user'); }catch(e){}
  if(user){ try{ uid=await user.id(); canWrite = (user.can && user.can('data.write'))!==false; }catch(e){} }
  if(!db){
    local=true; $('status').textContent='⚠️ Спільна база недоступна — дані зберігаються лише на цьому пристрої.';
    try{ rows=JSON.parse(localStorage.getItem('addr')||'[]'); }catch(e){ rows=[]; }
    render(); return;
  }
  if(!canWrite){ $('add').style.display='none'; $('status').textContent='Режим перегляду: додавати адреси можуть учасники з правом запису.'; }
  col=db.collection('addresses');
  col.onSnapshot(s=>{ rows=s.docs.map(d=>({...d.data(),id:d.id})); render(); },
    ()=>{ $('status').textContent='Не вдалося завантажити дані. Увійдіть в акаунт і оновіть сторінку.'; });
})();
</script>
</body>
</html>
