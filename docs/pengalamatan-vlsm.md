# Pengalamatan VLSM

Pembagian alamat memakai cara VLSM. Artinya, blok alamat dibagi sesuai
kebutuhan tiap bagian, bukan dibagi rata. Bagian yang butuh banyak komputer
dapat jatah besar, yang butuh sedikit dapat jatah kecil. Pembagian dimulai dari
yang paling besar, supaya alamatnya tidak cepat habis.

## Gedung A

| Bagian | Alamat jaringan | Prefix | Alamat gerbang | Alamat siaran | Host bisa dipakai | VLAN |
|---|---|---|---|---|---|---|
| Kelas | 192.168.10.0 | /26 | 192.168.10.1 | 192.168.10.63 | 62 | 10 |
| Laboratorium | 192.168.10.64 | /27 | 192.168.10.65 | 192.168.10.95 | 30 | 20 |
| Dosen dan Staf | 192.168.10.96 | /28 | 192.168.10.97 | 192.168.10.111 | 14 | 30 |
| Tamu | 192.168.10.112 | /28 | 192.168.10.113 | 192.168.10.127 | 14 | 99 |
| Zona Server | 192.168.10.144 | /29 | 192.168.10.145 | 192.168.10.151 | 6 | 100 |

## Gedung B

| Bagian | Alamat jaringan | Prefix | Alamat gerbang | Alamat siaran | Host bisa dipakai | VLAN |
|---|---|---|---|---|---|---|
| Kelas | 192.168.11.0 | /26 | 192.168.11.1 | 192.168.11.63 | 62 | 11 |
| Laboratorium | 192.168.11.64 | /27 | 192.168.11.65 | 192.168.11.95 | 30 | 21 |
| Dosen dan Staf | 192.168.11.96 | /28 | 192.168.11.97 | 192.168.11.111 | 14 | 31 |
| Tamu | 192.168.11.112 | /28 | 192.168.11.113 | 192.168.11.127 | 14 | 199 |

## Jalur antar-perangkat

| Keperluan | Alamat jaringan | Prefix | Ujung A | Ujung B |
|---|---|---|---|---|
| Jalur utama antar-gedung | 10.0.0.0 | /30 | R-GEDUNG-A G0/1 = 10.0.0.1 | R-GEDUNG-B G0/1 = 10.0.0.2 |
| Jalur cadangan antar-gedung | 172.16.30.0 | /30 | R-GEDUNG-A G0/2 = 172.16.30.1 | R-GEDUNG-B G0/2 = 172.16.30.2 |
| R-GEDUNG-A ↔ MLS-GEDUNG-A | 172.16.10.0 | /30 | R-GEDUNG-A G0/0 = 172.16.10.1 | — |
| R-GEDUNG-B ↔ MLS-GEDUNG-B | 172.16.20.0 | /30 | R-GEDUNG-B G0/0 = 172.16.20.1 | MLS-GEDUNG-B Fa0/1 = 172.16.20.2 |

> Catatan: pada simulasi, alamat `172.16.10.0/30` tidak lagi dipakai sebagai
> tautan ber-IP. R-GEDUNG-A tersambung ke MLS-GEDUNG-A lewat tautan **trunk**,
> dan setiap VLAN Gedung A mendapat sub-interface tersendiri di R-GEDUNG-A
> (`router-on-a-stick`). Pasangan alamat ini disimpan sebagai catatan
> pengalamatan tautan manajemen.

## Tabel alamat perangkat

### PC di Gedung A

| PC | VLAN | Alamat IP | Subnet mask | Gerbang |
|---|---|---|---|---|
| PC0 | 10 | 192.168.10.10 | 255.255.255.192 | 192.168.10.1 |
| PC1 | 10 | 192.168.10.11 | 255.255.255.192 | 192.168.10.1 |
| PC2 | 10 | 192.168.10.12 | 255.255.255.192 | 192.168.10.1 |
| PC3 | 10 | 192.168.10.13 | 255.255.255.192 | 192.168.10.1 |
| PC4 | 20 | 192.168.10.66 | 255.255.255.224 | 192.168.10.65 |
| PC5 | 20 | 192.168.10.67 | 255.255.255.224 | 192.168.10.65 |
| PC6 | 30 | 192.168.10.98 | 255.255.255.240 | 192.168.10.97 |
| PC7 | 99 | 192.168.10.114 | 255.255.255.240 | 192.168.10.113 |

### PC di Gedung B

| PC | VLAN | Alamat IP | Subnet mask | Gerbang |
|---|---|---|---|---|
| PC8 | 11 | 192.168.11.10 | 255.255.255.192 | 192.168.11.1 |
| PC9 | 11 | 192.168.11.11 | 255.255.255.192 | 192.168.11.1 |
| PC10 | 11 | 192.168.11.12 | 255.255.255.192 | 192.168.11.1 |
| PC11 | 11 | 192.168.11.13 | 255.255.255.192 | 192.168.11.1 |
| PC12 | 21 | 192.168.11.66 | 255.255.255.224 | 192.168.11.65 |
| PC13 | 21 | 192.168.11.67 | 255.255.255.224 | 192.168.11.65 |
| PC14 | 31 | 192.168.11.98 | 255.255.255.240 | 192.168.11.97 |
| PC15 | 199 | 192.168.11.114 | 255.255.255.240 | 192.168.11.113 |

### Perangkat jaringan dan server

| Perangkat | Alamat | Keterangan |
|---|---|---|
| R-GEDUNG-A G0/0.10 | 192.168.10.1 | Gerbang VLAN 10 |
| R-GEDUNG-A G0/0.20 | 192.168.10.65 | Gerbang VLAN 20 |
| R-GEDUNG-A G0/0.30 | 192.168.10.97 | Gerbang VLAN 30 |
| R-GEDUNG-A G0/0.99 | 192.168.10.113 | Gerbang VLAN 99 |
| R-GEDUNG-A G0/0.100 | 192.168.10.145 | Gerbang VLAN 100 |
| MLS-GEDUNG-B Vlan11 | 192.168.11.1 | Gerbang VLAN 11 |
| MLS-GEDUNG-B Vlan21 | 192.168.11.65 | Gerbang VLAN 21 |
| MLS-GEDUNG-B Vlan31 | 192.168.11.97 | Gerbang VLAN 31 |
| MLS-GEDUNG-B Vlan199 | 192.168.11.113 | Gerbang VLAN 199 |
| Server0 | 192.168.10.146 | Server UBBG, VLAN 100 |

## Cara membaca tabel ini

`192.168.10.0 /26` artinya ada 64 alamat. Dua di antaranya tidak bisa dipakai
komputer — satu untuk alamat jaringan dan satu untuk alamat siaran — sehingga
tersisa 62. Alamat paling awal di tiap bagian dipakai sebagai pintu keluar
(gerbang), misalnya `192.168.10.1`.

Blok `/29` untuk zona server memberi 8 alamat, jadi 6 yang bisa dipakai.
Alamat pertama yang tersedia adalah `192.168.10.146`; itulah alamat Server0.
