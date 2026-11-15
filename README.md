<div align="center">

# 🖥️ Windows RDP via GitHub Actions

**Bikin Windows Server RDP sementara, gratis, langsung dari GitHub Actions + Tailscale** ⚡

![Windows](https://img.shields.io/badge/Windows-2019%20%7C%202022%20%7C%202025-0078D6?logo=windows&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-2088FF?logo=githubactions&logoColor=white)
![Tailscale](https://img.shields.io/badge/Tailscale-VPN-242424?logo=tailscale&logoColor=white)
![Durasi](https://img.shields.io/badge/Durasi-1--6%20Jam-brightgreen)

</div>

---

## 📖 Apa ini?

Workflow GitHub Actions yang nyalain **Windows Server** di runner GitHub, lalu bukain akses **Remote Desktop (RDP)** lewat **Tailscale**. Jadi lo bisa punya Windows sementara buat nyoba-nyoba, testing, atau kerjaan ringan, tanpa harus punya PC sendiri. 🚀

> ⚠️ Sifatnya **sementara**. Begitu waktunya habis, runner dihapus dan **semua file di dalamnya hilang**. Simpan kerjaan lo ke tempat lain (GitHub, Google Drive, dll).

## ✨ Fitur

- 🪟 **Pilih versi Windows**: Server 2019, 2022, atau 2025
- 👤 **Username & password sendiri** diisi pas trigger (minimal 6 karakter)
- ⏱️ **Pilih durasi 1–6 jam** dengan **auto-stop**, job berhenti sendiri pas waktunya habis
- 🔐 **Akses lewat Tailscale**, jadi nggak perlu IP publik atau port forwarding
- 🧰 **Install software otomatis** (opsional, versi terbaru):
  - 💬 Discord
  - 💻 Visual Studio Code
  - 🗜️ WinRAR
- 🙈 Password di-mask di log Actions
- 🕒 Info jam auto-stop dalam WIB ditampilkan di log

---

## 📋 Persiapan

Yang lo butuhin sebelum mulai:

| Kebutuhan | Keterangan |
|---|---|
| 🐙 Akun GitHub | Buat repo dan jalanin Actions |
| 🌐 Akun Tailscale | Daftar gratis di [tailscale.com](https://tailscale.com) |
| 📱 Tailscale di perangkat lo | HP/PC/laptop yang dipakai buat connect, harus login **akun Tailscale yang sama** |
| 🖱️ Aplikasi Remote Desktop | Lihat bagian [cara connect](#-cara-connect-ke-rdp) |

---

## 🛠️ Langkah 1: Bikin Repository

1. Buka [github.com/new](https://github.com/new) ➜ bikin repo baru.
2. 🔒 **Pilih Private** (sangat disarankan, lihat bagian [Keamanan](#-keamanan--catatan-penting)).
3. Klik **Create repository**.

## 📝 Langkah 2: Bikin Workflow

1. Di repo lo, klik **Add file** ➜ **Create new file**.
2. Di kolom nama file, ketik persis:
   ```
   .github/workflows/rdp.yml
   ```
   > 💡 Pas ngetik `/`, GitHub otomatis bikin foldernya.
3. Copy **seluruh isi** file [`dezwindows.yml`](./dezwindows.yml) ke editor.
4. Klik **Commit changes** ✅ (commit ke branch `main`/default).

> ⚠️ Workflow dengan trigger manual (`workflow_dispatch`) **harus ada di default branch** biar tombol *Run workflow* muncul.

## 🔑 Langkah 3: Setup `TS_KEY` (Tailscale Auth Key)

`TS_KEY` itu kunci buat runner GitHub bisa gabung ke jaringan Tailscale lo.

### 3.1 Generate key di Tailscale

1. Buka 👉 [login.tailscale.com/admin/settings/keys](https://login.tailscale.com/admin/settings/keys)
2. Klik **Generate auth key...**
3. Atur opsinya:

   | Opsi | Saran | Kenapa |
   |---|---|---|
   | ♻️ **Reusable** | ✅ Aktifkan | Biar key bisa dipakai tiap kali workflow jalan |
   | 👻 **Ephemeral** | ✅ Aktifkan | Device runner otomatis kehapus dari daftar setelah offline |
   | ⏳ **Expiration** | Sesuai kebutuhan (maks 90 hari) | Key kedaluwarsa = workflow gagal connect |
   | 🏷️ **Tags** | Kosongin aja | Kecuali lo pakai ACL tags |

4. Klik **Generate key**, lalu **copy** key-nya. Bentuknya kayak `tskey-auth-xxxxxxxxxxxx`.

> 🚨 Key cuma ditampilkan **sekali**. Jangan dishare ke siapa pun dan jangan ditaruh di kode.

### 3.2 Simpan ke GitHub Secrets

1. Di repo lo ➜ **Settings** ➜ **Secrets and variables** ➜ **Actions**
2. Klik **New repository secret**
3. Isi:
   - **Name:** `TS_KEY` (huruf besar semua, harus persis)
   - **Secret:** paste key tadi
4. Klik **Add secret** ✅

---

## ▶️ Cara Menjalankan

1. Buka tab **Actions** di repo lo.
2. Pilih workflow **Windows RDP - 1-6 Jam** di sebelah kiri.
3. Klik **Run workflow** (tombol di kanan).
4. Isi form:

   | Input | Deskripsi | Default |
   |---|---|---|
   | 🪟 `windows_version` | Versi Windows Server | `windows-2022` |
   | 👤 `rdp_user` | Username RDP | `dezels` |
   | 🔑 `rdp_pass` | Password RDP (min. 6 karakter) | *(wajib diisi)* |
   | 🏷️ `ts_hostname` | Nama device di Tailscale | `dezwindows` |
   | ⏱️ `duration_hours` | Durasi 1–6 jam, auto-stop setelahnya | `6` |
   | 💬 `install_discord` | Install Discord terbaru ⚠️ butuh 3+ menit | ❌ |
   | 💻 `install_vscode` | Install VS Code terbaru | ❌ |
   | 🗜️ `install_winrar` | Install WinRAR terbaru | ❌ |

5. Klik **Run workflow** hijau ✅
6. Tunggu sampai step **🔐 RDP Info** muncul di log. Di situ ada **IP, hostname, username, dan jam auto-stop** 🎉

> 🕐 Proses setup biasanya makan beberapa menit. Install Discord paling lama (**3+ menit**), jadi sabar ya. 😴

---

## 🔌 Cara Connect ke RDP

### 1️⃣ Nyalain Tailscale di perangkat lo

Install Tailscale di perangkat yang mau dipakai connect, lalu **login dengan akun yang sama** dengan yang bikin `TS_KEY`.

- 🪟 Windows / 🍎 macOS / 🐧 Linux: [tailscale.com/download](https://tailscale.com/download)
- 🤖 Android / 🍏 iOS: cari **Tailscale** di Play Store / App Store

Pastikan statusnya **Connected** ✅. Di dashboard [admin machines](https://login.tailscale.com/admin/machines), device `dezwindows` (atau hostname pilihan lo) harus muncul dan statusnya online 🟢.

### 2️⃣ Ambil info koneksi

Buka log Actions ➜ step **🔐 RDP Info**:

```
======================================
 RDP INFORMATION
--------------------------------------
 IP        : 100.x.x.x
 HOST      : dezwindows
 USERNAME  : dezels
 PASSWORD  : (sesuai yang lo isi pas trigger)
 DURASI    : 6 jam
 AUTO-STOP : 02:30 WIB
======================================
```

### 3️⃣ Connect pakai aplikasi RDP

| Perangkat | Aplikasi | Cara |
|---|---|---|
| 🪟 **Windows** | Remote Desktop Connection | Tekan `Win + R` ➜ ketik `mstsc` ➜ Enter |
| 🍎 **macOS** | Windows App (Microsoft) | Install dari App Store ➜ *Add PC* |
| 🤖 **Android** | Microsoft Remote Desktop / Windows App | Install dari Play Store ➜ *Add PC* |
| 🍏 **iOS** | Windows App (Microsoft) | Install dari App Store ➜ *Add PC* |
| 🐧 **Linux** | Remmina atau `xfreerdp` | `xfreerdp /v:100.x.x.x /u:dezels` |

Isi data koneksinya:

- 🖥️ **PC name / Computer:** IP Tailscale dari log (`100.x.x.x`) atau hostname (mis. `dezwindows`)
- 👤 **Username:** sesuai yang lo isi (mis. `dezels`)
- 🔑 **Password:** sesuai yang lo isi pas trigger

Kalau muncul peringatan sertifikat ⚠️ (*"The identity of the remote computer cannot be verified"*), itu normal. Klik **Yes / Connect anyway**.

Selesai! Lo sekarang ada di Windows Server lo. 🎉

---

## ⏹️ Auto-Stop & Batas Waktu

- ⏱️ Timer dihitung **dari awal job**, jadi waktu setup ikut kehitung.
- 🛑 Pas waktunya habis, job selesai sendiri dan runner dimatiin.
- 📏 Pilihan 6 jam dibatasi **355 menit** supaya job selesai rapi sebelum batas keras GitHub (6 jam).
- ❌ Mau berhenti lebih cepat? Buka run-nya di tab **Actions** ➜ klik **Cancel workflow**.
- 🔁 Mau lanjut setelah habis? Jalanin workflow lagi (sesi baru = Windows baru, file lama hilang).

---

## 🧰 Software Opsional

| Software | Catatan |
|---|---|
| 💬 **Discord** | Diinstall ke profil user RDP. ⚠️ **Memerlukan waktu 3+ menit.** Setelah install, Discord **nggak kebuka otomatis** pas login, tinggal klik shortcut di Desktop. |
| 💻 **VS Code** | Versi terbaru, system installer (untuk semua user) |
| 🗜️ **WinRAR** | Versi x64 terbaru dari situs resmi RARLAB (trial) |

Kalau ada software yang gagal diinstall, RDP **tetap jalan**. Cek log step-nya buat detail error.

---

## 🩺 Troubleshooting

<details>
<summary>❌ <b>Tombol "Run workflow" nggak muncul</b></summary>

- Pastikan file ada di `.github/workflows/` dan sudah di-commit ke **default branch**.
- Cek tab **Actions** udah aktif (kalau repo hasil fork, klik tombol enable workflows).
</details>

<details>
<summary>❌ <b>Step "Connect Tailscale" gagal</b></summary>

- Pastikan secret bernama persis **`TS_KEY`**.
- Key mungkin udah **kedaluwarsa** atau salah copy. Generate key baru dan update secret-nya.
</details>

<details>
<summary>❌ <b>Workflow gagal di awal: "Password minimal 6 karakter"</b></summary>

Password yang lo isi kurang dari 6 karakter. Jalanin ulang dengan password yang lebih panjang.
</details>

<details>
<summary>❌ <b>RDP nggak bisa connect / timeout</b></summary>

- Pastikan Tailscale di perangkat lo **Connected** dan login akun yang sama.
- Cek device `dezwindows` **online** 🟢 di dashboard Tailscale.
- Coba pakai **IP Tailscale** (`100.x.x.x`) kalau hostname nggak ke-resolve.
- Pastikan workflow masih jalan (belum auto-stop) di tab Actions.
</details>

<details>
<summary>❌ <b>Username/password ditolak</b></summary>

Pakai username dan password **persis** seperti yang diisi pas trigger (huruf besar/kecil berpengaruh di password).
</details>

<details>
<summary>❌ <b>Discord nggak muncul di Desktop</b></summary>

Cek log step **Install Discord**. Kalau install gagal, jalanin ulang workflow dan centang Discord lagi.
</details>

---

## 🔒 Keamanan & Catatan Penting

- 🕵️ **Pakai repo Private.** Input `workflow_dispatch` (termasuk password) bisa kelihatan di halaman run Actions. Di repo public, orang lain bisa liat.
- 🔑 **Jangan pakai password yang sama** dengan akun lo yang lain.
- 🚫 **Jangan share `TS_KEY`.** Kalau bocor, langsung revoke di dashboard Tailscale.
- 👑 User RDP otomatis jadi **Administrator** dan password policy Windows dilonggarin biar password simpel bisa dipakai. Itu aman buat mesin sementara, tapi jangan simpan data sensitif di situ.
- 💸 Di repo **private**, pemakaian Actions ngurangin kuota menit gratis bulanan (runner Windows dihitung dengan pengali lebih besar dari Linux). Repo public nggak kena kuota.
- 🖥️ Spek runner standar (kurang lebih 4 core CPU & 16 GB RAM), **tanpa GPU**. Nggak cocok buat gaming atau render berat.

## ⚖️ Disclaimer

Project ini dibuat untuk **belajar, testing, dan eksperimen**. Ketentuan layanan GitHub Actions membatasi pemakaian runner untuk hal di luar pengembangan dan otomatisasi proyek, jadi pemakaian berlebihan atau untuk keperluan lain bisa bikin **akun/repo lo dibatasi atau di-suspend**. Segala risiko pemakaian jadi **tanggung jawab masing-masing**. Disarankan **jangan pakai di akun GitHub utama yang penting**.

Project ini nggak berafiliasi dengan GitHub, Microsoft, maupun Tailscale.

---

<div align="center">

### ⭐ Kalau berguna, kasih bintang ya!

Dibuat dengan ❤️ buat komunitas. Pull request dan saran selalu welcome! 🙌

</div>
