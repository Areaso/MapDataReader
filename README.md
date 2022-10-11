# MapDataReader
Super fast mapping DataReader to a strongly typed object, Using AOT source generator, no reflection, mapping code is generated at runtime.

## Benchmarks

20X faster than using reflection (`GetProperties` for a type then `Invoke` the setters), even with caching. Benchmark for a tiny class with 5 string properties:

| Method         |      Mean |     Error |   StdDev |   Gen0 | Allocated |
|--------------- |----------:|----------:|---------:|-------:|----------:|
|  Reflection    | 951.16 ns | 15.107 ns | 0.828 ns | 0.1459 |     920 B |
|  MapDataReader |  44.15 ns |  2.840 ns | 0.156 ns | 0.0089 |      56 B |

## Install

```
Install-Package MapDataReader
```

## Usage

```csharp
[GenerateDataReaderMapper]
public class MyClass
{
	public int ID { get; set; }
	public string Name { get; set; }
	public int Size { get; set; }
	public bool Enabled { get; set; }
}

List<MyClass> result = dbconnection.ExecuteReader("SELECT * FROM MyTable").ToMyClass();
```

## Some notes

* The `ToMyClass()` method above - is an etension method generated at compile time.
* The naming convention is `ToCLASSNAME()` we can't use generics here, since `<T>` is not part of method signatures in C# (considered in later versions of C#)
* Maps properies with public setters only.
* The reader is being closed after mapping, so don't reuse it.
* The example above uses Dapper's `.ExecuteReader` method, but you can generate the reader in any way you want, e.g. `SqlCommand.ExecuteReader` will also work.
* Supports `enum` properties based on `int` and other implicit casting (sometimes a DataReader may decide to return `byte` for small integer database value, and it maps to `int` perfectly via some unboxing magic)
* Complex-type properties may not work.
* netstandard 2.0
