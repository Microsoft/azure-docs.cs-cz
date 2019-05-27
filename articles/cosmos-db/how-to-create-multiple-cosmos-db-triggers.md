---
title: Jak vytvořit více nezávislých Azure Cosmos DB aktivačních událostí
description: Zjistěte, jak nakonfigurovat více nezávislých Azure Cosmos DB aktivačních událostí k vytvoření architektury řízené událostmi Azure Functions.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: maquaran
ms.openlocfilehash: 722da9f0112d63af52be8c9c3a746f6da9638bac
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241953"
---
# <a name="create-multiple-azure-cosmos-db-triggers"></a>Vytvoření více Azure Cosmos DB aktivačních událostí

Tento článek popisuje, jak lze nakonfigurovat více aktivačních událostí Cosmos DB fungovat souběžně a nezávisle na sobě reagovat na změny.

![Funkce bez serveru založené na události ve spolupráci s aktivační událost Azure Cosmos DB a sdílení zapůjčení kontejneru](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Požadavky na architekturu založený na událostech

Při vytváření architektur bez serveru s [Azure Functions](../azure-functions/functions-overview.md), má [doporučuje](../azure-functions/functions-best-practices.md#avoid-long-running-functions) k vytvoření sady malé funkce, které spolupracují namísto velkých dlouho běžící funkce.

Během vytváření založený na událostech bez serveru toky s využitím [aktivační událost Azure Cosmos DB](./change-feed-functions.md), je potřeba spustit do scénáře kde chcete udělat několik věcí, vždycky, když je vytvoření nové události v konkrétním [kontejneru Azure Cosmos](./databases-containers-items.md#azure-cosmos-containers). Pokud se akce, které chcete aktivovat, jsou navzájem nezávislé, by bylo ideální řešení **vytvořit jeden Trigger Cosmos DB každou akci** chcete udělat, všechny naslouchání pro změny stejného kontejneru Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optimalizace kontejnery pro více aktivačních událostí

Zadaný *požadavky* triggeru Cosmos DB, potřebujeme druhý kontejner k uložení stavu také volat *zapůjčení kontejneru*. To znamená, že potřebujete samostatné zapůjčení kontejneru pro každou funkci Azure?

Tady máte dvě možnosti:

* Vytvoření **jeden zapůjčení kontejneru pro každou funkci**: Tento přístup může znamenat další náklady, pokud používáte [sdílené propustnosti databáze](./set-throughput.md#set-throughput-on-a-database). Mějte na paměti, že minimální propustnost na úrovni kontejneru je 400 [jednotek žádostí](./request-units.md), a v případě kontejneru zapůjčení je pouze se používá k kontrolních bodů průběhu a Udržovat stav.
* Mít **jeden zapůjčit kontejner a sdílejte ji** pro všechny funkce: Tato druhá možnost je lepší využití zřízené jednotky žádostí ke kontejneru, protože umožňuje více funkcí Azure ke sdílení a použít stejné zřízenou propustnost.

Cílem tohoto článku je a provede vás k provedení druhou možnost.

## <a name="configuring-a-shared-leases-container"></a>Konfigurace sdílené zapůjčení kontejneru

Konfigurace sdílené zapůjčení kontejneru, je přidání pouze další konfigurace je třeba provést na vašich aktivační události `LeaseCollectionPrefix` [atribut](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) používáte C# nebo `leaseCollectionPrefix` [atribut](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example)Pokud používáte jazyk JavaScript. Hodnota atributu by měla být logické popisovač jaké konkrétní události.

Například, pokud máte tři aktivační události: ten, který se odešle e-mailů, jednu, která provádí agregaci vytvoření materializovaného zobrazení a ten, který odešle změny do jiného úložiště, pro pozdější analýzu, můžete přiřadit `LeaseCollectionPrefix` "e-mailů s" té předchozí, " materializovaného"na druhý a třetí příkaz" analytics".

Důležitou součástí je, že všechny tři aktivuje **můžete použít stejnou konfiguraci kontejneru zapůjčení** (název účtu, databáze a kontejneru).

Velmi jednoduché ukázky použití `LeaseCollectionPrefix` atribut C#, bude vypadat takto:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

Pro jazyk JavaScript, můžete použít konfiguraci na `function.json` souboru, se `leaseCollectionPrefix` atribut:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Vždy sledování v jednotkách žádostí zřízený v kontejnerech sdílené zapůjčení. Každá aktivační událost, která sdílí, zvýší průměrné využití propustnosti, takže možná budete muset zvýšit zřízená propustnost zvýšit počet Azure Functions, která ho používají.

## <a name="next-steps"></a>Další postup

* Zobrazit úplné konfigurace [aktivační událost Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Zkontrolujte, rozšířené [seznamu ukázek](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) pro všechny jazyky.
* Navštivte web bez serveru recepty pomocí služby Azure Cosmos DB a Azure Functions [úložiště GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) pro další ukázky.