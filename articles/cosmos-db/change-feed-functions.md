---
title: Použití kanálu změny Azure Cosmos DB s Azure Functions
description: Pomocí Azure Functions se připojte k Azure Cosmos DB změnám kanálu. Později můžete vytvořit reaktivní funkce Azure, které se aktivují při každé nové události.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7f4903cf29f15132db91e47d78efe5a556efd937
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340239"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektury založené na událostech bez serveru s Azure Cosmos DB a Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Functions poskytuje nejjednodušší způsob, jak se připojit ke [kanálu změn](change-feed.md). Můžete vytvořit malé proaktivní Azure Functions, které se automaticky aktivují pro každou novou událost v kanálu změn kontejneru Azure Cosmos.

:::image type="content" source="./media/change-feed-functions/functions.png" alt-text="Funkce založené na událostech bez serveru, které pracují s triggerem Azure Functions pro Cosmos DB" border="false":::

Pomocí [triggeru Azure Functions pro Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)můžete využívat funkce škálování a spolehlivého rozpoznávání událostí v [kanálu změn](./change-feed-processor.md), aniž byste museli udržovat žádnou [infrastrukturu pracovních procesů](./change-feed-processor.md). Zaměřte se jenom na logiku funkce Azure Functions, aniž byste se museli starat o zbytek kanálu týkajícího se zdrojů událostí. Trigger můžete dokonce kombinovat s dalšími [vazbami Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> V současné době se aktivační událost Azure Functions pro Cosmos DB podporuje jenom s rozhraním API Core (SQL).

## <a name="requirements"></a>Požadavky

K implementaci toku založeného na události bez serveru budete potřebovat:

* **Monitorovaný kontejner**: monitorovaný kontejner je monitorovaným kontejnerem Azure Cosmos a ukládá data, ze kterých se generuje kanál změn. Jakékoli vložení, aktualizace monitorovaného kontejneru se projeví v kanálu změn kontejneru.
* **Kontejner zapůjčení**: kontejner zapůjčení udržuje stav napříč několika a dynamickými instancemi funkcí Azure bez serveru a umožňuje dynamické škálování. Tento kontejner zapůjčení může být ručně nebo automaticky vytvořen triggerem Azure Functions pro Cosmos DB. Chcete-li automaticky vytvořit kontejner zapůjčení, nastavte v [konfiguraci](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)příznak *CreateLeaseCollectionIfNotExists* . Kontejnery zapůjčení musí mít `/id` definici klíče oddílu.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Vytvoření triggeru Azure Functions pro Cosmos DB

Vytvoření funkce Azure pomocí triggeru Azure Functions pro Cosmos DB se teď podporuje napříč všemi Azure Functions integrace IDE a CLI:

* [Rozšíření Visual studia](../azure-functions/functions-develop-vs.md) pro uživatele sady Visual Studio.
* [Visual Studio Code rozšíření](/azure/developer/javascript/tutorial-vscode-serverless-node-01) pro uživatele Visual Studio Code.
* A nakonec [základní nástroje CLI](../azure-functions/functions-run-local.md#create-func) pro nezávislá prostředí IDE pro různé platformy.

## <a name="run-your-trigger-locally"></a>Místní spuštění triggeru

Službu [Azure Function](../azure-functions/functions-develop-local.md) můžete spustit místně pomocí [emulátoru Azure Cosmos DB](./local-emulator.md) , abyste mohli vytvářet a vyvíjet toky založené na událostech bez serveru bez předplatného Azure nebo náklady.

Pokud chcete testovat živé scénáře v cloudu, můžete [vyzkoušet Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) , aniž byste museli vyžadovat platební kartu nebo předplatné Azure.

## <a name="next-steps"></a>Další kroky

Teď můžete dál získat další informace o službě Change feed v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Způsoby čtení kanálu změn](read-change-feed.md)
* [Použití knihovny Change feed Processor](change-feed-processor.md)
* [Jak pracovat s knihovnou Change feed Processor](change-feed-processor.md)
* [Výpočetní databáze bez serveru s využitím Azure Cosmos DB a Azure Functions](serverless-computing-database.md)
