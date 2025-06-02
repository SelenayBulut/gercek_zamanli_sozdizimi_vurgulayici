
# 📘 Gerçek Zamanlı Dilbilgisine Dayalı Sözdizimi Vurgulayıcı – Detaylı Teknik Dökümantasyon

## 📝 1. Dil ve Dilbilgisi Seçimi

### Seçilen Dil:
Bu projede, temel yapıları içeren sadeleştirilmiş bir ** C benzeri bir dil** hedeflenmiştir. Amaç; temel sözcüksel ve sözdizimsel analiz prensiplerini uygulayabilmektir.

### Desteklenen Yapılar:
- Veri türleri: `int`
- Kontrol yapıları: `if`, `else if`, `else`, `while`
- Seçim yapıları: `switch`, `case`, `default`, `break`
- Giriş/çıkış: `printf()`
- Literaller: string, sayılar

### Dilbilgisi Tanımı (CFG Örneği):
```ebnf
<program> → <statement_list>
<statement_list> → <statement> | <statement> <statement_list>
<statement> → <declaration> | <if_stmt> | <while_stmt> | <switch_stmt> | <printf_stmt>
<declaration> → <type> <identifier> [= <value>] ;
<type> → int | float | char
<if_stmt> → if ( <expression> ) { <statement_list> } [else { <statement_list> }]
<while_stmt> → while ( <expression> ) { <statement_list> }
<switch_stmt> → switch ( <identifier> ) { <case_block>* }
<case_block> → case <value> : <statement_list> break ;
<printf_stmt> → printf ( <string_literal> ) ;
```


## 🔍 2. Sözcüksel Analiz (Lexical Analysis)

### Kullanılan Yöntem:
✔️ **Durum Diyagramı & Program Uygulaması**

