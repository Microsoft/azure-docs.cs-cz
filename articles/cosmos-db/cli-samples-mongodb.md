---
title: Ukázky v Azure CLI pro rozhraní Azure Cosmos DB MongoDB API
description: Ukázky v Azure CLI pro rozhraní Azure Cosmos DB MongoDB API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: af510e5d29e494e96f6df6143fe012db20fb6ca4
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509453"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Ukázky v Azure CLI pro rozhraní Azure Cosmos DB MongoDB API

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro Azure Cosmos DB rozhraní MongoDB API. Referenční stránky pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](/cli/azure/cosmosdb). Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> V současné době můžete pomocí šablon PowerShellu, CLI a Správce prostředků vytvořit jenom 3,2 verze (tj. účty používající koncový bod ve formátu `*.documents.azure.com` ) Azure Cosmos DB API pro účty MongoDB. Pokud chcete vytvořit 3,6 verze účtů, použijte místo toho Azure Portal.

| |  |
|---|---|
| [Vytvoření účtu Azure Cosmos, databáze a kolekce](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a kolekci pro rozhraní API MongoDB. |
| [Změna propustnosti](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Aktualizujte RU/s na databázi a kolekci.|
| [Oblasti přidání nebo převzetí služeb při selhání](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Přidání oblasti, změna priority převzetí služeb při selhání, aktivace ručního převzetí služeb při selhání|
| [Klíče účtu a připojovací řetězce](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Vypíše klíče účtu, klíče jen pro čtení, znovu vygeneruje klíče a seznam připojovacích řetězců.|
| [Zabezpečení pomocí brány firewall protokolu IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos s nakonfigurovanou bránou firewall protokolu IP.|
| [Zabezpečený nový účet pomocí koncových bodů služby](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos a zabezpečte ho pomocí koncových bodů služby.|
| [Zabezpečení stávajícího účtu pomocí koncových bodů služby](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aktualizujte účet Cosmos na zabezpečení pomocí koncových bodů služby, když je podsíť nakonec nakonfigurovaná.|
| [Uzamčení prostředků před odstraněním](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||
