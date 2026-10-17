# Konfigurasi

Halaman ini menjelaskan konfigurasi tiap perangkat dan alasan di balik
pilihannya. Salinan lengkap `show running-config` ada di
[`src/configs/`](../src/configs/).

## Ringkasan perangkat

| Perangkat | Model | Peran |
|---|---|---|
| R-GEDUNG-A | Cisco 2911 | Gerbang Gedung A; router-on-a-stick; titik ACL tamu |
| R-GEDUNG-B | Cisco 2911 | Penghubung Gedung B ke Gedung A (transit) |
| MLS-GEDUNG-A | Cisco 3560-24PS | Transit lapis dua murni — **tanpa SVI** |
| MLS-GEDUNG-B | Cisco 3560-24PS | Pusat inter-VLAN routing Gedung B |
| SW-A1 | Cisco 2960-24TT | VLAN 10 dan 20 |
| SW-A2 | Cisco 2960-24TT | VLAN 30 dan 99 |
| SW-B1 | Cisco 2960-24TT | VLAN 11 dan 21 |
| SW-B2 | Cisco 2960-24TT | VLAN 31 dan 199 |
| Server0 | Server-PT | Server UBBG, VLAN 100 |

## Peta port

### MLS-GEDUNG-A

| Port | Mode | Tujuan |
|---|---|---|
| Fa0/1 | Trunk (10,20,30,99-100) | R-GEDUNG-A — tautan router-on-a-stick |
| Fa0/2 | Trunk (10,20,30,99-100) | SW-A1 |
| Fa0/3 | Trunk (10,20,30,99-100) | SW-A2 |
| Fa0/4 | Access VLAN 100 | Server0 |

### MLS-GEDUNG-B

| Port | Mode | Tujuan |
|---|---|---|
| Fa0/1 | Routed (`no switchport`), 172.16.20.2/30 | R-GEDUNG-B |
| Fa0/2 | Trunk (11,21) | SW-B1 |
| Fa0/3 | Trunk (31,199) | SW-B2 |

### Access switch

| Switch | Fa0/1 | Port access |
|---|---|---|
| SW-A1 | Trunk ke MLS-A (10,20) | Fa0/2, Fa0/4, Fa0/5 = VLAN 10; Fa0/3, Fa0/6, Fa0/7 = VLAN 20 |
| SW-A2 | Trunk ke MLS-A (30,99) | Fa0/2 = VLAN 30; Fa0/3, Fa0/4 = VLAN 99 |
| SW-B1 | Trunk ke MLS-B (11,21) | Fa0/2, Fa0/4, Fa0/5 = VLAN 11; Fa0/3, Fa0/6, Fa0/7 = VLAN 21 |
| SW-B2 | Trunk ke MLS-B (31,199) | Fa0/2 = VLAN 31; Fa0/3 = VLAN 199 |

## Keputusan penting

### Inter-VLAN routing Gedung A dipindah ke router

Rancangan awal menaruh inter-VLAN routing di MLS-GEDUNG-A memakai SVI. Saat
diuji, switch itu menolak `ip routing` dan SVI-nya tidak pernah muncul — model
yang terpasang berperilaku seperti WS-C2960, yang memang hanya lapis dua.

**Solusinya:** seluruh SVI di MLS-GEDUNG-A dihapus dan switch menjadi transit
lapis dua murni. Inter-VLAN routing dipindahkan ke R-GEDUNG-A memakai
sub-interface:

```
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.192
!
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.10.65 255.255.255.224
!(dan seterusnya untuk VLAN 30, 99, 100)
```

Gedung B tidak mengalami masalah ini karena MLS-GEDUNG-B benar-benar 3560 dan
SVI-nya berjalan normal. Jadi kedua gedung memakai cara yang berbeda — dan itu
memang disengaja, bukan ketidakkonsistenan.

### Pembatasan akses tamu

ACL dipasang di **R-GEDUNG-A**, diikat ke sub-interface VLAN 99 dengan arah
masuk. Ini tempat yang tepat karena seluruh trafik tamu Gedung A harus lewat
sini sebelum bisa mencapai VLAN lain.

```
access-list 110 deny ip 192.168.10.112 0.0.0.15 192.168.10.144 0.0.0.7
access-list 110 deny ip 192.168.11.112 0.0.0.15 192.168.10.144 0.0.0.7
access-list 110 permit ip any any
!
interface GigabitEthernet0/0.99
 ip access-group 110 in
```

Wildcard `0.0.0.15` mewakili blok tamu `/28` (14 host), dan `0.0.0.7` mewakili
zona server `/29`. Dua baris pertama menutup tamu dari **kedua** gedung —
Gedung A (`192.168.10.112`) maupun Gedung B (`192.168.11.112`) — karena tamu
dari gedung mana pun tidak boleh masuk zona server. Baris terakhir mengizinkan
trafik lain, termasuk akses internal ke server.

### Jalur cadangan

Rute utama dan rute cadangan dibedakan lewat *administrative distance*:

```
! R-GEDUNG-A
ip route 192.168.11.0 255.255.255.0 10.0.0.2        ! utama, distance 1
ip route 192.168.11.0 255.255.255.0 172.16.30.2 200 ! cadangan, distance 200
```

Karena distance 1 lebih kecil, rute utama selalu menang selama jalurnya hidup.
Begitu antarmuka `G0/1` turun, rute itu hilang dari tabel dan rute distance 200
langsung dipakai — tanpa menunggu timer protokol routing. Inilah sebabnya
perpindahannya cepat (terukur ~2,5 detik).

### Mengapa bukan routing dinamis

Proposal menyebut routing dinamis antar-gedung. Yang dipakai pada simulasi
adalah static route, dengan alasan: topologinya hanya punya dua rute tetap dan
tidak berubah, sehingga routing dinamis menambah kompleksitas tanpa manfaat.
Floating static sudah cukup untuk membuktikan perpindahan jalur, dan lebih
mudah dipertanggungjawabkan saat ditanya dosen.

### Catatan tentang encapsulation pada 2960

Pada switch 2960, baris `switchport trunk encapsulation dot1q` tidak muncul di
`show running-config` karena encapsulation-nya terdeteksi otomatis (`n-802.1q`).
Yang membuktikan port benar-benar trunk adalah keluaran `show interfaces trunk`,
bukan ada-tidaknya baris tersebut.

## Urutan pemasangan konfigurasi

Kalau perangkat perlu dikonfigurasi ulang dari nol:

1. R-GEDUNG-A
2. R-GEDUNG-B
3. MLS-GEDUNG-A
4. MLS-GEDUNG-B
5. SW-A1, SW-A2, SW-B1, SW-B2 (urutan bebas)
6. Alamat PC diatur lewat **Desktop > IP Configuration**
7. Alamat dan layanan Server0 diatur lewat **Desktop**

Berkas `.pkt` harus disimpan setiap kali ada perubahan — Packet Tracer tidak
memiliki penyimpanan otomatis.
