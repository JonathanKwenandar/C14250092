Baik Jonathan, kita pecah dulu jadi dua bagian:  
1. **Memahami metode IQR untuk deteksi & penghilangan outlier**  
2. **Eksperimen langsung pada dataset `TGM 2020-2023_eng.csv`** dengan histogram sebelum & sesudah.

---

## 1️⃣ Ringkasan Cara Kerja Metode IQR
**IQR (Interquartile Range)** adalah rentang antara kuartil ke-3 (Q3) dan kuartil ke-1 (Q1) dari data.  
Langkahnya:

1. **Urutkan data** dari kecil ke besar.
2. **Hitung Q1** (persentil 25%) dan **Q3** (persentil 75%).
3. **Hitung IQR**:
   \[
   IQR = Q3 - Q1
   \]
4. Tentukan **batas bawah** dan **batas atas**:
   \[
   \text{Lower Bound} = Q1 - 1.5 \times IQR
   \]
   \[
   \text{Upper Bound} = Q3 + 1.5 \times IQR
   \]
5. **Outlier** adalah data yang berada di luar batas ini.
6. **Filter** data agar hanya yang berada di dalam rentang tersebut yang dipertahankan.

💡 Catatan: Faktor 1.5 adalah default, tapi bisa dimodifikasi (misalnya 3.0 untuk deteksi outlier ekstrem).

---

## 2️⃣ Eksperimen pada Dataset
Berikut contoh kode Python untuk:
- Membaca data
- Menampilkan histogram sebelum & sesudah
- Menghapus outlier dengan metode IQR
- Memodifikasi agar bisa menghapus outlier di **lebih dari satu kolom numerik**

```python
import pandas as pd
import matplotlib.pyplot as plt

# 1. Baca data
df = pd.read_csv("/content/TGM 2020-2023_eng.csv")

# 2. Pilih kolom numerik yang ingin dicek outlier
# Misalnya: 'Reading_Score', 'Hours_Reading', 'Books_Read'
kolom_numerik = ['Reading_Score', 'Hours_Reading', 'Books_Read']

# 3. Fungsi untuk menghapus outlier dengan IQR
def remove_outliers_iqr(data, columns, factor=1.5):
    df_clean = data.copy()
    for col in columns:
        Q1 = df_clean[col].quantile(0.25)
        Q3 = df_clean[col].quantile(0.75)
        IQR = Q3 - Q1
        lower_bound = Q1 - factor * IQR
        upper_bound = Q3 + factor * IQR
        df_clean = df_clean[(df_clean[col] >= lower_bound) & (df_clean[col] <= upper_bound)]
    return df_clean

# 4. Histogram sebelum
plt.figure(figsize=(12, 4))
for i, col in enumerate(kolom_numerik):
    plt.subplot(1, len(kolom_numerik), i+1)
    df[col].hist(bins=20, color='skyblue', edgecolor='black')
    plt.title(f"Sebelum - {col}")
plt.tight_layout()
plt.show()

# 5. Hapus outlier
df_clean = remove_outliers_iqr(df, kolom_numerik, factor=1.5)

# 6. Histogram sesudah
plt.figure(figsize=(12, 4))
for i, col in enumerate(kolom_numerik):
    plt.subplot(1, len(kolom_numerik), i+1)
    df_clean[col].hist(bins=20, color='lightgreen', edgecolor='black')
    plt.title(f"Sesudah - {col}")
plt.tight_layout()
plt.show()

# 7. Info jumlah data
print(f"Jumlah data awal: {len(df)}")
print(f"Jumlah data setelah outlier dihapus: {len(df_clean)}")
```
