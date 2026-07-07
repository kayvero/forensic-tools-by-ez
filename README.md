
# Windows DFIR - Eric Zimmerman Tools Ringkasan

## Legenda

- **Pre / Artefak** = File yang dianalisis tool.
- **KAPE** = Apakah KAPE dapat mengumpulkan artefak dan/atau menjalankan parser tersebut melalui Module.

---

## Daftar Tool


| No | Tool | Pre / Artefak | Bentuk File | Lokasi Artefak Windows | Kegunaan | Pertanyaan yang Dijawab | KAPE |
|----|------|---------------|-------------|------------------------|----------|-------------------------|------|
|1|Autopsy|Disk Image|`.E01`, `.dd`, `.raw`, `.vmdk`|Hasil imaging|Analisis disk|File apa saja yang ada? File terhapus?|❌|
|2|MFTECmd.exe|`$MFT`, `$Boot`|File sistem NTFS|`C:\$MFT`|Metadata NTFS|Kapan file dibuat/diubah/dihapus?|✅|
|3|PECmd.exe|Prefetch|`.pf`|`C:\Windows\Prefetch\`|Evidence of Execution|Program apa dijalankan?|✅|
|4|JLECmd.exe|Jump Lists|`.automaticDestinations-ms`, `.customDestinations-ms`|`%AppData%\...\AutomaticDestinations\`|File yang dibuka|Dokumen apa dibuka?|✅|
|5|LECmd.exe|Shortcut|`.lnk`|`%AppData%\Microsoft\Windows\Recent\`|Shortcut & USB|File dari USB?|✅|
|6|WxTCmd.exe|Activities Cache|`ActivitiesCache.db`|`%LocalAppData%\ConnectedDevicesPlatform\`|Windows Timeline|Durasi penggunaan aplikasi?|✅|
|7|EvtxECmd.exe|Event Log|`.evtx`|`C:\Windows\System32\winevt\Logs\`|Windows Event Log|Login, shutdown, error?|✅|
|8|RECmd.exe|Registry Hive|`SYSTEM`, `SOFTWARE`, `SAM`, `SECURITY`, `NTUSER.DAT`, `USRCLASS.DAT`|Registry|Analisis Registry|USB, autorun, MRU?|✅|
|9|Registry Explorer|Registry Hive|Hive Registry|Sama seperti RECmd|Viewer Registry|Analisis manual|❌|
|10|AmcacheParser.exe|Amcache|`Amcache.hve`|`C:\Windows\AppCompat\Programs\`|Riwayat aplikasi|Program pernah diinstal?|✅|
|11|AppCompatCacheParser.exe|ShimCache|Hive `SYSTEM`|`C:\Windows\System32\Config\SYSTEM`|ShimCache|Executable pernah dijalankan?|✅|
|12|RBCmd.exe|Recycle Bin|`$I`, `$R`|`C:\$Recycle.Bin\`|Recycle Bin|File yang dihapus?|✅|
|13|SBECmd.exe|ShellBags|Registry Keys|`NTUSER.DAT`, `USRCLASS.DAT`|Folder History|Folder pernah dibuka?|✅|
|14|SrumECmd.exe|SRUM|`SRUDB.dat`|`C:\Windows\System32\sru\`|SRUM|Aktivitas aplikasi & jaringan?|✅|
|15|SQLECmd.exe|SQLite Database|`.db`, `.sqlite`, `History`|Chrome/Edge/App|SQLite Parser|Browser history?|✅|
|16|Timeline Explorer|CSV Output|`.csv`|Output parser|Timeline Viewer|Filter timeline|❌|
|17|EZViewer|CSV Output|`.csv`|Output parser|CSV Viewer|Membaca CSV|❌|
|18|KAPE|Live System/Disk|Folder & berbagai artefak|Seluruh sistem|Collection & Automation|Mengumpulkan artefak & menjalankan parser|—|

---

## Kapan Menggunakan Tool Ini?

| Jika Ingin Mengetahui... | Gunakan |
|--------------------------|----------|
| File pernah ada atau sudah dihapus | Autopsy, MFTECmd, RBCmd |
| Program apa yang dijalankan | PECmd |
| Dokumen apa yang dibuka | JLECmd |
| File berasal dari USB | LECmd |
| Durasi penggunaan aplikasi | WxTCmd |
| Login, logout, shutdown | EvtxECmd |
| Registry Windows | RECmd, Registry Explorer |
| Aplikasi yang pernah diinstal | AmcacheParser |
| Executable yang pernah dijalankan | AppCompatCacheParser |
| Folder yang pernah dibuka | SBECmd |
| Aktivitas jaringan aplikasi | SrumECmd |
| Browser History / SQLite | SQLECmd |
| Melihat timeline hasil analisis | Timeline Explorer |
| Membaca CSV hasil parser | EZViewer |
| Mengotomatisasi semuanya | KAPE |

---

## Urutan Belajar yang Direkomendasikan

| Level | Tool |
|-------|------|
| ⭐ Dasar | Autopsy |
| ⭐⭐ | MFTECmd |
| ⭐⭐⭐ | PECmd |
| ⭐⭐⭐ | JLECmd |
| ⭐⭐⭐ | LECmd |
| ⭐⭐⭐⭐ | WxTCmd |
| ⭐⭐⭐⭐ | EvtxECmd |
| ⭐⭐⭐⭐ | RECmd |
| ⭐⭐⭐⭐ | AmcacheParser |
| ⭐⭐⭐⭐ | AppCompatCacheParser |
| ⭐⭐⭐⭐ | RBCmd |
| ⭐⭐⭐⭐ | SBECmd |
| ⭐⭐⭐⭐⭐ | SrumECmd |
| ⭐⭐⭐⭐⭐ | SQLECmd |
| ⭐⭐⭐⭐⭐ | Timeline Explorer |
| ⭐⭐⭐⭐⭐ | EZViewer |
| 🚀 Setelah paham semuanya | KAPE |

---

## Kesimpulan Praktis

1. **Autopsy** → Melihat keseluruhan disk image dan mengekstrak artefak.
2. **Eric Zimmerman Tools** → Menganalisis artefak spesifik (MFT, Prefetch, Registry, Event Log, SRUM, dll.).
3. **Timeline Explorer / EZViewer** → Membaca, memfilter, dan menggabungkan hasil analisis CSV.
4. **KAPE** → Mengotomatisasi pengumpulan artefak dan menjalankan sebagian besar parser sehingga investigasi menjadi lebih cepat.


# Windows Registry Forensics setelah KapeTriage

Dokumen ini menjelaskan registry hive yang dikumpulkan oleh **KapeTriage**, fungsi masing-masing, lokasi asli, tool analisis yang digunakan, serta contoh pertanyaan forensik yang dapat dijawab.

---

# Alur KAPE

```
Windows
    │
    ▼
