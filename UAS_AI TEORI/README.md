Alur Pengerjaan Klasifikasi Sampah di Google Colab


Sel 1: Persiapan dan Autentikasi Kaggle

Tujuan: Menghubungkan Google Colab dengan Kaggle untuk mengambil dataset.
Penjelasan: Pada sel ini, kamu mengunggah file kaggle.json (API token dari akun Kaggle-mu), membuat direktori tersembunyi .kaggle, memindahkan file token ke sana, dan memberikan izin akses baca/tulis yang sesuai agar Colab bisa berkomunikasi dengan server Kaggle.

Sel 2: Mengunduh Dataset

Tujuan: Mengambil data gambar sampah.
Penjelasan: Menjalankan perintah CLI Kaggle (menggunakan tanda !) untuk mengunduh dataset Garbage Classification langsung ke ruang penyimpanan (storage) sementaranya Google Colab.

Sel 3: Ekstraksi File Dataset

Tujuan: Membuka file zip dataset.
Penjelasan: Menggunakan library bawaan Python (zipfile) untuk mengekstrak file dataset yang baru saja diunduh dari format .zip ke dalam sebuah folder biasa agar gambar-gambarnya bisa dibaca.

Sel 4: Mengimpor Library (Modul) yang Dibutuhkan

Tujuan: Memuat semua alat kerja ke dalam memori.
Penjelasan: Sel ini berisi daftar import untuk TensorFlow, Keras (terutama untuk ImageDataGenerator, MobileNetV2, dan Layers), Numpy, Matplotlib/Seaborn untuk grafik, dan Scikit-Learn untuk metrik evaluasi.

Sel 5: Mendefinisikan Variabel Global (Hyperparameter)

Tujuan: Mengatur konfigurasi dasar yang akan digunakan di seluruh proyek.
Penjelasan: Di sini kamu menentukan path/lokasi folder dataset, ukuran gambar yang akan diinput (misalnya 224x224 piksel untuk MobileNetV2), dan ukuran batch (jumlah gambar yang diproses mesin dalam satu waktu, biasanya 32).

Sel 6: Menyiapkan Data Augmentation (Data Generator)

Tujuan: Memperbanyak variasi gambar latih dan membagi data.
Penjelasan: Kamu membuat dua objek ImageDataGenerator.

Generator Pertama (Training): Diatur untuk membagi data (misal 80% latih), melakukan pra-pemrosesan khusus MobileNetV2, dan menambahkan efek augmentasi (rotasi, balik arah, geser, dll) agar model tidak gampang overfitting.

Generator Kedua (Validation): Diatur untuk 20% sisa datanya, hanya melakukan pra-pemrosesan tanpa augmentasi.

Sel 7: Mengalirkan Data (Flow From Directory)

Tujuan: Memasukkan gambar dari folder ke dalam generator.
Penjelasan: Menghubungkan generator yang dibuat di Sel 6 dengan folder dataset asli. Generator akan otomatis membaca nama sub-folder (kardus, kaca, plastik, dll) sebagai nama kelas (label) dan mencetak jumlah total gambar yang berhasil ditemukan.

Sel 8: Membangun Arsitektur Model (Transfer Learning)

Tujuan: Menyiapkan otak kecerdasan buatan.
Penjelasan:

Memanggil Base Model (MobileNetV2) dengan bobot dari 'ImageNet' tanpa lapisan akhir (include_top=False).

Membekukan (freeze) pelatihannya agar bobot pintarnya tidak rusak.

Menambahkan lapisan Head buatanmu di atasnya (Pooling, Dropout, Dense Layer, dan Output Layer dengan jumlah neuron sesuai jumlah kelas sampah).

Sel 9: Kompilasi Model dan Callbacks

Tujuan: Mengatur cara model belajar dan aturan berhentinya.
Penjelasan:

Kompilasi: Menetapkan Optimizer (misal Adam), Loss function (Categorical Crossentropy), dan metrik (Akurasi).

Callbacks: Menyiapkan EarlyStopping agar proses latihan otomatis berhenti jika akurasi model sudah tidak berkembang lagi, sehingga menghemat waktu dan mencegah overfitting.

Sel 10: Pelatihan Tahap 1 (Initial Training)

Tujuan: Melatih lapisan Head yang baru ditambahkan.
Penjelasan: Menjalankan perintah .fit(). Model akan belajar selama beberapa epoch (putaran). Karena Base Model dibekukan, pada tahap ini komputer hanya fokus mencari pola klasifikasi menggunakan fitur dasar dari MobileNetV2.

Sel 11: Persiapan Fine-Tuning (Membuka Kuncian Model)

Tujuan: Membuat model lebih spesifik mengenali sampah.
Penjelasan: Membuka kuncian (unfreeze) beberapa lapisan terakhir dari Base Model MobileNetV2. Setelah dibuka, model di-compile ulang tetapi kali ini wajib menggunakan Learning Rate (kecepatan belajar) yang sangat kecil (misal 1e-5) agar perubahan bobotnya sangat perlahan dan hati-hati.

Sel 12: Pelatihan Tahap 2 (Proses Fine-Tuning)

Tujuan: Mempertajam kemampuan model.
Penjelasan: Menjalankan .fit() lagi, melanjutkan dari epoch terakhir di Tahap 1. Model akan menyesuaikan saringan visualnya (filter) agar sangat peka terhadap perbedaan bahan seperti kaca, plastik, atau kertas.

Sel 13: Melakukan Prediksi pada Data Validasi

Tujuan: Menyiapkan data untuk dievaluasi.
Penjelasan: Mereset generator validasi agar urutannya tidak berantakan, lalu menyuruh model menebak (predict) semua gambar di data validasi. Hasil tebakan ini akan dibandingkan dengan kunci jawaban (label aslinya).

Sel 14: Menampilkan Metrik Evaluasi & Confusion Matrix

Tujuan: Melihat performa dan kelemahan model.
Penjelasan:

Mencetak classification_report untuk melihat Akurasi, Precision, Recall, dan F1-Score per kelas sampah.

Menggambar Confusion Matrix (biasanya berupa heatmap kotak-kotak biru) untuk melihat secara detail di jenis sampah mana model sering keliru (misalnya, berapa kali model mengira kaca sebagai plastik).

Sel 15: Menyimpan Model (Export)

Tujuan: Mengamankan hasil kerja.
Penjelasan: Menyimpan model yang sudah jadi ke dalam format file .h5 atau .keras. Dengan menyimpannya, kamu bisa mengunduh model ini dan menggunakannya di aplikasi web (seperti Streamlit), aplikasi mobile, atau server lain tanpa perlu melatih dari awal lagi.