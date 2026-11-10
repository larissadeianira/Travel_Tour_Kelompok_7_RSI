# Travel Tour

Aplikasi web pemesanan paket wisata berbasis Laravel. Pelanggan bisa memilih paket, memesan, dan membayar secara online. Staff memverifikasi pembayaran dan pemesanan, lalu admin menugaskan driver dan kendaraan untuk perjalanan tersebut.

> Proyek tugas kuliah (Kelompok 7). Repository ini hanya memuat satu bagian dari proyek, yaitu alur **pemesanan → pembayaran → verifikasi → penugasan**. Dikerjakan oleh Larissa. Fitur lain (data master, laporan, jadwal driver) dikerjakan anggota kelompok lain dan tidak disertakan di sini.

## Fitur

| Peran | Yang bisa dilakukan |
|---|---|
| Pengunjung | Melihat katalog paket wisata, mencari paket, dan membuka detail paket |
| Customer | Memesan paket, mengisi data pemesan dan penumpang, melihat total biaya, mengunggah bukti pembayaran, memantau status pesanan |
| Staff | Memeriksa bukti pembayaran, memverifikasi atau menolak pembayaran, lalu memverifikasi atau menolak pemesanan beserta link grup WhatsApp |
| Admin | Menugaskan driver dan kendaraan ke pemesanan yang sudah terverifikasi tanpa bentrok jadwal |

Aturan penting yang sudah berjalan:

- Kuota dicek saat pemesanan dan dikunci sementara selama 24 jam sampai pembayaran dikirim.
- Pesanan yang melewati batas waktu otomatis berstatus kedaluwarsa dan kuotanya dilepas.
- Bukti pembayaran hanya menerima JPG, PNG, atau PDF maksimal 4 MB, disimpan privat, dan hanya bisa dibuka Staff.
- Penugasan memeriksa status driver, status kendaraan, kapasitas kursi, dan bentrok tanggal.

## Teknologi

PHP 8.2+, Laravel 12, MySQL (atau SQLite), Blade, CSS biasa.

## Persiapan

Pastikan sudah terpasang:

- PHP 8.2 atau lebih baru dengan ekstensi `pdo_mysql`, `mbstring`, `openssl`, `dom`, `gd`, `zip`, `fileinfo`
- Composer 2
- MySQL 5.7/8 (XAMPP atau Laragon juga bisa)
- Git

## Instalasi

```bash
git clone <url-repository-ini>
cd travel-tour-laravel-LA
composer install
```

Salin file konfigurasi:

```bash
# Windows PowerShell
Copy-Item .env.example .env

# Linux / macOS / WSL
cp .env.example .env
```

Buat database kosong bernama `travel_tour`, lalu ubah bagian database di `.env`:

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=travel_tour
DB_USERNAME=root
DB_PASSWORD=
```

Lanjutkan:

```bash
php artisan key:generate
php artisan migrate --seed
php artisan storage:link
php artisan serve
```

Buka http://127.0.0.1:8000

Tidak ingin memakai MySQL? Biarkan `DB_CONNECTION=sqlite` di `.env`, aktifkan ekstensi `pdo_sqlite`, lalu jalankan perintah yang sama.

## Akun demo

Dibuat otomatis oleh `php artisan migrate --seed`. Password semuanya `password`.

| Peran | Email |
|---|---|
| Customer | customer@traveltour.test |
| Staff | staff@traveltour.test |
| Admin | admin@traveltour.test |

> **Hanya untuk percobaan di komputer sendiri.** Jangan menjalankan seeder ini di server publik. Hapus atau ganti akun dan password-nya sebelum aplikasi dipasang online.

## Cara mencoba

1. Login sebagai **Customer**, buka paket wisata, klik *Pesan Sekarang*, isi data peserta (NIK 16 digit), lalu *Check Out*.
2. Di halaman detail pesanan, unggah bukti pembayaran. Status pesanan bisa dilihat di menu *Pesanan Saya*.
3. Login sebagai **Staff**, buka *Verifikasi Staff*. Verifikasi pembayaran lebih dulu, lalu verifikasi pemesanan dengan mengisi link grup WhatsApp (harus diawali `https://`).
4. Login sebagai **Admin**, buka *Penugasan*, lalu pilih pemesanan terverifikasi, driver, kendaraan, titik kumpul, dan jam kumpul.

Gunakan jendela incognito atau logout dulu saat berpindah akun.

## Status pesanan

```
belum_terbayar → menunggu_verifikasi_pembayaran → pembayaran_terverifikasi → diverifikasi
       ↓                       ↓                            ↓
  kedaluwarsa               ditolak                      ditolak
```

## Letak kode

| Fitur | File |
|---|---|
| Katalog, pemesanan, pembayaran, status pesanan | `app/Http/Controllers/Customer/TravelController.php` |
| Verifikasi pembayaran dan pemesanan | `app/Http/Controllers/Staff/VerifikasiController.php` |
| Penugasan driver dan kendaraan | `app/Http/Controllers/Admin/PenugasanController.php` |
| Tampilan | `resources/views/travel`, `resources/views/staff`, `resources/views/admin` |
| Struktur tabel | `database/migrations` |
| Data contoh | `database/seeders/DatabaseSeeder.php` |
| Daftar route | `routes/web.php` (atau jalankan `php artisan route:list`) |

## Batasan saat ini

- Data paket, driver, dan kendaraan hanya diisi lewat seeder, belum ada halaman pengelolaannya di repository ini.
- Biaya admin masih Rp 0.
- QR pembayaran belum dikonfigurasi, sehingga halaman pesanan menampilkan peringatan.
- Hapus pesanan dan edit data penumpang belum tersedia.
- Aplikasi belum dikonfigurasi untuk HTTPS dan belum diuji beban.

## Keamanan

- Jangan mengunggah file `.env` ke GitHub (sudah masuk `.gitignore`).
- Ganti `APP_KEY`, kredensial database, dan semua akun demo sebelum dipakai di luar komputer lokal.
- Set `APP_DEBUG=false` pada server sungguhan.

## Pemecahan masalah

| Masalah | Solusi |
|---|---|
| `could not find driver` | Aktifkan ekstensi `pdo_mysql` (atau `pdo_sqlite`) di `php.ini` |
| `ext-gd` atau `ext-zip` missing saat `composer install` | Aktifkan ekstensinya di `php.ini`, lalu ulangi `composer install` |
| `No application encryption key` | Jalankan `php artisan key:generate` |
| Tidak bisa terhubung ke database | Pastikan MySQL menyala dan isi `.env` benar |
| Tampilan atau route tidak berubah | Jalankan `php artisan optimize:clear` |
| Ingin mengulang dari awal | Jalankan `php artisan migrate:fresh --seed` |

## Tes

```bash
php artisan test
```

Saat ini hanya ada uji dasar bahwa halaman awal dialihkan ke katalog.
