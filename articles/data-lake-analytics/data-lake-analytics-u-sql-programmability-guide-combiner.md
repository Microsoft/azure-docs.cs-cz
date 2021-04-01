---
title: Uživatelsky definovaná příručka pro programovatelnost u-SQL pro Azure Data Lake
description: Seznamte se s příručkou pro programovatelnost U-SQL UDO – uživatelsky definovaná kombinovaná.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512600"
---
# <a name="use-user-defined-combiner"></a>Použití uživatelsky definovaného objektu combiner

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL UDO: uživatelsky definovaný kombinující
Uživatelsky definovaná kombinovaná nebo UDC umožňuje kombinovat řádky z levé a pravé sady řádků na základě vlastní logiky. V kombinaci s výrazem kombinovat se používá uživatelsky definovaný kombinace.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Definování a použití uživatelsky definované kombinace

Kombinování je vyvoláno pomocí výrazu kombinace, který poskytuje potřebné informace o vstupních sadách řádků, sloupcích seskupení, očekávaném schématu výsledků a dalších informacích.

Pro volání kombinovaného skriptu U-SQL používáme následující syntaxi:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Další informace najdete v tématu [kombinování výrazu (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Pro definování uživatelsky definované kombinace musíme vytvořit `ICombiner` rozhraní s `SqlUserDefinedCombiner` atributem [], který je volitelný pro uživatelsky definovanou definici kombinování.

`ICombiner`Definice základní třídy:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Vlastní implementace `ICombiner` rozhraní by měla obsahovat definici pro `IEnumerable<IRow>` přepsání kombinování.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Atribut **SqlUserDefinedCombiner** označuje, že typ by měl být registrován jako uživatelsky definovaný modul pro kombinování. Tuto třídu nelze zdědit.

**SqlUserDefinedCombiner** se používá k definování vlastnosti kombinovaného režimu. Je to volitelný atribut pro uživatelsky definovanou definici kombinování.

CombinerMode režim

Výčet CombinerMode může mít následující hodnoty:

* Úplné (0) každý výstupní řádek potenciálně závisí na všech vstupních řádcích zleva a vpravo se stejnou hodnotou klíče.

* Left (1) každý výstupní řádek závisí na jednom vstupním řádku zleva (a potenciálně všechny řádky napravo se stejnou hodnotou klíče).

* Right (2) každý výstupní řádek závisí na jednom vstupním řádku zprava (a potenciálně všechny řádky nalevo se stejnou hodnotou klíče).

* Vnitřní (3) každý výstupní řádek závisí na jednom vstupním řádku vlevo a vpravo se stejnou hodnotou.

Příklad: [ `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` ]


Hlavními objekty programovatelnosti jsou:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Vstupní sady řádků jsou předány jako **levý** a **pravý** `IRowset` typ rozhraní. Pro zpracování se musí vytvořit výčet obou sad řádků. Každé rozhraní můžete vytvořit pouze jednou, takže je nutné v případě potřeby vytvořit výčet a Uložit do mezipaměti.

Pro účely ukládání do mezipaměti můžeme vytvořit seznam \<T\> struktury paměti jako výsledek provedení dotazu LINQ, konkrétně seznam<`IRow`>. Anonymní datový typ lze použít také při výčtu.

Další informace o dotazech LINQ a [ \<T\> rozhraní IEnumerable](/dotnet/api/system.collections.generic.ienumerable-1) pro další informace o rozhraní IEnumerable naleznete v tématu [Úvod do dotazů LINQ (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) \<T\> .

K získání skutečných hodnot dat z příchozího `IRowset` používání používáme metodu get () `IRow` rozhraní.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Jednotlivé názvy sloupců lze určit voláním `IRow` metody schématu.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Nebo použijte název sloupce schématu:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

Obecný výčet s LINQ vypadá takto:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po vytvoření výčtu obou sad řádků projdeme smyčkou všemi řádky. Pro každý řádek v levé sadě řádků budeme najít všechny řádky, které splňují podmínku našeho kombinovaného doplňku.

Výstupní hodnoty musí být nastaveny s `IUpdatableRow` výstupem.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Skutečný výstup je aktivován voláním metody `yield return output.AsReadOnly();` .

Následuje příklad kombinovaného:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

V tomto scénáři použití je sestavování analytické sestavy pro prodejce. Cílem je najít všechny produkty, které jsou vyšší než $20 000 a které procházejí webem rychleji než prostřednictvím pravidelného maloobchodníka v určitém časovém období.

Tady je základní skript U-SQL. Logiku můžete porovnat mezi pravidelným SPOJENÍm a kombinací:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Uživatelsky definovaný kombiner lze volat jako novou instanci objektu Applier:

```csharp
USING new MyNameSpace.MyCombiner();
```


Nebo s voláním metody vytváření obálky:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – přehled](data-lake-analytics-u-sql-programmability-guide.md)
* [Průvodce programovatelností U-SQL – UDT a UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)