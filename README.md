Tentu, mari kita bedah tugas akhir semester ini langkah demi langkah agar menjadi lebih jelas. Wajar jika Anda merasa bingung di awal, karena tugas ini menggabungkan dua konsep yang cukup dalam: pengolahan citra (Otsu Thresholding) dan optimasi (Genetic Algorithm).

Tugas ini pada dasarnya meminta Anda untuk **tidak menggunakan metode Otsu secara langsung**, melainkan menggunakan **Genetic Algorithm (GA) untuk *menemukan* nilai ambang (threshold) yang optimal, di mana "optimal" itu sendiri didefinisikan oleh kriteria Otsu.**

Mari kita pecah menjadi beberapa bagian:

### 1\. Tujuan Utama Tugas Anda (The Big Picture)

Bayangkan Anda ingin memisahkan objek dari latar belakang pada sebuah gambar grayscale. Cara paling sederhana adalah dengan menentukan satu nilai ambang, misalnya 128. Semua piksel dengan intensitas di bawah 128 diubah menjadi hitam (background), dan semua piksel di atas 128 diubah menjadi putih (objek).

**Pertanyaannya:** Apakah 128 adalah nilai ambang terbaik? Mungkin 150 lebih baik? Atau 110?

  * **Metode Otsu** adalah sebuah cara cerdas untuk secara otomatis menemukan nilai ambang *terbaik* ini. Ia akan mencoba semua kemungkinan nilai ambang (dari 0 hingga 255) dan menghitung "skor" untuk masing-masing nilai. Nilai ambang dengan skor tertinggi adalah pemenangnya.
  * **Tugas Anda** adalah mengganti proses "mencoba semua kemungkinan" tersebut dengan **Genetic Algorithm**. Jadi, GA akan bertugas "mencari" dan "menebak" nilai ambang yang paling optimal, dan untuk mengukur seberapa bagus tebakannya, GA akan menggunakan "skor" dari formula Otsu.

Intinya: **Anda menggunakan metode pencarian GA untuk menyelesaikan masalah optimasi Otsu.**

-----

### 2\. Membedah Konsep-Konsep Kunci

#### a. Thresholding

Ini adalah proses mengubah citra grayscale menjadi citra biner (hanya hitam dan putih). Anda butuh satu angka yang disebut **threshold (T)**.

  * Jika intensitas piksel \> T, piksel menjadi putih (255).
  * Jika intensitas piksel ≤ T, piksel menjadi hitam (0).

#### b. Metode Otsu (Otsu's Method)

Metode Otsu berkata bahwa threshold yang baik adalah yang bisa memisahkan piksel menjadi dua kelompok (background dan foreground) sedemikian rupa sehingga variasi di dalam masing-masing kelompok sangat kecil, sementara variasi *di antara* kedua kelompok sangat besar.

Untuk mencapai ini, Otsu mendefinisikan sebuah **fungsi objektif (objective function)**. Tautan yang diberikan menyebutkan dua, salah satunya yang paling umum adalah "maximization of between-class variance". Rumusnya terlihat rumit, tapi intinya sederhana:

> **Fungsi Objektif Otsu (${\\sigma\_B}^2(t)$):**
>
>   * **Input:** Sebuah nilai threshold `t`.
>   * **Output:** Sebuah "skor" yang mengukur seberapa bagus `t` tersebut dalam memisahkan kelas. Semakin tinggi skornya, semakin baik.

Secara tradisional, metode Otsu menghitung skor ini untuk setiap `t` dari 0 hingga 255 dan memilih `t` dengan skor tertinggi.

#### c. Genetic Algorithm (GA)

GA adalah algoritma pencarian yang terinspirasi dari evolusi biologi. Ia bekerja dengan sebuah "populasi" dari solusi-solusi yang mungkin. Dalam kasus Anda:

  * **Individu/Kromosom:** Sebuah calon nilai threshold. Karena threshold berkisar dari 0-255, Anda bisa merepresentasikannya sebagai angka 8-bit biner (contoh: `10011010` yang setara dengan 154).
  * **Populasi:** Sekumpulan individu (misalnya, 50 buah nilai threshold acak).
  * **Fungsi Fitness (Fitness Function):** Ini adalah jantung dari GA. Fungsi ini mengukur seberapa "baik" atau "fit" sebuah individu. **Di sinilah Anda akan menggunakan fungsi objektif dari Otsu\!** Jadi, `Fitness(individu) = ${\sigma_B}^2(nilai_threshold_individu)`.
  * **Evolusi:** Melalui proses **seleksi** (individu terkuat bertahan), **crossover** (individu saling bertukar gen untuk menciptakan keturunan baru), dan **mutasi** (perubahan acak kecil), populasi akan berevolusi dari generasi ke generasi menuju solusi yang semakin baik.

-----

### 3\. Langkah-Langkah Pengerjaan Proyek (Step-by-Step)

Berikut adalah panduan langkah demi langkah untuk mengerjakan proyek ini:

1.  **Bentuk Kelompok & Setup GitHub:**

      * Tentukan anggota kelompok Anda (maksimal 3 orang).
      * Buat satu repository baru di GitHub untuk proyek ini. Semua anggota harus berkolaborasi di repository ini.

