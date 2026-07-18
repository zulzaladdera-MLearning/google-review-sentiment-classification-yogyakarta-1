# ZEST-IT: Zero-Shot LLM Labeling and Low-Rank Adaptation for Robust Sentiment Transferability in Indonesia Tourism Reviews

Repository ini berisi kode implementasi tugas akhir mata kuliah **Text Analytics** yang berfokus pada eksperimen *Cross-Cluster Domain Adaptation* (Adaptasi Lintas Domain) untuk analisis sentimen ulasan pariwisata Google Maps di Yogyakarta.

---

## 📌 Peneliti
* **Nama:** Zulza Laddera Aripin
* **Program Studi:** Informatika
* **Universitas:** Universitas Islam Indonesia
---

## 🧠 Permasalahan & Novelty
1. **Rating Bias di Google Maps:** Banyak wisatawan memberikan rating bintang 5 karena tempat wisatanya indah secara umum, tetapi teks ulasannya sebenarnya berisi komplain kritis panjang. Jika model dilatih langsung menggunakan label `stars`, model akan mengalami bias semantik ekstrem.
2. **Domain Adaptation Challenge:** Penelitian ini menguji apakah model bahasa yang hanya dilatih menggunakan ulasan dari **Kluster Alam** (seperti pantai dan gunung) tetap cerdas (*robust*) saat ditransfer langsung untuk memprediksi sentimen pada **Kluster Budaya/Sejarah** (seperti candi, kraton, dan museum) tanpa melakukan pelatihan ulang dari nol.

---

## 📊 Deskripsi Dataset & Preprocessing
* **Dataset Asli:** 3.930 ulasan pariwisata crowdsourced di Yogyakarta hasil scraping Google Maps.
* **Pembersihan Data:** Menghapus data *missing values* dan mengeliminasi kelas Netral (54 ulasan) untuk menangani ketimpangan data ekstrem (*handling imbalance*), menghasilkan total **3.876 data ulasan biner** (Positif vs Negatif).
* **Pseudo-Labeling via LLM:** Menggunakan model gratis raksasa *State-of-the-Art* **`mDeBERTa-v3-base-xnli-multilingual-nli-2mil7`** untuk melabeli ulang sentimen murni berbasis teks kontekstual.
* **Pembagian Kluster Otomatis:** LLM memisahkan domain menjadi:
  * **Kluster Alam (Source Domain):** 3.467 ulasan (Dibagi menjadi 80% Train, 20% Validation).
  * **Kluster Budaya_Sejarah (Target Domain):** 409 ulasan (Diisolasi 100% khusus sebagai *Test Set*).

---

## 🤖 Perbandingan Model & Arsitektur Fine-Tuning
Eksperimen ini membandingkan dua *Pre-trained Language Model* populer dari Hugging Face Hub dengan pendekatan **Parameter-Efficient Fine-Tuning (PEFT) menggunakan LoRA**:
1. **IndoBERT** (`indobenchmark/indobert-base-p1`) - Model Monolingual Bahasa Indonesia.
2. **XLM-RoBERTa** (`xlm-roberta-base`) - Model Multibahasa Internasional.

### ⚙️ Eksplorasi Hyperparameter (Justifikasi SOTA)
* **Learning Rate:** `2e-4` (Nilai ideal agar bobot kecil adapter LoRA dapat belajar dengan cepat).
* **Batch Size:** `32` (Ukuran seimbang guna menjaga efisiensi RAM GPU T4 agar terhindar dari *Out of Memory*).
* **Number of Epochs:** `3` (Cukup untuk mencapai konvergensi optimal tanpa risiko *overfitting*).
* **LoRA Rank (r):** `8` (Melatih kurang dari **0.31% parameter aktif** dari total arsitektur dasar model).

---

## 📈 Ringkasan Hasil Eksperimen (Macro Average)

| Metrik Evaluasi | IndoBERT | XLM-RoBERTa | Model Terbaik |
| :--- | :---: | :---: | :---: |
| **Accuracy** | **92.18%** | **93.15%** | XLM-RoBERTa |
| **Macro F1-Score** | **0.7288** | 0.7125 | **IndoBERT** |
| **Macro Recall** | **0.6970** | 0.6552 | **IndoBERT** |
| **Macro Precision** | **0.7790** | **0.8957** | XLM-RoBERTa |

### 🔍 Kesimpulan Utama
* **IndoBERT memenangkan metrik utama Macro F1-Score (0.7288)**. Model monolingual terbukti jauh lebih sensitif (*Recall* tinggi = **0.6970**) dalam mendeteksi ekspresi kekecewaan atau keluhan lokal karena memiliki basis korpus kosa kata bahasa Indonesia yang kaya.
* **XLM-RoBERTa bersikap lebih konservatif**, namun memiliki tingkat kepastian tebakan paling akurat dengan capaian *Macro Precision* tertinggi (**0.8957**).

---
