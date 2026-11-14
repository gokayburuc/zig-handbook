# Zig - Variables

## Zig'de Değişkenler Nedir?

Zig'de değişken, bellekte bir depolama birimidir. const ve var olmak üzere iki anahtar kelimeyle tanımlanır. const değiştirilemez sabitleri, var ise değiştirilebilir değişkenleri belirtir. Zig, güvenlik ve optimizasyon gerekçesiyle mümkün olan her yerde const kullanılmasını şiddetle tavsiye eder; bu hem insanlar hem de bilgisayarlar için kod okumayı kolaylaştırır ve daha fazla optimizasyon fırsatı yaratır .

Zig'de değişkenler asla dış kapsamdaki tanımlayıcıları gölgeleyemez (shadowing yasaktır).

## Sözdizimi

Temel atama sözdizimi şöyledir:

```zig
(const|var) tanimlayici[: tur] = deger;
```

Tür belirtimi, başlangıç değerinden çıkarılabiliyorsa atlanabilir. Değişkenler mutlaka başlatılmalıdır; bilinen bir değer verilemiyorsa, tür belirtimi sağlanmak koşuluyla undefined kullanılabilir .

const, tanımlayıcının doğrudan adreslediği tüm baytlar için geçerlidir; işaretçilerin kendi const'luğu ayrıdır .

## Kullanım Alanları

Değişkenler üç kapsam düzeyinde tanımlanır:

- Yerel değişkenler: Fonksiyonlar, comptime blokları ve etiketli bloklar içinde tanımlanır.
- İsim alanı düzeyi değişkenler: struct, union, enum veya opaque içinde ya da dosya kapsamında tanımlanır; global ömre sahiptir.
- İş parçacığı yerel değişkenler: threadlocal anahtar kelimesiyle tanımlanır; her iş parçacığı ayrı bir örneğe sahiptir.

Ayrıca extern anahtar kelimesi veya @extern yerleşik fonksiyonu, başka bir nesneden dışa aktarılan bir değişkene bağlanmak için kullanılır. export veya @export ise değişkeni bağlama zamanında diğer nesnelere açmak için kullanılır. Her iki durumda da değişken türü C ABI uyumlu olmalıdır .

## Kullanım Türleri

### const ile Sabit Tanımlama

```zig
const yas = 25; // Tür çıkarımı: comptime_int
const isim: []const u8 = "Ali"; // Açık tür belirtimi
```

const değişkenin başlangıç değeri derleme zamanında biliniyorsa, değişkenin kendisi de comptime bilinir.

### var ile Değişken Tanımlama

```zig
var sayac: u32 = 0;
sayac += 1; // Değiştirilebilir
```

### comptime Değişkenleri

Yerel bir değişken comptime anahtar kelimesiyle nitelendirilebilir. Bu, değerinin derleme zamanında bilinmesine ve tüm okuma/yazma işlemlerinin derleme sırasında gerçekleşmesine neden olur. comptime ifadeleri içinde tanımlanan tüm değişkenler örtük olarak comptime değişkenleridir .

```zig
comptime var derleme_zamani_sayac: i32 = 10;
```

### undefined Kullanımı

```zig
var tampon: [100]u8 = undefined; // Henüz değer atanmadı
```

undefined, herhangi bir türe dönüştürülebilir, ancak okunmadan önce üzerine yazılmalıdır.

### Namespace Level Variables (İsim Alanı Düzeyi Değişkenler)

İsim alanı düzeyi değişkenler global ömre sahiptir, sıradan bağımsızdır ve tembel (lazy) olarak analiz edilir. Başlangıç değerleri örtük olarak comptime'dır. Değişken const ise değeri derleme zamanında, var ise çalışma zamanında bilinir . Bu değişkenler struct, union, enum veya opaque içinde tanımlanabilir.

```zig
const std = @import("std");

// Dosya kapsamında isim alanı düzeyi değişken
var global_sayac: u32 = 0;
const maksimum: u32 = 100; // Değeri comptime bilinir

const Yardimci = struct {
    // struct içinde isim alanı düzeyi değişken
    var cagri_sayisi: u32 = 0;
    const sabit_deger = 42;
};
```

### Locally-Scoped Global Variables (Fonksiyon İçinde Global Ömürlü Değişkenler)

Fonksiyonların içinde isim alanı tanımlayarak, yerel görünümlü ama global ömürlü değişkenler elde edilebilir . Bu, durum makinesi veya memoization gibi senaryolarda kullanışlıdır.

```zig
fn sayacArttir() u32 {
    const Durum = struct {
        var sayac: u32 = 0; // Global ömürlü, fonksiyon dışından erişilemez
    };
    Durum.sayac += 1;
    return Durum.sayac;
}
```

### Thread Local Variables (İş Parçacığı Yerel Değişkenler)

