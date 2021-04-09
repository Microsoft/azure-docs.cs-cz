---
title: Průvodce programovatelností u-SQL definovaného uživatelem pro Azure Data Lake
description: Přečtěte si informace o příručce pro programovatelnost U-SQL UDO uživatelsky definované uživatelem.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512534"
---
# <a name="use-user-defined-outputter"></a>Použití uživatelsky definovaného objektu outputter

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO: uživatelsky definovaného výstupu
Uživatelem definovaný modul pro výstup je jiný UDO U-SQL, který umožňuje rozšířené integrované funkce U-SQL. Podobně jako u extraktoru existuje několik vestavěných výstupů.

* Výstupy *. text ()*: zapisuje data do textových souborů s oddělovači různých kódování.
* *Outputters.Csv ()*: zapisuje data do souborů hodnot oddělených čárkami (CSV) různých kódování.
* Výstupy *. TSV ()*: zapisuje data do souborů hodnot oddělených tabulátory (TSV) různých kódování.

Vlastní Provisioning umožňuje zapisovat data do vlastního definovaného formátu. To může být užitečné pro následující úlohy:

* Zápis dat do částečně strukturovaných nebo nestrukturovaných souborů.
* Zápis dat s nepodporovanými kódováními.
* Úprava výstupních dat nebo přidávání vlastních atributů.

## <a name="how-to-define-and-use-user-defined-outputter"></a>Definování a použití uživatelsky definovaného výstupu
Pro definování uživatelsky definovaného výstupu je potřeba vytvořit `IOutputter` rozhraní.

Následující je implementace základní `IOutputter` třídy:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Všechny vstupní parametry, jako jsou například oddělovače sloupců nebo řádků, kódování a tak dále, musí být definovány v konstruktoru třídy. `IOutputter`Rozhraní by mělo obsahovat také definici pro `void Output` přepsání. Atribut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` může být volitelně nastaven pro zpracování atomických souborů. Další informace najdete v následujících podrobnostech.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` je volána pro každý vstupní řádek. Vrátí `IUnstructuredWriter output` sadu řádků.
* Třída konstruktoru se používá k předání parametrů do uživatelsky definovaného výstupu.
* `Close` slouží k volitelnému přepsání pro vydání nákladného stavu nebo určení, kdy byl poslední řádek napsán.

Atribut **SqlUserDefinedOutputter** označuje, že typ by měl být zaregistrován jako uživatelsky definovaný modul pro registraci. Tuto třídu nelze zdědit.

SqlUserDefinedOutputter je volitelný atribut pro uživatelsky definovanou definici výstupu. Slouží k definování vlastnosti AtomicFileProcessing.

* bool AtomicFileProcessing   

* **true** = znamená, že tento dokument vyžaduje atomické výstupní soubory (JSON, XML,...).
* **false** = znamená, že tento soubor k tomuto výstupu může pracovat s rozdělenými a distribuovanými soubory (CSV, SEQ,...)

Hlavními objekty programovatelnosti jsou **řádek** a **výstup**. Objekt **Row** se používá k zobrazení výčtu výstupních dat jako `IRow` rozhraní. **Výstup** se používá k nastavení výstupních dat do cílového souboru.

Výstupní data jsou k dispozici prostřednictvím `IRow` rozhraní. Výstupní data jsou úspěšně předána řádku.

Jednotlivé hodnoty jsou vyčísleny voláním metody Get rozhraní IRow:

```csharp
row.Get<string>("column_name")
```

Jednotlivé názvy sloupců lze určit voláním `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Tento přístup umožňuje vytvořit flexibilní výstup pro jakékoli schéma metadat.

Výstupní data jsou zapsána do souboru pomocí `System.IO.StreamWriter` . Parametr Stream je nastaven `output.BaseStream` jako součást `IUnstructuredWriter output` .

Všimněte si, že pro každou iteraci řádku je důležité vyprázdnit vyrovnávací paměť dat do souboru. Kromě toho `StreamWriter` musí být objekt použit s povoleným atributem na jedno použití (výchozí) a s klíčovým slovem **using** :

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

V opačném případě volání metody Flush () explicitně po každé iteraci. V následujícím příkladu to ukážeme.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Nastavení hlaviček a zápatí pro uživatelem definovaný výstup
Chcete-li nastavit hlavičku, použijte tok spuštění s jednou iterací.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Kód v prvním `if (isHeaderRow)` bloku je proveden pouze jednou.

Pro zápatí použijte odkaz na instanci `System.IO.Stream` objektu ( `output.BaseStream` ). Zapsat zápatí v metodě Close () `IOutputter` rozhraní.  (Další informace najdete v následujícím příkladu.)

Následuje příklad uživatelsky definovaného výstupu:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

A základní skript U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Toto je výstup HTML, který vytvoří soubor HTML s daty tabulky.

### <a name="call-outputter-from-u-sql-base-script"></a>Volání výstupu z základního skriptu U-SQL
Chcete-li volat vlastní modul pro výstup z základního skriptu U-SQL, je nutné vytvořit novou instanci objektu provýstupu.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Aby nedošlo k vytváření instancí objektu v základním skriptu, můžeme vytvořit obálku funkce, jak je znázorněno v našem příkladu:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

V takovém případě původní volání vypadá takto:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – přehled](data-lake-analytics-u-sql-programmability-guide.md)
* [Průvodce programovatelností U-SQL – UDT a UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)