# Data Types

- [ ] Buradaki notu ufak parçalara böl her bir notu bir zettel haline getir

# Zig Veri Türleri

Got it. In Zig, the type system is small but composable — “complex” types are mostly built out of primitives with modifiers like `?`, `*`, `[N]`, `[]`, `!`, etc.

Here’s a clear breakdown:

---

## **Primitive Types**

### Integers

- `uN` → unsigned integer, N bits (`u1`, `u8`, `u16`, `u32`, `u64`, `u128`).
- `iN` → signed integer, N bits (`i8`, `i16`, `i32`, `i64`, `i128`).
- `isize`, `usize` → pointer-sized signed/unsigned integers.

### Floating-Point

- `f16`, `f32`, `f64`, `f80`, `f128` (platform dependent).

### Boolean

- `bool`

### Void and Nullish

- `void` → no value.
- `null` → represents "no value" when wrapped in optional.
- `undefined` → uninitialized value (not a type, but literal).

### Special Compile-Time Types

- `comptime_int` → arbitrary precision integer at compile time.
- `comptime_float` → arbitrary precision float at compile time.
- `type` → the type of types.
- `anytype` → placeholder for generic parameters.
- `noreturn` → function never returns.

---

## **Composite / Complex Types**

These are built from primitives with type operators:

### Pointers

- `*T` → pointer to `T`.
- `*const T` → pointer to immutable `T`.
- `[*]T` → many-item pointer (like C pointer, length not tracked).
- `[]T` → slice (pointer + length).
- `[N]T` → fixed-size array.
- `[:N]T` → sentinel-terminated array.

### Optionals

- `?T` → either `null` or value of type `T`.

### Error Unions

- `!T` → either error or value of type `T`.

### Structs

- `struct { field1: T1, field2: T2, ... }`.

### Enums

- `enum { A, B, C }`.
- `enum(u8) { A, B, C }` → tagged enum with explicit integer backing.

### Unions

- `union(enum) { A: T1, B: T2 }` → tagged union (sum type).
- `union { A: T1, B: T2 }` → untagged union.

### Opaque

- `opaque {}` → forward-declared, hidden layout (used for C interop).

### Functions

- `fn (params) ReturnType` → function type.

---

## **Special Built-in Types for Interop**

- `c_int`, `c_uint`, `c_char`, etc. → C ABI compatible types.
- `anyerror` → universal error set.

---

⚡ Key idea: Zig doesn’t have dozens of “special” types. It has a **small set of primitives** + **composable wrappers** (pointer, optional, error union, arrays). That’s why a type like `?*const [18]u8` feels dense — it’s just stacking 3 wrappers (`?`, `*const`, `[N]`) around a `u8`.

Do you want me to make a **visual tree/diagram** of how these types build on each other? It makes the “lego blocks” clearer.
