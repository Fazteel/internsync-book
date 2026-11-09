# InternSync Book - Laporan Proyek Akhir

Dokumentasi dan naskah lengkap Buku Laporan Proyek Akhir berbasis LaTeX untuk sistem **InternSync** (Sistem Informasi Manajemen Praktik Kerja Lapangan / PKL SMK PGRI Telagasari).

---

## 👤 Informasi Penulis & Pembimbing

* **Penulis:** Fahmi Andika Setiono
* **NRP:** 3123512901
* **Program Studi:** D3 Teknik Informatika
* **Departemen:** Departemen Teknik Informatika dan Komputer
* **Institusi:** Politeknik Elektronika Negeri Surabaya (PENS)
* **Tahun:** 2026
* **GitHub:** [@Fazteel](https://github.com/Fazteel)
* **Repository:** [internsync-book](https://github.com/Fazteel/internsync-book)

### Dosen Pembimbing
1. **Dosen Pembimbing 1:** Mohammad Robihul Mufid, M.Tr.Kom *(NIP. 199408222020121002)*
2. **Dosen Pembimbing 2:** Yanuar Risah Prayogi, S.Kom., M.Kom *(NIP. 198801062019031009)*

---

## 📂 Struktur Direktori

```text
internsync-book/
├── main.tex              # File induk dokumen LaTeX (preamble, sampul, pengesahan, ToC)
├── bab/                  # Berkas konten laporan per bagian
│   ├── abstrak.tex       # Abstrak Bahasa Indonesia
│   ├── abstract.tex      # Abstract Bahasa Inggris
│   ├── kata_pengantar.tex# Kata Pengantar
│   ├── bab1.tex          # BAB I: Pendahuluan
│   ├── bab2.tex          # BAB II: Tinjauan Pustaka & Landasan Teori
│   ├── bab3.tex          # BAB III: Metodologi & Desain Sistem
│   ├── bab4.tex          # BAB IV: Implementasi & Pengujian
│   ├── bab5.tex          # BAB V: Penutup (Kesimpulan & Saran)
│   ├── pustaka.tex       # Daftar Pustaka (Bibliography)
│   └── lampiran.tex      # Lampiran dokumen
├── gambar/               # Diagram arsitektur, flow, tangkapan layar antarmuka, & logo
├── lampiran/             # Berkas pendukung lampiran tambahan
└── README.md             # Panduan dan informasi repositori
```

---

## 🛠️ Prasyarat Sistem (Prerequisites)

Untuk mengompilasi naskah laporan menjadi dokumen PDF, Anda memerlukan distribusi LaTeX:

* **Windows:** [MiKTeX](https://miktex.org/) atau [TeX Live](https://www.tug.org/texlive/)
* **Linux (Ubuntu/Debian):**
  ```bash
  sudo apt update
  sudo apt install texlive-latex-base texlive-latex-extra texlive-fonts-recommended texlive-lang-other texlive-pictures
  ```
* **macOS:** [MacTeX](https://www.tug.org/mactex/)
* **Alternatif Cloud:** [Overleaf](https://www.overleaf.com/) (tanpa perlu install lokal)

---

## 🚀 Tutorial Setup & Kompilasi

### Cara 1: Menggunakan Terminal / Command Line (Disarankan)

Kompilasi dokumen dilakukan dengan compiler **pdfLaTeX**. Jalankan perintah kompilasi sebanyak **2 sampai 3 kali** agar nomor halaman, daftar isi (ToC), daftar gambar (LoF), daftar tabel (LoT), dan rujukan silang (*cross-reference*) terindeks dengan benar.

```bash
# Clone repositori (jika belum)
git clone https://github.com/Fazteel/internsync-book.git
cd internsync-book

# Kompilasi manual menggunakan pdflatex
pdflatex main.tex
pdflatex main.tex
pdflatex main.tex
```

Atau jika Anda memiliki `latexmk`, cukup jalankan satu perintah:

```bash
latexmk -pdf main.tex
```

File output `main.pdf` akan dihasilkan pada direktori root proyek.

---

### Cara 2: Menggunakan Visual Studio Code

1. Pasang ekstensi **[LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop)** di VS Code.
2. Pastikan distribusi LaTeX (MiKTeX / TeX Live) sudah terpasang dan perintah `pdflatex` terdaftar pada `PATH` sistem Anda.
3. Buka folder `internsync-book` di VS Code.
4. Buka file `main.tex`.
5. Tekan shortcut `Ctrl + Alt + B` (Windows/Linux) atau `Cmd + Option + B` (macOS) untuk menjalankan proses build.
6. Untuk melihat preview dokumen PDF secara langsung di VS Code, tekan `Ctrl + Alt + V`.

---

### Cara 3: Menggunakan TeXstudio / TeXworks

1. Jalankan aplikasi TeXstudio atau TeXworks.
2. Buka berkas `main.tex`.
3. Pastikan compiler default diatur ke **pdfLaTeX**.
4. Tekan tombol **Build & View** (biasanya tombol `F5` di TeXstudio). Lakukan 2-3 kali untuk memutakhirkan seluruh penomoran dan indeks rujukan.

---

### Cara 4: Menggunakan Overleaf (Cloud)

1. Kompres seluruh isi folder proyek `internsync-book` menjadi format `.zip`.
2. Buka [Overleaf](https://www.overleaf.com/) dan login ke akun Anda.
3. Klik **New Project** > **Upload Project** dan unggah file `.zip` tadi.
4. Pada menu Settings (kiri atas):
   * Pastikan **Compiler** diset ke **pdfLaTeX**.
   * Pastikan **Main document** diarahkan ke `main.tex`.
5. Klik **Recompile**.

---

## 📝 Catatan Kompilasi

* **Paket Bahasa:** Dokumen ini menggunakan paket `babel` dengan dukungan bahasa Indonesia (`indonesian`).
* **Font:** Dokumen menggunakan `mathptmx` (setara Times New Roman 12pt) dengan spasi 1.5 sesuai panduan penulisan Proyek Akhir PENS.
* **Marginalia Halaman:** Format margin standar: Kiri 4 cm, Kanan 3 cm, Atas 3 cm, dan Bawah 3 cm.
