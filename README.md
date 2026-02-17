# Analisis Hubungan Kekeringan (SPI) dan Titik Panas (Hotspot) di Provinsi Jambi

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Research-orange)

## Deskripsi Proyek

Repositori ini memuat kode pemrograman untuk **Skripsi Sarjana** yang menganalisis hubungan statistik antara indeks kekeringan meteorologis (*Standardized Precipitation Index* / SPI) dengan kemunculan titik panas (*Hotspot*) kebakaran hutan dan lahan.

Studi kasus dilakukan di **Provinsi Jambi** dengan rentang waktu **2010-2020**. Analisis dilakukan menggunakan pendekatan spasial (*Spatial Join*) dan uji korelasi statistik (*Pearson & Spearman*).

## Tujuan Analisis
1.  Mengolah data spasial titik panas (NASA FIRMS) dan poligon kekeringan (SPI).
2.  Menghitung frekuensi hotspot yang muncul pada setiap kelas kekeringan.
3.  Menguji signifikansi hubungan antara tingkat kekeringan dengan jumlah hotspot menggunakan uji korelasi.

## Metodologi & Alur Kerja

Kode ini dijalankan di lingkungan **Google Colab**. Alur kerja utamanya adalah:

1.  **Preprocessing Data**:
    * Load Shapefile Batas Administrasi Jambi.
    * Load Shapefile Hotspot (NASA FIRMS MODIS) tahunan.
    * Load Shapefile SPI (1, 3, 6, 12 bulanan).
2.  **Filter & Cleaning**:
    * Filter Hotspot dengan tingkat kepercayaan (*Confidence*) ≥ 80%.
    * *Clipping* data spasial sesuai batas Provinsi Jambi.
3.  **Spatial Join**:
    * Menggabungkan data titik hotspot dengan poligon kelas SPI.
    * Menghitung jumlah hotspot per kelas SPI per bulan.
4.  **Analisis Statistik**:
    * Menghitung Koefisien Korelasi **Spearman Rank ($\rho$)**.
    * Menentukan signifikansi hubungan (p-value < 0.05).
5.  **Visualisasi**:
    * Membuat grafik batang (*Bar Chart*) nilai korelasi antar varian SPI.

## Struktur Direktori Data (Google Drive)

Agar script dapat berjalan tanpa error, struktur folder di Google Drive harus disusun sebagai berikut:

```text
/My Drive/Colab Notebooks/Skripsi/
├── Batas Adm Jambi/
│   └── Adm_Jambi_Prov.shp
├── NASA-FIRMS Fire (New2)/
│   ├── DL_FIRE_M-C61_587963_2010/
│   ├── DL_FIRE_M-C61_587964_2011/
│   └── ... (dst sampai 2020)
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

## Prasyarat Instalasi

Script membutuhkan pustaka Python berikut:

```bash
pip install geopandas pandas matplotlib seaborn scipy numpy

```

## Hasil Analisis

Script akan menghasilkan output berupa:

1. **File CSV**: Rekapitulasi jumlah hotspot per kelas SPI dan nilai korelasi.
2. **Grafik Statistik**: Plot nilai korelasi Spearman untuk SPI-1, SPI-3, SPI-6, dan SPI-12.

Contoh interpretasi hasil:

> Jika nilai *p-value* < 0.05 pada SPI-3, maka terdapat hubungan yang signifikan antara kekeringan triwulanan dengan kejadian kebakaran lahan di Jambi.

## Penulis

**Jariyan Arifudin** Mahasiswa Geografi Lingkungan

Universitas Gadjah Mada (UGM)

## Lisensi & Sitasi

Kode ini didistribusikan di bawah **MIT License**.
Jika Anda menggunakan metode ini untuk penelitian, silakan sitasi repositori ini:

> Arifudin, J. (2026). *Analisis Hubungan Kekeringan (SPI) dan Titik Panas (Hotspot) di Provinsi Jambi*. GitHub Repository.
