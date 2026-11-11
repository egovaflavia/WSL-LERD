# Panduan Instalasi & Setup Lerd di Windows

Dokumentasi lengkap untuk menginstal dan menjalankan **Lerd** secara langsung dari sisi Windows menggunakan *installer* resmi maupun skrip PowerShell.

---

## 1. Persyaratan Sistem

* **Sistem Operasi:** Windows 10 (versi 2004 atau lebih baru) atau Windows 11 (x64 / Arm64).
* **Virtualisasi:** Fitur virtualisasi (VT-x / AMD-V) aktif di BIOS.
* **Ruang Penyimpanan:** Minimal 5 GB ruang kosong untuk *image* WSL2, Ubuntu, dan Podman.

---

## 2. Metode Instalasi dari Windows

Anda dapat memilih salah satu dari dua metode otomatis berikut untuk menyiapkan Lerd, WSL2, dan aplikasi desktop Windows sekaligus:

### Metode A: Menggunakan Installer GUI (`Install-Lerd.exe`) — *Direkomendasikan*
1. Unduh file `Install-Lerd.exe` terbaru melalui halaman rilis resmi [GitHub Lerd Desktop](https://github.com) atau situs [Lerd for Windows](https://lerd.sh/getting-started/windows).
2. Jalankan `Install-Lerd.exe` (jika muncul peringatan *Windows SmartScreen*, klik **More info** > **Run anyway**).
3. Ikuti panduan *wizard*:
   * Masukkan **username** dan **password** baru untuk lingkungan Linux WSL2.
   * Jika fitur WSL belum aktif di Windows, *installer* akan meminta *restart* komputer satu kali dan melanjutkan proses pemasangan otomatis setelah Anda login kembali.
4. Setelah selesai, **Aplikasi Desktop Lerd** dan ikon *system tray* akan otomatis terpasang di Windows.

### Metode B: Menggunakan Perintah PowerShell
1. Buka jendela **PowerShell** biasa (**bukan** *Run as Administrator*).
2. Jalankan skrip instalasi resmi berikut:
   ```powershell
   irm https://lerd.sh/install.ps1 | iex
   ```
3. Skrip ini akan menjalankan tahapan yang sama dengan *installer* GUI dan hanya akan meminta izin Administrator saat Windows membutuhkannya.

---

## 3. Verifikasi & Penggunaan dari Terminal Windows

Setelah instalasi selesai, perintah `lerd` sudah terhubung dengan terminal Windows Anda (PowerShell / Windows Terminal) tanpa perlu membuka jendela Linux secara manual:

```powershell
# Cek status seluruh layanan (Nginx, DNS, PHP, Database)
lerd status

# Menjalankan atau menghentikan seluruh layanan Lerd
lerd start
lerd stop

# Membuka Dashboard Lerd (otomatis membuka Aplikasi Desktop atau Browser)
lerd dashboard

# Membuka tampilan Terminal UI (TUI) interaktif
lerd tui
```

---

## 4. Manajemen Folder & Proyek di Windows

Lerd menyimpan seluruh file situs di dalam sistem file WSL2 agar performa baca/tulis PHP dan Composer berjalan maksimal.

### Mengakses Folder Proyek dari Windows Explorer
Buka **File Explorer** (`Win + E`) dan masukkan jalur jaringan berikut di *address bar*:
```text
\\wsl.localhost\Ubuntu\home\<username_linux>\Lerd
```
*(Anda dapat mengklik kanan folder tersebut dan memilih **Pin to Quick access** agar mudah diakses kapan saja).*

### Perintah Dasar Proyek (Jalankan dari PowerShell)
```powershell
# Membuat proyek baru melalui wizard interaktif Lerd
lerd new

# Mendaftarkan folder induk agar setiap sub-folder otomatis mendapat domain .test
lerd park ~/Lerd

# Melihat daftar situs aktif beserta versi PHP, Node, dan status HTTPS
lerd sites
```

---

## 5. Fitur Utama & Layanan Bawaan

* **Domain & SSL Otomatis:** Setiap proyek otomatis mendapatkan domain `https://<nama-projek>.test` dengan sertifikat SSL lokal yang sudah dipercaya oleh browser Windows.
* **Multi-Versi PHP & Node:** Mengatur versi PHP dan Node.js berbeda untuk tiap proyek langsung melalui Dashboard Web/Desktop atau CLI (`lerd use php 8.4`).
* **Layanan Database & Cache Bawaan:** MySQL, PostgreSQL, Redis, Meilisearch, RustFS, dan Mailpit dapat dinyalakan atau dimatikan dengan satu klik dari Dashboard (`http://lerd.localhost`).
* **Expose LAN:** Membagikan akses situs lokal ke perangkat lain (seperti HP) dalam satu jaringan Wi-Fi melalui tombol **Expose LAN** di Dashboard.

---

## 6. Pembaruan & Penghapusan (Uninstall)

* **Memperbarui Lerd ke versi terbaru:**
  ```powershell
  lerd update
  ```
* **Menghapus Lerd dari Windows:**
  Buka **Settings** > **Apps** > **Installed apps** di Windows, cari **Lerd**, lalu klik **Uninstall**.


# Panduan Setup & Penggunaan WSL2 + Lerd (PHP & Laravel Environment)

Dokumentasi lengkap instalasi, konfigurasi, hingga panduan harian menjalankan lingkungan pengembangan lokal berbasis **WSL2 (Ubuntu)** dan **Lerd** di Windows.

---

## 1. Instalasi & Konfigurasi WSL2 (Windows)

Jalankan perintah berikut di **PowerShell (Run as Administrator)**:

```powershell
# 1. Instal WSL dan distribusi Ubuntu
wsl --install -d Ubuntu

# 2. Atur Ubuntu sebagai distribusi default (penting jika Docker Desktop terinstal)
wsl --set-default Ubuntu

# 3. Cek status dan versi WSL
wsl --list --verbose
```

---

## 2. Instalasi Lerd di Ubuntu WSL

Masuk ke terminal Ubuntu dengan mengetik `wsl` di PowerShell, lalu jalankan langkah berikut:

```bash
# 1. Perbarui paket sistem Ubuntu
sudo apt update && sudo apt upgrade -y

# 2. Instal Lerd beserta dependensinya
curl -fsSL https://lerd.sh | bash

# 3. Aktifkan integrasi jaringan WSL2 ke Windows (agar domain .test terbaca)
lerd wsl:setup

# 4. Cek status layanan Lerd
lerd status
```

---

## 3. Struktur Folder & Manajemen Proyek

Semua proyek wajib disimpan di dalam sistem file Linux (bukan di drive `C:\` atau `D:\`) agar performa pembacaan file tetap cepat.

* **Path di dalam terminal Ubuntu:** `/home/kangsseulgi/Lerd`
* **Path akses dari Windows File Explorer:** `\\wsl.localhost\Ubuntu\home\kangsseulgi\Lerd`

### Mendaftarkan Folder Proyek (`park`)
Cukup dijalankan **satu kali** untuk folder induk. Setiap folder baru di dalamnya otomatis mendapatkan domain `.test`.

```bash
mkdir -p ~/Lerd
lerd park ~/Lerd
```

### Perintah Manajemen Situs
```bash
# Melihat daftar folder yang diparkir
lerd parked

# Melihat daftar situs aktif beserta versi PHP, Node, dan status HTTPS
lerd sites

# Mengaktifkan HTTPS/TLS lokal (jalankan di dalam folder proyek)
lerd secure
```

---

## 4. Akses Antarmuka (UI) & Aplikasi di Windows

* **Dashboard Web UI Lerd:** `http://lerd.localhost` atau `http://127.0.0.1:7073`
* **Terminal UI (TUI) Lerd:** Jalankan `lerd tui` di terminal Ubuntu.
* **Domain Aplikasi Lokal:** `https://<nama-folder-projek>.test` (contoh: `https://sample.test`)
* **Membuka Proyek di VS Code dari Terminal:**
  ```bash
  cd ~/Lerd/sample
  code .
  ```
* **Membuka Folder Linux di Windows Explorer:**
  ```bash
  explorer.exe .
  ```

---

## 5. Checklist Setelah Komputer Restart

Setiap kali Windows di-restart, WSL akan mati secara otomatis. Ikuti langkah singkat ini untuk menyalakannya kembali (tidak perlu instal ulang atau `park` ulang):

1. **Buka Terminal Ubuntu:** Buka aplikasi **Ubuntu** di Start Menu atau ketik `wsl` di PowerShell.
2. **Nyalakan Layanan Lerd:**
   ```bash
   lerd start
   ```
3. **Sinkronisasi Ulang Jaringan WSL (jika domain `.test` belum terbuka):**
   ```bash
   lerd wsl:setup
   ```
4. **Cek Status:** Jalankan `lerd status` dan buka `http://lerd.localhost` di browser Windows (aktifkan kembali fitur **Expose LAN** dari dashboard jika diperlukan).

> **Catatan:** Biarkan jendela terminal Ubuntu tetap terbuka (cukup di-*minimize*) selama Anda bekerja agar layanan di latar belakang tidak berhenti.

---

## 6. Troubleshooting Singkat

* **Menghemat RAM saat selesai bekerja (di PowerShell Windows):**
  ```powershell
  wsl --shutdown
  ```
* **Domain `.test` tidak bisa diakses di browser Windows:**
  1. Pastikan ekstensi VPN atau *Secure DNS* (DoH) di browser dalam keadaan nonaktif.
  2. Jalankan `ipconfig /flushdns` di PowerShell/CMD Windows.
  3. Jalankan ulang `lerd wsl:setup` di terminal Ubuntu.


