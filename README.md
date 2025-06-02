
# 🎯 Gerçek Zamanlı Sözdizimi Vurgulayıcı Raporu(Mini C Dili için)

## 👋 Giriş

Bu projede, C benzeri bir dil için, **hiçbir hazır kütüphane kullanmadan**, tarayıcıda çalışan bir sözdizimi vurgulayıcı geliştirdim.  
Tüm analiz işlemleri (lexical ve syntax) sıfırdan JavaScript ile yazıldı.

---

## 🎥 Demo & Kaynaklar

> 📹 **Demo Videosu:** [YouTube'da İzle](https://youtu.be/dMZyoenbB8w)  


---

## 🛠️ Teknolojiler

- HTML
- CSS
- JavaScript (Vanilla JS)
- ❌ *Hiçbir syntax highlight kütüphanesi kullanılmadı*

---

## 🎯 Amaçlar

- Gerçek zamanlı sözcüksel analiz
- Top-down parser (önden türetim)
- 5+ belirteç türünün renklendirilmesi
- Anında hata bildirimi
- Basit, kullanıcı dostu bir GUI

---

## 🔍 Sözcüksel Analiz (Lexical Analysis)

Durum Diyagramı & Program Uygulaması kullanıldı.
Kod satırları `tokenize()` fonksiyonu ile ayrıştırıldı.  
Tanımlanan token türleri:

| Token Türü     | Açıklama                        |
|----------------|----------------------------------|
| `keyword`      | `int`, `if`, `else`, `while`... |
| `identifier`   | Değişken adları                  |
| `number`       | Tam ve ondalıklı sayılar         |
| `operator`     | `=`, `==`, `+`, `-`              |
| `punctuation`  | `{`, `}`, `;`, `(`, `)`          |
| `string-literal` | `"Merhaba"` gibi ifadeler     |
| `error`        | Tanımsız yapılar, eksik öğeler   |

---

## 🧠 Sözdizimsel Analiz (Syntax Analysis)

Top-down yaklaşımıyla basit bir parser geliştirildi.  
Yapılan kontroller:

- Değişken tanımı (`int x = 5;`)
- `if`, `else if`, `else` blokları
- `while` döngüsü
- `switch-case` yapısı
- `printf()` çağrısı
- Noktalı virgül ve parantez eşleşmeleri

Her yapı kendi kontrol fonksiyonları ile analiz ediliyor.

---

## 🖼️ Grafik Arayüz (GUI)

Arayüzde:

- `<textarea>` içinde kod giriliyor
- `<div>` içinde vurgulama gösteriliyor
- Hatalar `#errorMessage` alanında listeleniyor

Her tuş vuruşunda anında analiz yapılır.

---

## 🧪 Örnek Hatalar

```c
int x = ;           // Değer eksik
if x > 5 {          // Parantez eksik
printf("selam")     // Noktalı virgül eksik
```

Bu hatalar otomatik algılanır ve kullanıcıya açıklayıcı mesajla sunulur.

---



## 📚 Öğrendiklerim

- Token tanımlama ve sınıflandırma
- Dilbilgisel yapıların kontrolü
- Gerçek zamanlı GUI ile etkileşimli analiz
- Hatasız çalışan, kütüphanesiz syntax engine geliştirme

---

## ✅ Sonuç

Bu proje, tarayıcıda çalışan bağımsız bir sözdizimi vurgulayıcı geliştirmek isteyen herkes için öğretici bir örnek teşkil etmektedir.  
Kendi yazdığım analiz motoruyla çalışan bu sistem, öğrenmeye ve test etmeye açıktır.
