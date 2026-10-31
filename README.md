# LAPORAN ANALISIS TUGAS PENGOLAHAN CITRA DIGITAL : SEGMENTASI DAN PENGOLAHAN MORFOLOGI CITRA
Nama : Farhan Rizky Alkarim

NIM : 25/568476/PA/23997

---

### 1. Pendahuluan & Latar Belakang

Dalam Pengolahan Citra Digital, tahapan **segmentasi citra** bertujuan untuk memisahkan objek utama (*foreground*) dari latar belakang (*background*). Namun, dalam kondisi nyata, segmentasi berbasis ambang batas (*thresholding*) murni sering kali tidak menghasilkan region yang sempurna. Permasalahan yang kerap muncul meliputi:
- Adanya **bintik-bintik derau (noise)** pada latar belakang akibat pencahayaan yang tidak merata.
- Adanya **lubang-lubang kecil (holes) atau retakan** pada bagian dalam objek utama akibat variasi intensitas piksel.

Untuk mengatasi kelemahan tersebut, dilakukan tahap **pasca-pemrosesan (post-processing)** menggunakan **Morphological Image Processing**. Pada tugas `PCD_Assignment03` ini, seluruh fungsi segmentasi dan operasi morfologi diimplementasikan secara manual menggunakan matriks array NumPy tanpa mengandalkan pustaka khusus seperti `cv2.morphologyEx`.

---

### 2. Metodologi Implementasi

#### A. Segmentasi Citra (Simple Global Thresholding)
Segmentasi diimplementasikan dengan mengevaluasi nilai derajat keabuan (*grayscale*) $f(x,y)$ terhadap nilai ambang $T = 128$:
$$
g(x,y) = \begin{cases} 255, & \text{jika } f(x,y) \ge T \\ 0, & \text{jika } f(x,y) < T \end{cases}
$$

#### B. Operasi Morfologi Dasar (Kernel $3 \times 3$)
1. **Erosi (Erosion):** Mengikis batas luar objek. Nilai piksel keluaran menjadi $255$ **hanya jika seluruh elemen kernel $1$ bertumpukan sempurna (FIT)** dengan piksel *foreground* pada *Region of Interest* (ROI).
2. **Dilasi (Dilation):** Memperbesar/mempeltebal batas luar objek. Nilai piksel keluaran menjadi $255$ **jika minimal ada satu elemen kernel $1$ yang bertumpukan (HIT)** dengan piksel *foreground* pada ROI.

#### C. Operasi Morfologi Kombinasi
1. **Opening ($A \circ B = (A \ominus B) \oplus B$):** Operasi Erosi yang dilanjutkan dengan Dilasi. Berfungsi untuk menghapus bintik-bintik derau kecil di latar belakang tanpa merusak ukuran asli objek utama.
2. **Closing ($A \bullet B = (A \oplus B) \ominus B$):** Operasi Dilasi yang dilanjutkan dengan Erosi. Berfungsi untuk menutup celah sempit, retakan, serta mengisi lubang-lubang kecil (*holes*) di dalam objek utama.

---

### 3. Hasil & Pembahasan Analisis Perbandingan

| Tahapan Pemrosesan | Kondisi Visual Citra | Analisis & Efek Morfologi |
| :--- | :--- | :--- |
| **1. Citra Grayscale Asli** | Objek memiliki pencahayaan tidak merata dan derau halus. | Masukan mentah sebelum proses binerisasi. |
| **2. Segmentasi Tanpa Morfologi** | Objek terpisah menjadi biner, tetapi latar belakang ber-noise dan objek berlubang. | Segmentasi murni gagal memisahkan objek secara bersih karena hanya bergantung pada batas piksel tunggal. |
| **3. Setelah Operasi Opening** | Bintik-bintik derau di latar belakang hilang sepenuhnya. | Operasi Erosi menghapus derau kecil, lalu Dilasi mengembalikan ukuran asli objek utama. |
| **4. Segmentasi + Morfologi (Hasil Akhir)** | Latar belakang bersih, lubang di dalam objek terisi penuh, objek menjadi padat (*solid*). | Penggabungan **Opening** dan **Closing** menghasilkan region objek yang utuh, padat, dan siap dianalisis. |

---

### 4. Kesimpulan

1. Implementasi algoritma segmentasi dan morfologi secara manual *from scratch* berhasil dilakukan dengan memanfaatkan operasi iterasi spasial matriks pada NumPy.
2. Segmentasi biner murni tanpa pasca-pemrosesan kurang optimal dalam menangani citra ber-noise atau bertekstur tidak merata.
3. Pemrosesan morfologi kombinasi (**Opening** dan **Closing**) terbukti vital sebagai tahap *post-processing* untuk membersihkan derau latar belakang sekaligus memulihkan keutuhan struktur objek utama.
