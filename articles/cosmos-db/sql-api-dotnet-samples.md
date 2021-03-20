---
title: 'Azure Cosmos DB: příklady v .NET pro rozhraní SQL API'
description: Příklady C# .NET na GitHubu pro běžné úlohy s využitím Azure Cosmos DB SQL API, včetně operací CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 2e7bfd3735a57ddfa5d6100e18cf7a5676c1b9b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019036"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: příklady v .NET pro rozhraní SQL API
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

Úložiště GitHub [Azure-Cosmos-dotnet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) obsahuje nejnovější ukázková řešení .NET, která umožňují provádět CRUD a další běžné operace s Azure Cosmos DB prostředky. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v C# 
* Odkazy na související referenční obsah rozhraní API.

Ukázky kódu .NET SDK verze 3,0 (Preview) najdete v části nejnovější ukázky v úložišti GitHub [Azure-Cosmos-dotnet-V3](https://github.com/Azure/azure-cosmos-dotnet-v3) . 

## <a name="prerequisites"></a>Předpoklady

Visual Studio 2019 s nainstalovaným pracovním postupem vývoje Azure
- Můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**. 

[Balíček NuGetMicrosoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Předplatné Azure nebo bezplatný zkušební účet služby Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- Můžete [aktivovat výhody pro předplatitele Visual studia](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): vaše předplatné sady Visual Studio vám každý měsíc dává kredity, které můžete použít pro placené služby Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Ukázky jsou samostatně obsaženy a nastavují a vyčištěny po sobě s více voláními [CreateDocumentCollectionAsync ()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync). Každý výskyt účtuje vaše předplatné na jednu hodinu využití v úrovni výkonu vaší kolekce. 
> 

## <a name="database-examples"></a>Příklady pro databáze
Metoda [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L75-L91) ukázkového projektu *DatabaseManagement* ukazuje, jak provádět následující úlohy. Další informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L77) |[DocumentClient. CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L79) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [Čtení všech databází](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L83) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [Odstranění databáze](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L89) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>Příklady pro kolekce
Metoda [RunCollectionDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L86-L104) ukázkového projektu *CollectionManagement* ukazuje, jak provádět následující úlohy. Další informace o kolekcích Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L109) |[DocumentClient. CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync) 
| [Změna konfigurovaného výkonu kolekce](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L190) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [Získat kolekci podle ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L202) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [Čtení všech kolekcí v databázi](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L215) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [Odstranění kolekce](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L228) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>Příklady pro dokumenty
Metoda [RunDocumentsDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L109-L118) ukázkového projektu *DocumentManagement* ukazuje, jak provádět následující úlohy. Informace o dokumentech Azure Cosmos před spuštěním následujících ukázek najdete v tématu [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření dokumentu](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L154) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [Čtení dokumentu podle ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L168) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [Čtení všech dokumentů v kolekci](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L190) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Dotaz na dokumenty](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L18-L222) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Nahrazení dokumentu](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L240-L242) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Upsert dokumentu](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L254) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [Odstranění dokumentu](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L275-L277) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [Práce s dynamickými objekty v .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L349-L397) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Nahrazení dokumentu pomocí podmíněné kontroly ETag](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L405-L501) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [Čtení dokumentu jenom v případě, že došlo k jeho změně](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L454-L500) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>Příklady indexování
Metoda [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L93-L115) ukázkového projektu *IndexManagement* ukazuje, jak provádět následující úlohy. Další informace o indexování v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [zásady indexování](index-policy.md), [typy indexů](index-overview.md#index-types)a [cesty indexu](index-policy.md#include-exclude-paths). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vyloučení dokumentu z indexu](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L123-L162) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [Použití opožděného indexování](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L174-L192) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.documents.indexingpolicy.indexingmode) |
| [Vyloučení zadaných cest dokumentů z indexu](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L202-L263) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [Vynucení operace prohledávání rozsahu v cestě s indexem hash](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [Použití indexů rozsahu na řetězce](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L265-L316) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [Provedení transformace indexu](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L318-L370) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>Příklady geoprostorových funkcí
Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L94-L139) ukázkového *geoprostorového* projektu ukazuje, jak provádět následující úlohy.  Další informace o geografických a geoprostorovéch datech před spuštěním následujících ukázek naleznete v tématu [Use geoprostorového a geografického umístění dat o poloze JSON](./sql-query-geospatial-intro.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Povolení geoprostorového indexování v nové kolekci](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L48) |[IndexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Vložení dokumentů s body GeoJSON](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L104-L114) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Vyhledání bodů v zadané vzdálenosti](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L158-L199) |[ST_DISTANCE](sql-query-st-distance.md) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [Vyhledání bodů v rámci mnohoúhelníku](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L204-L227) |[ST_WITHIN](sql-query-st-within.md) </br> [GeometryOperationExtensions. v](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Mnohoúhelník](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [Povolení geoprostorového indexování v existující kolekci](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L385-L391) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy) |
| [Ověření dat bodů a mnohoúhelníků](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L290-L326) |[ST_ISVALID](sql-query-st-isvalid.md)<br>[ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>Příklady dotazů
Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L64-L129) projektu ukázkové *dotazy* ukazuje, jak provádět následující úlohy pomocí gramatiky dotazů SQL, poskytovatele LINQ s dotazem a lambda. Další informace o odkazech na dotaz SQL v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [příklady dotazů SQL pro Azure Cosmos DB](./sql-query-getting-started.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Dotazování na všechny dokumenty](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L131-L147) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz na rovnost s využitím ==](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L186-L198) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz na nerovnost s využitím != a NOT](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L288-L332) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s využitím operátorů rozsahu jako >, <, >=, <=](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L334-L355) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s využitím operátorů rozsahu na řetězce](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L355-L370) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s ŘAZENÍm podle](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L422-L446) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s agregačními funkcemi](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Práce s vnořenými dokumenty](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L498-L524) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s využitím operace Join uvnitř dokumentů](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L526-L540) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz pomocí operátorů String, Math a Array](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L636-L673) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz pomocí parametrizovaného SQL s využitím SqlQuerySpec](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L149-L184) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [Dotaz s využitím explicitního stránkování](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L675-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Paralelní dotazování dělených kolekcí](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L736-L807) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s ŘAZENÍm podle oddílů pro dělené kolekce](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L809-L882) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>Příklady kanálů změn 
Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L54-L97) ukázkového projektu *ChangeFeed* ukazuje, jak provádět následující úlohy. Další informace o kanálu změn v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [čtení Azure Cosmos DB Změna kanálu](read-change-feed.md) a [Změna procesoru kanálu](change-feed-processor.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Čtení kanálu změn](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [Čtení rozsahů klíčů oddílů](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

Ukázka [ChangeFeedMigrationTool](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/ChangeFeedMigrationTool)kanálu změn, která ukazuje, jak pomocí knihovny Change feed Processor replikovat data do jiného kontejneru Cosmos.   

## <a name="server-side-programming-examples"></a>Příklady programování na straně serveru
Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ServerSideScripts/Program.cs#L58-L91) ukázkového projektu *ServerSideScripts* ukazuje, jak provádět následující úlohy. Další informace o programování na straně serveru v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [uložené procedury, triggery a uživatelsky definované funkce](stored-procedures-triggers-udfs.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L110) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Spuštění uložené procedury](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L125) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [Čtení kanálu dokumentů pro uloženou proceduru](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L198) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Vytvořit uloženou proceduru s ŘAZENÍm podle](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L223) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Vytvoření triggeru před akcí](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L273) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Vytvoření triggeru po akci](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L341) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Vytvořit uživatelsky definovanou funkci (UDF)](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L421) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>Příklady správy uživatelů
Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/UserManagement/Program.cs#L55-L129) ukázkového projektu *UserManagement* ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření uživatele](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L93) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [Nastavení oprávnění pro kolekci nebo dokument](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L97) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [Získání seznamu uživatelských oprávnění](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L241) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |
