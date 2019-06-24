---
title: 'Azure Cosmos DB: příklady v .NET pro rozhraní SQL API'
description: Najít C# příklady .NET na Githubu pro běžné úlohy pomocí rozhraní Azure Cosmos DB SQL API, včetně operace CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 04/04/2019
ms.author: sngun
ms.openlocfilehash: df673e4f76dcc868811a2874ff36e0ea5c1c64fc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342097"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: příklady v .NET pro rozhraní SQL API
> [!div class="op_single_selector"]
> * [Příklady v .NET](sql-api-dotnet-samples.md)
> * [Příklady v Javě](sql-api-java-samples.md)
> * [Příklady v asynchronní Javě](sql-api-async-java-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[Azure-cosmos-dotnet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) úložiště GitHub zahrnuje nejnovější ukázkové řešení .NET provádět CRUD a dalších běžných operací s prostředky Azure Cosmos DB. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v C# 
* Odkazy na související referenční obsah rozhraní API

Ukázky kódu .NET SDK verze 3.0 (Preview) najdete v tématu nejnovější ukázky [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3) úložiště GitHub. 

## <a name="prerequisites"></a>Požadavky

Visual Studio 2019 s pracovním postupem vývoj pro Azure, nainstalované
- Můžete stáhnout a použít **bezplatné** [Visual Studio. 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**. 

[Balíček Microsoft.Azure.DocumentDB NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Předplatného Azure nebo Bezplatný zkušební účet Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- Je možné [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Vaše předplatné sady Visual Studio vám dává kredity každý měsíc, který můžete použít k placení za služby Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Ukázky jsou samostatný a nastavení a jejich čištění po sami s více volání [CreateDocumentCollectionAsync()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync). Každý výskyt účtuje předplatné za jednu hodinu využití v úroveň výkonu kolekce. 
> 

## <a name="database-examples"></a>Příklady pro databáze
[RunDatabaseDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L72-L121) metoda vzorku *DatabaseManagement* projektu ukazuje, jak provádět následující úlohy. Další informace o databáze Azure Cosmos předtím, než spustíte následující ukázky, viz [pracovat s databází, kontejnerů a položky](databases-containers-items.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L90) |[DocumentClient.CreateDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseasync) |
| [Dotazování databáze](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L81) |[DocumentQueryable.CreateDatabaseQuery](/previous-versions/azure/dn850286(v=azure.100)) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L102) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [Čtení všech databází](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L108-L113) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [Odstranění databáze](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L118) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>Příklady pro kolekce
[RunCollectionDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/CollectionManagement/Program.cs#L96-L185) metoda vzorku *CollectionManagement* projektu ukazuje, jak provádět následující úlohy. Další informace o kolekcích Azure Cosmos předtím, než spustíte následující ukázky, naleznete v tématu [pracovat s databází, kontejnerů a položky](databases-containers-items.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L101) |[DocumentClient.CreateDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync) |
| [Získání konfigurovaného výkonu kolekce](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L198) |[DocumentQueryable.CreateOfferQuery](/previous-versions/azure/dn975114(v=azure.100)) |
| [Změna konfigurovaného výkonu kolekce](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L207) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [Získání kolekce podle ID](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L153) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [Čtení všech kolekcí v databázi](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L162) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [Odstranění kolekce](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L175) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>Příklady pro dokumenty
[RunDocumentsDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L97-L102) metoda vzorku *DocumentManagement* projektu ukazuje, jak provádět následující úlohy. Další informace o dokumenty Azure Cosmos předtím, než spustíte následující ukázky, viz [pracovat s databází, kontejnerů a položky](databases-containers-items.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření dokumentu](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L198) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [Čtení dokumentu podle ID](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L211) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [Čtení všech dokumentů v kolekci](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L227) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Dotazování dokumentů](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L248-L251) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Nahrazení dokumentu](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L263) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Upsert dokumentu](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L300) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [Odstranění dokumentu](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L322) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [Práce s dynamickými objekty v .NET](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L331-L380) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Nahrazení dokumentu pomocí podmíněné kontroly ETag](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L398-L440) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [Čtení dokumentu jenom v případě, že došlo k jeho změně](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L442-L470) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>Příklady indexování
[RunIndexDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/ea8c977b9c2f37ddc2894911ec239907ab60e40a/samples/code-samples/IndexManagement/Program.cs#L89-L117) metoda vzorku *IndexManagement* projektu ukazuje, jak provádět následující úlohy. Další informace o indexování ve službě Azure Cosmos DB, před spuštěním následujících ukázkách najdete v tématu [indexu zásady](index-policy.md), [indexovat typy](index-types.md), a [indexu cesty](index-paths.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vyloučení dokumentu z indexu](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L125-L163) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [Použití ručního indexování (místo automatického)](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L171-L209) |[IndexingPolicy.Automatic](/dotnet/api/microsoft.azure.documents.indexingpolicy.automatic) |
| [Vyloučení zadaných cest dokumentů z indexu](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L248-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [Vynucení operace prohledávání rozsahu v cestě s indexem hash](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [Použití indexů rozsahu na řetězce](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L342-L405) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [Provedení transformace indexu](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L407-L464) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>Příklady geoprostorových funkcí
Ukázkový soubor pro geoprostorové funkce [azure-documentdb-dotnet/samples/code-samples/Geospatial/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) ukazuje, jak provádět následující úlohy.  Další informace o GeoJSON a Geoprostorová data předtím, než spustíte následující ukázky, viz [používat geoprostorové a údaje o poloze GeoJSON](geospatial.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Povolení geoprostorového indexování v nové kolekci](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L45-L63) |[IndexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Vložení dokumentů s body GeoJSON](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L116-L126) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Vyhledání bodů v zadané vzdálenosti](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L152-L194) |[ST_DISTANCE](sql-query-system-functions.md#spatial-functions) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [Vyhledání bodů v rámci mnohoúhelníku](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L196-L221) |[ST_WITHIN](sql-query-system-functions.md#spatial-functions) </br> [GeometryOperationExtensions.Within](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Mnohoúhelník](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [Povolení geoprostorového indexování v existující kolekci](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L312-L336) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy) |
| [Ověření dat bodů a mnohoúhelníků](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L223-L265) |[ST_ISVALID](sql-query-system-functions.md#spatial-functions)<br>[ST_ISVALIDDETAILED](sql-query-system-functions.md#spatial-functions)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>Příklady dotazů
Soubor dokumentu dotazu [azure-documentdb-dotnet/samples/code-samples/Queries/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs), ukazuje, jak provést každou z těchto úloh gramatiky dotazů SQL, zprostředkovatele LINQ pomocí dotazu i výrazu Lambda. Další informace o odkaz na dotaz SQL ve službě Azure Cosmos DB předtím, než spustíte následující ukázky, viz [Příklady dotazů SQL pro službu Azure Cosmos DB](how-to-sql-query.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Dotazování na všechny dokumenty](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L122-L138) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz na rovnost s využitím ==](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L251-L268) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz na nerovnost s využitím != a NOT](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L270-L276) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s využitím operátorů rozsahu jako >, <, >=, <=](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L305-L325) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s využitím operátorů rozsahu na řetězce](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L337-L346) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotazu pomocí klauzule ORDER BY](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L370-L392) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotazy s agregačními funkcemi](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Práce s vnořenými dokumenty](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L394-L419) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz s využitím operace Join uvnitř dokumentů](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L421-L435) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotazování pomocí řetězce, matematický zápis a pole operátory](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L527-L552) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotaz pomocí parametrizovaného SQL s využitím SqlQuerySpec](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L140-L174) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [Dotaz s využitím explicitního stránkování](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L554-L576) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Paralelní dotazování dělených kolekcí](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L664-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Dotazy s klauzulí ORDER BY používejte u dělených kolekcí](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L737-L810) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>Příklady kanálů změn 
Změna kanálu ukázce [azure-documentdb-dotnet/samples/code-samples/ChangeFeed/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ServerSideScripts/Program.cs), ukazuje, jak provádět následující úlohy. Další informace o kanálu ve službě Azure Cosmos DB, předtím, než spustíte následující ukázky změn najdete v tématu [služby Azure Cosmos DB pro čtení kanálu změn](read-change-feed.md) a [Change feed procesoru](change-feed-processor.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Čtení kanálu změn](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [Čtení rozsahů klíčů oddílů](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

Změna kanálu procesoru ukázka [ChangeFeedMigrationTool](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedMigrationTool), ukazuje, jak použít knihovnou change feed processor k replikaci dat do jiné kolekce Cosmos DB.   

## <a name="server-side-programming-examples"></a>Příklady programování na straně serveru
Ukázkový soubor pro programování na straně serveru [azure-documentdb-dotnet/samples/code-samples/ServerSideScripts/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ServerSideScripts/Program.cs) ukazuje, jak provádět následující úlohy. Další informace o programování na straně serveru ve službě Azure Cosmos DB předtím, než spustíte následující ukázky, viz [uložené procedury, triggery a uživatelem definovaných funkcí](stored-procedures-triggers-udfs.md). 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L112) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Spuštění uložené procedury](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L127) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [Čtení kanálu dokumentů pro uloženou proceduru](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L194) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Vytvořit uloženou proceduru s klauzulí ORDER BY](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L219) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Vytvoření triggeru před akcí](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L264) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Vytvoření triggeru po akci](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L329) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Vytvoření uživatelem definované funkce (UDF)](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L389) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>Příklady správy uživatelů
Soubor sady management uživatele [azure-documentdb-dotnet/samples/code-samples/UserManagement/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/UserManagement/Program.cs), ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření uživatele](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L81) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [Nastavení oprávnění pro kolekci nebo dokument](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L85) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [Získání seznamu uživatelských oprávnění](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L218) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |

