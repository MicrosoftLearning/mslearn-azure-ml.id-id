---
lab:
  title: Menjelajahi ruang kerja Azure Machine Learning
---

# Menjelajahi ruang kerja Azure Machine Learning

Azure Machine Learning menyediakan platform ilmu data untuk melatih dan mengelola model pembelajaran mesin. Di lab ini, Anda akan membuat ruang kerja Azure Machine Learning dan menjelajahi berbagai cara untuk bekerja dengan ruang kerja. Lab ini dirancang sebagai pengenalan berbagai kemampuan inti Azure Machine Learning dan alat pengembang. Jika Anda ingin mempelajari tentang kemampuan secara lebih mendalam, ada lab lain untuk dijelajahi.

## Sebelum Anda memulai

Anda memerlukan [langganan Azure](https://azure.microsoft.com/free?azure-portal=true) dengan akses tingkat administratif.

## Memprovisikan ruang kerja Azure Machine Learning

**Ruang kerja** Azure Machine Learning menyediakan tempat terpusat untuk mengelola semua sumber daya dan aset yang Anda butuhkan untuk melatih dan mengelola model Anda. Anda dapat menyediakan ruang kerja menggunakan antarmuka interaktif di portal Azure, atau Anda dapat menggunakan Azure CLI dengan ekstensi Azure Machine Learning. Dalam sebagian besar skenario produksi, yang terbaik adalah mengotomatiskan provisi dengan CLI sehingga Anda dapat memasukkan penyebaran sumber daya ke dalam proses pengembangan dan operasi (*DevOps*) yang dapat diulang. 

Dalam latihan ini, Anda akan menggunakan portal Azure untuk memprovisikan Azure Machine Learning untuk menjelajahi semua opsi.

1. Masuk ke `https://portal.azure.com/`.
2. Buat sumber daya **Azure Machine Learning** baru dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: `rg-dp100-labs`
    - **Nama ruang kerja**: `mlw-dp100-labs`
    - **Wilayah**: *Pilih wilayah geografis yang paling dekat dengan Anda*
    - **Akun penyimpanan**: *Perhatikan akun penyimpanan baru default yang akan dibuat untuk ruang kerja Anda*
    - **Brankas kunci**: *Perhatikan brankas kunci baru default yang akan dibuat untuk ruang kerja Anda*
    - **Wawasan aplikasi**: *Perhatikan sumber daya wawasan aplikasi baru default yang akan dibuat untuk ruang kerja Anda*
    - **Registri kontainer**: Tidak ada (*satu registri kontainer akan dibuat secara otomatis saat pertama kali Anda menyebarkan model ke kontainer*)
3. Tunggu hingga ruang kerja dan sumber daya terkait dibuat - ini biasanya memakan waktu sekitar 5 menit. 

> **Catatan**: Saat membuat ruang kerja Azure Machine Learning, Anda dapat menggunakan beberapa opsi lanjutan untuk membatasi akses melalui *titik akhir privat* dan menentukan kunci khusus untuk enkripsi data. Kami tidak akan menggunakan opsi ini dalam latihan ini - tetapi Anda harus mengetahuinya!

## Menjelajahi studio Azure Machine Learning

*Studio Azure Machine Learning* adalah portal berbasis web tempat Anda dapat mengakses ruang kerja Azure Machine Learning. Anda dapat menggunakan studio Azure Machine Learning untuk mengelola semua aset dan sumber daya dalam ruang kerja Anda. 

1. Buka grup sumber daya bernama **rg-dp100-labs**.
1. Konfirmasikan bahwa grup sumber daya berisi ruang kerja Azure Machine Learning Anda, Application Insights, Key Vault, dan Akun Penyimpanan. 
1. Pilih ruang kerja Azure Machine Learning Anda.
1. Pilih **Luncurkan studio** dari halaman **Gambaran Umum** . Tab lain akan terbuka di browser Anda untuk membuka studio Azure Machine Learning.
1. Tutup pop-up apa pun yang muncul di studio. 
1. Perhatikan berbagai halaman yang ditampilkan di sisi kiri studio. Jika hanya simbol yang terlihat di menu, pilih ikon &#9776; untuk memperluas menu dan menjelajahi nama halaman. 
1. Perhatikan bagian **Pembuat** , yang mencakup **Notebook,** **ML Otomatis**, dan **Perancang**. Ini adalah tiga cara Anda dapat membuat model pembelajaran mesin Anda sendiri dalam studio Azure Machine Learning.
1. Perhatikan bagian **Aset** , yang mencakup **Data**, **Pekerjaan**, dan **Model** antara lain. Aset dikonsumsi atau dibuat saat melatih atau menilai model. Aset digunakan untuk melatih, menyebarkan, dan mengelola model Anda dan dapat dibuat versinya untuk melacak riwayat Anda.
1. Perhatikan bagian **Kelola** , yang mencakup **Komputasi** dan **Datastore** antara lain. Ini adalah sumber daya infrastruktur yang diperlukan untuk melatih atau menyebarkan model pembelajaran mesin. 

## Menulis alur pelatihan

Untuk menjelajahi penggunaan aset dan sumber daya di ruang kerja Azure Machine Learning, mari kita coba dan latih model. 

Cara cepat untuk menulis alur pelatihan model adalah dengan menggunakan **Perancang**.

> [!Note]
> Pop-up mungkin muncul di seluruh untuk memandu Anda melalui studio. Anda dapat menutup dan mengabaikan semua pop-up dan fokus pada instruksi lab ini.

1. Pilih halaman **Perancang** dari menu di sisi kiri studio.
1. Pilih sampel **Regresi - Prediksi Harga Mobil (Dasar).** 
    
    Alur baru muncul. Di bagian atas alur, komponen ditunjukkan untuk memuat **data harga Mobil (mentah)**. Alur memproses data dan melatih model regresi linier untuk memprediksi harga untuk setiap mobil.
1. Pilih **Kirim** di bagian atas halaman. Kesalahan muncul karena Anda belum memilih target komputasi. Alur tidak dapat berjalan tanpa sumber daya komputasi. 

Mari kita buat target komputasi.

## Membuat target komputasi

Untuk menjalankan beban kerja apa pun dalam ruang kerja Azure Machine Learning, Anda memerlukan sumber daya komputasi. Salah satu manfaat Azure Machine Learning adalah kemampuan untuk membuat komputasi berbasis cloud tempat Anda dapat menjalankan eksperimen dan skrip pelatihan dalam skala besar.

1. Di studio Azure Machine Learning, pilih halaman **Komputasi** dari menu di sisi kiri. Ada empat jenis sumber daya komputasi yang dapat Anda gunakan:
    - **Instans komputasi**: Komputer virtual yang dikelola oleh Azure Machine Learning. Ideal untuk pengembangan saat Anda menjelajahi data dan secara berulang bereksperimen dengan model pembelajaran mesin. 
    - **Kluster komputasi**: Kluster mesin virtual scalable untuk pemrosesan kode eksperimen sesuai permintaan. Ideal untuk menjalankan kode produksi atau pekerjaan otomatis.
    - **Kluster inferensi**: Kluster Kubernetes yang digunakan selama inferensi. Ideal untuk penyebaran model real time dalam skala besar.
    - **Komputasi terlampir**: Lampirkan sumber daya komputasi Azure yang ada ke ruang kerja, seperti kluster Virtual Machines atau Azure Databricks.

Untuk melatih model pembelajaran mesin yang Anda tulis dengan Perancang, Anda dapat menggunakan instans komputasi atau kluster komputasi.

2. Pada tab **Instans komputasi**, tambahkan instans komputasi baru dengan pengaturan berikut. 
    - **Nama komputasi**: *masukkan nama unik*
    - **Lokasi**: *Lokasi yang sama secara otomatis dengan ruang kerja Anda*
    - **Jenis komputer virtual**: `CPU`
    - **Ukuran komputer virtual**: `Standard_DS11_v2`
    - **Kuota yang tersedia**: Ini menunjukkan inti khusus yang tersedia.
    - **Tampilkan pengaturan lanjutan**: Perhatikan pengaturan berikut, tetapi jangan pilih:
        - **Aktifkan akses SSH**: `Unselected` *(Anda dapat menggunakan ini untuk mengaktifkan akses langsung ke komputer virtual menggunakan klien SSH)*
        - **Aktifkan jaringan virtual**: `Unselected` *(Anda biasanya akan menggunakan ini di lingkungan perusahaan untuk meningkatkan keamanan jaringan)*
        - **Tetapkan ke pengguna lain**: `Unselected` *(Anda dapat menggunakan ini untuk menetapkan instans komputasi ke ilmuwan data)*
        - **Provisikan dengan skrip penyiapan**: `Unselected` *(Anda dapat menggunakan ini untuk menambahkan skrip yang akan dijalankan pada instans jarak jauh saat dibuat)*

3. Pilih **Buat** dan tunggu instans komputasi dimulai dan statusnya berubah menjadi **Berjalan**.

> **Catatan**: Instans dan kluster komputasi didasarkan pada gambar komputer virtual Azure standar. Untuk latihan ini, gambar *Standar_DS11_v2* disarankan untuk mencapai keseimbangan optimal antara saldo dan performa. Jika langganan Anda memiliki kuota yang tidak menyertakan gambar ini, pilih gambar alternatif; tetapi perlu diingat bahwa gambar yang lebih besar dapat dikenakan biaya yang lebih tinggi dan gambar yang lebih kecil mungkin tidak cukup untuk menyelesaikan tugas. Atau, minta administrator Azure Anda memperpanjang kuota Anda.

## Menjalankan alur pelatihan Anda

Anda telah membuat target komputasi dan sekarang dapat menjalankan sampel alur pelatihan Anda di Perancang.

1. Navigasi ke halaman **Perancang** .
1. Pilih tab **Bawaan klasik** .
1. Pilih draf alur **Regresi - Prediksi Harga Mobil (dasar).**
1. Pilih **Pengaturan** di kanan atas untuk memperluas panel **Pengaturan** .
1. Pilih **Instans komputasi** di bawah **Pilih jenis komputasi**.
1. Pilih instans komputasi yang baru Anda buat di bawah **Pilih kluster komputasi Azure ML**. 
1. Pilih **Kirim** untuk menjalankan alur pelatihan lagi.
1. Pop-up akan muncul untuk menyiapkan pekerjaan alur. Konfigurasikan dan kirim pekerjaan alur baru dengan pengaturan berikut:
    - **Eksperimen**: `Create new`
    - **Nama eksperimen baru**: `train-regression-designer`
    - Pertahankan semua pengaturan default lainnya.

Alur pelatihan sekarang akan dikirimkan ke instans komputasi. Dibutuhkan sekitar 10 menit agar alur selesai. Mari kita jelajahi beberapa halaman lain sementara itu.

## Menggunakan pekerjaan untuk melihat riwayat Anda

Setiap kali Anda menjalankan skrip atau alur di ruang kerja Azure Machine Learning, skrip tersebut dicatat sebagai **pekerjaan**. Pekerjaan memungkinkan Anda melacak beban kerja yang Anda jalankan dan membandingkannya satu sama lain. Pekerjaan milik **eksperimen**, yang memungkinkan Anda mengelompokkan pekerjaan berjalan bersama-sama.

1. Navigasi ke halaman **Pekerjaan** , menggunakan menu di sisi kiri studio Azure Machine Learning.
1. Pilih eksperimen **train-regression-designer** untuk melihat pekerjaannya berjalan. Di sini, Anda akan melihat gambaran umum semua pekerjaan yang merupakan bagian dari eksperimen ini. Jika Anda menjalankan beberapa alur pelatihan, tampilan ini memungkinkan Anda membandingkan alur dan mengidentifikasi alur terbaik.
1. Pilih pekerjaan terakhir dalam eksperimen **train-regression-designer** .
1. Perhatikan bahwa alur pelatihan ditampilkan di mana Anda dapat melihat komponen mana yang berhasil dijalankan atau gagal. Jika pekerjaan masih berjalan, Anda juga dapat mengidentifikasi apa yang saat ini sedang dijalankan.
1. Untuk melihat detail pekerjaan alur, pilih **Ringkasan pekerjaan** di kanan atas untuk memperluas **Ringkasan pekerjaan alur**. 
1. Perhatikan bahwa dalam parameter **Gambaran Umum** , Anda dapat menemukan status pekerjaan, yang membuat alur, ketika dibuat dan berapa lama waktu yang diperlukan untuk menjalankan alur lengkap (antara lain).

    Saat menjalankan skrip atau alur sebagai pekerjaan, Anda dapat menentukan input apa pun dan mendokumenkan output apa pun. Azure Machine Learning juga secara otomatis melacak properti pekerjaan Anda. Dengan menggunakan pekerjaan, Anda dapat dengan mudah melihat riwayat Anda untuk memahami apa yang telah Anda atau kolega Anda lakukan. 
    
    Selama eksperimen, pekerjaan membantu melacak berbagai model yang Anda latih untuk membandingkan dan mengidentifikasi model terbaik. Selama produksi, pekerjaan memungkinkan Anda memeriksa apakah beban kerja otomatis berjalan seperti yang diharapkan.

1. Ketika pekerjaan Anda selesai, Anda juga dapat melihat detail setiap komponen yang dijalankan, termasuk output. Jangan ragu untuk menjelajahi alur untuk memahami bagaimana model dilatih.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure Machine Learning, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Tutup tab studio Azure Machine Learning dan kembali ke portal Azure.
1. Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
1. Pilih grup sumber daya **rg-dp100-labs** .
1. Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**. 
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.