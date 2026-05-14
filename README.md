Berikut adalah dokumen **Requirement Finalization** yang siap kamu copy-paste untuk keperluan meeting, validasi SOP, dan tanda tangan user. Dokumen ini merangkum seluruh sistem yang telah kita rancang dan bangun.

---

# REQUIREMENT FINALIZATION DOCUMENT
## Digital Permit To Work (PTW) System

**Dokumen ini merupakan rangkuman kebutuhan sistem untuk keperluan validasi, revisi minor, dan penyesuaian SOP pabrik.**

---

## 1. FLOW BISNIS FINAL (Permit Lifecycle)

Sistem menerapkan *State Machine* yang ketat. Permit tidak bisa melompati tahap approval dan wajib mematuhi aturan safety sebelum eksekusi.

```text
[DRAFT] 
  ↓ (Wajib isi JSA/JHA terlebih dahulu)
[PENDING SUPERVISOR APPROVAL]
  ↓
[PENDING PRODUCTION APPROVAL]
  ↓
[PENDING EHS APPROVAL]
  ↓
[APPROVED]
  ↓ (Wajib verifikasi LOTO jika berisiko & di-approve)
[ACTIVE] 
  ↓ (Pekerjaan selesai / Waktu habis)
[CLOSED] atau [EXPIRED]
```

**Aturan Kritis (Business Rules):**
*   **Rule 1:** Permit **TIDAK BISA** di-submit jika JSA/JHA kosong.
*   **Rule 2:** Permit berstatus Approved **TIDAK BISA** di-aktifkan (Start Work) jika kolom `IsLOTORequired = True` tetapi isolasi energi belum diverifikasi (Zero Energy Check).
*   **Rule 3:** Permit yang melewati waktu `EndDateTime` akan otomatis diubah menjadi **EXPIRED** oleh sistem (Background Job), mencegah pekerjaan ilegal tanpa batas waktu.

---

## 2. ROLE USER & ACCESS CONTROL

Sistem menggunakan Role-Based Access Control (RBAC). Setiap user hanya melihat dan memproses data sesuai jabatannya.

| Role | Deskripsi Umum | Akses Utama di Sistem |
| :--- | :--- | :--- |
| **Admin** | IT / System Admin | Manajemen user, master data (Area, Mesin), konfigurasi sistem. Bisa melihat semua permit. |
| **Requestor** | Teknisi, Engineer, Kontraktor | Membuat permit, mengisi JSA/LOTO, upload lampiran, menutup permit (Close). Hanya melihat permit miliknya. |
| **Supervisor** | Supervisor Teknik/Maintenance | Me-review deskripsi pekerjaan & risiko. Approve/Reject di Level 1. |
| **Production** | Leader/Supervisor Produksi | Memastikan area aman & mesin bisa di-shutdown. Approve/Reject di Level 2. |
| **EHS / Safety** | Officer EHS | Validasi checklist safety, LOTO, dan keseluruhan permit. Approve/Reject di Level 3. Bisa Suspend permit aktif. |
| **Viewer** | Manajemen, Auditor | Dashboard monitoring, melihat report, melihat detail permit (Read-Only). |

---

## 3. APPROVAL HIERARCHY

Workflow approval bersifat **Multilevel & Sequential**. Tidak bisa melompati level.

*   **Level 1: Supervisor** 
    *   *Fokus:* Apakah pekerjaan ini perlu dilakukan? Apakah risikonya sudah teridentifikasi dengan benar?
*   **Level 2: Production** 
    *   *Fokus:* Apakah area produksi siap? Apakah shutdown mesin tidak mengganggu produksi secara kritis?
*   **Level 3: EHS / Safety** 
    *   *Fokus:* Final check. Apakah SOP terpenuhi? Apakah LOTO tervalidasi?

*Catatan untuk Meeting:* Apakah ada tipe permit (misal: Hot Work di Area Kritis) yang butuh approval level 4 (Plant Manager)? *(Tindakan: Diskusi dengan user)*

