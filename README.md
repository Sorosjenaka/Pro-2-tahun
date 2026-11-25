# Google AI Pro 2 Tahun Gratis — Script Otomatis untuk MuMu Player

Script ini mengotomatiskan proses klaim **Google AI Pro (5 TB) 12 bulan gratis** + **stacking 3x6 bulan** = **total 24 bulan (2 tahun) gratis** via Google Play Billing di emulator MuMu Player.

## 🎯 Apa yang Didapat

| Step | Durasi | Total | Catatan |
|------|--------|-------|---------|
| Step 1 | 12 bulan | 12 bulan | Free trial pertama |
| Step 2 | +6 bulan | 18 bulan | Stack via subscription upgrade |
| Step 3 | +6 bulan | 24 bulan | Stack via subscription upgrade |
| Backup | +6 bulan | 30 bulan | Maksimal (opsional) |

**Harga normal:** Rp 309.000/bulan → **Total hemat: ~Rp 7,4 juta**

---

## 📋 Prasyarat (WAJIB)

1. **MuMu Player 12** sudah terinstall dan **running**
2. **ADB Debugging ON** di Settings MuMu
3. **ROOT ON** di Settings MuMu (restart setelah enable)
4. **Google Account** sudah login di MuMu
5. **Payment Method (Kartu Kredit/Debit)** sudah terikat di Play Store
   - Script butuh kartu untuk verifikasi $0 trial
   - Pakai kartu limit rendah / virtual card untuk aman
6. **Tidak ada subscription Google One aktif** di account tersebut
7. **Python 3.10+** di Windows (sudah di PATH)

---

## 🚀 Quick Start (Copy-Paste Jalanin Satu per Satu)

### 1. Clone & Setup Environment (Hanya Pertama Kali)

```powershell
# Buka PowerShell di folder project
cd C:\path\to\Pro-2-tahun

# Jalankan setup otomatis (install Python packages, download adb, connect MuMu, self-check)
.\一键安装环境.bat
```

**Tunggu sampai keluar:** `✅ 环境就绪，可以双击「2_Pro_12个月_第1步.bat」开始下单`

> **Note:** Jika error encoding, jalankan manual:
> ```powershell
> $env:PYTHONIOENCODING="utf-8"; python setup_env.py
> ```

---

### 2. Fix Play Store Authentication (KUNCI SUKSES)

**SEBELUM jalanin step 1, WAJIB jalankan ini dulu:**

```powershell
# 1. Restart emulator biar fresh session
.\platform-tools\adb.exe -s 127.0.0.1:16384 reboot

# Tunggu 30-60 detik sampai MuMu reboot selesai, lalu:
# 2. Force start Play Store
.\platform-tools\adb.exe -s 127.0.0.1:16384 shell "am start -n com.android.vending/com.android.vending.AssetBrowserActivity"

# 3. Buka Google One app buat refresh token account (tunggu 10-15 detik)
.\platform-tools\adb.exe -s 127.0.0.1:16384 shell "am start -n com.google.android.apps.subscriptions.red/com.google.android.apps.subscriptions.red.LauncherActivity"

# 4. Verifikasi Play Store bisa dibuka normal (gak "Authentication required")
.\platform-tools\adb.exe -s 127.0.0.1:16384 shell "am start -a android.intent.action.VIEW -d 'https://play.google.com/store/account/subscriptions'"
```

---

### 3. Step 1: Klaim 12 Bulan Gratis

```powershell
$env:ADB_PATH=".\platform-tools\adb.exe"; $env:PYTHONIOENCODING="utf-8"; python ultra0.py --account "current@device" --no-add --force --plan pro12
```

**Output sukses:**
```
✅ 已拉起收银台：{'ev': 'launched', 'code': 0, 'msg': ''}
...
▶ 核对订阅状态
   ✅ 订阅页已显示 Pro/Ultra
```

**Verifikasi di Play Store:** `Next payment: Nov 25, 2027` (12 bulan ke depan)

---

### 4. Step 2: Tambah 6 Bulan (Total 18 Bulan)

```powershell
$env:ADB_PATH=".\platform-tools\adb.exe"; $env:PYTHONIOENCODING="utf-8"; python ultra0.py --account "current@device" --no-add --force --stack --plan pro6
```

**Verifikasi:** `Next payment` mundur 6 bulan ke depan

---

### 5. Step 3: Tambah 6 Bulan (Total 24 Bulan / 2 Tahun)

```powershell
$env:ADB_PATH=".\platform-tools\adb.exe"; $env:PYTHONIOENCODING="utf-8"; python ultra0.py --account "current@device" --no-add --force --stack --plan pro6b
```

