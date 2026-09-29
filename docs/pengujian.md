# Pengujian

## Skenario yang Diuji

| # | Skenario | Yang diperiksa | Alat uji |
|---|---|---|---|
| 1 | Pengalamatan otomatis | Perangkat pada setiap VLAN memperoleh alamat sesuai tabel | `ipconfig` pada PC |
| 2 | Gerbang VLAN | Perangkat dapat menjangkau gerbang VLAN-nya | `ping` |
| 3 | Konektivitas antar-VLAN | Perangkat dari VLAN berbeda dapat saling menjangkau | `ping` dan `tracert` |
| 4 | Pembatasan akses tamu | Tamu ditolak saat menuju jaringan medis, tetap bisa ke internet | `ping` dan `tracert` |
| 5 | Penamaan internal | Nama host internal diselesaikan dengan benar | `nslookup` |
| 6 | Portal web | Halaman internal terbuka dari perangkat yang berhak | Peramban pada PC |
| 7 | Server berkas | Transfer berkas pencadangan berhasil | Klien FTP |
| 8 | Jalur cadangan | Komunikasi berpindah saat satu jalur diputus | `tracert` sebelum dan sesudah |

## Tabel Bukti Pengujian

| # | Skenario | Dari perangkat | Menuju | Hasil | Bukti |
|---|---|---|---|---|---|
| 1 | Pengalamatan otomatis | PC setiap VLAN | — | Belum diuji | — |
| 2 | Gerbang VLAN | PC VLAN 10 | 192.168.10.1 | Belum diuji | — |
| 3 | Antar-VLAN | PC VLAN 10 | 192.168.10.65 | Belum diuji | — |
| 4 | Pembatasan tamu | PC VLAN 99 | 192.168.10.65 | Belum diuji | — |
| 4b | Tamu ke internet | PC VLAN 99 | Alamat publik | Belum diuji | — |
| 5 | Penamaan internal | PC VLAN 30 | server.klinik.local | Belum diuji | — |
| 6 | Portal web | PC VLAN 50 | http://web.klinik.local | Belum diuji | — |
| 7 | Server berkas | PC VLAN 50 | 192.168.10.242 | Belum diuji | — |
| 8 | Jalur cadangan | PC VLAN 10 | Alamat publik | Belum diuji | — |

> Seluruh alamat di tabel ini mengikuti [`pengalamatan-vlsm.md`](pengalamatan-vlsm.md).
> Perhatikan: Server-Layanan ada di **192.168.10.242** (VLAN 100), bukan
> 192.168.10.130 — VLAN 30 (Farmasi) menempati 192.168.10.128–159 sehingga
> .130 adalah perangkat farmasi biasa. Pengujian pembatasan tamu dilakukan
> **dari VLAN 99 (tamu)**, bukan dari VLAN 40 (laboratorium) — VLAN 40 bukan
> zona tamu dan tidak melewati ACL `BATAS_TAMU`.

## Cara Mengambil Bukti

Untuk setiap pengujian, simpan tangkapan layar jendela perintah dari perangkat
pada simulasi ke folder `docs/diagrams/bukti/` dengan nama
`uji-<nomor>-<skenario>.png`, lalu sebutkan nama berkasnya pada kolom Bukti.

## Catatan

Hasil pengujian pertama diisi pada minggu ke-6, dan dilengkapi menjelang UAS.
Khusus pengujian pembatasan akses (skenario 4), pengujian dilakukan dari dua arah:
dari tamu menuju medis (harus gagal) dan dari medis menuju tamu (harus berhasil),
supaya terbukti bahwa aturan bekerja sesuai arah yang dimaksud.
