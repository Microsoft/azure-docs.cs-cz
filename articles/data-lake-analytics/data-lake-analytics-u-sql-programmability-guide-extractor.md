---
title: Průvodce programovatelností u-SQL definovaný uživatelem pro Azure Data Lake
description: Přečtěte si o příručce pro programovatelnost U-SQL UDO – uživatelsky definované extrakce.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a3613c2b95f13e6bbaaf570f522ad1f7b7edd756
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512591"
---
# <a name="use-user-defined-extractor"></a>Použít uživatelem definovaný extraktor

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO: uživatelem definovaný extraktor
U-SQL umožňuje importovat externí data pomocí příkazu EXTRACT. Příkaz k EXTRAKCi může používat vestavěné extraktory UDO:  

* *Extraktory. text ()*: poskytuje extrakci z textových souborů s oddělovači různých kódování.

* *Extractors.Csv ()*: poskytuje extrakci ze souborů hodnot oddělených čárkami (CSV) různých kódování.

* *Extraktory. TSV ()*: poskytuje extrakci ze souborů hodnot oddělených tabulátory (TSV) různých kódování.

Může být užitečné pro vývoj vlastního extraktoru. To může být užitečné při importu dat, pokud chceme udělat některý z následujících úkolů:

* Upravte vstupní data rozdělením sloupců a úpravou jednotlivých hodnot. Funkce procesoru je lepší pro kombinování sloupců.
* Analyzujte nestrukturovaná data, jako jsou webové stránky a e-maily, nebo částečně nestrukturovaná data, jako je XML/JSON.
* Analyzovat data v nepodporovaném kódování.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Definování a použití uživatelem definovaného extraktoru
Pro definování uživatelsky definovaného extraktoru nebo OUČIT musíme vytvořit `IExtractor` rozhraní. Všechny vstupní parametry pro extraktor, jako jsou například oddělovače sloupců nebo řádků a kódování, musí být definovány v konstruktoru třídy. `IExtractor`Rozhraní by mělo také obsahovat definici pro přepsání následujícím `IEnumerable<IRow>` způsobem:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Atribut **SqlUserDefinedExtractor** označuje, že typ by měl být registrován jako uživatelem definovaný extraktor. Tuto třídu nelze zdědit.

SqlUserDefinedExtractor je volitelný atribut pro definici OUČIT. Slouží k definování vlastnosti AtomicFileProcessing pro objekt OUČIT.

* bool AtomicFileProcessing   

* **true** = označuje, že tento extraktor vyžaduje atomické vstupní soubory (JSON, XML,...)
* **false** = znamená, že tento extraktor může pracovat s rozdělenými a distribuovanými soubory (CSV, SEQ,...)

Hlavními objekty programovatelnosti OUČIT jsou **vstupy** a **výstupy**. Vstupní objekt se používá k vytvoření výčtu vstupních dat jako `IUnstructuredReader` . Výstupní objekt se používá k nastavení výstupních dat v důsledku aktivity extraktoru.

Vstupní data jsou k dispozici prostřednictvím `System.IO.Stream` a `System.IO.StreamReader` .

Pro výčet vstupních sloupců nejprve rozdělí vstupní datový proud pomocí oddělovače řádků.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Pak dále rozdělte vstupní řádek na části sloupce.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

K nastavení výstupních dat používáme `output.Set` metodu.

Je důležité pochopit, že vlastní extraktor pouze výstupuje sloupce a hodnoty, které jsou definovány s výstupem. Nastavte volání metody.

```csharp
output.Set<string>(count, part);
```

Skutečný výstup extraktoru se aktivuje voláním `yield return output.AsReadOnly();` .

Následuje příklad extrakce:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

V tomto scénáři použití extraktor znovu vygeneruje GUID pro sloupec "GUID" a převede hodnoty sloupce "uživatel" na velká písmena. Vlastní extraktory můžou díky analýze vstupních dat a manipulaci s nimi způsobovat složitější výsledky.

Následuje základní skript U-SQL, který používá vlastní extraktor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – přehled](data-lake-analytics-u-sql-programmability-guide.md)
* [Průvodce programovatelností U-SQL – UDT a UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)