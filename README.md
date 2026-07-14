# Analisis Hubungan Kekeringan (SPI) dan Titik Panas (Hotspot) di Provinsi Jambi

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Research-orange)

## Deskripsi Proyek

Repositori ini memuat kode pemrograman untuk **Skripsi Sarjana** yang menganalisis hubungan statistik antara indeks kekeringan meteorologis (*Standardized Precipitation Index* / SPI) dengan kemunculan titik panas (*Hotspot*) kebakaran hutan dan lahan.

Studi kasus dilakukan di **Provinsi Jambi** dengan rentang waktu **2010-2020**, menggunakan varian **SPI-1, SPI-3, SPI-6, dan SPI-12**. Analisis dilakukan dengan pendekatan spasial (*Spatial Join*, rasterisasi) dan tiga lapis uji statistik: korelasi **Pearson & Spearman** (raw count maupun densitas ternormalisasi) serta uji beda **Kruskal-Wallis**.

## Tujuan Analisis
1.  Mengolah data spasial titik panas (NASA FIRMS) dan poligon kelas kekeringan (SPI).
2.  Menghitung jumlah hotspot per kelas SPI per bulan, baik dalam bentuk **raw count** maupun **densitas** (dinormalisasi terhadap luas area kelas SPI, dengan masking badan air/sungai).
3.  Menguji signifikansi hubungan antara tingkat kekeringan dengan kejadian hotspot menggunakan korelasi Spearman/Pearson serta uji beda Kruskal-Wallis.
4.  Mengekspor data titik hotspot hasil spatial join (lengkap dengan geometri & kelas SPI) ke format GIS standar.

## Metodologi & Alur Kerja

Kode ini dijalankan di lingkungan **Google Colab**. Alur kerja utamanya adalah:

1.  **Preprocessing Data**:
    * Load Shapefile Batas Administrasi Jambi.
    * Load Shapefile Hotspot (NASA FIRMS MODIS-C61) tahunan (2010-2020).
    * Load Shapefile SPI (1, 3, 6, 12 bulanan) per tahun/bulan.
    * Load Shapefile poligon sungai (badan air), digunakan sebagai mask pada perhitungan densitas.
2.  **Filter & Cleaning**:
    * Filter Hotspot dengan tingkat kepercayaan (*Confidence*) ≥ 80%.
    * Filter berdasarkan bulan akuisisi (*acq_date*).
    * *Clipping* data spasial sesuai batas Provinsi Jambi.
    * Perbaikan topologi geometri (`buffer(0)`) dan standarisasi CRS (EPSG:4326).
3.  **Spatial Join & Normalisasi**:
    * Menggabungkan titik hotspot dengan poligon kelas SPI (`sjoin`, level individual — 1 baris = 1 titik hotspot).
    * Menghitung luas tiap kelas SPI per bulan (rasterisasi ke grid EPSG:32748/UTM 48S, resolusi 250 m) dengan piksel sungai dikeluarkan dari penyebut.
    * Menghitung **Densitas Hotspot = Jumlah Hotspot / Luas Area kelas SPI (km²)** sebagai metrik utama yang direkomendasikan (mengoreksi bias "denominator problem" pada raw count).
4.  **Analisis Statistik** (dihitung untuk tiap varian SPI):
    * Korelasi **Pearson & Spearman Rank (ρ)** — Kelas SPI vs Jumlah Hotspot (raw count, baseline).
    * Korelasi **Pearson & Spearman Rank (ρ)** — Kelas SPI vs Densitas Hotspot (hasil utama).
    * Uji beda **Kruskal-Wallis (H)** — distribusi jumlah hotspot antar kelas SPI (analisis pelengkap, level bulan x kelas).
    * Signifikansi ditentukan pada p-value < 0.05.
5.  **Visualisasi**:
    * Scatterplot + garis regresi (raw count & densitas) per varian SPI.
    * Peta choropleth sebaran spasial hotspot per kelas SPI.
    * Boxplot + stripplot distribusi hotspot antar kelas (Kruskal-Wallis).
    * Grafik batang (*Bar Chart*) rekap nilai korelasi Spearman antar varian SPI (versi raw count & versi densitas).
6.  **Export Data Spasial**:
    * Titik hotspot hasil spatial join (lengkap geometri, kelas SPI, koordinat) diekspor ke Shapefile, GeoJSON, dan GeoPackage per varian SPI, serta GeoPackage gabungan 4 layer.

## Struktur Direktori Data (Google Drive)

Agar script dapat berjalan tanpa error, struktur folder di Google Drive harus disusun sebagai berikut:

