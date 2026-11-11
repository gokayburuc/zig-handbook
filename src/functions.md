# Funtions

## Define A Function in Zig

`zig` dilinde bir _fonksiyon_ yazarken aşağıdaki yapıyı kullanırız:

```zig
fn functionname(arguments) returndatatype {
  // function code
}

```

Burada eğer `void` geri dönüş tipi verilirse fonksiyon herhangi bir değeri `stdout` olarak döndürmeyecektir. Yalnızca işlemleri gerçekleştirecektir.`u8` gibi bir yapı belirtilirse `return` ile birlikte `u8` tipinde değer döndürecektir.

```zig
const std = @import("std");

pub fn main() void {
    // The new function deepThought() should return the number 42. See below.
    const answer: u8 = deepThought();

    std.debug.print("Answer to the Ultimate Question: {}\n", .{answer});
}

// Please define the deepThought() function below.
//
// We're just missing a couple things. One thing we're NOT missing is the
// keyword "pub", which is not needed here. Can you guess why?
//
fn deepThought() u8 {
    return 42; // Number courtesy Douglas Adams
}


```

İlk örnekte ise `main()` isimli fonksiyon içerisinde `deepThought()` isimli başka bir fonksiyonu çağırıyoruz:

```zig
fn deepThought() u8 {
    return 42; // Number courtesy Douglas Adams
}
```

Bu fonksiyon ifadesi `main()` isimli fonksiyona aşağıdaki gibi çağrılıyor:

```zig

pub fn main() void {
    // The new function deepThought() should return the number 42. See below.
    const answer: u8 = deepThought();

    std.debug.print("Answer to the Ultimate Question: {}\n", .{answer});
}

```

## Function Parameters in Zig

```zig
   fn myFunction(number: u8, is_lucky: bool) {
       ...
   }

```

`zig` dilinde _fonksiyon paratemetreleri_ parantez içinde `:` kullanarak yazılır. `number` ve `is_lucky` isimli parametrelerin veri tipleri `:` işareti sonrasında belirtilmiştir.
