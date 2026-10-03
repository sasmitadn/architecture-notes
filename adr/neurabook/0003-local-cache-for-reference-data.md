# ADR-0003: Local Cache untuk Data Referensi

- **Status:** Accepted
- **Date:** Januari 2026
- **Deciders:** Sasmita Novitasari
- **Tags:** caching, performance, ux

## Context

Setelah migrasi ke server-based (ADR-0002), muncul masalah UX:

Setiap kali user membuka screen, data di-load ulang dari server — termasuk data yang **jarang berubah** seperti:

- Daftar kategori transaksi
- Daftar akun / wallet
- Preferensi user

Dampak:

- Beban server meningkat sia-sia.
- User menunggu loading untuk data yang seharusnya bisa langsung tampil.
- Boros bandwidth & baterai (mobile).

## Decision

**Kami akan menyimpan data referensi di local storage (non-database) pada sisi client.**

Implementasi per platform:

- **Web:** `IndexedDB` atau `localStorage`.
- **Mobile:** `AsyncStorage`, `SharedPreferences`, atau equivalent.
- **In-memory cache** untuk sesi berjalan.

Mekanisme:

1. **First open:** fetch semua data referensi dari server (versi terbaru).
2. **Setelahnya:** tampilkan dari cache lokal, tanpa hit server berulang.
3. **Refresh:** berkala (TTL) atau on-demand (mis. pull-to-refresh, atau event dari server).

Yang **TIDAK** di-cache dengan cara ini:

- Data transaksional kritis (transaksi keuangan).
- Data yang butuh konsistensi real-time.

## Consequences

**Positif:**

- Latency turun drastis (instant render).
- Beban server berkurang signifikan.
- Hemat bandwidth & baterai.
- UX lebih halus (tidak ada loading spinner berulang).

**Negatif:**

- Perlu strategi **cache invalidation** (versioning, TTL, atau push event).
- Potensi data stale jika refresh gagal.

**Netral / Risiko:**

- Kompleksitas client bertambah (state management cache).

## Alternatives Considered

- **Selalu fetch dari server:** Sederhana, tapi lambat & boros (masalah awal).
- **Full offline DB (SQLite di client):** Ditolak karena maintenance mahal (lihat ADR-0001).
- **Service Worker / HTTP cache:** Tidak cukup fleksibel untuk data terstruktur (kategori, preferensi).

## Open Questions

- **ADR-005 (TBD):** Strategi cache invalidation final — pakai versi, TTL, atau push event dari server?
- Apakah perlu **stale-while-revalidate** pattern?

## References

- ADR-0002 (Server-Based)
- ADR-0001 (Offline DB-First Ditolak)