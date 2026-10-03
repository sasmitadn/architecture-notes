# ADR-0002: Server-Based dengan Laravel + Filament + Orion

- **Status:** Accepted
- **Date:** Desember 2025
- **Deciders:** Sasmita Novitasari
- **Tags:** backend, framework, api

## Context

Setelah pendekatan offline DB-first ditolak (ADR-0001), NeuraBook diubah menjadi aplikasi **server-based**. Kebutuhan:

- Single source of truth untuk data user.
- Maintenance terpusat.
- Development cepat (waktu terbatas, ingin fokus ke fitur & UX).
- API siap pakai untuk client (mobile/web).

## Decision

**Kami akan membangun backend NeuraBook menggunakan:**

1. **Laravel** — sebagai framework backend utama (ekosistem matang, tooling lengkap).
2. **Filament** — untuk admin panel, CRUD internal, monitoring, dan operasional.
3. **Orion** — untuk auto-generate REST API dari Eloquent model, mengurangi boilerplate endpoint.

Arsitektur: Client → Orion (REST API) → Laravel → Database Admin → Filament Panel → Laravel → Database

## Consequences

**Positif:**

- Development cepat: Orion menghilangkan penulisan controller/route manual untuk CRUD.
- Filament mempercepat pembuatan panel admin & internal tooling.
- Server menjadi single source of truth → data aman & audit-able.
- Maintenance terpusat: update sekali, semua user dapat versi terbaru.

**Negatif:**

- Setiap screen client berpotensi melakukan network call → beban server & latency UX.
- Bergantung pada koneksi server (mitigasi di ADR-0003).

**Netral / Risiko:**

- Lock-in ke ekosistem Laravel/Filament/Orion (dinilai acceptable).

## Alternatives Considered

- **Node.js (NestJS/Express):** Familiar, tapi ekosistem admin panel tidak sekaya Filament.
- **Supabase / Firebase:** Cepat, tapi kurang fleksibel untuk logika bisnis kompleks & biaya bisa membengkak.
- **Manual REST API tanpa Orion:** Lebih kontrol, tapi development lebih lambat.

## References

- ADR-0001 (Offline DB-First Ditolak)
- ADR-0003 (Local Cache)
- https://laravel.com
- https://filamentphp.com
- https://orion.tailflow.org