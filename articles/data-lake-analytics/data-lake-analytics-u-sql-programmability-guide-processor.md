---
title: Příručka k programovatelnosti definované uživatelem u-SQL pro Azure Data Lake
description: Přečtěte si příručku k programovatelnosti U-SQL UDO – uživatelsky definovaný procesor.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512599"
---
# <a name="use-user-defined-processor"></a>Použití uživatelsky definovaného objektu processor

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO: uživatelsky definovaný procesor
Uživatelsky definovaný procesor nebo UDP je typ U-SQL UDO, který umožňuje zpracovat příchozí řádky pomocí funkcí programovatelnosti. Protokol UDP umožňuje kombinovat sloupce, upravit hodnoty a v případě potřeby přidávat nové sloupce. V podstatě pomáhá zpracovat sadu řádků a vytvořit požadované datové prvky.

## <a name="how-to-define-and-use-user-defined-processor"></a>Definování a použití uživatelsky definovaného procesoru
Abychom mohli definovat UDP, musíme vytvořit `IProcessor` rozhraní s `SqlUserDefinedProcessor` atributem, který je volitelný pro UDP.

Toto rozhraní by mělo obsahovat definici `IRow` přepsání sady řádků rozhraní, jak je znázorněno v následujícím příkladu:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** označuje, že typ by měl být registrován jako uživatelsky definovaný procesor. Tuto třídu nelze zdědit.

Atribut SqlUserDefinedProcessor je pro definici protokolu UDP **volitelný** .

Hlavními objekty programovatelnosti jsou **vstup** a **výstup**. Vstupní objekt se používá k vytvoření výčtu vstupních sloupců a výstupu a k nastavení výstupních dat v důsledku aktivity procesoru.

Pro výčet vstupních sloupců používáme `input.Get` metodu.

```csharp
string column_name = input.Get<string>("column_name");
```

Parametr pro `input.Get` metodu je sloupec, který je předán jako součást `PRODUCE` klauzule `PROCESS` příkazu základního skriptu U-SQL. V tomto případě musíme použít správný datový typ.

Pro výstup použijte `output.Set` metodu.

Je důležité si uvědomit, že vlastní producent pouze výstupy sloupců a hodnot, které jsou definovány pomocí `output.Set` volání metody.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

Skutečný výstup procesoru je aktivován voláním `return output.AsReadOnly();` .

Následuje příklad procesoru:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

V tomto scénáři použití vygeneruje procesor nový sloupec s názvem "full_description" tak, že kombinuje existující sloupce – v tomto případě "User" v případě velkých písmen a "des". Také znovu vygeneruje identifikátor GUID a vrátí původní a nové hodnoty GUID.

Jak vidíte v předchozím příkladu, můžete volat metody jazyka C# během `output.Set` volání metody.

Následuje příklad základního skriptu U-SQL, který používá vlastní procesor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – přehled](data-lake-analytics-u-sql-programmability-guide.md)
* [Průvodce programovatelností U-SQL – UDT a UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)