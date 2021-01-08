---
title: Příklady Node.js správy dat v databázi Azure Cosmos
description: Na GitHubu najdete příklady v Node.js pro běžné úlohy ve službě Azure Cosmos DB, včetně operací CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 192af33c6f07d38daef3a183fa8d746ff082ce2b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018968"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Příklady správy dat ve službě Azure Cosmos DB pomocí Node.js
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

Ukázková řešení, která provádějí operace CRUD a další běžné operace týkající se Azure Cosmos DB prostředků, jsou součástí úložiště GitHub [Azure-Cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) . Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v Node.js.
* Odkazy na související referenční obsah rozhraní API.

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete použít k placení za služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potřebujete také sadu [JavaScript SDK](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky provedou několik volání metody [Containers.create](/javascript/api/%40azure/cosmos/containers?preserve-view=true&view=azure-node-latest). Pokaždé, když k tomu dojde, se bude pro vaše předplatné účtovat 1 hodina využití pro úroveň výkonu vytvářeného kontejneru.
   > 
   > 

## <a name="database-examples"></a>Příklady pro databáze

Soubor [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) ukazuje, jak provést operace CRUD v databázi. Další informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření databáze, pokud ještě neexistuje](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest&preserve-view=true#createifnotexists-databaserequest--requestoptions-) |
| [Výpis databází pro účet](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Odstranění databáze](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest&preserve-view=true#delete-requestoptions-) |

## <a name="container-examples"></a>Příklady pro kontejnery

Soubor [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) ukazuje, jak provést operace CRUD na kontejneru. Informace o kolekcích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření kontejneru, pokud ještě neexistuje](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest&preserve-view=true#createifnotexists-containerrequest--requestoptions-) |
| [Výpis kontejnerů pro účet](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Čtení definice kontejneru](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Odstranění kontejneru](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest&preserve-view=true#delete-requestoptions-) |

## <a name="item-examples"></a>Příklady pro položky

Soubor [ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) ukazuje, jak provést operace CRUD pro položku. Další informace o dokumentech Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření položek](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest&preserve-view=true#create-t--requestoptions-) |
| [Čtení všech položek v kontejneru](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Čtení položky podle ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Čtení položky pouze v případě, že se změnila](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#accesscondition) |
| [Dotaz na dokumenty](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items?preserve-view=true&view=azure-node-latest) |
| [Nahrazení položky](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest) |
| [Nahrazení položky pomocí podmíněné kontroly ETag](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#accesscondition) |
| [Odstranění položky](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest) |

## <a name="indexing-examples"></a>Příklady indexování

V souboru [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) se dozvíte, jak spravovat indexování. Další informace o indexování v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku o [zásadách indexování](index-policy.md), [typech indexování](index-overview.md#index-types)a [indexovaných cestách](index-policy.md#include-exclude-paths) . 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Ruční indexace konkrétní položky](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#indexingdirective) |
| [Ručně vyloučit konkrétní položku z indexu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#indexingdirective) |
| [Vyloučení cesty z indexu](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy?preserve-view=true&view=azure-node-latest#excludedpaths) |
| [Vytvoření indexu rozsahu v cestě řetězce](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind?preserve-view=true&view=azure-node-latest), [IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy?preserve-view=true&view=azure-node-latest), [Items.query](/javascript/api/%40azure/cosmos/items?preserve-view=true&view=azure-node-latest) |
| [Vytvoření kontejneru s výchozí zásadou indexPolicy a její následná online aktualizace](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers?preserve-view=true&view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Příklady programování na straně serveru

Soubor [index. TS](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) projektu [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) ukazuje, jak provádět následující úlohy. Další informace o programování na straně serveru v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku o [uložených procedurách, triggerech a uživatelsky definovaných funkcích](stored-procedures-triggers-udfs.md) koncepční článek. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures?preserve-view=true&view=azure-node-latest) |
| [Spuštění uložené procedury](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure?preserve-view=true&view=azure-node-latest) |

Další informace o programování na straně serveru najdete v tématu o [programování na straně serveru pro službu Azure Cosmos DB: uložené procedury, aktivační události databáze a UDF](stored-procedures-triggers-udfs.md).