KapeTriage
(Collection)
    │
    ▼
Evidence
    │
    ├── Registry
    ├── EventLogs
    ├── Prefetch
    ├── Browser
    ├── SRUM
    ├── JumpLists
    └── dll
    │
    ▼
EZParser / RECmd / Registry Explorer
    │
    ▼
CSV / Timeline / Report
```

---

# Registry Hive

| Hive | Lokasi Asli | Fungsi | Tool |
|------|-------------|--------|------|
| SYSTEM | C:\Windows\System32\Config\SYSTEM | Konfigurasi sistem | RECmd, Registry Explorer |
| SOFTWARE | C:\Windows\System32\Config\SOFTWARE | Konfigurasi software | RECmd, Registry Explorer |
| SAM | C:\Windows\System32\Config\SAM | Database akun lokal | Registry Explorer |
| SECURITY | C:\Windows\System32\Config\SECURITY | Kebijakan keamanan | Registry Explorer |
| DEFAULT | C:\Windows\System32\Config\DEFAULT | Default user profile | Registry Explorer |
| COMPONENTS | C:\Windows\System32\Config\COMPONENTS | Windows Component Store | Registry Explorer |
| BCD | EFI\Microsoft\Boot\BCD | Boot Configuration | Registry Explorer |
| NTUSER.DAT | C:\Users\<User>\NTUSER.DAT | Aktivitas user | RECmd, Registry Explorer |
| UsrClass.dat | C:\Users\<User>\AppData\Local\Microsoft\Windows\UsrClass.dat | Explorer & Shell | RECmd, Registry Explorer |

---

# SYSTEM

Lokasi

```
C:\Windows\System32\Config\SYSTEM
```

Berisi konfigurasi Windows.

## Artefak Penting

| Artefak | Registry Path | Fungsi |
|----------|---------------|--------|
| USBSTOR | Enum\USBSTOR | Riwayat USB |
| MountedDevices | MountedDevices | Drive yang pernah dipasang |
| ComputerName | Control\ComputerName | Nama komputer |
| TimeZone | Control\TimeZoneInformation | Zona waktu |
| ShutdownTime | Control\Windows | Shutdown terakhir |
| Services | Services | Daftar service |

### Menjawab Pertanyaan

- USB apa yang pernah dipasang?
- Kapan komputer dimatikan?
- Nama komputer?
- Timezone komputer?

---

# SOFTWARE

Lokasi

```
C:\Windows\System32\Config\SOFTWARE
```

Berisi konfigurasi aplikasi.

## Artefak Penting

| Artefak | Registry Path | Fungsi |
|----------|---------------|--------|
| Uninstall | Microsoft\Windows\CurrentVersion\Uninstall | Program terinstal |
| Run | Microsoft\Windows\CurrentVersion\Run | Startup |
| RunOnce | Microsoft\Windows\CurrentVersion\RunOnce | Startup sekali |
| NetworkList | Microsoft\Windows NT\CurrentVersion\NetworkList | Network yang pernah terhubung |
| App Paths | Microsoft\Windows\CurrentVersion\App Paths | Lokasi executable |

### Menjawab Pertanyaan

- Software apa yang terinstal?
- Ada malware startup?
- Pernah terhubung ke WiFi apa?

---

# SAM

Lokasi

```
C:\Windows\System32\Config\SAM
```

Berisi akun lokal.

## Artefak

- Username
- RID
- Password Hash (memerlukan tool khusus)

### Menjawab Pertanyaan

- Siapa administrator?
- Berapa akun lokal?

---

# SECURITY

Lokasi

```
C:\Windows\System32\Config\SECURITY
```

Berisi informasi keamanan.

## Artefak

- LSA Secrets
- Cached Credentials
- Audit Policy

### Menjawab Pertanyaan

- Kebijakan keamanan?
- Credential cache?

---

# DEFAULT

Lokasi

```
C:\Windows\System32\Config\DEFAULT
```

Berisi konfigurasi default user profile.

Biasanya jarang digunakan dalam CTF.

---

# COMPONENTS

Lokasi

```
C:\Windows\System32\Config\COMPONENTS
```

Berisi informasi Windows Component Store.

Biasanya digunakan pada investigasi update Windows.

---

# BCD

Lokasi

```
EFI\Microsoft\Boot\BCD
```

Berisi konfigurasi boot.

Contoh informasi

- Boot Manager
- Recovery
- Safe Mode

---

# NTUSER.DAT

Lokasi

```
C:\Users\<User>\NTUSER.DAT
```

Hive paling penting dalam User Activity.

## Artefak Penting

| Artefak | Fungsi |
|----------|--------|
| UserAssist | Program yang dijalankan |
| RunMRU | Riwayat Win+R |
| RecentDocs | Dokumen terakhir |
| TypedURLs | URL yang diketik |
| OpenSaveMRU | File dibuka/disimpan |
| WordWheelQuery | Riwayat pencarian Start Menu |
| MountPoints2 | USB user |
| Explorer\RecentDocs | File terakhir |

### Menjawab Pertanyaan

- Program apa dijalankan?
- File apa terakhir dibuka?
- URL apa diketik?
- Apa yang diketik pada Run?
- USB milik user?

---

# UsrClass.dat

Lokasi

```
C:\Users\<User>\AppData\Local\Microsoft\Windows\UsrClass.dat
```

Berisi konfigurasi Explorer.

## Artefak

| Artefak | Fungsi |
|----------|--------|
| ShellBags | Folder yang pernah dibuka |
| BagMRU | Struktur folder |
| MuiCache | Program yang pernah dijalankan |

### Menjawab Pertanyaan

- Folder apa yang pernah diakses?
- Program apa pernah dijalankan?

---

# Struktur Output KapeTriage

```
Evidence
│
├── Registry
│   ├── SYSTEM
│   ├── SOFTWARE
│   ├── SAM
│   ├── SECURITY
│   ├── DEFAULT
│   ├── NTUSER.DAT
│   ├── UsrClass.dat
│   └── COMPONENTS
│
├── EventLogs
├── Browser
├── Prefetch
├── JumpLists
├── SRUM
├── RecycleBin
├── Amcache
└── dll
```

---

# Workflow Analisis

```
Image / Live Windows
        │
        ▼
