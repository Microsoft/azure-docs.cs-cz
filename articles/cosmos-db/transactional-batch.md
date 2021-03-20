---
title: Transakční dávkové operace v Azure Cosmos DB s využitím sady .NET SDK
description: Naučte se používat TransactionalBatch v sadě Azure Cosmos DB .NET SDK k provedení skupiny operací s body, které jsou buď úspěšné nebo neúspěšné.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97347553"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Transakční dávkové operace v Azure Cosmos DB s využitím sady .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Transakční dávka popisuje skupinu operací s body, které musí být buď úspěšné, nebo neúspěšné společně se stejným klíčem oddílu v kontejneru. V sadě .NET SDK `TransactionalBatch` je třída použita k definování této dávky operací. Pokud jsou všechny operace úspěšné v pořadí, v jakém jsou popsány v rámci operace transakční dávky, transakce bude potvrzena. Nicméně pokud dojde k chybě nějaké operace, vrátí se celá transakce zpátky.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Co je transakce v Azure Cosmos DB

Transakce v typické databázi může být definována jako posloupnost operací prováděná jako jediná logická jednotka práce. Každá transakce poskytuje záruky vlastností pro KYSELINu (atomická, konzistence, izolace, odolnost).

* **Nedělitelnost** zaručuje, že všechny operace provedené uvnitř transakce jsou považovány za jednu jednotku a všechny z nich jsou potvrzeny nebo žádné z nich.
* **Konzistence** zajistí, že data jsou vždy v platném stavu napříč transakcemi.
* **Izolace** zaručuje, že žádné dvě transakce neovlivňují vzájemnou činnost – mnoho komerčních systémů poskytuje několik úrovní izolace, které se dají použít v závislosti na potřebách aplikace.
* **Odolnost** zajišťuje, že všechny změny, které jsou potvrzené v databázi, budou vždy k dispozici.
Azure Cosmos DB podporuje s izolací snímku pro operace v rámci stejného [klíče logického oddílu](partitioning-overview.md) [úplné transakce splňující požadavky na kyselinu](database-transactions-optimistic-concurrency.md) .

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Transakční operace dávkového zpracování vs. uložené procedury

Azure Cosmos DB aktuálně podporuje uložené procedury, které také poskytují obor transakcí pro operace. Transakční operace v dávce ale nabízí následující výhody:

* **Možnost jazyka** – transakční dávka je podporovaná v sadě SDK a jazyce, se kterým už pracujete, zatímco uložené procedury je potřeba zapsat v JavaScriptu.
* **Správa verzí kódu** – kód aplikace a jeho registrace do kanálu CI/CD je mnohem přirozenější než orchestrace aktualizace úložné procedury a zajištění, aby se změna projevila ve správnou dobu. Také usnadňuje vracení změn.
* **Výkon** – zmenšená latence ekvivalentních operací až o 30% ve srovnání s prováděním uložených procedur.
* **Serializace obsahu** – každá operace v rámci transakční dávky může využít vlastní možnosti serializace pro jeho datovou část.

## <a name="how-to-create-a-transactional-batch-operation"></a>Postup vytvoření transakční operace v dávce

Při vytváření operace transakční dávky začínáte z instance kontejneru a volání `CreateTransactionalBatch` :

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

V dalším kroku budete muset zavolat `ExecuteAsync` do dávky:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Jakmile je odpověď přijata, prověřte, jestli je úspěšná, a extrahujte výsledky:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Pokud dojde k selhání, bude mít neúspěšná operace stavový kód odpovídající chyby. Všechny ostatní operace budou mít stavový kód 424 (neúspěšná závislost). V následujícím příkladu se operace nezdařila, protože se pokusí vytvořit položku, která již existuje (409 HttpStatusCode. konflikty). Stavový kód umožňuje určit příčinu selhání transakce.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Jak se provádějí transakční operace v dávce transakcí

Při `ExecuteAsync` volání metody jsou všechny operace v `TransactionalBatch` objektu seskupeny, serializovány do jediné datové části a odeslány jako jediný požadavek na službu Azure Cosmos DB.

Služba obdrží požadavek a provede všechny operace v rámci transakčního oboru a vrátí odpověď pomocí stejného protokolu serializace. Tato odpověď je buď úspěšná, nebo neúspěšná, a poskytuje jednotlivé odezvy operací na operaci.

Sada SDK zpřístupňuje odpověď pro ověření výsledku a volitelně extrahuje všechny výsledky vnitřní operace.

## <a name="limitations"></a>Omezení

V současné době existují dvě známá omezení:

* Limit velikosti požadavku Azure Cosmos DB omezuje velikost `TransactionalBatch` datové části na hodnotu nepřesahující 2 MB a maximální doba spuštění je 5 sekund.
* `TransactionalBatch`Pro zajištění toho, že výkon je podle očekávání a v rámci SLA, existuje aktuální limit 100 operací za sekundu.

## <a name="next-steps"></a>Další kroky

* Další informace o [tom, co můžete s TransactionalBatch dělat](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* Další informace o tom, jak používat naši sadu Cosmos DB .NET SDK, najdete v našich [ukázkách](sql-api-dotnet-v3sdk-samples.md) .
