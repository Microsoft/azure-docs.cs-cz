---
title: Použití sady .NET SDK pro adaptéry HBA – Azure HDInsight
description: Pomocí sady .NET SDK pro adaptéry HBA můžete vytvářet a odstraňovat tabulky a číst a zapisovat data.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: d1979e43adc76f4125097fc809ef137baee05f53
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939567"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Použití sady .NET SDK pro Apache HBA

[Apache HBA](apache-hbase-overview.md) poskytují dvě primární volby pro práci s daty: [Apache Hive dotazy a volání rozhraní RESTful API HBA](apache-hbase-tutorial-get-started-linux.md). Pomocí `curl` příkazu nebo podobného nástroje můžete pracovat přímo s REST API.

Pro aplikace v jazyce C# a .NET poskytuje [Klientská knihovna REST Microsoft HBA pro .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) knihovnu klienta na REST API HBA.

## <a name="install-the-sdk"></a>Instalace sady SDK

Sada HBA sady .NET SDK je k dispozici jako balíček NuGet, který lze nainstalovat z **konzoly Správce balíčků NuGet** sady Visual Studio pomocí následujícího příkazu:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Vytvoření instance nového objektu HBaseClient

Chcete-li použít sadu SDK, vytvořte instanci nového `HBaseClient` objektu a předejte se `ClusterCredentials` složeným z rozhraní `Uri` do vašeho clusteru a uživatelské jméno a heslo Hadoop.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Položku název_clusteru nahraďte názvem clusteru HDInsight HBA a uživatelské jméno a heslo s přihlašovacími údaji Apache Hadoop zadaným při vytváření clusteru. Výchozí uživatelské jméno Hadoop je **admin**.

## <a name="create-a-new-table"></a>Vytvoření nové tabulky

HBA ukládají data v tabulkách. Tabulka se skládá z *Rowkey*, primárního klíče a jedné nebo více skupin sloupců s názvem *rodin sloupců*. Data v každé tabulce jsou horizontálně distribuována Rowkey rozsahem do *oblastí*. Každá oblast má počáteční a koncový klíč. Tabulka může mít jednu nebo více oblastí. Při zvětšování dat v tabulce rozděluje adaptéry HBA velké oblasti do menších oblastí. Oblasti jsou uloženy v oblastech *servery*, kde jeden server oblasti může ukládat více oblastí.

Data se fyzicky ukládají do *HFiles*. Jeden HFile obsahuje data pro jednu tabulku, jednu oblast a jednu rodinu sloupců. Řádky v HFile jsou uloženy seřazené podle Rowkey. Každý HFile má index *B + stromu* pro urychlení načítání řádků.

Chcete-li vytvořit novou tabulku, zadejte `TableSchema` sloupce a. Následující kód zkontroluje, zda tabulka ' RestSDKTable ' již existuje – Pokud není, tabulka je vytvořena.

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

Tato nová tabulka má dvě rodiny sloupců, T1 a T2. Vzhledem k tomu, že rodin sloupců jsou uložené odděleně v různých HFiles, má smysl mít samostatnou rodinu sloupců pro často dotazovaná data. V následujícím příkladu [INSERT data](#insert-data) jsou sloupce přidány do rodiny sloupců T1.

## <a name="delete-a-table"></a>Odstranění tabulky

Odstranění tabulky:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Vložení dat

Chcete-li vložit data, zadejte jedinečný klíč řádku jako identifikátor řádku. Všechna data jsou uložena v `byte[]` poli. Následující kód definuje a přidá `title` `director` sloupce, a `release_date` do rodiny sloupců T1, protože tyto sloupce jsou nejčastěji používané. `description`Sloupce a `tagline` jsou přidány do rodiny sloupců T2. Data můžete podle potřeby rozdělit do rodin sloupců.

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

HBA implementují [Cloud BigTable](https://cloud.google.com/bigtable/), takže formát dat vypadá jako na následujícím obrázku:

![Výstup ukázkových dat Apache HBA](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Výběr dat

Chcete-li načíst data z tabulky HBA, předejte název tabulky a klíč řádku do `GetCellsAsync` metody, která vrátí `CellSet` .

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

V tomto případě kód vrátí pouze první vyhovující řádek, protože pro jedinečný klíč by měl být pouze jeden řádek. Vrácená hodnota se změní na `string` Formát v `byte[]` poli. Tuto hodnotu můžete také převést na jiné typy, jako je například celé číslo pro datum vydání verze videa:

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

## <a name="scan-over-rows"></a>Kontrolovat řádky

HBA využívají `scan` k načtení jednoho nebo více řádků. Tento příklad vyžaduje více řádků v dávkách 10 a načítá data, jejichž klíčová hodnota jsou mezi 25 a 35. Po načtení všech řádků odstraňte skener a vyčistěte prostředky.

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
* Sestavení ucelené aplikace s [analýzou mínění na Twitteru v reálném čase pomocí Apache HBA](./apache-hbase-tutorial-get-started-linux.md)