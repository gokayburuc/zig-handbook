# Zig - String Literals and Unicode Point Literals

## Zig'de String Literals ve Unicode Code Point Literals Nedir?

Zig'de string literal (dize değişmezi), çift tırnak içine yazılan ve derleme zamanında belleğe yerleştirilen sabit bir bayt dizisidir. Unicode code point literal ise tek tırnak içinde \u{...} sözdizimiyle ifade edilen ve bir Unicode kod noktasını temsil eden derleme zamanı tamsayısıdır. Zig kaynak kodu UTF-8 kodlamalıdır; bu nedenle string literal'lerdeki ASCII dışı baytlar UTF-8 olarak yorumlanır .

## Sözdizimi

### String Literals

```zig
const selam = "Merhaba";                    // Normal string literal
const kacisli = "Satır1\nSatır2\tSekme";    // Kaçış dizileri desteklenir
const cok_satirli =
    \\Birinci satır
    \\İkinci satır
;                                            // Çok satırlı string literal
```

Zig'de string literal'in türü *const [N:0]u8 şeklindedir. Bu, "N elemanlı, sonunda 0 (null) sentinel bulunan sabit bir u8 dizisine işaretçi" anlamına gelir . Bu yapı, C string'leriyle uyumluluk sağlar ve çeşitli dilim (slice) türlerine örtük olarak dönüştürülebilir.

### Unicode Code Point Literals

```zig
const harf = 'A';              // ASCII karakter, türü comptime_int
const turkce = 'ş';           // UTF-8 kaynak kodunda doğrudan yazılabilir
const unicode = '\u{1F600}';  // Unicode kod noktası (emoji)
```

Unicode kod noktası literal'lerinin türü comptime_int'tir . Geçerli maksimum Unicode kod noktası 0x10FFFF'dir .

## Kullanım Alanları

- String literal'ler: Sabit mesajlar, dosya yolları, format dizeleri, hata mesajları ve C kütüphaneleriyle birlikte çalışma.
- Unicode kod noktası literal'leri: Tek bir karakterin sayısal değerini elde etmek, karakter karşılaştırmaları ve Unicode tabanlı mantık işlemleri.

## Kullanım Türleri

### Kaçış Dizileri

String literal'ler ve Unicode kod noktası literal'leri aynı kaçış dizilerini destekler :

| Kaçış |  Anlamı|
| --- |  --- | 
| \n  | Yeni satır|
| \r |  Satır başı| 
| \t | Sekme | 
| \\ | Ters eğik çizgi| 
| \" |  Çift tırnak|
| \' | Tek tırnak| 
|\xNN | 2 haneli onaltılık bayt değeri| 
|\u{NNNN} |Unicode kod noktası (UTF-8 kodlanır)|

### Çok Satırlı String Literal

\\ token'ı ile başlar ve satır sonuna kadar devam eder. Satır sonu karakteri dizeye dahil edilmez; ancak bir sonraki satır da \\ ile başlıyorsa araya yeni satır eklenir .

```zig
const metin =
    \\Bu bir
    \\çok satırlı
    \\metindir.
;
// Sonuç: "Bu bir\nçok satırlı\nmetindir."
```

### Dize Dilimleri ve Dönüşümler

String literal'ler farklı türlere örtük olarak dönüştürülebilir :

```zig
const dizi: [5]u8 = "dizi".*;                    // Diziye kopyala
const isaretci: *const [5:0]u8 = "test";         // Sentinel'li dizi işaretçisi
const dilim: []const u8 = "dilim";                // Dilim
const sentinel_dilim: [:0]const u8 = "sentinel";  // Sentinel'li dilim
const c_uyumlu: [*:0]const u8 = "C string";       // C uyumlu çoklu işaretçi
```

## Örnek Kod

```zig
const std = @import("std");

pub fn main() void {
    // Normal string literal
    const isim = "Zig";
    std.debug.print("Merhaba, {s}!\n", .{isim});
    
    // Unicode kod noktası literal'i
    const kalp = '\u{2764}';  // ❤
    const kalp_bayt = '\u{1F4A1}';  // 💡
    std.debug.print("Kalp: {u}\n", .{kalp});
    
    // Türkçe karakterler (UTF-8 kaynak kodunda)
    const turkce = "Değerler: çğıöşü";
    std.debug.print("{s}\n", .{turkce});
    
    // Çok satırlı string
    const adres =
        \\Atatürk Cad.
        \\No: 1
        \\İstanbul
    ;
    std.debug.print("Adres:\n{s}\n", .{adres});
    
    // Kaçış dizileri
    const kacis = "Sekme:\tSon";
    std.debug.print("{s}\n", .{kacis});
    
    // Dize uzunluğu bayt cinsindendir (karakter değil)
    std.debug.print("'{s}' uzunluğu: {d} bayt\n", .{ turkce, turkce.len });
}
```

Çıktı:

```
Merhaba, Zig!
Kalp: ❤
Değerler: çğıöşü
Adres:
Atatürk Cad.
No: 1
İstanbul
Sekme:	Son
'Değerler: çğıöşü' uzunluğu: 19 bayt
```

Son satırdaki uzunluk 19'dur çünkü Türkçe karakterler UTF-8'de birden fazla bayt kaplar; .len karakter sayısını değil bayt sayısını verir .

## En İyi Uygulamalar

- String literal'leri []const u8 dilim türüyle kabul eden fonksiyonlar yazın; bu, tüm dize temsilleriyle uyumluluk sağlar.
- UTF-8 karakter sayısını saymak için std.unicode modülünü kullanın; .len yalnızca bayt sayısını verir .
- C kütüphaneleriyle çalışırken [*:0]const u8 türünü tercih edin; bu tür null-sonlandırmalıdır ve C ile doğrudan uyumludur.

> Uyarı: String literal'ler salt okunurdur ve var ile tanımlansalar bile içerikleri değiştirilemez. var msg = "metin"; yazıldığında msg türü *const [5:0]u8 olur; yani işaretçi değişebilir ama gösterdiği veri değişemez .

> Uyarı: String literal'lerde doğrudan Tab karakteri kullanılamaz; bunun yerine \t kaçış dizisi kullanılmalıdır .

> Uyarı: UTF-8 kodlamalı bir dizede indeksleme yapmak tek bir bayt döndürür; bu bayt geçerli bir UTF-8 karakterinin parçası olmayabilir. Karakter bazlı işlemler için std.unicode fonksiyonlarını kullanın .

## Kütüphane Referansları

- Zig Dil Referansı (String Literals): https://ziglang.org/documentation/master/
- Zig Standart Kütüphanesi std.unicode: https://ziglang.org/documentation/master/std/#std.unicode
- Zig Standart Kütüphanesi std.ascii: https://ziglang.org/documentation/master/std/#std.ascii

## Sonuç

Zig'de string literal'ler *const [N:0]u8 türündedir ve hem C uyumluluğu hem de Zig'in dilim tabanlı API'leriyle çalışabilme esnekliği sunar. Unicode kod noktası literal'leri comptime_int türündedir ve tek karakterlerin sayısal değerlerini ifade eder. Çok satırlı string'ler \\ ile başlar, kaçış dizisi içermez. UTF-8 kodlaması nedeniyle dize uzunlukları bayt cinsindendir; karakter sayısı için std.unicode kullanılmalıdır. Yeni başlayanlar için en önemli kural, string literal'lerin salt okunur olduğunu unutmamak ve dilim türlerinde []const u8 tercih etmektir.
