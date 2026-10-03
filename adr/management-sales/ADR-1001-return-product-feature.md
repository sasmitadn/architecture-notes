# ADR-001: Desain Fitur Return Product

## Status

Accepted — September 2026

## Context

Leader meminta fitur Return Product dibuat menyesuaikan dengan hasil contoh raw data Excel.

Pada saat permintaan datang, belum ada definisi jelas tentang:

Bagaimana struktur input data yang benar Apa saja field wajib per item Bagaimana menangani kasus di mana satu dokumen retur berisi banyak item Bagaimana mengklasifikasikan produk yang diretur Saya bertanggung jawab menerjemahkan kebutuhan ini menjadi desain sistem yang bisa dieksekusi oleh tim.

## Keputusan yang Diambil

### 1. Mengubah input dari "upload multiple image" menjadi "input by item product"

**Awalnya**: Fitur direncanakan hanya untuk upload multiple image product yang diretur.

**Keputusan**: Setelah diskusi dengan tim, kami sepakat mengubahnya menjadi input per item product, bukan hanya upload gambar.

**Alasan**: Upload image saja tidak cukup untuk menangkap struktur data retur. Admin membutuhkan detail per item (produk apa, berapa, kenapa diretur). Image hanya bukti visual, bukan data utama.

### 2. Menambahkan sistem klasifikasi pada item

**Konteks**: Setelah koordinasi dengan bagian admin, mereka membutuhkan klasifikasi tiap product — sesuatu yang tidak ada di permintaan awal.

**Keputusan**: Saya mendesain struktur di mana setiap item wajib memiliki:

**Klasifikasi** produk Product items (daftar produk yang diretur) Foto produk Nomor SPPB Catatan **Alasan**: Tanpa klasifikasi, admin tidak bisa memproses retur secara terstruktur. Field wajib memastikan data tidak setengah-setengah.

### 3. Mekanisme "1 klasifikasi bisa ditambahkan berulang" dalam satu SPPB

**Konteks**: Ternyata ditemukan bahwa 1 SPPB bisa berisi campuran berbagai klasifikasi.

**Awalnya**: Desain awal mengasumsikan 1 SPPB = 1 klasifikasi.

**Keputusan**: Saya dengan tim meminta admin untuk mengedukasi sales agar SPPB dipisahkan per **klasifikasi**. Sebagai konsekuensinya, saya mendesain mekanisme di mana 1 klasifikasi yang sama bisa ditambahkan berulang dalam satu SPPB.

## Alasan:

Mengubah struktur SPPB di level operasional lebih mahal daripada menyesuaikan desain input. Desain yang fleksibel (1 klasifikasi bisa berulang) mengakomodasi kondisi nyata tanpa memaksa perubahan besar di lapangan. Trade-off yang diterima:

Admin perlu mengedukasi sales → tambahan beban operasional Sistem jadi lebih kompleks (perlu handle duplikasi klasifikasi) Tapi: data jadi lebih bersih, dan sistem tidak perlu dirombak ulang saat ada kasus campuran Alternatives Considered

**Alternatif 1: Hanya upload multiple image (sesuai permintaan awal)**

Ditolak karena: Tidak menangkap data terstruktur. Admin akan kesulitan memproses retur karena tidak ada detail per item.

**Alternatif 2: 1 SPPB = 1 klasifikasi (paksa di level sistem)**

Ditolak karena: Kondisi nyata di lapangan tidak sesuai. Sales sering mencampur klasifikasi dalam satu SPPB. Memaksa sistem akan membuat data tidak akurat atau sales harus input ulang.

**Alternatif 3: Sistem otomatis memisahkan SPPB per klasifikasi**

Ditolak karena: Terlalu kompleks untuk kebutuhan saat ini, dan mengubah alur kerja sales secara signifikan tanpa persiapan.

## Consequences

Positif:

- Data retur jadi terstruktur per item, bukan hanya gambar
- Klasifikasi produk tersedia di level item, memudahkan admin
- Sistem fleksibel terhadap kondisi nyata (1 SPPB bisa punya banyak klasifikasi)

Negatif:

- Kompleksitas sistem meningkat (perlu handle duplikasi klasifikasi)
- Ada beban edukasi ke sales
- Perlu koordinasi lintas tim (admin, sales, engineering)

## Yang Saya Pelajari

- Permintaan awal sering tidak lengkap. Leader minta "sesuai Excel", tapi detailnya baru muncul setelah koordinasi dengan pihak yang bersangkutan.
- Keputusan arsitektur tidak selalu tentang teknologi. Kadang keputusan terbaik adalah mengubah proses bisnis (edukasi sales) daripada memaksa sistem.
- Trade-off harus eksplisit. Saya memilih sistem yang lebih kompleks tapi fleksibel, daripada sistem sederhana yang tidak sesuai kenyataan.
- Koordinasi lintas tim adalah bagian dari arsitektur. Saya tidak bisa memutuskan sendirian -- perlu admin, sales, dan tim engineering.