<!DOCTYPE html>
<html lang="uk">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Адреси: дах, ключ, драбина</title>
<style>
:root{--bg:#f3f4f7;--card:#fff;--ink:#1a1d29;--mute:#667085;--line:#dfe3ea;--acc:#4527d8;--acc-ink:#fff;--warn:#b54708;--warn-bg:#fff1e0;--ok:#0b7a53;--ok-bg:#dff6ec}
@media(prefers-color-scheme:dark){:root{--bg:#12141c;--card:#1c1f2b;--ink:#eef0f6;--mute:#98a0b3;--line:#2c3040;--acc:#8b78ff;--acc-ink:#12141c;--warn:#ffb166;--warn-bg:#3a2a16;--ok:#5fd6a5;--ok-bg:#15332a}}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--ink);font:16px/1.45 system-ui,-apple-system,"Segoe UI",Roboto,sans-serif}
main{max-width:760px;margin:0 auto;padding:16px 14px 90px}
h1{font-size:1.35rem;margin:6px 0 14px}
.bar{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px}
input,textarea,button{font:inherit;color:inherit}
.search{flex:1 1 100%;padding:11px 12px;border:1px solid var(--line);border-radius:10px;background:var(--card)}
.tab{padding:8px 14px;border:1px solid var(--line);border-radius:999px;background:var(--card);cursor:pointer}
.tab[aria-pressed=true]{background:var(--acc);color:var(--acc-ink);border-color:var(--acc)}
.item{background:var(--card);border:1px solid var(--line);border-radius:12px;padding:14px;margin-bottom:10px}
.item h2{font-size:1.05rem;margin:0 0 8px}
.row{display:flex;gap:8px;margin:3px 0}
.row b{min-width:92px;color:var(--mute);font-weight:500}
.badge{display:inline-block;padding:2px 10px;border-radius:999px;font-size:.85rem}
.badge.yes{background:var(--warn-bg);color:var(--warn)}.badge.no{background:var(--ok-bg);color:var(--ok)}
.meta{margin-top:8px;font-size:.82rem;color:var(--mute);display:flex;justify-content:space-between;align-items:center;gap:8px}
.link{background:none;border:0;color:var(--acc);cursor:pointer;padding:6px 0}
.fab{position:fixed;right:16px;bottom:16px;padding:14px 20px;border:0;border-radius:999px;background:var(--acc);color:var(--acc-ink);font-weight:600;box-shadow:0 4px 14px rgba(0,0,0,.25);cursor:pointer}
dialog{border:0;border-radius:14px;padding:18px;width:min(520px,94vw);background:var(--card);color:var(--ink)}
dialog::backdrop{background:rgba(0,0,0,.5)}
label{display:block;margin:12px 0 4px;font-size:.92rem}
.f{width:100%;padding:10px 12px;border:1px solid var(--line);border-radius:8px;background:var(--bg)}
.chk{display:flex;gap:8px;align-items:center;margin-top:14px}.chk input{width:20px;height:20px}
.btns{display:flex;gap:8px;margin-top:18px}
.btns button{flex:1;padding:12px;border-radius:10px;border:1px solid var(--line);background:var(--card);cursor:pointer}
.btns .ok{background:var(--acc);color:var(--acc-ink);border-color:var(--acc);font-weight:600}
.note{padding:12px;border-radius:10px;background:var(--warn-bg);color:var(--warn);margin-bottom:12px}
.empty{text-align:center;color:var(--mute);padding:36px 0}
:focus-visible{outline:3px solid var(--acc);outline-offset:2px}
</style>
</head>
<body>
<main>
  <h1>🏢 Адреси: дах, ключ, драбина</h1>
  <div id="note" class="note" hidden></div>
  <div class="bar">
    <input id="q" class="search" type="search" placeholder="Пошук за адресою або коментарем" aria-label="Пошук">
    <button class="tab" data-f="all" aria-pressed="true">Усі</button>
    <button class="tab" data-f="yes" aria-pressed="false">Потрібна драбина</button>
    <button class="tab" data-f="no" aria-pressed="false">Без драбини</button>
  </div>
  <div id="list"></div>
</main>
<button class="fab" id="add">+ Додати адресу</button>

<dialog id="dlg">
  <form id="form" method="dialog">
    <h2 id="dtitle" style="margin:0 0 4px">Нова адреса</h2>
    <label for="address">Адреса *</label>
    <input class="f" id="address" required maxlength="150" placeholder="вул. Шевченка 12, під'їзд 2">
    <label for="roof">Де вихід на дах</label>
    <input class="f" id="roof" maxlength="300">
    <label for="key">Де взяти ключ</label>
    <input class="f" id="key" maxlength="300">
    <div class="chk"><input type="checkbox" id="ladder"><label for="ladder" style="margin:0">Потрібна драбина</label></div>
    <label for="note2">Де драбина / коментар</label>
    <textarea class="f" id="note2" rows="3" maxlength="500"></textarea>
    <label for="author">Ваше ім'я (щоб колеги знали, хто додав)</label>
    <input class="f" id="author" maxlength="40">
    <div class="btns">
      <button type="button" id="cancel">Скасувати</button>
      <button type="submit" class="ok">Зберегти</button>
    </div>
  </form>
</dialog>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, updateDoc, doc, onSnapshot, query, orderBy, serverTimestamp }
  from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

/* ВСТАВТЕ СВІЙ firebaseConfig З КОНСОЛІ FIREBASE */
const firebaseConfig = {
  apiKey: "ВАШ_API_KEY",
  authDomain: "ВАШ_ПРОЄКТ.firebaseapp.com",
  projectId: "ВАШ_ПРОЄКТ",
  appId: "ВАШ_APP_ID"
};

const $ = s => document.querySelector(s);
const note = $("#note"), listEl = $("#list"), dlg = $("#dlg");
let items = [], filter = "all", editId = null, db = null;

const configured = !firebaseConfig.apiKey.startsWith("ВАШ");
if (!configured) {
  note.hidden = false;
  note.textContent = "База не підключена: вставте firebaseConfig у index.html (див. інструкцію).";
} else {
  db = getFirestore(initializeApp(firebaseConfig));
  onSnapshot(query(collection(db, "addresses"), orderBy("createdAt", "desc")),
    snap => { items = snap.docs.map(d => ({ id: d.id, ...d.data() })); render(); },
    err => { note.hidden = false; note.textContent = "Не вдалося завантажити дані: " + err.message; });
}

const esc = s => String(s ?? "").replace(/[&<>"']/g, c => ({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
const fmt = t => t?.toDate ? t.toDate().toLocaleDateString("uk-UA") : "щойно";

function render() {
  const s = $("#q").value.trim().toLowerCase();
  const shown = items.filter(i =>
    (filter === "all" || (filter === "yes") === !!i.ladder) &&
    (!s || [i.address, i.roof, i.key, i.note].join(" ").toLowerCase().includes(s)));
  if (!shown.length) {
    listEl.innerHTML = `<div class="empty">${items.length ? "Нічого не знайдено." : "Адрес ще немає. Додайте першу кнопкою внизу."}</div>`;
    return;
  }
  listEl.innerHTML = shown.map(i => `
    <article class="item">
      <h2>${esc(i.address)}</h2>
      ${i.roof ? `<div class="row"><b>Дах</b><span>${esc(i.roof)}</span></div>` : ""}
      ${i.key ? `<div class="row"><b>Ключ</b><span>${esc(i.key)}</span></div>` : ""}
      <div class="row"><b>Драбина</b><span><span class="badge ${i.ladder ? "yes" : "no"}">${i.ladder ? "потрібна" : "не потрібна"}</span></span></div>
      ${i.note ? `<div class="row"><b>Коментар</b><span>${esc(i.note)}</span></div>` : ""}
      <div class="meta"><span>${esc(i.author || "хтось із колег")} · ${fmt(i.createdAt)}</span>
      <button class="link" data-edit="${i.id}">Змінити</button></div>
    </article>`).join("");
}

document.querySelectorAll(".tab").forEach(b => b.onclick = () => {
  filter = b.dataset.f;
  document.querySelectorAll(".tab").forEach(x => x.setAttribute("aria-pressed", x === b));
  render();
});
$("#q").oninput = render;

function openForm(item) {
  editId = item?.id ?? null;
  $("#dtitle").textContent = item ? "Змінити адресу" : "Нова адреса";
  $("#address").value = item?.address ?? "";
  $("#roof").value = item?.roof ?? "";
  $("#key").value = item?.key ?? "";
  $("#ladder").checked = !!item?.ladder;
  $("#note2").value = item?.note ?? "";
  $("#author").value = item?.author ?? localStorage.getItem("author") ?? "";
  dlg.showModal();
}
$("#add").onclick = () => openForm();
$("#cancel").onclick = () => dlg.close();
listEl.onclick = e => {
  const id = e.target.dataset.edit;
  if (id) openForm(items.find(i => i.id === id));
};

$("#form").onsubmit = async e => {
  e.preventDefault();
  if (!db) { alert("Спочатку підключіть Firebase (firebaseConfig)."); return; }
  const data = {
    address: $("#address").value.trim(),
    roof: $("#roof").value.trim(),
    key: $("#key").value.trim(),
    ladder: $("#ladder").checked,
    note: $("#note2").value.trim(),
    author: $("#author").value.trim()
  };
  if (!data.address) return;
  localStorage.setItem("author", data.author);
  try {
    if (editId) await updateDoc(doc(db, "addresses", editId), { ...data, updatedAt: serverTimestamp() });
    else await addDoc(collection(db, "addresses"), { ...data, createdAt: serverTimestamp() });
    dlg.close();
  } catch (err) { alert("Не збереглося: " + err.message); }
};
render();
</script>
</body>
</html>
