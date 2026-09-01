# Adult Income Classification 💸

## Proje Amacı
Bu proje, 1994 yılına ait ABD Nüfus Sayımı [Kaggle - Adult Census Income Dataset](https://www.kaggle.com/datasets/priyamchoksi/adult-census-income-dataset) veri setini kullanarak bireylerin yıllık gelirlerinin **50.000$'ın üzerinde olup olmadığını ( >50K vs <=50K )** makine öğrenmesi modelleriyle tahmin etmeyi amaçlamaktadır. 

## Veri Analizi (EDA) ve Gözlemler
*   **Sınıf Dengesizliği (Class Imbalance):** Veri setinde gelir düzeyi 50.000$'ın altında olanlar çoğunlukta (~%76), üstünde olanlar ise azınlıktadır (~%24). Bu nedenle model başarısı değerlendirilirken sadece Accuracy (Doğruluk) değil, F1 Score ve Recall metrikleri de dikkate alınmıştır.
*   **Çarpık Dağılımlar (Skewness):** `capital.gain` ve `capital.loss` değişkenlerinin aşırı sağa çarpık olduğu görülmüş ve buna uygun özellik mühendisliği adımları atılmıştır.
*   **Eksik Veriler:** Veride `NaN` yerine `?` olarak kodlanmış gizli eksik değerler tespit edilmiştir.

## Veri Ön İşleme (Preprocessing) & Özellik Mühendisliği (Feature Engineering)
Veri sızıntısını (data leakage) engellemek amacıyla tüm işlemler `sklearn.pipeline.Pipeline` ve `ColumnTransformer` kullanılarak gerçekleştirilmiştir:
*   **Gereksiz Sütunların Çıkarılması:** Gelirle doğrudan mantıksal bağı olmayan `fnlwgt` ve `education.num` ile tekrara düşen `education` sütunları veri setinden çıkarıldı.
*   **Eksik Veri Doldurma:** `?` değerleri `NaN` yapıldıktan sonra kategorik değişkenlerdeki eksiklikler en sık tekrar eden değer (Mode) ile (`SimpleImputer`) dolduruldu.
*   **Yeni Özellikler (Feature Extraction):** Çarpık dağılan `capital.gain` ve `capital.loss` sütunlarından `has_capital_gain` ve `has_capital_loss` adında Binary (0 ve 1) yeni değişkenler türetildi.
*   **Kategorik Kodlama (Encoding):**
    *   *Düşük Kardinalite* (`workclass`, `marital.status`, `relationship`, `race`, `sex`): One-Hot Encoding
    *   *Yüksek Kardinalite* (`occupation`, `native.country`): Ordinal Encoding

## Modelleme ve Çapraz Doğrulama (Cross-Validation)
Sınıflandırma problemi için **Random Forest** ve **XGBoost** algoritmaları kullanılmıştır.
*   **CV:** Sınıf dağılımını korumak için 5 katlı `StratifiedKFold` (cv=5) kullanıldı.
*   **Hiperparametre Optimizasyonu:** Her iki model için `GridSearchCV` kullanılarak `n_estimators`, `max_depth`, `learning_rate` ve `min_samples_split` gibi parametreler için en iyi kombinasyonlar arandı.

## 🏆 Sonuçlar (Test Seti Üzerinde)
Yapılan testler sonucunda en iyi performansı gösteren algoritma **XGBoost** olmuştur.

*   **Model:** XGBoost (`learning_rate`: 0.2, `max_depth`: 5, `n_estimators`: 100)
*   **Doğruluk (Accuracy):** %87
*   **F1 Score (>50K sınıfı için):** 0.71
*   **Precision (>50K sınıfı için):** 0.78
*   **Recall (>50K sınıfı için):** 0.66

Ağaç tabanlı algoritmalar bu veri seti üzerinde başarıyla uygulanmış ve pipeline kurgusuyla üretime (production) hazır, temiz bir makine öğrenmesi hattı inşa edilmiştir.

## 🚀 Nasıl Çalıştırılır?

Bu proje bir Jupyter Notebook (`.ipynb`) olarak hazırlanmıştır ve veri okuma yolları Kaggle ortamına göre kurgulanmıştır. Projeyi incelemek veya test etmek için aşağıdaki iki yöntemden birini seçebilirsiniz:

**Seçenek 1: Kaggle Üzerinde (Önerilen)**
Notebook'u doğrudan Kaggle ortamına yükleyip, "Adult Census Income Dataset" veri setini projeye dahil ederek hiçbir kütüphane kurulumu yapmadan çalıştırabilirsiniz.

**Seçenek 2: Yerel (Local) Ortamda**
1. Repoyu bilgisayarınıza klonlayın:
   ```bash
   git clone [https://github.com/BayramEnesAtay/adult-income-classification.git](https://github.com/BayramEnesAtay/adult-income-classification.git)
2.Proje dizinine giderek gerekli kütüphaneleri yükleyin:
 ```bash
  pip install -r requirements.txt
  ```
3.Veri setini indirin ve notebook içindeki pd.read_csv() fonksiyonunda yer alan dosya yolunu kendi bilgisayarınızdaki konuma göre güncelleyin.

4.Jupyter Notebook'u başlatarak hücreleri sırasıyla çalıştırın.
