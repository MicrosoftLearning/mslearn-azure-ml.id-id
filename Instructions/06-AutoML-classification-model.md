---
lab:
  title: Temukan model klasifikasi terbaik dengan Pembelajaran Mesin Otomatis
---

# Temukan model klasifikasi terbaik dengan Pembelajaran Mesin Otomatis

Menentukan algoritme yang tepat dan transformasi pra-pemrosesan untuk pelatihan model dapat melibatkan banyak tebakan dan eksperimen.

Dalam latihan ini, Anda akan menggunakan pembelajaran mesin otomatis untuk menentukan algoritme optimal dan langkah-langkah pra-pemrosesan untuk model dengan melakukan beberapa pelatihan secara paralel.

## Sebelum Anda memulai

Anda memerlukan [langganan Azure](https://azure.microsoft.com/free?azure-portal=true) dengan akses tingkat administratif.

## Memprovisikan ruang kerja Azure Machine Learning

*Ruang kerja* Azure Machine Learning menyediakan tempat terpusat untuk mengelola semua sumber daya dan aset yang Anda butuhkan untuk melatih dan mengelola model Anda. Anda dapat berinteraksi dengan ruang kerja Azure Machine Learning melalui studio, Python SDK, dan Azure CLI. 

Anda akan menggunakan Azure CLI untuk menyediakan ruang kerja dan komputasi yang diperlukan, dan Anda akan menggunakan Python SDK untuk melatih model klasifikasi dengan Pembelajaran Mesin Otomatis.

### Membuat ruang kerja dan sumber daya komputasi

Untuk membuat ruang kerja Azure Machine Learning, instans komputasi, dan kluster komputasi, Anda akan menggunakan Azure CLI. Semua perintah yang diperlukan dikelompokkan dalam skrip Shell untuk Anda jalankan.

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

1. Setelah repositori dikloning, masukkan perintah berikut untuk mengubah ke folder untuk lab ini dan jalankan skrip **setup.sh** yang dikandungnya:

    ```azurecli
    cd azure-ml-labs/Labs/06
    ./setup.sh
    ```

    > Abaikan pesan (kesalahan) apa pun yang mengatakan bahwa ekstensi tidak diinstal. 

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

## Melatih model klasifikasi dengan pembelajaran mesin otomatis

Sekarang setelah Anda memiliki semua sumber daya yang diperlukan, Anda dapat menjalankan buku catatan untuk mengonfigurasi dan mengirimkan pekerjaan Pembelajaran Mesin Otomatis.

1. Buka **buku catatan Labs/06/Classification with Automated Machine Learning.ipynb** .

    > Pilih **Autentikasi** dan ikuti langkah-langkah yang diperlukan jika pemberitahuan muncul yang meminta Anda untuk mengautentikasi. 

1. Verifikasi bahwa notebook menggunakan kernel **Python 3.8 - AzureML** . 
1. Jalankan semua sel di buku catatan. 

Pekerjaan baru akan dibuat di ruang kerja Azure Machine Learning. Pekerjaan melacak input yang ditentukan dalam konfigurasi pekerjaan, aset data yang digunakan, dan output seperti metrik untuk mengevaluasi model.

Perhatikan bahwa pekerjaan Pembelajaran Mesin Otomatis berisi pekerjaan anak, yang mewakili model individual yang telah dilatih dan tugas lain yang diperlukan untuk dijalankan. 

3. Ketika pekerjaan Mengotomatiskan Pembelajaran Mesin telah selesai, jelajahi detail pekerjaan di studio:
    - Tab **Pagar pembatas** data memperlihatkan apakah data pelatihan Anda memiliki masalah.
    - Tab **Model** akan menampilkan semua model yang telah dilatih.
    - Pilih **Lihat penjelasan** untuk model terbaik guna memahami fitur mana yang paling memengaruhi nilai target.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure Machine Learning, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Tutup tab studio Azure Machine Learning dan kembali ke portal Azure.
1. Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
1. Pilih grup sumber daya **rg-dp100-labs** .
1. Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**. 
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.