KapeTriage
        │
        ▼
Evidence
        │
        ├── Registry
        ├── Event Logs
        ├── Browser
        ├── Prefetch
        │
        ▼
EZParser
        │
        ▼
CSV
        │
        ▼
Jika perlu investigasi manual
        │
        ▼
Registry Explorer
```

---

# Prioritas Belajar untuk CTF

## Sangat Sering Muncul

- SYSTEM
- SOFTWARE
- NTUSER.DAT
- UsrClass.dat

## Kadang Muncul

- SAM
- SECURITY

## Jarang Muncul

- DEFAULT
- COMPONENTS
- BCD

---

# Cheat Sheet Singkat

| Hive | Fokus Analisis |
|------|----------------|
| SYSTEM | USB, Shutdown, TimeZone, Computer Name |
| SOFTWARE | Installed Software, Startup, Network |
| SAM | User Account |
| SECURITY | LSA Secrets |
| NTUSER.DAT | User Activity |
| UsrClass.dat | ShellBags, Folder History |
| DEFAULT | Default Profile |
| COMPONENTS | Windows Update |
| BCD | Boot Configuration |


# Breakdown Tools Forensik Windows — Eric Zimmerman's Tools (EZtools) & Autopsy

> Catatan pendamping dari writeup **Windows Forensics 2 (TryHackMe)**.
> Fokus catatan ini: **per tool**, apa fungsinya, kapan dipakai, artefak apa yang diparsing, syntax command, dan output yang dihasilkan.
>
> Semua tools di bawah (kecuali Autopsy) adalah bagian dari **Eric Zimmerman's Tools (EZtools)** — satu suite tools gratis yang jadi standar industri buat DFIR di Windows.

---

## Daftar Isi
1. [MFTECmd.exe](#1-mftecmdexe)
2. [PECmd.exe](#2-pecmdexe)
3. [WxTCmd.exe](#3-wxtcmdexe)
4. [JLECmd.exe](#4-jlecmdexe)
5. [LECmd.exe](#5-lecmdexe)
6. [EZViewer](#6-ezviewer)
7. [Autopsy](#7-autopsy)
8. [Tabel Ringkasan Perbandingan](#8-tabel-ringkasan-perbandingan)
9. [Alur Kerja Investigasi (Kapan Pakai Tool Apa)](#9-alur-kerja-investigasi)

---

## 1. MFTECmd.exe

### Apa itu?
Tool untuk **parsing file-file internal NTFS**, terutama `$MFT` (Master File Table) dan `$Boot`. Ini tool paling "fondasi" karena MFT adalah database utama yang mencatat **semua objek (file & folder)** yang pernah ada di sebuah volume NTFS.

### Artefak yang Bisa Diparsing
| File Sumber | Isi Informasi |
|---|---|
| `$MFT` | Semua record file/folder di volume: nama, path, ukuran, timestamp (created/modified/accessed), cluster lokasi |
| `$Boot` | Info boot sector volume, termasuk **ukuran cluster** |
| `$LOGFILE` | ⚠️ **Belum didukung** oleh MFTECmd (per waktu walkthrough ini dibuat) |

### Kapan Dipakai
- Saat butuh tahu **ukuran file**, **timestamp**, atau **eksistensi file** tertentu di volume — termasuk file yang sudah dihapus (kalau record MFT-nya belum ke-overwrite).
- Saat butuh info teknis volume seperti **cluster size** (penting buat kalkulasi forensik lain, misal estimasi slack space).

### Syntax Command
```bash
# Parsing $MFT
MFTECmd.exe -f C:\path\to\$MFT --csv C:\output\folder

