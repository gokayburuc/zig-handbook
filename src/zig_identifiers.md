# Zig - Identifiers

## Zig Tanımlayıcıları Nedir?

Zig'de tanımlayıcılar (identifiers), değişkenlere, fonksiyonlara, türlere ve diğer program öğelerine verilen isimlerdir. Zig'in tanımlayıcı kuralları, dilin öngörülebilirlik ve okunabilirlik felsefesini yansıtır: isimler tutarlı bir biçimde yazılmalı ve derleyici tarafından dayatılan katı kurallara uymalıdır.

## Syntax - Sözdizimi

### Zorunlu Kurallar

Zig tanımlayıcıları şu kurallara zorunlu olarak uymalıdır :

- Bir harf (a-z, A-Z) veya alt çizgi (_) ile başlamalıdır.
- Sonraki karakterler harf, rakam (0-9) veya alt çizgi olabilir.
- Anahtar kelimelerle çakışmamalıdır (const, var, fn, return gibi).

```zig
const gecerli_isim = 10;      // Geçerli
const _alt_cizgi = 20;        // Geçerli
const sayi123 = 30;           // Geçerli

// const 123sayi = 40;        // HATA: rakamla başlayamaz
// const const = 50;          // HATA: anahtar kelime
```

## Özel Sözdizimi: @"" ile Tanımlayıcılar

Anahtar kelime olan veya normal kurallara uymayan bir isim gerekliyse (örneğin C kütüphanesinden gelen bir sembol), @"" sözdizimi kullanılır :

```zig
const @"error" = 1;           // Anahtar kelime ama tanımlayıcı olarak kullanılabilir
const @"really red" = 2;      // Boşluk içeren isim
const @"123baslangic" = 3;    // Rakamla başlayan isim
```

Bu sözdizimi özellikle C sembolleriyle bağlantı kurarken veya enum alanları özel karakterler içerdiğinde kullanışlıdır .

## Kullanım Alanları

Tanımlayıcılar programın her yerinde kullanılır:

- Değişkenler ve sabitler: snake_case (ör. kullanici_adi, maksimum_boyut)
- Fonksiyonlar: camelCase (ör. hesaplaToplam, dosyaOku)
- Türler: TitleCase (ör. Ogrenci, RenkKodu)
- Tür döndüren fonksiyonlar: TitleCase (ör. ArrayList, HashMap)
- İsim alanı struct'ları (0 alanlı): snake_case (ör. std.mem, std.json)

Bu kurallar derleyici tarafından zorunlu tutulmaz; zig fmt de isimleri değiştirmez . Ancak Zig standart kütüphanesi ve topluluk projeleri bu kurallara uyar.

## Kullanım Türleri

### Değişken ve Sabitler

```zig
const maksimum_kullanici = 100;   // snake_case
var aktif_kullanici_sayisi: u32 = 0;
const pi_sabiti = 3.14159;
```

### Fonksiyonlar

```zig
fn kullaniciAdiniGetir(id: u32) []const u8 {
    return "Ahmet";
}

fn toplamaYap(a: i32, b: i32) i32 {
    return a + b;
}
```

### Türler

```zig
const Ogrenci = struct {
    ad: []const u8,
    numara: u32,
};

const Renk = enum {
    kirmizi,
    yesil,
    mavi,
};
```

### Kısaltmaların Yazımı

Kısaltmalar normal kelime gibi işlenir; tümü büyük harfle yazılmaz :

```zig
const XmlParser = struct {};      // XML değil, Xml
fn parseXml() void {}             // parseXML değil
const http_istemci = "istemci";   // HTTP_ISTEMCI değil
fn readU32Be() u32 { return 0; }  // BE büyük harf kalır (yerleşik kısaltma)
```

### Yerleşik Kısaltmalar

POSIX gibi yerleşik kısaltmalar geleneksel yazımını korur :

```zig
const ENOENT = error.FileNotFound;  // POSIX geleneği korunur
```

## Örnek Kod

