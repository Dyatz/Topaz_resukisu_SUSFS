# ❄️ CryoKernel build for stability

![Status](https://img.shields.io/badge/status-testing%20%E2%80%93%20belum%20dirilis-orange)
![Arch](https://img.shields.io/badge/arch-arm64-blue)
![Slot](https://img.shields.io/badge/slot-A%2FB-blue)
![Kernel](https://img.shields.io/badge/kernel-5.15-lightgrey)
![Root](https://img.shields.io/badge/root-ReSukiSU%20%2B%20SUSFS-lightgrey)

> **Stabil dulu, fitur belakangan.**
> CryoKernel adalah kernel Android kustom dengan **ReSukiSU** dan **SUSFS** yang dibangun dengan satu prinsip: setiap perubahan harus terbukti stabil sebelum sampai ke pengguna.

---

## 🚧 Status proyek

**CryoKernel masih dalam tahap pengujian (testing) dan belum dirilis.**

Belum ada file rilis resmi. Build percobaan yang muncul dari workflow di repositori ini **bukan rilis**, dan tidak disarankan dipasang di perangkat utama. Rilis publik pertama baru akan dibuat setelah semua kriteria stabilitas di bawah terpenuhi.

| Hal | Keterangan |
|---|---|
| Tahap | Pengujian internal |
| Penguji | Pengembang sendiri (belum ada tester lain) |
| Rilis publik | Setelah benar-benar stabil, tanpa tanggal target |
| Dukungan | Belum ada. Jangan diminta bantuan flash untuk build percobaan |

---

## ✨ Fitur (target)

- **ReSukiSU**: root berbasis kernel.
- **SUSFS**: penyembunyian jejak modifikasi kernel dari aplikasi tertentu (hasilnya bergantung perangkat dan aplikasi, tidak ada jaminan).
- **Clang dengan ThinLTO**, dibangun otomatis lewat GitHub Actions.
- **Hanya untuk arm64 dan perangkat A/B**: zip flash memeriksa arsitektur dan slot sebelum menyentuh partisi apa pun.
- **Pemasangan minimal**: hanya mengganti kernel di partisi `boot` slot aktif, ramdisk tidak diubah.

---

## 📋 Kriteria "stabil" sebelum rilis

Rilis publik hanya terjadi jika semua poin ini sudah tercentang:

**Boot dan dasar**
- [ ] Boot berhasil berulang kali tanpa bootloop (minimal 20 kali reboot)
- [ ] Tidak ada kernel panic atau reboot mendadak selama pemakaian harian minimal 7 hari
- [ ] Deep sleep normal, konsumsi baterai saat standby wajar

**Perangkat keras**
- [ ] Layar dan sentuhan
- [ ] Wi-Fi, Bluetooth, dan data seluler
- [ ] Kamera, audio, dan sensor
- [ ] Pengisian daya (termasuk fast charge) dan USB/OTG
- [ ] GPS, NFC, dan fingerprint (jika perangkat memilikinya)

**Root dan SUSFS**
- [ ] ReSukiSU Manager mengenali kernel dan root berfungsi
- [ ] Modul root dan aplikasi uji berjalan normal
- [ ] Konfigurasi SUSFS tidak menimbulkan crash atau lag

**Rilis**
- [ ] Proses build bisa diulang dengan hasil yang sama
- [ ] Catatan perubahan (changelog) dan cara pemulihan tertulis lengkap

---

## 🧩 Konfigurasi build saat ini

Konfigurasi ini **masih berubah selama pengujian**.

| Komponen | Nilai |
|---|---|
| Arsitektur | arm64 |
| Partisi | A/B, kernel ditulis ke `boot` slot aktif |
| Versi kernel (perangkat uji) | 5.15.197, Android 13 |
| Root | ReSukiSU (branch `main`) |
| SUSFS | Branch `gki-android13-5.15` |
| Toolchain | Clang (`LLVM=1`), ThinLTO, runner `ubuntu-24.04` |
| Opsi SUSFS aktif | `SUS_PATH`, `SUS_MOUNT`, `SUS_KSTAT`, `SPOOF_UNAME`, `OPEN_REDIRECT`, `SUS_MAP`, `HIDE_KSU_SUSFS_SYMBOLS` |
| Opsi SUSFS dimatikan | `ENABLE_LOG`, `SPOOF_CMDLINE_OR_BOOTCONFIG` (lebih berisiko di tahap awal) |
| BTF | Dimatikan |
| Hasil build | `Image` dibungkus zip AnyKernel3 |

Perangkat uji: _(Xiaomi,Redmi Note 12 4G,topaz)_

---

## ⚠️ Keterbatasan saat ini

- Hasil build hanya berisi **`Image`**. **Modul vendor dan dtb belum disertakan**, jadi perangkat memakai modul bawaan yang sudah ada di perangkat.
- Karena itu kompatibilitas dengan modul bawaan perangkat belum terjamin. Kalau tidak cocok, bisa terjadi bootloop atau ada perangkat keras yang tidak berfungsi.
- Baru diuji di **satu perangkat**.
- Source kernel yang dipakai butuh penyesuaian otomatis agar patch SUSFS bisa dipasang (lihat catatan teknis).

---

## 🛠️ Cara build

Seluruh build berjalan di server GitHub, tidak perlu komputer.

1. **Fork** repositori ini dan aktifkan **Actions**.
2. Buka **Actions → workflow build (`main.yml`) → Run workflow**, lalu isi parameter (kernel repo, branch, defconfig, branch SUSFS, branch ReSukiSU).
3. Tunggu sampai hijau. Hasilnya ada di bagian **Artifacts** pada halaman run.
4. Jalankan **Bungkus AnyKernel3 (`anykernel3.yml`)** untuk membuat zip flash. Workflow ini memeriksa bahwa `Image` benar-benar kernel arm64 sebelum dibungkus.
5. Unduh zip dari **Artifacts** run tersebut.

Kalau build gagal, buka `build.log` dan `config.log` di artifact `build-log`, atau lihat ringkasan error di halaman run.

---

## 📲 Pemasangan (khusus pengujian)

> **Hanya untuk pengujian.** Lakukan di perangkat yang siap dipulihkan.

**Sebelum flash:**
1. Backup **`boot.img` stock** (slot a dan b).
2. Bootloader sudah terbuka, dan kamu tahu cara masuk **fastboot**.
3. Siapkan PC atau cara lain untuk memulihkan lewat fastboot.
4. Backup data penting.

**Flash:** pakai Kernel Flasher atau recovery, lalu pasang zip. Zip akan menolak berjalan kalau perangkat bukan arm64 atau bukan A/B.

**Kalau bootloop:**
```
fastboot set_active a      # atau b, pindah ke slot yang masih stock
fastboot flash boot boot.img
```
Slot lainnya tidak disentuh oleh zip, jadi itu jaring pengaman utama.

---

## 🗺️ Roadmap

- [x] Build otomatis lewat GitHub Actions
- [x] Pemasangan ReSukiSU dan SUSFS otomatis
- [x] Zip AnyKernel3 khusus arm64 dan A/B
- [x] Modul vendor
- [ ] Pengujian stabilitas jangka panjang
- [ ] Pengujian di lebih dari satu perangkat
- [ ] Rilis publik pertama

---

<details>
<summary><b>Catatan teknis</b></summary>

- Patch SUSFS tidak selalu langsung cocok dengan source kernel produsen yang lebih lama. Workflow menerapkan perbaikan otomatis untuk hunk yang gagal (misalnya `fs/namespace.c`, `fs/open.c`, `fs/notify/fdinfo.c`) dan berhenti dengan log jelas kalau ada yang tidak aman ditebak.
- Pada kernel yang lebih tua, fungsi `inotify_mark_user_mask` tidak ada, sehingga diganti `mark->mask & IN_ALL_EVENTS`.
- BTF dimatikan karena tidak dibutuhkan ReSukiSU dan SUSFS, dan menghindari ketergantungan pada `pahole`.
- Build memakai `make -k` agar semua error terkumpul sekaligus, dan `pipefail` agar kegagalan `make` tidak tersembunyi.

</details>

---

## 🙏 Kredit

- [ReSukiSU](https://github.com/ReSukiSU/ReSukiSU) dan para kontributor KernelSU
- [SUSFS](https://gitlab.com/simonpunk/susfs4ksu) oleh simonpunk
- [AnyKernel3](https://github.com/osm0sis/AnyKernel3) oleh osm0sis
- Source kernel dari produsen perangkat
- Komunitas Android kustom

## 📄 Lisensi

Kernel Linux berlisensi **GPL-3.0**. Komponen lain mengikuti lisensinya masing-masing.

## ⚖️ Penafian

Kernel kustom dapat menyebabkan bootloop, kehilangan data, atau perangkat tidak berfungsi, dan dapat memengaruhi garansi serta aplikasi yang memeriksa integritas perangkat. Gunakan dengan risiko sendiri. Proyek ini tidak memberi jaminan apa pun, termasuk soal kemampuan melewati deteksi root.