---

## 4. KEBUTUHAN SAFETY & COMPLIANCE

Sistem dirancang untuk memenuhi standar audit ISO 45001 dan EHS pabrik.

1.  **JSA/JHA Dinamis:** Requestor wajib mengidentifikasi *Activity, Hazard, Risk, Mitigation,* dan *PPE*. Data ini masuk ke tabel khusus, bukan sekadar text field.
2.  **LOTO Verification:** Setiap sumber energi harus dicatat (Energy Source & Isolation Method). Status isolasi harus di-toggle menjadi "Verified" oleh pihak yang berwenang sebelum permit di-aktivasi.
3.  **Audit Trail Tamper-Proof:** Setiap perubahan status (Draft -> Submit -> Approve -> Activate) dicatat secara otomatis di database (`PermitStatusHistories`) berisi: Waktu, User, dan Remark. Data ini tidak bisa diedit manual.
4.  **Soft Delete:** Tidak ada data yang dihapus permanen dari database (hanya di-flag `IsDeleted`), untuk keperluan investigasi insiden.

---

## 5. FORMAT DOKUMEN & OUTPUT

Sistem menghasilkan dokumen fisik/digital standar untuk ditempel di area kerja atau diarsipkan.

**A. PDF Permit (Format Cetak)**
*   **Header:** Logo Perusahaan, Nomor Permit (Barcode/QR), Status (Warna merah/hijau).
*   **Konten:** Detail Pekerjaan, Tabel JSA, Tabel LOTO & Verifikasi.
*   **Footer:** Kolom Approval (Digital Signature/Timestamp: Siapa, Kapan, Comment).

**B. QR Code System**
*   Setiap permit memiliki QR Code unik.
*   QR Code ditempelkan dicetak bersama PDF.
*   Saat di-scan via kamera HP, langsung mengarah ke halaman Detail Permit (Web App) untuk verifikasi cepat oleh Supervisor/EHS di lapangan.

---

## 6. KEBUTUHAN REPORT & DASHBOARD

**A. Realtime Dashboard (untuk Monitoring Harian)**
*   **KPI Cards:** Jumlah Permit Active, Pending Approval, Expired, High Risk.
*   **Live Activity Feed:** Notifikasi pop-up tanpa refresh saat ada permit baru, approval masuk, atau permit closed.
*   **Tabel Permit Terbaru:** Quick access ke permit hari ini.

**B. Reporting & Filtering (untuk Audit & Manajemen)**
*   Filter berdasarkan: Tanggal, Departemen, Area, Tipe Permit, Status.
*   Pencarian global berdasarkan Nomor Permit / Mesin.
*   Export laporan ke PDF dan Excel (Rekomendasi Phase 2).

---

## 7. OPEN ITEMS / PERTANYAAN UNTUK USER

Bagian ini perlu dikonfirmasi saat meeting agar developer bisa menyesuaikan kode:

1.  **Auto-Expire:** Apakah permit yang Expired otomatis men-trigger notifikasi email ke Requestor? Atau cukup di-dashboard saja?
2.  **Emergency Permit:** Apakah ada mekanisme "Emergency Work" di mana permit bisa di-aktivasi dulu (bypass approval sementara), lalu di-approve setelah pekerjaan selesai/darurat teratasi? (Saat ini sistem belum mengizinkan bypass).
3.  **Suspend Permit:** Jika di tengah jalan EHS menemukan pelanggaran, apakah EHS butuh tombol khusus "Suspend" yang langsung memaksa status permit menjadi tidak aktif, terlepas dari persetujuan level sebelumnya?
4.  **Master Data Integration:** Apakah data Mesin dan Area akan diinput manual di sistem PTW, atau harus di-integrasikan (API) langsung dari sistem ERP (SAP/Oracle) yang sudah ada?

---
*Dokumen ini divahdatkan sebagai dasar pengembangan MVP (Minimum Viable Product) Digital PTW System.*
