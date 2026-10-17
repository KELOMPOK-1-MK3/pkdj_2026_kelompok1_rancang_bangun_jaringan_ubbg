# Rencana Proyek — Rancang Bangun Jaringan UBBG Dua Gedung

**Mata kuliah:** Pemodelan Komunikasi Data dan Jaringan (MKKL1031) · Semester 7  
**Program Studi Ilmu Komputer — Fakultas Sains, Teknologi dan Ilmu Kesehatan, Universitas Bina Bangsa Getsempena**  
**Dosen Pengampu:** Ahmad Mujahid Abdurrahman, S.Kom, M.T.  
**Versi:** 2.0 — 29 September 2026

> Salinan kerja dari dokumen rencana proyek pada Google Docs. Perubahan wajib dilakukan pada kedua tempat agar penilaian tetap sinkron.
>
> **Catatan versi:** versi 1.0 memakai topik jaringan klinik kesehatan. Dosen
> kemudian menetapkan topik seragam untuk seluruh kelompok, yaitu rancang bangun
> jaringan UBBG dua gedung. Dokumen ini adalah versi setelah pergantian topik.

## 1. Identitas Kelompok

| No | Nama | NIM | Peran |
|---|---|---|---|
| 1 | Paris Mursidan Aufal | 23210125 | Skema pengalamatan VLSM kedua gedung dan tabel pengalamatan |
| 2 | Yogi Prasetya Sadewa | 23210060 | Ketua kelompok; perancangan topologi, konfigurasi server, dan integrasi simulasi |
| 3 | M. Sidiq Prasetio | 23210075 | Konfigurasi VLAN dan inter-VLAN routing pada switch lapis tiga |
| 4 | Deski Taiza | 23210003 | Routing dinamis antar-gedung (OSPF) dan jalur cadangan |
| 5 | Wira | 23210045 | Aturan pembatasan akses ke server dan pengujian konektivitas |
| 6 | T. Zain Wardana | 23210001 | Pengujian di Packet Tracer dan penyusunan tabel bukti |

## 2. Masalah dan Tujuan

### Masalah yang diselesaikan

Kampus UBBG memiliki lebih dari satu gedung kuliah, dan kegiatan perkuliahan
tersebar di gedung-gedung itu. Di salah satu gedung ada server yang menyimpan
data penting kampus. Dua hal yang harus diatur: seluruh komputer di kedua
gedung harus bisa memakai server tersebut, tetapi tidak semua orang boleh
masuk ke dalamnya.

Apabila semua perangkat disatukan dalam satu jaringan datar, data penting
kampus menjadi mudah dijangkau dari mana saja, termasuk dari jaringan tamu.
Perangkat tamu berada pada jaringan yang sama dengan komputer yang menyimpan
data, sehingga satu perangkat tamu yang terinfeksi dapat menjangkau data
tersebut. Selain itu, apabila hanya ada satu jalur penghubung antar gedung,
putusnya kabel itu langsung memutus seluruh akses ke server.

### Latar belakang

- Rancangan ini disusun sebagai simulasi yang dapat dijalankan dan diuji, bukan sekadar gambar topologi.
- Data penting kampus perlu pemisahan jaringan antara zona publik dan zona internal sebagai kebutuhan dasar, bukan tambahan.
- Perlu dibuktikan bahwa jalur cadangan benar-benar mengambil alih saat jalur utama terputus, bukan hanya direncanakan.

### Pengguna sasaran

- Pengelola jaringan kampus, sebagai gambaran jaringan dua gedung yang dapat diterapkan.
- Dosen dan mahasiswa, sebagai bahan pembelajaran perancangan VLAN, routing, dan pembatasan akses.

### Batasan lingkup

- Simulasi dibatasi pada dua gedung kuliah dengan sembilan VLAN.
- Perancangan dilakukan di Cisco Packet Tracer, bukan pada perangkat jaringan sungguhan.
- Alamat yang dipakai adalah alamat privat untuk jaringan dalam kampus.
- Judul dan skenario ditetapkan dosen sama untuk semua kelompok.

