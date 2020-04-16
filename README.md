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
EFCore 3.1.3 LINQ query (non-tracking)
EFCore 3.1.3 Interpolated SQL query (non-tracking)
EFCore 3.1.3 Raw SQL query (non-tracking)
EFCore 3.1.3 LINQ cached query (non-tracking)
Dapper 2.0.35
RepoDb 1.10.11
DbMap 0.5.2
Hand written 

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
|         EFCoreLinqTiny | 378.73 �s | 14.602 �s | 32.051 �s |  3.77 |    0.34 |
| EFCoreInterpolatedTiny | 295.35 �s | 10.352 �s | 22.723 �s |  2.93 |    0.23 |
|          EFCoreRawTiny | 295.40 �s | 11.132 �s | 24.898 �s |  2.97 |    0.24 |
| EFCoreCompliedLinqTiny | 168.56 �s |  1.652 �s |  3.484 �s |  1.68 |    0.04 |
|             DapperTiny | 144.21 �s |  1.178 �s |  2.560 �s |  1.43 |    0.03 |
|             RepoDbTiny | 110.61 �s |  0.503 �s |  1.039 �s |  1.10 |    0.02 |
|              DbMapTiny | 100.54 �s |  0.779 �s |  1.591 �s |  1.00 |    0.00 |
|        HandwrittenTiny |  99.64 �s |  0.360 �s |  0.752 �s |  0.99 |    0.02 |
```

Extra small benchmark, query 1 row of 6 solumns using 1 parameter
``` ini
|                       Method |     Mean |    Error |   StdDev | Ratio | RatioSD |
|----------------------------- |---------:|---------:|---------:|------:|--------:|
|         EFCoreLinqExtraSmall | 348.8 �s | 13.48 �s | 29.59 �s |  3.40 |    0.31 |
| EFCoreInterpolatedExtraSmall | 286.7 �s |  7.17 �s | 15.88 �s |  2.80 |    0.17 |
|          EFCoreRawExtraSmall | 279.4 �s |  5.84 �s | 12.57 �s |  2.73 |    0.14 |
| EFCoreCompliedLinqExtraSmall | 173.1 �s |  1.79 �s |  3.85 �s |  1.69 |    0.05 |
|             DapperExtraSmall | 151.6 �s |  1.08 �s |  2.34 �s |  1.48 |    0.03 |
|             RepoDbExtraSmall | 123.6 �s |  1.17 �s |  2.45 �s |  1.21 |    0.03 |
|              DbMapExtraSmall | 102.5 �s |  0.80 �s |  1.66 �s |  1.00 |    0.00 |
|        HandwrittenExtraSmall | 102.0 �s |  0.48 �s |  1.06 �s |  1.00 |    0.02 |
```

Small benchmark, query 10 rows of 6 columns using 1 parameter
``` ini
|                  Method |     Mean |    Error |   StdDev |   Median | Ratio | RatioSD |
|------------------------ |---------:|---------:|---------:|---------:|------:|--------:|
|         EFCoreLinqSmall | 382.8 �s | 16.13 �s | 35.40 �s | 381.4 �s |  3.27 |    0.28 |
| EFCoreInterpolatedSmall | 279.5 �s |  4.84 �s | 10.10 �s | 278.0 �s |  2.36 |    0.09 |
|          EFCoreRawSmall | 283.5 �s |  9.54 �s | 21.35 �s | 281.6 �s |  2.39 |    0.18 |
| EFCoreCompliedLinqSmall | 201.2 �s |  3.38 �s |  7.42 �s | 203.9 �s |  1.70 |    0.06 |
|             DapperSmall | 173.0 �s |  1.84 �s |  3.95 �s | 173.5 �s |  1.46 |    0.04 |
|             RepoDbSmall | 128.1 �s |  0.97 �s |  2.00 �s | 127.9 �s |  1.08 |    0.02 |
|              DbMapSmall | 118.3 �s |  0.53 �s |  1.12 �s | 118.1 �s |  1.00 |    0.00 |
|        HandwrittenSmall | 115.7 �s |  0.43 �s |  0.90 �s | 115.6 �s |  0.98 |    0.01 |
```

Medium benchmark, query 100 rows of 10 columns using 5 parameters
``` ini
|                   Method |     Mean |    Error |   StdDev |   Median | Ratio | RatioSD |
|------------------------- |---------:|---------:|---------:|---------:|------:|--------:|
|         EFCoreLinqMedium | 616.5 �s | 18.87 �s | 40.62 �s | 601.4 �s |  1.92 |    0.20 |
| EFCoreInterpolatedMedium | 504.9 �s |  6.29 �s | 12.99 �s | 507.9 �s |  1.58 |    0.12 |
|          EFCoreRawMedium | 528.7 �s | 13.43 �s | 29.76 �s | 517.9 �s |  1.64 |    0.13 |
| EFCoreCompliedLinqMedium | 425.8 �s |  9.47 �s | 19.98 �s | 419.7 �s |  1.32 |    0.10 |
|             DapperMedium | 414.8 �s | 11.65 �s | 25.33 �s | 405.8 �s |  1.29 |    0.15 |
|             RepoDbMedium | 355.3 �s |  9.48 �s | 20.40 �s | 354.4 �s |  1.11 |    0.12 |
|              DbMapMedium | 322.5 �s | 12.69 �s | 26.49 �s | 313.0 �s |  1.00 |    0.00 |
|        HandwrittenMedium | 333.5 �s | 12.94 �s | 28.41 �s | 316.9 �s |  1.04 |    0.10 |
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