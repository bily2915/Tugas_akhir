

| LAPORAN IMPLEMENTASI Fase 3 — Aplikasi Kasir Warung | Universitas Al Azhar Indonesia 27 Juni 2026 |
| :---- | ----: |

# **1\. Setup Project — Struktur File**

Program dibangun dalam satu file utama tanpa dependensi eksternal agar dapat dijalankan langsung dengan Python 3.8+ tanpa instalasi tambahan.

| Struktur File Project kasir\_warung/ ├── kasir\_warung.py        \# Program utama (± 450 baris) ├── test\_kasir\_warung.py   \# Unit test — 48 test cases └── README.md              \# Dokumentasi & AI Usage Log |
| :---- |

Keputusan arsitektur:

* Single-file: memudahkan pengumpulan dan presentasi — satu file untuk semua

* Tidak ada pip install: stdlib only (datetime) — portabel di semua sistem

* Guard \_\_name\_\_: program hanya berjalan jika dieksekusi langsung, aman di-import untuk testing

* Type hints (PEP 484): setiap fungsi dianotasi tipe parameter dan return value

# **2\. Implementasi Bertahap (Incremental)**

Pengembangan dilakukan secara incremental — dimulai dari layer terbawah (utility) naik ke layer controller, sesuai prinsip Bottom-Up dari Top-Down Design.

## **Iterasi 1 — Utility & Format (Bab 2, 5, 6\)**

Fungsi yang dikembangkan pertama: format\_rupiah(), garis(), cetak\_judul(), validasi\_input\_angka(), validasi\_input\_teks(). Alasan: seluruh fungsi layer atas bergantung pada utility ini.

| Contoh — format\_rupiah() dan validasi\_input\_angka() def format\_rupiah(angka: int) \-\> str:     return f"Rp {angka:,}".replace(",", ".")  \# Bab 6: f-string \+ replace def validasi\_input\_angka(prompt: str, min\_val: int \= 0,                           max\_val: int \= None) \-\> int:     while True:                    \# Bab 4: while loop         try:             nilai \= int(input(prompt))   \# Bab 2: str → int             if nilai \< min\_val:           \# Bab 3: seleksi                 continue             if max\_val and nilai \> max\_val:                 continue             return nilai         except ValueError:               \# Bab 3: exception handling             print("  ⚠  Input harus berupa angka bulat\!") |
| :---- |

## **Iterasi 2 — Struktur Data & Business Logic (Bab 7, 8\)**

Inisialisasi menu\_produk (dict of dict), implementasi tambah\_item\_ke\_keranjang(), hitung\_total(), proses\_pembayaran(), update\_stok().

| Contoh — init\_menu\_default() dan tambah\_item\_ke\_keranjang() def init\_menu\_default() \-\> dict:       \# Bab 8: nested dictionary     return {         "P001": {"nama": "Indomie Goreng", "harga": 3500, "stok": 50},         "P002": {"nama": "Aqua 600ml",     "harga": 4000, "stok": 30},         ...  \# 8 produk default     } def tambah\_item\_ke\_keranjang(kode, qty, menu\_produk, keranjang) \-\> tuple:     if kode not in menu\_produk:        \# Bab 8: O(1) dict lookup         return (False, "Kode tidak ditemukan.")     produk \= menu\_produk\[kode\]     if produk\['stok'\] \< qty:           \# Bab 3: validasi stok         return (False, "Stok tidak cukup.")     for item in keranjang:             \# Bab 9: linear search         if item\['kode'\] \== kode:       \# Bab 3: cek duplikat             item\['qty'\] \+= qty             item\['subtotal'\] \= item\['qty'\] \* produk\['harga'\]             return (True, 'Updated')     keranjang.append({...})            \# Bab 7: list.append()     return (True, 'Added') |
| :---- |

## **Iterasi 3 — Searching & Sorting (Bab 9, 10\)**

Implementasi dua algoritma searching (linear O(n) dan binary O(log n)) serta dua algoritma sorting (bubble sort O(n²) manual dan Timsort O(n log n) built-in) sebagai perbandingan akademik.

