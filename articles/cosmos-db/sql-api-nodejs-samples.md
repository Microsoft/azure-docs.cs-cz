---
title: Node.js examples to manage data in Azure Cosmos database
description: Na GitHubu najdete příklady v Node.js pro běžné úlohy ve službě Azure Cosmos DB, včetně operací CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.openlocfilehash: d79ac0e368ad824f6ceaea7bbcba151ecec72336
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220156"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Node.js examples to manage data in Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V2 SDK Examples](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Examples](sql-api-dotnet-v3sdk-samples.md)
> * [Příklady v Javě](sql-api-java-samples.md)
> * [Příklady v asynchronní Javě](sql-api-async-java-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie vzorového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Ukázková řešení, která provádí operace CRUD a další běžné operace s prostředky služby Azure Cosmos DB, jsou součástí úložiště [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) na GitHubu. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v Node.js.
* Odkazy na související referenční obsah rozhraní API

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete využít pro placené služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potřebujete také sadu [JavaScript SDK](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky provedou několik volání metody [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest). Pokaždé, když k tomu dojde, se bude pro vaše předplatné účtovat 1 hodina využití pro úroveň výkonu vytvářeného kontejneru.
   > 
   > 

## <a name="database-examples"></a>Příklady pro databáze

The [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) file shows how to perform the CRUD operations on the database. To learn about the Azure Cosmos databases before running the following samples, see [Working with databases, containers, and items](databases-containers-items.md) conceptual article. 

| Úkol | Referenční materiály k rozhraním API |
| --- | --- |
| [Vytvoření databáze, pokud ještě neexistuje](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#createifnotexists-databaserequest--requestoptions-) |
| [Výpis databází pro účet](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#readall-feedoptions-) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest#read-requestoptions-) |
| [Odstranění databáze](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest#delete-requestoptions-) |

## <a name="container-examples"></a>Příklady pro kontejnery

The [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) file shows how to perform the CRUD operations on the container. To learn about the Azure Cosmos collections before running the following samples, see [Working with databases, containers, and items](databases-containers-items.md) conceptual article. 

| Úkol | Referenční materiály k rozhraním API |
| --- | --- |
| [Vytvoření kontejneru, pokud ještě neexistuje](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#createifnotexists-containerrequest--requestoptions-) |
| [Výpis kontejnerů pro účet](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#readall-feedoptions-) |
| [Read a container definition](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest#read-requestoptions-) |
| [Odstranění kontejneru](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest#delete-requestoptions-) |

## <a name="item-examples"></a>Příklady pro položky

The [ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) file shows how to perform the CRUD operations on the item. To learn about the Azure Cosmos documents before running the following samples, see [Working with databases, containers, and items](databases-containers-items.md) conceptual article. 

| Úkol | Referenční materiály k rozhraním API |
| --- | --- |
| [Vytvoření položek](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest#create-t--requestoptions-) |
| [Čtení všech položek v kontejneru](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest#readall-feedoptions-) |
| [Čtení položky podle ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-) |
| [Čtení položky pouze v případě, že se změnila](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Dotaz na dokumenty](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Nahrazení položky](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Nahrazení položky pomocí podmíněné kontroly ETag](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Odstranění položky](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Příklady pro indexování

The [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) file shows how to manage indexing. To learn about indexing in Azure Cosmos DB before running the following samples, see [indexing policies](index-policy.md), [indexing types](index-types.md), and [indexing paths](index-paths.md) conceptual articles. 

| Úkol | Referenční materiály k rozhraním API |
| --- | --- |
| [Manually index a specific item](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Manually exclude a specific item from the index](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Vyloučení cesty z indexu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Vytvoření indexu rozsahu v cestě řetězce](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Vytvoření kontejneru s výchozí zásadou indexPolicy a její následná online aktualizace](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Příklady programování na straně serveru

Soubor [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) projektu [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) ukazuje, jak provádět následující úlohy. To learn about Server-side programming  in Azure Cosmos DB before running the following samples, see [Stored procedures, triggers, and user-defined functions](stored-procedures-triggers-udfs.md) conceptual article. 

| Úkol | Referenční materiály k rozhraním API |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Spuštění uložené procedury](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

Další informace o programování na straně serveru najdete v tématu o [programování na straně serveru pro Azure Cosmos DB: uložené procedury, aktivační události databáze a UDF](stored-procedures-triggers-udfs.md).

