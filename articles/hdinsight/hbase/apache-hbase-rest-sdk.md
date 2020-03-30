---
title: Použití sady HBase .NET SDK – Azure HDInsight
description: Pomocí sady HBase .NET SDK můžete vytvářet a odstraňovat tabulky a číst a zapisovat data.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: eba7d7ad009b2ef0442a916983489489eb5cceb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806656"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Použití sady .NET SDK pro Apache HBase

[Apache HBase](apache-hbase-overview.md) poskytuje dvě primární možnosti pro práci s [vašimi daty: apache hive dotazy a volání RESTful API HBase](apache-hbase-tutorial-get-started-linux.md). Pomocí příkazu nebo podobného `curl` nástroje můžete pracovat přímo s rozhraním REST API.

Pro aplikace C# a .NET poskytuje [klientská knihovna Microsoft HBase REST pro rozhraní .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) klientskou knihovnu nad rozhraním HBase REST API.

## <a name="install-the-sdk"></a>Instalace sady SDK

Sada HBase .NET SDK je k dispozici jako balíček NuGet, který lze nainstalovat z **konzoly Správce balíčků** sady Visual Studio NuGet pomocí následujícího příkazu:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Vytvoření instance nového objektu HBaseClient

Chcete-li použít sadu SDK, `HBaseClient` vytvořte `ClusterCredentials` instanci `Uri` nového objektu, který se skládá z clusteru a uživatelského jména a hesla Hadoop.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Nahraďte název clusteru HDInsight HBase názvem a username a password pověřeními Apache Hadoop zadanými při vytváření clusteru. Výchozí uživatelské jméno Hadoop je **admin**.

## <a name="create-a-new-table"></a>Vytvoření nové tabulky

HBase ukládá data do tabulek. Tabulka se skládá z *řádku ,* primárního klíče a jedné nebo více skupin sloupců nazývaných *rodiny sloupců*. Data v každé tabulce jsou horizontálně distribuována rozsahem Rowkey do *oblastí*. Každá oblast má počáteční a koncový klíč. Tabulka může mít jednu nebo více oblastí. Jak data v tabulce rostou, HBase rozdělí velké oblasti do menších oblastí. Oblasti jsou uloženy na *serverech oblastí*, kde jeden server oblasti může ukládat více oblastí.

Data jsou fyzicky uložena v *HFiles*. Jeden soubor HFile obsahuje data pro jednu tabulku, jednu oblast a jednu rodinu sloupců. Řádky v HFile jsou uloženy seřazené na Rowkey. Každý HFile má *b + strom* index pro rychlé načítání řádků.

Chcete-li vytvořit novou `TableSchema` tabulku, zadejte sloupce a. Následující kód zkontroluje, zda tabulka "RestSDKTable" již existuje – pokud ne, je tabulka vytvořena.

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

Tato nová tabulka má dvousloupcové rodiny, t1 a t2. Vzhledem k tomu, že rodiny sloupců jsou uloženy samostatně v různých HFiles, má smysl mít samostatnou rodinu sloupců pro často dotazovaná data. V následujícím příkladu [vložení dat](#insert-data) jsou sloupce přidány do rodiny sloupců t1.

## <a name="delete-a-table"></a>Odstranění tabulky

Odstranění tabulky:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Vložení dat

Chcete-li vložit data, zadejte jako identifikátor řádku jedinečný klíč řádku. Všechna data jsou `byte[]` uložena v poli. Následující kód definuje a `title`přidává `director`, `release_date` a sloupce do rodiny sloupců t1, protože tyto sloupce jsou nejčastěji přístupné. Sloupce `description` `tagline` a jsou přidány do rodiny sloupců t2. Podle potřeby můžete data rozdělit do rodin sloupců.

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

HBase implementuje [Cloud BigTable](https://cloud.google.com/bigtable/), takže formát dat vypadá jako následující obrázek:

![Výstup ukázkových dat Apache HBase](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Výběr dat

Chcete-li číst data z tabulky HBase, předajte název tabulky a klíč řádku metodě, `GetCellsAsync` která vrátí . `CellSet`

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

V tomto případě kód vrátí pouze první odpovídající řádek, protože by měl být pouze jeden řádek pro jedinečný klíč. Vrácená hodnota se `string` změní `byte[]` do formátu z pole. Hodnotu můžete také převést na jiné typy, například celé číslo pro datum vydání filmu:

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

## <a name="scan-over-rows"></a>Prohledávat přes řádky

HBase `scan` používá k načtení jednoho nebo více řádků. Tento příklad požaduje více řádků v dávkách 10 a načte data, jejichž hodnoty klíče jsou mezi 25 a 35. Po načtení všech řádků odstraňte skener a vyčistěte prostředky.

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

## <a name="next-steps"></a>Další kroky

* [Začínáme s příkladem Apache HBase ve službě HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Vytvořte komplexní aplikaci s [analyzujte nálady na Twitteru v reálném čase s Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