# Parsing $Boot
MFTECmd.exe -f C:\path\to\$Boot --csv C:\output\folder
```

### Output
File CSV yang berisi ribuan baris record — satu baris per file/folder di volume tersebut. Kolom yang biasanya dicari: `FileSize`, `Created0x10`, `LastModified0x10`, `FullPath`.

### Contoh Kasus Nyata
> "Berapa ukuran file `SceSetupLog.etl`?" → parse `$MFT`, cari nama file itu di kolom `FileName`/`FullPath`, lihat kolom `FileSize`.

---

## 2. PECmd.exe

### Apa itu?
**Prefetch Parser** — tool untuk membaca file `.pf` di folder Prefetch Windows. Ini adalah salah satu artefak **paling kuat untuk evidence of execution** (bukti program pernah dijalankan).

### Kenapa Prefetch Penting?
Windows membuat file prefetch **secara otomatis** setiap kali program dijalankan (tujuan aslinya buat mempercepat loading program di kemudian hari). Efek sampingnya buat forensik: ini jadi **log otomatis** program apa saja yang pernah jalan di sistem — walaupun programnya sendiri sudah dihapus/uninstall!

### Artefak yang Bisa Diparsing
- Lokasi sumber: `C:\Windows\Prefetch\*.pf`
- Info yang didapat per file `.pf`:
  - **Nama & path executable**
  - **Jumlah eksekusi (run count)**
  - **Waktu eksekusi terakhir** (last run time) — bahkan bisa sampai 8 run time terakhir tergantung versi Windows
  - **File & device handle** yang diakses program tersebut saat dijalankan

### Kapan Dipakai
- Investigasi malware: cek apakah file mencurigakan pernah dieksekusi, kapan, dan berapa kali.
- Menyusun timeline aktivitas user/attacker di sistem.
- Membuktikan program **pernah ada dan dijalankan** meskipun sudah dihapus dari disk.

### Syntax Command
```bash
# Parsing satu file prefetch
PECmd.exe -f C:\Windows\Prefetch\NOTEPAD.EXE-XXXXXXXX.pf --csv C:\output

