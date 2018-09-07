---
title: Pomocí knihovny .NET prováděcí modul hromadného budou provádět hromadné operace ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Pomocí knihovny .NET hromadné prováděcí modul služby Azure Cosmos DB pro hromadný import a aktualizaci dokumentů do kontejnerů Azure Cosmos DB.
keywords: Prováděcí modul hromadného .net
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: cc0faa44501ea130309a02bb48d02f9c5b33febd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053376"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Pomocí knihovny .NET prováděcí modul hromadného budou provádět hromadné operace ve službě Azure Cosmos DB

Tento kurz obsahuje pokyny k používání rozhraní Azure Cosmos DB hromadné prováděcí modul knihovny .NET pro import a aktualizace dokumentů do kontejneru Azure Cosmos DB. Další informace o hromadně prováděcí modul knihovny a jak vám může pomoct využít mimořádně velkou propustnost a úložiště, najdete v článku [přehled knihovny prováděcí modul hromadného](bulk-executor-overview.md) článku. Tento kurz vás provede ukázkovou aplikaci .NET, hromadné importy náhodně generované dokumenty do kontejneru Azure Cosmos DB. Po naimportování pak ukazuje, jak můžete provést hromadnou aktualizaci importovaná data tak, že zadáte jako operace mají provést na pole určitého dokumentu opravy.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Ujistěte se, že umožňují vývoj pro Azure během instalace sady Visual Studio.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete. 

