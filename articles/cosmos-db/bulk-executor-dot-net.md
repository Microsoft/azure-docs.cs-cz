---
title: Použití knihovny hromadného prováděcího modulu .NET v Azure Cosmos DB pro operace hromadného importu a aktualizace
description: Hromadné importujte a aktualizujte dokumenty Azure Cosmos DB pomocí knihovny hromadného vykonavatele .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246874"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Použití knihovny hromadného prováděcího modulu .NET k provádění hromadných operací v Azure Cosmos DB

Tento kurz obsahuje pokyny k použití hromadné hodovacího modulu .NET knihovny k importu a aktualizaci dokumentů do kontejneru Azure Cosmos. Další informace o knihovně hromadného prováděcího modulu a o tom, jak vám pomáhá využít masivní propustnost a úložiště, najdete v článku [přehled knihovny hromadného prováděcího modulu.](bulk-executor-overview.md) V tomto kurzu uvidíte ukázkovou aplikaci .NET, která hromadně importuje náhodně generované dokumenty do kontejneru Azure Cosmos. Po importu se zobrazí způsob hromadné aktualizace importovaných dat zadáním oprav jako operací, které se mají provádět v určitých polích dokumentu.

V současné době je knihovna hromadného prováděcího modulu podporovaná jenom pomocí účtů SQL API Azure Cosmos DB a gremlinského rozhraní API. Tento článek popisuje použití hromadné vykonavatel .NET knihovny s účty rozhraní SQL API. Informace o použití hromadné hostovětní knihovny .NET s účty Gremlin API najdete [v tématu provádění hromadných operací v rozhraní API Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Ujistěte se, že povolíte "Vývoj Azure" během nastavení Sady Visual Studio.

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) než začnete.

* Azure [Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků. Nebo můžete použít [emulátor Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/local-emulator) DB `https://localhost:8081` s koncovým bodem. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticating-requests).

