<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Absensi Checklist Siswa</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      padding: 10px;
      background: linear-gradient(135deg, #0f0c29, #302b63, #ff0080);
      background-size: 400% 400%;
      animation: gradient 5s ease infinite;
      margin: 0;
    }

    @keyframes gradient {
      0% {background-position: 0% 50%;}
      50% {background-position: 100% 50%;}
      100% {background-position: 0% 50%;}
    }

    h2 {
      text-align: center;
      color: white;
    }

    .input-hari-tanggal {
      display: flex;
      justify-content: center;
      gap: 10px;
      margin-bottom: 10px;
      flex-wrap: wrap;
    }

    .input-hari-tanggal input {
      padding: 6px;
      border-radius: 5px;
      border: 1px solid #ccc;
      font-size: 14px;
    }

    .tanggal {
      text-align: center;
      font-size: 16px;
      margin-bottom: 15px;
      color: #444;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      font-size: 14px;
      background: #fff;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

    th, td {
      border: 1px solid #ccc;
      padding: 8px;
      text-align: center;
    }

    th {
      background-color: #673ab7;
      color: white;
      font-size: 13px;
    }

    input[type="checkbox"] {
      transform: scale(1.3);
    }

    .summary {
      font-size: 14px;
      text-align: center;
      margin-top: 15px;
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      justify-content: center;
    }

    .summary span {
      padding: 5px 10px;
      border-radius: 6px;
    }

    .summary .hadir { background-color: #d0f0c0; color: green; }
    .summary .sakit { background-color: #ffe0b2; color: orange; }
    .summary .izin  { background-color: #bbdefb; color: #0d47a1; }
    .summary .alpha { background-color: #ffcdd2; color: red; }

    @media (max-width: 600px) {
      table, thead, tbody, th, td, tr {
        font-size: 12px;
      }

      input[type="checkbox"] {
        transform: scale(1.1);
      }

      .summary {
        font-size: 13px;
        flex-direction: column;
        align-items: center;
      }

      .input-hari-tanggal {
        flex-direction: column;
        align-items: center;
      }
    }
  </style>
</head>
<body>
  <h2>Daftar Absensi Siswa</h2>

  <div class="input-hari-tanggal">
    <input type="text" id="inputHari" placeholder="Isi Hari Manual (misal: Senin)">
    <input type="date" id="inputTanggal">
  </div>

  <div class="tanggal" id="tanggalNow">
    <!-- Ditampilkan otomatis jika tidak diisi -->
  </div>

  <div style="overflow-x: auto;">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Nama Siswa</th>
          <th>Hadir</th>
          <th>Sakit</th>
          <th>Izin</th>
          <th>Alpha</th>
        </tr>
      </thead>
      <tbody id="absenBody"></tbody>
    </table>
  </div>

  <div class="summary">
    <span class="hadir">Hadir: <span id="hadirCount">0</span></span>
    <span class="sakit">Sakit: <span id="sakitCount">0</span></span>
    <span class="izin">Izin: <span id="izinCount">0</span></span>
    <span class="alpha">Alpha: <span id="alphaCount">0</span></span>
  </div>

<div style="text-align: center; margin-top: 20px;">
  <button onclick="simpanSebagaiGambar()">Simpan Sebagai PNG</button>
  <button onclick="kirimWhatsApp()">Kirim via WhatsApp</button>
</div>

  <script>
  
    // Tampilkan hari dan tanggal otomatis jika input kosong
    function tampilkanTanggal() {
    const hariInput = document.getElementById('inputHari').value.trim();
    const tanggalInput = document.getElementById('inputTanggal').value;

    let tampil = "";

    if (hariInput && tanggalInput) {
      const tanggal = new Date(tanggalInput);
      const tanggalFix = tanggal.getDate();
      const bulanNama = tanggal.toLocaleString('id-ID', { month: 'long' });
      const tahun = tanggal.getFullYear();
      tampil = {hariInput}, {tanggalFix}, {bulanNama}, {tahun};
    } else {
      const hariList = ['Minggu','Senin','Selasa','Rabu','Kamis','Jumat','Sabtu'];
      const bulanList = ['Januari','Februari','Maret','April','Mei','Juni','Juli','Agustus','September','Oktober','November','Desember'];
      const now = new Date();
      const hari = hariList[now.getDay()];
      const tanggal = now.getDate();
      const bulan = bulanList[now.getMonth()];
      const tahun = now.getFullYear();
      tampil = {hari}, {tanggal}, {bulan}, {tahun};
    }

    document.getElementById("tanggalNow").innerText = tampil;
  }

    // Nama siswa bisa kamu ubah di sini
    const namaSiswa = [
      "Akhmad hepiansyah", "Andi Rahmat T.", "Desti Nur F.", "Deva Erlingga", "Dira Aurel K.P", "Farel Raditya", "Feby Ramadhani", "Fraisa Zaira P.", "Jeine Aldrianie", "Justin Antonio",
  "Kevin Yulio R.", "Marvin Ardiansyah S.", "M. Ridho Maulana", "Riko Siregar", "Rizal Nur Z.", "Rizki Candra C.", "Sandy RItama A.", "Sefta Aggi F.R", "Wedad Abdur R.", "Zaskia Prisilia N.A", "Zhora Septada"
    ];

    const absenBody = document.getElementById("absenBody");

    for (let i = 0; i < namaSiswa.length; i++) {
      const row = document.createElement("tr");
      row.innerHTML = `
        <td>${i + 1}</td>
        <td>${namaSiswa[i]}</td>
        <td><input type="checkbox" name="hadir" onclick="onlyOne(this)"></td>
        <td><input type="checkbox" name="sakit" onclick="onlyOne(this)"></td>
        <td><input type="checkbox" name="izin" onclick="onlyOne(this)"></td>
        <td><input type="checkbox" name="alpha" onclick="onlyOne(this)"></td>
      `;
      absenBody.appendChild(row);
    }

    function onlyOne(checkbox) {
      const row = checkbox.closest('tr');
      const boxes = row.querySelectorAll('input[type="checkbox"]');
      boxes.forEach(box => {
        if (box !== checkbox) box.checked = false;
      });
      updateCount();
    }

    function updateCount() {
      let hadir = 0, sakit = 0, izin = 0, alpha = 0;
      const rows = document.querySelectorAll('#absenBody tr');

      rows.forEach(row => {
        const boxes = row.querySelectorAll('input[type="checkbox"]');
        if (boxes[0].checked) hadir++;
        else if (boxes[1].checked) sakit++;
        else if (boxes[2].checked) izin++;
        else if (boxes[3].checked) alpha++;
      });

      document.getElementById("hadirCount").textContent = hadir;
      document.getElementById("sakitCount").textContent = sakit;
      document.getElementById("izinCount").textContent = izin;
      document.getElementById("alphaCount").textContent = alpha;
    }

    // Update tanggal otomatis saat halaman dibuka & jika input diganti
    window.onload = tampilkanTanggal;
    document.getElementById("inputHari").addEventListener("input", tampilkanTanggal);
    document.getElementById("inputTanggal").addEventListener("input", tampilkanTanggal);
</script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

<script>
  function simpanSebagaiGambar() {
    const elemen = document.body; // Bisa diganti dengan elemen khusus
    html2canvas(elemen).then(canvas => {
      const link = document.createElement("a");
      link.download = "absensi.png";
      link.href = canvas.toDataURL("image/png");
      link.click();

      // Simpan juga ke window global (jika ingin digunakan ulang)
      window.gambarCanvas = canvas.toDataURL("image/png");
    });
  }

  function kirimWhatsApp() {
    const pesan = "Berikut adalah absensi siswa hari ini. 📋\n\n(Note: Gambar absensi terlampir jika kamu kirim manual)";
    const noHP = "6281522871107"; // Ganti dengan nomor tujuan WA

    const urlWA = https://wa.me/${noHP}?text=${encodeURIComponent(pesan)};
    
    // Buka WhatsApp Web
    window.open(urlWA, '_blank');

    alert("📎 Gambar sudah disiapkan. Kirim manual di WhatsApp dengan lampiran gambar yang sudah kamu simpan.");
  }
</script>

</body>
</html>
