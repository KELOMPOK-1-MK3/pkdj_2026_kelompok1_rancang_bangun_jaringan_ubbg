# Berkas Simulasi

Berkas simulasi Cisco Packet Tracer diletakkan pada folder ini dengan nama:

```
jaringan-ubbg-dua-gedung.pkt
```

Berkas ini adalah hasil akhir yang sudah dijalankan dan diuji — tujuh dari
delapan skenario pada [`docs/pengujian.md`](../docs/pengujian.md) terbukti.

## Cara membuka

1. Buka Cisco Packet Tracer (diuji pada versi 9.0).
2. **File > Open**, pilih `jaringan-ubbg-dua-gedung.pkt`.
3. Tunggu 1–2 menit hingga seluruh perangkat selesai memuat konfigurasi.
4. Pastikan mode **Realtime** aktif (bukan Simulation).

## Aturan berkas

- Simpan berkas setelah setiap perubahan konfigurasi yang berarti.
- Sertakan nomor versi Packet Tracer pada pesan commit, misalnya
  `feat(topologi): pasang ACL tamu (PT 9.0)`.
- Jangan menyimpan berkas cadangan bertanda `~` atau berkas sementara.
- Ekspor diagram topologi sebagai gambar ke `docs/gambar/` agar tetap dapat
  diperlihatkan tanpa membuka Packet Tracer.

> **Penting:** Packet Tracer tidak memiliki penyimpanan otomatis. Simpan berkas
> secara berkala (Cmd+S) setiap 10–15 menit. Kalau aplikasi berhenti mendadak,
> pekerjaan yang belum disimpan akan hilang.

## Isi simulasi

| Bagian | Jumlah | Keterangan |
|---|---|---|
| Router | 2 | R-GEDUNG-A dan R-GEDUNG-B (Cisco 2911) |
| Multilayer switch | 2 | MLS-GEDUNG-A (transit L2) dan MLS-GEDUNG-B (SVI) |
| Access switch | 4 | SW-A1, SW-A2, SW-B1, SW-B2 (Cisco 2960-24TT) |
| Server | 1 | Server0, VLAN 100 Gedung A |
| PC | 16 | PC0–PC7 Gedung A, PC8–PC15 Gedung B |
| Jalur antar-gedung | 2 | Utama `10.0.0.0/30` dan cadangan `172.16.30.0/30` |