2.  **Pilih & Siapkan Citra:**

      * Pilih beberapa citra uji (test image). Mulailah dengan citra grayscale yang sederhana, misalnya gambar sebuah objek dengan latar belakang yang kontras.
      * Anda perlu bisa membaca citra ini ke dalam program Anda (misalnya menggunakan library seperti OpenCV atau Pillow di Python) dan mendapatkan data intensitas pikselnya.

3.  **Implementasikan Fungsi Objektif Otsu:**

      * Fokus pada artikel yang diberikan. Pilih salah satu dari dua fungsi objektif yang ada. Mari kita ambil contoh "Between-Class Variance".
      * Buat sebuah fungsi, misalnya `calculate_otsu_fitness(image, threshold)`.
      * Fungsi ini akan mengambil data citra dan sebuah nilai threshold sebagai input.
      * Di dalam fungsi ini, Anda akan menghitung:
          * Probabilitas kemunculan kelas background dan foreground.
          * Rata-rata intensitas kelas background dan foreground.
          * Varians antar-kelas (${\\sigma\_B}^2$) sesuai rumus Otsu.
      * Fungsi ini akan mengembalikan nilai varians tersebut. Ini akan menjadi **fungsi fitness** Anda.

4.  **Implementasikan Kerangka Genetic Algorithm:**

      * **a. Inisialisasi Populasi:**

          * Buat populasi awal. Misalnya, buat 50 buah string biner 8-bit secara acak. Setiap string biner ini adalah "individu" atau "kromosom" yang merepresentasikan satu calon threshold.

      * **b. Proses Evolusi (Loop Utama):**

          * Ulangi proses ini untuk sejumlah generasi tertentu (misal, 100 generasi).
          * **Evaluasi Fitness:** Untuk setiap individu di populasi, dekode string binernya menjadi angka (0-255). Gunakan angka ini sebagai input untuk fungsi `calculate_otsu_fitness()` yang sudah Anda buat. Simpan skor fitness untuk setiap individu.
          * **Seleksi:** Pilih individu-individu "terbaik" dari populasi untuk menjadi "orang tua" (parents). Individu dengan fitness lebih tinggi punya kesempatan lebih besar untuk terpilih. (Metode: Roulette Wheel Selection, Tournament Selection).
          * **Crossover:** Ambil pasangan orang tua dan "kawinkan" mereka untuk menghasilkan "anak" (offspring). Crossover pada string biner biasanya berarti memotong string di titik acak dan menukar bagiannya.
          * **Mutasi:** Untuk setiap anak yang baru dibuat, ada kemungkinan kecil (misalnya 1%) satu bitnya akan diubah (dari 0 ke 1 atau 1 ke 0). Ini untuk menjaga keragaman genetik.
          * **Buat Generasi Baru:** Ganti populasi lama dengan populasi anak-anak yang baru terbentuk.

5.  **Dapatkan Hasilnya:**

      * Setelah loop generasi selesai, cari individu dengan skor fitness tertinggi di populasi terakhir.
      * Dekode kromosom biner dari individu terbaik ini menjadi sebuah angka. **Itulah nilai threshold optimal yang ditemukan oleh GA Anda\!**

6.  **Terapkan dan Analisis:**

      * Gunakan nilai threshold yang Anda dapatkan untuk melakukan segmentasi pada citra uji Anda.
      * **Sebagai pembanding**, gunakan fungsi Otsu thresholding yang sudah ada di library seperti OpenCV (`cv2.threshold(img, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)`).
      * Bandingkan hasilnya. Apakah threshold yang ditemukan GA Anda mirip atau sama dengan yang ditemukan oleh fungsi Otsu standar? Seharusnya hasilnya sangat dekat atau bahkan sama persis jika implementasi Anda benar.
      * Tulis laporan yang menjelaskan metode Anda, prosesnya, dan analisis hasilnya. Unggah semua kode ke GitHub.

### Analogi Sederhana

Anggap mencari threshold terbaik itu seperti mencari puncak tertinggi di sebuah pegunungan (ketinggian = skor fitness).

  * **Metode Otsu Standar:** Anda berjalan kaki dan mengukur ketinggian di *setiap meter persegi* di seluruh area pegunungan, lalu kembali ke titik tertinggi yang pernah Anda catat. Pasti ketemu, tapi butuh kerja keras.
  * **Genetic Algorithm:** Anda menerjunkan 50 pendaki dengan parasut di lokasi acak (populasi awal). Di setiap putaran (generasi), Anda berkomunikasi lewat radio. Para pendaki di lokasi yang lebih tinggi (fitness lebih baik) akan menjadi acuan. Anda meminta mereka untuk membentuk tim baru (crossover) untuk menjelajahi area di sekitar mereka, dengan sedikit kemungkinan ada yang mencoba rute baru (mutasi). Lama-kelamaan, semua pendaki akan berkumpul di sekitar puncak tertinggi.

Semoga penjelasan ini membuat tugasnya lebih terbayang. Kuncinya adalah memahami bahwa **Formula Otsu bertindak sebagai "peta" atau "kompas" (fungsi fitness)**, dan **Genetic Algorithm adalah "sang petualang" (algoritma pencari)** yang menggunakan peta tersebut untuk menemukan harta karun (threshold optimal).

Jangan ragu untuk menjadwalkan diskusi dengan dosen/asisten jika ada bagian spesifik (misalnya implementasi crossover atau seleksi) yang masih membingungkan. Selamat mengerjakan\!