| Contoh — cari\_produk\_binary() O(log n) def cari\_produk\_binary(kode\_target: str, daftar\_terurut: list) \-\> int:     kiri, kanan \= 0, len(daftar\_terurut) \- 1   \# Bab 2: variabel int     while kiri \<= kanan:                         \# Bab 4: while         tengah \= (kiri \+ kanan) // 2         kode\_tengah \= daftar\_terurut\[tengah\]\[0\] \# Bab 7: indexing         if kode\_tengah \== kode\_target:           \# Bab 3: seleksi             return tengah         elif kode\_tengah \< kode\_target:          \# cari di kanan             kiri \= tengah \+ 1         else:                                    \# cari di kiri             kanan \= tengah \- 1     return \-1 |
| :---- |

## **Iterasi 4 — Rekursi (Bab 11\)**

Implementasi dua fungsi rekursif sebagai alternatif dari versi iteratif. Mendemonstrasikan base case, recursive case, dan tradeoff ruang O(n) vs O(1) iteratif.

| Contoh — hitung\_total\_rekursif() dengan tail recursion style def hitung\_total\_rekursif(keranjang: list, indeks: int \= 0\) \-\> int:     \# Bab 11: BASE CASE — kondisi berhenti     if indeks \>= len(keranjang):         return 0     \# Bab 11: RECURSIVE CASE — kurangi masalah jadi lebih kecil     return keranjang\[indeks\]\['subtotal'\] \\          \+ hitung\_total\_rekursif(keranjang, indeks \+ 1\) |
| :---- |

## **Iterasi 5 — Controller & Main (Bab 3, 4, 5\)**

Implementasi menu\_kasir(), menu\_stok(), menu\_laporan(), menu\_utama(), dan main(). Setiap controller merupakan while True loop dengan routing if/elif/else.

# **3\. Integrasi Konsep Per Bab — Matriks Pemetaan**

| Bab | Konsep | Implementasi | Lokasi Kode |
| :---- | :---- | :---- | :---- |
| **Bab 2** | Variabel & Tipe Data | str, int, bool, list, dict, tuple, set di semua variabel. Type hints pada setiap fungsi. | Semua fungsi |
| **Bab 3** | Seleksi (if/elif/else) | Routing menu (elif chain), validasi kode/stok/uang, flag sukses/gagal. | menu\_utama, proses\_pembayaran, tambah\_item |
| **Bab 4** | Perulangan (for/while) | while True loop menu, for loop iterasi keranjang/riwayat, enumerate(). | Semua fungsi UI & bisnis |
| **Bab 5** | Fungsi | 18 fungsi modular, parameter default, type hints, return tuple. | Seluruh program |
| **Bab 6** | String | f-string, ljust/rjust/center, lower/strip/replace, str.center(). | cetak\_struk, format\_rupiah, tampilkan\_\* |
| **Bab 7** | List/Tuple | Keranjang (list of dict), riwayat, return tuple (bool,int,str), append/pop/clear. | tambah\_item, hapus\_item, simpan\_riwayat |
| **Bab 8** | Dictionary/Set | menu\_produk nested dict, O(1) lookup, set kode unik via get\_kode\_unik(). | init\_menu, update\_stok, menu\_stok |
| **Bab 9** | Searching | cari\_produk\_linear() O(n), cari\_produk\_binary() O(log n). | menu\_stok, tambah\_item\_ke\_keranjang |
| **Bab 10** | Sorting | bubble\_sort\_produk() O(n²) manual \+ sort\_produk\_builtin() Timsort O(n log n). | menu\_stok (demo sort) |
| **Bab 11** | Rekursi | hitung\_total\_rekursif() \+ tampilkan\_laporan\_rekursif() dengan base case \+ tail rekursi. | menu\_kasir, menu\_laporan |
| **Bab 12** | Big-O | Komentar Big-O di setiap fungsi \+ tampilkan\_analisis\_bigO() tabel interaktif. | Semua fungsi (komentar) |
| **Bab 13** | AI Partner (CRIDE) | AI Usage Log di docstring main(), 4 interaksi terdokumentasi. | main(), README.md |

