---
title: Pomocí knihovny .NET vykonavatele hromadné provádět hromadné operace v Azure Cosmos DB | Microsoft Docs
description: Pomocí knihovny .NET vykonavatele hromadné Azure Cosmos DB hromadný import a aktualizace dokumentů pro Azure Cosmos DB kolekce.
keywords: Hromadné vykonavatele rozhraní .net
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: b09fd415c442c1e605987a6b25fd938ce04ce5c1
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300767"
---
# <a name="using-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Pomocí knihovny .NET vykonavatele hromadné provádět hromadné operace v Azure Cosmos DB

V tomto kurzu poskytuje pokyny k používání Azure Cosmos DB hromadné vykonavatele knihovny .NET importovat a aktualizovat dokumenty do kolekcí Azure Cosmos DB. Další informace o hromadné vykonavatele knihovny a jak ho pomáhá využívat obrovskou propustnost a úložiště najdete v tématu [přehled knihovny vykonavatele hromadné](bulk-executor-overview.md) článku. Tento kurz vás provede ukázkové aplikace .NET, která hromadné importy náhodně vygenerované dokumenty do kolekci Azure Cosmos DB. Po importu, ukazuje, jak můžete hromadně aktualizujete importovaných dat zadáním opravy jako operace provést na pole konkrétní dokumentu.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte nainstalované Visual Studio 2017, můžete stáhnout a použít [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Ujistěte se, že povolíte Azure development při instalaci sady Visual Studio.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete. 

* [Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků. Nebo můžete použít [emulátoru DB Cosmos Azure](https://docs.microsoft.com/azure/cosmos-db/local-emulator) s `https://localhost:8081` identifikátor URI. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticating-requests).

* Vytvoření účtu Azure Cosmos DB SQL API pomocí kroků popsaných v [vytvoření databázového účtu](create-sql-api-dotnet.md#create-a-database-account) v rozhraní .NET rychlý start článku. 

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď umožňuje přepnout do práce s kódem stažením některé ukázkové aplikace .NET z Githubu. Tyto aplikace provádět hromadné operace s daty Azure Cosmos DB. Klonování aplikace, otevřete příkazový řádek, přejděte do adresáře, kam chcete zkopírovat a spusťte následující příkaz:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Klonovaný úložiště obsahuje dvě ukázky "BulkImportSample" a "BulkUpdateSample." Můžete buď ukázkové aplikace otevřete, aktualizovat připojovací řetězce v souboru App.config pomocí účtu Azure Cosmos DB připojovací řetězce, sestavte řešení a potom ho spusťte. 

Aplikace "BulkImportSample" generuje náhodné dokumenty a hromadně importuje je do Azure Cosmos DB. Hromadné aplikace "BulkUpdateSample" aktualizuje importovaných dokumentů zadáním opravy jako operace provést na pole konkrétní dokumentu. V následujících částech zkontrolujete kód v každé z těchto ukázkových aplikací.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Hromadný import dat do databáze Azure Cosmos

1. Přejděte do složky "BulkImportSample" a "BulkImportSample.sln" soubor otevřít.  

2. Připojovací řetězce Azure Cosmos DB se načítají ze souboru App.config, jak je znázorněno v následujícím kódu:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Program pro import hromadné vytvoří novou databázi a kolekci s názvem databáze, název kolekce a propustnost hodnoty zadané v souboru App.config. 

3. Další objekt DocumentClient je inicializován s režimem TCP přímé připojení:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Objekt BulkExecutor je inicializován s hodnotami vysoké opakování dobu čekání a omezení požadavků. A potom jsou nastaveny na hodnotu 0 předat řízení zahlcení BulkExecutor pro celé jeho životnosti.  

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

5. Aplikace volá rozhraní API BulkImportAsync. Knihovny .NET poskytuje dvě přetížení hromadného importu rozhraní API – jeden, který přijímá seznam serializovaných dokumentů JSON a dalších přijímá seznam deserializovat objektů POCO dokumenty. Další informace o definicích každá z těchto metod přetížené, najdete v tématu [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

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
   |enableUpsert    |   Příznaku pro povolení upsert dokumentů. Pokud dokument s uvedenými id již existuje, jsou aktualizovány. Ve výchozím nastavení je nastavena na hodnotu false.      |
   |disableAutomaticIdGeneration    |    Příznak zakázat automatické generování ID. Ve výchozím nastavení je nastaven na hodnotu true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maximální stupeň souběžnosti na rozsah klíče oddílu, nastavení na hodnotu null způsobí, že knihovnu používat výchozí hodnota je 20. |
   |maxInMemorySortingBatchSize     |  Maximální počet dokumentů, jejichž načtený z dokumentu enumerátor, který je předán do rozhraní API volat v každé fázi.  V paměti předběžného zpracování řazení fázi před hromadný import nastavení na hodnotu null způsobí, že knihovna na výchozí hodnotu min (documents.count, 1000000).       |
   |CancellationToken    |    Token zrušení řádně ukončíte hromadného importu.     |

   **Hromadný import definice objektu odpovědi** výsledek hromadným importem volání rozhraní API obsahuje následující atributy:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Celkový počet dokumentů, které byly úspěšně importovány mimo dokumenty zadané hromadného importu volání rozhraní API.       |
   |TotalRequestUnitsConsumed (double)   |   Jednotek celkový žádosti (RU), které spotřebovávají hromadného importu volání rozhraní API.      |
   |TotalTimeTaken (TimeSpan)    |   Celková doba, za kterou hromadným importem volání rozhraní API pro dokončení provádění.      |
   |BadInputDocuments (seznam<object>)   |     Seznam chybný formát dokumenty, které nebyly úspěšně importovány v hromadného importu volání rozhraní API. Uživatel by měl opravte dokumenty vrácené a opakujte import. Dokumenty ve formátu chybný obsahovat dokumenty, jehož ID hodnota není řetězec (hodnotu null nebo jakýchkoli jiných datový typ je považovány za neplatné).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Hromadné aktualizace dat v Azure Cosmos DB

Pomocí rozhraní API BulkUpdateAsync můžete aktualizovat existující dokumenty. V tomto příkladu bude nastavena na novou hodnotu pole název a popis pole odebrat z existujících dokumentů. Pro úplnou sadu podporované pole operace aktualizace, použijte [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Přejděte do složky "BulkUpdateSample" a "BulkUpdateSample.sln" soubor otevřít.  

2. Definujte položky aktualizovat společně s odpovídající pole operace aktualizace. V tomto příkladu použijete SetUpdateOperation aktualizovat pole název a UnsetUpdateOperation pole popisu odebrání všechny dokumenty. Můžete také provést další operace, jako je přírůstek pole dokumentu určitou hodnotu, push konkrétní hodnoty do pole aplikace pole nebo odebrat konkrétní hodnotu z pole pole. Další informace o různých metod, které jsou součástí hromadné aktualizace rozhraní API, naleznete [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Aplikace volá rozhraní API BulkUpdateAsync. Další informace o definici metody BulkUpdateAsync, najdete v tématu [dokumentaci k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

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
   |maxConcurrencyPerPartitionKeyRange    |   Maximální stupeň souběžnosti na rozsah klíče oddílu, nastavení na hodnotu null způsobí, že knihovna na výchozí hodnotu 20.   |
   |maxInMemorySortingBatchSize    |    Maximální počet položek aktualizace načtený z položky enumerátor aktualizace předaný volání rozhraní API v každé fázi předběžného zpracování řazení fázi před hromadné aktualizace v paměti, nastavení na hodnotu null způsobí, že knihovna na výchozí hodnotu min (updateItems.count, 1000000).     |
   | CancellationToken|Token zrušení řádně ukončíte hromadné aktualizace. |

   **Hromadné aktualizace definice objektu odpovědi** výsledek hromadné aktualizace volání rozhraní API obsahuje následující atributy:

   |**Parametr**  |**Popis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Celkový počet dokumentů, které byly úspěšně aktualizovány ze souborů zadaný do hromadné aktualizace volání rozhraní API.      |
   |TotalRequestUnitsConsumed (double)   |    Jednotek celkový žádosti (RU) spotřebovávána hromadné aktualizace volání rozhraní API.    |
   |TotalTimeTaken (TimeSpan)   | Celková doba, kterou hromadným aktualizujte volání rozhraní API pro dokončení provádění. |
    
## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Zvažte následující pro lepší výkon při použití hromadné vykonavatele knihovny:

* Pro nejlepší výkon spusťte aplikaci z Azure virtuálního počítače, který je ve stejné oblasti jako zápisu oblast účtu Cosmos DB.  

* Doporučuje se vytvořit instanci jednoho objektu BulkExecutor pro celou aplikaci v rámci jednoho virtuálního počítače odpovídající určité kolekci Cosmos DB.  

* Vzhledem k tomu, že spuštění jedné hromadné operace rozhraní API spotřebuje velký blok klientský počítač procesoru a sítě vstupně-výstupní operace. To se stane vytvořením několika úloh interně, vyhněte se například více souběžných úloh v rámci procesu vaší aplikace, které volá každé rozhraní API provádění hromadné operace. Pokud volání jedné hromadné operace rozhraní API systémem jeden virtuální počítač nemůže využívat propustnost celou kolekci (Pokud vaše kolekce propustnost > 1 milion RU/s), je vhodnější k vytvoření samostatných virtuálních počítačů současně provést hromadné volání operace rozhraní API.  

* Zajistěte, aby že initializeasync() je volána po vytvoření instance objektu BulkExecutor načíst mapa oddílu cíl Cosmos DB kolekce.  

* V souboru App.Config vaší aplikace, zajistěte **gcserver –** je povolený pro lepší výkon
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Knihovny vysílá trasování, které se můžou shromažďovat do souboru protokolu nebo v konzole. Chcete-li obě, přidejte následující App.Config vaší aplikace.

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

## <a name="next-steps"></a>Další postup
* Další informace o podrobnosti balíčku Nuget a knihovny .net vykonavatele hromadné poznámky k verzi, přečtěte si téma[hromadné vykonavatele SDK podrobnosti](sql-api-sdk-bulk-executor-dot-net.md). 
