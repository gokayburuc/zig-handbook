# Zig - Testing

## Zig'de Test Nedir?

Zig'in test sistemi, dilin ayrılmaz bir parçasıdır. Testler, kaynak kodun içine doğrudan test bloğu olarak yazılır ve zig test komutu veya zig build test ile çalıştırılır. Derleyici, normal derleme sırasında test bloklarını yok sayar; yalnızca test komutu verildiğinde bu blokları derleyip çalıştırır . Bu yaklaşım, testlerin kaynak kodla birlikte yaşamasını ve API'nin nasıl kullanılacağını gösteren canlı örnekler olmasını sağlar .

## Syntax - Sözdizimi

Temel bir test bloğu şu yapıdadır:

```zig
const std = @import("std");
const testing = std.testing;

test "test adı" {
    try testing.expect(koşul);
    try testing.expectEqual(beklenen, gerçek);
}
```

test anahtar kelimesi, isteğe bağlı bir açıklama dizesi ve test gövdesini alır. try anahtar kelimesi, bir doğrulama başarısız olduğunda hatayı yukarıya iletir ve testi başarısız kılar.

## Kullanım Alanları

Testler şu amaçlarla kullanılır:

· Fonksiyonların beklenen çıktıyı ürettiğini doğrulamak
· Hata yollarının (!T dönen fonksiyonlar) doğru hatayı döndürdüğünü kontrol etmek
· Bellek sızıntılarını tespit etmek (std.testing.allocator ile)
· Regresyonları önlemek ve kodu yeniden düzenlerken güven sağlamak

## Kullanım Türleri

### Doğrulama Fonksiyonları

Zig standart kütüphanesi, testlerde kullanılan temel doğrulama fonksiyonlarını std.testing altında sunar :

| Fonksiyon| Amaç| 
| --- | --- |
| expect(bool) | Koşulun doğru olduğunu doğrular| 
| expectEqual(beklenen, gerçek) | Değer eşitliğini kontrol eder|
| expectEqualStrings(beklenen, gerçek) | Dize eşitliğini fark çıktısıyla kontrol eder| 
| expectEqualSlices(T, beklenen, gerçek) |Dilim eşitliğini kontrol eder | 
| expectEqualDeep(beklenen, gerçek) | İşaretçileri takip ederek derin eşitlik kontrol eder|
|expectError(hata, ifade)| Belirli bir hatanın döndüğünü doğrular| 

## Test Komutları

```bash
# Tek dosyadaki testleri çalıştır
zig test src/main.zig

# Build sistemi üzerinden tüm testleri çalıştır
zig build test

# Belirli bir testi isimle filtrele
zig build test -Dtest-filter="toplama"
```

zig build test kullanıldığında, build.zig dosyasında test adımının tanımlanmış olması gerekir .

### Bellek Sızıntısı Tespiti

std.testing.allocator, test sırasında yapılan bellek tahsislerini izler ve serbest bırakılmayan bellek varsa testi başarısız kılar . Bu allocator, DebugAllocator tabanlıdır ve sızıntı raporunda tahsisin yapıldığı yeri gösterir .

```zig
test "bellek sızıntısı yok" {
    const allocator = std.testing.allocator;
    var liste = std.ArrayList(u8).init(allocator);
    defer liste.deinit(); // Bu satır olmadan test başarısız olur
    try liste.append('A');
    try std.testing.expectEqual(@as(usize, 1), liste.items.len);
}
```

### refAllDecls ile Testleri Zorla Çalıştırma

Hiçbir yerden içe aktarılmayan bir dosyadaki testler çalıştırılmaz. std.testing.refAllDecls(@This()) çağrısı, bir kapsayıcıdaki tüm genel bildirimlerin analiz edilmesini zorlayarak bu testlerin çalıştırılmasını sağlar .

```zig
test {
    std.testing.refAllDecls(@This());
}
```

## Örnek Kod

```zig
const std = @import("std");
const testing = std.testing;

/// İki sayının toplamını döndürür.
fn topla(a: i32, b: i32) i32 {
    return a + b;
}

/// Böler; sıfıra bölmede hata döndürür.
fn bol(a: f64, b: f64) !f64 {
    if (b == 0.0) return error.SifiraBolme;
    return a / b;
}

test "topla: temel toplama" {
    // Beklenen değer ilk argüman olarak verilir
    try testing.expectEqual(@as(i32, 5), topla(2, 3));
    try testing.expectEqual(@as(i32, -1), topla(2, -3));
}

test "topla: sıfır ile toplama" {
    try testing.expect(topla(42, 0) == 42);
}

test "bol: normal bölme" {
    const sonuc = try bol(10.0, 2.0);
    try testing.expectApproxEqAbs(sonuc, 5.0, 1e-9);
}

test "bol: sıfıra bölme hatası" {
    try testing.expectError(error.SifiraBolme, bol(1.0, 0.0));
}
```

Çıktı:

```
1/1 test.test.topla: temel toplama... OK
2/2 test.test.topla: sıfır ile toplama... OK
3/3 test.test.bol: normal bölme... OK
4/4 test.test.bol: sıfıra bölme hatası... OK
All 4 tests passed.
```

## En İyi Uygulamalar

- Testleri, test ettikleri kodla aynı dosyada tutun; Zig standart kütüphanesi bu yaklaşımı benimser .
- Test adlarını açıklayıcı yazın; hata durumunda hangi davranışın bozulduğu hemen anlaşılsın.
- Testleri gereksiz yere soyutlamayın; somut girdi ve beklenen değerleri doğrudan yazmak okunabilirliği artırır .
- std.testing.allocator kullanan her testte defer ile kaynakları serbest bırakın.

> Uyarı: std.testing.allocator yalnızca test blokları içinde kullanılabilir; normal kodda kullanmaya çalışmak derleme hatasına yol açar .

> Uyarı: expectEqual dilimler için işaretçi ve uzunluk karşılaştırması yapar; içerik karşılaştırması için expectEqualDeep veya expectEqualSlices kullanılmalıdır .

> Uyarı: Bellek sızıntısı durumunda testin kendisi "OK" görünse bile özet satırında "1 tests leaked memory" hatası raporlanır; test çıktısını yalnızca satır bazında değil, özet üzerinden de kontrol edin .

## Kütüphane Referansları

- Zig Dil Referansı: https://ziglang.org/documentation/master/
- Zig Standart Kütüphanesi std.testing: https://ziglang.org/documentation/master/std/#std.testing
- Zig Build Sistemi: https://ziglang.org/learn/build-system/

## Sonuç

Zig'de testler test blokları olarak kaynak kodun içine yazılır ve zig test veya zig build test ile çalıştırılır. std.testing modülü, boolean, eşitlik, dize, dilim ve hata doğrulamaları için fonksiyonlar sunar. std.testing.allocator bellek sızıntılarını otomatik olarak tespit eder. refAllDecls ile içe aktarılmayan dosyalardaki testler zorla çalıştırılabilir. Yeni başlayanlar için en önemli kural, testleri kodla birlikte tutmak ve her testte kaynakları defer ile temizlemektir.