**Verifikasi:** `Next payment` mundur 6 bulan lagi (total 24 bulan dari sekarang)

---

### 6. (Opsional) Backup: Tambah 6 Bulan (Total 30 Bulan / Max)

```powershell
$env:ADB_PATH=".\platform-tools\adb.exe"; $env:PYTHONIOENCODING="utf-8"; python ultra0.py --account "current@device" --no-add --force --stack --plan pro6c
```

---

### 7. SETELAH SELESAI: Cancel Subscription (WAJIB!)

> **PENTING:** Jangan lupa cancel auto-renewal sebelum trial berakhir, kena charge Rp 309.000/bulan!

1. Buka Play Store → Profile → Payments & Subscriptions → Subscriptions
2. Pilih **Google One** / **Google AI Pro**
3. Klik **Cancel subscription** → Konfirmasi
4. **Benefit tetap aktif sampai tanggal expired**, cuma gak auto-renew

---

## 🔧 Parameter Script Explained

| Parameter | Fungsi |
|-----------|--------|
| `--account "current@device"` | Pakai account yg sudah login di device |
| `--no-add` | Skip tambah account (sudah login) |
| `--force` | Skip verifikasi Play account switch |
| `--plan pro12` | Target Pro 12 bulan free trial |
| `--plan pro6` | Target Pro 6 bulan free trial (stack 1) |
| `--plan pro6b` | Target Pro 6 bulan free trial (stack 2) |
| `--plan pro6c` | Target Pro 6 bulan free trial (stack 3/backup) |
| `--stack` | Mode subscription upgrade (penting buat stack!) |
| `--list` | Cek offer yang tersedia saja, gak down order |

---

## ❌ Troubleshooting Umum

### "Something wrong try again" / "Authentication required"
→ Play Store session expired. **Jalankan ulang Step 2 (Fix Authentication)** di atas.

### "没有检测到收银台" (Billing sheet gak kebuka)
1. Pastikan Play Store process running: `adb shell pidof com.android.vending`
2. Pastikan Google One app bisa kebuka normal
3. Restart emulator + ulang Step 2

### "您不符合免费试用条件" (Not eligible)
- Account udah pernah trial → **Gak bisa dipakai lagi**
- Pakai account baru yang belum pernah subscribe Google One

### "Invalid signature" / Web checkout error
- Hanya bisa lewat Android Billing (script ini), **bukan web**

### Subscription gak nambah tanggal (tetap hari ini)
- Pastikan subscription status **"Active" (auto-renew ON)**
- Jika "Cancelled" / "Resubscribe" → gak bisa stack
- Harus: Active → Stack → Baru cancel setelah step 3

### "Permission denied: '\\ current.json'"
- Bug minor path di script, gak ngaruh ke hasil
- Hasil tetap tersimpan di memori, cek Play Store manual

---

## 📁 Struktur File

```
Pro-2-tahun/
├── ultra0.py              # Script utama (sudah di-fix detection Pro)
├── setup_env.py           # Auto setup environment
├── 一键安装环境.bat       # Launcher setup (klik 2x)
├── 0_环境自检.bat         # Self-check only
├── 1_查看offer列表.bat    # Cek offer available
├── 2_Pro_12个月_第1步.bat  # Step 1: 12 bulan
├── 3_Pro_加6个月_第2步.bat  # Step 2: +6 bulan
├── 4_Pro_加6个月_第3步.bat  # Step 3: +6 bulan
├── 5_Pro_备用6个月.bat     # Backup: +6 bulan
├── requirements.txt       # frida>=16.0
├── query_sku.py           # Query SKU manual
├── README.md              # File ini
└── .gitignore
```

---

## ⚠️ Disclaimer

- **Gunakan account secondary/burner**, jangan account utama
- Script hanya memanfaatkan **client-side offer** yang dikembalikan Play BillingClient
- Tidak modifikasi server-side data
- **Cancel subscription sebelum expired** untuk hindari charge
- Use at your own risk

---

## 🙏 Credit

- Original: [crhywun/g1-acq](https://github.com/crhywun/g1-acq)
- Extended: [Dgkncn/g1-acq-plus](https://github.com/Dgkncn/g1-acq-plus) (Pro 18M/15M/12M SKU discovery)
- Script base: `google-ultra` / `ultra0.py` oleh komunitas LinuxDo

---

## 📞 Butuh Bantuan?

- Cek `ultra0-report/` untuk log detail
- Pastikan semua checklist prasyarat terpenuhi
- Error "Something wrong" = 99% Play Store butuh re-auth (Step 2)

**Selamat menikmati 2 Tahun Google AI Pro 5TB Gratis! 🎉**