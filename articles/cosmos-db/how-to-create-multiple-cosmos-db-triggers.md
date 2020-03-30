---
title: Vytvoření několika nezávislých aktivačních událostí Azure pro Cosmos DB
description: Zjistěte, jak nakonfigurovat několik nezávislých aktivačních událostí Azure pro Cosmos DB a vytvářet architektury řízené událostmi.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 32b680acdee29bf97a0e132fee93d5fee3377245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604938"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Vytvoření více aktivačních událostí Azure Functions pro Cosmos DB

Tento článek popisuje, jak nakonfigurovat několik triggerů služby Azure Functions pro službu Cosmos DB tak, aby fungovaly paralelně a nezávisle reagovaly na změny.

![Funkce založené na událostech bez serveru, které pracují s aktivační událostí Azure Functions pro Cosmos DB a sdílejí kontejner pronajímá](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Požadavky na architekturu založenou na událostech

Při vytváření architektur bez serveru pomocí [Azure Functions](../azure-functions/functions-overview.md)se [doporučuje](../azure-functions/functions-best-practices.md#avoid-long-running-functions) vytvořit malé sady funkcí, které pracují společně namísto velkých dlouhotrvajících funkcí.

Při vytváření toků bez serveru založených na událostech pomocí [aktivační události Azure Functions pro Cosmos DB](./change-feed-functions.md)narazíte na scénář, kde chcete provést více věcí, kdykoli se v konkrétním [kontejneru Azure Cosmos](./databases-containers-items.md#azure-cosmos-containers)zobrazí nová událost . Pokud akce, které chcete aktivovat, jsou na sobě nezávislé, ideálním řešením by bylo **vytvořit jednu aktivační událost Azure Functions pro Cosmos DB na akci,** kterou chcete provést, všechny naslouchání změnám ve stejném kontejneru Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optimalizace kontejnerů pro více aktivačních událostí

Vzhledem k *požadavkům* aktivační události Azure Functions pro Cosmos DB, potřebujeme druhý kontejner pro uložení stavu, také *volal, zapůjčení kontejneru*. Znamená to, že potřebujete samostatný kontejner zapůjčení pro každou funkci Azure?

Zde máte dvě možnosti:

* Vytvořit **jeden kontejner zapůjčení na funkci**: Tento přístup se může promítnout do dodatečných nákladů, pokud nepoužíváte [databázi sdílené propustností](./set-throughput.md#set-throughput-on-a-database). Nezapomeňte, že minimální propustnost na úrovni kontejneru je 400 [jednotek požadavků](./request-units.md)a v případě kontejneru zapůjčení se používá pouze ke kontrole průběhu a udržování stavu.
* Mít **jeden kontejner zapůjčení a sdílet jej** pro všechny vaše funkce: Tato druhá možnost umožňuje lépe využívat zřízené jednotky požadavků v kontejneru, protože umožňuje více funkcí Azure sdílet a používat stejnou zřízenou propustnost.

Cílem tohoto článku je vás provést k dosažení druhé možnosti.

## <a name="configuring-a-shared-leases-container"></a>Konfigurace kontejneru sdílených zapůjčení

Chcete-li nakonfigurovat kontejner sdílených zapůjčení, jedinou další konfigurací, `LeaseCollectionPrefix` kterou je třeba provést `leaseCollectionPrefix` na aktivačních událostech, je přidání [atributu,](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) pokud používáte c# nebo [atribut,](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) pokud používáte JavaScript. Hodnota atributu by měla být logickým popisovačem toho, co tento konkrétní aktivační událost.

Například pokud máte tři aktivační události: jeden, který odesílá e-maily, jeden, který provádí agregaci k vytvoření zhmotněného `LeaseCollectionPrefix` zobrazení a jeden, který odešle změny do jiného úložiště, pro pozdější analýzu, můžete přiřadit "e-maily" na první, "zhmotněný" na druhý a "analytics" na třetí.

Důležitou součástí je, že všechny tři aktivační události **můžete použít stejnou konfiguraci kontejneru zapůjčení** (účet, databáze a název kontejneru).

Velmi jednoduché ukázky `LeaseCollectionPrefix` kódu pomocí atributu v C#, bude vypadat takto:

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

A pro JavaScript, můžete použít `function.json` konfiguraci na `leaseCollectionPrefix` soubor, s atributem:

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
> Vždy sledovat na jednotky požadavku zřízené na kontejneru sdílené zapůjčení. Každá aktivační událost, která ji sdílí, zvýší průměrnou spotřebu propustnosti, takže možná budete muset zvýšit zřízenou propustnost, když zvýšíte počet funkcí Azure, které ji používají.

## <a name="next-steps"></a>Další kroky

* Podívejte se na úplnou konfiguraci [aktivační události Azure Functions pro Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Podívejte se na rozšířený [seznam ukázek](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) pro všechny jazyky.
* Další ukázky najdete v receptech bez serveru pomocí azure cosmos db a [úložiště GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) Azure Functions.