# Parsing seluruh folder prefetch sekaligus (lebih umum dipakai)
PECmd.exe -d C:\Windows\Prefetch --csv C:\output
```

### Output
CSV dengan kolom penting seperti `ExecutableName`, `RunCount`, `LastRun`, `Volume0Name`, `Directories`, `FilesLoaded`.

### Contoh Kasus Nyata
> "Berapa kali `gkape.exe` dijalankan, dan kapan terakhir?" → cek kolom `RunCount` dan `LastRun` di CSV hasil parsing folder Prefetch.

---

## 3. WxTCmd.exe

### Apa itu?
Tool untuk parsing **Windows 10 Timeline**, sebuah database **SQLite** (`ActivitiesCache.db`) yang menyimpan riwayat aktivitas aplikasi & file yang dipakai user.

### Artefak yang Bisa Diparsing
- Lokasi sumber:
  `C:\Users\<username>\AppData\Local\ConnectedDevicesPlatform\{randomfolder}\ActivitiesCache.db`
- Info yang didapat:
  - Aplikasi yang dijalankan
  - **Durasi fokus** (berapa lama aplikasi itu aktif digunakan di layar)
  - Timestamp mulai & selesai aktivitas

### Bedanya dengan PECmd (Prefetch)?
| | PECmd (Prefetch) | WxTCmd (Timeline) |
|---|---|---|
| Fokus | Eksekusi program (run count, last run) | Durasi pemakaian & fokus aplikasi |
| Granularitas waktu | Per eksekusi | Per sesi aktivitas (dengan durasi) |
| Sumber data | File `.pf` per program | Satu database SQLite terpusat |

Jadi kalau Prefetch jawab **"program X dijalankan berapa kali & kapan"**, Timeline jawab **"program X dipakai berapa lama"**.

### Kapan Dipakai
- Menentukan **berapa lama** user berinteraksi dengan sebuah file/aplikasi — berguna buat membuktikan "user benar-benar membaca/menggunakan file ini" bukan cuma sekadar membuka sebentar.

### Syntax Command
```bash
WxTCmd.exe -f "C:\Users\THM-4n6\AppData\Local\ConnectedDevicesPlatform\L.THM-4n6\ActivitiesCache.db" --csv C:\output
```

### Output
CSV dengan kolom seperti `ApplicationName`, `StartTime`, `EndTime`, `Duration` (durasi fokus).

### Contoh Kasus Nyata
> "Notepad.exe dibuka jam 10:56 tanggal 30/11/2021, berapa lama in-focus?" → cek kolom durasi pada baris dengan waktu mulai tersebut → `00:00:41`.

---

## 4. JLECmd.exe

### Apa itu?
Tool untuk parsing **Jump Lists** — fitur Windows yang muncul saat kamu klik-kanan icon aplikasi di taskbar, menampilkan daftar file yang baru-baru dibuka lewat aplikasi itu.

### Artefak yang Bisa Diparsing
- Lokasi sumber:
  `C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations`
- Info yang didapat:
  - Aplikasi apa yang menjalankan/membuka file (terhubung lewat **AppID**)
  - **Waktu pertama kali** & **waktu terakhir kali** file/aplikasi itu dieksekusi
  - Path lengkap file yang dibuka

### Kegunaan Ganda (Dual Purpose)
Jump List ini unik karena bisa dipakai untuk **dua jenis bukti sekaligus**:
1. **Evidence of Execution** — program apa yang dijalankan (Task 5)
2. **File/Folder Knowledge** — file/folder apa yang pernah dibuka user (Task 6)

### Kapan Dipakai
- Mencari tahu **aplikasi mana** yang dipakai untuk membuka file tertentu (misal: "file `.txt` ini dibuka pakai program apa?").
- Melacak riwayat akses folder tertentu (first opened vs last opened).

### Syntax Command
```bash
# Parsing satu file jump list
JLECmd.exe -f C:\path\to\jumplist.automaticDestinations-ms --csv C:\output

