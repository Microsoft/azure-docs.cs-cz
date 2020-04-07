---
title: Migrace z knihovny hromadného prováděcího modulu do hromadné podpory v azure cosmos DB .NET V3 SDK
description: Zjistěte, jak migrovat aplikaci z použití knihovny hromadného prováděcího modulu na hromadnou podporu v azure cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: maquaran
ms.openlocfilehash: 820a5398d84122659b1676b7d5722bce08b1837d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755977"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrace z knihovny hromadného prováděcího modulu do hromadné podpory v azure cosmos DB .NET V3 SDK

Tento článek popisuje požadované kroky k migraci kódu existující aplikace, který používá [knihovnu hromadného vykonavatele .NET](bulk-executor-dot-net.md) na funkci [hromadné podpory](tutorial-sql-api-dotnet-bulk-import.md) v nejnovější verzi sady .NET SDK.

## <a name="enable-bulk-support"></a>Povolit hromadnou podporu

Povolit hromadnou `CosmosClient` podporu instance prostřednictvím konfigurace [AllowBulkExecution:](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Vytvořit úkoly pro každou operaci

Hromadná podpora v sdk .NET funguje využitím [paralelní knihovny úloh](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) a seskupování operací, ke kterým dochází souběžně. 

Neexistuje žádná jediná metoda, která bude mít seznam dokumentů nebo operací jako vstupní parametr, ale spíše je třeba vytvořit task pro každou operaci, kterou chcete provést hromadně.

Pokud je například počátečním vstupem seznam položek, u kterých má každá položka následující schéma:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Pokud chcete provést hromadný import (podobně jako pomocí BulkExecutor.BulkImportAsync), musíte `CreateItemAsync` mít souběžná volání s každou hodnotou položky. Například:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Pokud chcete provést hromadnou *aktualizaci* (podobně jako pomocí [BulkExecutor.BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync) `ReplaceItemAsync` ), musíte mít souběžná volání metody po aktualizaci hodnoty položky. Například:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

A pokud chcete provést hromadné *odstranění* (podobně jako pomocí [BulkExecutor.BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), `DeleteItemAsync`musíte `id` mít souběžná volání , s a klíč oddílu každé položky. Například:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Zachycení stavu výsledku úkolu

V předchozích příkladech kódu jste vytvořili souběžný seznam `CaptureOperationResponse` úkolů a zavolali metodu u každého z těchto úkolů. Tato metoda je rozšíření, které nám umožňuje udržovat *podobné schéma odezvy* jako BulkExecutor, zachycením všech chyb a [sledováním využití jednotek požadavků](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Je-li `OperationResponse` deklarováno jako:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Souběžné provádění operací

Po definování seznamu úkolů počkejte, dokud nebudou všechny dokončeny. Dokončení úkolů můžete sledovat definováním rozsahu hromadné operace, jak je znázorněno v následujícím fragmentu kódu:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Statistiky sběru

Předchozí kód čeká, dokud nejsou dokončeny všechny operace a vypočítá požadované statistiky. Tyto statistiky jsou podobné jako u hromadné vykonavatele knihovny [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

Obsahuje: `BulkOperationResponse`

1. Celková doba zpracování seznamu operací prostřednictvím hromadné podpory.
1. Počet úspěšných operací.
1. Součet spotřebovaných jednotek požadavku.
1. Pokud jsou chyby, zobrazí seznam řazených kolekcí členů, které obsahují výjimku a související položku pro účely protokolování a identifikace.

## <a name="retry-configuration"></a>Opakovat konfiguraci

Hromadná knihovna vykonavatele `MaxRetryAttemptsOnThrottledRequests` měla [pokyny,](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) které jsou uvedeny pro nastavení `MaxRetryWaitTimeInSeconds` a [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) delegovat `0` řízení do knihovny.

Pro hromadnou podporu v sdk .NET neexistuje žádné skryté chování. Možnosti opakování můžete nakonfigurovat přímo prostřednictvím [cosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) a [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> V případech, kdy je zřízená jednotka požadavku mnohem nižší, než se očekávalo na základě množství dat, můžete zvážit nastavení těchto na vysoké hodnoty. Hromadná operace bude trvat déle, ale má vyšší šanci na úplné úspěchu z důvodu vyšší opakování.

## <a name="performance-improvements"></a>Zlepšení výkonu

Stejně jako u jiných operací s .NET SDK, pomocí rozhraní API datového proudu má za následek lepší výkon a zabraňuje zbytečné serializace. 

Použití datových proudových api je možné pouze v případě, že povaha dat, která používáte, odpovídá povaze datového proudu bajtů (například datových proudů souborů). V takových případech `CreateItemStreamAsync`použití `ReplaceItemStreamAsync`, `DeleteItemStreamAsync` nebo metody `ResponseMessage` a `ItemResponse`práce s (místo ) zvyšuje propustnost, které lze dosáhnout.

## <a name="next-steps"></a>Další kroky

* Další informace o verzích sady .NET SDK najdete v článku [Azure Cosmos DB SDK.](sql-api-sdk-dotnet.md)
* Získejte kompletní [zdrojový kód migrace](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) z GitHubu.
* [Další hromadné ukázky na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
