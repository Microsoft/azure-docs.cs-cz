---
title: Jak používat kanál změn Azure Cosmos DB s funkcemi Azure
description: Pomocí funkce Azure se můžete připojit ke zdroji změn Azure Cosmos DB. Později můžete vytvořit reaktivní funkce Azure, které se aktivují při každé nové události.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851363"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektury založené na událostech bez serveru s Azure Cosmos DB a Funkceazure

Funkce Azure poskytuje nejjednodušší způsob, jak se připojit k [kanálu změn](change-feed.md). Můžete vytvořit malé reaktivní funkce Azure, které se automaticky aktivují při každé nové události v kanálu změn vašeho kontejneru Azure Cosmos.

![Funkce založené na událostech bez serveru pracují s aktivační událostí Azure Funkce pro Cosmos DB](./media/change-feed-functions/functions.png)

S [aktivační událostí Funkce Azure pro Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md), můžete využít [změnit feed procesoru](./change-feed-processor.md)škálování a spolehlivé funkce detekce událostí bez nutnosti udržovat pracovní [infrastruktury](./change-feed-processor.md). Stačí se zaměřit na logiku funkce Azure, aniž byste se museli starat o zbytek kanálu získávání událostí. Můžete dokonce kombinovat Trigger s jakoukoli jinou [vazby Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> V současné době je aktivační událost Azure Functions pro Cosmos DB podporovaná pro použití jenom s rozhraním API Core (SQL).

## <a name="requirements"></a>Požadavky

Chcete-li implementovat tok založený na událostech bez serveru, potřebujete:

* **Monitorovaný kontejner**: Monitorovaný kontejner je kontejner Azure Cosmos, který se monitoruje, a ukládá data, ze kterých se generuje zdroj změn. Všechny vloží, aktualizace monitorovaného kontejneru se projeví v kanálu změn kontejneru.
* **Kontejner zapůjčení**: Kontejner zapůjčení udržuje stav napříč více a dynamické serverové funkce Azure function instance a umožňuje dynamické škálování. Tento kontejner zapůjčení lze ručně nebo automaticky vytvořit aktivační událost Funkce Azure pro Cosmos DB. Chcete-li automaticky vytvořit kontejner zapůjčení, nastavte příznak *CreateLeaseCollectionIfNotExists* v [konfiguraci](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Kontejnery s oddíly zapůjčení musí mít definici klíče oddílu. `/id`

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Vytvoření aktivační události Azure funkce pro Cosmos DB

Vytvoření funkce Azure pomocí aktivační události Azure Functions pro Cosmos DB je teď podporované napříč všemi integracemi IDE a CLI funkcí Azure:

* [Rozšíření visual studia](../azure-functions/functions-develop-vs.md) pro uživatele sady Visual Studio.
* [Rozšíření kódu visual studia](/azure/javascript/tutorial-vscode-serverless-node-01) pro uživatele kódu sady Visual Studio.
* A konečně [základní CLI nástroje](../azure-functions/functions-run-local.md#create-func) pro cross-platformní IDE agnostik zkušenosti.

## <a name="run-your-trigger-locally"></a>Spuštění aktivační události místně

Pomocí [emulátoru Azure Cosmos DB](./local-emulator.md) můžete funkci [Azure function](../azure-functions/functions-develop-local.md) spustit místně a můžete vytvářet a vyvíjet toky založené na událostech bez serveru bez předplatného Azure nebo bez jakýchkoli nákladů.

Pokud chcete otestovat živé scénáře v cloudu, můžete [cosmos DB vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez nutnosti jakékoli platební karty nebo předplatného Azure.

## <a name="next-steps"></a>Další kroky

Další informace o kanálu změn můžete nyní získat v následujících článcích:

* [Přehled zdroje změn](change-feed.md)
* [Způsoby čtení informačního kanálu o změnách](read-change-feed.md)
* [Použití knihovny procesoru kanálu změn](change-feed-processor.md)
* [Jak pracovat s knihovnou procesoru zdroje změn](change-feed-processor.md)
* [Výpočetní výpočetní služby bez serveru pomocí Azure Cosmos DB a Azure Functions](serverless-computing-database.md)
