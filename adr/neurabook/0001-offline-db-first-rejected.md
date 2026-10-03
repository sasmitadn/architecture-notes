# ADR-0001: Offline DB-First (Ditolak)

- **Status:** Rejected
- **Date:** September 2025
- **Deciders:** Sasmita Novitasari
- **Tags:** architecture, storage, offline

## Context

Versi awal NeuraBook dirancang sebagai aplikasi pencatat keuangan **offline-first** dengan database lokal (SQLite / embedded DB di device). Tujuannya agar aplikasi bisa dipakai tanpa koneksi internet dan responsif.

Namun setelah evaluasi, muncul kekhawatiran serius terkait:

1. **Biaya maintenance** — setiap perubahan skema, bug fix, atau migrasi harus didistribusikan ke device user.
2. **Risiko kehilangan data permanen** — jika user uninstall, ganti device, atau device rusak sebelum backup, data hilang total.
3. **Tidak ada single source of truth** — sulit melakukan recovery, audit, atau sinkronisasi antar device.

## Decision

**Kami menolak pendekatan offline DB-first sebagai arsitektur utama NeuraBook.**

Aplikasi tidak akan menyimpan data transaksional utama hanya di database lokal device tanpa backend.

## Consequences

**Positif:**

- Menghindari biaya maintenance jangka panjang yang tinggi.
- Menghilangkan risiko kehilangan data permanen di sisi user.
- Membuka jalan untuk multi-device & sinkronisasi.

**Negatif:**

- Aplikasi menjadi bergantung pada koneksi server.
- Perlu strategi cache untuk menjaga UX tetap cepat (lihat ADR-0003).

**Netral / Risiko:**

- Kehilangan keunggulan "fully offline" yang mungkin diharapkan sebagian user.

## Alternatives Considered

- **Offline-first + cloud sync:** Kompleksitas sinkronisasi (conflict resolution, eventual consistency) dinilai terlalu mahal untuk tahap ini.
- **Hybrid manual backup:** Bergantung pada disiplin user, tidak reliable.

## References

- ADR-0002 (Server-Based)
- ADR-0003 (Local Cache)