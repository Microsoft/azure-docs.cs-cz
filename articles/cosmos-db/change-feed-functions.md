---
title: Použití změn databáze Azure Cosmos DB kanálu s využitím Azure Functions
description: Použití služby Azure Cosmos DB Změna kanálu s využitím Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 18780deba1910b3ea77f7313bfb6d204dacabd82
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112017"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektur bez serveru založené na události s Azure Cosmos DB a Azure Functions

Služba Azure Functions poskytuje nejjednodušší způsob, jak připojit [kanálu změn](change-feed.md). Můžete vytvořit malé reaktivní funkce Azure, která se spustí automaticky při každé nové události v kanálu změn váš kontejner Azure Cosmos.

![Funkce bez serveru založené na událostech práce se aktivační událost Azure Cosmos DB](./media/change-feed-functions/functions.png)

S [aktivační událost Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), můžete využít [Change Feed Processor](./change-feed-processor.md)je škálování a funkci zjišťování spolehlivé událostí bez nutnosti udržovat žádné [pracovního procesu Infrastruktura](./change-feed-processor.md#implementing-the-change-feed-processor-library). Stačí soustředit na logiku funkce Azure bez starostí o zbytek kanálu modelu event sourcing. Aktivační událost můžete dokonce kombinovat s jinými [vazeb Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> V současné době podporuje aktivační událost Azure Cosmos DB pro použití s Core (SQL) rozhraní API pouze.

## <a name="requirements"></a>Požadavky

K implementaci bez serveru založené na událostech toku, budete potřebovat:

* **Monitorované kontejneru**: Monitorované kontejneru je kontejner Azure Cosmos monitoruje a ukládá data, ze kterého je generován kanál změn. Všechny operace vložení a změny (například CRUD) do monitorovaného kontejneru se projeví v kanálu změn kontejneru.
* **Kontejneru zapůjčení**: Kontejneru zapůjčení udržuje svůj stav mezi různými a dynamické funkci Azure Functions instance a umožňuje dynamické škálování. Tento kontejner zapůjčení mohou ručně nebo automaticky vytvořit Azure Cosmos DB Trigger.To automaticky vytvořit kontejner zapůjčení, nastavte *CreateLeaseCollectionIfNotExists* příznak v [konfigurace](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Dělené zapůjčení kontejnery jsou musí mít `/id` definice klíče oddílu.

## <a name="create-your-azure-cosmos-db-trigger"></a>Vytvoření triggeru služby Azure Cosmos DB

Vytvoření funkce Azure s aktivační událost Azure Cosmos DB se teď podporuje ve všech integrovaného vývojového prostředí Azure Functions a integrace rozhraní příkazového řádku:

* [Rozšíření sady Visual Studio](../azure-functions/functions-develop-vs.md) pro uživatele sady Visual Studio.
* [Rozšíření sady Visual Studio Core](https://code.visualstudio.com/tutorials/functions-extension/create-function) pro uživatele Visual Studio Code.
* A nakonec [základní rozhraní příkazového řádku nástroje](../azure-functions/functions-run-local.md#create-func) pro různé platformy bez prostředí IDE.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Místní spuštění aktivační událost Azure Cosmos DB

Můžete spustit váš [funkce Azure Functions místně](../azure-functions/functions-develop-local.md) s [emulátor služby Azure Cosmos DB](./local-emulator.md) k vytváření a vývoj bez serveru založené na událostech toky bez předplatného Azure a bez jakýchkoli nákladů.

Pokud chcete otestovat za provozu scénáře v cloudu, můžete si [Cosmos DB zdarma vyzkoušet](https://azure.microsoft.com/try/cosmosdb/) bez jakékoli platební kartu ani předplatné Azure.

## <a name="next-steps"></a>Další postup

Teď můžete pokračovat na další informace o změně v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Způsoby, jak čtení kanálu změn](read-change-feed.md)
* [Změna kanálu knihovny procesoru](change-feed-processor.md)
* [Jak pracovat s změnit informační kanál knihovny procesoru](change-feed-processor.md)
* [Výpočetní prostředí bez serveru databázi s využitím služby Azure Cosmos DB a Azure Functions](serverless-computing-database.md)
