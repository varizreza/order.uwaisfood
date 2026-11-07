UWAIS FOOD CUSTOMER PWA — PAKET AWAL INTEGRASI

Pilihan produk:
- Akses tamu dan akun pelanggan (daftar/login dengan nomor WhatsApp + kata sandi).
- Pembayaran QRIS/transfer atau COD saat pickup/pengantaran.
- PWA terpisah dari admin.
- Desain simple dan responsif.

FILE
- index.html: aplikasi pelanggan
- manifest.webmanifest, sw.js, icon-192.png, icon-512.png: PWA
- CustomerApi_Addon.gs: API tambahan untuk Apps Script

INTEGRASI BACKEND (WAJIB SEBELUM CUSTOMER PWA DIPAKAI)
1. Backup project Apps Script dan Spreadsheet terlebih dahulu.
2. Ganti seluruh Code.gs dengan file Code.gs yang sudah disertakan pada paket ini. File tersebut berasal dari backend v10.9.9 dan sudah ditambahkan API pelanggan.
3. Pada fungsi api_() di Code.gs v10.9.9, cari objek `const fns = { ... }`.
   Tambahkan enam properti berikut ke objek tersebut:
     customer_getCatalog, customer_register, customer_login,
     customer_createOrder, customer_lookupOrder, customer_uploadProof
   Contoh:
     const fns = { login, getData, ..., saveQrisImage,
       customer_getCatalog, customer_register, customer_login,
       customer_createOrder, customer_lookupOrder, customer_uploadProof
     };
4. Sebelum menyimpan, ganti nilai PIN placeholder pada bagian const PIN dengan PIN admin baru milik Anda (minimal 6 karakter). Jangan gunakan PIN lama yang pernah tersimpan di arsip.
5. Simpan. Deploy > Manage deployments > Edit > New version > Deploy. Sheet CustomerAccounts, OrderOnline, dan bukti pembayaran akan dibuat otomatis saat fitur terkait pertama kali dipakai.
6. Pastikan akses Web App tetap dapat dipanggil oleh pengunjung sesuai pengaturan deployment saat ini. Jangan pernah memasukkan PIN admin ke index.html.
7. Upload seluruh isi folder ini ke hosting HTTPS terpisah untuk customer app. Endpoint Apps Script sudah ditetapkan pada index.html.

INTEGRASI DATA
- Katalog mengambil data langsung dari sheet Menu (Nama, Harga, Isi, GambarURL).
- Tarif delivery hanya dapat dipilih dari TarifOngkir yang aktif dan dihitung ulang oleh server.
- Pesanan ditulis ke sheet Orders yang sama, sehingga masuk ke daftar order admin.
- Profil pelanggan disimpan ke sheet Pelanggan.
- Sheet CustomerAccounts menyimpan hash kata sandi, bukan kata sandi asli.
- Sheet OrderOnline menyimpan sumber Online/Customer, metode pembayaran, catatan, dan ID file bukti pembayaran.
- Bukti pembayaran tersimpan privat di Google Drive; akses file hanya untuk pemilik/admin Drive.

BATASAN VERSI AWAL
- Checkout kurir hanya memakai daerah yang sudah memiliki tarif aktif di TarifOngkir. Tarif baru perlu ditambahkan admin dahulu agar tidak ada ongkir yang dapat dimanipulasi pelanggan.
- Aplikasi menampilkan QRIS dan rekening dari konfigurasi server. Pelanggan memilih QRIS/Transfer atau COD.
- Tombol unggah bukti pembayaran tersedia pada konfirmasi pesanan QRIS/Transfer. File bukti disimpan privat di Drive dan ID file dicatat di sheet OrderOnline; tampilan pemeriksaan bukti pada halaman admin belum ditambahkan.
- Belum ada OTP WhatsApp/SMS; login akun menggunakan nomor WhatsApp dan kata sandi.
- Subdomain order.uwaisfood.id perlu disiapkan di penyedia domain/hosting; URL subdomain belum bisa dibuat otomatis dari sini.

KEAMANAN PENTING
PIN admin pada Code.gs di paket ini sengaja diganti placeholder. WAJIB isi PIN admin baru sebelum deploy. Jangan pernah membagikan PIN kepada pelanggan.
API publik hanya menampilkan katalog/tarif/pembayaran dan operasi pelanggan. Jangan menambahkan getData atau fungsi admin lain sebagai endpoint tanpa autentikasi.

PENGUJIAN WAJIB
- Setelah deploy, buka endpoint /exec dan pastikan halaman admin tetap tampil.
- Tes customer_getCatalog dari PWA.
- Tes registrasi/login akun.
- Tes checkout Pickup + COD.
- Tes checkout Kurir + QRIS/Transfer dengan area tarif aktif.
- Pastikan pesanan baru masuk ke Orders dan tampil di aplikasi admin.
- Tes tracking dengan kombinasi kode pesanan + nomor WhatsApp.
- Jangan mengaktifkan publik sebelum pengujian dan pemeriksaan izin Drive selesai.
