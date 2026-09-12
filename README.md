# Sürekli Faiz ve Tarihsel Yatırım Karşılaştırma Simülasyonları

> **Katkı notu.** Bu proje, İTÜ Matematik Mühendisliği MAT232 Diferansiyel
> Denklemler dersi kapsamında  bir ekiple geliştirilmiştir.
> Kod tarafı ekibin yazılım üyeleri tarafından yazılmış, repo  ilk olarak
> [kesasar/surekli_faiz_hesaplama](https://github.com/kesasar/surekli_faiz_hesaplama)
> adresinde yayınlanmıştır.
>
> Projedeki rolüm matematiksel modelleme oldu: `dS/dt = rS + k` denkleminin
> kurulması, başlangıç değer probleminin analitik olarak çözülmesi, hesaplama
> algoritmasının çıkarılması ve yazılan kodun matematiksel doğruluğunun
> kontrol edilmesi.

Bu repository iki farklı finansal simülasyon içerir:

1. **Gerçek tarihsel verilerle altın ve mevduat faizi karşılaştırması**
2. **Diferansiyel denklemlerle sürekli bileşik faiz modeli**

Uygulamalar Streamlit ile geliştirilmiştir ve tarayıcı üzerinden etkileşimli
olarak çalışır.


## 1. Gerçek tarihsel verilerle: altın mı, faiz mi?

Bu uygulama tahmin üretmez; tamamen geçmiş piyasa verileriyle çalışır. Yahoo
Finance üzerinden ons altın (`GC=F`) ve dolar/TL (`TRY=X`) verileri çekilir.

Kullanıcıdan alınan parametreler:

- Başlangıç sermayesi
- Aylık düzenli yatırım tutarı
- Ortalama mevduat faiz oranı

### Hesaplama mantığı

Altın yatırımı gram bazında takip edilir. Gram altının TL karşılığı şu şekilde
hesaplanır:

```
gram altın (TL) = (ons altın × USD/TRY) / 31.1035
```

31.1035 sabiti bir troy onsun gram karşılığıdır. Mevduat tarafında faiz günlük
bileşik olarak işletilir. Aylık düzenli ekleme her iki yatırım aracına da
uygulanır, böylece karşılaştırma aynı nakit akışı üzerinden yapılır.

### Çıktılar

- Toplam yatırılan anapara
- Altın portföyünün güncel değeri
- Faiz portföyünün güncel değeri
- İki yatırımın karşılaştırması
- Zaman içindeki değişimi gösteren grafik

---

## 2. Sürekli bileşik faiz — diferansiyel denklem modeli

### Matematiksel model

Sermayenin değişim hızının mevcut sermaye miktarıyla orantılı olduğu
varsayımından hareketle, birinci mertebeden lineer bir diferansiyel denklem
kurulur:

```
dS/dt = rS + k,    S(0) = S₀
```

| Sembol | Anlamı |
|---|---|
| `t` | Zaman (bağımsız değişken), `t ∈ [0, ∞)` |
| `S(t)` | `t` anındaki toplam sermaye (bağımlı değişken) |
| `S₀` | Başlangıç sermayesi |
| `r` | Sürekli bileşik faiz oranı |
| `k` | Birim zamanda sisteme giren para. `k > 0` yatırma, `k < 0` çekme |

`rS` terimi mevcut sermayenin doğal büyümesini, `k` terimi ise dışarıdan
gelen düzenli nakit akışını temsil eder. `k ≠ 0` olduğunda denklem homojen
olmayan bir yapıya dönüşür.

### Çözüm

Denklem `M(t) = e^(-rt)` integral çarpanıyla çözülür. Başlangıç koşulu
uygulandığında:

```
S(t) = (S₀ + k/r) · e^(rt) − k/r
```

`k = 0` özel durumunda ifade, bilinen sürekli bileşik faiz formülüne indirgenir:

```
S(t) = S₀ · e^(rt)
```

### Kesikli modelle ilişkisi

Geleneksel bankacılıkta faiz belirli aralıklarla işletilir. Faizin yılda `n` kez
işletildiği kesikli model:

```
S(t) = S₀ · (1 + r/n)^(nt)
```

Faiz işletim sıklığı sonsuza giderken bu ifade sürekli modele yakınsar:

```
lim (n→∞) S₀ · (1 + r/n)^(nt) = S₀ · e^(rt)
```

Bu limit Euler sayısının tanımıyla doğrudan ilişkilidir ve kesikli modelin
sürekli modelin bir yaklaşımı olduğunu gösterir.

Aynı matematiksel yapı yalnızca finansta değil, nüfus artışı ve radyoaktif
bozunma gibi süreçlerde de karşımıza çıkar; üstel büyüme ve azalma yasalarının
ortak modelidir.

### Uygulama özellikleri

- Aylık ve yıllık faiz dönemi seçimi
- Düzenli para yatırma ve çekme desteği (`k` parametresi)
- Sermayenin zaman içindeki değişim grafiği
- Kesikli ve sürekli model sonuçlarının karşılaştırılması
- Özet finansal metrikler

---


- Uygulamalar eğitim ve analiz amaçlıdır, yatırım tavsiyesi niteliği taşımaz.
- Sürekli bileşik faiz teorik bir modeldir.
- Tarihsel karşılaştırma geçmiş verilere dayanır ve gelecek performans hakkında
  bilgi vermez.
- Model vergi, komisyon ve enflasyon etkilerini hesaba katmaz.
