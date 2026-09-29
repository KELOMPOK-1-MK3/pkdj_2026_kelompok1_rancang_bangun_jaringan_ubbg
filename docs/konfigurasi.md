# Konfigurasi Perangkat

Salinan konfigurasi setiap perangkat jaringan. Berkas teks lengkapnya ada di
[`src/configs/`](../src/configs/) supaya perubahannya dapat ditelusuri melalui
riwayat commit.

Status: **terisi** — konfigurasi disusun mengikuti alamat pada
[`pengalamatan-vlsm.md`](pengalamatan-vlsm.md). Salinan di `src/configs/` masih
perlu dicocokkan dengan perangkat setelah topologi Packet Tracer dibangun
(minggu ke-4), terutama **nomor antarmuka**, karena penomoran bergantung pada
modul yang dipasang pada tiap perangkat.

## Ringkasan Perangkat

| Perangkat | Model pada simulasi | Peran | Berkas konfigurasi |
|---|---|---|---|
| Router-Klinik | ISR4331 | Penghubung ke internet, NAT, OSPF | `src/configs/router-klinik.txt` |
| Core-Switch-L3 | Catalyst 3560 | Pusat inter-VLAN routing, DHCP relay, OSPF, ACL | `src/configs/core-switch-l3.txt` |
| SW-Access-1 | Catalyst 2960 | Zona pendaftaran dan poliklinik | `src/configs/access-switch-1.txt` |
| SW-Access-2 | Catalyst 2960 | Zona farmasi, laboratorium, manajemen | `src/configs/access-switch-2.txt` |
| SW-Access-3 | Catalyst 2960 | Zona tamu | `src/configs/access-switch-3.txt` |
| SW-Server | Catalyst 2960 | Zona server layanan | `src/configs/switch-server.txt` |
| Server-Layanan | Server-PT | DHCP, DNS, web, FTP | `src/configs/server-layanan.txt` |

## Keputusan Konfigurasi yang Perlu Dijelaskan

### Inter-VLAN routing di core switch, bukan di router

Gateway setiap VLAN dibuat sebagai antarmuka virtual (SVI) pada Core-Switch-L3,
bukan pada Router-Klinik. Alasannya: trafik antar-VLAN (misalnya poliklinik ke
laboratorium) tidak perlu naik sampai router tepi, sehingga tidak membebani
tautan menuju internet dan tidak perlu melewati NAT.

### VLAN buangan (999) sebagai native VLAN pada port trunk

Port trunk memakai `switchport trunk native vlan 999` dengan VLAN 999 yang tidak
dipakai siapa pun. Tujuannya, bila ada frame tanpa tag yang masuk ke port trunk,
frame itu jatuh ke VLAN yang tidak berisi perangkat apa pun — bukan ke VLAN 1
yang secara bawaan ada di semua switch. Ini menutup salah satu celah
penyusupan VLAN yang paling umum (VLAN hopping).

### DHCP relay, bukan DHCP server di switch

Permintaan alamat diteruskan (`ip helper-address 192.168.10.242`) ke
Server-Layanan. Satu tempat pengelolaan alamat lebih mudah diaudit daripada
kumpulan alamat yang tersebar di tiap switch.

### ACL tamu di antarmuka masuk, bukan keluar

`BATAS_TAMU` dipasang pada `Vlan99` arah **masuk** (`in`). Dengan begitu paket
dari tamu disaring sebelum sempat dirutekan ke VLAN lain, sehingga trafiknya
tidak pernah masuk ke jaringan internal sama sekali — lebih hemat dan lebih
aman dibanding menyaring di antarmuka tujuan satu per satu.

### Layanan mana yang boleh dijangkau tamu

Tamu hanya boleh mencapai Server-Layanan (untuk DHCP dan DNS) dan internet.
Seluruh VLAN internal lain ditolak. Catatan penting: aturan ini **harus** diuji
dengan `ping` dari perangkat tamu ke perangkat poliklinik — kalau tidak,
ACL-nya hanya ada di atas kertas.

### Dua ACL dengan tugas berbeda — jangan tertukar

Ada dua ACL yang gampang dikira sama padahal berbeda tempat dan tujuan:

| ACL | Tempat | Tugas |
|---|---|---|
| `BATAS_TAMU` | Core-Switch-L3, `Vlan99` arah masuk | Memisahkan tamu dari VLAN medis. Bukan urusan NAT. |
| `DAPAT_KELUAR` | Router-Klinik, dipakai `ip nat inside source list` | Menentukan VLAN mana yang alamatnya boleh diterjemahkan keluar. |

Konsekuensinya, VLAN 99 **harus tetap ada di `DAPAT_KELUAR`**: tamu memang tidak
boleh masuk jaringan medis, tetapi justru harus bisa keluar ke internet — itu
tujuan pemasangan WiFi tamu. Pemisahannya sudah dikerjakan `BATAS_TAMU` di core
switch, sebelum paketnya sempat naik ke router. Kalau VLAN 99 ikut ditolak di
`DAPAT_KELUAR`, tamu tidak mendapat internet sama sekali dan pengujian 4b di
[`pengujian.md`](pengujian.md) akan gagal tanpa sebab yang jelas.

VLAN 100 (Server) juga perlu diizinkan keluar untuk pembaruan sistem dan
penerusan DNS ke luar. ACL diakhiri `deny any` eksplisit supaya VLAN yang tidak
disebut tidak diam-diam mendapat akses.

### Waktu kirim pada node IoT

Bukan bagian konfigurasi jaringan, tetapi berpengaruh pada pengukuran: Node 2
mengambil waktu dari NTP, sedangkan Node 1 (BLE) memakai waktu sejak menyala.
Penerima menyelaraskan selisih jam pada awal setiap sesi — lihat
`docs/arsitektur.md` pada repository MKKL1029.

## Rencana Pengujian Konfigurasi

| # | Yang diuji | Cara | Harapan |
|---|---|---|---|
| 1 | Trunk antar switch | `show interfaces trunk` | VLAN 10–100 berstatus trunking, native 999 |
| 2 | Inter-VLAN routing | `ping` dari VLAN 10 ke VLAN 20 | berhasil |
| 3 | DHCP per VLAN | `ipconfig` pada klien tiap VLAN | mendapat alamat sesuai kumpulan VLAN-nya |
| 4 | DNS internal | `nslookup server.klinik.local` | menjawab 192.168.10.242 |
| 5 | Pembatasan tamu | `ping` dari VLAN 99 ke VLAN 20 | gagal (tujuan tidak terjangkau) |
| 6 | Tamu ke server | `ping` dari VLAN 99 ke 192.168.10.242 | berhasil |
| 7 | Routing OSPF | `show ip route ospf` | rute jaringan tetangga muncul |
| 8 | NAT ke luar | `ping` ke alamat luar dari VLAN 50 | berhasil, `show ip nat translations` terisi |
| 9 | Portal web | buka `http://web.klinik.local` dari VLAN 50 | halaman portal terbuka |
| 10 | FTP cadangan | unduh berkas dari VLAN 50 | berhasil |
