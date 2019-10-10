---
title: 'Azure Cosmos DB: příklady rozhraní .NET (Microsoft. Azure. Cosmos) pro rozhraní SQL API'
description: Příklady sady C# .NET V3 SDK na GitHubu pro běžné úlohy s využitím Azure Cosmos DB SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.openlocfilehash: aeebbbab4e4a196b602a927d1d1b5d699220cf33
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248343"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Příklady pro rozhraní SQL API pro Azure Cosmos DB.NET V3 SDK (Microsoft. Azure. Cosmos)

> [!div class="op_single_selector"]
> * [Příklady sady .NET v2 SDK](sql-api-dotnet-samples.md)
> * [Příklady sady .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Příklady jazyka Java](sql-api-java-samples.md)
> * [Příklady asynchronních jazyků Java](sql-api-async-java-samples.md)
> * [Příklady pro Node. js](sql-api-nodejs-samples.md)
> * [Příklady Pythonu](sql-api-python-samples.md)
> * [Galerie ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

Úložiště GitHub [Azure-Cosmos-dotnet-V3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) obsahuje nejnovější ukázková řešení .NET, která umožňují provádět CRUD a další běžné operace na Azure Cosmos DBch prostředcích. Pokud jste obeznámeni s předchozí verzí sady .NET SDK, můžete se použít ke shromažďování podmínek a dokumentu. Vzhledem k tomu, že Azure Cosmos DB podporuje více modelů rozhraní API, verze 3,0 sady .NET SDK používá obecné výrazy "kontejner" a "položka". Kontejner může být kolekce, graf nebo tabulka. Položka může být dokument, okraj, vrchol nebo řádek a je obsahem uvnitř kontejneru. Tento článek popisuje:

* Odkazuje na úlohy v každém z ukázkových C# souborů projektu.
* Odkazuje na související referenční obsah rozhraní API.

## <a name="prerequisites"></a>Požadované součásti

Visual Studio 2019 s nainstalovaným pracovním postupem vývoje Azure

- Můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit **vývoj pro Azure** .

   [Balíček NuGet Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Předplatné Azure nebo bezplatný zkušební účet služby Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Můžete [aktivovat výhody pro předplatitele Visual studia](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): vaše předplatné sady Visual Studio vám každý měsíc dává kredity, které můžete použít pro placené služby Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Ukázky jsou samostatně obsaženy a nastavují se a vyčistí po sobě. Každý výskyt účtuje vaše předplatné na jednu hodinu využití v úrovni výkonu vašeho kontejneru.
> 

## <a name="database-examples"></a>Příklady databáze

Metoda [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) ukázkového projektu *DatabaseManagement* ukazuje, jak provádět následující úlohy. Další informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](databases-containers-items.md).

| Úloha | Referenční informace k rozhraní API |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient. CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database. ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet) |
| [Načte všechny databáze pro účet.](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet) |
| [Odstranění databáze](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database. DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) |

## <a name="container-examples"></a>Příklady kontejnerů

Metoda [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) ukázkového projektu *ContainerManagement* ukazuje, jak provádět následující úlohy. Další informace o kontejnerech Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](databases-containers-items.md).

| Úloha | Referenční informace k rozhraní API |
| --- | --- |
| [Vytvoření kontejneru](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Vytvoření kontejneru s vlastními zásadami indexů](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Změna nakonfigurovaného výkonu kontejneru](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet) |
| [Získat kontejner podle ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[Container. ReadContainerAsync](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet) |
| [Čtení všech kontejnerů v databázi](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Database. GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet) |
| [Odstranění kontejneru](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Container. DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet) |

## <a name="item-examples"></a>Příklady položek

Metoda [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) ukázkového projektu *ItemManagement* ukazuje, jak provádět následující úlohy. Informace o položkách Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](databases-containers-items.md).

| Úloha | Referenční informace k rozhraní API |
| --- | --- |
| [Vytvořit položku](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container. CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Čtení položky podle ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[vnitřního. ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Dotaz na položky](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[vnitřního. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Nahradit položku](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[vnitřního. ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet) |
| [Upsert položku](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[vnitřního. UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) |
| [Odstranění položky](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[vnitřního. DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet) |
| [Nahradit položku podmíněnou kontrolou ETag](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[RequestOptions. IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet) |

## <a name="indexing-examples"></a>Příklady indexování

Metoda [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) ukázkového projektu *IndexManagement* ukazuje, jak provádět následující úlohy. Další informace o indexování v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [zásady indexování](index-policy.md), [typy indexů](index-types.md)a [cesty indexu](index-paths.md). 

| Úloha | Referenční informace k rozhraní API |
| --- | --- |
| [Vyloučení položky z indexu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective. Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet) |
| [Použití opožděného indexování](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet) |
| [Vyloučit zadané cesty položek z indexu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet) |

## <a name="query-examples"></a>Příklady dotazů

Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) projektu ukázkové *dotazy* ukazuje, jak provádět následující úlohy pomocí gramatiky dotazů SQL, poskytovatele LINQ s dotazem a lambda. Další informace o odkazech na dotaz SQL v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [příklady dotazů SQL pro Azure Cosmos DB](how-to-sql-query.md).

| Úloha | Referenční informace k rozhraní API |
| --- | --- |
| [Dotazování položek z jednoho oddílu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[vnitřního. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Dotazování položek z více oddílů](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[vnitřního. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Dotazování pomocí příkazu SQL](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[vnitřního. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |

## <a name="change-feed-examples"></a>Změnit příklady kanálu

Metoda [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) ukázkového projektu *ChangeFeed* ukazuje, jak provádět následující úlohy. Další informace o kanálu změn v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [čtení Azure Cosmos DB Změna kanálu](read-change-feed.md) a [Změna procesoru kanálu](change-feed-processor.md).

| Úloha | Referenční informace k rozhraní API |
| --- | --- |
| [Základní funkce změny kanálu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Číst kanál změn z konkrétního času](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Číst kanál změn od začátku](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder. WithStartTime (DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet) |
| [Migrace z procesoru Change feed na změnu kanálu v sadě V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |

## <a name="server-side-programming-examples"></a>Příklady programování na straně serveru

Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) ukázkového projektu *ServerSideScripts* ukazuje, jak provádět následující úlohy. Další informace o programování na straně serveru v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [uložené procedury, triggery a uživatelsky definované funkce](stored-procedures-triggers-udfs.md).

| Úloha | Referenční informace k rozhraní API |
| --- | --- |
| [Vytvořit uloženou proceduru](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Skripty. CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet) |
| [Spustit uloženou proceduru](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Skripty. ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet) |
| [Odstraní uloženou proceduru.](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Skripty. DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet) |