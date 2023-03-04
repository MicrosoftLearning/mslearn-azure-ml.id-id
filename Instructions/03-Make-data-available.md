---
lab:
  title: Membuat data tersedia di Azure Machine Learning
---

# Membuat data tersedia di Azure Machine Learning

Meskipun cukup umum untuk bekerja dengan data pada sistem file lokal mereka, dalam lingkungan perusahaan, menyimpan data di lokasi pusat tempat beberapa ilmuwan data dan insinyur pembelajaran mesin dapat mengaksesnya dapat menjadi lebih efektif.

Dalam latihan ini, Anda akan menjelajahi *penyimpanan data* dan *aset data*, yang merupakan objek utama yang digunakan untuk mengabstraksi akses data di Azure Machine Learning.

## Sebelum Anda memulai

Anda memerlukan [langganan Azure](https://azure.microsoft.com/free?azure-portal=true) dengan akses tingkat administratif.

## Memprovisikan ruang kerja Azure Machine Learning

*Ruang kerja* Azure Machine Learning menyediakan tempat terpusat untuk mengelola semua sumber daya dan aset yang Anda butuhkan untuk melatih dan mengelola model Anda. Anda dapat berinteraksi dengan ruang kerja Azure Machine Learning melalui studio, Python SDK, dan Azure CLI. 

Anda akan menggunakan skrip Shell yang menggunakan Azure CLI untuk menyediakan ruang kerja dan sumber daya yang diperlukan. Selanjutnya, Anda akan menggunakan Perancang di studio Azure Machine Learning untuk melatih dan membandingkan model.

### Membuat ruang kerja dan sumber daya komputasi

Untuk membuat ruang kerja Azure Machine Learning dan sumber daya komputasi, Anda akan menggunakan Azure CLI. Semua perintah yang diperlukan dikelompokkan dalam skrip Shell untuk Anda jalankan.
1. Di browser, buka portal Azure di `https://portal.azure.com/`, masuk dengan akun Microsoft Anda.
1. Pilih tombol \[>_] (*Cloud Shell*) di bagian atas halaman di sebelah kanan kotak pencarian. Tindakan ini akan membuka panel Cloud Shell di bagian bawah portal.
1. Pilih **Bash** jika diminta. Saat pertama kali membuka cloud shell, Anda akan diminta untuk memilih jenis shell yang ingin Anda gunakan (*Bash* atau *PowerShell*). 
1. Periksa apakah langganan yang benar ditentukan dan pilih **Buat penyimpanan** jika Anda diminta untuk membuat penyimpanan untuk shell cloud Anda. Tunggu hingga penyimpanan dibuat.
1. Masukkan perintah berikut di terminal untuk mengkloning repositori ini:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Gunakan `SHIFT + INSERT` untuk menempelkan kode yang disalin ke dalam Cloud Shell. 

1. Masukkan perintah berikut setelah repositori dikloning, untuk mengubah ke folder untuk lab ini dan jalankan skrip **setup.sh** yang dikandungnya:

    ```azurecli
    cd azure-ml-labs/Labs/03
    ./setup.sh
    ```

    > Abaikan pesan (kesalahan) apa pun yang mengatakan bahwa ekstensi tidak diinstal. 

1. Tunggu hingga skrip selesai - ini biasanya memakan waktu sekitar 5-10 menit. 

## Menjelajahi penyimpanan data default

Saat Anda membuat ruang kerja Azure Machine Learning, Akun Penyimpanan secara otomatis dibuat dan tersambung ke ruang kerja Anda. Anda akan menjelajahi bagaimana Akun Penyimpanan tersambung.

1. Di portal Azure, navigasikan ke grup sumber daya baru bernama **rg-dp100-labs**.
1. Pilih Akun Penyimpanan di grup sumber daya. Nama sering dimulai dengan nama yang Anda berikan untuk ruang kerja (tanpa tanda hubung).
1. Tinjau halaman **Gambaran Umum** Akun Penyimpanan. Perhatikan bahwa Akun Penyimpanan memiliki beberapa opsi untuk **Penyimpanan data** seperti yang diperlihatkan di panel Gambaran Umum, dan di menu sebelah kiri.
1. Pilih **Kontainer** untuk menjelajahi bagian penyimpanan Blob dari Akun Penyimpanan. 
1. Perhatikan **kontainer azureml-blobstore-...** . Datastore default untuk aset data menggunakan kontainer ini untuk menyimpan data. 
1. Menggunakan tombol &#43; **Kontainer** di bagian atas layar, buat kontainer baru dan beri nama `training-data`. 
1. Pilih **Berbagi** file dari menu sebelah kiri untuk menjelajahi bagian Berbagi file dari Akun Penyimpanan.
1. Perhatikan **kode-...** berbagi file. Buku catatan apa pun di ruang kerja disimpan di sini. Setelah mengkloning materi lab, Anda dapat menemukan file dalam berbagi file ini, di folder **kode-.../Users/*your-user-name*/azure-ml-labs** .

## Salin kunci akses

Untuk membuat datastore di ruang kerja Azure Machine Learning, Anda perlu memberikan beberapa kredensial. Cara mudah untuk menyediakan ruang kerja dengan akses ke penyimpanan Blob adalah dengan menggunakan kunci akun.

1. Di Akun Penyimpanan, pilih tab **Kunci akses** dari menu sebelah kiri.
1. Perhatikan bahwa dua kunci disediakan: key1 dan key2. Setiap kunci memiliki fungsionalitas yang sama. 
1. Pilih **Tampilkan** untuk bidang **Kunci** di bawah **key1**.
1. Salin nilai bidang **Kunci** ke notepad. Anda harus menempelkan nilai ini ke dalam buku catatan nanti. 
1. Salin nama akun penyimpanan Anda dari bagian atas halaman. Nama harus dimulai dengan **mlwdp100storage...** Anda juga harus menempelkan nilai ini ke dalam buku catatan nanti. 

> **Catatan**: Salin kunci akun dan nama ke notepad untuk menghindari kapitalisasi otomatis (yang terjadi di Word). Kuncinya peka huruf besar/kecil.

## Mengkloning bahan lab

Untuk membuat datastore dan aset data dengan Python SDK, Anda harus mengkloning bahan lab ke ruang kerja.

1. Di portal Azure, navigasikan ke ruang kerja Azure Machine Learning bernama **mlw-dp100-labs**.
1. Pilih ruang kerja Azure Machine Learning, dan di halaman **Gambaran Umumnya** , pilih **Luncurkan studio**. Tab lain akan terbuka di browser Anda untuk membuka studio Azure Machine Learning.
1. Tutup pop-up apa pun yang muncul di studio.
1. Dalam studio Azure Machine Learning, navigasikan ke halaman **Komputasi** dan verifikasi bahwa instans komputasi dan kluster yang Anda buat di bagian sebelumnya ada. Instans komputasi harus berjalan, kluster harus diam dan memiliki 0 simpul yang berjalan.
1. Di tab **Instans komputasi, temukan instans** komputasi Anda, dan pilih aplikasi **Terminal** .
1. Di terminal, instal Python SDK pada instans komputasi dengan menjalankan perintah berikut di terminal:
    
    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mltable
    ```

    > Abaikan pesan (kesalahan) apa pun yang mengatakan bahwa paket tidak diinstal.

1. Jalankan perintah berikut untuk mengkloning repositori Git yang berisi buku catatan, data, dan file lainnya ke ruang kerja Anda:
    
    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```
 
1. Setelah perintah selesai, di panel **File** , klik **&#8635;** untuk menyegarkan tampilan dan memverifikasi bahwa folder **Users/*your-user-name*/azure-ml-labs** baru telah dibuat. 

**Secara opsional**, di tab browser lain, navigasikan kembali ke [portal Azure](https://portal.azure.com?azure-portal=true). Jelajahi kode berbagi file **-...** di akun Penyimpanan lagi untuk menemukan materi lab kloning di folder **azure-ml-labs yang** baru dibuat.

## Membuat datastore dan aset data

Kode untuk membuat penyimpanan data dan aset data dengan Python SDK disediakan dalam buku catatan.

1. Buka buku **catatan Labs/03/Work with data.ipynb** .

    > Pilih **Autentikasi** dan ikuti langkah-langkah yang diperlukan jika pemberitahuan muncul yang meminta Anda untuk mengautentikasi. 

1. Verifikasi bahwa notebook menggunakan kernel **Python 3.8 - AzureML** . 
1. Jalankan semua sel di buku catatan.

## Opsional: Menjelajahi aset data

**Secara opsional**, Anda dapat menjelajahi bagaimana aset data disimpan di Akun Penyimpanan terkait.

1. Navigasi ke tab **Data** di studio Azure Machine Learning untuk menjelajahi aset data. 
1. Pilih nama aset data **diabetes-lokal** untuk menjelajahi detailnya. 

    Di bawah **Sumber data** untuk aset data **diabetes-lokal** , Anda akan menemukan tempat file telah diunggah. Jalur yang dimulai dengan **LocalUpload/...** menunjukkan jalur dalam kontainer Akun Penyimpanan **azureml-blobstore-...**. Anda dapat memverifikasi file yang ada dengan menavigasi ke jalur tersebut di portal Azure.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure Machine Learning, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Tutup tab studio Azure Machine Learning dan kembali ke portal Azure.
1. Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
1. Pilih grup sumber daya **rg-dp100-labs** .
1. Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**. 
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
