---
title: Použití kanálu změny Azure Cosmos DB s Azure Functions
description: Použití Azure Cosmos DB změn kanálu s Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: db3f1b6657ae455ae049eaffd6758fc7e6944fb9
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001021"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektury založené na událostech bez serveru s Azure Cosmos DB a Azure Functions

Azure Functions poskytuje nejjednodušší způsob, jak se připojit ke [kanálu změn](change-feed.md). Můžete vytvořit malé proaktivní Azure Functions, které se automaticky aktivují pro každou novou událost v kanálu změn kontejneru Azure Cosmos.

![Funkce založené na událostech bez serveru pracující s triggerem Azure Cosmos DB](./media/change-feed-functions/functions.png)

Pomocí [triggeru Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)můžete využívat funkce škálování a spolehlivého rozpoznávání událostí v [kanálu změn](./change-feed-processor.md)bez nutnosti udržovat [infrastrukturu pracovních procesů](./change-feed-processor.md). Zaměřte se jenom na logiku funkce Azure Functions, aniž byste se museli starat o zbytek kanálu týkajícího se zdrojů událostí. Trigger můžete dokonce kombinovat s dalšími [vazbami Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> V současné době se aktivační událost Azure Cosmos DB podporuje jenom s rozhraním API Core (SQL).

## <a name="requirements"></a>Požadavky

K implementaci toku založeného na události bez serveru budete potřebovat:

* **Monitorovaný kontejner**: Monitorovaný kontejner je monitorovaný kontejner Azure Cosmos a ukládá data, ze kterých se generuje kanál změn. Jakékoli vložení a změny (například CRUD) do monitorovaného kontejneru se projeví v kanálu změn kontejneru.
* **Kontejner zapůjčení**: Kontejner zapůjčení udržuje stav napříč několika a dynamickými instancemi funkcí Azure bez serveru a umožňuje dynamické škálování. Tento kontejner zapůjčení můžete ručně nebo automaticky vytvořit pomocí Azure Cosmos DB Trigger.To automaticky vytvořit kontejner zapůjčení, v [konfiguraci](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)nastavte příznak *CreateLeaseCollectionIfNotExists* . Kontejnery zapůjčení musí mít `/id` definici klíče oddílu.

## <a name="create-your-azure-cosmos-db-trigger"></a>Vytvoření triggeru Azure Cosmos DB

Vytvoření funkce Azure pomocí triggeru Azure Cosmos DB se teď podporuje napříč všemi Azure Functions IDE a integrací CLI:

* [Rozšíření Visual studia](../azure-functions/functions-develop-vs.md) pro uživatele sady Visual Studio.
* [Základní rozšíření sady Visual Studio](https://code.visualstudio.com/tutorials/functions-extension/create-function) pro uživatele Visual Studio Code.
* A nakonec [základní nástroje CLI](../azure-functions/functions-run-local.md#create-func) pro nezávislá prostředí IDE pro různé platformy.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Místní spuštění triggeru Azure Cosmos DB

Službu [Azure Function](../azure-functions/functions-develop-local.md) můžete spustit místně pomocí emulátoru [Azure Cosmos DB](./local-emulator.md) , abyste mohli vytvářet a vyvíjet toky založené na událostech bez serveru bez předplatného Azure nebo náklady.

Pokud chcete testovat živé scénáře v cloudu, můžete [vyzkoušet Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) , aniž byste museli vyžadovat platební kartu nebo předplatné Azure.

## <a name="next-steps"></a>Další kroky

Teď můžete dál získat další informace o službě Change feed v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Způsoby čtení kanálu změn](read-change-feed.md)
* [Změna kanálu knihovny procesoru](change-feed-processor.md)
* [Jak pracovat s knihovnou Change feed Processor](change-feed-processor.md)
* [Výpočetní databáze bez serveru s využitím Azure Cosmos DB a Azure Functions](serverless-computing-database.md)