# Parsing seluruh folder (lebih umum)
JLECmd.exe -d "C:\Users\THM-4n6\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations" --csv C:\output
```

### Output
CSV dengan kolom seperti `AppId`, `AppIdDescription`, `TargetPath` (file yang dibuka), `SourceModified`, `CreationTime` (first opened), `LastModified` (last opened).

### Contoh Kasus Nyata
> "Program apa yang dipakai buka `ChangeLog.txt`?" → cari path file itu di kolom `TargetPath`, cek `AppIdDescription` → `Notepad.exe`
> "Kapan folder `regripper` pertama & terakhir dibuka?" → cek kolom `CreationTime` vs `LastModified`.

---

## 5. LECmd.exe

### Apa itu?
**Lnk Explorer** — tool untuk parsing **shortcut files** (`.lnk`), yaitu file yang otomatis dibuat Windows setiap kali sebuah file dibuka (lokal maupun remote/network).

### Artefak yang Bisa Diparsing
- Lokasi sumber:
  - `C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\`
  - `C:\Users\<username>\AppData\Roaming\Microsoft\Office\Recent\`
- Info yang didapat:
  - **Tanggal pembuatan shortcut** → menunjukkan kapan file **pertama kali** dibuka
  - **Tanggal modifikasi shortcut** → menunjukkan kapan file **terakhir kali** diakses
  - Path lengkap file asli
  - **Info removable drive** (kalau file dibuka dari USB): volume name, volume type, serial number

### Kegunaan Ganda (Dual Purpose)
Sama seperti Jump List, `.lnk` files juga berguna untuk dua konteks:
1. **File/Folder Knowledge** (Task 6) — file apa yang pernah dibuka
2. **USB Device Forensics** (Task 7) — device USB apa yang pernah dipakai (lewat info volume serial number yang tersimpan di shortcut)

### Kapan Dipakai
- Membuktikan user **pernah membuka file spesifik**, meskipun file itu sudah dipindah/dihapus (karena shortcut-nya masih tersisa).
- Mengidentifikasi USB drive yang pernah dipakai untuk membuka file, berdasarkan serial number yang tercatat di shortcut.

### Syntax Command
```bash
LECmd.exe -f C:\path\to\shortcut.lnk --csv C:\output
# atau -d untuk parsing seluruh folder shortcut sekaligus
```

### Output
CSV dengan kolom seperti `SourceCreated` (first opened), `SourceModified` (last opened), `LocalPath`/`NetworkPath`, `VolumeSerialNumber`, `VolumeLabel`.

---

## 6. EZViewer

### Apa itu?
Bukan parser — ini **viewer/pembaca** untuk membuka hasil output CSV dari semua tool EZtools di atas (juga bisa buat buka file CSV umum lainnya).

### Kenapa Perlu Tool Khusus?
CSV hasil parsing MFTECmd/PECmd/dll biasanya punya **puluhan kolom dan ribuan baris**, sehingga kurang nyaman kalau dibuka di text editor biasa. EZViewer bikin tampilan CSV lebih rapi & mudah di-filter/cari.

### Kapan Dipakai
- Setiap kali habis menjalankan tool parsing EZtools manapun dan mau **melihat hasilnya** dengan nyaman.

### Cara Pakai
Cukup buka file `.csv` hasil parsing lewat aplikasi EZViewer (ada di folder EZtools).

---

## 7. Autopsy

### Apa itu?
**GUI forensic suite lengkap**, jauh lebih kompleks dari tools EZtools yang sifatnya single-purpose command-line. Autopsy dipakai untuk analisis **disk image** secara menyeluruh.

### Fitur yang Dipakai di Room Ini

| Fitur | Fungsi |
|---|---|
| **Data Source: Disk Image or VM File** | Untuk load & analisis full disk image (`.001`, `.E01`, dll) |
| **Data Source: Logical Files** | Untuk load folder biasa (bukan full disk image) — dipakai buat analisis triage folder |
| **File Views / Deleted Files** | Menampilkan file yang sudah dihapus (ditandai ikon **X**), bisa langsung di-**Extract** untuk recovery |
| **Ingest Module: Recent Activity** | Modul khusus untuk parsing **web history** (termasuk IE/Edge `WebCacheV*.dat`) |

### Kapan Dipakai
1. **Recovery file yang dihapus** dari disk image (Task 4) — karena Autopsy bisa scan area unallocated dan menemukan sisa-sisa file yang belum ter-overwrite.
2. **Analisis web history / IE-Edge cache** (Task 6) — karena parsing `WebCacheV*.dat` butuh engine khusus yang sudah built-in di Autopsy, gak ada tool CLI sederhana dari EZtools untuk ini.

### Alur Kerja Umum
```
New Case → isi nama case → Finish
  → pilih tipe data source (Disk Image / Logical Files)
  → arahkan ke lokasi image/folder
  → pilih ingest modules (Deselect All jika hanya butuh browsing manual,
     atau centang "Recent Activity" saja jika butuh web history)
  → tunggu proses loading
  → expand Data Sources di panel kiri → browse/cari file
