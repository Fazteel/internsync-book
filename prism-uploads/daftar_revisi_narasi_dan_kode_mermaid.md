# DOKUMEN KHUSUS REVISI NARASI & KODE MERMAID

Dokumen ini hanya berisi bagian-bagian yang perlu diperbaiki/ditimpa pada file `PA_Fahmi_Full_Gabungan.docx` agar **100% sinkron** antara narasi, diagram, dan *screenshot* UI (Aplikasi Mobile untuk Siswa & Guru Pembimbing, Logbook Mandiri/*Self-Reporting*, serta Alur Lowongan Berjenjang).

## BAGIAN 1: REVISI NARASI BAB 1, BAB 2, & BAB 3.1

### 1.1 Subbab 1.3 Batasan Masalah (Ganti Poin 2 dan Poin 4)

* **Poin 2:** Aplikasi utama dikembangkan berbasis *responsive web* untuk seluruh aktor pengguna sekolah, sedangkan platform *mobile* berbasis framework Flutter disediakan secara khusus sebagai *frontend* seluler untuk mendukung mobilitas tinggi dari peran **Siswa dan Guru Pembimbing** di lapangan.

* **Poin 4:** Pengelolaan lowongan PKL dilakukan secara berjenjang. Hubin menambahkan data master industri mitra beserta dokumen MoU, kemudian Koordinator PKL mengatur rincian lowongan pada industri tersebut yang meliputi kuota, jenis lowongan (umum atau khusus jurusan), posisi, deskripsi, dan batas waktu pendaftaran. Siswa dapat melihat katalog dan melamar pada lowongan yang tersedia, lalu Koordinator menyeleksi pendaftar sebelum proses pengajuan penempatan PKL dilanjutkan kepada Hubin.

### 1.2 Subbab 2.2 Teori Penunjang (Ganti Paragraf ke-3)

Arsitektur sistem ini mengadopsi pendekatan *client-server* berbasis REST API terpusat dengan pengorganisasian basis data yang rapi, yaitu pemisahan tegas antara tabel master (berawalan `m_`) yang bersifat statis dan tabel transaksi (berawalan `tr_`) yang menampung data dinamis. Fungsionalitas inti sistem mencakup manajemen *multi-role* (Admin Sistem, Waka Hubin, Koordinator PKL, Guru Pembimbing, dan Siswa) beserta akses tautan ber-token bagi Pembimbing Industri, manajemen mitra industri dan katalog lowongan, seleksi dan penempatan PKL, *logbook* harian digital mandiri (*self-reporting*), perizinan presensi terintegrasi berfasilitas *fallback* 24 jam, *monitoring* durasi 3 atau 6 bulan, evaluasi akhir berbasis rubrik baku (bobot 60% industri dan 40% sekolah), pengarsipan surat dinas, serta laporan rekapitulasi terpusat. Seluruh pusat relasi data transaksional sistem ini dikunci pada entitas `tr_internships`.

### 1.3 Subbab 3.1 Deskripsi Solusi (Ganti Poin 1, 2, dan 4)

* **Sinkronisasi Multiplatform Terpusat:** Menyediakan akses *web* responsif (CMS) untuk seluruh aktor pengguna sekolah dan aplikasi *mobile* berbasis Flutter khusus untuk mendongkrak mobilitas Siswa saat mengisi laporan harian di lokasi magang serta Guru Pembimbing saat memantau siswa di lapangan.

* **Katalog Lowongan Industri Terpadu:** Data master industri mitra dan dokumen MoU ditambahkan oleh Hubin, sedangkan pembagian lowongan (meliputi kuota, jenis umum atau khusus jurusan, posisi, dan deskripsi lowongan) diatur oleh Koordinator PKL. Siswa dapat melihat katalog dan melamar lowongan yang sesuai dengan kompetensi keahliannya, kemudian Koordinator menyeleksi pendaftar tersebut sebelum penempatan diajukan secara resmi kepada Hubin.

* **Monitoring Jurnal Harian (Logbook) Digital Mandiri:** Memfasilitasi pencatatan aktivitas kerja harian mandiri siswa (*self-reporting*) secara terstruktur beserta unggahan bukti kegiatan yang terhubung dengan penguncian tanggal izin/sakit otomatis, dipantau secara berkala oleh guru pembimbing serta dilengkapi fitur ekspor rekapitulasi PDF ber-QR Code resmi untuk arsip sekolah.

## BAGIAN 2: REVISI NARASI DIAGRAM BAB 3.2.1 – 3.2.6

### 2.1 Subbab 3.2.1 Rancangan Arsitektur Sistem (Ganti Seluruh Subbab 3.2.1)

Arsitektur sistem pada aplikasi manajemen Praktik Kerja Lapangan (PKL) InternSync dirancang menggunakan pendekatan arsitektur *three-tier* (*Client Tier*, *Application Tier*, dan *Data Tier*) berbasis RESTful API yang memisahkan secara tegas antara lapisan antarmuka pengguna (*presentation layer*), lapisan logika bisnis (*business logic layer*), dan lapisan penyimpanan data (*persistence layer*). Pendekatan ini bertujuan untuk memisahkan tanggung jawab setiap komponen sistem sehingga pengelolaan data, pemrosesan aturan bisnis, serta penyajian antarmuka lintas platform dapat berjalan secara terstruktur dan mudah dikembangkan.

Pada lapisan *Client Tier*, interaksi pengguna dibagi ke dalam tiga kanal akses utama. Pertama, Aplikasi Web CMS yang dibangun menggunakan React.js, TypeScript, dan Tailwind CSS untuk mengakomodasi lima aktor internal sekolah yang memiliki akun, yaitu Admin Sistem, Waka Hubin, Koordinator PKL, Guru Pembimbing, dan Siswa. Kedua, Aplikasi Mobile berbasis Flutter (Dart) yang dirancang khusus bagi Siswa dan Guru Pembimbing guna mendukung mobilitas pelaporan dan pemantauan di lapangan. Ketiga, Antarmuka Web Publik berbasis rute token unik (*public route*) yang memungkinkan Pembimbing Industri memberikan persetujuan izin/sakit serta menginput penilaian rubrik tanpa perlu melakukan login akun.

*(Tempatkan Gambar 3.1 Desain Arsitektur Sistem di sini)*

Berdasarkan Gambar 3.1, proses komunikasi data dimulai ketika aplikasi web maupun *mobile* mengirimkan permintaan melalui protokol HTTPS dalam format JSON yang diamankan dengan *Bearer Token*, sedangkan antarmuka publik industri berkomunikasi menggunakan parameter *Unique URL Token*. Seluruh permintaan diterima oleh *RESTful API Gateway* berbasis framework Laravel pada *Application Tier*. Permintaan dari aktor berakun akan melewati *Middleware Autentikasi & Otorisasi* (*Laravel Sanctum* dan *Spatie RBAC*), sementara akses publik industri akan divalidasi oleh *Token Validator Service*. Selanjutnya, permintaan diproses oleh *Core Business Services* yang mencakup layanan katalog lowongan dan penempatan, layanan *self-reporting logbook* dan perizinan dengan aturan *fallback* 24 jam, layanan evaluasi rubrik (rasio 60:40), serta generator dokumen *DomPDF* dan *QR Code*. Pada lapisan *Data Tier*, sistem memanfaatkan *Eloquent ORM* untuk berkomunikasi dengan basis data relasional MySQL (`internsync-db`) yang memisahkan tabel master (`m_`) dan tabel transaksi (`tr_`), serta terhubung dengan *File Storage Server* untuk menyimpan lampiran bukti *logbook*, surat izin, dokumen MoU, dan berkas PDF resmi ber-QR Code.

### 2.2 Subbab 3.2.2 BPMN (Ganti Paragraf di Bawah Gambar 3.2)

Berdasarkan Gambar 3.2, alur bisnis dimulai dari Waka Hubin yang menginput data master mitra industri serta dokumen MoU. Koordinator PKL kemudian mengatur lowongan PKL pada industri tersebut yang mencakup kuota, jenis lowongan (umum atau khusus jurusan), posisi, dan deskripsi. Siswa melihat katalog lowongan dan mengajukan lamaran pada lowongan yang diminati, lalu Koordinator menyeleksi siswa pendaftar lowongan tersebut. Apabila ditolak, siswa dapat memilih kembali lowongan lain yang tersedia; apabila diterima, Koordinator membuat dokumen pengajuan dan pengiriman PKL disertai penunjukan Guru Pembimbing dan penetapan durasi (3 atau 6 bulan) untuk ditinjau oleh Waka Hubin. Jika disetujui, Hubin menerbitkan Surat Pengantar PKL otomatis berformat PDF dan mengaktifkan status PKL siswa.

Selama masa pelaksanaan PKL, siswa mengisi *logbook* harian secara mandiri atau mengajukan permohonan izin dan sakit. Catatan *logbook* harian dipantau oleh Guru Pembimbing Sekolah dan dapat diekspor menjadi rekapitulasi PDF ber-QR Code, sedangkan permohonan izin/sakit diteruskan kepada Pembimbing Industri melalui tautan token untuk diberikan keputusan (*approve/reject*). Apabila dalam waktu lebih dari 24 jam pihak industri belum merespons (*timeout*), wewenang verifikasi izin dialihkan ke mekanisme *fallback* oleh Guru Pembimbing Sekolah. Selain itu, Guru Pembimbing melaksanakan kunjungan industri berdasarkan jadwal Surat Perintah Perjalanan Dinas (SPPD) yang ditetapkan oleh Koordinator PKL. Pada tahap akhir, Pembimbing Industri menginput nilai pada 11 indikator rubrik industri (bobot 60%) melalui tautan token evaluasi dan Guru Pembimbing menginput nilai pembimbingan sekolah (bobot 40%), sehingga siswa dapat melihat nilai akhir serta mengunduh lembar penilaian berformat PDF ber-QR Code. Adapun peran Admin Sistem berada di luar alur bisnis operasional ini karena difokuskan secara eksklusif pada konfigurasi sistem dan tata kelola data master.

### 2.3 Subbab 3.2.3 Use Case Diagram (Ganti Daftar Aktor di Bawah Gambar 3.3)

Terdapat lima aktor internal sekolah yang memiliki akun login dan satu aktor eksternal mitra DUDI tanpa login, dengan rincian fungsionalitas per modul sebagai berikut:
a. **Admin Sistem:** Mengakses modul autentikasi (login dan manajemen profil), mengelola seluruh data master sistem (meliputi data *user*, *role*, tahun ajaran, jurusan, kelas, siswa, dan guru), serta melakukan *monitoring audit log* dan pengaturan sistem.
b. **Waka Hubin (Hubungan Industri):** Mengakses modul autentikasi, mengelola data mitra industri dan dokumen MoU, melakukan persetujuan pengajuan dan penempatan PKL dari koordinator, serta menerbitkan surat pengantar PKL resmi dan memantau rekap data PKL.
c. **Koordinator PKL:** Mengakses modul autentikasi, mengelola kuota lowongan PKL dan menyeleksi siswa pendaftar, mengelola pengajuan, pengiriman, dan durasi PKL (3 atau 6 bulan), serta mengelola penugasan perjalanan dinas (SPPD) dan rekapitulasi laporan.
d. **Guru Pembimbing:** Mengakses modul autentikasi, melakukan *monitoring* dan mengekspor *logbook* harian siswa ke format PDF ber-QR Code, memverifikasi izin/sakit siswa (secara langsung maupun melalui mekanisme *fallback* > 24 jam), melihat jadwal kunjungan industri dan mengunduh SPPD, serta menginput nilai sekolah (40%) dan merekap evaluasi siswa.
e. **Siswa:** Mengakses modul autentikasi, melihat dan mengajukan lamaran pada katalog lowongan PKL, menginput dan mengelola *logbook* harian secara mandiri, mengajukan permohonan izin/sakit beserta pembagian tautan token konfirmasi, serta melihat dan mengunduh lembar penilaian akhir berformat PDF ber-QR Code.
f. **Pembimbing Industri (Aktor Eksternal Mitra DUDI):** Berinteraksi tanpa login melalui modul publik berbasis tautan token untuk mengonfirmasi permohonan izin/sakit siswa serta menginput rubrik penilaian kinerja industri (bobot 60%).

### 2.4 Subbab 3.2.4.1 Activity Diagram Login (Ganti Paragraf di Bawah Gambar 3.4)

Berdasarkan Gambar 3.4, proses dimulai ketika pengguna membuka halaman login InternSync, lalu sistem menampilkan formulir login. Pengguna memasukkan kredensial berupa Email, NIP, atau NIS beserta kata sandi dan menekan tombol masuk. Sistem melakukan validasi kecocokan kredensial terhadap basis data; apabila tidak cocok, sistem menampilkan pesan kesalahan *"Kredensial Tidak Valid"* dan mengembalikan pengguna ke formulir login. Apabila kredensial cocok, sistem memeriksa status keaktifan akun (`is_active == 1`). Jika akun berstatus tidak aktif, sistem menolak akses dan menampilkan pesan *"Akun Dinonaktifkan"*. Jika akun aktif, sistem men-*generate* token sesi autentikasi (*Laravel Sanctum*), mencatat aktivitas ke dalam tabel *audit log*, mengidentifikasi peran pengguna melalui *Spatie RBAC*, dan mengarahkan pengguna ke halaman dasbor sesuai perannya masing-masing.

### 2.5 Subbab 3.2.4.3 Activity Diagram Pengisian Logbook (Ganti Paragraf di Bawah Gambar 3.6)

Berdasarkan Gambar 3.6, alur dimulai ketika siswa membuka menu *logbook* harian dan menekan tombol tambah atau ubah *logbook* untuk menginput tanggal, rincian aktivitas kerja, serta mengunggah berkas bukti pendukung. Setelah siswa menekan tombol simpan (*submit*), sistem memeriksa apakah tanggal yang dipilih terkunci oleh status permohonan izin/sakit yang telah disetujui (*approved*) atau telah terisi sebelumnya (duplikat). Apabila tanggal terkunci atau duplikat, sistem menolak input dan menampilkan pesan peringatan. Apabila tanggal valid, sistem langsung menyimpan catatan *logbook* secara mandiri ke dalam tabel `tr_logbooks`, menampilkan riwayat *logbook* secara kronologis, serta memperbarui kalkulasi progres hari pelaksanaan PKL siswa. Selanjutnya, Guru Pembimbing dapat memantau seluruh entri aktivitas melalui menu *monitoring logbook*, memfilter berdasarkan nama siswa atau rentang tanggal, meninjau lampiran bukti kegiatan, dan menekan tombol ekspor PDF untuk menghasilkan dokumen rekapitulasi *logbook* resmi ber-QR Code.

### 2.6 Subbab 3.2.5 ERD & 3.2.6 PDM (Ganti Paragraf di Bawah Gambar 3.9 & 3.10)

* **Paragraf di bawah Gambar 3.9 (ERD):**
  Berdasarkan Gambar 3.9, struktur entitas sistem menggunakan `USERS`, `ROLES`, dan `USER_ROLES` untuk mengelola autentikasi dan hak akses multi-peran. Profil pengguna akademik dipetakan ke dalam entitas `STUDENTS` dan `TEACHERS`, yang didukung oleh entitas struktur akademik meliputi `ACADEMIC_YEARS`, `MAJORS`, dan `CLASSROOMS`. Data kemitraan perusahaan disimpan pada entitas `INDUSTRIES`, yang berelasi dengan entitas `VACANCIES` dan `VACANCY_APPLICANTS` untuk memfasilitasi katalog lowongan PKL serta seleksi pendaftar oleh koordinator. Proses pengajuan penempatan kelompok siswa dikelola melalui entitas `INTERNSHIP_APPLICATIONS` dan `APPLICATION_STUDENTS`, yang kemudian menghasilkan penempatan aktif pada entitas pusat `INTERNSHIPS` serta penerbitan dokumen surat resmi pada `LETTERS`. Selama pelaksanaan PKL, pencatatan kegiatan harian disimpan pada `LOGBOOKS`, pengajuan izin/sakit ber-token pada `PERMISSIONS`, penugasan supervisi lapangan guru pada `VISIT_REQUESTS`, dan penilaian akhir berbasis rubrik pada `EVALUATIONS`. Sebagai pendukung tata kelola sistem, entitas `NOTIFICATIONS` menangani pesan pemberitahuan pengguna dan `AUDIT_LOGS` merekam jejak aktivitas krusial demi menjaga akuntabilitas data.

* **Paragraf di bawah Gambar 3.10 (PDM):**
  Perancangan fisik basis data pada Gambar 3.10 memisahkan secara tegas antara sepuluh tabel master (berawalan `m_`) dan sepuluh tabel transaksi (berawalan `tr_`). Tabel master menyimpan referensi data yang relatif statis, mencakup `m_users`, `m_roles`, `m_user_roles`, `m_academic_years`, `m_majors`, `m_classrooms`, `m_students`, `m_teachers`, `m_industries`, serta `m_vacancies` (menyimpan katalog lowongan beserta kuota dan relasi opsional `major_id` untuk lowongan umum maupun khusus jurusan). Sebaliknya, tabel transaksi mengakomodasi aktivitas operasional yang dinamis, meliputi `tr_vacancy_applicants`, `tr_internship_applications`, `tr_application_students`, `tr_internships`, `tr_logbooks`, `tr_permissions` (dilengkapi atribut `approval_token` dan `approved_by_type` untuk mekanisme *fallback*), `tr_visit_requests`, `tr_evaluations` (menyimpan matriks 11 indikator pada kolom JSON `rubric_scores` dengan bobot `industry_score` 60% dan `school_score` 40%), `tr_letters`, `tr_notifications`, serta `tr_audit_logs`. Seluruh relasi operasional pelaksanaan magang berpusat pada tabel `tr_internships` yang telah dinormalisasi hingga *Third Normal Form* (3NF) guna menjaga integritas referensial dan menghindari redundansi data.

## BAGIAN 3: REVISI NARASI MOCKUP UI BAB 3.2.7 (SINKRON DENGAN SCREENSHOT)

### Gambar 3.12 Antarmuka Panel Administrasi dan Manajemen Pengguna (Admin)

Antarmuka ini merupakan pusat kendali manajemen akun yang diperuntukkan khusus bagi peran Admin Sistem pada menu Kelola Pengguna. Halaman ini memisahkan pengelolaan akun ke dalam dua tab terstruktur, yaitu tab Data Siswa dan tab Data Guru & Staff, serta menampilkan informasi *password default* bagi akun pengguna baru. Komponen utama antarmuka berupa *data table* yang menyajikan daftar pengguna beserta atribut NIS/NIP, *Role* (*badge* Siswa/Guru), Status keaktifan akun (*Aktif*), serta kolom Aksi yang memuat tombol pintasan pengaturan email, ubah data (*edit*), dan hapus akun. Untuk mempercepat migrasi data massal pada awal tahun ajaran, antarmuka ini dilengkapi fitur penyaringan berlapis (berdasarkan Jurusan, Kelas, dan Status), kolom pencarian cepat, serta tombol operasional *Download Template*, *Import Excel*, dan *+ Tambah Siswa* di bagian kanan atas.

### Gambar 3.15 Antarmuka Katalog dan Pendaftaran Lowongan PKL (Siswa)

Antarmuka ini menyajikan informasi seluruh peluang penempatan PKL aktif kepada siswa prakerin dalam format kartu interaktif (*grid cards*) yang responsif dan informatif. Setiap kartu lowongan memvisualisasikan kategori jurusan (*Semua Jurusan* atau jurusan spesifik), indikator sisa kuota yang tersedia, judul posisi pekerjaan, nama dan alamat industri mitra, deskripsi tugas, hingga kotak persyaratan khusus (seperti kepatuhan K3LH) beserta informasi batas waktu pendaftaran. Siswa dapat mencari lowongan melalui kolom pencarian di sudut kanan atas dan langsung mengajukan pendaftaran mandiri dengan menekan tombol **Ajukan Lamaran** pada kartu lowongan yang diminati. Sistem menyematkan validasi otomatis di mana tombol lamaran akan dinonaktifkan apabila kuota lowongan telah terisi penuh atau apabila siswa telah berstatus aktif menjalani PKL.

### Gambar 3.17 Antarmuka Manajemen Pengajuan PKL (Koordinator)

Halaman ini merupakan antarmuka manajemen dokumen pengajuan penempatan PKL yang dikelola oleh Koordinator PKL pada submenu Pengajuan PKL. Komponen utama menampilkan *data table* daftar permohonan penempatan yang telah disusun berdasarkan hasil seleksi lowongan, memuat atribut nama Industri & Batch (beserta nomor registrasi dokumen `PKL/APP/...`), nama Guru Pembimbing yang ditugaskan, indikator jumlah siswa dalam *batch* tersebut, periode pengajuan magang, serta *badge* status persetujuan (*Menunggu*, *Disetujui*, atau *Ditolak*). Halaman ini dilengkapi filter status dan periode tanggal, tombol **Detail** pada kolom aksi untuk meninjau rincian nominatif siswa yang diajukan, serta tombol **+ Buat Pengajuan** di sudut kanan atas untuk membuka formulir penyusunan pengajuan penempatan baru untuk diteruskan kepada Waka Hubin.

### Gambar 3.18 Antarmuka Evaluasi dan Persetujuan Pengajuan PKL (Hubin)

Antarmuka ini berfungsi sebagai halaman antrean persetujuan (*approval queue*) dokumen pengajuan penempatan PKL bagi Waka Hubin pada submenu Persetujuan Pengajuan. Halaman ini menyajikan *data table* seluruh pengajuan masuk dari Koordinator PKL yang berstatus *Menunggu*, mencakup informasi perusahaan tujuan beserta nomor surat pengajuan, nama guru pembimbing pendamping, jumlah siswa peserta, serta rentang periode pelaksanaan yang diusulkan. Pada kolom aksi di sisi kanan, tersedia tombol **Proses** yang memungkinkan Waka Hubin membuka rincian dokumen pengajuan untuk memberikan keputusan persetujuan (yang secara otomatis menerbitkan Surat Pengantar PKL berformat PDF) atau penolakan disertai alasan revisi.

### Gambar 3.19 Antarmuka Proses Pengiriman dan Penetapan Durasi PKL (Koordinator)

Halaman ini merupakan tindak lanjut teknis pada submenu Pengiriman PKL setelah dokumen pengajuan awal disetujui oleh Waka Hubin, yang berfokus pada pengelolaan jadwal keberangkatan fisik siswa ke lokasi industri. Antarmuka menyajikan *data table* daftar pengiriman per *batch* industri beserta nama guru pembimbing, jumlah siswa, tanggal keberangkatan definitif, dan *badge* status pengiriman (*Draft* atau *Menunggu*). Koordinator dapat menekan tombol **+ Buat Pengiriman** atau tombol **Edit** pada baris berstatus *Draft* untuk menetapkan tanggal mulai keberangkatan dan mengunci durasi pelaksanaan PKL (3 bulan atau 6 bulan), maupun menekan tombol **Detail** untuk memantau pengiriman yang sedang menunggu *clearance* akhir dari pihak Hubin.

### Gambar 3.20 Antarmuka Final Clearance dan Persetujuan Penempatan PKL (Hubin)

Antarmuka ini merepresentasikan tahap validasi keberangkatan akhir (*final clearance*) oleh Waka Hubin pada submenu Persetujuan Penempatan sebelum siswa resmi berstatus aktif melaksanakan PKL di industri. Halaman ini menampilkan *data table* antrean pengiriman siswa dari Koordinator PKL yang memuat informasi industri tujuan dan nomor dokumen, guru pembimbing yang bertugas, jumlah siswa, tanggal pasti keberangkatan, serta status *Menunggu*. Melalui tombol aksi **Process**, Waka Hubin dapat meninjau rincian final dan mengesahkan keberangkatan tersebut. Pengesahan pada tahap ini menjadi pemicu sistemik (*system trigger*) yang mengubah status siswa menjadi aktif PKL pada tabel `tr_internships` serta membuka akses penuh ke modul operasional harian (*logbook* dan perizinan) pada akun masing-masing siswa.

### Gambar 3.21 Dasbor Antarmuka Siswa Prakerin (Tracking Progres PKL)

Antarmuka dasbor siswa berfungsi sebagai pusat kendali dan pemantauan informasi personal siswa selama menjalani masa PKL. Pada sisi kiri atas, terdapat kartu ringkasan kuantitatif yang menampilkan metrik **Total Logbook Diisi** beserta lencana motivasi pelaksanaan. Di bawahnya, tersaji tabel linimasa **Aktivitas Terakhir** yang merangkum tanggal dan deskripsi kegiatan *logbook* terbaru yang baru saja diinput oleh siswa. Pada sisi kanan, dasbor menampilkan elemen visualisasi utama berupa *gauge chart* (grafik setengah lingkaran progresif) pada panel **Progres Pelaksanaan PKL** yang secara otomatis menghitung persentase waktu magang yang telah ditempuh beserta rincian metrik total Durasi PKL (hari), jumlah Logbook Diisi, dan Sisa Waktu pelaksanaan.

### Gambar 3.23 Antarmuka Pengajuan Izin dan Sakit Siswa

Antarmuka ini dirancang untuk mengakomodasi pencatatan ketidakhadiran siswa prakerin secara tertib dan transparan. Komponen utama menampilkan *data table* riwayat permohonan izin/sakit siswa yang memuat kolom Rentang Tanggal, Tipe permohonan (*Izin* atau *Sakit*), Alasan ketidakhadiran, *badge* Status (*Pending* atau *Approved* beserta keterangan *Persetujuan Industri*), serta kolom **Konfirmasi Industri**. Siswa dapat menekan tombol **+ Ajukan Izin** di kanan atas untuk menginput tanggal, alasan, dan mengunggah bukti sah (surat keterangan dokter atau surat orang tua). Untuk permohonan yang masih berstatus *Pending*, kolom Konfirmasi Industri menyediakan tombol praktis **Kirim WA** dan **Salin Link** agar siswa dapat langsung meneruskan tautan token persetujuan unik kepada Pembimbing Industri tanpa memerlukan login.

### Gambar 3.26 Antarmuka Monitoring Logbook Siswa (Guru Pembimbing)

*(Catatan: Hapus kata "dan Verifikasi Bimbingan" pada judul caption Gambar 3.26 agar konsisten dengan isi narasi self-reporting di bawahnya).*

### Gambar 3.27 Antarmuka Monitoring Kunjungan Industri dan Surat Tugas SPPD (Guru Pembimbing)

Antarmuka pada menu Perjalanan Dinas ini memfasilitasi Guru Pembimbing dalam memantau jadwal penugasan kunjungan supervisi ke lokasi industri mitra yang telah ditetapkan oleh Koordinator PKL. Komponen utama menampilkan *data table* daftar penugasan kunjungan yang memuat nama Industri sasaran, Tanggal pelaksanaan kunjungan, Tujuan Kunjungan (seperti pengantaran Siswa PKL), serta *badge* Status persetujuan (*Disetujui*). Pada kolom aksi, tersedia tombol **Lihat Surat** untuk melihat dan mengunduh dokumen Surat Perintah Perjalanan Dinas (SPPD) resmi berformat PDF sebagai kelengkapan administratif guru saat bertugas ke lapangan.

### Gambar 3.29 Antarmuka Manajemen dan Input Penilaian Sekolah 40% (Guru Pembimbing)

Halaman Lembar Evaluasi Guru Pembimbing ini digunakan oleh guru pembimbing untuk menginput nilai akademik sekolah sekaligus memfinalisasi nilai akhir PKL siswa bimbingannya. Pada sisi kiri, antarmuka menampilkan kartu profil siswa (nama, NIS, kelas, konsentrasi keahlian, dan tempat PKL), kartu **Akumulasi Nilai Akhir PKL** yang mengalkulasi skor gabungan secara *real-time* beserta predikat kelulusan (misalnya `85.2 / 100 - Predikat B Baik (Kompeten)` dan rincian kontribusi poin Industri 60% vs Sekolah 40%), serta kolom input catatan evaluasi sidang. Pada sisi kanan, tersaji formulir **Bagian 1: Aspek Akademik Sekolah (Bobot 40%)** yang memfasilitasi guru dalam memberi skor (melalui tombol skor cepat maupun input angka 0–100) pada komponen *Penulisan & Sistematika Laporan PKL* serta *Presentasi & Penguasaan Materi Sidang PKL*, sedangkan di bawahnya tampil ringkasan **Bagian 2: Nilai Kinerja Industri (Bobot 60%)** yang telah masuk dari pembimbing lapangan.

### Gambar 3.30 Antarmuka Akses Hasil Evaluasi dan Lembar Nilai Akhir Siswa (Published)

Antarmuka pada menu Evaluasi & Nilai ini menyajikan lembar transparansi capaian hasil akhir PKL yang dapat diakses oleh siswa setelah proses penilaian difinalisasi oleh pembimbing industri dan guru pembimbing sekolah (ditandai dengan *badge* `Status: Sudah Dinilai` di kanan atas). Bagian atas halaman menampilkan tiga kartu ringkasan utama, yaitu kartu **Nilai Akhir PKL** beserta predikat kelulusan, kartu **Rata-rata Industri (Bobot 60%)** lengkap dengan nama mentor penilai lapangan, serta kartu **Rata-rata Sekolah (Bobot 40%)** beserta nama guru pembimbing. Di bagian bawah, sistem menyajikan rincian skor transparan untuk kesebelas indikator dalam tiga kolom terpisah: *A. Aspek Sikap & Non-Teknis (Pembimbing Industri)*, *B. Aspek Keterampilan & Teknis (Pembimbing Industri)*, dan *C. Aspek Akademik & Laporan (Guru Pembimbing Sekolah)*, yang selanjutnya dapat diunduh oleh siswa sebagai sertifikat transkrip nilai resmi berformat PDF ber-QR Code untuk menjamin keaslian dokumen.

### Gambar 3.31 (Ubah Judul Caption Saja)

Ubah judul *caption* Gambar 3.31 menjadi:
**Gambar 3.31 Antarmuka Riwayat Penempatan dan Pengelolaan Durasi PKL (Koordinator)**

## BAGIAN 4: REVISI SUBBAB 3.3, 3.4, DAN BAB 4

### 4.1 Subbab 3.3 Rancangan Aplikasi Mobile (Ganti Seluruh Subbab 3.3)

**3.3 RANCANGAN APLIKASI MOBILE (SISWA DAN GURU PEMBIMBING)**
Pengembangan antarmuka tambahan berupa aplikasi *mobile* dirancang secara spesifik untuk memfasilitasi tingkat mobilitas operasional harian yang tinggi di lapangan bagi peran **Siswa** dan **Guru Pembimbing**. Aplikasi ini dibangun menggunakan framework Flutter (Dart), yang memungkinkan kompilasi lintas platform menggunakan satu basis kode yang efisien. Sistem ini tidak dirancang sebagai entitas independen yang berdiri sendiri, melainkan beroperasi sebagai *frontend* seluler yang terintegrasi secara langsung dengan *backend* Laravel melalui arsitektur RESTful API. Pendekatan integrasi semacam ini lazim diterapkan pada pengembangan aplikasi *mobile* berbasis Flutter yang mengandalkan REST API sebagai jembatan komunikasi data dengan *backend*, sebagaimana diterapkan pada penelitian pengembangan aplikasi *reimbursement* berbasis Android \[16\]. Autentikasi berbasis token yang diimplementasikan melalui *Laravel Sanctum* pada sistem ini juga sejalan dengan praktik umum penerapan *Bearer Token* / *JSON Web Token* (JWT) pada arsitektur REST API untuk menjamin keamanan sesi pengguna \[17\].

Pendekatan *client-server* berbasis REST API terpusat ini memastikan konsistensi dan integritas data secara mutlak antara platform *web* dan *mobile*. Fungsionalitas aplikasi *mobile* difokuskan pada kepraktisan pelaporan harian siswa di lokasi magang serta kemudahan pengawasan guru pembimbing di lapangan dengan rincian fitur sebagai berikut:
a. **Autentikasi Terpusat dan Keamanan Sesi**
Sistem login menggunakan kredensial (Email/NIP/NIS dan kata sandi) yang tersinkronisasi penuh dengan versi *web*. Autentikasi memanfaatkan *Bearer Token* berbasis REST (*Laravel Sanctum*) untuk memastikan manajemen sesi yang aman dan mengarahkan tampilan aplikasi sesuai peran pengguna (Siswa atau Guru Pembimbing) tanpa perlu login berulang kali.
b. **Dasbor Ringkas (*Executive Summary*)**
Menyajikan metrik esensial secara instan sesuai peran pengguna. Bagi siswa, dasbor menampilkan persentase progres durasi PKL dan total catatan *logbook* harian yang telah diisi; sedangkan bagi guru pembimbing, dasbor menampilkan total siswa bimbingan aktif, permohonan izin *fallback* yang perlu ditinjau, serta jadwal kunjungan terdekat.
c. **Pencatatan dan Pemantauan Logbook Harian Responsif**
Memudahkan siswa untuk langsung mencatat aktivitas kerja harian secara mandiri (*self-reporting*) dan mengunggah foto dokumentasi kegiatan langsung dari kamera ponsel saat berada di lokasi industri. Di sisi lain, guru pembimbing dapat langsung memantau riwayat aktivitas harian dan memeriksa lampiran bukti kegiatan siswa bimbingannya melalui layar ponsel tanpa harus membuka perangkat komputer.
d. **Pengajuan Izin/Sakit dan Mekanisme *Fallback* Presensi**
Memfasilitasi siswa dalam mengajukan permohonan izin atau sakit beserta unggahan foto surat keterangan dokter dan membagikan tautan token konfirmasi ke pembimbing industri melalui WhatsApp. Bagi guru pembimbing, aplikasi menyediakan akses cepat untuk memverifikasi izin siswa melalui mekanisme *fallback* apabila pihak industri belum merespons dalam waktu 24 jam.
e. **Pemantauan Progres dan Evaluasi Lapangan**
Menampilkan daftar siswa di bawah supervisi guru pembimbing beserta lokasi industri dan indikator penyelesaian durasi PKL, sekaligus memfasilitasi input nilai pembimbingan sekolah (40%) serta peninjauan hasil evaluasi akhir secara fleksibel saat melaksanakan supervisi kunjungan industri.

Desain antarmuka (UI/UX) pada aplikasi *mobile* ini difokuskan pada kemudahan navigasi dan efisiensi interaksi (*user-friendly*), menghindari elemen visual yang berlebihan agar aplikasi tetap ringan dijalankan pada berbagai spesifikasi perangkat ponsel pintar siswa maupun tenaga pendidik.

### 4.2 Subbab 3.4 Rencana Pengujian Sistem (Ganti Poin b dan Poin c)

* **b. Pengujian Fungsionalitas Inti (CRUD Operations):**

  * **Modul Katalog Lowongan & Pengajuan PKL:** Memvalidasi alur pembuatan lowongan oleh Koordinator, pemilihan lowongan oleh Siswa, seleksi pendaftar, hingga pengajuan dan persetujuan akhir oleh pihak Hubin berjalan tanpa galat.

  * **Modul Logbook Harian & Perizinan:** Menguji keandalan form input catatan aktivitas mandiri oleh siswa, kelancaran proses unggah file bukti, penguncian tanggal otomatis saat izin/sakit disetujui, serta ekspor rekapitulasi PDF ber-QR Code oleh guru pembimbing.

  * **Modul Evaluasi Akhir:** Memastikan nilai rubrik industri (60%) dan nilai sekolah (40%) diproses secara matematis dengan benar, termasuk pengujian validasi terhadap input anomali data (seperti karakter non-numerik atau angka negatif).

* **c. Pengujian Integrasi Lintas Platform (Web dan Mobile):**

  * Mengevaluasi presisi sinkronisasi data secara *real-time* melalui REST API terpusat. Skenario utamanya adalah ketika siswa menginput *logbook* harian atau ketika guru pembimbing memverifikasi perizinan *fallback* melalui aplikasi *mobile*, perubahan data tersebut harus langsung tampil seketika pada dasbor *web* tanpa adanya latensi atau ketidakkonsistenan data.

### 4.3 Perbaikan Kecil Bab 4

* **Subbab 4.1 (Kalimat sebelum Tabel 4.1):** Ganti *"Lingkungan pengujian yang digunakan adalah sebagai berikut."* menjadi *"Lingkungan pengujian yang digunakan disajikan pada Tabel 4.1."*

* **Tabel 4.3 (Baris 5):** Tambahkan kata *"ber-QR Code"* sehingga menjadi *"Mengekspor data logbook bimbingan ke format PDF ber-QR Code"*.

* **Tabel 4.7 (Baris 4):** Ganti *"Verifikasi logbook siswa lain oleh Pembimbing berbeda"* menjadi *"Pemantauan logbook siswa lain oleh Pembimbing berbeda"*.

## BAGIAN 5: KODE MERMAID (`.mmd`) YANG SUDAH DISESUAIKAN

### 5.1 `Gambar_3.1_Desain_Arsitektur_Sistem.mmd`

```
flowchart LR
    subgraph Client_Tier ["Client Tier (Presentation Layer)"]
        direction TB
        WebApp["Aplikasi Web CMS<br/>(React.js + TypeScript + Tailwind)<br/>Aktor: Admin, Hubin, Koordinator,<br/>Guru Pembimbing, Siswa"]
        MobileApp["Aplikasi Mobile<br/>(Flutter Dart)<br/>Aktor: Siswa & Guru Pembimbing"]
        PublicWeb["Antarmuka Web Publik Token<br/>(Tanpa Login / Public Route)<br/>Aktor: Pembimbing Industri"]
    end

    subgraph App_Tier ["Application Tier (Business Logic Layer)"]
        direction TB
        API["RESTful API Gateway<br/>(Laravel Framework)"]
        Auth["Middleware Autentikasi & Otorisasi<br/>(Laravel Sanctum + Spatie RBAC)"]
        TokenGuard["Token Validator Service<br/>(Validasi Approval & Evaluation Token)"]
        Services["Core Business Services<br/>- Vacancy & Placement Service<br/>- Self-Reporting Logbook & Permission (24h Fallback)<br/>- Rubric Evaluation Service (60:40)<br/>- DomPDF + QR Code Generator"]
        
        API --> Auth
        API --> TokenGuard
        Auth --> Services
        TokenGuard --> Services
    end

    subgraph Data_Tier ["Data Tier (Persistence Layer)"]
        direction TB
        DB[("Database Relasional<br/>MySQL (internsync-db)<br/>Tabel Master (m_) & Transaksi (tr_)")]
        Storage[("File Storage Server<br/>- Lampiran Logbook & Bukti Izin<br/>- Dokumen MoU & Kunjungan<br/>- PDF Surat & Lembar Nilai Ber-QR")]
    end

    WebApp <-->|"HTTPS / JSON<br/>(Bearer Token)"| API
    MobileApp <-->|"HTTPS / JSON<br/>(Bearer Token)"| API
    PublicWeb <-->|"HTTPS / JSON<br/>(Unique URL Token)"| API

    Services <-->|"Eloquent ORM / Query"| DB
    Services <-->|"Read / Write File"| Storage

```

### 5.2 `Gambar_3.2_BPMN.mmd`

```
flowchart TB
    subgraph Lane_Hubin ["Waka Hubin (Hubungan Industri)"]
        Start((Mulai)) --> H1["Input Data Master Mitra Industri<br/>& Dokumen MoU"]
        H2{"Review & Setujui<br/>Pengajuan & Pengiriman PKL?"}
        H3["Terbitkan Surat Pengantar PKL<br/>(Otomatis PDF) & Aktifkan Status PKL"]
    end

    subgraph Lane_Koordinator ["Koordinator PKL (Kaprog)"]
        K1["Atur Lowongan PKL pada Industri Mitra<br/>(Kuota, Jenis Umum/Khusus, Posisi, Deskripsi)"]
        K2{"Seleksi Siswa<br/>Pendaftar Lowongan?"}
        K3["Buat Pengajuan & Pengiriman PKL,<br/>Assign Guru Pembimbing & Durasi (3/6 Bln)"]
        K4["Tetapkan & Verifikasi Jadwal<br/>Kunjungan Industri (SPPD)"]
    end

    subgraph Lane_Siswa ["Siswa"]
        S1["Lihat Katalog Lowongan PKL<br/>& Ajukan Lamaran"]
        S2["Melaksanakan PKL:<br/>Isi Logbook Harian Mandiri / Ajukan Izin & Sakit"]
        S3["Lihat Nilai Akhir & Unduh<br/>Lembar Penilaian PDF Ber-QR Code"] --> Finish((Selesai))
    end

    subgraph Lane_Pembimbing ["Guru Pembimbing Sekolah"]
        G1["Monitoring Logbook Harian Siswa<br/>& Ekspor Rekap PDF Ber-QR Code"]
        G2["Verifikasi Fallback Izin/Sakit<br/>(Jika Industri > 24 Jam Belum Merespons)"]
        G3["Laksanakan Kunjungan Industri (SPPD) &<br/>Input Nilai Pembimbingan Sekolah (40%)"]
    end

    subgraph Lane_Industri ["Pembimbing Industri (Mitra DUDI)"]
        I1["Buka Tautan Token Izin/Sakit<br/>& Beri Keputusan (Approve/Reject)"]
        I2["Buka Tautan Token Evaluasi &<br/>Input 11 Indikator Rubrik Industri (60%)"]
    end

    H1 --> K1
    K1 --> S1
    S1 --> K2
    K2 -- "Ditolak" --> S1
    K2 -- "Diterima" --> K3
    K3 --> H2
    H2 -- "Ditolak" --> K3
    H2 -- "Disetujui" --> H3
    H3 --> S2
    S2 --> G1
    S2 --> I1
    I1 -. "Timeout > 24 Jam" .-> G2
    K4 --> G3
    G1 --> G3
    I1 --> I2
    G2 --> I2
    I2 --> S3
    G3 --> S3

```

### 5.3 `Gambar_3.3_Use_Case_Diagram.mmd`

```
flowchart LR
    subgraph Actors_Internal ["Aktor Internal Sekolah (Login)"]
        direction TB
        Admin["[Aktor] Admin Sistem"]
        Hubin["[Aktor] Waka Hubin"]
        Koor["[Aktor] Koordinator PKL"]
        Guru["[Aktor] Guru Pembimbing"]
        Siswa["[Aktor] Siswa"]
    end

    subgraph System ["Sistem Manajemen PKL InternSync"]
        direction TB
        
        subgraph Grp_Common ["Autentikasi"]
            UC_Auth(["Login & Manajemen Profil"])
        end

        subgraph Grp_Admin ["Modul Admin Sistem"]
            direction TB
            UC_Master(["Kelola Data Master<br/>(User, Role, Tahun Ajaran, Jurusan, Kelas, Siswa, Guru)"])
            UC_Audit(["Monitoring Audit Log & Pengaturan Sistem"])
        end

        subgraph Grp_Hubin ["Modul Waka Hubin"]
            direction TB
            UC_Ind(["Kelola Data Mitra Industri & MoU"])
            UC_AppRev(["Persetujuan Pengajuan & Penempatan PKL"])
            UC_Letter(["Terbitkan Surat Pengantar PKL & Rekap Data"])
        end

        subgraph Grp_Koor ["Modul Koordinator PKL"]
            direction TB
            UC_Vac(["Kelola Kuota Lowongan PKL & Seleksi Siswa"])
            UC_Place(["Kelola Pengajuan, Pengiriman, & Durasi PKL"])
            UC_VisitRev(["Kelola Penugasan Perjalanan Dinas (SPPD) & Rekapitulasi"])
        end

        subgraph Grp_Guru ["Modul Guru Pembimbing"]
            direction TB
            UC_LogMon(["Monitoring & Ekspor Logbook Siswa (PDF QR)"])
            UC_PermFallback(["Verifikasi Izin/Sakit Siswa (Langsung / Fallback >24 Jam)"])
            UC_Visit(["Lihat Jadwal Kunjungan Industri & Unduh SPPD"])
            UC_EvalSchool(["Input Nilai Sekolah (40%) & Rekap Evaluasi Siswa"])
        end

        subgraph Grp_Siswa ["Modul Siswa"]
            direction TB
            UC_Catalog(["Lihat & Ajukan Lamaran Katalog Lowongan PKL"])
            UC_Logbook(["Input & Kelola Logbook Harian Mandiri"])
            UC_Perm(["Ajukan Izin/Sakit & Bagikan Tautan Token"])
            UC_ViewEval(["Lihat & Unduh Lembar Penilaian Akhir (PDF QR)"])
        end

        subgraph Grp_Public ["Modul Publik (Akses Token Tanpa Login)"]
            direction TB
            UC_TokenPerm(["Konfirmasi Izin/Sakit Siswa via Tautan Token"])
            UC_TokenEval(["Input Rubrik Penilaian Industri (60%) via Tautan Token"])
        end
    end

    subgraph Actors_External ["Aktor Eksternal Mitra DUDI"]
        direction TB
        Industri["[Aktor] Pembimbing Industri<br/>(Tanpa Login / Token Link)"]
    end

    %% Connections
    Admin --> UC_Auth
    Admin --> UC_Master
    Admin --> UC_Audit

    Hubin --> UC_Auth
    Hubin --> UC_Ind
    Hubin --> UC_AppRev
    Hubin --> UC_Letter

    Koor --> UC_Auth
    Koor --> UC_Vac
    Koor --> UC_Place
    Koor --> UC_VisitRev

    Guru --> UC_Auth
    Guru --> UC_LogMon
    Guru --> UC_PermFallback
    Guru --> UC_Visit
    Guru --> UC_EvalSchool

    Siswa --> UC_Auth
    Siswa --> UC_Catalog
    Siswa --> UC_Logbook
    Siswa --> UC_Perm
    Siswa --> UC_ViewEval

    UC_TokenPerm <--- Industri
    UC_TokenEval <--- Industri

```

### 5.4 `Gambar_3.5_Activity_Diagram_Katalog_Lowongan_dan_Penempatan_PKL.mmd`

```
flowchart LR
    subgraph Hubin ["Waka Hubin"]
        Start((Mulai)) --> H1["Input Data Master<br/>Mitra Industri & MoU"]
        H2{"Verifikasi Pengajuan &<br/>Penempatan PKL?"}
        H3["Tolak Pengajuan &<br/>Input Alasan Penolakan"]
        H4["Setujui Penempatan &<br/>Terbitkan Surat Pengantar (PDF)"]
    end

    subgraph Koor ["Koordinator PKL"]
        K1["Buat Lowongan PKL pada Industri<br/>(Kuota, Jenis Umum/Khusus,<br/>Posisi, Deskripsi)"]
        K2{"Seleksi Siswa<br/>Pendaftar?"}
        K3["Buat Pengajuan & Pengiriman:<br/>Pilih Pembimbing & Durasi (3/6 Bln)"]
    end

    subgraph Sistem ["Sistem InternSync"]
        SYS1["Simpan Lowongan (m_vacancies)<br/>& Tampilkan di Katalog"]
        SYS2["Validasi Jurusan & Sisa Kuota,<br/>Simpan Lamaran ('applied')"]
        SYS3["Ubah Status ('rejected')<br/>& Kirim Notifikasi ke Siswa"]
        SYS4["Ubah Status Pendaftar = 'accepted'<br/>& Kurangi Sisa Kuota"]
        SYS5["Simpan Dokumen Pengajuan<br/>(tr_internship_applications)"]
        SYS6["Set PKL Aktif (tr_internships),<br/>Update is_pkl = 1, & Simpan Surat"]
    end

    subgraph Siswa ["Siswa"]
        S1["Buka Menu Katalog Lowongan<br/>(Jika is_pkl == 0)"]
        S2["Pilih Lowongan &<br/>Klik Ajukan Lamaran"]
        S3["Status Siswa Aktif PKL<br/>& Ruang Kerja Logbook Terbuka"] --> Finish((Selesai))
    end

    H1 --> K1
    K1 --> SYS1
    SYS1 --> S1
    S1 --> S2
    S2 --> SYS2
    SYS2 --> K2
    K2 -- "Tolak" --> SYS3
    SYS3 --> S1
    K2 -- "Terima" --> SYS4
    SYS4 --> K3
    K3 --> SYS5
    SYS5 --> H2
    H2 -- "Tolak" --> H3
    H3 --> K3
    H2 -- "Setuju" --> H4
    H4 --> SYS6
    SYS6 --> S3

```

### 5.5 `Gambar_3.6_Activity_Diagram_Pengisian_Logbook.mmd`

```
flowchart TD
    subgraph Siswa ["Siswa"]
        Start((Mulai)) --> S1["Buka Menu Logbook Harian"]
        S2["Klik '+ Tambah Logbook' / 'Ubah':<br/>Input Tanggal, Aktivitas, & Lampiran Bukti"]
        S3["Klik Simpan (Submit) Logbook"]
    end

    subgraph Sistem ["Sistem InternSync"]
        SYS1{"Cek Apakah Tanggal Tersebut<br/>Terkunci Izin/Sakit Approved<br/>atau Duplikat?"}
        SYS2["Tolak Input & Tampilkan Pesan:<br/>'Tanggal Terkunci Izin/Sakit atau Sudah Diisi'"]
        SYS3["Simpan Logbook Secara Mandiri<br/>ke Basis Data (tr_logbooks)"]
        SYS4["Tampilkan Riwayat Logbook Kronologis<br/>& Hitung Progres Hari PKL"]
        SYS5["Generate Dokumen Rekap Logbook PDF<br/>Dilengkapi QR Code Resmi"] --> Finish((Selesai))
    end

    subgraph Guru ["Guru Pembimbing"]
        G1["Buka Menu Monitoring Logbook Siswa"]
        G2["Filter Nama Siswa / Rentang Tanggal<br/>& Tinjau Lampiran Bukti ('Lihat File')"]
        G3["Klik Tombol 'Export PDF'"]
    end

    S1 --> S2
    S2 --> S3
    S3 --> SYS1
    SYS1 -- "Ya (Terkunci / Duplikat)" --> SYS2
    SYS2 --> S1
    SYS1 -- "Tidak (Tanggal Valid)" --> SYS3
    SYS3 --> SYS4
    SYS4 --> G1
    G1 --> G2
    G2 --> G3
    G3 --> SYS5

```

### 5.6 `Gambar_3.10_Physical_Data_Model_PDM.mmd`

```
erDiagram
    m_users {
        bigint id PK
        varchar email
        varchar password
        tinyint is_active
        timestamp created_at
        timestamp updated_at
        timestamp deleted_at
    }

    m_roles {
        bigint id PK
        varchar name
        varchar guard_name
    }

    m_user_roles {
        bigint role_id FK
        varchar model_type
        bigint model_id FK
    }

    m_academic_years {
        bigint id PK
        varchar name
        tinyint is_active
    }

    m_majors {
        bigint id PK
        varchar major_code
        varchar major_name
    }

    m_classrooms {
        bigint id PK
        varchar name
        varchar major_code
    }

    m_students {
        bigint id PK
        bigint user_id FK
        bigint academic_year_id FK
        varchar nis
        varchar name
        varchar jurusan
        varchar kelas
        varchar phone
        text address
        tinyint is_pkl
    }

    m_teachers {
        bigint id PK
        bigint user_id FK
        varchar nip
        varchar name
        varchar phone
        text address
    }

    m_industries {
        bigint id PK
        varchar name
        text address
        varchar hr_name
        varchar hr_email
        varchar hr_phone
        tinyint is_active
        varchar mou_file
    }

    m_vacancies {
        bigint id PK
        bigint industry_id FK
        bigint major_id FK "Nullable (Umum / Khusus Jurusan)"
        int quota
        varchar title
        text description
        text requirements
        enum status "open, closed"
        date deadline
    }

    tr_vacancy_applicants {
        bigint id PK
        bigint vacancy_id FK
        bigint student_id FK
        enum status "applied, accepted, rejected"
        text notes
    }

    tr_internship_applications {
        bigint id PK
        bigint coordinator_id FK
        bigint industry_id FK
        bigint pembimbing_id FK
        date start_date
        date end_date
        int duration_months "3 atau 6"
        enum status "pending, approved, rejected"
        text rejection_reason
    }

    tr_application_students {
        bigint id PK
        bigint application_id FK
        bigint student_id FK
    }

    tr_internships {
        bigint id PK
        bigint application_id FK
        bigint student_id FK
        bigint industry_id FK
        bigint pembimbing_id FK
        date start_date
        date end_date
        tinyint is_extended
        enum status "aktif, selesai, batal"
    }

    tr_logbooks {
        bigint id PK
        bigint internship_id FK
        date date
        text activity
        varchar file_path
        timestamp created_at
        timestamp updated_at
    }

    tr_permissions {
        bigint id PK
        bigint internship_id FK
        date start_date
        date end_date
        enum type "sick, leave"
        text reason
        varchar attachment
        enum status "pending, approved, rejected"
        varchar approval_token
        enum approved_by_type "industry, teacher, admin"
        bigint approved_by_id FK
        text rejection_reason
        timestamp approved_at
    }

    tr_visit_requests {
        bigint id PK
        bigint pembimbing_id FK
        bigint coordinator_id FK
        bigint industry_id FK
        date planned_date
        text purpose
        varchar file_path
        enum status "pending, approved, rejected, completed"
        text feedback
    }

    tr_evaluations {
        bigint id PK
        bigint internship_id FK
        bigint visit_request_id FK
        bigint evaluator_id FK
        date evaluation_date
        enum type "monthly, final, custom"
        varchar industry_token
        json rubric_scores "11 Indikator SMK"
        decimal industry_score "Bobot 60%"
        decimal school_score "Bobot 40%"
        decimal final_score "Nilai Akhir"
        varchar grade "A, B, C, D"
        varchar industry_evaluator_name
        text industry_feedback
        timestamp industry_evaluated_at
    }

    tr_letters {
        bigint id PK
        bigint internship_id FK
        bigint application_id FK
        varchar letter_number
        varchar type
        varchar file_path
    }

    tr_notifications {
        bigint id PK
        bigint user_id FK
        varchar title
        text message
        varchar type
        tinyint is_read
    }

    tr_audit_logs {
        bigint id PK
        bigint user_id FK
        varchar action
        text description
        varchar ip_address
    }

    m_users ||--o{ m_user_roles : "model_id"
    m_roles ||--o{ m_user_roles : "role_id"
    m_users ||--o| m_students : "user_id"
    m_users ||--o| m_teachers : "user_id"
    m_academic_years ||--o{ m_students : "academic_year_id"
    m_majors ||--o{ m_vacancies : "major_id"
    m_industries ||--o{ m_vacancies : "industry_id"
    m_vacancies ||--o{ tr_vacancy_applicants : "vacancy_id"
    m_students ||--o{ tr_vacancy_applicants : "student_id"
    m_teachers ||--o{ tr_internship_applications : "coordinator_id / pembimbing_id"
    m_industries ||--o{ tr_internship_applications : "industry_id"
    tr_internship_applications ||--|{ tr_application_students : "application_id"
    m_students ||--o{ tr_application_students : "student_id"
    tr_internship_applications ||--o{ tr_internships : "application_id"
    m_students ||--o{ tr_internships : "student_id"
    m_industries ||--o{ tr_internships : "industry_id"
    m_teachers ||--o{ tr_internships : "pembimbing_id"
    tr_internships ||--o{ tr_logbooks : "internship_id"
    tr_internships ||--o{ tr_permissions : "internship_id"
    tr_internships ||--o{ tr_evaluations : "internship_id"
    m_teachers ||--o{ tr_visit_requests : "pembimbing_id"
    m_industries ||--o{ tr_visit_requests : "industry_id"
    tr_visit_requests ||--o{ tr_evaluations : "visit_request_id"
    tr_internship_applications ||--o{ tr_letters : "application_id"
    tr_internships ||--o{ tr_letters : "internship_id"
    m_users ||--o{ tr_notifications : "user_id"
    m_users ||--o{ tr_audit_logs : "user_id"

```