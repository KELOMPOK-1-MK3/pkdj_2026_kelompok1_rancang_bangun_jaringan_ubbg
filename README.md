# Jaringan Klinik Kesehatan Kecil — Segmentasi VLAN, Inter-VLAN Routing, OSPF, dan Layanan Server

**Mata kuliah:** Pemodelan Komunikasi Data dan Jaringan (MKKL1031) · Semester 7
**Program Studi Ilmu Komputer — Fakultas Sains, Teknologi dan Ilmu Kesehatan**
**Universitas Bina Bangsa Getsempena**
**Dosen Pengampu:** Ahmad Mujahid Abdurrahman, S.Kom, M.T.

Rancangan dan simulasi jaringan untuk klinik kesehatan kecil satu lantai. Setiap zona dipisahkan ke VLAN tersendiri — pendaftaran, rekam medis, dokter, tamu, server, dan manajemen — lalu dihubungkan melalui inter-VLAN routing pada core switch lapis tiga. Routing dinamis menghubungkan jaringan klinik dengan jalur internet, sementara aturan pembatasan akses menutup jalan dari WiFi tamu menuju jaringan rekam medis. Empat layanan server disiapkan dan diuji: pengalamatan otomatis, penamaan internal, portal web, dan server berkas untuk pencadangan.

## Anggota Kelompok

| No | Nama | NIM | Peran |
|---|---|---|---|
| 1 | Paris Mursidan Aufal | 23210125 | Skema pengalamatan VLSM kedua gedung dan tabel pengalamatan |
| 2 | Yogi Prasetya Sadewa | 23210060 | Ketua kelompok; perancangan topologi, konfigurasi server, dan integrasi simulasi |
| 3 | M. Sidiq Prasetio | 23210075 | Konfigurasi VLAN dan inter-VLAN routing pada switch lapis tiga |
| 4 | Deski Taiza | 23210003 | Routing dinamis antar-gedung (OSPF) dan jalur cadangan |
| 5 | Wira | 23210045 | Aturan pembatasan akses ke server dan pengujian konektivitas |
| 6 | T. Zain Wardana | 23210001 | Pengujian di Packet Tracer dan penyusunan tabel bukti |

> Kelompok berjumlah 6 orang; setiap anggota mengerjakan satu bagian di tiap mata
> kuliah dan melakukan commit dari akun GitHub masing-masing.

## Rencana Proyek

