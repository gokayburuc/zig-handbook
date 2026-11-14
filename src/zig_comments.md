# Zig - Comments

## Zig Yorumları Nedir?

Zig'de yorumlar, derleyici tarafından yok sayılan ve yalnızca kod okuyan geliştiricilere açıklama sağlayan metin parçalarıdır. Zig'in yorum sistemi bilinçli olarak basit tutulmuştur: çok satırlı yorum bloğu (/* */) yoktur, bu sayede her kod satırı bağımsız olarak tokenize edilebilir .

## Syntax - Sözdizimi

Zig üç tür yorum destekler:

Tür | Sözdizimi  |Amaç |
| --- | --- | --- | 
| Normal yorum |  // |Kod içi açıklamalar, satır sonuna kadar geçerli
| Dokümantasyon yorumu |  /// |  Fonksiyon, struct, alan gibi bildirimlerin dokümantasyonu
| Üst düzey dokümantasyon | //! |Paket veya modül düzeyinde genel açıklama

```zig
// Bu normal bir yorumdur, satır sonuna kadar geçerlidir
const x = 10; // Satır sonu yorumu da kullanılabilir

/// Bu fonksiyon iki sayıyı toplar.
/// Birden fazla /// satırı birleşerek çok satırlı dokümantasyon oluşturur.
pub fn add(a: i32, b: i32) i32 {
    return a + b;
}
```

Dokümantasyon yorumları (///), normal yorumlardan (//) farklıdır ve zig build-lib -femit-docs komutuyla üretilen HTML dokümantasyonunda görünür . //// (dört slash) dokümantasyon yorumu sayılmaz.

## Kullanım Alanları

· Normal yorumlar (//): Kodun nasıl çalıştığını açıklamak, geçici kod devre dışı bırakmak, TODO notları eklemek için kullanılır .
· Dokümantasyon yorumları (///): Genel API'ler, fonksiyonlar, struct alanları ve enum değerleri için kullanılır; otomatik dokümantasyon üretiminde görünür .
· Üst düzey dokümantasyon (//!): Bir modülün veya dosyanın tamamını tanımlamak için dosyanın başında kullanılır .

## Kullanım Türleri

### Normal Yorum

```zig
const std = @import("std");

pub fn main() void {
    // Konsola mesaj yazdır
    std.debug.print("Merhaba\n", .{});
    
    // const yasak = true; // Bu satır çalıştırılmaz
}
```

### Dokümantasyon Yorumu

```zig
/// Zaman damgasını saniye ve nanosaniye olarak saklayan yapı.
const Timestamp = struct {
    /// Epoch'tan itibaren saniye sayısı.
    seconds: i64,
    /// Saniyenin kesirini temsil eden nanosaniye.
    nanos: u32,

    /// Unix epoch zamanını döndürür (1970-01-01 00:00:00 UTC).
    pub fn unixEpoch() Timestamp {
        return .{ .seconds = 0, .nanos = 0 };
    }
};
```

Bu örnekte seconds alanındaki // signed so we can represent pre-1970 yorumu normal yorumdur ve dokümantasyonda görünmez .

### Üst Düzey Dokümantasyon

```zig
//! Bu modül tarih ve saat işlemleri için yardımcı fonksiyonlar sağlar.
//! libc'ye bağımlı değildir, ancak mevcutsa libc fonksiyonlarını kullanır.

const std = @import("std");
```

## Örnek Kod

Aşağıda, yorumların pratik kullanımını gösteren bir örnek verilmiştir:

```zig
//! Bu dosya, sıcaklık dönüşümleri için basit bir yardımcı modül içerir.

const std = @import("std");

/// Celsius değerini Fahrenheit'a dönüştürür.
/// 
/// Formül: F = C * 9/5 + 32
/// 
/// Örnek:
/// ```
/// const f = celsiusToFahrenheit(100.0); // 212.0 döner
/// ```
pub fn celsiusToFahrenheit(celsius: f64) f64 {
    return celsius * 9.0 / 5.0 + 32.0; // Dönüşüm formülü
}

/// Fahrenheit değerini Celsius'a dönüştürür.
pub fn fahrenheitToCelsius(fahrenheit: f64) f64 {
    return (fahrenheit - 32.0) * 5.0 / 9.0;
}

pub fn main() void {
    // 100 derece Celsius'u Fahrenheit'a çevir
    const f = celsiusToFahrenheit(100.0);
    
    // Sonucu yazdır: 212.0
    std.debug.print("{d} C = {d} F\n", .{ 100.0, f });
}
```

Çıktı:

```
100.0 C = 212.0 F
```

## En İyi Uygulamalar

· Genel API'ler için /// dokümantasyon yorumlarını kullanın; uygulama detayları için // tercih edin .
· Dokümantasyon yorumlarında Markdown'un bir alt kümesini kullanabilirsiniz; ancak HTML etiketleri desteklenmez ve kaynak kodda okunabilirliği bozar .
· Dokümantasyon yorumlarını, dokümante ettikleri bildirimin hemen öncesine yerleştirin; araya normal yorum girmemelidir.

> Uyarı: Zig'de C dilindeki gibi /* */ çok satırlı yorum yoktur. Bunu kullanmaya çalışmak derleme hatasına yol açar .

> Uyarı: Dokümantasyon yorumları yalnızca belirli konumlarda geçerlidir. Bir ifadenin ortasında veya normal yorumun hemen öncesinde /// kullanmak gelecekte derleme hatası haline gelecektir .

> Uyarı: //// (dört veya daha fazla slash) dokümantasyon yorumu değildir; normal yorum olarak işlenir .

## Kütüphane Referansları

-  Zig Dil Referansı (Yorumlar bölümü): https://ziglang.org/documentation/master/
- Zig Standart Kütüphanesi Dokümantasyonu: https://ziglang.org/documentation/master/std/
-  Zig Dokümantasyon Üretimi: https://ziglang.org/documentation/master/#Generating-Documentation

## Sonuç

Zig'in yorum sistemi üç seviyelidir: 
- kod içi açıklamalar için //, 
- API dokümantasyonu için /// 
- modül tanıtımı için //!. 

Çok satırlı yorum bloğunun bulunmaması, dilin "her satır bağımsız tokenize edilebilir" tasarım felsefesinin bir sonucudur. 

Dokümantasyon yorumları -femit-docs ile HTML dokümantasyonuna dönüştürülür ve Markdown'un sınırlı bir alt kümesini destekler. Yeni başlayanlar için en önemli kural, /* */ kullanmamak ve /// yorumlarını doğru konumlandırmaktır.
