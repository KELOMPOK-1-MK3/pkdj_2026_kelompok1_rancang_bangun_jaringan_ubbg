# Pengujian

Delapan skenario dari proposal diuji langsung pada simulasi. Hasil di bawah
bukan rancangan di atas kertas — semuanya sudah dijalankan dan hasilnya dicatat.

## Ringkasan hasil

| # | Skenario | Yang diharapkan | Hasil | Status |
|---|---|---|---|---|
| 1 | Pengalamatan setiap PC | Sesuai tabel VLSM | 16 PC terpasang alamat statis sesuai tabel | ✅ |
| 2 | Komunikasi antar-VLAN satu gedung | Balasan | PC0 ↔ PC3 balasan 4/4 | ✅ |
| 3 | PC Gedung B mengakses server Gedung A | Balasan | PC8 → 192.168.10.146 balasan 4/4 | ✅ |
| 4 | Portal web server dibuka | Halaman terbuka | PC0 dan PC8 membuka `http://192.168.10.146` | ✅ |
| 5 | Tamu mengakses server | Ditolak | PC7 → server 0/4 (100% loss) | ✅ |
| 6 | Tamu memakai internet | Berhasil | Tidak diuji — butuh ISP | ⬜ |
| 7 | Jalur utama putus, cadangan mengambil alih | Tetap tersambung | PC0 → PC8 tetap balasan 4/4 | ✅ |
| 8 | Waktu perpindahan jalur cadangan | Diukur | ~2,5 detik | ✅ |

## Tabel bukti

### 1. Pengalamatan

Setiap PC diatur lewat **Desktop > IP Configuration** dengan alamat statis
sesuai [`pengalamatan-vlsm.md`](pengalamatan-vlsm.md). Diperiksa dengan
`ipconfig /all` pada tiap PC.

Hasil: seluruh 16 PC memperoleh alamat, subnet mask, dan gerbang yang sesuai
tabel. Tidak ada alamat yang bertabrakan.

### 2. Komunikasi antar-VLAN dalam satu gedung

Perintah pada PC0 (VLAN 10):

```
ping 192.168.10.1     # gerbang VLAN sendiri
ping 192.168.10.66    # PC4 pada VLAN 20
```

Hasil: 4/4 balasan, 0% loss. Inter-VLAN routing berjalan lewat sub-interface
R-GEDUNG-A.

### 3. Akses lintas gedung ke server

Perintah pada PC8 (VLAN 11, Gedung B):

```
ping 192.168.10.146
```

Hasil: 4/4 balasan, 0% loss. Jalur yang dilewati: PC8 → MLS-GEDUNG-B →
R-GEDUNG-B → R-GEDUNG-A → Server0.

### 4. Portal web

Perintah pada PC0 dan PC8 lewat **Desktop > Web Browser**:

```
http://192.168.10.146
```

Hasil: halaman web bawaan Packet Tracer terbuka.

> Catatan: pada proposal, skenario ini ditulis sebagai "nama server internal bisa
> diterjemahkan". Pada simulasi, nama domain belum dipetakan di layanan DNS,
> sehingga pengujian memakai alamat IP langsung. Yang terbukti adalah portal
> webnya dapat diakses dari dua gedung berbeda.

### 5. Pembatasan akses tamu

Diuji dari dua arah supaya terbukti aturannya bekerja sesuai maksud, bukan
sekadar memblokir semua trafik.

| Dari | VLAN | Ke | Hasil |
|---|---|---|---|
| PC0 (internal) | 10 | 192.168.10.146 | **4/4 balasan, 0% loss** |
| PC7 (tamu) | 99 | 192.168.10.146 | **0/4, 100% loss** — `Destination host unreachable` |

Selisih hasil antara dua baris di atas adalah buktinya: komputer internal tetap
bisa masuk, jaringan tamu ditolak.

### 6. Tamu memakai internet

Tidak diuji. Skenario ini memerlukan sambungan ke penyedia internet, sedangkan
pada batasan masalah disebutkan alamat yang dipakai adalah alamat privat untuk
jaringan dalam kampus. Karena itu skenario ini dilewati sejak awal.

### 7. Perpindahan ke jalur cadangan

Perintah pada PC0:

```
ping 192.168.11.10
```

Diuji pada dua keadaan:

| Keadaan | Hasil |
|---|---|
| Kedua jalur hidup | 4/4 balasan, 0% loss |
| `GigabitEthernet0/1` R-GEDUNG-A dimatikan | 4/4 balasan, 0% loss — lewat jalur cadangan |

### 8. Waktu perpindahan

Diukur dengan memantau isi tabel rute R-GEDUNG-A sambil jalur utama dimatikan.

| Keadaan | Distance | Next-hop |
|---|---|---|
| Sebelum (kedua jalur hidup) | 1 | 10.0.0.2 |
| Sesaat setelah G0/1 dimatikan | 200 | 172.16.30.2 |
| Setelah G0/1 dihidupkan lagi | 1 | 10.0.0.2 |

Tiga kali percobaan menghasilkan **2,4 detik / 2,5 detik / 2,5 detik**. Rute
cadangan langsung dipakai begitu rute utama hilang, tanpa menunggu timer.

## Cara mengambil bukti

Untuk setiap pengujian, simpan tangkapan layar jendela perintah dari perangkat
pada simulasi ke folder `docs/gambar/` dengan nama
`uji-<nomor>-<skenario>.png`, lalu sebutkan nama berkasnya pada kolom bukti.

## Catatan

Pengujian dilakukan menjelang UAS setelah seluruh konfigurasi selesai. Untuk
pengujian pembatasan akses (skenario 5), pengujian sengaja dilakukan dari dua
arah — dari tamu menuju server (harus gagal) dan dari internal menuju server
(harus berhasil) — supaya terbukti bahwa aturannya bekerja sesuai arah yang
dimaksud, bukan memblokir semuanya.
