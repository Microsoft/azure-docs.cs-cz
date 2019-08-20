---
title: Použití knihovny hromadného prováděcího modulu .NET k provádění hromadných operací importu a aktualizace v Azure Cosmos DB
description: Hromadné importy a aktualizace dokumentů Azure Cosmos DB pomocí knihovny hromadného prováděcího modulu .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 0b74c7b178ee4512067de4b8decba0c3c565ccd4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616972"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Použití knihovny hromadného prováděcího modulu .NET k provádění hromadných operací v Azure Cosmos DB

V tomto kurzu najdete pokyny k použití knihovny hromadného prováděcího modulu .NET Azure Cosmos DB k importu a aktualizaci dokumentů do služby Azure Cosmos Container. Další informace o hromadné prováděcí knihovně a o tom, jak vám pomůže využít obrovské propustnost a úložiště, najdete v článku [Přehled knihovny hromadného prováděcího modulu](bulk-executor-overview.md) . V tomto kurzu se zobrazí ukázková aplikace .NET, která hromadně naimportuje náhodně generované dokumenty do kontejneru Azure Cosmos. Po importu se dozvíte, jak hromadně aktualizovat importovaná data zadáním oprav jako operací, které se mají provést u konkrétních polí dokumentu. 

Hromadné prováděcí modul knihovny je v současné době nepodporuje rozhraní SQL API služby Azure Cosmos DB a pouze účty rozhraní Gremlin API. Tento článek popisuje, jak používat hromadnou prováděcí knihovnu .NET s účty rozhraní SQL API. Další informace o použití knihovny hromadné prováděcí modul .NET pomocí rozhraní Gremlin API, najdete v článku [provádět hromadné operace v rozhraní Gremlin API služby Azure Cosmos DB](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte nainstalovanou aktualizaci Visual Studio 2019, můžete si stáhnout a použít [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit vývoj pro Azure.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete. 

* [Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků. Nebo můžete použít [emulátor Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) u `https://localhost:8081` koncového bodu. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticating-requests).

* Vytvořte Azure Cosmos DB účet rozhraní SQL API pomocí postupu popsaného v části [vytvoření databázového účtu](create-sql-api-dotnet.md#create-account) v článku rychlý Start pro .NET. 

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď se podíváme na práci s kódem stažením některých ukázkových aplikací .NET z GitHubu. Tyto aplikace provádějí hromadnou operaci s Azure Cosmos DBmi daty. Chcete-li naklonovat aplikace, otevřete příkazový řádek, přejděte do adresáře, kam je chcete zkopírovat, a spusťte následující příkaz:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Klonovaný úložiště obsahuje dvě ukázky "BulkImportSample" a "BulkUpdateSample". Můžete otevřít některou z ukázkových aplikací, aktualizovat připojovací řetězce v souboru App. config pomocí připojovacích řetězců účtu Azure Cosmos DB, sestavit řešení a spustit ho. 

Aplikace "BulkImportSample" vygeneruje náhodné dokumenty a hromadně je naimportuje do Azure Cosmos DB. Aplikace "BulkUpdateSample" aktualizuje importované dokumenty zadáním oprav jako operací, které se mají provést u konkrétních polí dokumentu. V dalších částech si provedete kód v každé z těchto ukázkových aplikací.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Hromadné import dat do služby Azure Cosmos DB

1. Přejděte do složky "BulkImportSample" a otevřete soubor "BulkImportSample. sln".  

2. Připojovací řetězce Azure Cosmos DB jsou načteny ze souboru App. config, jak je znázorněno v následujícím kódu:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Programu pro import hromadné vytvoří novou databázi a kolekci s názvem databáze, název kolekce a propustnost hodnoty zadané v souboru App.config. 

3. Následující objekt DocumentClient se inicializuje s přímým režimem připojení TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Objekt BulkExecutor je inicializován s vysokou hodnotou opakování pro dobu čekání a omezené požadavky. A pak jsou nastavené na 0, aby bylo možné předávat řízení přetížení BulkExecutor po dobu života.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Aplikace vyvolá rozhraní BulkImportAsync API. Knihovna .NET poskytuje dvě přetížení rozhraní API hromadného importu – jeden, který přijímá seznam serializovaných dokumentů JSON a druhý přijímá seznam rekonstruovaných dokumentů POCO. Další informace o definicích těchto přetížených metod najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Metoda BulkImportAsync přijímá následující parametry:**
   
   |**Parametr**  |**Popis** |
   |---------|---------|
   |enableUpsert    |   Příznak, který povolí upsertuje dokumentů. Pokud dokument s daným ID již existuje, se aktualizuje. Ve výchozím nastavení je nastaveno na false.      |
   |disableAutomaticIdGeneration    |    Příznak, který chcete zakázat automatické generování ID. Ve výchozím nastavení, je nastavena na hodnotu true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maximální stupeň souběžnosti na rozsah klíče oddílu a nastavení na hodnotu null způsobí, že knihovna použije výchozí hodnotu 20. |
   |maxInMemorySortingBatchSize     |  Maximální počet dokumentů načtených z čítače výčtu dokumentů, který se předává volání rozhraní API v každé fázi.  V případě fáze řazení předběžného zpracování v paměti před hromadným importem nastavení na hodnotu null způsobí, že knihovna použije výchozí hodnotu min (Documents. Count, 1000000).       |
   |cancellationToken    |    Token zrušení pro bezproblémové ukončení hromadného importu.     |

   **Definice objektu odpovědi pro hromadné importy** Výsledek volání rozhraní API hromadného importu obsahuje následující atributy:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |NumberOfDocumentsImported (dlouhý)   |  Celkový počet dokumentů, které byly úspěšně naimportovány z dokumentů zadaný pro hromadného importu volání rozhraní API.       |
   |TotalRequestUnitsConsumed (Double)   |   Jednotky celkový počet žádostí (RU) využívaný hromadného importu volání rozhraní API.      |
   |TotalTimeTaken (TimeSpan)    |   Celková doba, za kterou hromadný import volání rozhraní API k dokončení provádění.      |
   |BadInputDocuments (seznam\<objektů >)   |     Seznam chybný formát dokumenty, které nebyly úspěšně naimportovány hromadně importovat volání rozhraní API. Uživatel musí opravit vrácených dokumentů a zkuste import zopakovat. Dokumenty ve formátu chybný obsahovat dokumenty, jejichž ID hodnota není řetězec (datový typ null nebo jakékoli jiné se považuje za neplatný).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Hromadné aktualizace dat ve službě Azure Cosmos DB

Pomocí rozhraní API BulkUpdateAsync můžete aktualizovat existující dokumenty. V tomto příkladu bude nastavena na novou hodnotu pole název a popis pole odebrat z existujících dokumentů. Úplnou sadu podporovaných operací aktualizace polí najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Přejděte do složky "BulkUpdateSample" a otevřete soubor "BulkUpdateSample. sln".  

2. Definujte položky aktualizace společně s odpovídajícími operacemi aktualizace polí. V tomto příkladu použijete SetUpdateOperation aktualizovat pole s názvem a UnsetUpdateOperation odebrání pole Popis všechny dokumenty. Můžete také provádět jiné operace jako přírůstek pole dokumentu podle konkrétní hodnoty, nasdílení změn určité hodnoty do pole nebo odebrat konkrétní hodnoty z pole. Další informace o různých metodách, které poskytuje rozhraní API pro hromadnou aktualizaci, najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Aplikace vyvolá rozhraní BulkUpdateAsync API. Další informace o definici metody BulkUpdateAsync najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Metoda BulkUpdateAsync přijímá následující parametry:**

   |**Parametr**  |**Popis** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Maximální stupeň souběžnosti na rozsah klíče oddílu, nastavení na hodnotu null způsobí, že knihovna použije výchozí hodnotu 20.   |
   |maxInMemorySortingBatchSize    |    Maximální počet položek aktualizace načtených z čítače výčtu položek, který se předává do volání rozhraní API v každé fázi pro fázi řazení předběžného zpracování v paměti před hromadnou aktualizací, nastavení na hodnotu null způsobí, že knihovna použije výchozí hodnotu min (updateItems. Count, 1000000).     |
   | cancellationToken|Token zrušení pro řádné ukončení hromadné aktualizace. |

   **Definice objektu odpovědi na hromadnou aktualizaci** Výsledek volání rozhraní API hromadné aktualizace obsahuje následující atributy:

   |**Parametr**  |**Popis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (dlouhý)    |   Celkový počet dokumentů, které byly úspěšně aktualizovány z těch, které byly zadány pro volání hromadné aktualizace rozhraní API.      |
   |TotalRequestUnitsConsumed (Double)   |    Jednotky celkový počet žádostí (RU) využívaný hromadné aktualizace volání rozhraní API.    |
   |TotalTimeTaken (TimeSpan)   | Celková doba, za kterou hromadného aktualizujte volání rozhraní API k dokončení provádění. |
    
## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Zvažte následující body pro zajištění lepšího výkonu při použití hromadné prováděcí modul knihovny:

* Nejlepšího výkonu dosáhnete, když aplikaci spustíte z virtuálního počítače Azure, který je ve stejné oblasti jako oblast zápisu Cosmos DB účtu.  

* Doporučuje se vytvořit instanci jednoho objektu BulkExecutor pro celou aplikaci v jednom virtuálním počítači, který odpovídá konkrétnímu kontejneru Cosmos.  

* Protože spuštění operace rozhraní API jednou hromadnou spotřebovává velké blok klientského počítače procesoru a sítě vstupně-výstupních operací. To se stane, vytvořením více úkolů interně, vyhněte se vytváření podřízeného procesu více souběžných úloh v rámci procesu aplikace, které volá každé rozhraní API provádění hromadné operace. Pokud jedno volání rozhraní API hromadné operace, které běží na jednom virtuálním počítači, nedokáže spotřebovat propustnost celého kontejneru (Pokud propustnost kontejneru > 1 000 000 RU/s), je vhodnější vytvořit samostatné virtuální počítače pro souběžné spouštění. volání rozhraní API hromadných operací.  

* Zajistěte, aby se InitializeAsync () vyvolala po vytvoření instance objektu BulkExecutor, aby se načetla cílová mapa oddílu kontejneru Cosmos.  

* V App. config aplikace ověřte, že je povolený **gcServer** pro lepší výkon.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Knihovna vygeneruje trasování, která je možné shromáždit buď do souboru protokolu, nebo do konzoly nástroje. Pokud to chcete povolit, přidejte do souboru App. config vaší aplikace následující:

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Další kroky
* Další informace o podrobnostech balíčku NuGet a poznámkách k verzi hromadné prováděcí knihovny .NET najdete v tématu[hromadné prováděcí informace sady SDK](sql-api-sdk-bulk-executor-dot-net.md). 
