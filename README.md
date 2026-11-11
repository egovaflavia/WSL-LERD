# Panduan Lengkap Setup WSL2 (Ubuntu) & Lerd di Windows

Dokumentasi ini disusun berdasarkan tahapan instalasi dan konfigurasi nyata mulai dari pemasangan distribusi Linux di WSL2, instalasi Lerd di Ubuntu, hingga akses proyek dan dashboard di Windows.

---

## 1. Instalasi & Konfigurasi WSL2 di Windows

Buka **PowerShell** (atau Windows Terminal) di Windows dan jalankan langkah-langkah berikut:

### A. Cek Status Distribusi WSL
```powershell
wsl --list --verbose
```
*(Jika sebelumnya sudah terpasang Docker Desktop, biasanya hanya muncul `docker-desktop` dan belum ada distribusi Linux utama).*

### B. Instal Distribusi Ubuntu
```powershell
# (Opsional) Lihat daftar distribusi Linux yang tersedia secara online
wsl --list --online

# Instal Ubuntu
wsl --install -d Ubuntu
```
Setelah proses unduhan selesai, jendela terminal Ubuntu akan terbuka otomatis untuk meminta Anda membuat **username** dan **password** Linux baru.

### C. Ubah Ubuntu Menjadi Distribusi Default
Agar perintah `wsl` langsung membuka terminal Ubuntu (bukan `docker-desktop`), ubah pengaturan default-nya di PowerShell:
```powershell
wsl --set-default Ubuntu
```

---

## 2. Masuk ke Ubuntu & Instalasi Lerd

### A. Masuk ke Terminal Ubuntu
Dari PowerShell Windows, masuk ke lingkungan Linux dengan perintah:
```powershell
wsl
```
*(Pastikan prompt terminal sudah berubah menjadi `username@nama-pc:~$` sebelum menjalankan perintah Linux di bawah ini).*

### B. Perbarui Sistem & Instal Lerd
Jalankan perintah berikut di dalam **terminal Ubuntu**:
```bash
# 1. Perbarui paket sistem Ubuntu
sudo apt update && sudo apt upgrade -y

# 2. Instal Lerd beserta dependensinya
curl -fsSL https://lerd.sh | bash

# 3. Muat ulang konfigurasi shell (jika perintah lerd belum langsung terbaca)
source ~/.bashrc

# 4. Hubungkan jaringan WSL2 dengan Windows (agar domain .test terbaca)
lerd wsl:setup

# 5. Cek status seluruh layanan Lerd
lerd status
```

---

## 3. Manajemen Folder & Proyek (`park`)

Simpan seluruh proyek di dalam direktori Linux agar performa pembacaan file PHP/Laravel tetap cepat.

### A. Lokasi Folder Proyek
* **Path di dalam terminal Ubuntu:** `/home/<username_linux>/Lerd`
* **Path akses dari Windows File Explorer:** `\\wsl.localhost\Ubuntu\home\<username_linux>\Lerd`

### B. Daftarkan Folder Proyek ke Lerd
Jalankan perintah berikut **satu kali saja** di terminal Ubuntu untuk mendaftarkan folder induk proyek:
```bash
# Buat folder induk (jika belum ada)
mkdir -p ~/Lerd

# Daftarkan folder agar setiap sub-folder otomatis mendapatkan domain .test
lerd park ~/Lerd

# Cek daftar folder yang sudah diparkir
lerd parked

# Cek daftar situs aktif, versi PHP, Node, dan status HTTPS
lerd sites
```

---

## 4. Akses Aplikasi & Dashboard di Windows

Setelah Lerd aktif, semua layanan dan proyek dapat diakses langsung dari Windows:

* **Dashboard Web UI Lerd:** Buka `http://lerd.localhost` atau `http://127.0.0.1:7073` di browser Windows.
* **Domain Aplikasi Lokal:** Buka `https://<nama-folder-projek>.test` di browser Windows.
* **Akses dari Perangkat Lain (HP/Laptop satu Wi-Fi):** Aktifkan tombol **Expose LAN** pada proyek melalui Dashboard Web UI Lerd.
* **Akses Cepat dari Terminal Ubuntu:**
  ```bash
  # Membuka proyek di VS Code Windows
  cd ~/Lerd/<nama-projek>
  code .

  # Membuka direktori aktif di Windows File Explorer
  explorer.exe .
  ```

---

## 5. Langkah Setelah Komputer Restart

Setiap kali Windows di-restart, WSL akan berhenti secara otomatis. Anda **tidak perlu** menginstal atau melakukan `lerd park` ulang. Lakukan 3 langkah ini saat komputer menyala kembali:

1. **Bangunkan WSL Ubuntu:** Buka aplikasi **Ubuntu** di Start Menu atau ketik `wsl` di PowerShell.
2. **Nyalakan Layanan Lerd:**
   ```bash
   lerd start
   ```
3. **Sinkronisasi Jaringan WSL (jika domain `.test` atau IP berubah):**
   ```bash
   lerd wsl:setup
   ```
4. **Cek Dashboard & Status:** Jalankan `lerd status`, lalu buka `http://lerd.localhost` di browser Windows (aktifkan kembali **Expose LAN** dari dashboard jika diperlukan).

> **Tips:** Biarkan jendela terminal Ubuntu tetap terbuka (cukup di-*minimize*) selama Anda bekerja agar layanan di latar belakang tidak mati.

---

## 6. Perintah Pemeliharaan & Troubleshooting

* **Mematikan WSL untuk menghemat RAM saat selesai bekerja (jalankan di PowerShell Windows):**
  ```powershell
  wsl --shutdown
  ```
* **Domain `.test` tidak terbuka di browser Windows:**
  1. Matikan sementara ekstensi VPN atau fitur *Secure DNS* (DoH) di browser.
  2. Jalankan `ipconfig /flushdns` di PowerShell/CMD Windows.
  3. Jalankan `lerd wsl:setup` di terminal Ubuntu.
