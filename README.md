# Panduan Kustomisasi Touchpad Gestures (InputActions) - Kubuntu Wayland

File ini berisi dokumentasi kustomisasi gesture touchpad menggunakan [InputActions](https://github.com/taj-ny/InputActions) pada sesi KDE Plasma Wayland di Kubuntu. Konfigurasi ini dirancang agar memiliki perilaku navigasi yang fluid menyerupai sistem operasi Windows, lengkap dengan penanganan volume kustom.

---
## Cara Pakai Config

1. Clone repo ke local
2. Pindah `config.yaml` ke ~/.config/inputactions
3. Run `inputactions config reload`
---

## 📌 Rangkuman Aturan Sintaksis (Cheat Sheet)

Jika ingin menambahkan perintah baru atau menerjemahkan kombinasi tombol keyboard ke dalam kode InputActions, ikuti aturan dasar berikut:

### 1. Struktur Blok Dasar
Untuk pemetaan pintasan keyboard biasa (bukan fungsi internal KDE), gunakan kombinasi parameter `input` dan `keyboard`:
```yaml
actions:
  - input:
      - keyboard: [ tombol_di_sini ]
```

### 2. Aturan Tanda Pengubah Tombol (`+`, `-`, dan Tanpa Tanda)
* **Tanpa Tanda (Contoh: `ctrl+f3`):** Berarti **Tap** (sekali ketuk). Tombol akan ditekan dan langsung dilepas pada saat itu juga. Cocok untuk aksi instan seperti Volume Up/Down, Brightness, atau pintasan aplikasi tunggal.
* **Tanda `+` (Contoh: `+leftalt`):** Berarti **Key Down** (menahan tombol). Tombol akan ditekan dan dikunci dalam kondisi tertahan sampai ada perintah pelepasan. Sangat berguna untuk skenario navigasi jendela seperti Alt+Tab.
* **Tanda `-` (Contoh: `-leftalt`):** Berarti **Key Up** (melepaskan tombol). Digunakan untuk melepas kembali tombol yang sebelumnya dikunci oleh tanda `+`.

### 3. Kombinasi Multi-Tombol
Jika aksi membutuhkan penekanan beberapa tombol secara bersamaan dalam satu kali ketuk, gabungkan tombol-tombol tersebut menggunakan tanda `+` di dalam satu string di dalam kurung siku:
* `[ ctrl+f3 ]`
* `[ ctrl+alt+t ]`
* `[ shift+delete ]`

---

## 🛠️ Konfigurasi Lengkap (`~/.config/inputactions/config.yaml`)

Berikut adalah file konfigurasi final yang menggabungkan navigasi Alt+Tab fluid ala Windows, kendali Virtual Desktop, serta pintasan kontrol volume suara:

```yaml
touchpad:
  gestures:
    # -----------------------------------------------------------------
    # 3 FINGERS GESTURES (Windows-like Alt+Tab & Volume Management)
    # -----------------------------------------------------------------
    # Swipe Kiri/Kanan (Dua Arah): Membuka menu Alt+Tab dan memilih jendela dengan stabil
    - type: swipe
      fingers: 3
      direction: left_right
      actions:
        # 1. Saat AWAL usapan: Tahan Alt dan ketuk Tab sekali untuk memicu menu muncul
        - on: begin
          input:
            - keyboard: [ +leftalt, tab ]

        # 2. Saat digerakkan ke KIRI: Gunakan tombol arah kiri (Mundur)
        - on: update
          interval: -60  # Jarak piksel responsivitas usapan
          input:
            - keyboard: [ left ]

        # 3. Saat digerakkan ke KANAN: Gunakan tombol arah kanan (Maju)
        - on: update
          interval: 60
          input:
            - keyboard: [ right ]

        # 4. Saat Jari DIANGKAT: Lepaskan tombol Alt sepenuhnya untuk mengonfirmasi pilihan window
        - on: end_cancel
          input:
            - keyboard: [ -leftalt ]

    # Swipe Bawah: Collapse semua window (Minimize All / Show Desktop)
    - type: swipe
      fingers: 3
      direction: down
      actions:
        - plasma_shortcut: "kwin,Show Desktop"

    # Swipe Atas: Kontrol Volume Up via Pintasan Keyboard (Ctrl + F3)
    - type: swipe
      fingers: 3
      direction: up
      actions:
        - input:
            - keyboard: [ ctrl+f3 ]

    # -----------------------------------------------------------------
    # 4 FINGERS GESTURES (Virtual Desktop Switching)
    # -----------------------------------------------------------------
    # Swipe Kiri: Berpindah ke Virtual Desktop Sebelumnya
    - type: swipe
      fingers: 4
      direction: left
      actions:
        - plasma_shortcut: "kwin,Switch to Previous Desktop"

    # Swipe Kanan: Berpindah ke Virtual Desktop Berikutnya
    - type: swipe
      fingers: 4
      direction: right
      actions:
        - plasma_shortcut: "kwin,Switch to Next Desktop"
```

---

## 🚀 Cara Menerapkan

1. **Simpan File Konfigurasi:**
   `~/.config/inputactions/config.yaml`

2. **Reload Daemon:**
   ```bash
   inputactions config reload
   ```