# **4\. Analisis Kompleksitas Big-O (Bab 12\)**

Setiap fungsi dianotasi dengan komentar Big-O langsung di dalam kode. Di bawah ini adalah ringkasan tabel kompleksitas waktu dan ruang:

| Fungsi | Waktu | Ruang | Keterangan |
| :---- | :---- | :---- | :---- |
| format\_rupiah() | **O(1)** | O(1) | Konversi string konstan |
| validasi\_input\_angka() | **O(k)** | O(1) | k \= percobaan input user |
| tambah\_item\_ke\_keranjang() | **O(n)** | O(1) | n \= item keranjang |
| hapus\_item\_keranjang() | **O(n)** | O(1) | Linear search \+ pop |
| hitung\_total() — iteratif | **O(n)** | O(1) | Satu pass keranjang |
| hitung\_total() — rekursif | **O(n)** | O(n) | n frame di call stack |
| proses\_pembayaran() | **O(1)** | O(1) | Aritmetika integer |
| update\_stok() | **O(n)** | O(1) | n iter \+ O(1) dict lookup |
| cari\_produk\_linear() | **O(n)** | O(k) | n produk, k hasil cocok |
| cari\_produk\_binary() | **O(log n)** | O(1) | Prasyarat: sudah terurut |
| bubble\_sort\_produk() | **O(n²)** | O(n) | Nested loop, O(n) copy |
| sort\_produk\_builtin() | **O(n log n)** | O(n) | Python Timsort |

**Perbandingan Kunci**

* Linear Search O(n) vs Binary Search O(log n): binary search 10× lebih cepat pada 1000 produk, tapi butuh data terurut terlebih dahulu.

* Bubble Sort O(n²) vs Timsort O(n log n): untuk 100 produk, bubble sort memerlukan \~10.000 perbandingan, Timsort \~700 perbandingan.

* Rekursi O(n) ruang vs Iterasi O(1) ruang: hitung\_total() iteratif lebih efisien memori; rekursif hanya untuk demonstrasi akademik.

# **5\. AI Usage Log — Bab 13 (Framework CRIDE)**

Sesuai prinsip CRIDE (Context → Request → Iterate → Document → Evaluate), seluruh interaksi dengan AI dicatat di bawah ini. Kode inti ditulis mandiri oleh mahasiswa; AI hanya digunakan untuk dokumentasi dan review.

## **5.1 Tabel Interaksi AI**