## 3. Ruang Lingkup Pekerjaan

| Aspek | Isi |
|---|---|
| Pemisahan jaringan | Sembilan VLAN di dua gedung: kelas, laboratorium, dosen dan staf, tamu, serta zona server |
| Pengalamatan | VLSM per gedung, dibagi sesuai kebutuhan tiap bagian |
| Inter-VLAN routing | Gedung A lewat sub-interface router; Gedung B lewat SVI switch lapis tiga |
| Routing antar-gedung | Static route dengan jalur cadangan ber-*administrative distance* lebih tinggi |
| Pembatasan akses | ACL menolak jaringan tamu menuju zona server |
| Layanan server | Portal web internal |
| Pengujian | Delapan skenario dari proposal, dengan tabel bukti |

## 4. Alat dan Bahan

| Perangkat lunak | Dipakai untuk | Biaya |
|---|---|---|
| Cisco Packet Tracer | Membuat dan menguji rancangan jaringan | Gratis |
| Akun Cisco NetAcad | Syarat mengunduh Packet Tracer | Gratis |
| GitHub | Menyimpan berkas simulasi dan dokumentasi | Gratis |

Perangkat yang digambarkan di simulasi: 2 router, 2 switch lapis tiga,
4 switch biasa, 1 server, dan 16 komputer.

## 5. Pembagian Kerja

Setiap anggota mengerjakan satu bagian di mata kuliah ini. Setiap bagian harus
ada bukti commit dari akun GitHub masing-masing, karena penilaian dilakukan
dari jejak pekerjaan, bukan dari nama yang tertulis di dokumen.

| Anggota | Bagian |
|---|---|
| Paris Mursidan Aufal | Skema pengalamatan VLSM kedua gedung dan tabel pengalamatan |
| Yogi Prasetya Sadewa | Perancangan topologi, konfigurasi server, dan integrasi simulasi |
| M. Sidiq Prasetio | Konfigurasi VLAN dan inter-VLAN routing pada switch lapis tiga |
| Deski Taiza | Routing antar-gedung dan jalur cadangan |
| Wira | Aturan pembatasan akses ke server dan pengujian konektivitas |
| T. Zain Wardana | Pengujian di Packet Tracer dan penyusunan tabel bukti |

## 6. Rencana Kegiatan

| Pertemuan | Kegiatan | Hasil yang diharapkan | Status |
|---|---|---|---|
| 1 – 2 | Menyusun proposal dan membuat repository | Proposal dan repository siap | Selesai |
| 3 – 5 | Merancang topologi dan membagi alamat IP | Gambar topologi dan tabel alamat | Selesai |
| 5 – 6 | Mengatur VLAN dan komunikasi antar-VLAN | Perangkat di tiap bagian terhubung | Selesai |
| 7 – 8 | Menghubungkan kedua gedung dan menyiapkan jalur cadangan | Kedua gedung tersambung | Selesai |
| 9 – 12 | Membatasi akses ke server dan menguji seluruh skenario | Tabel bukti pengujian terisi | Selesai |
| 13 – 16 | Merapikan laporan dan menyiapkan presentasi | Laporan akhir dan bahan presentasi | Berjalan |

## 7. Risiko

| Risiko | Dampak | Cara mengurangi |
|---|---|---|
| Perangkat pada Packet Tracer berbeda tipe dari yang dirancang | Konfigurasi ditolak, rancangan harus diubah | Sudah terjadi pada MLS Gedung A — inter-VLAN routing dipindah ke router. Perangkat diperiksa dengan `show version` sebelum konfigurasi ditulis. |
| Berkas simulasi hilang karena aplikasi berhenti mendadak | Pekerjaan berulang | Simpan berkas setiap 10–15 menit; simpan salinan bertanggal sebelum perubahan besar. |
| Anggota tidak melakukan commit | Nilai individu berkurang | Ingatkan minimal sekali seminggu; pembagian tugas dicatat pada Issues. |
| Pengujian tidak lengkap saat penilaian | Skenario terbukti hanya di atas kertas | Tabel bukti diisi langsung setelah tiap pengujian dijalankan. |
