---
title: Uživatelsky definovaný Applier Průvodce programovatelností u-SQL pro Azure Data Lake
description: Přečtěte si o příručce pro programovatelnost U-SQL UDO – uživatelsky definované Applier.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512537"
---
# <a name="use-user-defined-applier"></a>Použití uživatelsky definovaného objektu applier 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO: uživatelsky definované Applier
Uživatelsky definované Applier U-SQL vám umožní vyvolat vlastní funkci jazyka C# pro každý řádek, který je vrácený výrazem vnější tabulky dotazu. Správný vstup je vyhodnocen pro každý řádek od levého vstupu a řádky, které jsou vytvořeny, jsou zkombinovány pro konečný výstup. Seznam sloupců, které jsou vytvořeny pomocí operátoru APPLy, je kombinací sady sloupců vlevo a správného vstupu.


## <a name="how-to-define-and-use-user-defined-applier"></a>Definování a použití uživatelsky definované Applier
Uživatelem definované Applier je vyvoláno jako součást výrazu SELECT USQL.

Typické volání uživatelsky definovaného Applier vypadá takto:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Další informace o použití appliers ve výrazu SELECT najdete v části [U-SQL vyberte možnost vybrat z křížového a vnějšího aplikování](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Uživatelsky definovaná definice základní třídy Applier je následující:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Pro definování uživatelsky definovaného Applier musíme vytvořit `IApplier` rozhraní s `SqlUserDefinedApplier` atributem [], který je volitelný pro uživatelsky definovanou definici Applier.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Pro každý řádek vnější tabulky je volána metoda Apply. Vrátí `IUpdatableRow` výstupní sadu řádků.
* Třída konstruktoru se používá k předání parametrů uživatelsky definovanému Applier.

**SqlUserDefinedApplier** označuje, že typ by měl být zaregistrován jako uživatelsky definovaný Applier. Tuto třídu nelze zdědit.

**SqlUserDefinedApplier** je **volitelná** pro uživatelsky definovanou definici Applier.


Hlavními objekty programovatelnosti jsou tyto:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Vstupní sady řádků jsou předány jako `IRow` vstup. Výstupní řádky jsou generovány jako `IUpdatableRow` výstupní rozhraní.

Jednotlivé názvy sloupců lze určit voláním `IRow` metody schématu.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

K získání skutečných hodnot dat z příchozího `IRow` používání používáme metodu get () `IRow` rozhraní.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Nebo používáme název sloupce schématu:

```csharp
row.Get<int>(row.Schema[0].Name)
```

Výstupní hodnoty musí být nastaveny s `IUpdatableRow` výstupem:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Je důležité pochopit, že vlastní appliers výstupní sloupce a hodnoty, které jsou definovány pomocí `output.Set` volání metody.

Skutečný výstup je aktivován voláním `yield return output.AsReadOnly();` .

Uživatelsky definované parametry Applier lze předat konstruktoru. Applier může vrátit proměnný počet sloupců, které je třeba definovat během volání Applier v základní skriptu U-SQL.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Tady je příklad uživatelsky definovaného Applier:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Následuje základní skript U-SQL pro tento uživatelsky definovaný Applier:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

V tomto scénáři použití uživatelsky definované Applier funguje jako analyzátor hodnot oddělených čárkami pro vlastnosti loďstva auta. Řádky vstupních souborů vypadají jako následující:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Jedná se o typický soubor TSV s oddělovači se sloupcem vlastností, který obsahuje vlastnosti auta, jako je například značka a model. Tyto vlastnosti musí být analyzovány do sloupců tabulky. Applier, který je k dispozici, také umožňuje generovat dynamický počet vlastností ve výsledné sadě řádků na základě předaného parametru. Můžete vygenerovat jenom všechny vlastnosti nebo konkrétní sadu vlastností.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

Uživatelsky definované Applier lze volat jako novou instanci objektu Applier:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Nebo s voláním metody vytváření obálky:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – přehled](data-lake-analytics-u-sql-programmability-guide.md)
* [Průvodce programovatelností U-SQL – UDT a UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)