threadlocal anahtar kelimesi, her iş parçacığının ayrı bir örneğe sahip olmasını sağlar. Tek iş parçacıklı derlemelerde, iş parçacığı yerel değişkenler normal isim alanı düzeyi değişkenler gibi davranır. Bu değişkenler const olamaz .

```zig
threadlocal var is_parcacigi_verisi: u32 = 0;
```

### extern ve export

```zig
extern var harici_sayac: c_int;        // Başka nesneden gelen değişken
export var disa_acilan_sayac: c_int = 0; // Diğer nesnelere açılan değişken
```

## Örnek Kod

```zig
const std = @import("std");

// İsim alanı düzeyi sabit (derleme zamanında bilinir)
const maksimum_kullanici = 100;

// İsim alanı düzeyi değişken (çalışma zamanında bilinir)
var toplam_kullanici: u32 = 0;

// İş parçacığı yerel değişken
threadlocal var is_parcacigi_sayaci: u32 = 0;

pub fn main() void {
    // Değiştirilemez sabit
    const uygulama_adi = "Zig Uygulamasi";
    
    // Değiştirilebilir yerel değişken
    var aktif_kullanici: u32 = 10;
    aktif_kullanici += 5;
    toplam_kullanici = aktif_kullanici;
    
    // Fonksiyon içi global ömürlü sayaç
    const sayac = sayacArttir();
    
    // undefined tampon
    var mesaj_tamponu: [50]u8 = undefined;
    const mesaj = "Merhaba";
    @memcpy(mesaj_tamponu[0..mesaj.len], mesaj);
    
    // İş parçacığı yerel değişkeni güncelle
    is_parcacigi_sayaci += 1;
    
    std.debug.print("{s}: {d} aktif kullanici\n", .{ uygulama_adi, aktif_kullanici });
    std.debug.print("Maksimum: {d}, Toplam: {d}\n", .{ maksimum_kullanici, toplam_kullanici });
    std.debug.print("Fonksiyon sayaci: {d}\n", .{sayac});
    std.debug.print("Is parcacigi sayaci: {d}\n", .{is_parcacigi_sayaci});
}

fn sayacArttir() u32 {
    const Durum = struct {
        var sayac: u32 = 0;
    };
    Durum.sayac += 1;
    return Durum.sayac;
}
```

Çıktı:

```
Zig Uygulamasi: 15 aktif kullanici
Maksimum: 100, Toplam: 15
Fonksiyon sayaci: 1
Is parcacigi sayaci: 1
```

## En İyi Uygulamalar

- Değerin değişmesi gerekmiyorsa const kullanın; bu hem okunabilirliği artırır hem de optimizasyon fırsatları yaratır.
- Değişkenleri tanımlarken mümkünse hemen başlatın.
- undefined'ı yalnızca performans gerekçesiyle ve gerçekten zorunlu olduğunda kullanın.
- C ile birlikte çalışırken extern/export kullanılan değişkenlerin C ABI uyumlu olduğundan emin olun.

> Uyarı: Zig'de değişken gölgeleme (shadowing) kesinlikle yasaktır. İç kapsamdaki bir tanımlayıcı, dış kapsamdaki aynı adlı tanımlayıcıyı gölgeleyemez.

> Uyarı: threadlocal ile tanımlanan iş parçacığı yerel değişkenler const olamaz; bu derleme hatasına yol açar .

> Uyarı: İsim alanı düzeyi değişkenler tembel analiz edildiğinden, başlangıç değerleri bağımlılık sırasına göre değerlendirilir; tanımlama sırası önemli değildir, ancak döngüsel bağımlılık derleme hatasına yol açabilir.

> Uyarı: undefined değerini okumak undefined behavior'dır. Debug ve safe modda Zig, undefined belleğe 0xaa baytları yazar; ancak bu bir dil semantiği değil, uygulama ayrıntısıdır ve garanti edilmez .

## Kütüphane Referansları

- Zig Dil Referansı (Değişkenler): https://ziglang.org/documentation/master/#Variables
- Zig Standart Kütüphanesi: https://ziglang.org/documentation/master/std/

## Sonuç

Zig'de değişkenler const ve var ile tanımlanır; varsayılan tercih const olmalıdır. Değişkenler yerel, isim alanı düzeyi ve iş parçacığı yerel olmak üzere üç kapsamda bulunur. İsim alanı düzeyi değişkenler global ömürlü, sıradan bağımsız ve tembel analiz edilir; const ise derleme zamanında bilinir. Fonksiyon içinde isim alanı tanımlayarak global ömürlü yerel değişkenler elde edilebilir. threadlocal her iş parçacığına ayrı örnek verir ve const olamaz. extern/export C ABI uyumlu değişken bağlama sağlar. Gölgeleme yasaktır ve undefined dikkatli kullanılmalıdır. Yeni başlayanlar için en önemli kural: değişmesi gerekmeyen her şey için const kullanın.
