# agrosmart
Agrosmart
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>AgroSmart Tanjung</title>
<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#1b5e20">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
body { margin:0; font-family:Arial; background:#eef2f1; }
header { background:#1b5e20; color:white; padding:15px; text-align:center; }
nav { display:flex; background:white; }
nav button { flex:1; padding:12px; border:none; background:#e8f5e9; }
section { display:none; padding:15px; }
section.active { display:block; }
.card { background:white; padding:15px; border-radius:12px; margin-bottom:15px; box-shadow:0 2px 8px rgba(0,0,0,0.1); }
input, textarea { width:100%; padding:8px; margin:6px 0; }
button.primary { background:#2e7d32; color:white; border:none; padding:10px; border-radius:8px; }
</style>
</head>
<body>

<header>
<h2>🌾 AgroSmart Tanjung</h2>
<small>Monitoring & Produksi Pertanian Offline</small>
</header>

<nav>
<button onclick="show('dash')">Dashboard</button>
<button onclick="show('panen')">Panen</button>
<button onclick="show('edukasi')">Edukasi</button>
</nav>

<section id="dash" class="active">
<div class="card">
<h3>Ringkasan Produksi</h3>
<p>Total Produksi: <b id="totalKg">0</b> kg</p>
<p>Total Omzet: Rp<b id="totalRp">0</b></p>
</div>
<div class="card">
<canvas id="chart"></canvas>
</div>
</section>

<section id="panen">
<div class="card">
<h3>Input Panen</h3>
<input id="komoditas" placeholder="Komoditas">
<input id="jumlah" type="number" placeholder="Jumlah (kg)">
<input id="harga" type="number" placeholder="Harga per kg">
<button class="primary" onclick="simpan()">Simpan</button>
</div>
<div class="card">
<h4>Riwayat</h4>
<div id="list"></div>
</div>
</section>

<section id="edukasi">
<div class="card">
<h3>Modul Bioflok</h3>
<p>• Aerasi 24 jam wajib stabil.</p>
<p>• Rasio C/N ideal 10-15.</p>
</div>
<div class="card">
<h3>Modul Pertanian</h3>
<p>• Sistem surjan meningkatkan efisiensi air.</p>
<p>• Gunakan pupuk NPK seimbang.</p>
</div>
</section>

<script>
function show(id){
document.querySelectorAll("section").forEach(s=>s.classList.remove("active"));
document.getElementById(id).classList.add("active");
}

let chart;

function simpan(){
let total = jumlah.value * harga.value;
let data = {komoditas:komoditas.value, jumlah:+jumlah.value, total:+total};
let list = JSON.parse(localStorage.getItem("panen")||"[]");
list.push(data);
localStorage.setItem("panen", JSON.stringify(list));
tampil(); grafik();
}

function tampil(){
let list = JSON.parse(localStorage.getItem("panen")||"[]");
list.innerHTML = list.map(d=>`<p>${d.komoditas} - ${d.jumlah}kg</p>`).join("");
}

function ringkasan(){
let list = JSON.parse(localStorage.getItem("panen")||"[]");
let totalKg = list.reduce((a,b)=>a+b.jumlah,0);
let totalRp = list.reduce((a,b)=>a+b.total,0);
totalKg.innerText = totalKg;
totalRp.innerText = totalRp;
}

function grafik(){
let list = JSON.parse(localStorage.getItem("panen")||"[]");
let labels = list.map(d=>d.komoditas);
let data = list.map(d=>d.jumlah);

if(chart) chart.destroy();

chart = new Chart(document.getElementById("chart"), {
type:"bar",
data:{labels:labels, datasets:[{label:"Produksi (kg)", data:data}]}
});
ringkasan();
}

tampil(); grafik();

if ('serviceWorker' in navigator) {
navigator.serviceWorker.register('service-worker.js');
}
</script>

</body>
</html>{
"name": "AgroSmart Tanjung",
"short_name": "AgroSmart",
"start_url": "index.html",
"display": "standalone",
"background_color": "#eef2f1",
"theme_color": "#1b5e20",
"icons": [
{
"src": "icon.png",
"sizes": "192x192",
"type": "image/png"
}
]
}self.addEventListener("install", e => {
e.waitUntil(
caches.open("agro").then(cache =>
cache.addAll(["index.html"])
)
);
});
