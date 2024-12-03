# Analisis-HTTP


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


### 3. Landing Page

Buat file `index.html` dan masukkan kode berikut:

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTTP1 dan HTTP2</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@300..700&family=Roboto+Slab:wght@100..900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Fira Code', 'Arial Narrow', Arial, sans-serif, sans-serif;
        }
    </style>
</head>

<body>
    <h1>HTTP1 dan HTTP2</h1>
    <p>HTTP1 dan HTTP2 adalah protokol yang digunakan untuk mengakses data dari server.</p>
    <br>
    <h2>Pembuktian menggunakan Protokol HTTP/1 dan HTTP2</h2>
    <ol>
        <li>Gunakan browser Chrome</li>
        <li>Klik Kanan pada sembarang tempat -> Pilih <strong>Inspect</strong></li>
        <li>Pilih Tab <strong>Network</strong></li>
        <li>Refresh halaman ini</li>
        <li>Lihat baris request halaman ini (localhost)</li>
        <li>Lihat bagian kolom <strong>Protocol</strong></li>
        <li>Jika tidak ada kolom <strong>Protocol</strong>, klik kanan pada salah satu kolom -> <strong>Check Protocol</strong></li>
        <li>HTTP1 -> Protokol:HTTP1</li>
        <li>HTTP2 -> Protokol:h2</li>
    </ol>
</body>

</html>
```
   
  ### 4. Membuat File Docker Compose
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

## Pengujian Proyek
1. Navigasikan ke folder `{root_project}/` menggunakan terminal.
2. Jalankan perintah berikut untuk memulai layanan:

   ```bash
   docker-compose up
   ```

   Akses melalui browser Chrome:
- Untuk **HTTP/1**, buka: [http://localhost:8001](http://localhost:8001)
![Deskripsi Gambar](https://github.com/Harrydhe/Analisis-HTTP-SMTP-dan-DNS/blob/main/assets/http1%20protocol.jpg)

![Deskripsi Gambar](https://github.com/Harrydhe/Analisis-HTTP-SMTP-dan-DNS/blob/main/assets/Screenshot%202024-11-28%20214303.jpg)



- Untuk **HTTP/2**, buka: [https://localhost](https://localhost)

![Deskripsi Gambar](https://github.com/Harrydhe/Analisis-HTTP-SMTP-dan-DNS/blob/main/assets/http2protocol.jpg)

![Deskripsi Gambar](https://github.com/Harrydhe/Analisis-HTTP-SMTP-dan-DNS/blob/main/assets/http2.jpg)


