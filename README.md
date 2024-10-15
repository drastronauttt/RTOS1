![image](https://github.com/user-attachments/assets/caa7177a-e274-4497-a8ed-a95a65110805)

Setiap pekerjaan atau utas beroperasi secara bersamaan dalam sistem yang menggunakan Sistem Operasi Real-Time (RTOS), tetapi penjadwal RTOS merencanakan eksekusi masing-masing dengan cermat. Karena prioritas menentukan urutan eksekusi saat banyak tugas disiapkan untuk dieksekusi secara bersamaan, prioritas tugas merupakan komponen penting dari manajemen ini.

**Prioritas Tugas Kode dan Hubungannya**
Ada lima tugas dalam kode yang diberikan, masing-masing dengan prioritas berbeda:

defaultTask - Dijadwalkan secara berkala

selectButtonDownload - Prioritas rendah

getADCTask - Prioritas di Atas Normal

dispUARTTask - Tugas dengan prioritas berkala

ctrlLEDTask - Dijadwalkan secara berkala

Saat beberapa tugas tersedia untuk dieksekusi, urutan penyelesaiannya bergantung pada prioritasnya.

### Prinsip Prioritas RTOS

Tugas dengan prioritas lebih tinggi selalu dikerjakan terlebih dahulu oleh RTOS. Tugas dengan prioritas lebih rendah akan dihentikan dan tugas dengan prioritas tinggi akan dikerjakan oleh RTOS jika tugas dengan prioritas lebih tinggi tersedia. Tugas dengan prioritas yang sama akan dieksekusi secara bergantian, biasanya menggunakan teknik *time-slicing* atau tergantung pada apakah tugas tersebut *diblokir* atau sedang menunggu suatu peristiwa.

### Koneksi Tugas dalam Kode

**defaultTask**: Tugas ini memiliki prioritas normal dan tidak memiliki tujuan tertentu; tugas ini kemungkinan berfungsi sebagai tugas pengganti atau tugas utama dengan sedikit tindakan.
**pickButtonTask** (Prioritas rendah): Bertugas mengawasi tombol fisik (Tombol 1 dan Tombol 2) dan kondisinya.

Pekerjaan ini memiliki prioritas rendah, sehingga hanya akan dieksekusi jika tidak ada tugas lain dengan prioritas lebih tinggi yang tersedia. Penundaan berfungsi untuk *menghilangkan pantulan* tombol sehingga pembacaannya akurat.

**getADCTask** (Prioritas lebih tinggi dari biasanya):
- Bertugas membaca data dari konverter analog-ke-digital, atau ADC. Untuk memastikan data sensor yang benar, penjadwal akan menjalankan tugas ini lebih sering daripada tugas dengan prioritas normal atau rendah karena prioritasnya yang lebih tinggi. **dispUARTTask** (Prioritas normal): Mengirimkan status tombol dan data ADC melalui UART. Tugas ini hanya akan berjalan dengan prioritas standar jika tidak ada tugas lain dengan prioritas lebih tinggi yang tersedia.
**ctrlLEDTask** (Prioritas normal): Tugas ini mengelola LED dengan mempertimbangkan nilai ADC dan status tombol. LED akan menyala atau mati berdasarkan apakah tombol disentuh atau jika nilai ADC naik di atas ambang batas yang telah ditentukan sebelumnya. Pekerjaan ini akan bergantian dengan **dispUARTTask** dan **defaultTask** karena memiliki prioritas reguler.

### Hubungan Prioritas

Tugas dengan prioritas lebih rendah seperti **pickButtonTask** dan **dispUARTTask** akan selalu terganggu saat **getADCTask**, yang memiliki prioritas tertinggi, siap dijalankan. Tugas dengan prioritas terendah, **pickButtonTask**, hanya akan dijalankan saat semua tugas lainnya tidak aktif atau menunggu. Penjadwal akan menjalankan tugas dengan prioritas yang sama, seperti **dispUARTTask** dan **ctrlLEDTask**, secara bergantian, tergantung pada teknik *time-slicing* atau spesifikasi masing-masing tugas.

### Skema RTOS untuk Eksekusi

Tugas dengan prioritas tertinggi yang siap dilaksanakan oleh RTOS. Pekerjaan yang sedang berlangsung akan *dihentikan* jika tugas dengan prioritas lebih tinggi tersedia. RTOS akan membagi waktu eksekusi atau menjalankan pekerjaan yang telah menunggu lebih lama (round-robin) jika ada banyak tugas dengan prioritas yang sama. Pekerjaan dengan prioritas lebih rendah, seperti **pickButtonTask**, akan dilaksanakan jika tidak ada tugas dengan prioritas tinggi yang tersedia. RTOS dapat segera menanggapi tugas dengan prioritas tinggi tanpa mengabaikan tugas dengan prioritas lebih rendah tetapi sama pentingnya.
