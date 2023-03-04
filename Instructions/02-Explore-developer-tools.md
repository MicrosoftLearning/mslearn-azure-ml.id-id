---
lab:
  title: Menjelajahi alat pengembang untuk interaksi ruang kerja
---

# Menjelajahi alat pengembang untuk interaksi ruang kerja

Anda dapat menggunakan berbagai alat untuk berinteraksi dengan ruang kerja Azure Machine Learning. Bergantung pada tugas apa yang perlu Anda lakukan dan preferensi Anda untuk alat pengembang, Anda dapat memilih alat mana yang akan digunakan kapan. Lab ini dirancang sebagai pengantar alat pengembang yang biasa digunakan untuk interaksi ruang kerja. Jika Anda ingin mempelajari cara menggunakan alat tertentu secara lebih mendalam, ada lab lain untuk dijelajahi.

## Sebelum Anda memulai

Anda memerlukan [langganan Azure](https://azure.microsoft.com/free?azure-portal=true) dengan akses tingkat administratif.

Alat pengembang yang umum digunakan untuk berinteraksi dengan ruang kerja Azure Machine Learning adalah:
- **Azure CLI** dengan ekstensi Azure Machine Learning: Pendekatan baris perintah ini sangat ideal untuk otomatisasi infrastruktur.
- **Studio Azure Machine Learning**: Gunakan UI yang mudah digunakan untuk menjelajahi ruang kerja dan semua kemampuannya.
- **Python SDK** untuk Azure Machine Learning: Gunakan untuk mengirimkan pekerjaan dan mengelola model dari notebook Jupyter, ideal untuk ilmuwan data.

Anda akan menjelajahi masing-masing alat ini untuk tugas yang biasanya dilakukan dengan alat tersebut.

## Memprovisikan infrastruktur dengan Azure CLI

Agar ilmuwan data melatih model pembelajaran mesin dengan Azure Machine Learning, Anda harus menyiapkan infrastruktur yang diperlukan. Anda dapat menggunakan Azure CLI dengan ekstensi Azure Machine Learning untuk membuat ruang kerja dan sumber daya Azure Machine Learning seperti instans komputasi. 

Untuk memulai, buka Cloud Shell Azure, instal ekstensi Azure Machine Learning dan kloning repositori Git.

1. Di browser, buka portal Azure di `https://portal.azure.com/`, masuk dengan akun Microsoft Anda.
1. Pilih tombol \[>_] (*Cloud Shell*) di bagian atas halaman di sebelah kanan kotak pencarian. Tindakan ini akan membuka panel Cloud Shell di bagian bawah portal.
1. Pilih **Bash** jika diminta. Saat pertama kali membuka cloud shell, Anda akan diminta untuk memilih jenis shell yang ingin Anda gunakan (*Bash* atau *PowerShell*). 
1. Periksa apakah langganan yang benar ditentukan dan pilih **Buat penyimpanan** jika Anda diminta untuk membuat penyimpanan untuk shell cloud Anda. Tunggu hingga penyimpanan dibuat.
1. Hapus ekstensi ML CLI apa pun (versi 1 dan 2) untuk menghindari konflik dengan versi sebelumnya dengan perintah ini:
    
    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > Gunakan `SHIFT + INSERT` untuk menempelkan kode yang disalin ke dalam Cloud Shell. 

    > Abaikan pesan (kesalahan) apa pun yang mengatakan bahwa ekstensi tidak diinstal. 

1. Instal ekstensi Azure Machine Learning (v2) dengan perintah berikut:
    
    ```azurecli
    az extension add -n ml -y
    ```

1. Buat grup sumber daya. Pilih lokasi yang dekat dengan Anda.
    
    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. Membuat ruang kerja:
    
    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. Tunggu hingga ruang kerja dan sumber daya terkait dibuat - ini biasanya memakan waktu sekitar 5 menit. 

## Membuat instans komputasi dengan Azure CLI

Bagian penting lain dari infrastruktur yang diperlukan untuk melatih model pembelajaran mesin adalah **komputasi**. Meskipun Anda dapat melatih model secara lokal, lebih dapat diskalakan dan hemat biaya untuk menggunakan komputasi cloud. 

Ketika ilmuwan data mengembangkan model pembelajaran mesin di ruang kerja Azure Machine Learning, mereka ingin menggunakan komputer virtual tempat mereka dapat menjalankan notebook Jupyter. Untuk pengembangan, **instans komputasi** sangat cocok. 

Setelah membuat ruang kerja Azure Machine Learning, Anda juga dapat membuat instans komputasi menggunakan Azure CLI. 

Dalam latihan ini, Anda akan membuat instans komputasi dengan pengaturan berikut:
- **Nama komputasi**: *Nama instans komputasi. Harus unik dan kurang dari 24 karakter.*
- **Ukuran komputer virtual**: STANDARD_DS11_V2
- **Jenis komputasi** (instans atau kluster): ComputeInstance
- **Nama ruang kerja Azure Machine Learning**: mlw-dp100-labs
- **Grup sumber daya**: rg-dp100-labs

1. Gunakan perintah berikut untuk membuat instans komputasi di ruang kerja Anda. Jika nama instans komputasi berisi "XXXX", ganti dengan angka acak untuk membuat nama unik. 

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    Jika Anda mendapatkan pesan kesalahan bahwa instans komputasi dengan nama sudah ada, ubah nama dan coba lagi perintah .

## Membuat kluster komputasi dengan Azure CLI

Meskipun instans komputasi sangat ideal untuk pengembangan, kluster komputasi lebih cocok ketika kita ingin melatih model pembelajaran mesin. Hanya ketika pekerjaan dikirimkan untuk menggunakan kluster komputasi, apakah akan mengubah ukurannya menjadi lebih dari 0 simpul dan menjalankan pekerjaan. Setelah kluster komputasi tidak lagi diperlukan, kluster akan secara otomatis mengubah ukuran kembali ke 0 simpul untuk meminimalkan biaya. 

Untuk membuat kluster komputasi, Anda dapat menggunakan Azure CLI, mirip dengan membuat instans komputasi. 

Anda akan membuat kluster komputasi dengan pengaturan berikut:
- **Nama komputasi**: aml-cluster
- **Ukuran komputer virtual**: STANDARD_DS11_V2
- **Jenis komputasi**: AmlCompute *(Membuat kluster komputasi)*
- **Instans maksimum**: *Jumlah maksimum simpul*
- **Nama ruang kerja Azure Machine Learning**: mlw-dp100-labs
- **Grup sumber daya**: rg-dp100-labs

1. Gunakan perintah berikut untuk membuat kluster komputasi di ruang kerja Anda.
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

## Mengonfigurasi stasiun kerja Anda dengan studio Azure Machine Learning

Meskipun Azure CLI sangat ideal untuk otomatisasi, Anda mungkin ingin meninjau output perintah yang Anda jalankan. Anda dapat menggunakan Azure Machine Learning Studio untuk memeriksa apakah sumber daya dan aset telah dibuat, dan untuk memeriksa apakah pekerjaan berhasil dijalankan atau meninjau mengapa pekerjaan gagal. 

1. Di portal Azure, navigasikan ke ruang kerja Azure Machine Learning bernama **mlw-dp100-labs**.
1. Pilih ruang kerja Azure Machine Learning, dan di halaman **Gambaran Umumnya** , pilih **Luncurkan studio**. Tab lain akan terbuka di browser Anda untuk membuka studio Azure Machine Learning.
1. Tutup pop-up apa pun yang muncul di studio. 
1. Dalam studio Azure Machine Learning, navigasikan ke halaman **Komputasi** dan verifikasi bahwa instans komputasi dan kluster yang Anda buat di bagian sebelumnya ada. Instans komputasi harus berjalan, kluster harus diam dan memiliki 0 simpul yang berjalan.

## Menggunakan Python SDK untuk melatih model

Sekarang setelah Anda memverifikasi bahwa komputasi yang diperlukan telah dibuat, Anda dapat menggunakan Python SDK untuk menjalankan skrip pelatihan. Anda akan menginstal dan menggunakan Python SDK pada instans komputasi dan melatih model pembelajaran mesin pada kluster komputasi.

1. Pilih aplikasi **Terminal** untuk **instans komputasi** Anda untuk meluncurkan terminal.
1. Di terminal, instal Python SDK pada instans komputasi dengan menjalankan perintah berikut di terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Abaikan pesan (kesalahan) apa pun yang mengatakan bahwa paket tidak diinstal.

1. Jalankan perintah berikut untuk mengkloning repositori Git yang berisi buku catatan, data, dan file lainnya ke ruang kerja Anda:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ``` 

1. Setelah perintah selesai, di panel **File** , pilih **&#8635;** untuk me-refresh tampilan dan memverifikasi bahwa folder **Users/*your-user-name*/azure-ml-labs** baru telah dibuat. 
1. Buka buku catatan **Labs/02/Jalankan pelatihan script.ipynb** .
    
    > Pilih **Autentikasi** dan ikuti langkah-langkah yang diperlukan jika pemberitahuan muncul yang meminta Anda untuk mengautentikasi. 

1. Verifikasi bahwa notebook menggunakan kernel **Python 3.8 - AzureML** . Setiap kernel memiliki gambar sendiri dengan serangkaian paketnya sendiri yang telah diinstal sebelumnya.
1. Jalankan semua sel di buku catatan. 

Pekerjaan baru akan dibuat di ruang kerja Azure Machine Learning. Pekerjaan melacak input yang ditentukan dalam konfigurasi pekerjaan, kode yang digunakan, dan output seperti metrik untuk mengevaluasi model.

## Meninjau riwayat pekerjaan Anda di studio Azure Machine Learning

Saat mengirimkan pekerjaan ke ruang kerja Azure Machine Learning, Anda dapat meninjau statusnya di studio Azure Machine Learning.

1. Pilih URL pekerjaan yang disediakan sebagai output di buku catatan, atau navigasikan ke halaman **Pekerjaan** di studio Azure Machine Learning.
1. Eksperimen baru tercantum bernama **diabetes-training**. Pilih pekerjaan terbaru **diabetes-pythonv2-train**.
1. Tinjau **Properti** pekerjaan. Perhatikan **Status** pekerjaan: 
    - **Diantrekan**: Pekerjaan sedang menunggu komputasi tersedia.
    - **Persiapan**: Kluster komputasi mengubah ukuran atau lingkungan sedang diinstal pada target komputasi.
    - **Berjalan**: Skrip pelatihan sedang dijalankan. 
    - **Menyelesaikan**: Skrip pelatihan berjalan dan pekerjaan sedang diperbarui dengan semua informasi akhir.
    - **Selesai**: Pekerjaan berhasil diselesaikan dan dihentikan.
    - **Gagal**: Pekerjaan gagal dan dihentikan.
1. Ketika pekerjaan selesai, Anda dapat menemukan *AUC* dan *Akurasi* model di bawah **Metrik**. 
1. Di bawah **Output + log**, Anda akan menemukan:
    - File pickle model dalam **output** folder. 
    - Output skrip dalam **user_logs/std_log.txt**. Output dari pernyataan **cetak** dalam skrip akan ditampilkan di sini. Jika ada kesalahan karena masalah dengan skrip, Anda juga akan menemukan pesan kesalahan di sini.
1. Di bawah **Kode**, Anda akan menemukan folder yang Anda tentukan dalam konfigurasi pekerjaan. Folder ini mencakup skrip pelatihan dan himpunan data.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure Machine Learning, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Tutup tab studio Azure Machine Learning dan kembali ke portal Azure.
1. Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
1. Pilih grup sumber daya **rg-dp100-labs** .
1. Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**. 
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.