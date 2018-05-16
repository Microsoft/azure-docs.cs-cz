---
title: 'Azure Cosmos DB: Vývoj v .NET s využitím rozhraní SQL API | Microsoft Docs'
description: Naučte se vyvíjet v .NET s využitím rozhraní SQL API služby Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: 528832473d68fa90e6383873b1e0491f5abe09c7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB: Vývoj v .NET s využitím rozhraní SQL API

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos.

V tomto kurzu se dozvíte, jak pomocí webu Azure Portal vytvořit účet služby Azure Cosmos DB a pak pomocí rozhraní [SQL API pro .NET](sql-api-introduction.md) vytvořit databázi dokumentů a kolekci s [klíčem oddílu](sql-api-partition-data.md#partition-keys). Když při vytváření kolekce definujete klíč oddílu, vaše aplikace bude připravená na bezproblémové škálování s ohledem na růst objemu dat.

Tento kurz se zabývá následujícími úkony s použitím rozhraní [SQL API pro .NET](sql-api-sdk-dotnet.md):

> [!div class="checklist"]
> * Vytvoření účtu služby Azure Cosmos DB
> * Vytvoření databáze a kolekce s klíčem oddílu
> * Vytvoření dokumentů JSON
> * Aktualizace dokumentu
> * Dotazování dělených kolekcí
> * Spuštění uložených procedur
> * Odstranění dokumentu
> * Odstranění databáze

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte následující:

* Přístup k účtu služby Azure Cosmos DB

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

  Můžete použít také vlastní předplatné Azure, když si zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/free/). Potom si můžete [vytvořit účet služby Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account).

* Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.


> [!TIP]
> * Pokud se rozhodnete používat emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md).
>
>