```

### Kenapa "Deselect All" di Ingest Modules?
Karena ingest modules Autopsy (hash lookup, keyword search, dll) **butuh waktu lama untuk memproses** seluruh disk image. Kalau tujuan kita cuma butuh browsing manual/cari file spesifik (bukan full scan), lebih efisien matikan semua modul dulu.

### Tips Ekstraksi File Terhapus
1. Cari file dengan ikon **X** (menandakan file terhapus tapi datanya masih bisa dibaca)
2. Klik kanan → **Extract File(s)**
3. Pilih lokasi simpan → file berhasil direcover dalam bentuk aslinya

---

## 8. Tabel Ringkasan Perbandingan

| Tool | Kategori | Artefak Sumber | Pertanyaan yang Dijawab |
|---|---|---|---|
| **MFTECmd.exe** | File System (NTFS) | `$MFT`, `$Boot` | Metadata file, cluster size volume |
| **PECmd.exe** | Evidence of Execution | Prefetch `.pf` | Program apa dijalankan, berapa kali, kapan terakhir |
| **WxTCmd.exe** | Evidence of Execution | `ActivitiesCache.db` (Timeline) | Berapa lama aplikasi/file dipakai (durasi fokus) |
| **JLECmd.exe** | Execution + File Knowledge | Jump Lists | Program apa buka file X; kapan folder pertama/terakhir dibuka |
| **LECmd.exe** | File Knowledge + USB Forensics | Shortcut `.lnk` | Kapan file dibuka; info USB drive dari shortcut |
| **EZViewer** | Utility/Viewer | CSV hasil parsing | (bukan parser, cuma penampil hasil) |
| **Autopsy** | Disk Forensics (GUI) | Disk image, folder logical, web cache | Recovery file terhapus, riwayat web/file lokal |

---

## 9. Alur Kerja Investigasi

Kalau ditanya **"harus mulai dari mana pas dapet 1 disk image/triage folder?"**, urutan logis yang masuk akal:

```
1. Kenali file system-nya dulu (FAT/NTFS)
        ↓
2. Kalau NTFS → parse $MFT & $Boot pakai MFTECmd
   (dapat gambaran umum isi volume + cluster size)
        ↓
3. Cari bukti program yang dijalankan:
   - PECmd → Prefetch (run count + last run time)
   - WxTCmd → Timeline (durasi pemakaian)
        ↓
4. Cari bukti file yang dibuka user:
   - LECmd → Shortcut files (first/last opened + info USB)
   - JLECmd → Jump Lists (program + file yang dibuka)
   - Autopsy (Recent Activity module) → web/local file history
        ↓
5. Kalau curiga ada file yang sengaja dihapus untuk menutupi jejak:
   - Autopsy → cari file bertanda X → Extract untuk recovery
        ↓
6. Semua hasil CSV → buka & analisis pakai EZViewer
```


# Skenario Penggunaan Tools Forensik Windows

## Kasus

Seorang karyawan diduga membocorkan dokumen rahasia perusahaan sebelum resign.

Laptop disita dan dibuat **disk image**. Investigator ingin mengetahui:

- Apakah dokumen pernah dibuka?
- Program apa yang digunakan?
- Kapan terakhir dibuka?
- Apakah menggunakan USB?
- Apakah file dihapus?
- Berapa lama file dikerjakan?

---

# 1. Autopsy

**Fungsi**
- Melihat isi disk image.
- Mencari file, browser history, recycle bin, dan file yang terhapus.

**Menjawab**
- Apakah file masih ada?
- Apakah file sudah dihapus?
- Di mana lokasi file?

**Contoh Hasil**

```
Proposal_Final.docx ditemukan.
Status: Deleted
Lokasi: D:\Project\
```

**Kesimpulan**

✔ File memang pernah ada.

---

# 2. MFTECmd

**Artefak**

```
$MFT
```

**Fungsi**
Membaca metadata file NTFS.

**Menjawab**
- Kapan file dibuat?
- Kapan diubah?
- Kapan dihapus?

**Contoh Hasil**

```
Created : 20 Juni
Modified: 27 Juni
Deleted : 28 Juni 17:14
```

**Kesimpulan**

✔ File dihapus pada 28 Juni pukul 17:14.

---

# 3. PECmd

**Artefak**

```
Prefetch (.pf)
```

**Fungsi**
Melihat aplikasi yang pernah dijalankan.

**Menjawab**
- Program apa yang dijalankan?
- Berapa kali dijalankan?
- Kapan terakhir dijalankan?

**Contoh Hasil**

```
WINWORD.EXE

