---
title: Použití knihovny hromadného prováděcího modulu .NET v Azure Cosmos DB pro operace hromadného importu a aktualizace
description: Hromadný import a aktualizace Azure Cosmos DBch dokumentů pomocí knihovny hromadného prováděcího modulu .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365495"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Použití knihovny hromadného prováděcího modulu .NET k provádění hromadných operací v Azure Cosmos DB

Tento kurz poskytuje pokyny k použití knihovny hromadného prováděcího modulu .NET k importu a aktualizaci dokumentů do kontejneru Azure Cosmos. Další informace o knihovně hromadného prováděcího modulu a o tom, jak vám pomůže využít obrovské propustnost a úložiště, najdete v článku [Přehled knihovny hromadného prováděcího modulu](bulk-executor-overview.md) . V tomto kurzu se zobrazí ukázková aplikace .NET, která hromadně naimportuje náhodně generované dokumenty do kontejneru Azure Cosmos. Po importu se dozvíte, jak hromadně aktualizovat importovaná data zadáním oprav jako operací, které se mají provést u konkrétních polí dokumentu.

V současné době je knihovna hromadných prováděcích modulů podporována pouze pomocí Azure Cosmos DB rozhraní API SQL a účtů rozhraní API Gremlin. Tento článek popisuje použití knihovny hromadného prováděcího modulu .NET s účty rozhraní SQL API. Další informace o použití knihovny hromadného prováděcího modulu .NET s účty rozhraní Gremlin API najdete v tématu [provádění hromadných operací v rozhraní API pro Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte nainstalovanou aktualizaci Visual Studio 2019, můžete si stáhnout a použít [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit "vývoj pro Azure".

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

* [Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků. Nebo můžete použít [emulátor Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) s koncovým bodem `https://localhost:8081`. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticating-requests).

* Vytvořte Azure Cosmos DB účet rozhraní SQL API pomocí postupu popsaného v části [vytvoření databázového účtu](create-sql-api-dotnet.md#create-account) v článku rychlý Start pro .NET.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď se podíváme na práci s kódem stažením ukázkové aplikace .NET z GitHubu. Tato aplikace provádí hromadné operace s daty uloženými v účtu Azure Cosmos. K naklonování aplikace otevřete příkazový řádek, přejděte do adresáře, kam ho chcete zkopírovat, a spusťte následující příkaz:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Klonovaný úložiště obsahuje dvě ukázky "BulkImportSample" a "BulkUpdateSample". Můžete otevřít některou z ukázkových aplikací, aktualizovat připojovací řetězce v souboru App. config pomocí připojovacích řetězců účtu Azure Cosmos DB, sestavit řešení a spustit ho.

Aplikace "BulkImportSample" generuje náhodné dokumenty a hromadně je importuje do svého účtu Azure Cosmos. Aplikace "BulkUpdateSample" aktualizuje importované dokumenty zadáním oprav jako operací, které se mají provést u konkrétních polí dokumentu. V dalších částech si provedete kód v každé z těchto ukázkových aplikací.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Hromadné importy dat do účtu Azure Cosmos

1. Přejděte do složky "BulkImportSample" a otevřete soubor "BulkImportSample. sln".  

2. Připojovací řetězce Azure Cosmos DB jsou načteny ze souboru App. config, jak je znázorněno v následujícím kódu:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Hromadný import vytvoří novou databázi a kontejner s názvem databáze, názvem kontejneru a hodnotami propustnosti zadanými v souboru App. config.

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

5. Aplikace vyvolá rozhraní BulkImportAsync API. Knihovna .NET poskytuje dvě přetížení rozhraní API hromadného importu – jeden, který přijímá seznam serializovaných dokumentů JSON a druhý, který přijímá seznam rekonstruovaných dokumentů POCO. Další informace o definicích těchto přetížených metod najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

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
   
   |**Ukazatele**  |**Popis** |
   |---------|---------|
   |enableUpsert    |   Příznak, který povolí operace Upsert v dokumentech. Pokud dokument s daným ID již existuje, je aktualizován. Ve výchozím nastavení je nastaveno na false.      |
   |disableAutomaticIdGeneration    |    Příznak, který chcete zakázat automatické generování ID. Ve výchozím nastavení, je nastavena na hodnotu true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maximální stupeň souběžnosti na rozsah klíče oddílu a nastavení na hodnotu null způsobí, že knihovna použije výchozí hodnotu 20. |
   |maxInMemorySortingBatchSize     |  Maximální počet dokumentů, které jsou získány z enumerátoru dokumentu, který se předává volání rozhraní API v každé fázi. Pro fázi řazení v paměti, která se před hromadným importem stane, nastavení tohoto parametru na hodnotu null způsobí, že knihovna použije výchozí minimální hodnotu (Documents. Count, 1000000).       |
   |cancellationToken    |    Token zrušení pro řádné ukončení operace hromadného importu.     |

   **Definice objektu odpovědi pro hromadné importy** Výsledek volání rozhraní API hromadného importu obsahuje následující atributy:

   |**Ukazatele**  |**Popis**  |
   |---------|---------|
   |NumberOfDocumentsImported (dlouhý)   |  Celkový počet dokumentů, které byly úspěšně naimportovány z celkového počtu dokumentů dodaných do volání rozhraní API hromadného importu.       |
   |TotalRequestUnitsConsumed (Double)   |   Jednotky celkový počet žádostí (RU) využívaný hromadného importu volání rozhraní API.      |
   |TotalTimeTaken (TimeSpan)    |   Celková doba, kterou zabere volání rozhraní API hromadného importu k dokončení provádění.      |
   |BadInputDocuments (seznam\<> objektů)   |     Seznam chybný formát dokumenty, které nebyly úspěšně naimportovány hromadně importovat volání rozhraní API. Opravte vrácené dokumenty a opakujte import. Dokumenty ve formátu chybný obsahovat dokumenty, jejichž ID hodnota není řetězec (datový typ null nebo jakékoli jiné se považuje za neplatný).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Hromadná aktualizace dat ve vašem účtu Azure Cosmos

Pomocí rozhraní API BulkUpdateAsync můžete aktualizovat existující dokumenty. V tomto příkladu nastavíte pole `Name` na novou hodnotu a odeberete pole `Description` z existujících dokumentů. Úplnou sadu podporovaných operací aktualizace najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

1. Přejděte do složky "BulkUpdateSample" a otevřete soubor "BulkUpdateSample. sln".  

2. Definujte položky aktualizace společně s odpovídajícími operacemi aktualizace polí. V tomto příkladu použijete `SetUpdateOperation` k aktualizaci pole `Name` a `UnsetUpdateOperation` k odebrání `Description` pole ze všech dokumentů. Můžete také provádět jiné operace jako přírůstek pole dokumentu podle konkrétní hodnoty, nasdílení změn určité hodnoty do pole nebo odebrat konkrétní hodnoty z pole. Další informace o různých metodách, které poskytuje rozhraní API pro hromadnou aktualizaci, najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

   |**Ukazatele**  |**Popis** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Maximální stupeň souběžnosti na rozsah klíče oddílu, nastavením tohoto parametru na hodnotu null umožní knihovně používat výchozí hodnotu (20).   |
   |maxInMemorySortingBatchSize    |    Maximální počet položek aktualizace načtených z výčtu položek aktualizace předaných do volání rozhraní API v každé fázi. Pro fázi řazení v paměti, která se stane před hromadnou aktualizací, nastavení tohoto parametru na hodnotu null způsobí, že knihovna použije výchozí minimální hodnotu (updateItems. Count, 1000000).     |
   | cancellationToken|Token zrušení pro řádné ukončení operace hromadné aktualizace. |

   **Definice objektu odpovědi na hromadnou aktualizaci** Výsledek volání rozhraní API hromadné aktualizace obsahuje následující atributy:

   |**Ukazatele**  |**Popis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (dlouhý)    |   Počet dokumentů, které byly úspěšně aktualizovány z celkových dokumentů dodaných do volání hromadné aktualizace rozhraní API.      |
   |TotalRequestUnitsConsumed (Double)   |    Celkový počet jednotek žádosti (ru) spotřebované voláním rozhraní API hromadné aktualizace.    |
   |TotalTimeTaken (TimeSpan)   | Celková doba, kterou zabere volání rozhraní API hromadné aktualizace, aby se dokončilo provádění. |
    
## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Při použití knihovny hromadného prováděcího modulu zvažte následující body:

* Nejlepšího výkonu dosáhnete, když aplikaci spustíte z virtuálního počítače Azure, který je ve stejné oblasti jako oblast zápisu vašeho účtu Azure Cosmos.  

* Doporučuje se vytvořit instanci jednoho `BulkExecutor` objektu pro celou aplikaci v jednom virtuálním počítači, který odpovídá konkrétnímu kontejneru Azure Cosmos.  

* Vzhledem k tomu, že jedno spuštění rozhraní API hromadného zpracování spotřebovává velké množství dat procesoru klientského počítače a v/v sítě (k tomu dochází vytvořením několika úloh interně). Nevytvářejte v procesu aplikace více souběžných úloh, které spustí volání rozhraní API hromadného provozu. Pokud jedno volání rozhraní API hromadné operace, které běží na jednom virtuálním počítači, nedokáže spotřebovat propustnost celého kontejneru (Pokud propustnost kontejneru > 1 000 000 RU/s), je vhodnější vytvořit samostatné virtuální počítače pro souběžné spouštění volání rozhraní API hromadné operace.  

* Zajistěte, aby se metoda `InitializeAsync()` vyvolala po vytvoření instance objektu BulkExecutor, aby se načetla mapa oddílů cílového kontejneru Cosmos.  

* V App. config aplikace ověřte, že je povolený **gcServer** pro lepší výkon.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Knihovna vygeneruje trasování, která je možné shromáždit buď do souboru protokolu, nebo do konzoly nástroje. Pokud chcete povolit obojí, přidejte do souboru App. config aplikace následující kód.

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

* Další informace o podrobnostech balíčku NuGet a poznámkách k verzi najdete v [podrobnostech o sadě SDK hromadného prováděcího modulu](sql-api-sdk-bulk-executor-dot-net.md).
