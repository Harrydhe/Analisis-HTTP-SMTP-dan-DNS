# Analisis-HTTP-SMTP-dan-DNS

Persiapan Sistem

Rekomendasi: Disarankan menggunakan sistem operasi UNIX.

1. Docker
Pastikan Docker dan Docker Compose telah terinstal di perangkat lokal Anda.
Jika belum, kunjungi situs resmi Docker untuk mengunduh dan menginstalnya.
2. OpenSSL
Pastikan OpenSSL sudah terpasang.
Jika belum, instal OpenSSL sesuai dengan sistem operasi perangkat Anda.
3. Struktur Folder
Buat folder proyek dan navigasikan ke direktori tersebut.
Di dalam folder proyek, buat dua folder tambahan: nginx-config dan ssl


Membuat Proyek
1. Konfigurasi Nginx
- Navigasikan ke folder {root_project}/nginx-config.
Buat file bernama default.conf, lalu tambahkan konfigurasi berikut:
server {
  # HTTP/1.1 pada port 80
  listen 80;

  # HTTP/2 pada port 443 dengan SSL
  listen 443 ssl;
  http2 on;

  # Konfigurasi SSL menggunakan OpenSSL
  ssl_certificate /etc/nginx/certs/server.crt;
  ssl_certificate_key /etc/nginx/certs/server.key;
  ssl_protocols TLSv1.3;
  ssl_prefer_server_ciphers off;

  # Header keamanan
  add_header X-Content-Type-Options nosniff;

  # Direktori root untuk website (Linux OS)
  root /var/www/;
  index index.html;
}


2. Mengatur SSL dengan OpenSSL
Pindah ke folder {root_project}/.
Buka terminal dan jalankan perintah berikut :
openssl req -x509 -newkey rsa:2048 -keyout ssl/server.key -out ssl/server.crt -days 365 -nodes