Durum Diyagramı ve Program Uygulaması, sözcüksel analizde kullanılan bir yöntemdir. Bu yöntemde, karakterlerin türüne göre (örneğin harf, rakam, boşluk, tırnak) farklı durumlara geçiş yapılır. Bu geçişler önce bir durum diyagramı olarak düşünülebilir, ardından bu yapı if-else bloklarıyla kodlanır. Her karakter tek tek kontrol edilerek uygun token’a dönüştürülür. Projedeki tokenize() fonksiyonu da bu yaklaşımı kullanır; karakterleri sırasıyla okur, türlerine göre ayırır ve anlamlı parçalara (token'lara) dönüştürür. Bu sayede hazır bir kütüphane kullanmadan, sıfırdan bir analiz sistemi kurulmuş olur.

Aşağıdaki örnekte bir sayının nasıl ayrıştırıldığı gösterilmektedir:

```javascript
// Eğer karakter bir rakam ise, bu bir sayının başlangıcı olabilir
else if (ch.match(/[0-9]/)) {
  let number = ch;      // Sayıyı tutacak değişken, ilk rakamla başlatılır
  i++;                  // Bir sonraki karaktere geç
  let dotSeen = false;  // Nokta (.) daha önce görüldü mü? Ondalıklı sayı kontrolü için

  // Kodun sonuna gelinmediği sürece ve karakter rakam ya da (eğer nokta daha önce görülmediyse) nokta ise döngü devam eder
  while (i < code.length && (code[i].match(/[0-9]/) || (!dotSeen && code[i] === '.'))) {
    if (code[i] === '.') dotSeen = true; // Eğer karakter nokta ise, artık nokta görüldü olarak işaretle
    number += code[i++]; // Sayıya bu karakteri ekle ve bir sonraki karaktere geç
  }

  tokens.push(number);  // Tam sayı veya ondalıklı sayı olarak toplanan değeri token listesine ekle
}

```

### Tanımlanan Token Türleri:

| Tür             | Açıklama                                  |
|------------------|--------------------------------------------|
| `keyword`        | `if`, `else`, `int`, `while`, `switch`    |
| `identifier`     | Geçerli değişken adları                   |
| `number`         | Tam ve ondalıklı sayılar                  |
| `operator`       | `+`, `-`, `*`, `/`, `=`, `==`, `!=`        |
| `punctuation`    | `(`, `)`, `{`, `}`, `;`, `:`              |
| `string-literal` | `"..."`                                   |
| `error`          | Tanımsız karakter veya yapılar           |

### Tokenlaştırma Akışı:

1. Kod metni `tokenize(code)` fonksiyonu ile karakterlere ayrılır.
2. Her parça `classifyToken(token)` ile sınıflandırılır.
3. Renk kodlama bu sınıflara göre yapılır.

---

## 🧠 3. Sözdizimi Analizi (Syntax Analysis)

### Yöntem:
✔️ **Top-Down Parser (Recursive Descent mantığında)**

op-Down Parser, sözdizimsel analizde kullanılan bir yöntemdir ve dilin başlangıç sembolünden başlayarak kuralları yukarıdan aşağıya doğru uygular. Parser, her yapıyı sırayla kontrol eder ve girdinin dilbilgisine uyup uymadığını anlamaya çalışır. Bu yöntem genellikle if, while, switch gibi yapıların doğrudan tanımlı fonksiyonlarla kontrol edildiği projelerde kullanılır. Projede yer alan checkIfExpression() veya checkWhileExpression() gibi fonksiyonlar, top-down analiz yönteminin uygulamasına örnektir. Örnek:

```javascript
function checkIfExpression(tokens, startIndex) {
  let i = startIndex + 1; // 'if' kelimesinden sonraki token kontrol edilecek

  // 'if' ifadesinden sonra gelen token '(' olmalı
  if (tokens[i] !== "(") {
    return "Hata: 'if' ifadesinden sonra '(' bekleniyor.";
  }

  i++; // Açılış parantezinden sonrasına geç

  let exprTokens = [];     // Parantez içindeki ifadeyi tutacak dizi
  let hasExpression = false; // Parantez içinde gerçekten bir ifade var mı kontrolü
  let openParens = 1;        // Açılan parantez sayısı (iç içe parantezler için)

  // Parantezler kapanana kadar token'ları gez
  while (i < tokens.length && openParens > 0) {
    const token = tokens[i];

    if (token === "(") openParens++;        // Yeni açılan parantez varsa sayaç artır
    else if (token === ")") {
      openParens--;                         // Kapanan parantez varsa sayaç azalt
      if (openParens === 0) break;          // Ana açılış parantezimiz kapanmışsa çık
    } else if (!/^\s+$/.test(token)) {
      hasExpression = true;                 // Boşluk değilse ifade var
      exprTokens.push(token);               // İfade parçasını diziye ekle
    } else {
      exprTokens.push(token);               // Boşluk da olsa, ifadeye dahil et
    }

    i++;
  }
}
```

### Desteklenen Yapılar ve Fonksiyonlar:

| Yapı        | Fonksiyon                     |
|-------------|-------------------------------|
| Değişken tanımı | `parseVariableDeclaration()` |
| if          | `checkIfExpression()`         |
| else if     | `checkElseIfExpression()`     |
| else        | `checkElseExpression()`       |
| while       | `checkWhileExpression()`      |
| switch      | `checkSwitchStructure()`      |
| case        | `checkCaseStatement()`        |
| printf      | `checkPrintfUsage()`          |

Her fonksiyon, kurala aykırı durumlarda detaylı hata mesajları döndürür.

---

## 🎨 4. Vurgulama (Highlighting)

### Kullanılan CSS Sınıfları:

```css
.keyword { color: blue; font-weight: bold; }
.identifier { color: purple; }
.number { color: green; }
.operator { color: black; }
.punctuation { color: pink; }
.string-literal { color: orange; }
.error { background-color: pink; color: red; }
```

### Uygulama:
```javascript
highlighted += `<span class="${ct.type}">${ct.value}</span>`;
```

`highlightCode()` fonksiyonu, bu sınıfları kullanarak token'ları `<span>` etiketlerine sarar.

---

## 🖼️ 5. GUI Uygulaması

### Kullanılan Yapılar:
- `<textarea id="codeInput">` – kullanıcıdan kod girişi
- `<div id="highlightedCode">` – vurgulu kodun gösterimi
- `<div id="errorMessage">` – hata mesajlarının gösterimi


### Kullanılan Teknolojiler:
- **HTML**: Yapı
- **CSS**: Tasarım ve stil
- **JavaScript**: Tüm analiz ve etkileşim



### Gerçek Zamanlı İşleyiş:

```javascript
codeInput.addEventListener("input", () => {
  const code = codeInput.value;
  const { highlighted, errors } = highlightCode(code);
  highlightedCode.innerHTML = highlighted;
  errorMessage.textContent = errors.join(" | ");
});
```
1. Kullanıcı `#codeInput` alanına yazdığında `input` olayı tetiklenir
2. Kod tokenize edilir ve parse edilir
3. Renkli çıktı `#highlightedCode` alanına basılır
4. Hatalar varsa `#errorMessage` alanında gösterilir

Kullanıcı yazdıkça analiz ve vurgulama **anında** güncellenir.

---

## 🧪 6. Hata Senaryoları

```c
int x = ;           // Eksik değer
if x > 5 {          // '(' eksik
printf("Merhaba")   // ';' eksik
switch (x {         // ')' eksik
```

Tespit edilen hatalar hem renklendirme ile hem de yazılı mesajla gösterilir.

---

## ✅ Özet

Bu proje; HTML, CSS ve JavaScript kullanarak, kütüphane kullanmadan geliştirilen bir **gerçek zamanlı sözdizimi vurgulayıcıdır**.  
Lexical ve syntax analiz birleştirilmiş, GUI ile etkileşimli hale getirilmiştir.
