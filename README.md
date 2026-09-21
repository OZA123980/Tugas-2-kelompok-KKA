# Tugas-2-kelompok-KKA

# Tugas 2 Kelompok — Analisis Data Penjualan Kantin/Toko

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/OZA123980/Tugas-2-kelompok-KKA/blob/main/TugasKelompok2.ipynb)

Analisis data penjualan kantin menggunakan Python (pandas) — mulai dari loading & inspection, cleaning, manipulasi data, hingga menjawab pertanyaan analisis awal.

## Anggota Kelompok

1. Moh. Fauza Akbar — Loading, Inspection & Cleaning
2. Mishbahul Khofid — Manipulation, Analisis & Presentasi

## Dataset

**Data Penjualan Kantin/Toko** — `data_kantin - data_kantin.csv`

Kolom:

| Kolom | Tipe Awal | Keterangan |
|---|---|---|
| `tanggal` | object | Tanggal transaksi, diubah ke `datetime` |
| `menu` | object | Nama menu |
| `kategori` | object | `Makanan` / `Minuman` / `Snack` |
| `harga` | int64 | Harga satuan |
| `terjual` | float64 | Jumlah terjual per transaksi |

Ukuran awal: **110 baris × 5 kolom**.

Contoh data:

```
tanggal     menu         kategori  harga  terjual
2026-08-20  Teh Hangat   Minuman   3000   11.0
2026-08-31  Nasi Goreng  Makanan   12000  29.0
```

## Pertanyaan Analisis Awal

1. Menu apa yang memiliki jumlah penjualan paling banyak?
2. Kategori produk mana yang menghasilkan pendapatan terbesar?
3. Produk apa yang memiliki pendapatan penjualan tertinggi?

## Hasil Analisis (Dataset Bersih)

> Setelah cleaning: **105 baris × 6 kolom** (kolom turunan `pendapatan` ditambahkan).

### 1. Menu Paling Banyak Terjual: Es Teh (556 porsi)

| Menu | Total Terjual | Total Pendapatan (Rp) |
|---|---|---|
| Es Teh | 556.0 | 2.224.000 |
| Kerupuk | 506.0 | 1.012.000 |
| Nasi Goreng | 347.0 | 4.164.000 |
| Es Jeruk | 312.0 | 1.560.000 |
| Mie Ayam | 261.0 | 2.610.000 |
| Teh Hangat | 173.0 | 519.000 |
| Roti Bakar | 161.0 | 1.288.000 |
| Tidak Diketahui | 85.0 | 593.000 |

### 2. Kategori dengan Pendapatan Terbesar: Makanan (Rp 8.458.000)

| Kategori | Total Terjual | Total Pendapatan (Rp) |
|---|---|---|
| Makanan | 802.0 | 8.458.000 |
| Minuman | 1.093.0 | 4.500.000 |
| Snack | 506.0 | 1.012.000 |

Catatan menarik: Minuman terjual paling banyak secara volume (1.093), tetapi Makanan menghasilkan pendapatan terbesar karena harga satuan lebih tinggi.

### 3. Produk dengan Pendapatan Tertinggi: Nasi Goreng (Rp 4.164.000)

Walaupun volume penjualannya peringkat ke-3 (347), harga Rp 12.000 membuat Nasi Goreng menjadi penyumbang pendapatan terbesar.

## Alur Kerja (Sesuai Notebook `TugasKelompok2.ipynb`)

### P3 — Loading & Inspection

```python
import pandas as pd
df = pd.read_csv("data_kantin - data_kantin.csv")
df.head()
df.info()
df.describe()
print(df.shape)
print(df.isnull().sum())
print(df.duplicated().sum())
```

Temuan awal:

- Missing value: `menu` = 3, `terjual` = 6
- Duplikat: 5 baris
- `tanggal` masih `object`, perlu ke `datetime`

### P4 — Cleaning

```python
# Tipe data
df["tanggal"] = pd.to_datetime(df["tanggal"])
df["harga"] = pd.to_numeric(df["harga"], errors="coerce")
df["terjual"] = pd.to_numeric(df["terjual"], errors="coerce")

# Missing value
df["menu"] = df["menu"].fillna("Tidak Diketahui")
df["terjual"] = df["terjual"].fillna(df["terjual"].median())

# Duplikat
df = df.drop_duplicates()
```

Hasil: missing 0, duplikat 0, shape `(105, 5)`.

### P5 — Manipulation

```python
# Filter
df_filter = df[df["terjual"] > 20]

# Sort
df_sort = df.sort_values(by="terjual", ascending=False)

# Kolom turunan
df["pendapatan"] = df["harga"] * df["terjual"]

# Agregasi per menu & kategori
penjualan_menu = df.groupby("menu").agg(
    total_terjual=("terjual", "sum"),
    total_pendapatan=("pendapatan", "sum")
).sort_values("total_terjual", ascending=False)

penjualan_kategori = df.groupby("kategori").agg(
    total_terjual=("terjual", "sum"),
    total_pendapatan=("pendapatan", "sum")
).sort_values("total_pendapatan", ascending=False)
```

### P6 — Uji & Presentasi

```python
menu_terlaris = penjualan_menu["total_terjual"].idxmax()       # Es Teh
kategori_tertinggi = penjualan_kategori["total_pendapatan"].idxmax()  # Makanan
produk_pendapatan_tertinggi = penjualan_menu["total_pendapatan"].idxmax()  # Nasi Goreng

df.to_csv("dataset_bersih.csv", index=False)
```

## Struktur Repository

```
.
├── TugasKelompok2.ipynb              # Notebook utama (P3–P6)
├── data_kantin - data_kantin.csv     # Dataset mentah
├── dataset_bersih.csv                # Dataset hasil cleaning + kolom pendapatan
└── README.md
```

## Cara Menjalankan

1. Buka notebook di Colab via badge di atas, atau lokal:
   ```bash
   pip install pandas
   jupyter notebook TugasKelompok2.ipynb
   ```
2. Upload / pastikan `data_kantin - data_kantin.csv` sejajar dengan notebook.
3. Run All — output `dataset_bersih.csv` akan terbuat otomatis.

## Jadwal Kerja

| Tahap | Fokus |
|---|---|
| P3 | Loading & Inspection: memuat dan memeriksa dataset |
| P4 | Cleaning: missing value, duplikat, tipe data |
| P5 | Manipulation: filter, sorting, kolom turunan, groupby |
| P6 | Uji & Presentasi: menguji hasil dan menyiapkan presentasi |
