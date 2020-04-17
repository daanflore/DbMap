# DbMap
Performance-oriented database-to-object mapper for .NET

## Install via NuGet
To install DbMap, run the following command in the Package Manager Console:

```cmd
PM> Install-Package DbMap
```

You can also view the package page on [Nuget](https://www.nuget.org/packages/DbMap/).

## Example usage

```c#

using DbMap;

class Program
{
    private static readonly DbQuery CustomerQuery = new DbQuery("SELECT Name FROM Customer WHERE CustomerId = @customerId");

    static void Main()
    {
        // TODO: Set connection string.
        var connectionString = "...";
    
        using var connection = new SqlConnection(connectionString);
        
        var customer = CustomerQuery.QuerySingleOrDefault<Customer>(connection, new { customerId = 10 });
        
        // TODO: Do something with customer.
        .
        .
        .    
    }

}
```


## Benchmarks
These benchmarks are focused on the deserialization performance of different object-relational mappers. 
Given differences should be interpreted as theoretical maximum. In reality your query, the network latency and the performance of your database server is more likely to affect performance.

Tested object mappers are:
- EFCore 3.1.3 LINQ query (non-tracking)
- EFCore 3.1.3 Interpolated SQL query (non-tracking)
- EFCore 3.1.3 Raw SQL query (non-tracking)
- EFCore 3.1.3 LINQ cached query (non-tracking)
- Dapper 2.0.35
- RepoDb 1.10.11
- DbMap 0.5.2
- Hand written 

``` ini
BenchmarkDotNet=v0.12.0, OS=Windows 10.0.18362
Intel Core i7-6600U CPU 2.60GHz (Skylake), 1 CPU, 4 logical and 2 physical cores
.NET Core SDK=3.1.101
  [Host]     : .NET Core 3.1.1 (CoreCLR 4.700.19.60701, CoreFX 4.700.19.60801), X64 RyuJIT
  Job-ZKXYTC : .NET Core 3.1.1 (CoreCLR 4.700.19.60701, CoreFX 4.700.19.60801), X64 RyuJIT
```


Tiny benchmark, query 1 row of 1 string column using 1 parameter
``` ini
|                 Method |      Mean |     Error |    StdDev | Ratio | RatioSD |
|----------------------- |----------:|----------:|----------:|------:|--------:|
|         EFCoreLinqTiny | 378.73 µs | 14.602 µs | 32.051 µs |  3.77 |    0.34 |
| EFCoreInterpolatedTiny | 295.35 µs | 10.352 µs | 22.723 µs |  2.93 |    0.23 |
|          EFCoreRawTiny | 295.40 µs | 11.132 µs | 24.898 µs |  2.97 |    0.24 |
| EFCoreCompliedLinqTiny | 168.56 µs |  1.652 µs |  3.484 µs |  1.68 |    0.04 |
|             DapperTiny | 144.21 µs |  1.178 µs |  2.560 µs |  1.43 |    0.03 |
|             RepoDbTiny | 110.61 µs |  0.503 µs |  1.039 µs |  1.10 |    0.02 |
|              DbMapTiny | 100.54 µs |  0.779 µs |  1.591 µs |  1.00 |    0.00 |
|        HandwrittenTiny |  99.64 µs |  0.360 µs |  0.752 µs |  0.99 |    0.02 |
```

Extra small benchmark, query 1 row of 6 columns using 1 parameter
``` ini
|                       Method |     Mean |    Error |   StdDev | Ratio | RatioSD |
|----------------------------- |---------:|---------:|---------:|------:|--------:|
|         EFCoreLinqExtraSmall | 348.8 µs | 13.48 µs | 29.59 µs |  3.40 |    0.31 |
| EFCoreInterpolatedExtraSmall | 286.7 µs |  7.17 µs | 15.88 µs |  2.80 |    0.17 |
|          EFCoreRawExtraSmall | 279.4 µs |  5.84 µs | 12.57 µs |  2.73 |    0.14 |
| EFCoreCompliedLinqExtraSmall | 173.1 µs |  1.79 µs |  3.85 µs |  1.69 |    0.05 |
|             DapperExtraSmall | 151.6 µs |  1.08 µs |  2.34 µs |  1.48 |    0.03 |
|             RepoDbExtraSmall | 123.6 µs |  1.17 µs |  2.45 µs |  1.21 |    0.03 |
|              DbMapExtraSmall | 102.5 µs |  0.80 µs |  1.66 µs |  1.00 |    0.00 |
|        HandwrittenExtraSmall | 102.0 µs |  0.48 µs |  1.06 µs |  1.00 |    0.02 |
```

Small benchmark, query 10 rows of 6 columns using 1 parameter
``` ini
|                  Method |     Mean |    Error |   StdDev |   Median | Ratio | RatioSD |
|------------------------ |---------:|---------:|---------:|---------:|------:|--------:|
|         EFCoreLinqSmall | 382.8 µs | 16.13 µs | 35.40 µs | 381.4 µs |  3.27 |    0.28 |
| EFCoreInterpolatedSmall | 279.5 µs |  4.84 µs | 10.10 µs | 278.0 µs |  2.36 |    0.09 |
|          EFCoreRawSmall | 283.5 µs |  9.54 µs | 21.35 µs | 281.6 µs |  2.39 |    0.18 |
| EFCoreCompliedLinqSmall | 201.2 µs |  3.38 µs |  7.42 µs | 203.9 µs |  1.70 |    0.06 |
|             DapperSmall | 173.0 µs |  1.84 µs |  3.95 µs | 173.5 µs |  1.46 |    0.04 |
|             RepoDbSmall | 128.1 µs |  0.97 µs |  2.00 µs | 127.9 µs |  1.08 |    0.02 |
|              DbMapSmall | 118.3 µs |  0.53 µs |  1.12 µs | 118.1 µs |  1.00 |    0.00 |
|        HandwrittenSmall | 115.7 µs |  0.43 µs |  0.90 µs | 115.6 µs |  0.98 |    0.01 |
```

Medium benchmark, query 100 rows of 10 columns using 5 parameters
``` ini
|                   Method |     Mean |    Error |   StdDev |   Median | Ratio | RatioSD |
|------------------------- |---------:|---------:|---------:|---------:|------:|--------:|
|         EFCoreLinqMedium | 616.5 µs | 18.87 µs | 40.62 µs | 601.4 µs |  1.92 |    0.20 |
| EFCoreInterpolatedMedium | 504.9 µs |  6.29 µs | 12.99 µs | 507.9 µs |  1.58 |    0.12 |
|          EFCoreRawMedium | 528.7 µs | 13.43 µs | 29.76 µs | 517.9 µs |  1.64 |    0.13 |
| EFCoreCompliedLinqMedium | 425.8 µs |  9.47 µs | 19.98 µs | 419.7 µs |  1.32 |    0.10 |
|             DapperMedium | 414.8 µs | 11.65 µs | 25.33 µs | 405.8 µs |  1.29 |    0.15 |
|             RepoDbMedium | 355.3 µs |  9.48 µs | 20.40 µs | 354.4 µs |  1.11 |    0.12 |
|              DbMapMedium | 322.5 µs | 12.69 µs | 26.49 µs | 313.0 µs |  1.00 |    0.00 |
|        HandwrittenMedium | 333.5 µs | 12.94 µs | 28.41 µs | 316.9 µs |  1.04 |    0.10 |
```

Large benchmark, query 1,000 rows of 22 columns using 10 parameters
``` ini
|                  Method |     Mean |     Error |    StdDev |   Median | Ratio | RatioSD |
|------------------------ |---------:|----------:|----------:|---------:|------:|--------:|
|         EFCoreLinqLarge | 3.649 ms | 0.0704 ms | 0.1486 ms | 3.579 ms |  1.23 |    0.07 |
| EFCoreInterpolatedLarge | 3.443 ms | 0.0381 ms | 0.0770 ms | 3.415 ms |  1.16 |    0.05 |
|          EFCoreRawLarge | 3.501 ms | 0.0583 ms | 0.1256 ms | 3.437 ms |  1.18 |    0.06 |
|             DapperLarge | 3.705 ms | 0.0911 ms | 0.1941 ms | 3.600 ms |  1.25 |    0.09 |
|             RepoDbLarge | 3.151 ms | 0.0599 ms | 0.1302 ms | 3.086 ms |  1.06 |    0.06 |
|              DbMapLarge | 2.968 ms | 0.0566 ms | 0.1219 ms | 2.899 ms |  1.00 |    0.00 |
|        HandwrittenLarge | 3.020 ms | 0.0672 ms | 0.1461 ms | 2.938 ms |  1.02 |    0.06 |
```

Extra large benchmark, query 10,000 rows of 22 columns using 10 parameters
``` ini
|                       Method |     Mean |    Error |   StdDev |   Median | Ratio | RatioSD |
|----------------------------- |---------:|---------:|---------:|---------:|------:|--------:|
|         EFCoreLinqExtraLarge | 42.79 ms | 0.710 ms | 1.528 ms | 42.11 ms |  1.26 |    0.07 |
| EFCoreInterpolatedExtraLarge | 41.64 ms | 0.812 ms | 1.765 ms | 40.79 ms |  1.23 |    0.08 |
|          EFCoreRawExtraLarge | 42.22 ms | 0.851 ms | 1.850 ms | 41.50 ms |  1.25 |    0.07 |
|             DapperExtraLarge | 42.71 ms | 0.652 ms | 1.375 ms | 41.97 ms |  1.26 |    0.06 |
|             RepoDbExtraLarge | 36.80 ms | 0.654 ms | 1.409 ms | 36.19 ms |  1.08 |    0.06 |
|              DbMapExtraLarge | 34.00 ms | 0.608 ms | 1.281 ms | 33.27 ms |  1.00 |    0.00 |
|        HandwrittenExtraLarge | 34.31 ms | 0.635 ms | 1.395 ms | 33.73 ms |  1.01 |    0.04 |

```