```text
/My Drive/Colab Notebooks - <email_akun>/Skripsi/
├── Batas Adm Jambi/
│   └── Adm_Jambi_Prov.shp
├── NASA-FIRMS Fire (New2)/
│   ├── DL_FIRE_M-C61_587963_2010/
│   ├── DL_FIRE_M-C61_587964_2011/
│   └── ... (dst sampai 2020)
├── SHP Sungai Indonesia/
│   └── Sungai Poligon 50K (129).shp
├── SPI1-Output/
│   └── Polygon_Class/
│       └── 2010/ ...
├── SPI3-Output/
│   └── Polygon_SPI3_2009-2011/ ...
├── SPI6-Output/
│   └── Polygon_SPI6_2009-2011/ ...
└── SPI12-Output/
    └── Polygon_SPI12_2009-2011/ ...
```

> Catatan: `BASE_PATH` pada script mengikuti pola nama folder Colab yang menyertakan alamat email akun Google (`Colab Notebooks - <email>/Skripsi`). Sesuaikan variabel `BASE_PATH` di bagian konfigurasi jika struktur Drive Anda berbeda.

## Prasyarat Instalasi

Script membutuhkan pustaka Python berikut:

```bash
pip install geopandas pandas matplotlib seaborn scipy numpy rasterio openpyxl
```

Script dijalankan di Google Colab dan memanfaatkan modul `google.colab.drive` untuk mount Google Drive.

## Hasil Analisis

Script akan menghasilkan output berupa:

1. **Data per titik hotspot** (level individual): `Hasil_Hotspot_Individual_SPI{1,3,6,12}_2010-2020.csv/.xlsx`.
2. **Data agregat bulanan** (kelas SPI x bulan, lengkap luas area & densitas): `Hasil_Hotspot_Bulanan_SPI{1,3,6,12}_2010-2020.csv/.xlsx`.
3. **Rekap korelasi Spearman/Pearson** untuk seluruh varian SPI:
   * `Rekap_Korelasi_Utama_RawCount_All_SPI_Final.csv` (baseline, belum dinormalisasi).
   * `Rekap_Korelasi_Utama_Densitas_All_SPI_Final.csv` (hasil utama, ternormalisasi).
   * `Rekap_Korelasi_Utama_All_SPI_Final.xlsx` — rekap multi-sheet (raw, densitas, Kruskal-Wallis, data bulanan & individual tiap SPI).
4. **Rekap uji Kruskal-Wallis**: `Rekap_KruskalWallis_All_SPI_Final.csv`.
5. **Data spasial hasil spatial join** (folder `Export_Hotspot_SPI/`): Shapefile, GeoJSON, dan GeoPackage per varian SPI, serta GeoPackage gabungan `Hotspot_AllSPI_Jambi_2010-2020.gpkg` (4 layer: SPI1, SPI3, SPI6, SPI12).
6. **Grafik Statistik**: scatterplot regresi, peta sebaran spasial, boxplot Kruskal-Wallis, dan bar chart rekap korelasi (raw count vs densitas) untuk SPI-1, SPI-3, SPI-6, dan SPI-12.

Contoh interpretasi hasil:

> Jika nilai *p-value* < 0.05 pada uji korelasi densitas hotspot SPI-3, maka terdapat hubungan yang signifikan antara kekeringan triwulanan dengan kejadian kebakaran lahan di Jambi, setelah memperhitungkan perbedaan luas area tiap kelas SPI.

## Catatan Metodologis

* Metrik **densitas hotspot** (bukan raw count) adalah hasil yang direkomendasikan dalam analisis ini, karena raw count berpotensi bias akibat "denominator problem": kelas SPI yang kebetulan mencakup area lebih luas pada suatu bulan otomatis memiliki peluang lebih besar memunculkan hotspot, terlepas dari kekuatan hubungan kekeringan-kebakaran yang sebenarnya.
* Penyebut densitas dihitung dari seluruh piksel wilayah kajian dikurangi piksel yang teridentifikasi sebagai badan air/sungai. Masking tutupan lahan lain (misalnya permukiman) belum diterapkan dan menjadi salah satu arah pengembangan lanjutan.
* Uji Kruskal-Wallis digunakan sebagai analisis pelengkap untuk menguji perbedaan (bukan korelasi) jumlah hotspot antar kelas SPI pada level agregat bulan x kelas.

## Penulis

**Jariyan Arifudin** Mahasiswa Geografi Lingkungan

Universitas Gadjah Mada (UGM)

## Lisensi & Sitasi

Kode ini didistribusikan di bawah **MIT License**.
Jika Anda menggunakan metode ini untuk penelitian, silakan sitasi repositori ini:

> Arifudin, J. (2026). *Analisis Hubungan Kekeringan (SPI) dan Titik Panas (Hotspot) di Provinsi Jambi*. GitHub Repository.
