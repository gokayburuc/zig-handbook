# Import Libraries

zig dili içerisinde proje içerisinde yer alan internal bir kütüphaneyi içe aktarmak import etmek istediğimizde aşağıdaki yazım biçimini kullanırız.

```zig
const std = @import("std");
const uv = @import("ultraviole.zig");

// import sub package
const parseInt = std.fmt.parseInt;
```

## External Library Import in Zig

zig dilinde internet üzerinde yer alan bir harici kütüphaneyi projeye dahil etmek istediğimizde daha farklı bir yol izleriz.

dışarıdan paket eklemek için öncelikle projede yer alan build.zig isimli dosyamıza ekleyeceğimiz kütüphane bilgilerini giriyoruz.

<!-- FIX: external package import to zig -->

```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    const exe = b.addExecutable(.{
        .name = "myapp",
        .root_source_file = .{ .path = "src/main.zig" },
        .target = b.standardTargetOptions(.{}),
        .optimize = b.standardOptimizeOption(.{}),
    });

    // Add external package
    const json_pkg = b.dependency("json", .{
        .url = "https://github.com/Hejsil/zig-json/archive/main.tar.gz",
    });

    exe.addModule("json", json_pkg.module("json"));

    exe.install();
}
```

Yukarıda dependency kısmına projenin yer aldığı yolu ve projenin tar.gz olarak ziplenmiş halini ekliyoruz.

- [ ] External Package import in Zig
