# Zig - Error Union Type

## Zig'de Hata Birleşim Türü (Error Union Type) Nedir?

Zig'de hata birleşim türü (error union type), bir fonksiyonun ya bir hata değeri ya da normal bir başarı değeri döndürebileceğini ifade eden bir türdür. ! operatörü ile oluşturulur ve !T sözdizimi, "hata kümesi veya T türünde bir değer" anlamına gelir . Zig'de istisna (exception) mekanizması yoktur; hatalar birer değerdir ve derleyici tarafından açıkça ele alınmaları zorunlu tutulur .

## Sözdizimi

Hata birleşim türünün genel formu şöyledir:

```zig
HataKumesi!BasariTuru
```

En basit haliyle !T, anyerror!T için kısaltmadır; yani herhangi bir hata veya T türünde değer döndürür . Belirli bir hata kümesiyle sınırlamak için:

```zig
const DosyaHatasi = error{DosyaYok, IzinYok};

fn dosyaOku(yol: []const u8) DosyaHatasi![]u8 {
    // ...
}
```

## Kullanım Alanları

Hata birleşim türleri, başarısız olabilecek her işlemde kullanılır:

- Dosya okuma/yazma işlemleri
- Ağ istekleri
- Sayı ayrıştırma (parsing)
- Bellek tahsisi
- Kullanıcı girdisi doğrulama

Test yazarken de hata birleşimleri kritik öneme sahiptir; çünkü try ve catch ifadeleri test bloklarında hata yollarını doğrulamak için kullanılır.

## Kullanım Türleri

### try ile Hata Yayma

try anahtar kelimesi, hata birleşimi döndüren bir ifadeyi çağırır. Başarılıysa değeri açar ve döndürür; başarısızsa hatayı çağırana iletir .

```zig
fn ikiKatiniAl(x: u32) !u32 {
    if (x > 1000) return error.CokBuyuk;
    return x * 2;
}

fn isleyici() !void {
    const sonuc = try ikiKatiniAl(50); // Hata varsa buradan çıkar
    // sonuc = 100
}
```

try x, aslında x catch |err| return err ifadesinin kısaltmasıdır .

### catch ile Hatayı Yakalama

catch anahtar kelimesi, hatayı yerel olarak ele alır ve varsayılan bir değer döndürür veya bir blok çalıştırır .

```zig
// Varsayılan değer ile
const sayi = ayristir("abc") catch 0;

// Hata yakalama bloğu ile
const sonuc = dosyaOku("veri.txt") catch |err| {
    std.debug.print("Hata: {}\n", .{err});
    return;
};
```

### if ile Hata Birleşimini Açma

Hata birleşimleri, if ifadesiyle de ele alınabilir. Başarı dalında değer açılır, else dalında hata yakalanır .

```zig
if (ayristir(girdi)) |deger| {
    std.debug.print("Sonuç: {d}\n", .{deger});
} else |err| {
    std.debug.print("Hata: {}\n", .{err});
}
```

### errdefer ile Hata Durumunda Temizlik

errdefer, yalnızca fonksiyon bir hata ile döndüğünde çalışır. Kaynak temizliği için kullanılır .

```zig
fn kaynaklariKullan() !void {
    const kaynak = try kaynakTahsisEt();
    errdefer kaynakSerbestBirak(kaynak); // Sadece hata durumunda çalışır
    
    try islemYap(kaynak); // Hata olursa kaynak serbest bırakılır
}
```

## Örnek Kod (Test ile Birlikte)

Aşağıdaki örnek, hata birleşim türünün testlerde nasıl doğrulandığını gösterir:

```zig
const std = @import("std");
const testing = std.testing;

/// Pozitif sayıyı ayrıştırır; geçersizse hata döndürür.
fn pozitifAyristir(metin: []const u8) !u32 {
    const sayi = std.fmt.parseInt(u32, metin, 10) catch |err| {
        return err; // Ayrıştırma hatasını yay
    };
    if (sayi == 0) return error.SifirOlamaz;
    return sayi;
}

test "pozitifAyristir: başarılı" {
    const sonuc = try pozitifAyristir("42");
    try testing.expectEqual(@as(u32, 42), sonuc);
}

test "pozitifAyristir: sıfır hatası" {
    try testing.expectError(error.SifirOlamaz, pozitifAyristir("0"));
}

test "pozitifAyristir: geçersiz format hatası" {
    // parseInt'in döndürdüğü hata anyerror kapsamında
    try testing.expectError(error.InvalidCharacter, pozitifAyristir("abc"));
}
```

Çıktı:

```
1/1 test.test.pozitifAyristir: başarılı... OK
2/2 test.test.pozitifAyristir: sıfır hatası... OK
3/3 test.test.pozitifAyristir: geçersiz format hatası... OK
All 3 tests passed.
```

## En İyi Uygulamalar

- Fonksiyonun döndürebileceği hataları açıkça belirtin (DosyaHatasi!T gibi); anyerror kullanımından kaçının .
- Kaynak tahsisi yapan fonksiyonlarda errdefer ile hata durumunda temizlik yapın.
- Testlerde hata yollarını expectError ile doğrulayın; yalnızca başarı durumunu test etmek yeterli değildir.

> Uyarı: try ve catch birbirinin alternatifidir; aynı ifadede birlikte kullanılamaz. try hatayı yaymak, catch ise ele almak için kullanılır .

> Uyarı: Hata kümeleri (error{...}) ile hata birleşimleri (!T) farklı şeylerdir. catch ve try yalnızca hata birleşimleriyle çalışır; saf hata değerleriyle çalışmaz .

## Kütüphane Referansları

-  Zig Dil Referansı (Hata İşleme): https://ziglang.org/documentation/master/#Errors
-  Zig Standart Kütüphanesi std.testing: https://ziglang.org/documentation/master/std/#std.testing

## Sonuç

Hata birleşim türleri (!T), Zig'in istisnasız hata işleme modelinin temelidir. try ile hatalar yayılır, catch ile yerel olarak ele alınır, if ile başarı ve hata dalları ayrılır. errdefer hata durumunda kaynak temizliği sağlar. Testlerde expectError ile hata yolları doğrulanmalıdır. Yeni başlayanlar için en önemli kural, hata birleşimlerini try veya catch olmadan kullanmamak ve testlerde hata durumlarını da kapsamaktır.