- Google Docs (dibagikan kepada dosen dengan akses komentar) — tautan: [MKKL1031 — Jaringan Klinik Kesehatan Kecil](https://docs.google.com/document/d/1krusm61k6EZOCbH7tlWy59acsWTg5411hK5TJ2IUZFU/edit)
- Salinan di repository: [`docs/rencana-proyek.md`](docs/rencana-proyek.md)

## Cara Menjalankan

### Kebutuhan

- Cisco Packet Tracer (versi 8.x) untuk membuka berkas simulasi
- Alternatif: GNS3 atau Mininet, dengan catatan konfigurasi perlu penyesuaian

### 1. Membuka simulasi

Buka berkas di folder `simulation/` menggunakan Packet Tracer, lalu tunggu
seluruh perangkat selesai memuat konfigurasi.

### 2. Memeriksa pengalamatan

Pada setiap PC, buka Command Prompt dan jalankan:

```
ipconfig /all
```

Alamat yang muncul harus sesuai tabel pada `docs/pengalamatan-vlsm.md`.
Alamat penting yang dipakai pada langkah berikutnya:

| Perangkat | Alamat |
|---|---|
| Gerbang VLAN 10 (pendaftaran) | 192.168.10.1 |
| Gerbang VLAN 20 (poliklinik) | 192.168.10.65 |
| Server-Layanan | 192.168.10.242 |

### 3. Menguji konektivitas antar-VLAN

```
ping 192.168.10.1        # gerbang VLAN sendiri
ping 192.168.10.65       # gerbang VLAN lain (bukti inter-VLAN routing)
ping 192.168.10.242      # Server-Layanan pada VLAN 100
tracert 192.168.10.242   # harus melewati core switch
```

### 4. Menguji pembatasan akses tamu

Dari PC pada VLAN tamu, akses ke jaringan medis harus gagal sementara akses
ke internet tetap berjalan. Catat hasilnya pada `docs/pengujian.md`.

### 5. Menguji layanan server

```
nslookup server.klinik.local      # penamaan internal
buka http://web.klinik.local      # portal web
ftp 192.168.10.242                # server berkas pencadangan
```

> Berkas simulasi ditambahkan setelah topologi selesai dikerjakan.

## Struktur Repository

```
pkdj_2026_kelompok1_rancang_bangun_jaringan_ubbg/
├── README.md
├── docs/
│   ├── rencana-proyek.md
│   ├── pengalamatan-vlsm.md
│   ├── konfigurasi.md
│   ├── pengujian.md
│   ├── diagrams/
│   └── MKKL1031-Jaringan-Klinik-Kesehatan.docx
├── simulation/
│   ├── README.md
│   └── klinik-kesehatan.pkt   # berkas Cisco Packet Tracer
└── src/
    └── configs/               # salinan konfigurasi tiap perangkat
```

## Dokumentasi

| Berkas | Isi |
|---|---|
| `docs/rencana-proyek.md` | Rencana proyek, target UTS dan UAS, pembagian kerja per minggu, risiko |
| `docs/pengalamatan-vlsm.md` | Tabel VLSM: alamat jaringan, alamat broadcast, dan rentang host per VLAN |
| `docs/konfigurasi.md` | Konfigurasi router, core switch, access switch, dan server |
| `docs/pengujian.md` | Skenario pengujian dan tabel bukti konektivitas |
| `docs/diagrams/` | Diagram topologi dan alur antar-VLAN |

## Pemenuhan Ketentuan Mata Kuliah

- **Topologi disimulasikan**: berkas simulasi disimpan pada repository dan dapat dijalankan.
- **Skema pengalamatan lengkap**: VLSM dengan tabel alamat jaringan, alamat broadcast, dan rentang host.
- **VLAN dan routing**: enam VLAN, inter-VLAN routing pada core switch, serta routing dinamis antar-perangkat.
- **Layanan server diuji**: pengalamatan otomatis, penamaan internal, portal web, dan server berkas, seluruhnya dengan bukti pengujian.

## Aturan Kerja Kelompok

- Commit dilakukan dari akun GitHub masing-masing anggota, bukan satu akun untuk seluruh kelompok.
- Pesan commit deskriptif dan menunjukkan kemajuan; dikerjakan minimal sekali per minggu per anggota.
- Pekerjaan bersama menggunakan branch dan pull request; pembagian tugas dicatat pada Issues.
- Kredensial, token, dan berkas `.env` tidak boleh masuk repository.

## Status

| Tahap | Target | Status |
|---|---|---|
| Pertemuan 2 | Rencana proyek, repository, undangan kolaborator | Selesai |
| Pertemuan 8 (UTS) | Topologi dasar terbentuk dan seluruh VLAN dikonfigurasi pada switch | Belum dimulai |
| Pertemuan 16 (UAS) | Model jaringan klinik lengkap dan dapat dijalankan pada perangkat lunak simulasi | Belum dimulai |

Yang **sudah selesai di atas kertas**: tabel VLSM lengkap, konfigurasi tujuh
perangkat, dan rencana pengujian sepuluh butir.

Yang **wajib dikerjakan berikutnya** — semuanya butuh Cisco Packet Tracer:

1. Bangun topologi di Packet Tracer, lalu **cocokkan nomor antarmuka** dengan
   `src/configs/*.txt` (nomor di berkas masih contoh karena bergantung modul
   yang dipasang).
2. Simpan berkas simulasi ke `simulation/klinik-kesehatan.pkt`.
3. Jalankan tabel pengujian di [`docs/pengujian.md`](docs/pengujian.md) dan isi
   kolom bukti dengan tangkapan layar.

Alamat di seluruh berkas sudah diperiksa konsisten dengan
[`docs/pengalamatan-vlsm.md`](docs/pengalamatan-vlsm.md).
