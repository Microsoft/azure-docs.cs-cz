---
title: Použití knihovny hromadného prováděcího modulu .NET v Azure Cosmos DB pro operace hromadného importu a aktualizace
description: Hromadný import a aktualizace Azure Cosmos DBch dokumentů pomocí knihovny hromadného prováděcího modulu .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: b2fb3e2031d5656668b9971fdf357f66824179fc
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90975874"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Použití knihovny hromadného prováděcího modulu .NET k provádění hromadných operací v Azure Cosmos DB

> [!NOTE]
> Tato knihovna hromadného prováděcího modulu popsaná v tomto článku je udržována pro aplikace používající verzi sady .NET SDK 2. x. Pro nové aplikace můžete použít **hromadnou podporu** , která je přímo k dispozici v sadě [.NET SDK verze 3. x](tutorial-sql-api-dotnet-bulk-import.md) a nevyžaduje žádnou externí knihovnu. 

> Pokud aktuálně používáte knihovnu hromadného prováděcího modulu a plánujete migrovat na hromadnou podporu v novější sadě SDK, použijte postup v [Průvodci migrací](how-to-migrate-from-bulk-executor-library.md) k migraci aplikace.

Tento kurz obsahuje pokyny k importu a aktualizaci dokumentů v kontejneru Azure Cosmos s využitím knihovny Bulk Executor pro .NET. Další informace o knihovně hromadného prováděcího modulu a o tom, jak vám pomůže využít obrovské propustnost a úložiště, najdete v článku [Přehled knihovny hromadného prováděcího modulu](bulk-executor-overview.md) . V tomto kurzu se zobrazí ukázková aplikace .NET, která hromadně naimportuje náhodně generované dokumenty do kontejneru Azure Cosmos. Po importu se dozvíte, jak hromadně aktualizovat importovaná data zadáním oprav jako operací, které se mají provést u konkrétních polí dokumentu.

