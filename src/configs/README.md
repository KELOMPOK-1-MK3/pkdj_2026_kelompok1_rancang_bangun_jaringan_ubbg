# Salinan Konfigurasi

Folder ini menyimpan salinan konfigurasi setiap perangkat dalam bentuk teks,
supaya perubahannya bisa ditelusuri lewat riwayat commit.

Seluruh berkas di sini **diambil dari `show running-config` perangkat yang
sungguh berjalan di Packet Tracer** — bukan rancangan di atas kertas. Jadi isi
berkas ini dan isi simulasi tidak boleh berbeda.

| Berkas | Perangkat | Peran |
|---|---|---|
| `router-gedung-a.txt` | R-GEDUNG-A (2911) | Gerbang Gedung A; router-on-a-stick untuk VLAN 10/20/30/99/100; titik pemasangan ACL tamu |
| `router-gedung-b.txt` | R-GEDUNG-B (2911) | Penghubung Gedung B ke Gedung A (murni transit) |
| `multilayer-switch-gedung-a.txt` | MLS-GEDUNG-A (3560-24PS) | Transit lapis dua murni — tidak ada SVI |
| `multilayer-switch-gedung-b.txt` | MLS-GEDUNG-B (3560-24PS) | Pusat inter-VLAN routing Gedung B (SVI 11/21/31/199) |
| `access-switch-a1.txt` | SW-A1 (2960-24TT) | VLAN 10 Kelas, VLAN 20 Laboratorium |
| `access-switch-a2.txt` | SW-A2 (2960-24TT) | VLAN 30 Dosen/Staf, VLAN 99 Tamu |
| `access-switch-b1.txt` | SW-B1 (2960-24TT) | VLAN 11 Kelas, VLAN 21 Laboratorium |
| `access-switch-b2.txt` | SW-B2 (2960-24TT) | VLAN 31 Dosen/Staf, VLAN 199 Tamu |
| `server-ubbg.txt` | Server0 (Server-PT) | Pengaturan IP dan layanan server |

## Dua hal yang berubah dari rancangan awal

Keduanya ditemukan saat pengujian, dan sudah tercermin di berkas di atas.

### 1. MLS-GEDUNG-A tidak bisa `ip routing`

Rancangan awal menaruh pusat inter-VLAN routing di MLS-GEDUNG-A. Saat
dijalankan, switch itu menolak `ip routing` dan SVI-nya tidak pernah muncul —
model yang terpasang berperilaku seperti WS-C2960 (lapis dua), bukan 3560.

**Akibatnya:** inter-VLAN routing Gedung A dipindahkan ke R-GEDUNG-A memakai
sub-interface (`router-on-a-stick`), dan MLS-GEDUNG-A menjadi transit lapis dua
murni tanpa SVI. Inter-VLAN routing Gedung B tetap di MLS-GEDUNG-B karena
model di sana benar-benar 3560 dan SVI-nya jalan normal.

### 2. Jalur cadangan memakai floating static, bukan routing dinamis

Rancangan awal menyebut routing dinamis antar-gedung. Yang dipakai di simulasi
adalah **static route dengan administrative distance** — rute utama distance 1,
rute cadangan distance 200. Perpindahan terjadi langsung saat status antarmuka
turun, tanpa menunggu timer protokol routing.

## Catatan teknis

- **Nomor antarmuka sudah sesuai perangkat sungguhan.** Tidak perlu dicocokkan
  ulang; berkas ini hasil salinan langsung.
- **`encapsulation dot1q` tidak muncul di running-config beberapa port trunk.**
  Pada model 2960, encapsulation terdeteksi otomatis (`n-802.1q`). Yang
  membuktikan port itu trunk adalah `show interfaces trunk`, bukan ada-tidaknya
  baris encapsulation.
- **Tidak ada sandi di berkas ini.** Sandi perangkat tidak ikut masuk
  repository publik.
