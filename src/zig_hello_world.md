# Zig - Hello World


## Zig Nedir?

Zig, sağlamlık, optimallik ve sürdürülebilirlik hedefleriyle tasarlanmış, genel amaçlı, statik tipli, derlenen bir sistem programlama dilidir . C dilinin yerini almayı amaçlar; örtük kontrol akışı, örtük bellek tahsisi, önişlemci ve makro gibi karmaşıklıkları ortadan kaldırır . comptime mekanizması sayesinde derleme zamanında kod çalıştırma ve meta programlama sunar .

## Syntax - Sözdizimi

Zig'de bir "Hello World" programının en temel yapısı şöyledir:

```zig
const std = @import("std"); // Standart kütüphaneyi içe aktar

pub fn main() !void {
    std.debug.print("Hello, world!\n", .{}); // Konsola yazdır
}
```

std.debug.print fonksiyonu, hata ayıklama amaçlı çıktı üretir. !void dönüş tipi, fonksiyonun hata döndürebileceğini belirtir .

## Kullanım Alanları

Zig özellikle şu alanlarda tercih edilir:

· C/C++ kütüphaneleriyle birlikte çalışma ve mevcut kod tabanlarını kademeli olarak iyileştirme 
· Gömülü sistemler ve düşük seviyeli programlama
· Çapraz derleme gerektiren projeler (Zig, bağımlılık olmadan C/C++ derleyicisi olarak da kullanılabilir) 
· Yeni başlayanlar için dil öğrenimi (basit sözdizimi sayesinde)

## Kullanım Türleri

### Tek Dosya Derleme

Proje yapısı oluşturmadan tek bir .zig dosyasını derlemek için:

```bash
zig build-exe hello.zig   # hello adında çalıştırılabilir dosya üretir
./hello                    # Çalıştır
```

Çıktı: Hello, world! 

### Proje Yapısı ile Derleme

zig init komutu standart bir proje iskeleti oluşturur:

```bash
mkdir hello && cd hello
zig init
```

Bu komut build.zig, build.zig.zon ve src/main.zig dosyalarını oluşturur . src/main.zig içeriğini düzenleyip şu komutla çalıştırabilirsiniz:

```bash
zig build run
```

## Örnek Kod

Aşağıda, kullanıcıdan isim alıp selamlayan gerçekçi bir örnek verilmiştir:

```zig
const std = @import("std");

pub fn main() !void {
    // Standart çıktı yazıcısını al
    const stdout = std.io.getStdOut().writer();
    
    // Kullanıcıya sor
    try stdout.print("Adınızı girin: ", .{});
    
    // Standart girdiden oku
    var buf: [100]u8 = undefined;
    const stdin = std.io.getStdIn().reader();
    const name = try stdin.readUntilDelimiterOrEof(&buf, '\n') orelse "Misafir";
    
    // Selamlama mesajı yazdır
    try stdout.print("Merhaba, {s}!\n", .{name});
}
```

Çıktı:

```
Adınızı girin: Ali
Merhaba, Ali!
```

Bu örnekte std.io.getStdOut().writer() ve std.io.getStdIn().reader() fonksiyonları kullanılmıştır. readUntilDelimiterOrEof metodu, satır sonuna kadar okuma yapar .

## En İyi Uygulamalar

· Değişkenleri varsayılan olarak const ile tanımlayın; yalnızca değiştirilmesi gerekiyorsa var kullanın .
· Hata döndürebilecek fonksiyonlarda ! dönüş tipini kullanın ve try anahtar kelimesiyle hataları yayın.
· zig build test komutuyla testleri çalıştırın; Zig'in yerleşik test çalıştırıcısı vardır .

Uyarı: Zig hızla gelişen bir dildir. Kullandığınız sürüme ait belgelere başvurun; master sürümü ile kararlı sürümler arasında sözdizimi farklılıkları olabilir .

Uyarı: std.debug.print hata ayıklama çıktısı içindir ve üretim kodunda kullanılmamalıdır. Gerçek çıktı için std.io.getStdOut().writer() tercih edilmelidir.

## Kütüphane Referansları

· Zig Standart Kütüphanesi: https://ziglang.org/documentation/master/std/
· Zig Dil Referansı: https://ziglang.org/documentation/master/
· Zig Başlangıç Kılavuzu: https://ziglang.org/intro/getting-started/

## Sonuç

Zig, basit sözdizimi ve güçlü derleme zamanı özellikleriyle yeni başlayanlar için öğrenilebilir bir sistem programlama dilidir. "Hello World" örneği, std.debug.print ve zig build run komutlarıyla hızlıca çalıştırılabilir. Proje yapısı oluşturmak için zig init kullanılır; tek dosya derlemek için zig build-exe yeterlidir. Zig öğrenirken sürüm uyumluluğuna dikkat edilmeli ve yerleşik test altyapısından yararlanılmalıdır.