V současné době je knihovna hromadných prováděcích modulů podporována pouze pomocí Azure Cosmos DB rozhraní API SQL a účtů rozhraní API Gremlin. Tento článek popisuje použití knihovny hromadného prováděcího modulu .NET s účty rozhraní SQL API. Další informace o použití knihovny hromadného prováděcího modulu .NET s účty rozhraní Gremlin API najdete v tématu [provádění hromadných operací v rozhraní API pro Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte nainstalovanou aktualizaci Visual Studio 2019, můžete si stáhnout a použít [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit "vývoj pro Azure".

* Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Můžete [vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků. Nebo můžete použít [emulátor Azure Cosmos DB](/azure/cosmos-db/local-emulator) u `https://localhost:8081` koncového bodu. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticate-requests).

* Vytvořte Azure Cosmos DB účet rozhraní SQL API pomocí postupu popsaného v části [vytvoření databázového účtu](create-sql-api-dotnet.md#create-account) v článku rychlý Start pro .NET.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď se podíváme na práci s kódem stažením ukázkové aplikace .NET z GitHubu. Tato aplikace provádí hromadné operace s daty uloženými v účtu Azure Cosmos. K naklonování aplikace otevřete příkazový řádek, přejděte do adresáře, kam ho chcete zkopírovat, a spusťte následující příkaz:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Klonovaný úložiště obsahuje dvě ukázky "BulkImportSample" a "BulkUpdateSample". Můžete otevřít některou z ukázkových aplikací, aktualizovat připojovací řetězce v souboru App.config pomocí připojovacích řetězců vašeho účtu Azure Cosmos DB, sestavit řešení a spustit ho.

Aplikace "BulkImportSample" generuje náhodné dokumenty a hromadně je importuje do svého účtu Azure Cosmos. Aplikace "BulkUpdateSample" aktualizuje importované dokumenty zadáním oprav jako operací, které se mají provést u konkrétních polí dokumentu. V dalších částech si provedete kód v každé z těchto ukázkových aplikací.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Hromadné importy dat do účtu Azure Cosmos

1. Přejděte do složky "BulkImportSample" a otevřete soubor "BulkImportSample. sln".  

2. Připojovací řetězce Azure Cosmos DB jsou načteny ze souboru App.config, jak je znázorněno v následujícím kódu:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Hromadný import vytvoří novou databázi a kontejner s názvem databáze, názvem kontejneru a hodnotami propustnosti zadanými v souboru App.config.

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

5. Aplikace vyvolá rozhraní BulkImportAsync API. Knihovna .NET poskytuje dvě přetížení rozhraní API hromadného importu – jeden, který přijímá seznam serializovaných dokumentů JSON a druhý, který přijímá seznam rekonstruovaných dokumentů POCO. Další informace o definicích těchto přetížených metod najdete v [dokumentaci k rozhraní API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet&preserve-view=true).

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
   |enableUpsert    |   Příznak, který povolí operace Upsert v dokumentech. Pokud dokument s daným ID již existuje, je aktualizován. Ve výchozím nastavení je nastaveno na false.      |
   |disableAutomaticIdGeneration    |    Příznak, který zakáže automatickou generaci IDENTIFIKÁTORů. Ve výchozím nastavení je nastavena na hodnotu true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maximální stupeň souběžnosti na rozsah klíče oddílu a nastavení na hodnotu null způsobí, že knihovna použije výchozí hodnotu 20. |
   |maxInMemorySortingBatchSize     |  Maximální počet dokumentů, které jsou získány z enumerátoru dokumentu, který se předává volání rozhraní API v každé fázi. Pro fázi řazení v paměti, která se před hromadným importem stane, nastavení tohoto parametru na hodnotu null způsobí, že knihovna použije výchozí minimální hodnotu (Documents. Count, 1000000).       |
   |cancellationToken    |    Token zrušení pro řádné ukončení operace hromadného importu.     |

   **Definice objektu odpovědi pro hromadné importy** Výsledek volání rozhraní API hromadného importu obsahuje následující atributy:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |NumberOfDocumentsImported (dlouhý)   |  Celkový počet dokumentů, které byly úspěšně naimportovány z celkového počtu dokumentů dodaných do volání rozhraní API hromadného importu.       |
   |TotalRequestUnitsConsumed (Double)   |   Celkový počet jednotek žádosti (RU) spotřebovaných voláním rozhraní API hromadného importu.      |
   |TotalTimeTaken (TimeSpan)    |   Celková doba, kterou zabere volání rozhraní API hromadného importu k dokončení provádění.      |
   |BadInputDocuments (seznam \<object> )   |     Seznam dokumentů se špatným formátem, které nebyly úspěšně importovány v volání rozhraní API hromadného importu. Opravte vrácené dokumenty a opakujte import. Chybné – formátované dokumenty obsahují dokumenty, jejichž hodnota ID není řetězec (null nebo jakýkoli jiný datový typ se považuje za neplatné).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Hromadná aktualizace dat ve vašem účtu Azure Cosmos

Existující dokumenty můžete aktualizovat pomocí rozhraní BulkUpdateAsync API. V tomto příkladu nastavíte `Name` pole na novou hodnotu a odeberete `Description` pole z existujících dokumentů. Úplnou sadu podporovaných operací aktualizace najdete v [dokumentaci k rozhraní API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet&preserve-view=true).

1. Přejděte do složky "BulkUpdateSample" a otevřete soubor "BulkUpdateSample. sln".  

2. Definujte položky aktualizace společně s odpovídajícími operacemi aktualizace polí. V tomto příkladu použijete `SetUpdateOperation` k aktualizaci `Name` pole a `UnsetUpdateOperation` k odebrání `Description` pole ze všech dokumentů. Můžete také provádět jiné operace, jako je například zvýšení pole dokumentu konkrétní hodnotou, zadání specifických hodnot do pole pole nebo odebrání konkrétní hodnoty z pole Array. Další informace o různých metodách, které poskytuje rozhraní API pro hromadnou aktualizaci, najdete v [dokumentaci k rozhraní API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet&preserve-view=true).

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

3. Aplikace vyvolá rozhraní BulkUpdateAsync API. Další informace o definici metody BulkUpdateAsync najdete v [dokumentaci k rozhraní API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet&preserve-view=true).  

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
   |maxConcurrencyPerPartitionKeyRange    |   Maximální stupeň souběžnosti na rozsah klíče oddílu, nastavením tohoto parametru na hodnotu null umožní knihovně používat výchozí hodnotu (20).   |
   |maxInMemorySortingBatchSize    |    Maximální počet položek aktualizace načtených z výčtu položek aktualizace předaných do volání rozhraní API v každé fázi. Pro fázi řazení v paměti, která se stane před hromadnou aktualizací, nastavení tohoto parametru na hodnotu null způsobí, že knihovna použije výchozí minimální hodnotu (updateItems. Count, 1000000).     |
   | cancellationToken|Token zrušení pro řádné ukončení operace hromadné aktualizace. |

   **Definice objektu odpovědi na hromadnou aktualizaci** Výsledek volání rozhraní API hromadné aktualizace obsahuje následující atributy:

   |**Parametr**  |**Popis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (dlouhý)    |   Počet dokumentů, které byly úspěšně aktualizovány z celkových dokumentů dodaných do volání hromadné aktualizace rozhraní API.      |
   |TotalRequestUnitsConsumed (Double)   |    Celkový počet jednotek žádosti (ru) spotřebované voláním rozhraní API hromadné aktualizace.    |
   |TotalTimeTaken (TimeSpan)   | Celková doba, kterou zabere volání rozhraní API hromadné aktualizace, aby se dokončilo provádění. |
    
## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Při použití knihovny hromadného prováděcího modulu zvažte následující body:

* Nejlepšího výkonu dosáhnete, když aplikaci spustíte z virtuálního počítače Azure, který je ve stejné oblasti jako oblast zápisu vašeho účtu Azure Cosmos.  

* Doporučuje se vytvořit instanci jednoho `BulkExecutor` objektu pro celou aplikaci v jednom virtuálním počítači, který odpovídá konkrétnímu kontejneru Azure Cosmos.  

* Vzhledem k tomu, že jedno spuštění rozhraní API hromadného zpracování spotřebovává velké množství dat procesoru klientského počítače a v/v sítě (k tomu dochází vytvořením několika úloh interně). Nevytvářejte v procesu aplikace více souběžných úloh, které spustí volání rozhraní API hromadného provozu. Pokud jedno volání rozhraní API hromadné operace, které běží na jednom virtuálním počítači, nedokáže spotřebovat propustnost celého kontejneru (Pokud propustnost kontejneru > 1 000 000 RU/s), je vhodnější vytvořit samostatné virtuální počítače pro souběžné spouštění volání rozhraní API hromadné operace.  

* Zajistěte, aby se `InitializeAsync()` Metoda vyvolala po vytvoření instance objektu BulkExecutor, aby se načetla mapa oddílů cílového kontejneru Cosmos.  

* V App.Config vaší aplikace ověřte, že je povolený **gcServer** pro lepší výkon.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Knihovna vygeneruje trasování, která je možné shromáždit buď do souboru protokolu, nebo do konzoly nástroje. Pokud chcete povolit obojí, přidejte do souboru App.Config vaší aplikace následující kód.

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
