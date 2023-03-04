---
lab:
  title: Melatih model dengan Perancang Azure Machine Learning
---

# Melatih model dengan Perancang Azure Machine Learning

Perancang Azure Machine Learning menyediakan antarmuka seret dan letakkan yang dapat Anda tentukan alur kerjanya. Anda dapat membuat alur kerja untuk melatih model, menguji, dan membandingkan beberapa algoritma dengan mudah.

Dalam latihan ini, Anda akan menggunakan Perancang untuk melatih dan membandingkan dua algoritma klasifikasi dengan cepat.

## Sebelum Anda memulai

Anda memerlukan [langganan Azure](https://azure.microsoft.com/free?azure-portal=true) dengan akses tingkat administratif.

## Memprovisikan ruang kerja Azure Machine Learning

*Ruang kerja* Azure Machine Learning menyediakan tempat terpusat untuk mengelola semua sumber daya dan aset yang Anda butuhkan untuk melatih dan mengelola model Anda. Anda dapat berinteraksi dengan ruang kerja Azure Machine Learning melalui studio, Python SDK, dan Azure CLI. 

Anda akan menggunakan skrip Shell yang menggunakan Azure CLI untuk menyediakan ruang kerja dan sumber daya yang diperlukan. Selanjutnya, Anda akan menggunakan Perancang di studio Azure Machine Learning untuk melatih dan membandingkan model.

### Membuat ruang kerja dan kluster komputasi

Untuk membuat ruang kerja Azure Machine Learning dan kluster komputasi, Anda akan menggunakan Azure CLI. Semua perintah yang diperlukan dikelompokkan dalam skrip Shell untuk Anda jalankan.

1. Di browser, buka portal Azure di `https://portal.azure.com/`, masuk dengan akun Microsoft Anda.
1. Pilih tombol \[>_] (*Cloud Shell*) di bagian atas halaman di sebelah kanan kotak pencarian. Tindakan ini akan membuka panel Cloud Shell di bagian bawah portal.
1. Pilih **Bash** jika diminta. Saat pertama kali membuka cloud shell, Anda akan diminta untuk memilih jenis shell yang ingin Anda gunakan (*Bash* atau *PowerShell*). 
1. Periksa apakah langganan yang benar ditentukan dan pilih **Buat penyimpanan** jika Anda diminta untuk membuat penyimpanan untuk shell cloud Anda. Tunggu hingga penyimpanan dibuat.
1. Di terminal, masukkan perintah berikut untuk mengkloning repositori ini:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Gunakan `SHIFT + INSERT` untuk menempelkan kode yang disalin ke dalam Cloud Shell. 

1. Setelah repositori dikloning, masukkan perintah berikut untuk mengubah ke folder untuk lab ini dan jalankan skrip setup.sh yang dikandungnya:

    ```azurecli
    cd azure-ml-labs/Labs/05
    ./setup.sh
    ```

    > Abaikan pesan (kesalahan) apa pun yang mengatakan bahwa ekstensi tidak diinstal. 

1. Tunggu hingga skrip selesai - ini biasanya memakan waktu sekitar 5-10 menit. 

## Mengonfigurasi alur baru

Setelah membuat ruang kerja dan kluster komputasi yang diperlukan, Anda dapat membuka studio Azure Machine Learning dan membuat alur pelatihan dengan Perancang. 

1. Di portal Azure, navigasikan ke ruang kerja Azure Machine Learning bernama **mlw-dp100-labs**.
1. Pilih ruang kerja Azure Machine Learning, dan di halaman **Gambaran Umumnya** , pilih **Luncurkan studio**. Tab lain akan terbuka di browser Anda untuk membuka studio Azure Machine Learning.
1. Tutup pop-up apa pun yang muncul di studio.
1. Dalam studio Azure Machine Learning, navigasikan ke halaman **Komputasi** dan verifikasi bahwa kluster komputasi yang Anda buat di bagian sebelumnya ada. Kluster harus menganggur dan memiliki 0 simpul yang berjalan.
1. Navigasi ke halaman **Perancang** .
1. Pilih tab **Kustom** di bagian atas halaman.
1. Buat alur kosong baru menggunakan komponen kustom.
1. Ubah nama alur default (**Pipeline-Created-on-date****) menjadi `Train-Diabetes-Classifier` dengan mengklik ikon **&#9881;** di sebelah kanan untuk membuka panel **Pengaturan** .
1. Anda harus menentukan target komputasi untuk menjalankan alur. Di panel **Pengaturan** , di bawah **Pilih jenis komputasi** dan pilih **Kluster komputasi**, dan di bawah **Pilih kluster komputasi Azure ML** dan pilih **aml-cluster**. Tutup panel pengaturan.

## Buat alur baru
Untuk melatih model, Anda memerlukan data. Anda dapat menggunakan data apa pun yang disimpan di datastore atau menggunakan URL yang dapat diakses publik.

1. Di menu sebelah kiri, pilih tab **Data** .
1. Seret dan letakkan komponen **folder diabetes** ke kanvas.

    Sekarang setelah Anda memiliki data, Anda dapat melanjutkan dengan membuat alur menggunakan komponen kustom yang sudah ada di dalam ruang kerja (dibuat untuk Anda selama penyiapan).

1. Di menu sebelah kiri, pilih tab **Komponen** .
1. Seret dan letakkan komponen **Hapus Baris Kosong** ke kanvas, di bawah **folder diabetes**. 
1. Sambungkan output data ke input komponen baru.
1. Seret dan **letakkan komponen Normalisasi kolom numerik** ke kanvas, di bawah **Hapus baris kosong**. 
1. Sambungkan output komponen sebelumnya ke input komponen baru.
1. Seret dan letakkan komponen **Latih Model Pengklasifikasi Pohon Keputusan** ke kanvas, di bawah **Hapus baris kosong**.
1. Sambungkan output komponen sebelumnya ke input komponen baru. 
1. Kirimkan alur Anda. 
1. Buat eksperimen baru dan beri `diabetes-designer-pipeline`nama . 
1. Tunggu hingga semua komponen berhasil diselesaikan.

    Mengirimkan pekerjaan akan menginisialisasi kluster komputasi. Karena kluster komputasi menganggur hingga saat ini, mungkin perlu beberapa waktu bagi kluster untuk mengubah ukuran menjadi lebih dari 0 simpul. Setelah kluster diubah ukurannya, kluster akan secara otomatis mulai menjalankan alur. 

1. Anda dapat melihat status eksekusi alur dengan memilih **Detail pekerjaan** di panel **Pekerjaan yang dikirimkan** di sebelah kiri.

Anda akan dapat melacak eksekusi setiap komponen. Ketika alur gagal, Anda akan dapat menjelajahi komponen mana yang gagal dan mengapa gagal. Pesan kesalahan akan ditampilkan di tab **Output + log** dari ringkasan pekerjaan. 

## Melatih model kedua untuk membandingkan

Untuk membandingkan antara algoritma dan mengevaluasi mana yang berkinerja lebih baik, Anda dapat melatih dua model dalam satu alur dan membandingkan.

1. Dalam alur yang sama dengan yang telah Anda kerjakan sejauh ini
1. Tambahkan komponen **Latih Model Pengklasifikasi Regresi Logistik** ke kanvas, di samping komponen pelatihan lainnya.
1. Sambungkan output komponen **Normalisasi kolom numerik** ke input komponen pelatihan baru. 
1. Di bagian atas, pilih **Kirim**. 
1. Ketika diminta, buat eksperimen baru bernama `designer-compare-classification`, dan jalankan.  
1. Setelah alur selesai, tinjau **Metrik** untuk kedua komponen pelatihan.
1. Coba dan tentukan model mana yang berkinerja lebih baik.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure Machine Learning, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Tutup tab studio Azure Machine Learning dan kembali ke portal Azure.
1. Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
1. Pilih grup sumber daya **rg-dp100-labs** .
1. Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**. 
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.