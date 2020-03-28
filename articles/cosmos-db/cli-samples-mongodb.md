---
title: Ukázky rozhraní API Azure pro rozhraní API Azure Cosmos DB MongoDB
description: Ukázky rozhraní API Azure pro rozhraní API Azure Cosmos DB MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77524559"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Ukázky rozhraní API Azure pro rozhraní API Azure Cosmos DB MongoDB

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro rozhraní API Azure Cosmos DB MongoDB. Referenční stránky pro všechny příkazy Rozhraní příkazu Příkaz příkazu Azure Cosmos DB JSOU k dispozici v [referenčním rozhraní příkazového příkazu Azure .](/cli/azure/cosmosdb) Všechny ukázky skriptu Rozhraní příkazového od Db BZI Azure Cosmos najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> V současné době můžete vytvořit jenom verzi 3.2 (to `*.documents.azure.com`znamená účty pomocí koncového bodu ve formátu) rozhraní API Azure Cosmos DB pro účty MongoDB pomocí šablon PowerShell, CLI a Resource Manager. Chcete-li vytvořit verzi účtů 3.6, použijte místo toho portál Azure.

| |  |
|---|---|
| [Vytvoření účtu, databáze a kolekce Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a kolekci pro rozhraní API MongoDB. |
| [Změnit propustnost](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Aktualizace RU/s v databázi a kolekci.|
| [Přidání oblastí s podporou převzetí služeb při selhání](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Přidejte oblast, změňte prioritu převzetí služeb při selhání, aktivujte ruční převzetí služeb při selhání.|
| [Klíče účtů a připojovací řetězce](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Seznam klíčů účtů, klíčů jen pro čtení, regenerovat klíče a seznam připojovacích řetězců.|
| [Zabezpečení pomocí ip firewallu](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos s nakonfigurovanou bránou firewall IP.|
| [Zabezpečení nového účtu pomocí koncových bodů služby](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos a zabezpečte je pomocí koncových bodů služby.|
| [Zabezpečení existujícího účtu pomocí koncových bodů služby](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aktualizujte účet Cosmos pro zabezpečení pomocí koncových bodů služby, když je nakonec nakonfigurována podsíť.|
|||
