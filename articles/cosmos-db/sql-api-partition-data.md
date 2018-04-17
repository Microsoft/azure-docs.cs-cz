---
title: Vytváření oddílů a škálování v Azure Cosmos DB | Microsoft Docs
description: Další informace o tom, jak rozdělení funguje v Azure Cosmos DB, jak nakonfigurovat, vytváření oddílů a oddílu klíče a jak vybrat klíč správné oddílu pro vaši aplikaci.
services: cosmos-db
author: rafats
manager: kfile
documentationcenter: ''
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50be809df0938272a3e1d710b879ca3dd5de9428
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="partitioning-in-azure-cosmos-db-using-the-sql-api"></a>Vytváření oddílů v Azure DB Cosmos pomocí rozhraní SQL API

[Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) je globální databáze distribuované, více modelu služba navržená tak, aby vám pomohou dosáhnout rychlé, předvídatelný výkon a škálování bezproblémově společně s vaší aplikací je s růstem. 

Tento článek obsahuje přehled o tom, jak pracovat s oddíly Cosmos DB kontejnery s rozhraním API SQL. V tématu [vytváření oddílů a horizontální škálování](../cosmos-db/partition-data.md) přehled o konceptech a osvědčené postupy pro vytváření oddílů s jakéhokoli rozhraní API Azure Cosmos DB. 

