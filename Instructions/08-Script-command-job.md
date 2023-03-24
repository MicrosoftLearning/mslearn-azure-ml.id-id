---
lab:
  title: Menjalankan skrip pelatihan sebagai pekerjaan perintah di Azure Machine Learning
---

# Menjalankan skrip pelatihan sebagai pekerjaan perintah di Azure Machine Learning

Notebook sangat ideal untuk eksperimen dan pengembangan. Setelah Anda mengembangkan model pembelajaran mesin dan siap untuk produksi, Anda mungkin ingin melatihnya dengan skrip. Anda dapat menjalankan skrip sebagai pekerjaan perintah. 

Dalam latihan ini, Anda akan menguji skrip lalu menjalankannya sebagai pekerjaan perintah.

## Sebelum Anda memulai

Anda memerlukan [langganan Azure](https://azure.microsoft.com/free?azure-portal=true) dengan akses tingkat administratif.

## Memprovisikan ruang kerja Azure Machine Learning

*Ruang kerja* Azure Machine Learning menyediakan tempat terpusat untuk mengelola semua sumber daya dan aset yang Anda butuhkan untuk melatih dan mengelola model Anda. Anda dapat berinteraksi dengan ruang kerja Azure Machine Learning melalui studio, Python SDK, dan Azure CLI. 

Anda akan menggunakan Azure CLI untuk menyediakan ruang kerja dan komputasi yang diperlukan, dan Anda akan menggunakan Python SDK untuk menjalankan pekerjaan perintah.

### Membuat ruang kerja dan sumber daya komputasi

Untuk membuat ruang kerja Azure Machine Learning, instans komputasi, dan kluster komputasi, Anda akan menggunakan Azure CLI. Semua perintah yang diperlukan dikelompokkan dalam skrip Shell untuk Anda jalankan.

1. Di browser, buka portal Azure di `https://portal.azure.com/`, masuk dengan akun Microsoft Anda.
1. Pilih tombol \[>_] (*Cloud Shell*) di bagian atas halaman di sebelah kanan kotak pencarian. Tindakan ini akan membuka panel Cloud Shell di bagian bawah portal.
1. Pilih **Bash** jika diminta. Pertama kali Anda membuka cloud shell, Anda akan diminta untuk memilih jenis shell yang ingin Anda gunakan (*Bash* atau *PowerShell*). 
1. Periksa apakah langganan yang benar ditentukan dan pilih **Buat penyimpanan** jika Anda diminta untuk membuat penyimpanan untuk shell cloud Anda. Tunggu hingga penyimpanan dibuat.
1. Di terminal, masukkan perintah berikut untuk mengkloning repositori ini:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Gunakan `SHIFT + INSERT` untuk menempelkan kode yang disalin ke dalam Cloud Shell. 

1. Setelah repositori dikloning, masukkan perintah berikut untuk mengubah ke folder untuk lab ini dan jalankan skrip **setup.sh** yang dikandungnya:
    
    ```azurecli
    cd azure-ml-labs/Labs/08
    ./setup.sh
    ```

    > Abaikan pesan (kesalahan) yang mengatakan bahwa ekstensi tidak diinstal. 

1. Tunggu hingga skrip selesai - ini biasanya memakan waktu sekitar 5-10 menit. 

## Mengkloning bahan lab

Setelah membuat ruang kerja dan sumber daya komputasi yang diperlukan, Anda dapat membuka studio Azure Machine Learning dan mengkloning materi lab ke ruang kerja. 

1. Di portal Azure, navigasikan ke ruang kerja Azure Machine Learning bernama **mlw-dp100-labs**.
1. Pilih ruang kerja Azure Machine Learning, dan di halaman **Gambaran Umumnya** , pilih **Luncurkan studio**. Tab lain akan terbuka di browser Anda untuk membuka studio Azure Machine Learning.
1. Tutup pop-up apa pun yang muncul di studio.
1. Dalam studio Azure Machine Learning, navigasikan ke halaman **Komputasi** dan verifikasi bahwa instans komputasi dan kluster yang Anda buat di bagian sebelumnya ada. Instans komputasi harus berjalan, kluster harus diam dan memiliki 0 simpul yang berjalan.
1. Di tab **Instans komputasi, temukan instans** komputasi Anda, dan pilih aplikasi **Terminal** .
1. Di terminal, instal Python SDK pada instans komputasi dengan menjalankan perintah berikut di terminal:
    
    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Abaikan pesan (kesalahan) yang mengatakan bahwa paket tidak dapat ditemukan dan dihapus instalasinya.

1. Jalankan perintah berikut untuk mengkloning repositori Git yang berisi buku catatan, data, dan file lainnya ke ruang kerja Anda:
    
    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```
 
1. Setelah perintah selesai, di panel **File** , klik **&#8635;** untuk menyegarkan tampilan dan memverifikasi bahwa folder **Users/*your-user-name*/azure-ml-labs** baru telah dibuat. 

## Mengonversi buku catatan menjadi skrip

Menggunakan notebook yang dilampirkan ke instans komputasi sangat ideal untuk eksperimen dan pengembangan karena memungkinkan Anda untuk segera menjalankan kode yang telah Anda tulis dan tinjau outputnya. Untuk berpindah dari pengembangan ke produksi, Anda mungkin ingin menggunakan skrip. Sebagai langkah pertama, Anda dapat menggunakan studio Azure Machine Learning untuk mengonversi buku catatan Anda menjadi skrip.

1. Buka notebook **Labs/08/src/Train classification model.ipynb** .

    > Pilih **Autentikasi** dan ikuti langkah-langkah yang diperlukan jika pemberitahuan muncul meminta Anda untuk mengautentikasi. 

1. Verifikasi bahwa notebook menggunakan kernel **Python 3.8 - AzureML** . 
1. Jalankan semua sel untuk menjelajahi kode dan melatih model.
1. Pilih ikon &#9776; di bagian atas buku catatan untuk menampilkan **menu buku catatan**. 
1. Perluas **Ekspor sebagai**, dan pilih **Python (.py)** untuk mengonversi buku catatan menjadi skrip Python.
1. Beri nama file baru sebagai `train-classification-model.py`. 
1. Setelah file baru dibuat, skrip akan dibuka secara otomatis. Jelajahi file dan perhatikan bahwa file berisi kode yang sama dengan buku catatan.
1. Pilih ikon &#9655;&#9655; di bagian atas buku catatan untuk **menyimpan dan menjalankan skrip di terminal**.
1. Skrip dimulai oleh **perintah python train-classification-model.py** dan output harus ditunjukkan di bawah perintah. 

## Menguji skrip dengan terminal

Setelah mengonversi buku catatan menjadi skrip, Anda mungkin ingin memperbaikinya lebih lanjut. Salah satu praktik terbaik saat bekerja dengan skrip, adalah menggunakan fungsi. Saat skrip Anda terdiri dari fungsi, lebih mudah untuk menguji kode Anda secara unit. Saat Anda menggunakan fungsi, skrip Anda akan terdiri dari blok kode, setiap blok melakukan tugas tertentu.

1. Buka skrip **Labs/08/src/train-model-parameters.py** dan jelajahi kontennya. 
    Perhatikan bahwa ada fungsi utama yang mencakup empat fungsi lainnya:
    
    - Membaca data
    - Memisahkan Data
    - Model latihan
    - Mengevaluasi model

    Setelah fungsi utama, setiap fungsi ditentukan. Perhatikan bagaimana setiap fungsi mendefinisikan input dan output yang diharapkan.

1. Pilih ikon &#9655;&#9655; di bagian atas buku catatan untuk **menyimpan dan menjalankan skrip di terminal**. Anda akan mendapatkan kesalahan setelah **Membaca data...** memberi tahu Anda bahwa itu tidak bisa mendapatkan data karena jalur file tidak valid. 
    
    Skrip memungkinkan Anda membuat parameter kode untuk dengan mudah mengubah data atau parameter input. Dalam hal ini, skrip mengharapkan parameter input untuk jalur data yang tidak kami sediakan. Anda dapat menemukan parameter yang ditentukan dan diharapkan di akhir skrip dalam fungsi **parse_args().** 

    Ada dua parameter input yang ditentukan:
    - **--training_data** yang mengharapkan string.
    - **--reg_rate** yang mengharapkan angka, tetapi memiliki nilai default 0,01.
    
    Untuk menjalankan skrip dengan sukses, Anda harus menentukan nilai untuk parameter data pelatihan. Mari kita lakukan dengan merujuk ke file **diabetes.csv** yang disimpan dalam folder yang sama dengan skrip pelatihan.

1. Di terminal, jalankan perintah berikut:

    ```
    python train-model-parameters.py --training_data diabetes.csv
    ```

Skrip harus berhasil dijalankan dan sebagai hasilnya, output harus menunjukkan akurasi dan AUC model terlatih.

Menguji skrip di terminal sangat ideal untuk memverifikasi apakah skrip berfungsi seperti yang diharapkan. Jika ada masalah dengan kode, Anda akan menerima kesalahan di terminal. 

**Secara opsional**, edit kode untuk memaksa kesalahan dan jalankan perintah lagi di terminal untuk menjalankan skrip. Misalnya, hapus **panda impor baris sebagai pd**, simpan dan jalankan skrip dengan parameter input untuk meninjau pesan kesalahan.

## Menjalankan skrip sebagai pekerjaan perintah

Jika Anda tahu skrip berfungsi, Anda dapat menjalankannya sebagai pekerjaan perintah. Dengan menjalankan skrip Anda sebagai pekerjaan perintah, Anda akan dapat melacak semua input dan output skrip.

1. Buka buku catatan **Labs/08/Jalankan skrip sebagai command job.ipynb** .
1. Jalankan semua sel di buku catatan.
1. Di studio Azure Machine Learning, navigasikan ke halaman **Pekerjaan** . 
1. Navigasikan ke pekerjaan **diabetes-train-script** untuk menjelajahi gambaran umum pekerjaan perintah yang Anda jalankan. 
1. Tinjau properti pekerjaan. Perhatikan bahwa **Command** menunjukkan nilai parameter **perintah** dari pekerjaan perintah: perintah yang Anda jalankan untuk menjalankan skrip.
1. Navigasi ke tab **Kode** . Semua konten folder **src** , yang merupakan nilai parameter **kode** dari pekerjaan perintah, disalin di sini. Anda dapat meninjau skrip pelatihan yang dijalankan oleh pekerjaan perintah.
1. Navigasi ke tab **Output + log** . 
1. Buka file **std_log.txt** dan jelajahi isinya. Konten file ini adalah output dari perintah. Ingat output yang sama ditampilkan di terminal ketika Anda menguji skrip di sana. Jika pekerjaan tidak berhasil karena masalah dengan skrip, pesan kesalahan akan ditampilkan di sini.

**Secara opsional**, edit kode untuk memaksa kesalahan dan gunakan notebook untuk memulai pekerjaan perintah lagi. Misalnya, hapus **panda impor baris sebagai pd** dari skrip dan simpan skrip. Atau, edit konfigurasi pekerjaan perintah untuk menjelajahi pesan kesalahan ketika ada yang salah dengan konfigurasi pekerjaan itu sendiri alih-alih skrip.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure Machine Learning, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Tutup tab studio Azure Machine Learning dan kembali ke portal Azure.
1. Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
1. Pilih grup sumber daya **rg-dp100-labs** .
1. Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**. 
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.