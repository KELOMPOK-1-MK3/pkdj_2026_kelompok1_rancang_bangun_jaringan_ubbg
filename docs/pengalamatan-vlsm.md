# VLSM — Alokasi Alamat Jaringan Klinik Kesehatan Kecil

Blok induk: **192.168.10.0/24** (254 alamat host).
Metode: VLSM — subnet terbesar dilayani lebih dulu, sehingga ruang alamat terpakai efisien.

## Urutan kebutuhan

| # | Segmen | Perkiraan host | Prefix | Alamat jaringan |
|---|---|---|---|---|
| 1 | VLAN 10 — Pendaftaran | 40 | /26 | 192.168.10.0 |
| 2 | VLAN 20 — Poliklinik | 40 | /26 | 192.168.10.64 |
| 3 | VLAN 30 — Farmasi | 20 | /27 | 192.168.10.128 |
| 4 | VLAN 40 — Laboratorium | 20 | /27 | 192.168.10.160 |
| 5 | VLAN 50 — Manajemen | 10 | /28 | 192.168.10.192 |
| 6 | VLAN 99 — Tamu | 10 | /28 | 192.168.10.208 |
| 7 | Sisa | — | /28 | 192.168.10.224 (dicadangkan) |
| 8 | Zona server | 6 | /29 | 192.168.10.240 |

Total **alamat host yang dapat dipakai**: 62+62+30+30+14+14+14+6 = 232 alamat.
Kalau dihitung per blok, kedelapan blok itu menghabiskan 248 dari 256 alamat;
yang tersisa **192.168.10.248–.255** (satu blok /29, 6 alamat host) dan
disisakan untuk pengembangan.

> Dua blok terakhir berbeda perannya: `192.168.10.224/28` **dicadangkan** untuk
> perluasan VLAN di masa depan (belum dipakai perangkat apa pun), sedangkan
> `192.168.10.240/29` sudah dipakai Zona Server.

## Tabel alamat per VLAN

| VLAN | Nama | Prefix | Alamat jaringan | Gateway (SVI Core-Switch-L3) | Rentang DHCP | Siaran |
|---|---|---|---|---|---|---|
| 10 | Pendaftaran | /26 | 192.168.10.0 | 192.168.10.1 | .10 – .50 | 192.168.10.63 |
| 20 | Poliklinik | /26 | 192.168.10.64 | 192.168.10.65 | .74 – .114 | 192.168.10.127 |
| 30 | Farmasi | /27 | 192.168.10.128 | 192.168.10.129 | .138 – .158 | 192.168.10.159 |
| 40 | Laboratorium | /27 | 192.168.10.160 | 192.168.10.161 | .170 – .190 | 192.168.10.191 |
| 50 | Manajemen | /28 | 192.168.10.192 | 192.168.10.193 | .202 – .212 | 192.168.10.207 |
| 99 | Tamu | /28 | 192.168.10.208 | 192.168.10.209 | .218 – .222 | 192.168.10.223 |
| — | Server | /29 | 192.168.10.240 | 192.168.10.241 | statis | 192.168.10.247 |

**Rumus yang dipakai:** jumlah host per prefix = 2^(32-prefix) − 2.
Contoh /26 → 2^6 − 2 = 62 host; gateway memakai alamat pertama yang tersedia.

> Gateway dan DHCP: alamat .1, .65, .129, .161, .193, .209, .241 dipakai gateway SVI
> dan **dikecualikan** dari kumpulan DHCP, begitu pula alamat server layanan.

## Alamat statis perangkat

| Perangkat | Antarmuka | Alamat |
|---|---|---|
| Router-Klinik | Gig0/0/0 (ke internet) | DHCP dari penyedia / 10.0.0.2 pada simulasi |
| Router-Klinik | Gig0/0/1 (ke Core-Switch-L3) | 172.16.1.1/30 |
| Core-Switch-L3 | Gig1/0/1 (ke Router-Klinik) | 172.16.1.2/30 |
| Core-Switch-L3 | Vlan 10–99 | masing-masing gateway pada tabel di atas |
| Server-Layanan | FastEthernet0 | 192.168.10.242/29 |

## Rencana routing

- **Router-Klinik ↔ Core-Switch-L3:** tautan /30 di 172.16.1.0/30.
- **OSPF area 0:** keduanya mengumumkan jaringan yang dimiliki; VLAN internal
  diumumkan oleh Core-Switch-L3, jaringan luar oleh Router-Klinik.
- **Rute bawaan:** Router-Klinik memakai rute bawaan ke internet, lalu
  mengumumkannya ke OSPF supaya Core-Switch-L3 ikut mengarahkan trafik keluar.
- **NAT (overload):** diterapkan pada antarmuka luar Router-Klinik agar alamat
  privat 192.168.10.0/24 dan 172.16.1.0/30 dapat keluar.

## Rencana VLAN dan port

| VLAN | Nama | Zona |
|---|---|---|
| 10 | Pendaftaran | SW-Access-1 |
| 20 | Poliklinik | SW-Access-1 |
| 30 | Farmasi | SW-Access-2 |
| 40 | Laboratorium | SW-Access-2 |
| 50 | Manajemen | SW-Access-2 |
| 99 | Tamu | SW-Access-3 |
| 100 | Server | SW-Server |

Port trunk 802.1Q dipakai pada tautan switch-ke-switch: VLAN 10, 20, 30, 40, 50,
99, 100 dilewatkan seluruhnya, VLAN 1 dikeluarkan (`switchport trunk native vlan 999`
dengan VLAN 999 sebagai VLAN buangan) supaya trafik tak bertag tidak tercampur.

Port ke perangkat pengguna disetel **access** sesuai VLAN zona masing-masing.
