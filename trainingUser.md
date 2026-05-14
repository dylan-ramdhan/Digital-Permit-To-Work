Berikut adalah dokumen **Training User & Change Management** yang dirancang khusus untuk lingkungan manufaktur. 

Dokumen ini bukan sekadar manual tombol, tapi **strategi adaptasi** karena di pabrik, mengubah kebiasaan pakai kertas ke digital itu sama sulitnya dengan mengubah budaya kerja.

---

# TRAINING & USER ADOPTION PLAN
## Digital Permit To Work (PTW) System

**Prinsip Utama:** "Sistem yang paling aman adalah sistem yang paling mudah dipakai. Kita tidak menghukum user dengan teknologi, kita mempermudah hidup mereka."

---

## 1. STRATEGI CHANGE MANAGEMENT (Menghadapi User Lama)

Transisi dari kertas ke digital sering kali mendapat penolakan (resistensi). Berikut cara mengatasinya:

| Keluhan User Lama (Paper-based) | Jawaban & Pendekatan Digital | Fitur Sistem yang Membantu |
| :--- | :--- | :--- |
| *"Ribet ah, harus login buka laptop."* | *"Bapak tidak perlu cari atasannya lagi untuk tanda tangan. Cukup klik dari HP, atasannya approve dari ruangannya."* | **QR Code & Mobile Web**. Scan QR di dinding langsung buka permit. |
| *"Nanti data hilang kalau server eror."* | *"Kertas bisa kena minyak, sobek, atau hilang di saku baju. Data digital ada backupnya dan tidak bisa dihapus."* | **Soft Delete & Audit Trail**. Semua tercatat permanen. |
| *"Saya tidak paham teknologi."* | *"Ini cuma isi form seperti biasa, tapi di layar. Kalau bisa pakai WhatsApp, pasti bisa pakai ini."* | **Step-by-step Wizard**. UI mirip form biasa, bahasa industri. |
| *"Kalau lampu mati/pihak maintenance tidak bawa HP gimana?"* | *"Sistem tetap jalan di jaringan pabrik. Kalau darurat, ada prosedur Emergency Permit."* | **Emergency Flag**. Tetap ada SOP fallback fisik untuk kasus darurat absolut. |

**Tips Pelaksanaan:**
*   Jangan pernah bilang *"Ini perintah dari Manajemen/IT"*. Bilangkan *"Ini agar pekerjaan Bapak/Ibu lebih aman, dan tidak bisa disalahkan kalau ada insiden karena ada bukti digitalnya"*.

---

## 2. MODUL TRAINING BERBASIS ROLE

Jangan ajarkan semua fitur ke semua orang. Ajarkan hanya apa yang mereka butuhkan.

### Modul A: Requestor (Teknisi, Engineer, Kontraktor)
**Fokus:** Cara mengajukan pekerjaan dengan benar agar cepat di-approve.
1.  Cara Login & Lihat Dashboard pribadi.
2.  **Membuat Permit Baru:** Isi detail pekerjaan, tanggal, area.
3.  **Mengisi JSA/JHA (Paling Kritis):** Cara menambah baris bahaya (Hazard) dan mitigasi. *Tekankan: Tanpa JSA, sistem akan MENOLAK permit untuk di-submit.*
4.  **Upload Lampiran:** Cara foto area kerja dari HP dan upload sebagai bukti.
5.  **Aktivasi & Penutupan:** Cara mengaktifkan permit saat mulai kerja, dan **PENTING:** cara menutup (Close) permit saat selesai.

### Modul B: Approver (Supervisor, Production, EHS)
**Fokus:** Efisiensi review dan keputusan cepat.
1.  Menerima Notifikasi (Suara/Pop-up saat ada permit masuk).
2.  **Membaca Summary Permit:** Cara scroll cepat melihat JSA dan LOTO.
3.  **Aksi Approve/Reject:** Cara approve. *Tekankan:* Wajib isi Comment/Alasan kalau Reject, agar teknisi tahu harus revisi apa.
4.  **Verifikasi LOTO (Khusus EHS/Supervisor):** Cara mencentang verifikasi isolasi energi di lapangan via HP.

### Modul C: Admin & Management
**Fokus:** Monitoring dan pelaporan.
1.  Cara monitor permit aktif yang sudah melewati batas waktu (Expired).
2.  Cara filter laporan berdasarkan departemen/bulan.
3.  Cara export data ke PDF/Excel untuk audit EHS.

---

## 3. DEMO WORKFLOW SCRIPT (Skenario Live Demo)

*Gunakan skenario ini saat training tatap muka. Jangan demo fitur kosong, pakai kasus nyata di pabrik kalian.*

**Skenario:** "Pengelasan (Hot Work) di Area Pump House oleh Teknisi Pak Budi, disetujui oleh Supervisor Pak Agus & EHS Bu Sari."

