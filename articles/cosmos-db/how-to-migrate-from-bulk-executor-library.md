---
title: Migrace z knihovny hromadného prováděcího modulu do hromadné podpory v sadě Azure Cosmos DB .NET V3 SDK
description: Naučte se migrovat aplikaci z použití knihovny hromadných prováděcích modulů na hromadnou podporu v sadě Azure Cosmos DB SDK v3.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: f7f51f6944de48e58ff53e7685164df3a04afe56
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075581"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrace z knihovny hromadného prováděcího modulu do hromadné podpory v sadě Azure Cosmos DB .NET V3 SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje nezbytné kroky pro migraci kódu existující aplikace, který používá [knihovnu hromadného prováděcího modulu .NET](bulk-executor-dot-net.md) , do funkce [Hromadná podpora](tutorial-sql-api-dotnet-bulk-import.md) v nejnovější verzi sady .NET SDK.

## <a name="enable-bulk-support"></a>Povolit hromadnou podporu

Povolit hromadnou podporu `CosmosClient` instance prostřednictvím konfigurace [AllowBulkExecution](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Vytvořit úkoly pro každou operaci

Hromadná podpora v sadě .NET SDK funguje pomocí operací [Task Parallel Library](/dotnet/standard/parallel-programming/task-parallel-library-tpl) a seskupování, ke kterým dochází současně. 

V sadě SDK neexistuje žádná jediná metoda, která by převzala váš seznam dokumentů nebo operací jako vstupní parametr, ale místo toho je potřeba vytvořit úlohu pro každou operaci, kterou chcete provést hromadně, a pak jednoduše počkat na dokončení.

Například pokud je vaším počátečním vstupem seznam položek, kde má každá položka následující schéma:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Pokud chcete hromadné importy (podobně jako v používání BulkExecutor. BulkImportAsync), musíte mít souběžné volání `CreateItemAsync` . Například:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Chcete-li provést hromadnou *aktualizaci* (podobně jako při použití [BulkExecutor. BulkUpdateAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), je nutné mít souběžné volání `ReplaceItemAsync` metody po aktualizaci hodnoty položky. Například:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

A pokud chcete hromadné *odstranění* (podobně jako při použití [BulkExecutor. BulkDeleteAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), musíte mít souběžné volání `DeleteItemAsync` s `id` klíčem oddílu a každé položky. Například:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Zaznamenat stav výsledku úlohy

V předchozích příkladech kódu jsme vytvořili souběžný seznam úkolů a `CaptureOperationResponse` pro každou z těchto úloh jste volali metodu. Tato metoda je rozšíření, které nám umožňuje udržovat *podobné schéma odpovědi* jako BulkExecutor a zachytit případné chyby a sledovat [využití jednotek žádostí](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Kde `OperationResponse` je deklarován jako:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Souběžné provádění operací

Pro sledování rozsahu celého seznamu úkolů používáme tuto pomocnou třídu:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

`ExecuteAsync`Metoda počká, dokud nebudou dokončeny všechny operace, a můžete ji použít například takto:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Zachytit statistiku

Předchozí kód počká, dokud nebudou dokončeny všechny operace, a vypočítává požadované statistiky. Tyto statistiky jsou podobné jako [BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)knihovny hromadného prováděcího modulu.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`Obsahuje:

1. Celková doba, jakou trvalo zpracování seznamu operací prostřednictvím hromadné podpory.
1. Počet úspěšných operací.
1. Celkový počet spotřebovaných jednotek žádosti.
1. Pokud dojde k chybám, zobrazí se seznam řazených kolekcí členů, které obsahují výjimku, a přidruženou položku pro účely protokolování a identifikace.

## <a name="retry-configuration"></a>Opakovat konfiguraci

V knihovně hromadných prováděcích knihoven byly [doprovodné](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) materiály, které jsou zmíněny k nastavení `MaxRetryWaitTimeInSeconds` a `MaxRetryAttemptsOnThrottledRequests` [RetryOptions](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) k `0` delegování řízení do knihovny.

Pro hromadnou podporu v sadě .NET SDK neexistuje žádné skryté chování. Možnosti opakování můžete nakonfigurovat přímo přes [CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) a [CosmosClientOptions. MaxRetryWaitTimeOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> V případech, kdy jsou zřízené jednotky žádosti mnohem nižší než očekávané na základě množství dat, můžete zvážit jejich nastavení na vysoké hodnoty. Hromadná operace bude trvat déle, ale bude mít větší šanci na naprostou úspěch z důvodu vyššího počtu opakovaných pokusů.

## <a name="performance-improvements"></a>Vylepšení výkonu

Stejně jako u jiných operací se sadou .NET SDK používá rozhraní API Stream lepší výkon a zabrání zbytečné serializaci. 

Použití rozhraní API služby Stream je možné pouze v případě, že povaha dat, která používáte, odpovídá datovému proudu bajtů (například datovým proudům souborů). V takových případech použití `CreateItemStreamAsync` `ReplaceItemStreamAsync` metod, nebo `DeleteItemStreamAsync` a práce s `ResponseMessage` (namísto `ItemResponse` ) zvyšuje propustnost, kterou je možné dosáhnout.

## <a name="next-steps"></a>Další kroky

* Další informace o vydání sady .NET SDK najdete v článku [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) .
* Získejte kompletní [zdrojový kód migrace](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) z GitHubu.
* [Další hromadné ukázky na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)