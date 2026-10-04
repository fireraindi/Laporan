# Aplikasi Laporan Kegiatan Mingguan

Aplikasi web ringan (pure frontend) untuk mencatat kegiatan harian/mingguan dan mencetaknya langsung ke dalam format PDF berukuran A4. 
Dibuat dengan HTML, CSS, dan JavaScript vanilla, tanpa memerlukan server, backend, atau database. Data Anda disimpan secara lokal di browser (`localStorage`).

## Fitur
1. **Penyimpanan Lokal:** Data tidak akan hilang saat Anda menutup halaman, kecuali Anda menghapusnya secara manual menggunakan tombol "Reset Semua Data".
2. **Pratinjau Kertas A4:** Tampilan laporan langsung bisa dilihat di layar yang mensimulasikan kertas ukuran A4.
3. **Cetak Langsung & Unduh PDF:** 
   - Anda dapat mencetak melalui dialog printer (Ctrl+P atau tombol "Cetak") yang sudah dioptimasi menghilangkan tombol-tombol antarmuka.
   - Anda juga dapat mengunduh langsung file PDF menggunakan tombol "Unduh PDF" tanpa lewat dialog cetak (menggunakan jsPDF).
4. **Data Hirarkis:** Mendukung banyak kegiatan dalam satu tanggal tanpa mengulang penulisan tanggal di tabel (tanggal hanya muncul di baris pertama).
5. **Responsif & Siap GitHub Pages:** Aplikasi ini hanya berupa satu file `index.html`. Sangat mudah jika ingin di-hosting secara gratis menggunakan GitHub Pages.

## Cara Menggunakan
1. Buka file `index.html` di browser Anda (Chrome, Firefox, Safari, Edge, dll) dengan klik dua kali.
2. Masukkan **Nama Pelaksana** di panel kiri. Nama ini akan otomatis tersimpan dan terupdate di laporan.
3. Tambahkan kegiatan dengan memilih **Tanggal**, mengisi **Kegiatan**, dan **Keterangan**, lalu klik **Tambah Data**.
4. Gunakan tombol **Edit** atau **Hapus** pada baris tabel untuk mengubah atau menghapus data spesifik.
5. Jika sudah selesai, klik tombol **Unduh PDF** untuk menyimpan laporan. File otomatis bernama sesuai format `laporan-kegiatan-[nama]-[tanggal].pdf`.

## Cara Mengubah / Memodifikasi Laporan (Untuk Developer)
Karena aplikasi ini 100% sisi klien (client-side) dalam satu file HTML, semua modifikasi bisa dilakukan dengan mengedit `index.html` di teks editor (Notepad, VS Code, Sublime, dll).

### 1. Menambah Kolom Baru pada Tabel
Jika Anda ingin menambah kolom baru, misalnya kolom "Lokasi":

**a. Tambah di Header Tabel (HTML):**
Cari elemen `<thead>` di HTML dan tambahkan tag `<th>` baru:
```html
<th class="col-keg">Kegiatan</th>
<th class="col-lokasi">Lokasi</th> <!-- Tambahan baru -->
<th class="col-ket">Keterangan</th>
```

**b. Tambah di Form Input (HTML):**
Tambahkan input field baru di form dengan ID `formKegiatan`:
```html
<div class="form-group">
    <label for="inputLokasi">Lokasi</label>
    <input type="text" id="inputLokasi" required>
</div>
```

**c. Update di Fungsi JavaScript:**
Update fungsi pengambilan nilai dari form pada saat *submit*:
```javascript
const lokasi = document.getElementById("inputLokasi").value;
```
Kemudian update objek State pada `tambahPekerjaan` untuk menyimpan Lokasi:
```javascript
state.Laporan.push({
    Tanggal: tanggal,
    Pekerjaan: [
        { Kegiatan: kegiatan, Lokasi: lokasi, Keterangan: keterangan }
    ]
});
```

**d. Tampilkan di Tabel Layar (Fungsi `renderData`):**
```javascript
<td class="col-keg">${pek.Kegiatan}</td>
<td class="col-lokasi">${pek.Lokasi}</td> <!-- Tambahan baru -->
<td class="col-ket">${pek.Keterangan}</td>
```

**e. Tampilkan di PDF (Fungsi `unduhPDF`):**
Ubah bagian `tableColumn` dan push row saat menggunakan jsPDF:
```javascript
const tableColumn = ["No", "Tanggal", "Kegiatan", "Lokasi", "Keterangan"];
// ...
tableRows.push([
    no.toString(),
    idx === 0 ? tglFormat : "", 
    pek.Kegiatan,
    pek.Lokasi, // Data lokasi
    pek.Keterangan
]);
```

### 2. Mengubah Margin PDF
Jika margin 15mm terasa kurang atau terlalu lebar, cari baris berikut di Javascript `unduhPDF()`:
```javascript
const margin = 15; // ubah angka ini dalam milimeter
```
Anda juga perlu menyesuaikan di CSS bagian:
```css
.a4-page {
    padding: 15mm;
}
@media print {
    @page { margin: 15mm; }
}
```

### 3. Mengganti Logo
Pastikan ada file gambar bernama `logo.png` di folder yang sama dengan `index.html`. Jika Anda menggunakan GitHub pages, unggah `logo.png` tersebut berdampingan di repository Anda.

## Dukungan Browser
- Aplikasi menggunakan fitur modern ES6 (Let, Const, Arrow functions). Pastikan Anda menggunakan versi terbaru browser (Chrome, Edge, Firefox, Safari). 
- Untuk memuat library PDF (`jsPDF` dan `jsPDF-AutoTable`), dibutuhkan **Koneksi Internet** karena dipanggil lewat CDN Cloudflare.