* [Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků. Nebo můžete použít [emulátor služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) s `https://localhost:8081` identifikátoru URI. Primární klíč je uvedený v části [Ověřování požadavků](local-emulator.md#authenticating-requests).

* Vytvoření účtu rozhraní SQL API služby Azure Cosmos DB pomocí kroků popsaných v [vytvoření databázového účtu](create-sql-api-dotnet.md#create-a-database-account) část tohoto článku rychlý start .NET. 

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem stažením některé ukázkové aplikace .NET z Githubu. Tyto aplikace provádět hromadné operace s daty služby Azure Cosmos DB. Klonování aplikací, otevřete příkazový řádek, přejděte do adresáře, kam chcete zkopírovat na ně a spusťte následující příkaz:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Naklonované úložiště obsahuje dvě ukázky "BulkImportSample" a "BulkUpdateSample." Můžete otevřít buď z ukázkových aplikací, připojovací řetězce v souboru App.config aktualizujte připojovací řetězce svého účtu Azure Cosmos DB, sestavte řešení a spustíme ji. 

Aplikace "BulkImportSample" generuje náhodné dokumenty a hromadně naimportuje je do služby Azure Cosmos DB. Hromadné "BulkUpdateSample" aplikace aktualizuje tak, že zadáte jako operace mají provést na pole určitého dokumentu opravy importovaných dokumentů. V následujících částech se podívejte do kódu v každé z těchto ukázkových aplikací.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Hromadné import dat do služby Azure Cosmos DB

1. Přejděte do složky "BulkImportSample" a "BulkImportSample.sln" soubor otevřít.  

2. Azure Cosmos DB připojovací řetězce jsou načteny ze souboru App.config, jak je znázorněno v následujícím kódu:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Programu pro import hromadné vytvoří novou databázi a kolekci s názvem databáze, název kolekce a propustnost hodnoty zadané v souboru App.config. 

3. Vedle objektu DocumentClient je inicializován s režimem připojení protokolu TCP s přímým přístupem:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor objekt je inicializován s hodnotami vysoké opakování pro doba čekání a omezuje požadavky. A pak jsou nastaveny na hodnotu 0 pro předání řízení zahlcení BulkExecutor po dobu jeho existence.  

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

5. Aplikace volá rozhraní API BulkImportAsync. Knihovna .NET poskytuje dvě přetížení hromadného importu rozhraní API – ten, který přijímá seznam serializovaná dokumentů JSON a druhý seznam deserializovaný dokumenty POCO. Další informace o definicích každého z těchto přetížených metod, naleznete [dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

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
   |enableUpsert    |   Příznak, který chcete povolit upsert dokumenty. Pokud dokument s daným id již existuje, se aktualizuje. Ve výchozím nastavení je nastavena na hodnotu false.      |
   |disableAutomaticIdGeneration    |    Příznak, který chcete zakázat automatické generování ID. Ve výchozím nastavení, je nastavena na hodnotu true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maximální stupeň souběžnosti na rozsah klíče oddílu, nastavení na hodnotu null způsobí, že knihovna použít výchozí hodnotu 20. |
   |maxInMemorySortingBatchSize     |  Maximální počet dokumentů získaných z dokumentu enumerátor, který je předán do rozhraní API volat v každé fázi.  V paměti předběžného zpracování řazení fázi před hromadný import nastavení na hodnotu null způsobí knihovny používat výchozí hodnota min (documents.count, 1000000).       |
   |cancellationToken    |    Token zrušení pro hromadný import řádně ukončit.     |

   **Hromadný import definice objektu odpovědi** výsledek volání rozhraní API hromadného importu obsahuje následující atributy:

   |**Parametr**  |**Popis**  |
   |---------|---------|
   |NumberOfDocumentsImported (dlouhé)   |  Celkový počet dokumentů, které byly úspěšně naimportovány z dokumentů zadaný pro hromadného importu volání rozhraní API.       |
   |TotalRequestUnitsConsumed (double)   |   Jednotky celkový počet žádostí (RU) využívaný hromadného importu volání rozhraní API.      |
   |TotalTimeTaken (časový interval)    |   Celková doba, za kterou hromadný import volání rozhraní API k dokončení provádění.      |
   |BadInputDocuments (seznam<object>)   |     Seznam chybný formát dokumenty, které nebyly úspěšně naimportovány hromadně importovat volání rozhraní API. Uživatel musí opravit vrácených dokumentů a zkuste import zopakovat. Dokumenty ve formátu chybný obsahovat dokumenty, jejichž ID hodnota není řetězec (datový typ null nebo jakékoli jiné se považuje za neplatný).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Hromadné aktualizace dat ve službě Azure Cosmos DB

Pomocí rozhraní API BulkUpdateAsync můžete aktualizovat existující dokumenty. V tomto příkladu bude nastavena na novou hodnotu pole název a popis pole odebrat z existujících dokumentů. Pro úplnou sadu podporovaných polí operace aktualizace, přečtěte si [dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Přejděte do složky "BulkUpdateSample" a "BulkUpdateSample.sln" soubor otevřít.  

2. Definujte aktualizace položek spolu s odpovídající operace aktualizace pole. V tomto příkladu použijete SetUpdateOperation aktualizovat pole s názvem a UnsetUpdateOperation odebrání pole Popis všechny dokumenty. Můžete také provádět jiné operace jako přírůstek pole dokumentu podle konkrétní hodnoty, nasdílení změn určité hodnoty do pole nebo odebrat konkrétní hodnoty z pole. Další informace o různých způsobech hromadné aktualizace rozhraní API poskytuje, naleznete [dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Aplikace volá rozhraní API BulkUpdateAsync. Další informace o definici metody BulkUpdateAsync, najdete v tématu [dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

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
   |maxInMemorySortingBatchSize    |    Maximální počet položek aktualizace získaných z výčtu aktualizace položek předává do volání rozhraní API v jednotlivých fázích pro fázi předběžného zpracování řazení v paměti před hromadných aktualizací, nastavení na hodnotu null způsobí, že knihovna používat výchozí hodnota min (updateItems.count, 1 000 000).     |
   | cancellationToken|Token zrušení řádně ukončit hromadné aktualizace. |

   **Hromadná aktualizace definice objektu odpovědi** výsledek hromadné aktualizace volání rozhraní API obsahuje následující atributy:

   |**Parametr**  |**Popis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (dlouhé)    |   Celkový počet dokumentů, které byly úspěšně aktualizovány mimo těch, které jsou předány hromadné aktualizace volání rozhraní API.      |
   |TotalRequestUnitsConsumed (double)   |    Jednotky celkový počet žádostí (RU) využívaný hromadné aktualizace volání rozhraní API.    |
   |TotalTimeTaken (časový interval)   | Celková doba, za kterou hromadného aktualizujte volání rozhraní API k dokončení provádění. |
    
## <a name="performance-tips"></a>Tipy pro zvýšení výkonu 

Zvažte následující body pro zajištění lepšího výkonu při použití hromadné prováděcí modul knihovny:

* Pro zajištění nejlepšího výkonu spusťte aplikaci z virtuálního počítače Azure, který je ve stejné oblasti jako oblast zápisu účtu Cosmos DB.  

* Doporučuje se vytvořit instanci jednoho objektu BulkExecutor pro celou aplikaci v rámci jedné virtuální počítač odpovídající konkrétní kontejneru Cosmos DB.  

* Protože spuštění operace rozhraní API jednou hromadnou spotřebovává velké blok klientského počítače procesoru a sítě vstupně-výstupních operací. To se stane, vytvořením více úkolů interně, vyhněte se vytváření podřízeného procesu více souběžných úloh v rámci procesu aplikace, které volá každé rozhraní API provádění hromadné operace. Pokud volání jednou hromadnou operaci rozhraní API běžící na jeden virtuální počítač nemůže využívat propustnosti celého kontejneru (Pokud se váš kontejner propustnost > 1 milion RU/s), je vhodnější k vytvoření samostatných virtuálních počítačů současně provést hromadné volání operace rozhraní API.  

* Zajistěte, aby že initializeasync() je volána po vytvoření instance objektu BulkExecutor načíst cílová mapa oddílu kontejneru Cosmos DB.  

* V souboru App.Config vaší aplikace, zkontrolujte **gcServer** je povolená pro zajištění lepšího výkonu
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Knihovny vysílá trasování, které se můžou shromažďovat buď do souboru protokolu, nebo v konzole. Pokud chcete povolit obě, přidejte následující do App.Config vaší aplikace.

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
* Další informace o podrobnosti balíčku Nuget a knihovny .net prováděcí modul hromadného poznámky k verzi najdete v tématu[hromadně podrobnosti o sadě SDK prováděcí modul](sql-api-sdk-bulk-executor-dot-net.md). 
