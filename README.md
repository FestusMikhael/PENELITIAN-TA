# Modifikasi Arsitektur YOLOv8n dengan Backbone MobileNetV3-Small untuk Mendeteksi Kerusakan Jalan Raya

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/)
[![Ultralytics YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-orange.svg)](https://github.com/ultralytics/ultralytics)
[![Dataset RDD2022](https://img.shields.io/badge/Dataset-RDD2022%20Japan-green.svg)](https://crwrd.github.io/rdd2022/)

Repositori ini memuat kode sumber, fail konfigurasi arsitektur hibrida, serta dokumentasi eksperimen laboratorium untuk modifikasi model deteksi objek **YOLOv8 Nano (YOLOv8n)**. Penelitian ini berfokus pada substitusi komponen *backbone* bawaan (*CSPDarknet*) menggunakan **MobileNetV3-Small** berbasis mekanisme atensi *Squeeze-and-Excitation* (SE) guna menekan beban komputasi struktural model untuk kebutuhan penugasan berskala *real-time deployment*.

---

## 🚀 Akses Notebook Eksperimen (Google Colab)

Seluruh log proses *training*, grafik konvergensi, prapemrosesan data, hingga tahap evaluasi akhir diuji menggunakan ekosistem Google Colab. Silakan klik lencana resmi di bawah ini untuk meninjau secara interaktif:

| Komponen Eksperimen | Tautan Akses Langsung |
| :--- | :--- |
| **1. Tahap Persiapan & Prapemrosesan Data** <br> (Konversi koordinat spasial YOLO & pembersihan kelas) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1vEwzo-sfNWgtBLKzLFJpXwUYl_9sfEKk?usp=sharing) |
| **2. Tahap Rekayasa Arsitektur & Eksperimen Model** <br> (Registrasi modul kustom, *training* M1-M5, & evaluasi) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1D8adwCVTBY8uccb5Gp4in4iza6MetLLV?usp=sharing) |

---

## 🛠️ Ikhtisar Modifikasi Arsitektur

Modifikasi ini mengintegrasikan struktur ringkas milik *MobileNetV3-Small* ke dalam skema piramida fitur YOLOv8 melalui tiga teknik rekayasa utama:
1. **Layer Adapter Registration (`Select_Explicit_Fix`):** Menjembatani perbedaan keluaran format *Multi-Stride List of Tensor* dari pustaka `timm` agar selaras dengan kebutuhan *Single Tensor input parser* internal Ultralytics. Modul ini diinjeksikan melalui mekanisme *code overriding* pada kelas `GhostConv`.
2. **Depthwise Separable Convolution:** Memisahkan filter spasial dari korelasi antar-saluran (*channel*) untuk memangkas jumlah kalkulasi perkalian matriks secara drastis.
3. **Squeeze-and-Excitation (SE) Attention:** Mengoptimalkan kapasitas ekstraksi fitur lokal dengan memberikan bobot perhatian dinamis pada area kerusakan jalan yang relevan dan mereduksi noise visual dari latar belakang aspal normal.

---

## 📊 Karakteristik Dataset

Penelitian ini mengevaluasi performa model menggunakan sub-korpus negara **Jepang** pada repositori **Road Damage Detection 2022 (RDD2022)** dengan total **9.311 lembar citra bersih** (termasuk 1.411 *background images* untuk mereduksi *false positive rate*). Objek terbagi ke dalam 4 kelas kerusakan jalan utama:
* `D00` : *Alligator Crack* (Retak Buaya)
* `D10` : *Longitudinal Crack* (Retak Membujur)
* `D20` : *Transverse Crack* (Retak Melintang)
* `D40` : *Pothole* (Lubang)

---
