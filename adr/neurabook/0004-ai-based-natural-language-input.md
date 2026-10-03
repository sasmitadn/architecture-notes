# ADR-0004: AI-Based Natural Language Input

- **Status:** Accepted
- **Date:** Januari 2026
- **Deciders:** Sasmita Novitasari
- **Tags:** ux, ai, input, nlp

## Context

Setelah server-based + cache lokal berjalan (ADR-0002, ADR-0003), UX membaca data sudah cepat. Namun **input transaksi masih terasa lambat**.

Alur input manual saat ini:

1. Klik tombol `+`
2. Pilih kategori
3. Input amount
4. Input note
5. Klik Save

→ **5 aksi** untuk 1 transaksi. Terlalu banyak untuk aktivitas yang dilakukan puluhan kali sehari.

## Decision

**Kami akan menambahkan input berbasis teks/voice bebas di home screen, yang otomatis di-parse oleh AI menjadi transaksi terstruktur.**

Contoh:

> User input: *"makan siang nasi padang 25 ribu"*

AI parse →

```json
{
  "type": "expense",
  "category": "food",
  "amount": 25000,
  "note": "nasi padang"
}
```

→ auto-save (dengan opsi konfirmasi/undo).

Dukungan:

- **Teks bebas** (primary).

- **Voice-to-text** sebagai input alternatif (lihat Open Questions).

## Consequences

**Positif:**

- Mengurangi aksi dari 5 klik → 1 aksi (input + enter).

- UX jauh lebih natural, khususnya untuk mobile.

- Diferensiasi produk: "Neura" = neural/AI-driven.

- Memanfaatkan AI untuk structured extraction — cocok dengan nama produk.

**Negatif:**

- Perlu **fallback manual** jika AI salah parse atau gagal.

- Biaya API AI (jika pakai LLM cloud) atau kompleksitas model lokal.

- Perlu **confirmation step** opsional untuk mencegah salah catat.

- Perlu handling multi-bahasa & slang ("25rb", "25k", "dua lima ribu").

**Netral / Risiko:**

- Akurasi AI tidak 100% → wajib ada UI koreksi cepat.

- Ketergantungan pada provider AI pihak ketiga (jika cloud).

## Alternatives Considered

- **Form manual saja:** Ditolak — masalah awal (terlalu banyak klik).

- **Quick-add preset (tombol nominal):** Kurang fleksibel, tidak scalable untuk kategori beragam.

- **Rule-based parser (regex):** Cepat & murah, tapi rapuh terhadap variasi bahasa. Bisa jadi fallback, bukan primary.

## Open Questions

- **ADR-006 (TBD):** Pilihan AI provider — OpenAI? Gemini? Model lokal (mis. Llama)?

- **ADR-008 (TBD):** Voice-to-text engine — on-device (Whisper.cpp) vs cloud?

- Format konfirmasi: auto-save + undo, atau preview-then-save?

## References

- ADR-0002 (Server-Based)

- ADR-0003 (Local Cache)