| \# | Tahap CRIDE | Prompt ke AI | Output AI | Diedit oleh Mahasiswa? |
| :---- | :---- | :---- | :---- | :---- |
| **1** | Dokumentasi | Bantu tulis docstring lengkap dengan Args, Returns, dan Big-O untuk fungsi tambah\_item\_ke\_keranjang | Template docstring professional | Ya — tambahkan contoh kode konkret dan edge case |
| **2** | Review Kode | Review logika binary search saya, apakah sudah handle edge case list kosong? | Konfirmasi logika benar \+ saran tambah guard len==0 | Ya — tambah validasi \`if not daftar\_terurut\` sebelum loop |
| **3** | Debugging | Kenapa format\_rupiah(10000) menghasilkan 'Rp 10,000' bukan 'Rp 10.000'? | Penjelasan: Python gunakan ',' sebagai ribuan, ganti dengan .replace(',','.') | Ya — implementasi sendiri dengan pemahaman baru |
| **4** | Dokumentasi | Bantu buat tabel markdown Big-O untuk semua 12 fungsi utama | Draft tabel Big-O dengan kolom Waktu dan Ruang | Ya — diverifikasi manual, beberapa nilai dikoreksi |

## **5.2 Evaluasi Penggunaan AI**

| Bagian yang dikerjakan MANDIRI (TANPA AI): Seluruh logika bisnis: tambah\_item, hitung\_total, proses\_pembayaran, update\_stok Algoritma searching: linear search dan binary search — ditulis dari awal Algoritma sorting: bubble sort manual — setiap baris dipahami sendiri Fungsi rekursi: base case, recursive case, parameter akumulasi Desain struktur data: nested dict, list of dict, set kode unik Semua sesi testing dan debugging |
| :---- |

| Bagian yang DIBANTU AI (dengan review & edit): Docstring template — template dibuat AI, konten diisi dan diverifikasi sendiri Review binary search — konfirmasi logika, edge case kode-kosong ditambahkan mandiri Format rupiah debugging — AI menjelaskan masalah, solusi diimplementasikan sendiri Draft tabel Big-O — nilai diverifikasi manual dan beberapa dikoreksi |
| :---- |

# **6\. Hasil Testing — 48 Test Cases**

Semua fungsi bisnis diuji menggunakan unit test manual di test\_kasir\_warung.py. Setiap test case dijalankan tanpa framework eksternal (stdlib only).

| Bab | Fungsi yang Diuji | Jumlah Test | Hasil |
| :---- | :---- | :---- | :---- |
| Bab 6 | format\_rupiah | 3 | **✓ 3/3 Lulus** |
| Bab 8 | Dictionary — init\_menu | 4 | **✓ 4/4 Lulus** |
| Bab 8 | Set — get\_kode\_unik | 3 | **✓ 3/3 Lulus** |
| Bab 7+9 | tambah\_item\_ke\_keranjang | 9 | **✓ 9/9 Lulus** |
| Bab 7 | hapus\_item\_keranjang | 3 | **✓ 3/3 Lulus** |
| Bab 4+7 | hitung\_total (iteratif) | 2 | **✓ 2/2 Lulus** |
| Bab 11 | hitung\_total\_rekursif | 2 | **✓ 2/2 Lulus** |
| Bab 2+3 | proses\_pembayaran | 5 | **✓ 5/5 Lulus** |
| Bab 8 | update\_stok | 1 | **✓ 1/1 Lulus** |
| Bab 9 | cari\_produk\_linear | 4 | **✓ 4/4 Lulus** |
| Bab 9 | cari\_produk\_binary | 4 | **✓ 4/4 Lulus** |
| Bab 10 | bubble\_sort\_produk | 2 | **✓ 2/2 Lulus** |
| Bab 10 | sort\_produk\_builtin | 2 | **✓ 2/2 Lulus** |
| Bab 7+11 | simpan & riwayat rekursif | 4 | **✓ 4/4 Lulus** |

| ✓  HASIL: 48/48 TEST LULUS — 100% python test\_kasir\_warung.py |
| :---: |

# **7\. Refleksi & Pembelajaran**

## **7.1 Tantangan yang Dihadapi**

* Desain counter\_id: awalnya menggunakan int biasa, tapi fungsi Python tidak bisa memodifikasi int primitif dari scope luar. Solusi: dikemas dalam list\[int\] agar bersifat mutable.

* Binary search edge case: saat daftar kosong, algoritma langsung return \-1 tanpa masuk loop. Kasus ini dideteksi saat testing dan ditambahkan guard condition.

* Format struk agar rata kolom kanan: eksperimen dengan berbagai kombinasi ljust/rjust sebelum menemukan format yang konsisten di berbagai panjang nama produk.

## **7.2 Keputusan Desain**

* Pilih dict untuk menu\_produk (bukan list) karena O(1) lookup sering dibutuhkan saat transaksi, vs O(n) jika menggunakan list.

* Fungsi rekursif diimplementasikan sebagai ALTERNATIF (bukan pengganti) fungsi iteratif — keduanya hadir untuk menunjukkan perbandingan.

* Bubble sort tetap diimplementasikan meski tidak efisien — untuk demonstrasi akademik O(n²) vs O(n log n).

## **7.3 Yang Akan Dikembangkan di Fase Finalisasi**

* Persistensi data: simpan menu\_produk ke file JSON agar data tidak hilang saat program ditutup.

* Fitur diskon: tambah field diskon pada dict transaksi.

* Laporan harian: filter riwayat berdasarkan tanggal.

