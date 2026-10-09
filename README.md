*Choose language / Pilih bahasa:*
* [🇬🇧 English](#-english)
* [🇮🇩 Bahasa Indonesia](#-bahasa-indonesia)

---

# 🇬🇧 English

# ❄️ CryoKernel build for stability

![Status](https://img.shields.io/badge/status-testing%20%E2%80%93%20unreleased-orange)
![Arch](https://img.shields.io/badge/arch-arm64-blue)
![Slot](https://img.shields.io/badge/slot-A%2FB-blue)
![Kernel](https://img.shields.io/badge/kernel-5.15-lightgrey)
![Root](https://img.shields.io/badge/root-ReSukiSU%20%2B%20SUSFS-lightgrey)

> **Stability first, features later.**
> CryoKernel is a custom Android kernel with **ReSukiSU** and **SUSFS** built on one principle: every change must be proven stable before reaching the user.

---

## 🚧 Project Status

**CryoKernel is still in the testing phase and has not been released.**

There are no official release files yet. The experimental builds generated from the workflows in this repository are **not releases**, and it is highly discouraged to flash them on your daily driver. The first public release will only be made once all the stability criteria below are met.

| Item | Description |
|---|---|
| Stage | Internal testing |
| Tester | Developer only (no other testers yet) |
| Public Release | Once completely stable, no target date (ETA) |
| Support | None yet. Do not ask for flashing assistance for test builds |

---

## ✨ Features (Target)

- **ReSukiSU**: kernel-based root.
- **SUSFS**: hides traces of kernel modifications from specific apps (results depend on the device and app, no guarantees).
- **Clang with Full LTO**, built automatically via GitHub Actions.
- **Various options for the kernel**: Flashable zip will provide extensive options such as CLO.KSU/CLO.No.KSU.
- **Minimal installation**: only replaces the kernel in the active slot's `boot` partition, leaving the ramdisk untouched.

---

## 📋 "Stable" Criteria Before Release

A public release will only happen if all these checkboxes are ticked:

**Boot and Basics**
- [ ] Boots successfully multiple times without bootlooping (minimum 20 reboots)
- [ ] No kernel panics or random reboots during daily usage for at least 7 days
- [ ] Deep sleep works normally, standby battery consumption is reasonable

**Hardware**
- [ ] Screen and touch
- [ ] Wi-Fi, Bluetooth, and cellular data
- [ ] Camera, audio, and sensors
- [ ] Charging (including fast charge) and USB/OTG
- [ ] GPS, NFC, and fingerprint (if the device has them)

**Root and SUSFS**
- [ ] ReSukiSU Manager recognizes the kernel and root functions properly
- [ ] Root modules and test apps run normally
- [ ] SUSFS configuration does not cause crashes or lags

**Release**
- [ ] Build process is reproducible with the same results
- [ ] Changelog and recovery instructions are fully documented

---

## 🧩 Current Build Configuration

This configuration **is subject to change during testing**.

| Component | Value |
|---|---|
| Architecture | arm64 |
| Partition | A/B, kernel written to active `boot` slot |
| Kernel Version (Test Device)| 5.15.197, Android 13 |
| Root | ReSukiSU (`main` branch) |
| SUSFS | `gki-android13-5.15` branch |
| Toolchain | Clang (`LLVM=1`), Full LTO, runner `ubuntu-24.04` |
| Active SUSFS Options | `SUS_PATH`, `SUS_MOUNT`, `SUS_KSTAT`, `SPOOF_UNAME`, `OPEN_REDIRECT`, `SUS_MAP`, `HIDE_KSU_SUSFS_SYMBOLS` |
| Disabled SUSFS Options| `ENABLE_LOG`, `SPOOF_CMDLINE_OR_BOOTCONFIG` (too risky in early stages) |
| BTF | Disabled |
| Build Output | `Image` wrapped in an AnyKernel3 zip |

Test Device: _(Xiaomi, Redmi Note 12 4G, topaz)_

---

## ⚠️ Current Limitations

- The build output only contains the **`Image`**. **Vendor modules and dtb are not included**, so the device will use its existing pre-installed modules.
- Because of this, compatibility with stock modules is not guaranteed. Mismatches can cause bootloops or broken hardware functionality.
- Currently only tested on **one device**.
- The used kernel source requires automatic adjustments to apply the SUSFS patch (see technical notes).

---

## 🛠️ How to Build

All builds run on GitHub servers, no PC required.

1. **Fork** this repository and enable **Actions**.
2. Go to **Actions → build workflow (`main.yml`) → Run workflow**, then fill in the parameters (kernel repo, branch, defconfig, SUSFS branch, ReSukiSU branch).
3. Wait for it to turn green. The output will be in the **Artifacts** section of the run page.
4. Run **Wrap AnyKernel3 (`anykernel3.yml`)** to create the flashable zip. This workflow verifies that the `Image` is truly an arm64 kernel before packaging.
5. Download the zip from that run's **Artifacts**.

If the build fails, check `build.log` and `config.log` in the `build-log` artifact, or view the error summary on the run page.

---

## 📲 Installation (Testing Only)

> **For testing purposes only.** Only do this on a device you are prepared to recover.

**Before flashing:**
1. Backup the **stock `boot.img`** (both slot a and b).
2. Bootloader must be unlocked, and you must know how to enter **fastboot**.
3. Have a PC ready or another method to recover via fastboot.
4. Backup important data.

**Flashing:** Use Kernel Flasher or a custom recovery to install the zip. The zip will abort if the device is not arm64 or not A/B.

**In case of bootloop:**
your previous boot.img make sure that you backup boot.img before installing this kernel
```bash
fastboot flash boot boot.img
```
The other slot is untouched by the zip, acting as your primary safety net.

---

## 🗺️ Roadmap

- [x] Automated build via GitHub Actions
- [x] Automated ReSukiSU and SUSFS installation
- [x] AnyKernel3 zip strictly for arm64 and A/B
- [x] Vendor modules
- [ ] Long-term stability testing
- [ ] Testing on more than one device
- [ ] First public release

---

<details>
<summary><b>Technical Notes</b></summary>

- The SUSFS patch doesn't always cleanly apply to older OEM kernel sources. The workflow applies automatic fixes for failed hunks (e.g., `fs/namespace.c`, `fs/open.c`, `fs/notify/fdinfo.c`) and aborts with clear logs if an unsafe guess occurs.
- On older kernels, the `inotify_mark_user_mask` function is missing, so it is replaced with `mark->mask & IN_ALL_EVENTS`.
- BTF is disabled because it is not needed by ReSukiSU and SUSFS, and to avoid dependencies on `pahole`.
- The build uses `make -k` to gather all errors at once, and `pipefail` so `make` failures aren't hidden.

</details>

---

## 🙏 Credits

- [ReSukiSU](https://github.com/ReSukiSU/ReSukiSU) and KernelSU contributors
- [SUSFS](https://gitlab.com/simonpunk/susfs4ksu) by simonpunk
- [AnyKernel3](https://github.com/osm0sis/AnyKernel3) by osm0sis
- OEM kernel sources
- The custom Android community

## 📄 License

The Linux Kernel is licensed under **GPL-3.0**. Other components follow their respective licenses.

## ⚖️ Disclaimer

Custom kernels can cause bootloops, data loss, or bricked hardware, and may void your warranty or affect apps that check device integrity. Use at your own risk. This project provides no guarantees, including regarding its ability to bypass root detection.

<br>

---

# 🇮🇩 Bahasa Indonesia

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
- **Clang dengan Full LTO**, dibangun otomatis lewat GitHub Actions.
- **Berbagai macam pilihan kernel**: zip flash akan memberikan opsi yang diperluas sehingga dapat menyesuaikan keinginan user.
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
| Toolchain | Clang (`LLVM=1`), Full LTO, runner `ubuntu-24.04` |
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
Boot.img yang sebelumnya di-backup di flash lagi memakai perintah yang ada di bawah 👇
```bash
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
- Source kernel dari produsen perangkat dan komunitas yang hebat dan bijaksana 
- Komunitas Android kustom

## 📄 Lisensi

Kernel Linux berlisensi **GPL-3.0**. Komponen lain mengikuti lisensinya masing-masing.

## ⚖️ Penafian

Kernel kustom dapat menyebabkan bootloop, kehilangan data, atau perangkat tidak berfungsi, dan dapat memengaruhi garansi serta aplikasi yang memeriksa integritas perangkat. Gunakan dengan risiko sendiri. Proyek ini tidak memberi jaminan apa pun, termasuk soal kemampuan melewati deteksi root.