---
title: Použití sady .NET SDK – Azure HDInsight
description: Pomocí sady .NET SDK vytvářet a odstraňovat tabulky a číst a zapisovat data.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: 89c74b0c2144776d3bbc8a87f660b546ad40987f
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495412"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Použití sady .net SDK pro Apache HBase

[Apache HBase](apache-hbase-overview.md) poskytuje dvě primární možnosti pro práci s daty: [dotazy Apache Hive a volání rozhraní RESTful API HBase na](apache-hbase-tutorial-get-started-linux.md). Může spolupracovat přímo s použitím rozhraní REST API `curl` příkaz nebo podobného nástroje.

Pro aplikace C# a .NET [Microsoft HBase REST Client Library pro .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) poskytuje knihovna klienta nad HBase REST API.

## <a name="install-the-sdk"></a>Instalace sady SDK

Sady .NET SDK je k dispozici jako balíček NuGet, který si můžete nainstalovat pomocí sady Visual Studio **Konzola správce balíčků NuGet** pomocí následujícího příkazu:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Vytvoření instance nového objektu HBaseClient

Použití sady SDK pro vytvoření instance nového `HBaseClient` objekt předávajícího `ClusterCredentials` skládá z `Uri` váš cluster Hadoop uživatelské jméno a heslo.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Nahraďte název clusteru s názvem clusteru HDInsight HBase a uživatelské jméno a heslo zadané při vytvoření clusteru Apache Hadoop přihlašovací údaje. Je výchozí uživatelské jméno Hadoop **správce**.

## <a name="create-a-new-table"></a>Vytvořit novou tabulku

HBase ukládá data v tabulkách. Tabulka se skládá z *Rowkey*, primární klíč a jednu nebo více skupin sloupců se říká *rodin sloupců*. Data v každé tabulce vodorovně distribuovaná podle rozsahu Rowkey do *oblastech*. Každá oblast má počáteční a koncové klíč. Tabulka může mít jednu nebo více oblastech. S růstem data v tabulce HBase velké oblasti rozdělí menší regiony. Oblasti jsou uloženy v *oblastní servery*, kde jeden server oblasti můžete ukládat víc oblastí.

Jsou data uložená ve fyzicky *HFiles*. Jeden hfile – obsahuje data pro jednu tabulku, jedné oblasti a jedné rodiny sloupců. Řádky v hfile – ukládají, seřazená podle Rowkey. Má každý hfile – *B + stromu* index pro rychlé načítání řádků.

Chcete-li vytvořit novou tabulku, zadejte `TableSchema` a sloupce. Následující kód zkontroluje, zda je vytvořen v tabulce "RestSDKTable" již existuje – v opačném případě v tabulce.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Tato nová tabulka obsahuje dva sloupce rodiny, t1 a t2. Protože rodin sloupců jsou uloženy odděleně v různých HFiles, je vhodné mít samostatný sloupec řady pro data často poslal dotaz. V následujícím [dat vložte](#insert-data) například sloupce jsou přidány do rodiny sloupců t1.

## <a name="delete-a-table"></a>Odstranění tabulky

Pokud chcete odstranit tabulku:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Vložení dat

K vložení dat, můžete zadat klíč jedinečném řádku jako identifikátor řádku. Všechna data jsou uložená v `byte[]` pole. Následující kód definuje a přidá `title`, `director`, a `release_date` sloupce do rodiny sloupců t1 jako tyto sloupce jsou nejčastěji používaná. `description` a `tagline` sloupce jsou přidány do rodiny sloupců t2. Můžete svá data dělit do rodin sloupců podle potřeby.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

Implementuje HBase [cloudu BigTable](https://cloud.google.com/bigtable/), tak, že formát dat vypadá nějak takto:

![Uživatel s rolí uživatele clusteru](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Výběr dat

Přečíst data z tabulky HBase, předejte klíč řádků a název tabulky do `GetCellsAsync` metodu pro návrat `CellSet`.

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

V takovém případě vrátí kód pouze první odpovídající řádek, jak by měla existovat pouze jeden řádek pro jedinečný klíč. Vrácená hodnota se změní na `string` formátování z `byte[]` pole. Můžete také převést hodnotu na jiné typy, jako je například integer pro tento film datum vydání:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Skenovat řádků

Používá HBase `scan` načíst jeden nebo více řádků. Tento příklad žádosti více řádků v dávkách po 10 a načítá data, jejichž klíčové hodnoty jsou 25 až 35. Po načtení všech řádků, odstraňte skeneru pro vyčištění prostředků.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Další postup

* [Začínáme s příkladem Apache HBase v HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Sestavení aplikace začátku do konce se [analýza v reálném čase sentimentu Twitter s Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
