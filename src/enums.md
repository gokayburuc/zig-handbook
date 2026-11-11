# enums

# Enums in Zig

Enum yapıları zig programlama dilinde sabit değer kümelerini tanımlamak için kullanılan ifadelerdir.

```zig

const Direction = enum {
	North,
	South,
	East,
	West
};

const dir = Direction.North;

```

Bir değer kümesinde hangi mevcut değerlerin olabileceğini daha önceden tanımladığımız veri yapılarıdır. Yukarıdaki ifadenin devamında şöyle bir _switch_ yapısı oluşturalım:

```zig
switch (dir) {
    Direction.North => std.debug.print("Going up\n", .{}),
    Direction.South => std.debug.print("Going down\n", .{}),
    Direction.East  => std.debug.print("Going right\n", .{}),
    Direction.West  => std.debug.print("Going left\n", .{}),
}
```

Eğer işlemlerimiz sırasında `dir` isimli sabitimiz `Direction.North` isimli enum öğesi ile karşılaşırsa `std.debug.print` ile ekrana yazdırılacak ifade belirtiliyor.

- Eğer `switch` yapısı içerisinde bir `enum` değerini unutursanız zig size hata bildiriminde bulunur.

## Tagged Unions in Zig

```zig
const Shape = union(enum) {
    Circle: f32,
    Square: f32,
};

const s = Shape{ .Circle = 5.0 };
```

`union` ifadelerinin yazımı sırasında parametre türünü `enum` olarak belirlediğinizde bu karmaşık modellerde doğrudan ifadelere geçiş hakkı sağlar.

## Error Handling with Enums in Zig

Enum yapıları hata yönetimi (_error handling_ ) için de kullanılır.

```zig
const MyError = error{ NotFound, PermissionDenied };

fn read() MyError!void {
    return MyError.NotFound;
}
```

Yukarıdaki ifade bir _error set_ ifadesidir ve bir _enum_ yapısıdır. Yani nokta gösterimi (dot notation) sağlar.
