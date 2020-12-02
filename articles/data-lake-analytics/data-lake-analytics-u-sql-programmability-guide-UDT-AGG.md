---
title: Průvodce programovatelností u-SQL UDT a UDAGG pro Azure Data Lake
description: Přečtěte si o programovatelnosti U-SQL UDT a UDAGG v Azure Data Lake Analytics, abyste mohli vytvořit dobrý USQL skript.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: bf2e1e1bc30aeb3306d0a643eca4725d8765edac
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512615"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>Průvodce programovatelností U-SQL – UDT a UDAGG



## <a name="use-user-defined-types-udt"></a>Použít uživatelsky definované typy: UDT
Uživatelsky definované typy nebo UDT jsou další funkcí programovatelnosti U-SQL. UDT typu U-SQL funguje jako běžný uživatelsky definovaný typ jazyka C#. C# je jazyk silného typu, který umožňuje použití vestavěných a vlastních uživatelsky definovaných typů.

U-SQL nemůže implicitně serializovat nebo deserializovat libovolný UDT při předávání UDT mezi vrcholy v sadách řádků. To znamená, že uživatel musí zadat explicitní formátovací modul pomocí rozhraní IFormatter. To poskytuje U-SQL s metodami serializace a deserializace pro UDT.

> [!NOTE]
> Vestavěné extraktory U-SQL a modul pro výstupy aktuálně nemůžou serializovat nebo deserializovat data UDT do souborů i ze IFormatter sady. Takže pokud píšete data UDT do souboru s příkazem OUTPUT nebo je přečtete pomocí extraktoru, je nutné jej předat jako řetězec nebo pole bajtů. Pak zavoláte kód serializace a deserializace (tj. metodu ToString () objektu UDT explicitně. Uživatelem definované extrakce a výstupy můžou na druhé straně číst a zapisovat UDT.

Pokud se pokusíte použít UDT v EXTRAKTORu nebo v předvýrobním programu (z předchozího výběru), jak je znázorněno zde:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Zobrazí se následující chyba:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Aby bylo možné pracovat se systémem UDT v modulu pro výstup, je nutné jej serializovat na řetězec pomocí metody ToString () nebo vytvořit vlastní modul pro výstup.

UDT se v tuto chvíli nedá použít v GROUP BY. Pokud je v GROUP BY použit parametr UDT, je vyvolána následující chyba:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Abychom definovali UDT, musíme:

1. Přidejte následující obory názvů:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Přidat `Microsoft.Analytics.Interfaces` , který je požadován pro rozhraní UDT. Kromě toho `System.IO` může být potřeba definovat rozhraní IFormatter.

3. Definujte použitý typ s atributem SqlUserDefinedType.

**SqlUserDefinedType** se používá k označení definice typu v sestavení jako uživatelem definovaný typ (UDT) v U-SQL. Vlastnosti atributu odpovídají fyzickým vlastnostem UDT. Tuto třídu nelze zdědit.

SqlUserDefinedType je vyžadovaný atribut pro definici UDT.

Konstruktor třídy:  

* SqlUserDefinedTypeAttribute (formátování typu)

* Typ formátování: povinný parametr pro definování formátovacího modulu UDT – konkrétně typ `IFormatter` rozhraní musí být předán zde.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typický parametr UDT také vyžaduje definici rozhraní IFormatter, jak je znázorněno v následujícím příkladu:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`Rozhraní serializace a deserializace graf objektu s kořenovým typem \<typeparamref name="T"> .

\<typeparam name="T">Kořenový typ pro graf objektu pro serializaci a deserializaci.

* **Deserializace**: deserializovat data v zadaném datovém proudu a rekonstruovat graf objektů.

* **Serializace**: serializace objektu nebo grafu objektů s daným kořenem k poskytnutému datovému proudu.

`MyType` instance: instance typu.  
`IColumnWriter` zapisovač/ `IColumnReader` Čtenář: podkladový datový proud sloupce.  
`ISerializationContext` Context: Enum, který definuje sadu příznaků, které určují zdrojový nebo cílový kontext pro datový proud během serializace.

* **Intermediate**: Určuje, že zdrojový nebo cílový kontext není trvalé úložiště.

* **Persistence**: Určuje, zda je zdrojový nebo cílový kontext trvalým úložištěm.

Jako běžný typ C# může definice UDT U-SQL zahrnovat přepsání pro operátory, jako je +/= =/! =. Může také zahrnovat statické metody. Pokud například použijete tento typ UDT jako parametr pro agregační funkci U-SQL MIN, je nutné definovat < operátor override.

V předchozí části tohoto průvodce jsme ukázali příklad identifikace fiskálního období z konkrétního data ve formátu `Qn:Pn (Q1:P10)` . Následující příklad ukazuje, jak definovat vlastní typ pro hodnoty fiskálního období.

Následuje příklad oddílu kódu na pozadí s vlastním rozhraním UDT a IFormatter:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Definovaný typ obsahuje dvě čísla: čtvrtletí a měsíc. Operátory `==/!=/>/<` a statická metoda `ToString()` jsou zde definovány.

Jak bylo zmíněno dříve, je možné použít UDT ve výrazech SELECT, ale nelze je použít v nástroji pro výstup/EXTRAKCe bez vlastní serializace. Buď musí být serializován jako řetězec s použitím `ToString()` nebo s vlastním výstupem/extrakcí.

Teď se podíváme na použití UDT. V části s kódem na pozadí jsme změnili naši funkci GetFiscalPeriod na následující:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Jak vidíte, vrátí hodnotu našeho typu FiscalPeriod.

Zde uvádíme příklad, jak ho dál používat v základním skriptu U-SQL. Tento příklad ukazuje různé formy vyvolání UDT ze skriptu U-SQL.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Tady je příklad úplné části s kódem na pozadí:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Použít uživatelsky definované agregace: UDAGG
Uživatelsky definované agregace jsou jakékoli funkce související s agregací, které nejsou dodány předem pomocí U-SQL. Příkladem může být agregace pro provádění vlastních matematických výpočtů, zřetězení řetězců, manipulace s řetězci a tak dále.

Uživatelsky definovaná definice základní třídy agregace je následující:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** označuje, že typ by měl být registrován jako uživatelsky definovaná agregace. Tuto třídu nelze zdědit.

Atribut SqlUserDefinedType je pro definici UDAGG **volitelný** .


Základní třída umožňuje předat tři abstraktní parametry: dva jako vstupní parametry a jeden jako výsledek. Datové typy jsou proměnné a měly by být definovány během dědičnosti třídy.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** se vyvolá jednou pro každou skupinu během výpočtu. Poskytuje inicializační rutinu pro každou skupinu agregace.  
* **Shromažďování** je provedeno jednou pro každou hodnotu. Poskytuje hlavní funkce pro agregační algoritmus. Dá se použít k agregaci hodnot s různými datovými typy, které jsou definovány během dědičnosti třídy. Může přijmout dva parametry datových typů proměnných.
* **Ukončení** se provádí jednou na agregační skupinu na konci zpracování, aby se vytvářely výstup výsledku pro každou skupinu.

Chcete-li deklarovat správné vstupní a výstupní datové typy, použijte definici třídy následujícím způsobem:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: první parametr, který se má nashromáždit
* T2: druhý parametr ke shromáždění
* TResult: návratový typ ukončení

Příklad:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

nebo

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Použití UDAGG v U-SQL
Chcete-li použít UDAGG, nejprve ho definujte v kódu na pozadí nebo na něj odkázat z existující programovatelnosti knihovny DLL, jak je popsáno výše.

Pak použijte následující syntaxi:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Tady je příklad UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

A základní skript U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

V tomto scénáři použití jsou pro konkrétní uživatele zřetězeni identifikátory GUID tříd.

## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – přehled](data-lake-analytics-u-sql-programmability-guide.md)
* [Průvodce programovatelností U-SQL – UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
