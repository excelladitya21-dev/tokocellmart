# Cellmart — Sistem Informasi Penjualan

Aplikasi Laravel untuk proyek pembelajaran basis data lanjutan. Aplikasi mencakup pengelolaan pelanggan, kategori, produk, transaksi penjualan, laporan, serta skrip praktik MariaDB/MySQL.

Pada formulir transaksi, pilih produk atau pindai barcode barang untuk memasukkannya ke keranjang; harga satuan dan total belanja diperbarui otomatis. Isi nilai barcode sesuai kode pada kemasan produk, dan gunakan reader barcode yang berfungsi seperti keyboard. Setiap transaksi juga memiliki halaman struk yang dapat dicetak dari detail transaksi.

Daftarkan member baru langsung dari menu **Kartu member** dengan mengisi nama lengkap, nomor HP, dan UID RFID; email serta kota bersifat opsional. Pelanggan yang sudah terdaftar juga dapat dipilih. Saat transaksi, fokuskan reader pada kolom pemindaian (reader keyboard-wedge) atau masukkan UID lalu tekan Enter; kartu aktif akan memilih pelanggan dan dicatat pada transaksi.

## Kebutuhan

- PHP 8.3 atau lebih baru dan ekstensi PDO MySQL
- Composer
- MySQL/MariaDB (XAMPP sudah menyertakan MariaDB)

## Menjalankan aplikasi di XAMPP

1. Jalankan Apache dan MySQL dari XAMPP Control Panel.
2. Buat database `db_tokokita` melalui phpMyAdmin.
3. Sesuaikan koneksi database di file `.env`:

   ```dotenv
   APP_NAME=Cellmart
   APP_LOCALE=id
   APP_FALLBACK_LOCALE=id
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=db_tokokita
   DB_USERNAME=root
   DB_PASSWORD=
   ```

4. Jalankan perintah berikut dari folder proyek:

   ```bash
   composer install
   php artisan key:generate
   php artisan migrate --seed
   php artisan serve
   ```

5. Buka `http://127.0.0.1:8000`.

Untuk praktik berbasis SQLite, ubah `DB_CONNECTION=sqlite` dan jalankan migrasi serta seeder yang sama. View laporan dibuat oleh migrasi Laravel.

## Akun pembeli dan kasir

- Pembeli dapat membuat akun di `/daftar`, masuk dari `/masuk/pembeli`, memilih produk, dan mencatat pembayaran tunai saat checkout. Pembeli hanya dapat melihat pesanan miliknya sendiri.
- Kasir dapat mendaftar di `/daftar/kasir` atau dibuat melalui `php artisan app:create-cashier`, lalu masuk dari `/masuk/kasir`. Area kasir hanya menyediakan dashboard, pembuatan transaksi, riwayat, dan struk.
- Admin pertama dapat mendaftar melalui `/daftar/admin` selama belum ada akun admin, lalu masuk dari `/masuk/admin`. Setelah admin pertama dibuat, pendaftaran publik ditutup dan admin yang sudah login dapat menambah akun lain di `/admin/akun`. Perintah `php artisan app:create-admin` juga tersedia untuk setup melalui terminal.
- Pembayaran saat ini dicatat sebagai tunai dan lunas di aplikasi; belum terhubung ke payment gateway.

## Data contoh

Seeder membuat 10 pelanggan dan kartu RFID demo, 5 kategori, 12 produk, serta 20 transaksi dengan dua rincian barang per transaksi. Menjalankan ulang seeder tidak menggandakan contoh tersebut.

## Pemetaan jobsheet

- **Tabel dan index:** tabel pelanggan, kartu member, produk, kategori, transaksi, detail transaksi, dan log; indeks tersedia pada UID kartu, barcode dan kode produk, kategori, pelanggan, dan tanggal transaksi.
- **View:** `view_laporan_penjualan` (JOIN) dan `view_total_belanja_pelanggan` (agregasi).
- **JOIN dan subquery:** halaman **Laporan & SQL** menampilkan detail penjualan, pelanggan tanpa transaksi, transaksi di atas rata-rata, dan ringkasan pelanggan.
- **Stored procedure, function, trigger, UNION, TCL, dan EXPLAIN:** tersedia dalam `database/sql/db_tokokita.sql`. Jalankan dengan phpMyAdmin atau MySQL CLI pada database MariaDB/MySQL.
- **Transaksi aplikasi:** penyimpanan penjualan dan pengurangan stok dibungkus satu transaksi database; bila stok tidak mencukupi, semua perubahan dibatalkan.

Migrasi Laravel membuat tabel dan view. Produk lama mendapatkan nilai barcode awal dari kode produknya; perbarui dengan barcode pada kemasan agar reader bisa mencocokkan barang fisik. Skrip SQL menyediakan contoh database mandiri beserta rutin dan trigger khusus MariaDB/MySQL. Jangan jalankan skrip SQL pada SQLite.

## Pengujian

```bash
php artisan test
```
