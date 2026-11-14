# Zig - Values

## Zig Değerleri ve Değer Türleri Nedir?

Zig'de "değer", bir değişkende saklanan veya bir ifade tarafından üretilen somut veridir. "Değer türü" ise bu verinin türünü (integer, float, boolean vb.) belirtir. Zig'in tür sistemi katı ve öngörülebilirdir; derleme zamanında bilinen değerler (comptime) ile çalışma zamanında bilinen değerler arasında net bir ayrım yapar .

## Syntax - Sözdizimi

Değerler, const veya var anahtar kelimeleriyle tanımlanan değişkenlere atanır:

```zig
const sabit_deger = 42;           // Değiştirilemez
var degisken_deger: i32 = -10;    // Değiştirilebilir
```

Tür belirtimi isteğe bağlıdır; Zig, başlangıç değerinden tür çıkarımı yapabilir . Çıkarım yapılan tür comptime_int veya comptime_float olur ve bu değerler yalnızca derleme zamanında geçerlidir.

## Kullanım Alanları

Değer türleri programın her yerinde kullanılır:

- Integer'lar: Sayılar, indeksler, bit maskeleri (i32, u8, i7 gibi keyfi bit genişlikleri)
- Float'lar: Ondalık sayılar, fiziksel hesaplamalar (f16, f32, f64, f128)
- Boolean: Mantıksal koşullar (true, false)
- undefined: Henüz değer atanmamış değişkenler
- null: Optional türlerin boş değeri

## Kullanım Türleri

### Integer Değerleri

Zig, keyfi bit genişliğinde integer'ları destekler. i işaretli, u işaretsiz anlamına gelir :

```zig
const ondalik = 98222;
const onaltilik = 0xFF;
const sekizlik = 0o755;
const ikilik = 0b11110000;

// Okunabilirlik için alt çizgi kullanılabilir
const bir_milyar = 1_000_000_000;
const bayt_maskesi = 0b1111_0000;

// Keyfi bit genişliği
const yedi_bit: i7 = -64;         // -64 ile 63 arası
const yirmi_dort_bit: u24 = 1000;
```

Integer literalleri boyut sınırlaması olmadan derleme zamanında değerlendirilir. Ancak çalışma zamanına geçtiğinde boyut sabitlenir ve taşma durumunda undefined behavior oluşur . Taşma yerine sarmalama isteniyorsa +% ve -% operatörleri kullanılır .

### Float Değerleri

Zig, IEEE-754 standardına uygun float türleri sunar :

```zig
const f32_deger: f32 = 3.14;
const f64_deger: f64 = 299_792_458.000_000;
const hex_float = 0x103.70p-5;
```

Float literalleri comptime_float türündedir ve f128 hassasiyetindedir. Kesirli kısmı yoksa integer türlerine örtük olarak dönüşebilir .

### Boolean Değerleri

```zig
const dogru: bool = true;
const yanlis: bool = false;
```

### undefined Değeri

undefined, "anlamlı bir değer yok" anlamına gelir. Kullanımı bir hatadır; değer ya kullanılmayacak ya da kullanılmadan önce üzerine yazılacaktır :

```zig
var tampon: [100]u8 = undefined;  // Henüz değer atanmadı
```

Debug modunda Zig, undefined belleğe 0xaa baytları yazarak hataları erken yakalamaya yardımcı olur .

### null Değeri

null, yalnızca optional türlerde (?T) kullanılır. Normal işaretçiler null olamaz :

```zig
const belki_sayi: ?u32 = null;
const kesin_sayi: ?u32 = 42;
```

### comptime Değerleri

Derleme zamanında bilinen değerler comptime olarak işaretlenir. Tüm integer ve float literalleri başlangıçta comptime türündedir :

```zig
const derleme_zamani = 100;  // comptime_int
const calisma_zamani: i32 = 100;  // i32, çalışma zamanında
```

## Örnek Kod

```zig
const std = @import("std");

pub fn main() void {
    // Integer değerler
    const yas: u8 = 25;
    const sicaklik: i16 = -5;
    
    // Float değer
    const pi: f64 = 3.14159;
    
    // Boolean
    const yetiskin: bool = yas >= 18;
    
    // undefined tampon
    var tampon: [10]u8 = undefined;
    tampon[0] = 'A';  // Sadece ilk eleman atandı
    
    // Optional değer
    const belki_isim: ?[]const u8 = null;
    
    // Değerleri yazdır
    std.debug.print("Yaş: {d}, Yetişkin: {}\n", .{ yas, yetiskin });
    std.debug.print("Sıcaklık: {d}\n", .{sicaklik});
    std.debug.print("Pi: {d:.2}\n", .{pi});
    std.debug.print("Tampon[0]: {c}\n", .{tampon[0]});
    std.debug.print("İsim: {?s}\n", .{belki_isim});
}
```

Çıktı:

```
Yaş: 25, Yetişkin: true
Sıcaklık: -5
Pi: 3.14
Tampon[0]: A
İsim: null
```

## En İyi Uygulamalar

- Değişmez değerler için const kullanın; var yalnızca gerçekten değişecekse tercih edilmelidir .
- Integer taşması riski olan yerlerde +%/-% kullanın veya @intCast ile güvenli dönüşüm yapın .
- undefined kullanırken değişkenin kullanılmadan önce tamamen başlatıldığından emin olun.

> Uyarı: Integer literalleri derleme zamanında sınırsızdır, ancak çalışma zamanı değerleri sabit boyutludur. a / b işleminde b sıfır olabilir ve bu undefined behavior'a yol açar .

> Uyarı: undefined değerini okumak undefined behavior'dır. Debug modunda 0xaa deseni görülebilir, ancak Release modunda bu garanti edilmez .

> Uyarı: Normal işaretçiler (*T) null olamaz. Boş olabilirlik gerekiyorsa ?*T optional işaretçi türü kullanılmalıdır .

Kütüphane Referansları

- Zig Dil Referansı (Değerler ve Türler): https://ziglang.org/documentation/master/
- Zig Standart Kütüphanesi: https://ziglang.org/documentation/master/std/

## Sonuç

Zig'de değerler, integer'lar, float'lar, boolean'lar, undefined ve null gibi temel yapı taşlarından oluşur. Integer'lar keyfi bit genişliğinde tanımlanabilir; float'lar IEEE-754 uyumludur. comptime türleri derleme zamanında değerlendirilir ve çalışma zamanı türlerinden ayrılır. undefined yalnızca başlatılmamış değişkenler için kullanılır ve okunması hatadır. null ise yalnızca optional türlerde geçerlidir. Yeni başlayanlar için en önemli kural, const'u tercih etmek ve undefined değerini kullanmadan önce başlatmaktır.
