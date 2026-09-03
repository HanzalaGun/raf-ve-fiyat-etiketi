**Türkçe** · [English](README.en.md) · [Русский](README.ru.md) · [Deutsch](README.de.md) · [العربية](README.ar.md)

# Raf ve fiyat etiketi oluşturucu

Ürün listesinden rafa asılacak fiyat etiketi hazırlar — **birim fiyatı kendi
hesaplar** ve mevzuatın zorunlu tuttuğu alanlardan eksik olanı söyler. Tek bir
`index.html` dosyası; kurulum, derleme, sunucu yok.

**Canlı sürüm:** [hanzala.com.tr/araclar/raf-ve-fiyat-etiketi](https://hanzala.com.tr/araclar/raf-ve-fiyat-etiketi)

<br>

## Asıl işi süslü etiket değil

Fiyat Etiketi Yönetmeliği **madde 5/2** etikette şunları zorunlu tutuyor:

- malın üretim yeri
- malın ayırıcı özelliği (burada ürün adı)
- tüm vergiler dâhil satış fiyatı
- **birim fiyatı**
- fiyatın uygulanmaya başladığı tarih

Bu araç birim fiyatı sizin yerinize hesaplıyor ve yukarıdakilerden eksik olanı
ürün ürün söylüyor. Etiketi yine de basıyor — eksik bilgiyle baskıyı engellemek
işletmeyi çıkmaza sokar; uyarıp bırakmak doğrusu.

İndirimde **madde 11**: indirim öncesi fiyat, indirimli fiyatla *birlikte* ve
okunabilir gösterilir; o fiyat indirimden önceki **on gün içinde uygulanan en
düşük** fiyattır (çabuk bozulan üründe bir önceki fiyat). Hangi fiyatın on
günün en düşüğü olduğunu bu araç bilemez — onu siz giriyorsunuz. Araç yalnız
ikisini birlikte, okunabilir basıyor.

<br>

## Birim fiyat

Gram ve mililitre önce kilo ve litreye çevriliyor:

```
500 g, 180 ₺  →  360,00 ₺/kg
1 L, 240 ₺    →  240,00 ₺/L
3 adet, 12 ₺  →    4,00 ₺/adet
```

`0,045 ₺/g` teknik olarak doğru ama rafta kimseye bir şey anlatmıyor.

<br>

## Etiket rengi

Beyaz, **sarı** (indirim), **kırmızı** (son fırsat), yeşil, turuncu — ya da
kendi renginiz. Perakendede renk bilgi taşıyor, süs değil.

**Yazı rengi ölçümle seçiliyor, göz kararıyla değil.** Zemin için siyah ve
beyazın WCAG kontrast oranı hesaplanıp yüksek olan kullanılıyor; sarı zeminde
siyah, koyu yeşilde beyaz çıkıyor. Parlaklık eşiğiyle karar vermek orta
tonlarda yanlış tarafı seçiyor.

Bunun ölçülmüş bir garantisi var: **hangi rengi seçerseniz seçin yazı/zemin
kontrastı en az 4,5:1** kalıyor. En kötü durum 4,59 ve `#008900` zemininde
çıkıyor — 256 adımlık tam tarama sınamada koşuyor. Ekranda gösterilen oran
7:1'in altındaysa araç bunu söylüyor; rafta bir metre uzaktan, değişken market
ışığında okunan bir etiket için anlamlı ayrım orada.

İndirim rozeti de zemine uyuyor: perakende kırmızısı mümkünse korunuyor, ama
kırmızı etikette kaybolacağı için orada ters çevriliyor.

Madde 9/1 etiketin **okunabilir** olmasını istiyor — yani kontrast uyum
meselesi, dekorasyon değil.

Renkli zemin **baskıda da korunuyor**: tarayıcılar zemin rengini varsayılan
olarak atıyor, `print-color-adjust: exact` ile açıkça isteniyor. Sarı etiketin
beyaz basılması aracın renk seçimini anlamsız kılardı.

<br>

## Giriş biçimi

Her satıra bir ürün, sütunlar **sekme**, **noktalı virgül** veya **boru** ile:

```
Zeytinyağı 1 L	240	1	L	280	Ayvalık
Beyaz peynir	180	500	g		Trakya
Türk kahvesi 100 g	45	100	g		İzmir
```

`ad · fiyat · miktar · birim · eski fiyat · üretim yeri`
Birim: `kg` `g` `L` `ml` `adet` `m`.

Satırda bu üç ayraçtan hiçbiri yoksa virgüle düşülür. Virgül bilerek son çare:
Türkçe fiyat `145,90` yazılıyor ve virgülü ayraç saymak fiyatı 145, miktarı 90
yapardı.

**Boş fiyat ile sıfır fiyat aynı şey değil.** `Number('')` sıfır döndürür ve
`Number.isFinite(0)` doğrudur — bu eleme olmadan boş bırakılan fiyat sessizce
"0,00 ₺" basılır, eksik alan uyarısı da çıkmazdı.

<br>

## CSV

Liste tarayıcıda duruyor, sunucuya gitmiyor — sekmeyi kapatınca kaybolur. Uzun
listeyi CSV olarak indirin, sonra aynı dosyayı yükleyip kaldığınız yerden devam
edin. Bir şey yazdıysanız sekmeyi kapatırken tarayıcı uyarıyor.

Dosya **BOM ile** başlıyor (`EF BB BF`); onsuz Excel UTF-8'i tanımıyor ve
"Zeytinyağı" → "ZeytinyaÄŸÄ±" oluyor.

**CSV enjeksiyonuna karşı korumalı.** `=`, `+`, `-`, `@` ile başlayan hücreyi
Excel ve LibreOffice formül sayıyor. Dışa aktardığınız dosya başkasına
gidebiliyor; `=cmd|' /c calc'!A1` gibi bir ürün adı karşı tarafta çalışabilir.
Tehlikeli hücreler tek tırnakla metne kilitleniyor, okurken geri alınıyor.

`.xlsx` yerine CSV bilerek: Excel `.csv`yi çift tıkla açıyor, kullanıcı
açısından fark yok. `.xlsx` ise ZIP açıp XML okuyan yabancı bir kütüphane
demek — bu araç "veri tarayıcıdan çıkmıyor" sözü üzerine kurulu.

<br>

## Diller

Türkçe, İngilizce, Rusça, Almanca ve Arapça — sitedeki dillerin aynısı. Arapça
sağdan sola diziliyor; ürün listesi kutusu o düzende de soldan sağa kalıyor,
çünkü liste hesap tablosundan yapıştırılıyor ve sütunlar yapıştırıldığı sırada
durmalı.

Her dilin kendi adresi var: `index.html?dil=en`. Dil seçilmezse tarayıcının
dili kullanılıyor, o da tanınmıyorsa Türkçe.

<br>

## Kullanım

`index.html` dosyasını indirip çift tıklayın. Hiçbir bağımlılığı yok, çevrimdışı
da çalışır.

<br>

## Sınama

`index.html?test=1` adresini açın. Boş/sıfır fiyat ayrımı, birim çevrimleri,
indirim yüzdesi, CSV enjeksiyonu koruması, CSV gidiş-dönüşü, BOM'un bayt
düzeyinde varlığı, kontrast hesabı, her zeminde yazı renginin AA'yı tutması
(256 adımlık tam tarama) ve beş dilin çeviri tablosu sınanıyor. Biri kalırsa sekme
başlığı `HATA:` olur.

<br>

## Lisans

MIT — [LICENSE](LICENSE). İstediğiniz gibi kullanın, değiştirin, satın.

Bu araç [hanzala.com.tr](https://hanzala.com.tr) için yazıldı. Orada
[başka ücretsiz araçlar](https://hanzala.com.tr/araclar) da var.
