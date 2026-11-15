<div align="center">

# 🖥️ Windows RDP via GitHub Actions

**Buat Windows Server RDP sementara, gratis, langsung dari GitHub Actions + Tailscale** ⚡

![Windows](https://img.shields.io/badge/Windows-2019%20%7C%202022%20%7C%202025-0078D6?logo=windows&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-2088FF?logo=githubactions&logoColor=white)
![Tailscale](https://img.shields.io/badge/Tailscale-VPN-242424?logo=tailscale&logoColor=white)
![Durasi](https://img.shields.io/badge/Durasi-1--6%20Jam-brightgreen)

</div>

---

## 📖 Apa Ini?

Workflow GitHub Actions yang menjalankan **Windows Server** di runner GitHub, lalu membuka akses **Remote Desktop (RDP)** melalui **Tailscale**. Dengan begitu, kamu bisa memiliki Windows sementara untuk mencoba-coba, testing, atau pekerjaan ringan tanpa perlu PC sendiri. 🚀

> ⚠️ Sifatnya **sementara**. Setelah waktunya habis, runner dihapus dan **semua file di dalamnya akan hilang**. Simpan pekerjaanmu ke tempat lain (GitHub, Google Drive, dan sebagainya).

## ✨ Fitur

- 🪟 **Pilih versi Windows**: Server latest, 2025, dan 2022
- 👤 **Username dan password sendiri**, diisi saat menjalankan workflow (minimal 6 karakter)
- ⏱️ **Pilih durasi 1–6 jam** dengan **auto-stop**, sehingga job berhenti otomatis saat waktunya habis
- 🔐 **Akses melalui Tailscale**, tanpa perlu IP publik atau port forwarding
- 🧰 **Instalasi software otomatis** (opsional, versi terbaru):
  - 💬 Discord
  - 💻 Visual Studio Code
  - 🗜️ WinRAR
- 🙈 Password disembunyikan (di-mask) di log Actions
- 🕒 Jam auto-stop dalam WIB ditampilkan di log

---

## 📋 Persiapan

Hal-hal yang dibutuhkan sebelum memulai:

| Kebutuhan | Keterangan |
|---|---|
| 🐙 Akun GitHub | Untuk membuat repository dan menjalankan Actions |
| 🌐 Akun Tailscale | Daftar gratis di [tailscale.com](https://tailscale.com) |
| 📱 Tailscale di perangkatmu | HP/PC/laptop yang dipakai untuk terhubung, harus login dengan **akun Tailscale yang sama** |
| 🖱️ Aplikasi Remote Desktop | Lihat bagian [Cara Terhubung ke RDP](#-cara-terhubung-ke-rdp) |

---

## 🛠️ Langkah 1: Buat Repository

1. Buka [github.com/new](https://github.com/new), lalu buat repository baru.
2. 🔒 **Pilih Private** (sangat disarankan, lihat bagian [Keamanan](#-keamanan--catatan-penting)).
3. Klik **Create repository**.

## 📝 Langkah 2: Buat Workflow

1. Di repository kamu, klik **Add file** ➜ **Create new file**.
2. Pada kolom nama file, ketik persis:
   ```
   .github/workflows/rdp.yml
   ```
   > 💡 Saat mengetik `/`, GitHub otomatis membuat foldernya.
3. Salin **seluruh isi** file [`dezwindows.yml`](./dezwindows.yml) ke editor.
4. Klik **Commit changes** ✅ (commit ke branch `main`/default).

> ⚠️ Workflow dengan pemicu manual (`workflow_dispatch`) **harus berada di default branch** agar tombol *Run workflow* muncul.

## 🔑 Langkah 3: Siapkan `TS_KEY` (Tailscale Auth Key)

`TS_KEY` adalah kunci agar runner GitHub bisa bergabung ke jaringan Tailscale milikmu.

### 3.1 Buat key di Tailscale

1. Buka 👉 [login.tailscale.com/admin/settings/keys](https://login.tailscale.com/admin/settings/keys)
2. Klik **Generate auth key...**
3. Atur opsinya:

   | Opsi | Saran | Alasan |
   |---|---|---|
   | ♻️ **Reusable** | ✅ Aktifkan | Agar key bisa dipakai setiap kali workflow dijalankan |
   | 👻 **Ephemeral** | ✅ Aktifkan | Device runner otomatis terhapus dari daftar setelah offline |
   | ⏳ **Expiration** | Sesuai kebutuhan (maksimal 90 hari) | Jika key kedaluwarsa, workflow akan gagal terhubung |
   | 🏷️ **Tags** | Biarkan kosong | Kecuali kamu menggunakan ACL tags |

4. Klik **Generate key**, lalu **salin** key tersebut. Bentuknya seperti `tskey-auth-xxxxxxxxxxxx`.

> 🚨 Key hanya ditampilkan **satu kali**. Jangan dibagikan kepada siapa pun dan jangan ditaruh di dalam kode.

### 3.2 Simpan ke GitHub Secrets

1. Di repository kamu, buka **Settings** ➜ **Secrets and variables** ➜ **Actions**
2. Klik **New repository secret**
3. Isi:
   - **Name:** `TS_KEY` (huruf kapital semua, harus persis)
   - **Secret:** tempel key yang tadi disalin
4. Klik **Add secret** ✅

---

## ▶️ Cara Menjalankan

1. Buka tab **Actions** di repository kamu.
2. Pilih workflow **Windows RDP - 1-6 Jam** di sisi kiri.
3. Klik **Run workflow** (tombol di sisi kanan).
4. Isi formulir berikut:

   | Input | Deskripsi | Default |
   |---|---|---|
   | 🪟 `windows_version` | Versi Windows Server | `windows-2022` |
   | 👤 `rdp_user` | Username RDP | `dezels` |
   | 🔑 `rdp_pass` | Password RDP (minimal 6 karakter) | *(wajib diisi)* |
   | 🏷️ `ts_hostname` | Nama device di Tailscale | `dezwindows` |
   | ⏱️ `duration_hours` | Durasi 1–6 jam, auto-stop setelahnya | `6` |
   | 💬 `install_discord` | Install Discord terbaru ⚠️ butuh 3+ menit | ❌ |
   | 💻 `install_vscode` | Install VS Code terbaru | ❌ |
   | 🗜️ `install_winrar` | Install WinRAR terbaru | ❌ |

5. Klik tombol hijau **Run workflow** ✅
6. Tunggu hingga langkah **🔐 RDP Info** muncul di log. Di sana tertera **IP, hostname, username, dan jam auto-stop** 🎉

> 🕐 Proses persiapan biasanya memakan waktu beberapa menit. Instalasi Discord yang paling lama (**3+ menit**), jadi mohon bersabar. 😴

---

## 🔌 Cara Terhubung ke RDP

### 1️⃣ Aktifkan Tailscale di perangkatmu

Pasang Tailscale di perangkat yang akan dipakai untuk terhubung, lalu **login dengan akun yang sama** dengan akun pembuat `TS_KEY`.

- 🪟 Windows / 🍎 macOS / 🐧 Linux: [tailscale.com/download](https://tailscale.com/download)
- 🤖 Android / 🍏 iOS: cari **Tailscale** di Play Store / App Store

Pastikan statusnya **Connected** ✅. Di dashboard [admin machines](https://login.tailscale.com/admin/machines), device `dezwindows` (atau hostname pilihanmu) harus muncul dengan status online 🟢.

### 2️⃣ Ambil informasi koneksi

Buka log Actions ➜ langkah **🔐 RDP Info**:

```
======================================
 RDP INFORMATION
--------------------------------------
 IP        : 100.x.x.x
 HOST      : dezwindows
 USERNAME  : dezels
 PASSWORD  : (sesuai yang diisi saat menjalankan workflow)
 DURASI    : 6 jam
 AUTO-STOP : 02:30 WIB
======================================
```

### 3️⃣ Hubungkan menggunakan aplikasi RDP

| Perangkat | Aplikasi | Cara |
|---|---|---|
| 🪟 **Windows** | Remote Desktop Connection | Tekan `Win + R`, ketik `mstsc`, lalu Enter |
| 🍎 **macOS** | Windows App (Microsoft) | Pasang dari App Store, lalu pilih *Add PC* |
| 🤖 **Android** | Microsoft Remote Desktop / Windows App | Pasang dari Play Store, lalu pilih *Add PC* |
| 🍏 **iOS** | Windows App (Microsoft) | Pasang dari App Store, lalu pilih *Add PC* |
| 🐧 **Linux** | Remmina atau `xfreerdp` | `xfreerdp /v:100.x.x.x /u:dezels` |

Isi data koneksinya:

- 🖥️ **PC name / Computer:** IP Tailscale dari log (`100.x.x.x`) atau hostname (misalnya `dezwindows`)
- 👤 **Username:** sesuai yang kamu isi (misalnya `dezels`)
- 🔑 **Password:** sesuai yang kamu isi saat menjalankan workflow

Jika muncul peringatan sertifikat ⚠️ (*"The identity of the remote computer cannot be verified"*), itu hal yang wajar. Klik **Yes / Connect anyway**.

Selesai! Sekarang kamu sudah berada di Windows Server milikmu. 🎉

---

## ⏹️ Auto-Stop dan Batas Waktu

- ⏱️ Timer dihitung **sejak awal job**, sehingga waktu persiapan ikut terhitung.
- 🛑 Saat waktunya habis, job selesai dengan sendirinya dan runner dimatikan.
- 📏 Pilihan 6 jam dibatasi **355 menit** agar job selesai dengan rapi sebelum batas maksimal GitHub (6 jam).
- ❌ Ingin berhenti lebih cepat? Buka run-nya di tab **Actions**, lalu klik **Cancel workflow**.
- 🔁 Ingin melanjutkan setelah waktu habis? Jalankan workflow kembali (sesi baru berarti Windows baru, file lama akan hilang).

---

## 🧰 Software Opsional

| Software | Catatan |
|---|---|
| 💬 **Discord** | Diinstal ke profil user RDP. ⚠️ **Memerlukan waktu 3+ menit.** Setelah instalasi, Discord **tidak terbuka otomatis** saat login, cukup klik shortcut di Desktop. |
| 💻 **VS Code** | Versi terbaru, menggunakan system installer (untuk semua user) |
| 🗜️ **WinRAR** | Versi x64 terbaru dari situs resmi RARLAB (trial) |

Jika ada software yang gagal diinstal, RDP **tetap berjalan**. Periksa log langkah terkait untuk melihat detail error.

---

## 🩺 Pemecahan Masalah

<details>
<summary>❌ <b>Tombol "Run workflow" tidak muncul</b></summary>

- Pastikan file berada di `.github/workflows/` dan sudah di-commit ke **default branch**.
- Periksa bahwa tab **Actions** sudah aktif (jika repository hasil fork, klik tombol untuk mengaktifkan workflow).
</details>

<details>
<summary>❌ <b>Langkah "Connect Tailscale" gagal</b></summary>

- Pastikan nama secret persis **`TS_KEY`**.
- Key mungkin sudah **kedaluwarsa** atau salah salin. Buat key baru, lalu perbarui secret-nya.
</details>

<details>
<summary>❌ <b>Workflow gagal di awal: "Password minimal 6 karakter"</b></summary>

Password yang diisi kurang dari 6 karakter. Jalankan ulang dengan password yang lebih panjang.
</details>

<details>
<summary>❌ <b>RDP tidak bisa terhubung / timeout</b></summary>

- Pastikan Tailscale di perangkatmu berstatus **Connected** dan login dengan akun yang sama.
- Periksa bahwa device `dezwindows` berstatus **online** 🟢 di dashboard Tailscale.
- Coba gunakan **IP Tailscale** (`100.x.x.x`) jika hostname tidak dapat dikenali.
- Pastikan workflow masih berjalan (belum auto-stop) di tab Actions.
</details>

<details>
<summary>❌ <b>Username/password ditolak</b></summary>

Gunakan username dan password **persis** seperti yang diisi saat menjalankan workflow (huruf besar/kecil pada password berpengaruh).
</details>

<details>
<summary>❌ <b>Discord tidak muncul di Desktop</b></summary>

Periksa log langkah **Install Discord**. Jika instalasi gagal, jalankan ulang workflow dan centang Discord kembali.
</details>

---

## 🔒 Keamanan dan Catatan Penting

- 🕵️ **Gunakan repository Private.** Input `workflow_dispatch` (termasuk password) dapat terlihat di halaman run Actions. Pada repository public, orang lain dapat melihatnya.
- 🔑 **Jangan gunakan password yang sama** dengan akun penting milikmu yang lain.
- 🚫 **Jangan bagikan `TS_KEY`.** Jika bocor, segera cabut (revoke) di dashboard Tailscale.
- 👑 User RDP otomatis menjadi **Administrator**, dan kebijakan password Windows dilonggarkan agar password sederhana dapat dipakai. Hal ini aman untuk mesin sementara, tetapi jangan menyimpan data sensitif di dalamnya.
- 💸 Pada repository **private**, pemakaian Actions mengurangi kuota menit gratis bulanan (runner Windows dihitung dengan pengali yang lebih besar dibanding Linux). Repository public tidak terkena kuota.
- 🖥️ Spesifikasi runner standar (kurang lebih 4 core CPU dan 16 GB RAM), **tanpa GPU**. Tidak cocok untuk gaming atau rendering berat.

## ⚖️ Disclaimer

Project ini dibuat untuk **belajar, testing, dan eksperimen**. Ketentuan layanan GitHub Actions membatasi penggunaan runner untuk hal di luar pengembangan dan otomatisasi proyek, sehingga pemakaian berlebihan atau untuk keperluan lain dapat menyebabkan **akun/repository dibatasi atau ditangguhkan (suspend)**. Segala risiko penggunaan menjadi **tanggung jawab masing-masing pengguna**. Disarankan **tidak menggunakannya pada akun GitHub utama yang penting**.

Project ini tidak berafiliasi dengan GitHub, Microsoft, maupun Tailscale.

---

<div align="center">

### ⭐ Jika bermanfaat, jangan lupa beri bintang!

Dibuat dengan ❤️ untuk komunitas. Pull request dan saran sangat diterima! 🙌

</div>
