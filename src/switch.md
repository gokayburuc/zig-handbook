# Switch

# Switch in Zig

```zig
const std = @import("std");

pub fn PlateNumbers() void {
    const plates = [_]u8{ 22, 39, 16, 34, 17, 10, 59, 6, 1, 53, 61 };

    for (plates) |plate| {
        switch (plate) {
            22 => std.debug.print(" {} : Edirne\n", .{plate}),
            39 => std.debug.print(" {} : Kırklareli\n", .{plate}),
            59 => std.debug.print(" {} : Tekirdağ\n", .{plate}),
            34 => std.debug.print(" {} : İstanbul\n", .{plate}),
            16 => std.debug.print(" {} : Bursa\n", .{plate}),
            10 => std.debug.print(" {} : Balıkesir\n", .{plate}),
            17 => std.debug.print(" {} : Çanakkale\n", .{plate}),
            else => {
                std.debug.print(" {} : Ortadoğu Cehennemi\n", .{plate});
            },
        }
    }
}

```

Yukarıdaki basit `zig` uygulaması plaka listesindeki numaralara göre eşleştirmeler yapar ve eğer verilenler dışında bir plaka ile karşılaşırsa `Ortadoğu Cehennemi` yazar.

## Switch Statement as Expression

```zig

const std = @import("std");

pub fn ReturnResponseMessage() void {
    const html_response_codes = [_]u16{
        200,
        403,
        404,
        502,
        503,
        402,
        600,
        201,
        203,
        600,
    };

    for (html_response_codes) |response| {
        const resp_status: []const u8 = switch (response) {
            200 => "OK",
            403 => "Forbidden",
            404 => "Not Found",
            405 => "Method Not Allowed",
            407 => "Proxy Authentication Required",
            502 => "Bad Gateway",
            else => "Another Error",
        };

        std.debug.print("{} : {s}\n", .{ response, resp_status });
    }
}


```

Burada `switch` yapısını **expression** ifadesine atıyoruz. Böylece `resp_status` isimli ifademiz verilen sayıya göre otomatik olarak `switch` listemizdeki `case` ifadelerinden birini değer olarak alabiliyor.