Run Count : 57
Last Run  : 28 Juni 17:05
```

**Kesimpulan**

✔ Microsoft Word dijalankan sebelum file dihapus.

---

# 4. JLECmd

**Artefak**

```
Jump Lists
```

**Fungsi**
Melihat file yang dibuka oleh suatu aplikasi.

**Menjawab**
- File apa yang dibuka?
- Folder apa yang dibuka?
- Kapan terakhir dibuka?

**Contoh Hasil**

```
WINWORD.EXE

Opened:
Proposal_Final.docx

Last Open:
28 Juni 17:04
```

**Kesimpulan**

✔ Word benar-benar membuka file tersebut.

---

# 5. LECmd

**Artefak**

```
Shortcut (.lnk)
```

**Fungsi**
Melihat informasi shortcut Windows.

**Menjawab**
- File apa yang pernah dibuka?
- Lokasi file?
- Apakah berasal dari USB?

**Contoh Hasil**

```
Target:
D:\Project\Proposal_Final.docx

Volume:
KINGSTON

Drive Serial:
1234-ABCD
```

**Kesimpulan**

✔ Ada indikasi file diakses menggunakan USB Kingston.

---

# 6. WxTCmd

**Artefak**

```
ActivitiesCache.db
```

**Fungsi**
Membaca Windows Timeline.

**Menjawab**
- Berapa lama aplikasi digunakan?
- Berapa lama file dikerjakan?

**Contoh Hasil**

```
WINWORD.EXE

Focus Duration:
2 Jam 34 Menit
```

**Kesimpulan**

✔ Dokumen dikerjakan cukup lama.

---

# Timeline Hasil Investigasi

| Waktu | Bukti |
|-------|-------|
|17:00|Word dijalankan (PECmd)|
|17:04|Proposal dibuka (JLECmd)|
|17:04|Shortcut menunjukkan USB Kingston (LECmd)|
|17:05|Word terakhir dijalankan (PECmd)|
|17:14|File dihapus (MFTECmd)|
|2 jam 34 menit|Word aktif (WxTCmd)|

Autopsy melengkapi dengan file yang telah dihapus dan lokasi penyimpanannya.

---

# Alur Penggunaan

```
Disk Image
     │
     ▼
 Autopsy
     │
     ├── File ditemukan
     │
     ├── MFTECmd → Kapan file dibuat/diubah/dihapus
     │
     ├── PECmd → Program apa yang dijalankan
     │
     ├── JLECmd → File apa yang dibuka
     │
     ├── LECmd → Apakah berasal dari USB
     │
     └── WxTCmd → Berapa lama aplikasi digunakan
                    │
                    ▼
          Susun Timeline Investigasi
```

---

# Ringkasan

| Tool | Fungsi Utama |
|------|---------------|
|Autopsy|Melihat isi disk image, recovery file, browser history|
|MFTECmd|Metadata file (Create, Modify, Delete)|
|PECmd|Riwayat aplikasi yang dijalankan|
|JLECmd|File/folder yang dibuka oleh aplikasi|
|LECmd|Informasi shortcut dan indikasi penggunaan USB|
|WxTCmd|Durasi penggunaan aplikasi/file (Windows Timeline)|

## Cara Mengingat

- **Autopsy** → Cari file.
- **MFTECmd** → Kapan file berubah.
- **PECmd** → Program apa yang dijalankan.
- **JLECmd** → File apa yang dibuka.
- **LECmd** → Dari mana file berasal (termasuk USB).
- **WxTCmd** → Berapa lama pengguna mengerjakan file.

---

*Catatan pendamping untuk writeup Windows Forensics 2 (TryHackMe). Disusun untuk referensi cepat per-tool.*
