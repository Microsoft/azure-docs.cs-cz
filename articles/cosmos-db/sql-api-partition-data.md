---
title: Dělení a škálování ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o tom, jak dělení funguje v Azure Cosmos DB, jak nakonfigurovat, vytváření oddílů a klíče oddílu a jak vybrat správný oddíl klíč pro vaši aplikaci.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d44d3933a132158a6dfca8201919eb72541f276
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163241"
---
# <a name="partitioning-in-azure-cosmos-db-using-the-sql-api"></a>Dělení ve službě Azure Cosmos DB pomocí rozhraní SQL API

[Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) je globální distribuovaná a vícemodelová databázová služba navržená tak, aby vám pomohou dosáhnout rychlého, předvídatelného výkonu a škálování – bez problémů vaší aplikace, jak rostou. 

Tento článek poskytuje přehled o tom, jak pracovat s dělením kontejnerů služby Cosmos DB pomocí rozhraní SQL API. Zobrazit [vytváření oddílů a horizontální škálování](../cosmos-db/partition-data.md) Přehled konceptů a osvědčené postupy pro dělení s jakékoli rozhraní API služby Azure Cosmos DB. 

Abyste mohli začít s kódem, stáhněte si projekt z [Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Po přečtení tohoto článku, budou moci odpovědět na následující otázky:   

* Jak funguje dělení ve službě Azure Cosmos DB?
* Jak nakonfigurovat dělení ve službě Azure Cosmos DB
* Co jsou klíče oddílu a jak vyberte klíč oddílu správné pro mé aplikace?

Abyste mohli začít s kódem, stáhněte si projekt z [Azure Cosmos DB výkonu testování ovladače Sample](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Klíče oddílů

V rozhraní SQL API zadat definici klíče oddílu v podobě cestu JSON. V následující tabulce jsou uvedeny příklady definice klíče oddílu a hodnoty odpovídající každé. Klíč oddílu je zadat jako cestu, například `/department` představuje vlastnost oddělení. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Klíč oddílu</strong></p></td>
            <td valign="top"><p><strong>Popis</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Odpovídá hodnotě doc.department, kde je položka dokumentu.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ vlastnosti/názvu</p></td>
            <td valign="top"><p>Odpovídá hodnotě doc.properties.name, kde je dokumentace položky (vnořené vlastnosti).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ID</p></td>
            <td valign="top"><p>Odpovídá hodnotě doc.id (klíč id a oddílu se stejnou vlastnost).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "název oddělení"</p></td>
            <td valign="top"><p>Odpovídá hodnotě doc ["jméno oddělení"], kde je položka dokumentu.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> Syntaxe pro klíč oddílu je podobný specifikaci cesty pro indexování zásad cesty klíčovým rozdílem cestu odpovídající vlastnost místo hodnoty, to znamená bez zástupný znak není na konci. Například zadáte/oddělení /? pro indexování hodnot v rámci oddělení, ale zadejte /department jako definice klíče oddílu. Klíč oddílu je implicitně indexují a nejde vyloučit z indexování s využitím indexování přepsání zásady.
> 
> 

Podívejme se na tom, jak volba klíče oddílu má vliv na výkon vaší aplikace.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Práce se sadami SDK služby Azure Cosmos DB
Azure Cosmos DB přidali jsme podporu pro automatické dělení s [rozhraní REST API verze 2015-12 – 16](/rest/api/cosmos-db/). Chcete-li vytvořit dělené kontejnerů, je nutné stáhnout sadu SDK verze 1.6.0 nebo novější v jednom z podporovaných sadu SDK platformy (.NET, Node.js, Java, Python, MongoDB). 

### <a name="creating-containers"></a>Vytvoření kontejnerů
Následující příklad ukazuje fragment .NET vytvořit kontejner pro uložení zařízení telemetrická data z 20 000 jednotek žádostí za sekundu propustnosti. Sada SDK nastaví hodnotu OfferThroughput (což zase nastaví `x-ms-offer-throughput` hlavičku požadavku v rozhraní REST API). Zde můžete nastavit `/deviceId` jako klíč oddílu. Volba klíče oddílu se uloží spolu se zbývajícími metadata kontejneru, jako jsou název a zásady indexování.

V tomto příkladu jste vybrali `deviceId` protože víte, že (a) existuje velký počet zařízení, zápisy je možné rovnoměrně distribuovat napříč oddíly a díky tomu umožňuje škálovat databázi pro příjem velkých objemů dat a (b) mnoho požadavků, třeba načítání nejnovější materiály pro zařízení jsou omezená na jednu ID zařízení a mohou být načteny z jednoho oddílu.

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

Tato metoda provádí volání do služby Cosmos DB rozhraní REST API a služba zřídí několik oddílů na základě požadované propustnosti. Propustnost kontejneru nebo sadu kontejnerů můžete změnit podle měnících se potřeb výkonu. 

### <a name="reading-and-writing-items"></a>Čtení a zápis položky
Teď přidáme data do služby Cosmos DB. Tady je ukázková třída obsahuje čtení zařízení a volání createdocumentasync vložit nové čtení do kontejneru zařízení. Blok kódu příklad, který využívá rozhraní SQL API je následující:

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

Pojďme čtení položky podle jeho klíče oddílu a, aktualizujte ji a v posledním kroku, odstraňte ho podle klíče oddílu a id. Čtení zahrnují hodnotu PartitionKey (odpovídající `x-ms-documentdb-partitionkey` hlavičku požadavku v rozhraní REST API).

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

### <a name="querying-partitioned-containers"></a>Dotazování dělených kontejnery
Při dotazování dat v dělených kontejnery, Cosmos DB automaticky směruje dotaz do oddílů, které odpovídají hodnoty klíčů oddílů zadaných ve filtru (Pokud tam nějaké jsou). Například tento dotaz se směruje jenom do oddílu, který obsahuje klíč oddílu XMS-0001.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
Následující dotaz neobsahuje filtr klíče oddílu (DeviceId) a distribuuje se do všech oddílů, ve kterých se provede na index oddílu. Je nutné zadat EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` v rozhraní REST API) aby sada SDK provedla dotaz napříč oddíly.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB podporuje [agregační funkce](how-to-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`,, a `AVG` přes rozdělit na oddíly kontejnery pomocí SQL spuštění pomocí sady SDK 1.12.0 a vyšší. Dotazy musí obsahovat jeden agregační operátor a musí obsahovat jednu hodnotu v projekci.

### <a name="parallel-query-execution"></a>Paralelní provádění dotazů
Cosmos DB SDK 1.9.0 a novější podpora možnosti paralelního provádění dotazů, které můžete provádět dotazy s nízkou latencí na dělené kolekce i v případě, že budou muset touch velký počet oddílů. Například následující dotaz je nakonfigurovaný tak, aby se spustil paralelně napříč oddíly.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Paralelní provádění dotazů můžete spravovat laděním následujících parametrů:

* Nastavením `MaxDegreeOfParallelism`, můžete řídit stupeň paralelismu to znamená, že maximální počet souběžných síťových připojení k oddílům kontejneru. Pokud tuto vlastnost nastavíte na hodnotu -1, stupeň paralelismu bude spravovat sada SDK. Pokud `MaxDegreeOfParallelism` není zadaná nebo je nastavená na 0, což je výchozí hodnota, bude existovat jediné síťové připojení k oddílům kontejneru.
* Nastavením `MaxBufferedItemCount` můžete zvolit kompromis mezi latencí dotazů a využitím paměti na straně klienta. Pokud tento parametr vynecháte nebo tuto vlastnost nastavte na hodnotu -1, počet položek, které jsou ukládány do vyrovnávací paměti během paralelního provádění dotazů, které spravuje sady SDK.

Za předpokladu stejného stavu kolekce vrátí paralelní dotaz výsledky ve stejném pořadí jako při sériovém provedení. Při provádění dotazu napříč oddíly, který zahrnuje řazení (ORDER BY nebo TOP), Azure Cosmos DB SDK vydá dotaz paralelně napříč oddíly a sloučením částečně seřazených výsledků na straně klienta vytvoří globálně seřazené výsledky.

### <a name="executing-stored-procedures"></a>Spouštění uložených procedur
Atomické transakce s dokumenty se stejným ID zařízení, můžete také spustit příklad, pokud uchováváte agregace nebo poslední stav zařízení v jedné položky. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
V další části se podíváte na tom, jak můžete přesunout do dělené kontejnerů z kontejnerů jedním oddílem.

## <a name="next-steps"></a>Další postup
Tento článek poskytuje přehled o tom, jak pracovat s dělením kontejnerů Azure Cosmos DB pomocí rozhraní SQL API. Viz také [vytváření oddílů a horizontální škálování](../cosmos-db/partition-data.md) Přehled konceptů a osvědčené postupy pro dělení s jakékoli rozhraní API služby Azure Cosmos DB. 

* Testovat škálování a výkon pomocí služby Azure Cosmos DB. Zobrazit [testování výkonu a škálování pomocí služby Azure Cosmos DB](performance-testing.md) ukázku.
* Začínáme s kódováním [sady SDK](sql-api-sdk-dotnet.md) nebo [rozhraní REST API](/rest/api/cosmos-db/)
* Další informace o [zřízenou propustnost v databázi Azure Cosmos DB](request-units.md)

