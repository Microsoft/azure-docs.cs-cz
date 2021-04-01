---
title: Průvodce programovatelností u-SQL definovaný uživatelem pro Azure Data Lake
description: Seznamte se s průvodcem pro programovatelnost U-SQL UDO – uživatelsky definovaným omezením.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512533"
---
# <a name="use-user-defined-reducer"></a>Použití uživatelsky definovaného objektu reducer

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO: uživatelsky definované redukce

U-SQL umožňuje napsat vlastní sadu řádků reduktorů v jazyce C# pomocí rozhraní rozšiřitelnosti uživatelsky definovaného operátoru a implementací rozhraní IReducer.

Uživatelsky definované zpomalení nebo UDR lze použít k eliminaci zbytečných řádků při extrakci dat (import). Dá se taky použít k manipulaci a vyhodnocení řádků a sloupců. Na základě logiky programovatelnosti může také definovat, které řádky je třeba extrahovat.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Definování a použití uživatelsky definovaného snížení
Pro definování třídy UDR musíme vytvořit `IReducer` rozhraní s nepovinným `SqlUserDefinedReducer` atributem.

Toto rozhraní třídy by mělo obsahovat definici pro `IEnumerable` přepsání rozhraní sady řádků.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Atribut **SqlUserDefinedReducer** označuje, že typ by měl být registrován jako uživatelsky definovaným omezením. Tuto třídu nelze zdědit.
**SqlUserDefinedReducer** je volitelný atribut pro definici zúžení definované uživatelem. Slouží k definování rekurzivní vlastnosti.

* logická hodnota je nerekurzivní    
* **true**  = určuje, zda je tento redukce asociativní a komutativní

Hlavními objekty programovatelnosti jsou **vstup** a **výstup**. Vstupní objekt se používá k vytvoření výčtu vstupních řádků. Výstup se používá k nastavení výstupních řádků v důsledku snížení aktivity.

Pro výčet vstupních řádků používáme `Row.Get` metodu.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr pro `Row.Get` metodu je sloupec, který se předává jako součást `PRODUCE` třídy `REDUCE` příkazu základního skriptu U-SQL. Je potřeba použít taky správný datový typ.

Pro výstup použijte `output.Set` metodu.

Je důležité pochopit, že vlastní redukce má jenom hodnoty, které jsou definované pomocí `output.Set` volání metody.

```csharp
output.Set<string>("mycolumn", guid);
```

Skutečný výstup pro redukci se aktivuje voláním metody `yield return output.AsReadOnly();` .

Následuje příklad pro snížení:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

V tomto scénáři použití vynechává zpomalení řádků prázdné uživatelské jméno. Pro každý řádek v sadě řádků načte všechny požadované sloupce a pak vyhodnotí délku uživatelského jména. Vlastní řádek vypíše pouze v případě, že je hodnota délka uživatelského jména větší než 0.

Následuje základní skript U-SQL, který používá vlastní redukci:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – přehled](data-lake-analytics-u-sql-programmability-guide.md)
* [Průvodce programovatelností U-SQL – UDT a UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)