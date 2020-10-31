---
title: Ukázky v Azure CLI pro rozhraní Azure Cosmos DB Gremlin API
description: Ukázky v Azure CLI pro rozhraní Azure Cosmos DB Gremlin API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb992b81318c33e9b06637f8e3ad52f63fa8ed33
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073157"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-gremlin-api"></a>Ukázky v Azure CLI pro rozhraní Azure Cosmos DB Gremlin API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro službu Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](/cli/azure/cosmosdb). Ukázkové skripty Azure Cosmos DB CLI můžete najít také v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Tyto ukázky vyžadují Azure CLI verze 2.12.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli) .

## <a name="common-samples"></a>Běžné ukázky

Tyto ukázky se vztahují na všechna rozhraní Azure Cosmos DB API.

|Úloha | Popis |
|---|---|
| [Oblasti přidání nebo převzetí služeb při selhání](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Přidání oblasti, změna priority převzetí služeb při selhání, aktivace ručního převzetí služeb při selhání|
| [Klíče účtu a připojovací řetězce](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Vypíše klíče účtu, klíče jen pro čtení, znovu vygeneruje klíče a seznam připojovacích řetězců.|
| [Zabezpečení pomocí brány firewall protokolu IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos s nakonfigurovanou bránou firewall protokolu IP.|
| [Zabezpečený nový účet pomocí koncových bodů služby](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos a zabezpečte ho pomocí koncových bodů služby.|
| [Zabezpečení stávajícího účtu pomocí koncových bodů služby](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aktualizujte účet Cosmos na zabezpečení pomocí koncových bodů služby, když je podsíť nakonec nakonfigurovaná.|
|||

## <a name="gremlin-api-samples"></a>Ukázky rozhraní API pro Gremlin

|Úloha | Popis |
|---|---|
| [Vytvoření účtu Azure Cosmos, databáze a grafu](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a graf pro rozhraní Gremlin API. |
| [Vytvoření účtu Azure Cosmos, databáze a grafu pomocí automatického škálování](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a graf s možností automatického škálování pro rozhraní Gremlin API. |
| [Operace propustnosti](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Přečtěte si, aktualizujte a migrujte mezi automatické škálování a standardní propustností databáze a grafu.|
| [Uzamčení prostředků před odstraněním](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||