## <a id="SetupVS"></a>Nastavení řešení v sadě Visual Studio
1. Otevřete na svém počítači sadu **Visual Studio**.
2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.
3. V dialogovém okně **Nový projekt** vyberte **Šablony** / **Visual C#** / **Konzolová aplikace (.NET Framework)**, pojmenujte svůj projekt a klikněte na **OK**.
   ![Snímek obrazovky okna Nový projekt](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. V **Průzkumníku řešení** klikněte pravým tlačítkem na novou konzolovou aplikaci v rámci řešení sady Visual Studio a pak klikněte na **Spravovat balíčky NuGet**.

    ![Snímek obrazovky místní nabídky projektu](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. Na kartě **NuGet** klikněte na **Procházet** a do vyhledávacího pole zadejte **documentdb**.
<!---stopped here--->
6. Najděte ve výsledcích **Microsoft.Azure.DocumentDB** a klikněte na **Nainstalovat**.
   ID balíčku s klientskou knihovnou služby Azure Cosmos DB je [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Snímek obrazovky s nabídkou NuGet pro vyhledání klientské sady SDK služby Azure Cosmos DB](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Pokud se vám zobrazí zpráva týkající se kontroly změn řešení, klikněte na **OK**. Pokud se vám zobrazí zpráva týkající se přijetí licence, klikněte na **Souhlasím**.

## <a id="Connect"></a>Přidání referencí do projektu
Zbývající kroky v tomto kurzu poskytují fragmenty kódu rozhraní SQL API, které jsou potřeba k vytvoření a aktualizaci prostředků služby Azure Cosmos DB ve vašem projektu.

Nejprve do své aplikace přidejte tyto reference.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Připojení aplikace

Dále do své aplikace přidejte tyto dvě konstanty a proměnnou *client*.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Pak přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte adresu URL koncového bodu a primární klíč. Adresa URL koncového bodu a primární klíč jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace.

Na webu Azure Portal přejděte do svého účtu služby Azure Cosmos DB. V nabídce vlevo vyberte **Klíče** a pak vyberte **Klíče pro čtení i zápis**.

Zkopírujte identifikátor URI z portálu a vložte ho místo `<your endpoint URL>` v souboru program.cs. Pak zkopírujte PRIMÁRNÍ KLÍČ z portálu a vložte ho místo `<your primary key>`. Nezapomeňte ve svých hodnotách odebrat `<` a `>`.

![Snímek obrazovky webu Azure Portal, který se v kurzu NoSQL používá k vytvoření konzolové aplikace v jazyce C#. Ukazuje účet služby Azure Cosmos DB se zvýrazněnou možností KLÍČE v části účtu služby Azure Cosmos DB a zvýrazněnými hodnotami IDENTIFIKÁTOR URI a PRIMÁRNÍ KLÍČ v části Klíče.](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Vytvoření instance DocumentClient

Teď vytvořte novou instanci **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Vytvoření databáze

Dále vytvořte [databázi](sql-api-resources.md#databases) Azure Cosmos DB pomocí metody [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) nebo [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) třídy **DocumentClient** v sadě [SQL SDK pro .NET](sql-api-sdk-dotnet.md). Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Výběr klíče oddílu

Kolekce jsou kontejnery pro ukládání dokumentů. Jedná se o logické prostředky, které můžou [využívat jeden nebo více fyzických oddílů](partition-data.md). [Klíč oddílu](sql-api-partition-data.md) je vlastnost (nebo cesta) v rámci dokumentů, která slouží k distribuci dat napříč servery nebo oddíly. Všechny dokumenty se stejným klíčem oddílu se ukládají do stejného oddílu.

Určení klíče oddílu představuje důležité rozhodnutí, které je potřeba provést před vytvořením kolekce. Klíče oddílů jsou vlastnost (nebo cesta) v rámci dokumentů, kterou služba Azure Cosmos DB používá k distribuci dat napříč několika servery nebo oddíly. Cosmos DB hashuje hodnotu klíče oddílu a výslednou hodnotu hash používá k určení oddílu, do kterého má dokument uložit. Všechny dokumenty se stejným klíčem oddílu se ukládají do stejného oddílu a klíče oddílů není možné po vytvoření kolekce změnit.

Pro účely tohoto kurzu je potřeba nastavit klíč oddílu na `/deviceId`, aby se všechna data pro jedno zařízení ukládala do jednoho oddílu. Doporučujeme zvolit klíč oddílu s velkým rozsahem hodnot, z nichž každá se bude používat přibližně stejně často, aby se zajistilo, že Cosmos DB dokáže vyrovnávat zatížení s ohledem na růst objemu dat a dosáhnout úplné propustnosti kolekce.

Další informace o dělení najdete v tématu [Jak dělit a škálovat ve službě Azure Cosmos DB?](partition-data.md)

## <a id="CreateColl"></a>Vytvoření kolekce

Když máte klíč oddílu `/deviceId`, můžete vytvořit [kolekci](sql-api-resources.md#collections) pomocí metody [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) nebo [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) třídy **DocumentClient**. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

> [!WARNING]
> Za vytvoření kolekce se hradí poplatky, protože rezervujete propustnost pro komunikaci aplikace se službou Azure Cosmos DB. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Tato metoda provádí volání rozhraní REST API do služby Azure Cosmos DB a ta na základě požadované propustnosti zřídí několik oddílů. S ohledem na měnící se požadavky na výkon můžete propustnost kolekce nebo sady kolekcí měnit pomocí sady SDK nebo webu [Azure Portal](set-throughput.md).

## <a id="CreateDoc"></a>Vytvoření dokumentů JSON
Teď do služby Azure Cosmos DB přidáme několik dokumentů JSON. [Dokument](sql-api-resources.md#documents) je možné vytvořit pomocí metody [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) třídy **DocumentClient**. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Tato ukázková třída obsahuje čtení zařízení a volání metody CreateDocumentAsync, která vloží nové čtení zařízení do kolekce.

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

// Create a document. Here the partition key is extracted
// as "XMS-0001" based on the collection definition
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
## <a name="read-data"></a>Čtení dat

Teď pomocí metody ReadDocumentAsync přečteme dokument podle jeho klíče oddílu a ID. Všimněte si, že operace čtení zahrnují hodnotu PartitionKey (ta odpovídá záhlaví požadavku`x-ms-documentdb-partitionkey` v rozhraní REST API).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Aktualizace dat

Teď pomocí metody ReplaceDocumentAsync některá data aktualizujeme.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  reading);
```

## <a name="delete-data"></a>Odstranění dat

Teď pomocí metody DeleteDocumentAsync odstraníme dokument podle jeho klíče oddílu a ID.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Dotazování dělených kolekcí

Při dotazování dat v dělených kolekcích služba Azure Cosmos DB automaticky směruje dotaz do oddílů, které odpovídají hodnotám klíčů oddílů zadaných ve filtru (pokud existují). Například tento dotaz se směruje jenom do oddílu, který obsahuje klíč oddílu XMS-0001.

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

## <a name="parallel-query-execution"></a>Paralelní provádění dotazů
Sady SQL SDK služby Azure Cosmos DB verze 1.9.0 a novější podporují možnosti paralelního provádění dotazů. Díky tomu můžete provádět dotazy s nízkou latencí na dělené kolekce i v případě, že je potřeba pracovat s velkým počtem oddílů. Například následující dotaz je nakonfigurovaný tak, aby se spustil paralelně napříč oddíly.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Paralelní provádění dotazů můžete spravovat laděním následujících parametrů:

* Nastavením `MaxDegreeOfParallelism` můžete řídit stupeň paralelismu, tj. maximální počet souběžných síťových připojení k oddílům kolekce. Pokud tento parametr nastavíte na hodnotu -1, stupeň paralelismu bude spravovat sada SDK. Pokud hodnota `MaxDegreeOfParallelism` není zadaná nebo je nastavená na 0, což je výchozí hodnota, k oddílům kolekce bude existovat jediné síťové připojení.
* Nastavením `MaxBufferedItemCount` můžete zvolit kompromis mezi latencí dotazů a využitím paměti na straně klienta. Pokud tento parametr vynecháte nebo ho nastavíte na hodnotu -1, počet položek ukládaných do vyrovnávací paměti během paralelního provádění dotazů bude spravovat sada SDK.

Za předpokladu stejného stavu kolekce vrátí paralelní dotaz výsledky ve stejném pořadí jako při sériovém provedení. Při provádění dotazu napříč oddíly, který zahrnuje řazení (ORDER BY nebo TOP) sada SQL SDK provede dotaz paralelně napříč oddíly a sloučením částečně seřazených výsledků na straně klienta vytvoří globálně seřazené výsledky.

## <a name="execute-stored-procedures"></a>Provádění uložených procedur
A konečně, přidáním následujícího kódu do svého projektu můžete provádět atomické transakce s dokumenty se stejným ID zařízení, například pokud v jednom dokumentu uchováváte agregace nebo poslední stav zařízení.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") },
    "XMS-001-FE24C");
```

A to je vše! Toto jsou hlavní komponenty aplikace Azure Cosmos DB, která pomocí klíče oddílu efektivně škáluje distribuci dat napříč oddíly.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikace nadále používat, pomocí následujících kroků odstraňte všechny prostředky vytvořené v rámci tohoto kurzu na webu Azure Portal:

1. V nabídce vlevo na webu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na jedinečný název vytvořeného prostředku.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Vytvořili jste účet služby Azure Cosmos DB.
> * Vytvořili jste databázi a kolekci s klíčem oddílu.
> * Vytvořili jste dokumenty JSON.
> * Aktualizovali jste dokument.
> * Dotazovali jste dělené kolekce.
> * Spustili jste uloženou proceduru.
> * Odstranili jste dokument.
> * Odstranili jste databázi.

Teď můžete přejít k dalšímu kurzu a importovat do účtu služby Cosmos DB další data.

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
