# Developer Workspace

**Developer Workspace** adalah aplikasi desktop personal *offline-first* yang memadukan manajemen tugas (Smart Kanban), *snippet manager*, Pomodoro timer, serta pelacak hidrasi dan kalori harian.

Didesain khusus untuk *software engineer*, aplikasi ini berjalan senyap di System Tray, mengamati aktivitas Git lokal secara non-intrusif, dan menjamin **nol interupsi kesehatan saat sesi fokus kerja berlangsung**.

## Fitur Utama

* **Smart Kanban & Git Automation (v1.2):**

  * Kolom alur kerja: `To Do`, `In Progress`, `Review`, dan `Done`.

  * 4 tingkat prioritas visual (*Urgent*, *High*, *Medium*, *Low*) dengan indikator tenggat waktu lokal (*deadline*).

  * Integrasi Git lokal *read-only* (polling efisien \~4ms): otomatis memindahkan kartu dari *To Do* ke *In Progress* saat commit pertama terdeteksi pada branch yang ditautkan (mendukung penamaan branch tiket kantor seperti `fix/PAY-482-timeout` tanpa trailer khusus).

  * Navigasi dan pemindahan kartu penuh via keyboard serta sistem *soft-delete* dengan toast *undo*.

* **Pomodoro Background Engine & Silent Buffer:**

  * Timer dikelola langsung oleh proses Rust (berbasis monotonic clock)—tidak terhenti saat jendela ditutup ke Tray atau saat WebView tertidur.

  * Notifikasi kontekstual cerdas: mendeteksi status *Do Not Disturb (DND)* dan aplikasi *fullscreen*. Pengingat istirahat/minum ditahan hingga kondisi layar aman.

* **Local Code Snippet & Markdown Notes:**

  * Penyimpanan catatan kode dengan *syntax highlighting* multi-bahasa (TypeScript, Rust, Python, Go, SQL, Bash, dll.).

  * Indeks pencarian teks instan berbasis **SQLite FTS5**.

  * Fitur *Quick Copy* raw clipboard dan *auto-save* debounce 750 ms.

* **Wellness Tracker (Offline-First):**

  * Estimasi kebutuhan energi istirahat (Mifflin–St Jeor) dan heuristik target hidrasi harian.

  * Pencatatan air minum cepat (+150ml, +250ml, +300ml, +500ml) serta pencatatan makanan dengan basis data USDA lokal.

* **Privasi & Keamanan Data:**

  * 100% data tersimpan di komputer pengguna (SQLite). Tidak membutuhkan pembuatan akun, tanpa telemetri default, dan mendukung *consistent snapshot backup & restore*.

## Tech Stack

* **Desktop Framework:** [Tauri 2](https://v2.tauri.app/?utm_source=gemini) (Rust-based native binding & System Tray)

* **Frontend:** React, TypeScript, Tailwind CSS, Vite

* **Backend / Core Engine:** Rust (`tokio` async runtime, monotonic timer, Git CLI reader)

* **Database:** Embedded SQLite via `rusqlite` (WAL mode, FTS5 full-text search)

## Prasyarat Sistem

Sebelum menjalankan atau mengompilasi proyek ini, pastikan sistem Anda telah terpasang:

1. **OS:** Windows 10/11 64-bit (dengan [Microsoft Edge WebView2 Evergreen Runtime](https://developer.microsoft.com/en-us/microsoft-edge/webview2/?utm_source=gemini)).

2. **Node.js:** Versi `20.x` atau lebih baru.

3. **Rust Toolchain:** Versi *stable* terbaru (`rustup toolchain install stable-x86_64-pc-windows-msvc`).

4. **Git CLI:** Terinstal dan terdaftar pada `PATH` sistem (opsional, untuk fitur otomatisasi Git).

5. **C++ Build Tools:** Visual Studio C++ Build Tools dengan Windows SDK.

## Panduan Memulai (Development)

### 1. Kloning Repositori

```
git clone https://github.com/username/developer-workspace.git
cd developer-workspace

```

### 2. Instalasi Dependensi Frontend

```
npm install
# atau
pnpm install

```

### 3. Menjalankan di Lingkungan Lokal (Dev Mode)

Jalankan perintah ini untuk membuka aplikasi dalam mode *hot-reload* (UI React dan Rust process berjalan bersamaan):

```
npm run tauri dev

```

## Pengujian & Linting

Untuk memastikan stabilitas state machine, migrasi SQLite, dan integritas aturan Git:

```
# Uji unit & integrasi Rust (termasuk 38+ skenario Git & Timer)
cargo test

# Linting kode Rust
cargo clippy -- -D warnings

# Pemeriksaan tipe TypeScript
npm run typecheck

# Pengujian unit Frontend
npm test

```

## Membangun Biner & Installer (.exe)

Untuk memproduksi berkas instalasi atau biner portabel:

### 1. Setup Konfigurasi Bundler NSIS (Menghasilkan Setup `.exe`)

Pastikan target `nsis` aktif di `src-tauri/tauri.conf.json`:

```
{
  "bundle": {
    "active": true,
    "targets": ["nsis"],
    "windows": {
      "nsis": {
        "languages": ["English", "Indonesian"],
        "displayLanguageSelector": false
      }
    }
  }
}

```

### 2. Jalankan Build

```
npm run tauri build

```

Hasil kompilasi akan berada di:

* **Installer Setup `.exe`:** `src-tauri/target/release/bundle/nsis/Developer-Workspace_<version>_x64-setup.exe`

* **Portable Standalone `.exe`:** `src-tauri/target/release/developer-workspace.exe` (dapat langsung dijalankan tanpa instalasi).

## Struktur Direktori Utama

```
├── src/                      # UI Layer (React, TypeScript, Tailwind CSS)
│   ├── components/           # Komponen UI (Kanban, Snippets, Wellness, Timer)
│   ├── hooks/                # Custom React Hooks & IPC bridge bindings
│   ├── lib/                  # Utilitas parser Markdown, formatters, state
│   └── main.tsx              # Entry point frontend
├── src-tauri/                # Backend Native Layer (Rust)
│   ├── src/
│   │   ├── commands/         # Tauri IPC commands
│   │   ├── services/         # Git watcher, Timer worker, Notification policy
│   │   ├── db/               # Migrasi SQLite, query FTS5, repository logic
│   │   └── main.rs           # Entry point aplikasi Tauri & event loop
│   ├── Cargo.toml            # Dependensi Rust
│   └── tauri.conf.json       # Konfigurasi jendela, capability, & tray
├── docs/                     # Dokumentasi PRD, User Flow, & Architecture
├── USER.md                   # Panduan Lengkap Penggunaan
└── README.md

```

## Dokumentasi Terkait

* **Panduan Pengguna:** Baca [USER.md](USER.md) untuk panduan pintasan keyboard, aturan transisi Git, dan konfigurasi profil kesehatan.

* **Product Requirements Document:** Lihat [PRD.md](PRD.md) untuk acuan arsitektur data dan matriks penerimaan fitur.

* **User Flows:** Lihat [USER_FLOWS.md](USER_FLOWS.md) untuk rincian alur pemulihan (*recovery*) dan logika interaksi layar.

## Lisensi

Proyek ini didistribusikan di bawah lisensi [MIT](LICENSE).