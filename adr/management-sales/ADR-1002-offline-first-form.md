# ADR-0002: Offline-First Form untuk Return Product

## Status

Accepted — September 2026

Terkait: [ADR-0001: Desain Fitur Return Product](https://./0001-return-product-feature.md)

## Context

Fitur Return Product (lihat ADR-0001) mengharuskan sales **memfoto setiap produk yang diretur, satu per satu**, lalu melampirkan data klasifikasi, nomor SPPB, dan catatan.

Namun kondisi lapangan:

- **Koneksi internet sangat tidak stabil** — bisa hilang di tengah proses input

- **Device yang digunakan low RAM** — aplikasi bisa tertutup paksa (force close) atau di-reopen oleh sistem

- **Proses input memakan waktu** — memfoto produk satu per satu berarti user bisa berpindah-pindah, menutup app, atau kehabisan RAM di tengah jalan

Kalau data hanya disimpan di server (realtime), maka:

- Foto yang sudah diambil bisa hilang saat koneksi terputus

- Data yang sudah diisi bisa hilang saat app di-reopen

- User harus mengulang dari awal — ini **beban terbesar di lapangan**

## Keputusan

Saya memutuskan untuk menerapkan **offline-first architecture** pada form Return Product:

1. **Semua data disimpan lokal terlebih dahulu** (foto + field data) sebelum dikirim ke server.

2. **Foto disimpan di local storage** (bukan hanya di memori), sehingga aman meskipun app di-reopen atau kehabisan RAM.

3. **Sinkronisasi ke server dilakukan secara background** — tidak memblokir user, dan bisa dilanjutkan kapan saja saat koneksi tersedia.

4. **User bisa melihat status** setiap item: "tersimpan lokal", "menunggu sinkronisasi", atau "tersinkronisasi".

## Alasan

- **Data loss adalah risiko terbesar.** Kalau sales sudah memfoto 20 produk, lalu koneksi putus atau app tertutup, kehilangan semua data itu **tidak bisa diterima** — secara operasional maupun emosional.

- **Koneksi tidak bisa diandalkan.** Memaksa realtime berarti user harus berada di tempat dengan sinyal bagus, yang tidak realistis untuk sales di lapangan.

- **Device low RAM memperparah risiko.** Android bisa menutup app kapan saja untuk membebaskan memori. Kalau data hanya di memori, hilang.

- **Offline-first memberi kontrol ke user.** Sales bisa input kapan saja, di mana saja, tanpa takut kehilangan data.

## Alternatives Considered

### Alternatif 1: Realtime-only (langsung kirim ke server)

**Ditolak karena:** Koneksi tidak stabil. Setiap kali koneksi putus, data hilang. Ini akan membuat fitur tidak bisa dipakai di lapangan.

### Alternatif 2: Simpan di memori (in-memory state) lalu kirim saat selesai

**Ditolak karena:** Device low RAM. App bisa di-reopen atau force close, dan data di memori hilang. Ini justru **risiko paling besar** yang ingin dihindari.

### Alternatif 3: Manual save ke server (tombol "Simpan" per item)

**Ditolak karena:** User tetap butuh koneksi untuk menyimpan. Kalau koneksi putus di tengah, data tetap berisiko. Juga menambah friksi: user harus ingat menyimpan setiap kali.

### Alternatif 4: Simpan lokal tanpa background sync (upload manual)

**Ditolak karena:** User harus ingat meng-upload nanti. Risiko data menumpuk di device dan tidak pernah terkirim.

## Consequences

**Positif:**

- Data tidak hilang meskipun koneksi putus atau app di-reopen

- User bisa bekerja tanpa bergantung pada koneksi

- Foto tersimpan aman di local storage

**Negatif:**

- Kompleksitas sistem meningkat: perlu mekanisme sinkronisasi antara data yang sudah masuk ke server dengan data yang ada di local

- Proses hanya bisa dilakukan 1 user

- Perlu manajemen storage: foto bisa menumpuk di device

- Perlu indikator status agar user tahu data sudah tersinkronisasi atau belum

**Trade-off yang diterima:**

- Sistem lebih rumit, tapi **data loss dicegah** — ini prioritas utama

- Storage device terpakai, tapi bisa dikelola dengan pembersihan otomatis setelah sinkronisasi berhasil

## Yang Saya Pelajari

1. **Konteks lapangan menentukan arsitektur.** Keputusan offline-first bukan karena "trend", tapi karena kondisi nyata: koneksi tidak stabil + device low RAM.

2. **Risiko terbesar bukan fitur tidak jalan, tapi data hilang.** User bisa memaafkan aplikasi lambat, tapi tidak bisa memaafkan data yang hilang setelah mereka susah payah memfoto.

3. **Offline-first bukan sekadar "simpan lokal".** Ini mencakup antrian sinkronisasi, penanganan konflik, indikator status, dan manajemen storage.

4. **Keputusan arsitektur harus mempertimbangkan user di lapangan**, bukan hanya kondisi ideal di kantor.