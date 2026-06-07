# RT-IoT2022 — Karar Ağacı Tabanlı Ağ Trafiği Saldırı Tespiti

Nesnelerin İnterneti (IoT) ağ trafiğindeki saldırıların tespiti amacıyla **RT-IoT2022** veri seti üzerinde çok sınıflı sınıflandırma analizi.

---

## Veri Seti

| Özellik | Değer |
|---------|-------|
| Kaynak | [UCI ML Repository — ID: 942](https://archive.ics.uci.edu/dataset/942/rt-iot2022) |
| Satır sayısı | ~123.117 |
| Özellik sayısı | 83 |
| Hedef değişken | `Attack_type` |
| Sınıf sayısı | 13 (Normal + 12 saldırı türü) |

### Sınıflar

| Sınıf | Açıklama |
|-------|----------|
| Normal | Normal / meşru ağ trafiği |
| DDoS_HTTP / ICMP / TCP / UDP | Dağıtık hizmet engelleme saldırıları |
| DoS_SYN_Hping | SYN flood tabanlı DoS saldırısı |
| MQTT_Publish | IoT protokolü üzerinden saldırı |
| Recon_OSScan / HostDiscovery / PingSweep | Keşif ve tarama saldırıları |
| Recon_PortScan | Port tarama saldırısı |
| Thing_Speak / Wipro_bulb | IoT cihaz hedefli saldırılar |

---

## Yapılan Analizler

### 1. Keşifsel Veri Analizi (EDA)
- Veri seti boyutu, eksik değer kontrolü
- Sınıf dağılımı (çubuk + pasta grafik)
- Korelasyon ısı haritası (en yüksek varyanslı 20 özellik)

### 2. Veri Ön İşleme
- Gereksiz sütunların kaldırılması (`Unnamed: 0`, `id.orig_p`, `id.resp_p`)
- Label Encoding (hedef değişken `Attack_type`)
- One-Hot Encoding (`proto`, `service` sütunları, `drop_first=True`)

### 3. Model Eğitimi
- Karar Ağacı (Decision Tree, Gini kriteri, `random_state=42`)
- **%80-%20** stratified split (ana analiz)
- **%70-%30** split (akademik karşılaştırma protokolü)

### 4. Değerlendirme Metrikleri
- Accuracy, Precision, Sensitivity (Recall), Specificity, F1-Score
- 5-katlı çapraz doğrulama (Cross-Validation)
- Sınıf bazlı metrik analizi (One-vs-Rest)
- ROC eğrisi ve AUC

### 5. Model İyileştirme
- Budanmış model (`max_depth=10`)
- Sınıf ağırlıklı model (`class_weight='balanced'`)
- GridSearchCV ile hiperparametre optimizasyonu

### 6. Akademik Karşılaştırma
- **Birincil referans:** Ratnakumari et al. (JSIR 2024) — %70-30 protokolünde aynı algoritmayla doğrudan karşılaştırma
- **Destekleyici referans:** Airlangga (MALCOM 2024) — aynı veri setinde farklı algoritmalar

---

## Sonuçlar

### %80-20 Protokolü (Temel Model)

| Metrik | Değer |
|--------|-------|
| Accuracy | ~%99.6 |
| Macro F1 | ~0.9467 |

### %70-30 Protokolü — Model Karşılaştırması

| Metrik | Temel DT | Balanced DT | GridSearch | Ratnakumari et al. (JSIR 2024) |
|--------|----------|-------------|------------|-------------------------------|
| Accuracy | 0.9965 | **0.9971** | 0.9968 | 0.9985 |
| Macro Precision | 0.9398 | **0.9688** | 0.9466 | 0.9400 |
| Macro Recall | 0.9583 | **0.9741** | 0.9516 | 0.9600 |
| Macro F1 | 0.9467 | **0.9700** | 0.9480 | 0.9430 |

> **En iyi model:** `class_weight='balanced'` parametreli Karar Ağacı

---

## Dosyalar

| Dosya | Açıklama |
|-------|----------|
| `RT_IoT2022_Siniflandirma_Analizi.ipynb` | Tüm analiz adımlarını içeren Jupyter Notebook |
| `RT_IoT2022_Rapor.docx` | Proje raporu (orijinal sürüm) |

---

## Çalıştırma

```bash
# Gerekli kütüphaneleri kur
pip install ucimlrepo seaborn ipykernel scikit-learn pandas numpy matplotlib

# Notebook'u aç
jupyter notebook RT_IoT2022_Siniflandirma_Analizi_DUZELTILMIS.ipynb
```

Veri seti notebook içinden **otomatik olarak** UCI Repository'den indirilir, manuel indirme gerekmez.

> Google Colab'da da çalıştırılabilir: notebook'u Colab'a yükle → **Runtime → Run all**

### Gereksinimler

```
pandas
numpy
matplotlib
seaborn
scikit-learn
ucimlrepo
ipykernel
```

---

## Akademik Referanslar

**Birincil Referans:**
> Ratnakumari, Ch. et al. (2024). *Performance Evaluation of Parametric and Non-Parametric Machine Learning Models using Statistical Analysis for RT-IoT2022 Dataset.* Journal of Scientific & Industrial Research (JSIR), Vol. 83, pp. 864–872, Ağustos 2024.

**Destekleyici Referans:**
> Airlangga, G. (2024). *Comparative Analysis of Machine Learning Models for Intrusion Detection in Internet of Things Networks Using the RT-IoT2022 Dataset.* MALCOM: Indonesian Journal of Machine Learning and Computer Science, Vol. 4, No. 2. https://doi.org/10.57152/malcom.v4i2.1304

**Veri Seti:**
> Sharmila, B.S. & Nagapadma, R. (2023). *RT-IoT2022 [Dataset].* UCI Machine Learning Repository. https://doi.org/10.24432/C5P338

---

## Kullanılan Araçlar

- Python 3
- scikit-learn, pandas, numpy, matplotlib, seaborn
- Jupyter Notebook / Google Colab
