---
title: Použití Azure HDInsight HBase .NET SDK - | Microsoft Docs
description: Vytvářet a odstraňovat tabulky a ke čtení a zápisu dat pomocí .NET SDK služby HBase.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: f0e2c6412a965c73b0055a24c799e05ad582a8c7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="use-the-hbase-net-sdk"></a>Použití sady .NET SDK

[HBase](apache-hbase-overview.md) poskytuje dvě primární možnosti pro práci s daty: [Hive dotazy a volání rozhraní RESTful API HBase je](apache-hbase-tutorial-get-started-linux.md). Můžete pracovat přímo s pomocí rozhraní REST API `curl` příkaz nebo podobného nástroje.

Pro aplikace C# a rozhraní .NET [Microsoft HBase REST Klientská knihovna pro .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) poskytuje knihovna klienta nad HBase REST API.

## <a name="install-the-sdk"></a>Instalace sady SDK

.NET SDK služby HBase je k dispozici jako balíčku NuGet, které můžete nainstalovat ze sady Visual Studio **Konzola správce balíčků NuGet** pomocí následujícího příkazu:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Vytvořit nový objekt HBaseClient

K využívání sady SDK, vytvořit novou instanci `HBaseClient` objekt, předávání v `ClusterCredentials` tvořený `Uri` na cluster a Hadoop uživatelské jméno a heslo.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Nahraďte název clusteru s názvem clusteru HDInsight HBase a uživatelské jméno a heslo zadané při vytváření clusteru Hadoop pověření. Výchozí Hadoop uživatelské jméno je **správce**.

## <a name="create-a-new-table"></a>Vytvořit novou tabulku

HBase ukládá data v tabulkách. Tabulka se skládá z *Rowkey*, primární klíč a jednu nebo více skupin sloupců s názvem *rodin sloupců*. Data v každé tabulce je vodorovně distribuován podle rozsahu Rowkey do *oblasti*. Každá oblast má počáteční a koncové klíč. Tabulka může mít jeden nebo více oblastí. S růstem data v tabulce HBase rozdělí velké oblasti na menší oblasti. Oblasti jsou uložené v *oblast servery*, kde jeden server oblasti můžete uložit více oblastí.

Data jsou fyzicky uložena v *HFiles*. Jeden hfile – obsahuje data pro jednu tabulku, jedné oblasti a jeden rodin sloupců. Řádky v hfile – jsou uloženy na Rowkey seřazeny. Má každý hfile – *stromu B* index pro rychlé načítání řádků.

Pokud chcete vytvořit novou tabulku, zadejte `TableSchema` a sloupců. Následující kód kontroluje, zda je vytvořen v tabulce 'RestSDKTable' již existuje - Pokud ne, v tabulce.

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

Tato nová tabulka obsahuje dva sloupce rodiny, t1 a t2. Vzhledem k tomu, že rodin sloupců jsou uloženy odděleně v různých HFiles, má smysl mít rodinu samostatný sloupec pro data z často dotazu. V následujícím [vkládání dat](#insert-data) například sloupce jsou přidány do rodin sloupců t1.

## <a name="delete-a-table"></a>Odstranění tabulky

Pokud chcete odstranit:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Vložení dat

Pokud chcete vložit data, zadejte jedinečné řádek klíč jako identifikátor řádku. Veškerá data budou uložena v `byte[]` pole. Následující kód definuje a přidá `title`, `director`, a `release_date` sloupce, které chcete rodin t1 sloupců, jako tyto sloupce jsou nejčastěji používaná. `description` a `tagline` sloupce jsou přidány do rodin sloupců t2. Data můžete oddílu do rodinami sloupců, podle potřeby.

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

HBase implementuje BigTable, tak formát dat vypadá takto:

![Uživatel s rolí uživatele clusteru](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Výběr dat

Čtení dat z tabulky HBase, předejte klíč, na název a řádek tabulky `GetCellsAsync` metodu pro návrat `CellSet`.

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

V tomto případě kód vrátí pouze první odpovídající řádek, jako by měla existovat jenom jeden řádek pro jedinečný klíč. Vrácená hodnota se změnil na `string` formátu z `byte[]` pole. Také můžete převést hodnotu na jiné typy, jako je například celé číslo, pro datum vydání na video:

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

## <a name="scan-over-rows"></a>Kontrola nad řádků

Používá HBase `scan` načíst jeden nebo více řádků. Tento příklad požadavky více řádků v dávkách 10 a načítá data, jejichž hodnoty klíče jsou 25 až 35. Po načtení všechny řádky, odstraňte skeneru vyčištění prostředků.

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

* [Začínáme s příklad Apache HBase v HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Sestavení aplikace začátku do konce s [analýzu v reálném čase sentimentu Twitter s HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