```zig
const std = @import("std");

/// Kullanıcı bilgilerini saklayan yapı.
const Kullanici = struct {
    kullanici_adi: []const u8,   // snake_case alan
    yas: u8,
    
    /// Kullanıcının yetişkin olup olmadığını kontrol eder.
    pub fn yetiskinMi(self: Kullanici) bool {
        return self.yas >= 18;
    }
};

/// Kullanıcı listesini oluşturur ve döndürür.
/// Dönüş tipi tür olduğu için fonksiyon adı TitleCase'dir.
fn KullaniciListesi(comptime n: usize) type {
    return struct {
        elemanlar: [n]Kullanici,
        
        pub fn ilkEleman(self: @This()) Kullanici {
            return self.elemanlar[0];
        }
    };
}

pub fn main() void {
    // Değişken adı snake_case
    const kullanici_sayisi = 2;
    
    // Tür adı TitleCase
    const Liste = KullaniciListesi(kullanici_sayisi);
    
    var liste = Liste{
        .elemanlar = .{
            .{ .kullanici_adi = "Ali", .yas = 25 },
            .{ .kullanici_adi = "Ayşe", .yas = 16 },
        },
    };
    
    // Fonksiyon adı camelCase
    const ilk = liste.ilkEleman();
    
    std.debug.print("{s} yetişkin mi: {}\n", .{ ilk.kullanici_adi, ilk.yetiskinMi() });
    std.debug.print("{s} yetişkin mi: {}\n", .{ liste.elemanlar[1].kullanici_adi, liste.elemanlar[1].yetiskinMi() });
}
```

Çıktı:

```
Ali yetişkin mi: true
Ayşe yetişkin mi: false
```

Bu örnekte KullaniciListesi fonksiyonu bir tür döndürdüğü için TitleCase ile, ilkEleman ise normal bir fonksiyon olduğu için camelCase ile adlandırılmıştır. kullanici_sayisi değişkeni snake_case kuralına uyar .

## En İyi Uygulamalar

- İsim alanı tekrarından kaçının: json.JsonValue yerine json.Value kullanın; tam nitelikli isimde isim alanı zaten mevcuttur .
- Genel belirsiz isimlerden kaçının: Value, Data, Context, Manager, utils gibi kelimeler tipik olarak hiçbir şey ifade etmez .
- Birimleri ve nitelikleri sona ekleyin: gecikme_ms_maks gibi isimler, gecikme_ms_min ile doğal olarak gruplanır .
- Simetrik isimler kullanın: kaynak ve hedef gibi ilgili isimler, kodun karşılaştırılmasını kolaylaştırır .

> Uyarı: Zig'de değişken gölgeleme (shadowing) kesinlikle yasaktır. Bir iç kapsamdaki değişken, dış kapsamdaki aynı isimli değişkeni gölgeleyemez. Bu, derleme zamanı hatasına yol açar .

> Uyarı: zig fmt yalnızca boşlukları ve girintileri düzenler; tanımlayıcı isimlerinin büyük/küçük harf kuralına uyup uymadığını kontrol etmez . İsimlendirme kurallarına uymak geliştiricinin sorumluluğundadır.

## Kütüphane Referansları

- Zig Dil Referansı (Tanımlayıcılar): https://ziglang.org/documentation/master/#Identifiers
- Zig Stil Kılavuzu: https://ziglang.org/documentation/master/#Style-Guide
- Zig Standart Kütüphanesi: https://ziglang.org/documentation/master/std/

## Sonuç

Zig tanımlayıcıları, dilin öngörülebilirlik ve tutarlılık hedeflerini yansıtan net kurallara sahiptir. Zorunlu kurallar (harf/alt çizgi ile başlama, anahtar kelime olmama) derleyici tarafından denetlenir; isimlendirme kuralları (camelCase, snake_case, TitleCase) ise stil kılavuzu tarafından önerilir ve zig fmt tarafından uygulanmaz. @"" sözdizimi, anahtar kelime veya özel karakter içeren isimler için esneklik sağlar. Değişken gölgelemenin yasak olması, Zig'in kapsam güvenliğine verdiği önemi gösterir. Yeni başlayanlar için en önemli kural, türler için TitleCase, fonksiyonlar için camelCase, değişkenler için snake_case kullanmaktır.
