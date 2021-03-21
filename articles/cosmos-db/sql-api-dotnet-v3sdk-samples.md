---
title: 'Azure Cosmos DB: příklady rozhraní .NET (Microsoft. Azure. Cosmos) pro rozhraní SQL API'
description: Příklady sady SDK C# .NET V3 na GitHubu pro běžné úlohy s využitím Azure Cosmos DB SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 30ccd5783b3fa9524072235a58b3c8708962ef1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433977"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Příklady pro rozhraní SQL API pro Azure Cosmos DB.NET V3 SDK (Microsoft. Azure. Cosmos)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Příklady sady .NET v2 SDK](sql-api-dotnet-samples.md)
> * [Příklady sady .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Příklady sady Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Příklady sad data pružiny V3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

Úložiště GitHub [Azure-Cosmos-dotnet-V3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) obsahuje nejnovější ukázková řešení .NET, která umožňují provádět CRUD a další běžné operace na Azure Cosmos DBch prostředcích. Pokud jste obeznámeni s předchozí verzí sady .NET SDK, můžete se použít ke shromažďování podmínek a dokumentu. Vzhledem k tomu, že Azure Cosmos DB podporuje více modelů rozhraní API, verze 3,0 sady .NET SDK používá obecné výrazy "kontejner" a "položka". Kontejner může být kolekce, graf nebo tabulka. Položka představuje obsah uvnitř kontejneru a může to být dokument, pár hrany a vrcholu nebo řádek. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v C#
* Odkazy na související referenční obsah rozhraní API.

## <a name="prerequisites"></a>Předpoklady

Visual Studio 2019 s nainstalovaným pracovním postupem vývoje Azure

- Můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

   [Balíček NuGet Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Předplatné Azure nebo bezplatný zkušební účet služby Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Můžete [aktivovat výhody pro předplatitele Visual studia](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): vaše předplatné sady Visual Studio vám každý měsíc dává kredity, které můžete použít pro placené služby Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Ukázky jsou samostatně obsaženy a nastavují se a vyčistí po sobě. Každý výskyt účtuje vaše předplatné na jednu hodinu využití v úrovni výkonu vašeho kontejneru.
> 

## <a name="database-examples"></a>Příklady pro databáze

Metoda [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) ukázkového projektu *DatabaseManagement* ukazuje, jak provádět následující úlohy. Další informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md).

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient. CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database. ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync) |
| [Načte všechny databáze pro účet.](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator) |
| [Odstranění databáze](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database. DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) |

## <a name="container-examples"></a>Příklady pro kontejnery

Metoda [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) ukázkového projektu *ContainerManagement* ukazuje, jak provádět následující úlohy. Další informace o kontejnerech Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md).

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kontejneru](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) |
| [Vytvoření kontejneru s vlastními zásadami indexů](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L161-L178) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) |
| [Změna nakonfigurovaného výkonu kontejneru](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L198-L223) |[Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync) |
| [Získat kontejner podle ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L225-L236) |[Container. ReadContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync) |
| [Čtení všech kontejnerů v databázi](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L242-L258) |[Database. GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator) |
| [Odstranění kontejneru](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L264-L270) |[Container. DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync) |

## <a name="item-examples"></a>Příklady pro položky

Metoda [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) ukázkového projektu *ItemManagement* ukazuje, jak provádět následující úlohy. Informace o položkách Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md).

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření položky](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container. CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) |
| [Čtení položky podle ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[vnitřního. ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) |
| [Dotaz na položky](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[vnitřního. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Nahrazení položky](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L415-L456) |[vnitřního. ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync) |
| [Upsert položku](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L458-L509) |[vnitřního. UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) |
| [Odstranění položky](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L511-L522) |[vnitřního. DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync) |
| [Nahradit položku podmíněnou kontrolou ETag](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L682-L772) |[RequestOptions. IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag) |

## <a name="indexing-examples"></a>Příklady indexování

Metoda [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) ukázkového projektu *IndexManagement* ukazuje, jak provádět následující úlohy. Další informace o indexování v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [zásady indexování](index-policy.md), [typy indexů](index-overview.md#index-types)a [cesty indexu](index-policy.md#include-exclude-paths). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vyloučení položky z indexu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective) |
| [Použití opožděného indexování](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode) |
| [Vyloučit zadané cesty položek z indexu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths) |

## <a name="query-examples"></a>Příklady dotazů

Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) projektu ukázkové *dotazy* ukazuje, jak provádět následující úlohy pomocí gramatiky dotazů SQL, poskytovatele LINQ s dotazem a lambda. Další informace o odkazech na dotaz SQL v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [příklady dotazů SQL pro Azure Cosmos DB](./sql-query-getting-started.md).

| Úkol | API – referenční informace |
| --- | --- |
| [Dotazování položek z jednoho oddílu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[vnitřního. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Dotazování položek z více oddílů](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[vnitřního. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Dotazování pomocí příkazu SQL](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[vnitřního. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |

## <a name="change-feed-examples"></a>Příklady kanálů změn

Metoda [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) ukázkového projektu *ChangeFeed* ukazuje, jak provádět následující úlohy. Další informace o kanálu změn v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [čtení Azure Cosmos DB Změna kanálu](read-change-feed.md) a [Změna procesoru kanálu](change-feed-processor.md).

| Úkol | API – referenční informace |
| --- | --- |
| [Základní funkce změny kanálu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |
| [Číst kanál změn z konkrétního času](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |
| [Číst kanál změn od začátku](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder. WithStartTime (DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime) |
| [Migrace z procesoru Change feed na změnu kanálu v sadě V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |

## <a name="server-side-programming-examples"></a>Příklady programování na straně serveru

Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) ukázkového projektu *ServerSideScripts* ukazuje, jak provádět následující úlohy. Další informace o programování na straně serveru v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [uložené procedury, triggery a uživatelsky definované funkce](stored-procedures-triggers-udfs.md).

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Skripty. CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync) |
| [Spuštění uložené procedury](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync) |
| [Odstraní uloženou proceduru.](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Skripty. DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync) |
