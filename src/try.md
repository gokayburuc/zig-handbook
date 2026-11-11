# Try

Zig dilinde `try` ifadesi, **hata yönetimi** için kullanılan güçlü bir araçtır. Zig’in hata sistemi, istisna (exception) tabanlı değil; bunun yerine **explicit error handling** yaklaşımını kullanır. `try`, bir hata döndürebilecek işlemi çalıştırır ve hata oluşursa onu **otomatik olarak üst seviyeye iletir**.

---

### try Nedir?

`try`, bir `!T` türündeki (örneğin `!i32`) değeri işler. Eğer işlem başarılıysa sonucu döner; eğer hata oluşursa, o hatayı bulunduğu fonksiyona geri iletir (`return` gibi davranır).

---

### Kullanım Şekli

```zig
const std = @import("std");

pub fn main() !void {
    const result = try doSomething();
    std.debug.print("Sonuç: {}\n", .{result});
}

fn doSomething() !i32 {
    return error.Failed; // hata döndürür
}
```

`try` yapısı ayrıca `canFail() catch |err| return err;` benzeri yapıları daha kısa yazmak için kullanılır.

#### Açıklama:

- `doSomething()` fonksiyonu `!i32` türünde bir değer döner.
- `try doSomething()` ifadesi hata dönerse, `main()` fonksiyonu da aynı hatayı döndürür.
- `main()` fonksiyonunun dönüş tipi `!void` olduğu için hata döndürebilir.

---

### Ne İçin Kullanılır?

- Hataları üst seviyeye iletmek için
- Kodun daha sade ve okunabilir olmasını sağlamak için
- Hataları manuel olarak `catch` ile işlemeye gerek kalmadan yönetmek için

---

### 🔄 `try` vs `catch`

| Özellik        | `try`                            | `catch`                              |
| -------------- | -------------------------------- | ------------------------------------ |
| Hata durumu    | Hata varsa üst fonksiyona iletir | Hata varsa alternatif kod çalıştırır |
| Kullanım amacı | Hataları zincirleme iletmek      | Hataları yerinde işlemek             |
| Kod stili      | Daha sade ve fonksiyonel         | Daha kontrollü ve lokal              |

---
