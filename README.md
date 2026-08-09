# Online Market — tek dosyalık QR menü / single-file QR menu

Veritabanı, backend, build adımı yok. `index.html` dosyasını çift tıklayıp açabilirsin.
No database, no backend, no build step — just open `index.html`.

```
online-market/
├── index.html     ← sayfanın tamamı (stil + veri + mantık)
└── uploads/       ← ürün/kategori resimleri + logo + parallax görselleri
```

## 1. Telefon numarası / Phone number

`index.html` içinde, `CONFIG` bloğu (dosyanın başında, `<script>` etiketinin ilk satırları):

```js
const CONFIG = {
  phone: "+90 555 000 00 00", // ← siparişin SMS ile gideceği numara
  brand: "SmartSacak",
  currency: "₺",
  currencyAfter: true, // 12 ₺  →  true   |   ₺12  →  false
  defaultLanguage: "tr", // tr | en | ru | tk
  defaultTheme: "dark", // dark | light
  logo: "uploads/logo.png",
  heroLeft: "uploads/hero-left.png",
  heroRight: "uploads/hero-right.png",
  bannerAutoplayMs: 3500,
};
```

Müşteri sepeti doldurup **Mesaj ile sipariş ver** düğmesine bastığında, telefonun
kendi **mesajlar (SMS) uygulaması** açılır ve seçilen ürünler + adet + toplam
tutar hazır bir metin olarak bu numaraya yazılır — WhatsApp veya internet
gerekmez, müşteri sadece **Gönder**e basar.

**Ad ve adres zorunludur** — ikisi de doldurulmadan gönder düğmesi çalışmaz;
boş alan varsa uyarı çıkar ve eksik alana odaklanır.

Örnek mesaj:

```
Merhaba! Sipariş vermek istiyorum:

2 x Mercimek Çorbası - 15.98 ₺
1 x Klasik Burger - 16.99 ₺

Toplam: 32.97 ₺
Ad: Kerim
Adres: Atatürk cad. No 5
```

## 2. Ürün eklemek / Adding products

`index.html` içindeki `const MENU = [...]` listesi. Her kategori bir blok, her ürün
tek bir kayıt (sayfada tek bir kart/div olarak çıkar):

```js
{
  "id": "burgers",
  "name": { "en": "Burgers", "tr": "Burgerler", "tk": "Burgerler", "ru": "Бургеры" },
  "image": "burger-burger6.webp",
  "items": [
    { "id": "p1", "name": { "en": "Classic Burger", "tr": "Klasik Burger", "tk": "Klassik burger", "ru": "Классический бургер" }, "price": 16.99, "image": "burger-burger1.webp" }
  ]
}
```

- `id` her ürün için farklı olmalı (sepet bununla çalışır).
- `image` → `uploads/` klasöründeki dosyanın **sadece adı**. Yeni resmi bu klasöre kopyala.
- Resim yoksa `"image": null` yaz; kart tabak simgesiyle görünür.
- Dil eksikse İngilizce (`en`) yazısı kullanılır.

## 3. Banner (kayan afiş) / Auto-scrolling banner

`index.html` içindeki `const BANNERS = [...]` listesi. Kendiliğinden kayar
(`CONFIG.bannerAutoplayMs` süresinde bir), parmakla / mouse ile sürükleyip de
geçilebilir:

```js
{
  image: 'burger-burger6.webp',
  title: { tr: 'Burger Saati', en: 'Burger Hour', ru: 'Час бургеров', tk: 'Burger sagady' },
  text: { tr: 'Her burgerin yanında patates ikram', en: 'Free fries with every burger', ru: '…', tk: '…' },
}
```

Liste boşsa (`BANNERS = []`) banner alanı hiç görünmez.

## 4. Hazır özellikler / What's included

- 20 kategori, 100 ürün (mevcut menü verisinden alındı)
- **Parallax üst alan**: logo + "Hoş geldiniz" yazısı kaydırınca yavaşça kaybolur,
  yan görseller daha yavaş sürüklenir — Electron uygulamasındaki hero ile aynı his
- **Kayan banner**: otomatik kayar, elle de kaydırılabilir, noktalarla gezinilir
- **Kategori şeridi**: yapışkan (sticky) durur; bir kategoriye dokunursan sayfa o
  ürünlere kayar; sayfayı kendin kaydırdıkça hangi kategoride olduğun otomatik
  öne çıkar (scroll-spy) — Electron uygulamasındaki davranışın aynısı
- **Ürüne dokun → büyük resim penceresi** açılır (isim, kategori, fiyat, sepete
  ekle / +/- düğmeleriyle — adet sayısı zaten kartın üzerinde göründüğü için
  pencerede ayrıca tekrar edilmez); karttaki **+** / **-** ise pencereyi açmadan
  direkt sepete ekler/çıkarır
- **Ad ve adres zorunlu**: sipariş göndermeden önce ikisi de doldurulmuş olmalı
- Arama kaldırıldı — sadece kategori + kayan sayfa var
- Dil değiştirici: Türkçe / English / Русский / Türkmençe
- Koyu ↔ açık tema; seçim tarayıcıda saklanır
- Alt barda sadece sağda tek daire = sepet (adet göstergeli)
- Sepet, dil ve tema sayfa yenilenince kaybolmaz (localStorage)
- Sipariş, WhatsApp değil **telefonun kendi SMS uygulaması** ile gönderilir
- PDF menü yok, backend/veritabanı çağrısı yok, internet gerekmez

## 5. Yayına almak / Publishing

GitHub Pages: `index.html` ve `uploads/` klasörünü repo köküne koy, push et.

```bash
cp -R index.html uploads Creatorcreats77.github.io/
cd Creatorcreats77.github.io && git add . && git commit -m "online market" && git push
```

Yerelde denemek için (resimlerin yüklenmesi için sunucu şart değil, ama önerilir):

```bash
python3 -m http.server 8080     # → http://localhost:8080
```
