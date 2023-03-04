---
lab:
  title: Bekerja dengan sumber daya komputasi di Azure Machine Learning
---

# Bekerja dengan sumber daya komputasi di Azure Machine Learning

Salah satu manfaat utama cloud adalah kemampuan untuk menggunakan sumber daya komputasi sesuai permintaan yang dapat diskalakan untuk pemrosesan data besar yang hemat biaya.

Dalam latihan ini, Anda akan mempelajari cara menggunakan komputasi cloud di Azure Machine Learning untuk menjalankan eksperimen dan kode produksi dalam skala besar.

## Sebelum Anda memulai

Anda memerlukan [langganan Azure](https://azure.microsoft.com/free?azure-portal=true) dengan akses tingkat administratif.

## Memprovisikan ruang kerja Azure Machine Learning

*Ruang kerja* Azure Machine Learning menyediakan tempat terpusat untuk mengelola semua sumber daya dan aset yang Anda butuhkan untuk melatih dan mengelola model Anda. Anda dapat berinteraksi dengan ruang kerja Azure Machine Learning melalui studio, Python SDK, dan Azure CLI.

Untuk membuat ruang kerja Azure Machine Learning, Anda akan menggunakan Azure CLI. Semua perintah yang diperlukan dikelompokkan dalam skrip Shell untuk Anda jalankan.

1. Di browser, buka portal Azure di `https://portal.azure.com/`, masuk dengan akun Microsoft Anda.
1. Pilih tombol \[>_] (*Cloud Shell*) di bagian atas halaman di sebelah kanan kotak pencarian. Tindakan ini akan membuka panel Cloud Shell di bagian bawah portal.
1. Pilih **Bash** jika diminta. Pertama kali Anda membuka cloud shell, Anda akan diminta untuk memilih jenis shell yang ingin Anda gunakan (*Bash* atau *PowerShell*). 
1. Periksa apakah langganan yang benar ditentukan dan pilih **Buat penyimpanan** jika Anda diminta untuk membuat penyimpanan untuk shell cloud Anda. Tunggu hingga penyimpanan dibuat.
1. Untuk menghindari konflik dengan versi sebelumnya, hapus ekstensi ML CLI apa pun (versi 1 dan 2) dengan menjalankan perintah ini di terminal:

    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > Gunakan `SHIFT + INSERT` untuk menempelkan kode yang disalin ke dalam Cloud Shell. 

    > Abaikan pesan (kesalahan) yang mengatakan bahwa ekstensi tidak diinstal. 

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

1. Tunggu hingga perintah selesai - ini biasanya memakan waktu sekitar 5-10 menit. 

## Membuat skrip penyiapan komputasi

Untuk menjalankan notebook dalam ruang kerja Azure Machine Learning, Anda memerlukan instans komputasi. Anda dapat menggunakan skrip penyiapan untuk mengonfigurasi instans komputasi saat pembuatan.

1. Di portal Azure, navigasikan ke ruang kerja Azure Machine Learning bernama **mlw-dp100-labs**.
1. Pilih ruang kerja Azure Machine Learning, dan di halaman **Gambaran Umumnya** , pilih **Luncurkan studio**. Tab lain akan terbuka di browser Anda untuk membuka studio Azure Machine Learning.
1. Tutup pop-up apa pun yang muncul di studio.
1. Di dalam studio Azure Machine Learning, navigasikan ke halaman **Buku Catatan** .
1. Di panel **File** , pilih ikon &#10753; untuk **Menambahkan file**. 
1. Pilih **Buat file baru**.
1. Verifikasi bahwa lokasi file adalah **Users/* your-user-name***.
1. Ubah jenis file menjadi **Bash (*.sh)**.
1. Ubah nama file menjadi `compute-setup.sh`.
1. Buka file **compute-setup.sh** yang baru dibuat dan tempelkan yang berikut ini ke kontennya:

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Simpan file **compute-setup.sh** .

## Membuat instans komputasi

Untuk membuat instans komputasi, Anda dapat menggunakan studio, Python SDK, atau Azure CLI. Anda akan menggunakan studio untuk membuat instans komputasi dengan skrip penyiapan yang baru saja Anda buat.

1. Navigasi ke halaman **Komputasi** , menggunakan menu di sebelah kiri.
1. Di tab **Instans komputasi** , pilih **Baru**.
1. Konfigurasikan (jangan buat) instans komputasi dengan pengaturan berikut: 
    - **Nama komputasi**: *masukkan nama unik*
    - **Jenis komputer virtual**: *CPU*
    - **Ukuran komputer virtual**: *Standard_DS11_v2*
1. Pilih **Berikutnya: Pengaturan tingkat lanjut**.
1. Pilih **Tambahkan jadwal** dan konfigurasikan jadwal untuk **menghentikan** instans komputasi setiap hari pada pukul **18:00** atau 18 **:00**. 
1. Pilih tombol untuk **Provisi dengan skrip penyiapan**. 
1. Pilih skrip **compute-setup.sh** yang Anda buat sebelumnya.
1. Tinjau pengaturan tingkat lanjut lainnya tetapi **jangan** pilih:
    - **Aktifkan akses SSH**: *Anda dapat menggunakan ini untuk mengaktifkan akses langsung ke komputer virtual menggunakan klien SSH.*
    - **Aktifkan jaringan virtual**: *Anda biasanya akan menggunakan ini di lingkungan perusahaan untuk meningkatkan keamanan jaringan.*
    - **Tetapkan ke pengguna lain**: *Anda dapat menggunakan ini untuk menetapkan instans komputasi ke ilmuwan data lain.*
1. **Buat** instans komputasi dan tunggu hingga dimulai dan statusnya berubah menjadi **Berjalan**.
1. Saat instans komputasi berjalan, navigasikan ke halaman **Buku Catatan** . Di panel **File** , klik **&#8635;** untuk me-refresh tampilan dan memverifikasi bahwa folder **Users/*your-user-name*/dp100-azure-ml-labs** baru telah dibuat. 

## Mengonfigurasi instans komputasi

Saat Anda telah membuat instans komputasi, Anda bisa menjalankan buku catatan di dalamnya. Anda mungkin perlu menginstal paket tertentu untuk menjalankan kode yang Anda inginkan. Anda dapat menyertakan paket dalam skrip penyiapan, atau menginstalnya menggunakan terminal.

1. Di tab **Instans komputasi, temukan instans** komputasi Anda, dan pilih aplikasi **Terminal** .
1. Di terminal, instal Python SDK pada instans komputasi dengan menjalankan perintah berikut di terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Abaikan pesan (kesalahan) yang mengatakan bahwa paket tidak diinstal.

1. Ketika paket diinstal, Anda dapat menutup tab untuk mengakhiri terminal. 

## Membuat klaster komputasi

Notebook sangat ideal untuk pengembangan atau pekerjaan berulang selama eksperimen. Saat bereksperimen, Anda mungkin ingin menjalankan notebook pada instans komputasi untuk menguji dan meninjau kode dengan cepat. Saat pindah ke produksi, Anda mungkin ingin menjalankan skrip pada kluster komputasi. Anda akan membuat kluster komputasi dengan Python SDK, lalu menggunakannya untuk menjalankan skrip sebagai pekerjaan.

1. Buka buku catatan **Labs/04/Work with compute.ipynb** .

    > Pilih **Autentikasi** dan ikuti langkah-langkah yang diperlukan jika pemberitahuan muncul meminta Anda untuk mengautentikasi. 

1. Verifikasi bahwa notebook menggunakan kernel **Python 3.8 - AzureML** . 
1. Jalankan semua sel di buku catatan.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure Machine Learning, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Tutup tab studio Azure Machine Learning dan kembali ke portal Azure.
1. Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
1. Pilih grup sumber daya **rg-dp100-labs** .
1. Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**. 
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
