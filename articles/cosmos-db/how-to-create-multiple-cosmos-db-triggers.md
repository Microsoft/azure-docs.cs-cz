---
title: Vytvoření více nezávislých triggerů Azure Functions pro Cosmos DB
description: Naučte se konfigurovat více nezávislých Azure Functions triggerů, které Cosmos DB k vytváření architektur řízených událostmi.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 78fff48a97965f0b80456cd3e56ed1507bc784fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336669"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Vytvoření více triggerů Azure Functions pro Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje, jak nakonfigurovat několik triggerů služby Azure Functions pro službu Cosmos DB tak, aby fungovaly paralelně a nezávisle reagovaly na změny.

:::image type="content" source="./media/change-feed-functions/multi-trigger.png" alt-text="Funkce založené na událostech bez serveru, které pracují s triggerem Azure Functions pro Cosmos DB a sdílení kontejneru zapůjčení" border="false":::

## <a name="event-based-architecture-requirements"></a>Požadavky na architekturu založené na událostech

Při sestavování architektur bez serveru pomocí [Azure Functions](../azure-functions/functions-overview.md) [doporučujeme](../azure-functions/functions-best-practices.md#avoid-long-running-functions) vytvořit malé sady funkcí, které budou společně fungovat místo velkých dlouho běžících funkcí.

Při sestavování toků bez serveru založeného na událostech pomocí [Azure Functions triggeru pro Cosmos DB](./change-feed-functions.md)spustíte ve scénáři, ve kterém chcete provést několik akcí, když dojde k nové události v konkrétním [kontejneru Azure Cosmos](./account-databases-containers-items.md#azure-cosmos-containers). Pokud akce, které chcete spustit, jsou nezávisle na sobě, ideální řešení by mělo **vytvořit jednu Azure Functions triggery pro Cosmos DB na akci** , kterou chcete provést, a to vše, které naslouchá změnám ve stejném kontejneru Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optimalizace kontejnerů pro vícenásobné triggery

Vzhledem k *požadavkům* Azure Functions triggeru pro Cosmos DB potřebujeme druhý kontejner pro uložení stavu, který se také označuje jako *kontejner zapůjčení*. Znamená to, že pro každou funkci Azure potřebujete samostatný kontejner zapůjčení?

Tady máte dvě možnosti:

* Vytvoření **jednoho kontejneru zapůjčení na funkci**: Tento přístup se může překládat na další náklady, pokud nepoužíváte [sdílenou databázi propustnosti](./set-throughput.md#set-throughput-on-a-database). Pamatujte na to, že minimální propustnost na úrovni kontejneru je 400 [jednotek žádostí](./request-units.md)a v případě kontejneru zapůjčení se používá jenom k vytvoření kontrolního bodu a stavu jeho údržby.
* Mít **jeden kontejner zapůjčení a sdílet ho** pro všechny vaše funkce: Tato druhá možnost zajišťuje lepší využití jednotek zřízené žádosti na kontejneru, protože umožňuje sdílení a používání stejné zřízené propustnosti více Azure Functions.

Cílem tohoto článku je provést druhou možnost.

## <a name="configuring-a-shared-leases-container"></a>Konfigurace kontejneru sdílených zapůjčení

Chcete-li konfigurovat kontejner Shared Leases, je nutné, aby při triggerech byly přidány pouze dodatečné konfigurace, `LeaseCollectionPrefix` Pokud používáte jazyk JavaScript nebo atribut [, pokud používáte](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) `leaseCollectionPrefix` [](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) jazyk JavaScript. Hodnota atributu by měla být logickým popisovačem toho, co konkrétní Trigger spouští.

Například pokud máte tři triggery: jednu, která odesílá e-maily, jednu, která má agregaci pro vytvoření materializované zobrazení, a jeden, který odesílá změny do jiného úložiště, pro pozdější analýzu můžete přiřadit `LeaseCollectionPrefix` "e-maily" prvnímu "materializované" druhé "a" analytické "třetí straně.

Důležitou součástí je, že všechny tři triggery **můžou používat stejnou konfiguraci kontejneru zapůjčení** (účet, databáze a název kontejneru).

Velmi jednoduché ukázky kódu používající `LeaseCollectionPrefix` atribut v jazyce C# by vypadaly takto:

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

A pro JavaScript můžete použít konfiguraci `function.json` souboru s `leaseCollectionPrefix` atributem:

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
> Vždy monitorujte jednotky žádostí zřízené na vašem kontejneru vašich sdílených zapůjčení. Každá aktivační událost, která ji sdílí, zvýší průměrnou spotřebu propustnosti, takže možná bude potřeba zvýšit zajištěnou propustnost při zvýšení počtu Azure Functions, které ji používají.

## <a name="next-steps"></a>Další kroky

* Zobrazit úplnou konfiguraci [aktivační události Azure Functions pro Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Podívejte se na rozšířený [seznam ukázek](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) pro všechny jazyky.
* Další ukázky najdete v části recepty bez serveru s Azure Cosmos DB a Azure Functions [úložiště GitHubu](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) .
