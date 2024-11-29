# Analisis-HTTP-SMTP-dan-DNS


## Persiapan Sistem
**Rekomendasi**: Disarankan menggunakan sistem operasi UNIX.

### 1. Docker
- Pastikan Docker dan Docker Compose telah terinstal di perangkat lokal Anda.
- Jika belum, kunjungi [situs resmi Docker](https://www.docker.com) untuk mengunduh dan menginstalnya.

### 2. OpenSSL
- Pastikan OpenSSL sudah terpasang di perangkat Anda.
- Jika belum, instal OpenSSL sesuai dengan sistem operasi perangkat Anda.

### 3. Struktur Folder
- Buat folder proyek dan navigasikan ke direktori tersebut.
- Di dalam folder proyek, buat dua folder tambahan:  
  - `nginx-config`  
  - `ssl`

---

## Membuat Proyek

### 1. Konfigurasi Nginx
- Navigasikan ke folder `{root_project}/nginx-config`.
- Buat file bernama `default.conf`, lalu tambahkan konfigurasi berikut:

```nginx
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
```
### 2. Mengatur SSL dengan OpenSSL
1. Pindah ke folder `{root_project}/`.
2. Buka terminal dan jalankan perintah berikut:

   ```bash
   openssl req -x509 -newkey rsa:2048 -keyout ssl/server.key -out ssl/server.crt -days 365 -nodes 
   ```
3. Isi jawaban dari pertanyaan sesuai yang diinginkan

   
  ### 3. Membuat File Docker Compose
1. Buat file bernama `docker-compose.yml` di direktori proyek.
2. Tambahkan konfigurasi berikut ke dalam file tersebut:

   ```yaml
   version: '3.8'
   services:
     web:
       image: nginx:alpine
       ports:
         - "8001:80" # HTTP/1.1
         - "443:443" # HTTP/2
       volumes:
         - ./nginx-config:/etc/nginx/conf.d
         - ./ssl:/etc/nginx/certs
         - ./index.html:/var/www/index.html
    ```
3. Save

### Pengujian Proyek
1. Navigasikan ke folder `{root_project}/` menggunakan terminal.
2. Jalankan perintah berikut untuk memulai layanan:

   ```bash
   docker-compose up
   ```

   Akses melalui browser Chrome:
- Untuk **HTTP/1**, buka: [http://localhost:8001](http://localhost:8001)
- Untuk **HTTP/2**, buka: [https://localhost](https://localhost)