| Step | Aksi Aktor | Apa yang terjadi di Sistem (Tunjukkan di Layar) |
| :--- | :--- | :--- |
| **1** | Pak Budi (Requestor) | Membuat permit. Isi area: Pump House, Centang LOTO Required. |
| **2** | Pak Budi | Isi JSA: Hazard = Percikan Api, Mitigasi = Fire Blanket, APD = Welding Mask. Upload foto area. |
| **3** | Pak Budi | Klik **Submit**. *Tunjukkan error kalau JSA belum diisi.* Lalu isi JSA, Submit lagi. Status berubah jadi `Pending Supervisor`. |
| **4** | Pak Agus (Supervisor) | HP/PC Pak Agus muncul notifikasi pop-up (SignalR). Buka permit. Baca JSA. Klik **Approve**. Status jadi `Pending Production/EHS`. |
| **5** | Bu Sari (EHS) | Lihat ada permit masuk. Buka halaman detail. Lihat LOTO belum diverifikasi. Klik tombol **Verify** pada item LOTO (Electrical Panel). Klik **Approve**. Status jadi `Approved`. |
| **6** | Pak Budi | Tombol **Start Work (Activate)** muncul. Klik. *Tunjukkan bahwa sistem memblokir kalau LOTO belum diverifikasi oleh EHS.* Status jadi `Active`. |
| **7** | Pak Budi | Cetak PDF berisi QR Code. Tempel di dinding Pump House. |
| **8** | Pak Agus (Lapar) | Lewat Pump House, scan QR Code di dinding pakai HP. Langsung buka halaman detail permit. |
| **9** | Sistem (Hangfire) | Jam 17:00 (waktu selesai kerja). Permit otomatis berubah jadi `Expired` berwarna merah. |
| **10**| Pak Budi | Kembali ke area, bersih-bersih. Buka HP, klik **Close Permit**. Status `Closed`. |

---

## 4. QUICK START USER GUIDE (Panduan Singkat untuk Ditempel)

*Print panduan ini berukuran kartu nama atau A4, tempelkan di meja kontrol / ruang operator.*

### 🚀 PANDUAN CEPAT REQUESTOR (TEKNISI)
1.  **Buat Permit:** Klik `+ Create Permit` → Isi Form → `Save as Draft`.
2.  **Wajib Isi Safety:** Buka Draft → Tambah **JSA** (Bahaya & Pencegahan). Kalau tidak diisi, **TIDAK BISA SUBMIT!**
3.  **Ajukan Approval:** Klik `Submit for Approval`. Tunggu notifikasi.
4.  **Mulai Kerja:** Jika sudah di-approve, buka permit → Klik `Start Work (Activate)`.
5.  **Selesai Kerja:** Jangan lupa! Buka permit → Klik `Close Permit`.
6.  **Lupa Ditutup?** Sistem akan otomatis menghentikan permit (Expired) jika melewati jam selesai yang Anda isi.

### ✅ PANDUAN CEPAT APPROVER (SUPERVISOR / EHS)
1.  **Cek Notifikasi:** Dashboard akan berkedip/bunyi jika ada permit menunggu Anda.
2.  **Review Cepat:** Buka permit → Fokus lihat **JSA** (Apakah mitigasi sudah benar?) dan **LOTO**.
3.  **Keputusan:**
    *   Setuju? → Klik `Approve`.
    *   Tidak Setuju? → Klik `Reject` → **WAJIB tulis alasan di Comment** agar teknisi bisa revisi.
4.  **Verifikasi LOTO (Khusus Safety):** Di lapangan, pastikan isolasi energi sesuai. Buka HP → Klik `Verify Isolation` pada daftar LOTO.

---

## 5. F.A.Q UNTUK USER (Sering Ditanyakan)

**Q: Kalau saya salah klik Submit, bisa dibatalkan?**
A: Segera hubungi Supervisor/EHS. Mereka bisa menekan tombol `Reject` agar permit kembali ke status Draft, dan Anda bisa mengeditnya.

**Q: Apakah saya harus buka komputer di ruang control untuk mengajukan permit?**
A: Tidak. Sistem bisa diakses dari browser HP/Tablet Anda asalkan terhubung ke jaringan WiFi pabrik. Anda bisa foto area kerja dan langsung upload dari HP.

**Q: Apa yang terjadi jika jaringan WiFi di area mati saat saya mau Close Permit?**
A: Sistem akan terus mencatat waktu. Saat koneksi kembali, segera lakukan Close Permit. Sistem akan mencatat kapan permit sebenarnya selesai berdasarkan catatan Anda.

**Q: Kenapa permit saya tidak bisa di-Activate / Start Work?**
A: Ada dua kemungkinan: (1) Belum semua level approval menyetujui, atau (2) Permit Anda butuh LOTO tapi pihak EHS belum melakukan verifikasi "Zero Energy" di sistem. Pastikan EHS sudah ceklist verifikasi.

---

## 6. TIPS SUKSES IMPLEMENTASI

1.  **Pilot Project:** Jangan langsung pakai di seluruh pabrik. Pilih 1 area (misal: Workshop) dan 1 shift dulu selama 2 minggu. Perbaiki feedback, baru roll-out ke pabrik lain.
2.  **Sanksi Disiplin:** Manajemen harus tegas. Setelah bulan pertama, form PTW kertas **tidak lagi diizinkan** dan dianggap tidak sah (illegal) oleh EHS. Harus pakai digital.
3.  **Apresiasi:** Berikan apresiasi/pujian ke departemen yang paling aktif dan disiplin menutup permit di waktunya. Gamifikasi di dashboard sangat membantu.