Pokud chcete začít s kódem, stáhněte si projekt z [Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Po přečtení tohoto článku, budete moct odpovězte si na následující otázky:   

* Jak funguje vytváření oddílů v Azure Cosmos DB?
* Jak nakonfigurovat, vytváření oddílů v Azure Cosmos DB
* Jaké jsou klíče oddílů a jak vyberte klíč oddílu správné své aplikaci?

Pokud chcete začít s kódem, stáhněte si projekt z [Azure Cosmos DB výkonu testování ovladačů Sample](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Klíče oddílů

V rozhraní API SQL zadejte definici klíče oddílu ve formě cesta JSON. V následující tabulce jsou uvedeny příklady oddílu klíčové definice a hodnoty odpovídající každé. Klíč oddílu je zadán jako cesta, například `/department` představuje vlastnost oddělení. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Klíč oddílu</strong></p></td>
            <td valign="top"><p><strong>Popis</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Odpovídá hodnotě doc.department, kde je dokumentace položky.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ vlastnosti/název</p></td>
            <td valign="top"><p>Odpovídá hodnotě doc.properties.name, kde je dokumentace položky (vnořené vlastnosti).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ID</p></td>
            <td valign="top"><p>Odpovídá hodnotě doc.id (id a oddílu klíče jsou stejnou vlastnost).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ ""oddělení název</p></td>
            <td valign="top"><p>Odpovídá hodnotě doc ["název oddělení"], kde je dokumentace položky.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> Syntaxe pro klíč oddílu je podobná specifikace cesty pro indexování zásad cesty s klíčovým rozdílem cestu odpovídající vlastnost namísto hodnoty, tj. žádné zástupný znak není na konci. Například zadali byste/oddělení /? Chcete-li index hodnoty v rámci oddělení, ale zadejte /department jako definice klíče oddílu. Klíč oddílu je implicitně indexovaný a nelze vyloučit z indexování pomocí přepsání indexování zásad.
> 
> 

Podíváme, jak volba klíč oddílu má dopad na výkon vaší aplikace.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Práce s Azure Cosmos DB sady SDK
Azure Cosmos DB přidala se podpora pro automatické vytváření oddílů s [REST API verze 2015-12-16](/rest/api/cosmos-db/). Chcete-li vytvořit oddílů kontejnery, je nutné stáhnout verze sady SDK 1.6.0 nebo novějším v jednom z podporovaných platforem SDK (.NET, Node.js, Java, Python, MongoDB). 

### <a name="creating-containers"></a>Vytvoření kontejnerů
Následující příklad ukazuje fragment .NET vytvořit kontejner pro uložení zařízení telemetrická data z 20 000 jednotek žádosti za sekundu, propustnosti. Sada SDK nastaví hodnotu OfferThroughput (který naopak nastaví `x-ms-offer-throughput` hlavička požadavku v rozhraní REST API). Zde jsme nastavit `/deviceId` jako klíč oddílu. Volba klíč oddílu se uloží spolu s ostatními metadata kontejneru jako název a zásady indexování.

Tato ukázka jsme zachyceny `deviceId` vzhledem k tomu, že nám vědět, že (a) od existuje velký počet zařízení, zápisy mohou být distribuovány na oddíly rovnoměrně a abychom mohli škálovat databázi k ingestování ohromné objemy dat a (b) mnoho požadavků, jako načítání nejnovější čtení pro zařízení jsou omezená na jednom ID zařízení a mohou být načteny z jednoho oddílu.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Tato metoda umožňuje volání do databáze Cosmos rozhraní REST API a zřizovat počet oddílů podle propustnosti, požadované služby. Výkon vašich potřeb momentální, můžete změnit propustnost kontejner. 

### <a name="reading-and-writing-items"></a>Čtení a zápis položky
Nyní Pojďme vkládat data do databáze Cosmos. Zde je ukázka třída obsahující zařízení, čtení a volání CreateDocumentAsync vložit nového zařízení čtení do kontejneru. Jedná se o příklad využívá rozhraní SQL API:

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

Umožňuje přečíst položku svým id a klíč oddílu, aktualizovat a jako poslední krok, odstraňte ji. klíč oddílu a id. Všimněte si, že operace čtení zahrnují hodnotu PartitionKey (ta odpovídá záhlaví požadavku`x-ms-documentdb-partitionkey` v rozhraní REST API).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>Dotazování oddílů kontejnery
Pro dotazování dat v kontejnerech oddílů, Cosmos DB automaticky směruje dotaz na oddíly odpovídající hodnoty klíče oddílu zadaných ve filtru (pokud existují). Například tento dotaz se směruje jenom do oddílu, který obsahuje klíč oddílu XMS-0001.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
Následující dotaz neobsahuje filtr klíče oddílu (DeviceId) a distribuuje se do všech oddílů, ve kterých se provede na index oddílu. Poznámka: Pokud chcete, aby sada SDK provedla dotaz ve všech oddílech, musíte zadat EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` v rozhraní REST API).

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Podporuje cosmos DB [agregační funkce](sql-api-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, `SUM` a `AVG` přes oddíly kontejnery pomocí SQL spouštění pomocí sady SDK 1.12.0 a vyšší. Dotazy musí obsahovat jeden agregační operátor a musí obsahovat jednu hodnotu v projekci.

### <a name="parallel-query-execution"></a>Paralelní provádění dotazů
Sady SDK DB Cosmos 1.9.0 a vyšší možnosti provádění paralelního dotazu podpory, které umožňují provádět dotazy s nízkou latencí pro dělené kolekce, i v případě, že potřebují k touch velký počet oddílů. Například následující dotaz je nakonfigurovaný tak, aby se spustil paralelně napříč oddíly.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Paralelní provádění dotazů můžete spravovat laděním následujících parametrů:

* Nastavením `MaxDegreeOfParallelism`, můžete řídit stupně paralelního zpracování tedy maximální počet souběžných síťová připojení k oddílům kontejneru. Pokud nastavíte hodnotu -1, stupeň paralelismu bude spravovat sada SDK. Pokud `MaxDegreeOfParallelism` není zadaný, nebo je nastavený na 0, což je výchozí hodnota, bude jedno síťové připojení k oddílům kontejneru.
* Nastavením `MaxBufferedItemCount` můžete zvolit kompromis mezi latencí dotazů a využitím paměti na straně klienta. Pokud tento parametr vynecháte nebo ho nastavíte na hodnotu -1, počet položek ukládaných do vyrovnávací paměti během paralelního provádění dotazů bude spravovat sada SDK.

Za předpokladu stejného stavu kolekce vrátí paralelní dotaz výsledky ve stejném pořadí jako při sériovém provedení. Při provádění dotazu mezi oddílu, který zahrnuje řazení (ORDER BY a/nebo horní), sadu SDK Azure Cosmos DB vydá dotaz paralelně napříč oddíly a sloučí částečně seřazená výsledky na straně klienta k vytvoření globální seřazené výsledky.

### <a name="executing-stored-procedures"></a>Provádění uložené procedury
Můžete také provést jednotlivé transakce na dokumenty se stejným ID zařízení, například pokud jste zachování agregace nebo nejnovější stav zařízení v jedné položce. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
V další části podíváme na tom, jak můžete přesunout do oddílů kontejnerů z kontejnerů jedním oddílem.

## <a name="next-steps"></a>Další postup
V tomto článku jsme poskytuje přehled o tom, jak pracovat s oddíly kontejnery Azure Cosmos DB s rozhraním API SQL. Viz také [vytváření oddílů a horizontální škálování](../cosmos-db/partition-data.md) přehled o konceptech a osvědčené postupy pro vytváření oddílů s jakéhokoli rozhraní API Azure Cosmos DB. 

* Proveďte škálování a výkon testování pomocí Azure Cosmos DB. V tématu [testování výkonu a škálování s Azure Cosmos DB](performance-testing.md) pro ukázku.
* Začínáme s kódování [sady SDK](sql-api-sdk-dotnet.md) nebo [REST API](/rest/api/cosmos-db/)
* Další informace o [zřízené propustnosti v Azure Cosmos DB](request-units.md)