* Vytvořte účet SQL API Azure Cosmos DB pomocí kroků popsaných v části [vytvoření databázového účtu](create-sql-api-dotnet.md#create-account) v článku rychlého startu .NET.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Nyní přejdeme k práci s kódem stažením ukázkové aplikace .NET z GitHubu. Tato aplikace provádí hromadné operace s daty uloženými ve vašem účtu Azure Cosmos. Chcete-li aplikaci klonovat, otevřete příkazový řádek, přejděte do adresáře, do kterého ji chcete zkopírovat, a spusťte následující příkaz:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Klonované úložiště obsahuje dva ukázky "BulkImportSample" a "BulkUpdateSample". Můžete otevřít některou z ukázkových aplikací, aktualizovat připojovací řetězce v souboru App.config pomocí připojovacích řetězců účtu Azure Cosmos DB, vytvořit řešení a spustit.

Aplikace BulkImportSample generuje náhodné dokumenty a hromadně je importuje do vašeho účtu Azure Cosmos. Aplikace BulkUpdateSample hromadně aktualizuje importované dokumenty zadáním oprav jako operací, které mají být v polích určitých dokumentů. V dalších částech zkontrolujete kód v každé z těchto ukázkových aplikací.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Hromadná data importu do účtu Azure Cosmos

1. Přejděte do složky BulkImportSample a otevřete soubor BulkImportSample.sln.  

2. Připojovací řetězce Azure Cosmos DB se načítají ze souboru App.config, jak je znázorněno v následujícím kódu:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Velkoobjemový importér vytvoří novou databázi a kontejner s názvem databáze, názvem kontejneru a hodnotami propustnosti zadanými v souboru App.config.

3. Dále je objekt DocumentClient inicializován v režimu přímého připojení TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor objekt je inicializován s vysokou hodnotou opakování pro čekací doby a omezené požadavky. A pak jsou nastaveny na 0 předat řízení přetížení BulkExecutor po celou dobu jeho životnosti.  

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

5. Aplikace vyvolá bulkimportasynchronní rozhraní API. Knihovna .NET poskytuje dvě přetížení rozhraní API hromadného importu – jedno, které přijímá seznam serializovaných dokumentů JSON, a druhé, které přijímá seznam rekonstruovaných dokumentů POCO. Další informace o definicích jednotlivých přetížených metod naleznete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync metoda přijímá následující parametry:**
   
   |**Parametr**  |**Popis** |
   |---------|---------|
   |enableUpsert    |   Příznak umožňující operace upsert na dokumenty. Pokud dokument s daným ID již existuje, aktualizuje se. Ve výchozím nastavení je nastavena na false.      |
   |disableAutomatickéIdGeneration    |    Příznak zakázat automatické generování ID. Ve výchozím nastavení je nastavena na hodnotu true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maximální stupeň souběžnosti na rozsah klíčů oddílu, nastavení null způsobí, že knihovna použít výchozí hodnotu 20. |
   |maxInMemorySortingBatchSize     |  Maximální počet dokumentů, které jsou vyžádány z čítače výčtu dokumentu, který je předán volání rozhraní API v každé fázi. Pro fázi řazení v paměti, ke které dojde před hromadným importem, nastavení tohoto parametru na hodnotu null způsobí, že knihovna použije výchozí minimální hodnotu (documents.count, 1000000).       |
   |Cancellationtoken    |    Token zrušení řádně ukončit operaci hromadného importu.     |

   **Definice objektu odpovědi hromadného importu** Výsledek volání rozhraní API hromadného importu obsahuje následující atributy:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |NumberOfDocumentsImported (dlouhý)   |  Celkový počet dokumentů, které byly úspěšně importovány z celkového počtu dokumentů dodaných do volání rozhraní API hromadného importu.       |
   |TotalRequestUnitsConsumed (double)   |   Celkový počet jednotek požadavku (RU) spotřebovaných voláním rozhraní API hromadného importu.      |
   |TotalTimeTaken (TimeSpan)    |   Celková doba, kterou volání rozhraní API hromadného importu trvalo k dokončení provádění.      |
   |BadInputDocuments (> objektu seznamu)\<   |     Seznam dokumentů chybného formátu, které nebyly úspěšně importovány v volání rozhraní API hromadného importu. Opravte vrácené dokumenty a opakujte import. Chybně formátované dokumenty zahrnují dokumenty, jejichž hodnota ID není řetězec (null nebo jakýkoli jiný datový typ je považován za neplatný).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Hromadná data aktualizací v účtu Azure Cosmos

Existující dokumenty můžete aktualizovat pomocí rozhraní BulkUpdateAsync API. V tomto příkladu `Name` nastavíte pole na novou `Description` hodnotu a odeberete pole z existujících dokladů. Úplnou sadu podporovaných operací aktualizace naleznete v [dokumentaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)k rozhraní API .

1. Přejděte do složky BulkUpdateSample a otevřete soubor BulkUpdateSample.sln.  

2. Definujte položky aktualizace spolu s odpovídajícími operacemi aktualizace polí. V tomto příkladu `SetUpdateOperation` použijete `Name` k `UnsetUpdateOperation` aktualizaci `Description` pole a odebrání pole ze všech dokladů. Můžete také provádět další operace, jako je zvýšení pole dokumentu o určitou hodnotu, nabízení určitých hodnot do pole nebo odebrání určité hodnoty z pole pole. Další informace o různých metodách poskytovaných rozhraním API pro hromadnou aktualizaci naleznete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Aplikace vyvolá rozhraní BulkUpdateAsync API. Další informace o definici metody BulkUpdateAsync naleznete v [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync metoda přijímá následující parametry:**

   |**Parametr**  |**Popis** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Maximální stupeň souběžnosti na rozsah klíčů oddílu, nastavení tohoto parametru na hodnotu null způsobí, že knihovna použije výchozí hodnotu(20).   |
   |maxInMemorySortingBatchSize    |    Maximální počet položek aktualizace natažené z čítače aktualizovat položky předaných volání rozhraní API v každé fázi. Pro fázi řazení v paměti, ke které dojde před hromadnou aktualizací, nastavení tohoto parametru na hodnotu null způsobí, že knihovna použije výchozí minimální hodnotu (updateItems.count, 1000000).     |
   | Cancellationtoken|Token zrušení řádně ukončit operaci hromadné aktualizace. |

   **Definice objektu odpovědi hromadné aktualizace** Výsledek volání rozhraní API hromadné aktualizace obsahuje následující atributy:

   |**Parametr**  |**Popis** |
   |---------|---------|
   |NumberOfDocumentsAktualizován (dlouhý)    |   Počet dokumentů, které byly úspěšně aktualizovány z celkového počtu dokumentů dodaných do volání rozhraní API hromadné aktualizace.      |
   |TotalRequestUnitsConsumed (double)   |    Celkový počet jednotek požadavku (RU) spotřebovaných voláním rozhraní API hromadné aktualizace.    |
   |TotalTimeTaken (TimeSpan)   | Celková doba, kterou volání rozhraní API hromadné aktualizace k dokončení provádění. |
    
## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Při použití knihovny hromadného vykonavatele zvažte následující body pro lepší výkon:

* Pro dosažení nejlepšího výkonu spusťte aplikaci z virtuálního počítače Azure, který je ve stejné oblasti jako oblast zápisu vašeho účtu Azure Cosmos.  

* Doporučujeme vytvořit instanci jednoho `BulkExecutor` objektu pro celou aplikaci v rámci jednoho virtuálního počítače, který odpovídá konkrétní kontejner Azure Cosmos.  

* Vzhledem k tomu, že spuštění rozhraní API s jednou hromadnou operací spotřebovává velký blok vznětového zařízení klientského počítače a vi.– to se stane vytvořením více úloh interně). Vyhněte se tření více souběžných úloh v rámci procesu aplikace, které spouštějí volání rozhraní API hromadné operace. Pokud volání rozhraní API s jednou hromadnou operací, které běží na jednom virtuálním počítači, nemůže spotřebovat propustnost celého kontejneru (pokud propustnost vašeho kontejneru > 1 milion RU/s), je upřednostňováno vytvoření samostatných virtuálních počítačů pro souběžné spuštění volání rozhraní API hromadné operace.  

* Ujistěte `InitializeAsync()` se, že metoda je vyvolána po vytvoření instance BulkExecutor objekt načíst cíl Cosmos kontejneru mapa oddílů.  

* V aplikaci App.Config, ujistěte se, **že gcServer** je povolenpro lepší výkon
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Knihovna vydává trasování, které lze shromažďovat do souboru protokolu nebo v konzole. Chcete-li povolit obojí, přidejte do souboru App.Config aplikace následující kód.

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

* Informace o podrobnostech balíčku Nuget a poznámkách k verzi naleznete v [podrobnostech hromadného prováděcího modulu SDK](sql-api-sdk-bulk-executor-dot-net.md).
