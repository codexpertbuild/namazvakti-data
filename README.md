# Namaz Vakti Verisi (Türkiye)

Türkiye geneli namaz vakitlerini içeren açık veri seti. T.C. Diyanet İşleri
Başkanlığı resmi hesaplamalarına dayanır.

**Kapsam:** 81 il, 869 ilçe, 2026 yılı (365 gün)
**Boyut:** ~58 MB (toplam) / ~67 KB (ilçe başına)
**Lisans:** Veri Diyanet'e aittir; bu repo sadece dağıtım için kullanılır.

## Endpoint Yapısı

### 1. İl/İlçe Index Listesi (~98 KB)

```
https://codexpertbuild.github.io/namazvakti-data/ilceler.json
```

Tüm 81 il ve 869 ilçenin meta listesi. Mobil uygulamaya
gömülmek için tasarlandı (her ilçe için ID, isim, slug).

### 2. Tek İlçe Vakitleri (~67 KB)

```
https://codexpertbuild.github.io/namazvakti-data/vakitler/{ilce_id}.json
```

Bir ilçenin 2026 yılı için 365 günlük namaz vakitleri. ID'leri
`ilceler.json` üzerinden alın.

**Örnekler:**
- Bursa merkez:    `vakitler/9335.json`
- İstanbul merkez: `vakitler/9541.json`
- Ankara merkez:   `vakitler/9206.json`

## JSON Formatı

### `ilceler.json`
```json
{
  "year": 2026,
  "version": "2026.1",
  "country": "Türkiye",
  "province_count": 81,
  "district_count": 869,
  "iller": [
    {
      "name": "Bursa",
      "slug": "bursa",
      "ilceler": [
        { "id": 9335, "name": "Bursa", "slug": "bursa" },
        { "id": 9339, "name": "İnegöl", "slug": "inegol" }
      ]
    }
  ]
}
```

### `vakitler/{id}.json`
```json
{
  "id": 9335,
  "il": "Bursa",
  "ilce": "Bursa",
  "year": 2026,
  "day_count": 365,
  "times": [
    {
      "date": "2026-01-01",
      "imsak":  "06:49",
      "gunes":  "08:19",
      "ogle":   "13:12",
      "ikindi": "15:34",
      "aksam":  "17:55",
      "yatsi":  "19:20"
    }
  ]
}
```

## Mobil Kullanım

```kotlin
// 1. APK'da gömülü ilceler.json ile il/ilçe seçim ekranı
val index = Json.decodeFromString<TurkiyeIndex>(
    context.assets.open("ilceler.json").bufferedReader().readText()
)

// 2. Kullanıcı ilçe seçince sadece o dosyayı indir
val url = "https://codexpertbuild.github.io/namazvakti-data/vakitler/${secilenId}.json"
val vakitler = httpClient.get(url).body<IlceVakitleri>()

// 3. SQLite/Room'a kaydet, bir daha indirme yok
db.vakitlerDao().insertAll(vakitler.times)
```

## Yıllık Güncelleme

Diyanet bir sonraki yılın verisini Aralık'ta yayınlar. Bu repo o zaman
güncellenir (`version` artar). Mobil app `version` mismatch tespit edip
yeni yılı indirir.

## Veri Kaynağı

Tüm veriler [namazvakitleri.diyanet.gov.tr](https://namazvakitleri.diyanet.gov.tr)
adresinden çekilmiştir.

---

**Son güncelleme:** 2026-05-05
