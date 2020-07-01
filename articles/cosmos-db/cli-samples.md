---
title: Ukázky v Azure CLI pro Azure Cosmos DB SQL (jádro) API
description: Ukázky v Azure CLI pro Azure Cosmos DB SQL (jádro) API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 39550c3da7a5ed129e26dec3d33f19c65d76cdfa
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560529"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Ukázky v Azure CLI pro rozhraní API pro Azure Cosmos DB Core (SQL)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro rozhraní API pro Azure Cosmos DB Core (SQL). Referenční stránky pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](/cli/azure/cosmosdb). Ukázky Azure CLI pro další Azure Cosmos DB rozhraní API najdete v tématu [rozhraní API Cassandra](cli-samples-cassandra.md), [rozhraní MongoDB API](cli-samples-mongodb.md), rozhraní [Gremlin API](cli-samples-gremlin.md)a [rozhraní API pro tabulky](cli-samples-table.md). Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

|Úkol | Popis |
|---|---|
| [Vytvoření účtu Azure Cosmos, databáze a kontejneru](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a kontejner pro rozhraní API SQL (Core). |
| [Změna propustnosti](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Aktualizujte RU/s na databázi a kontejner.|
| [Oblasti přidání nebo převzetí služeb při selhání](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Přidání oblasti, změna priority převzetí služeb při selhání, aktivace ručního převzetí služeb při selhání|
| [Klíče účtu a připojovací řetězce](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Vypíše klíče účtu, klíče jen pro čtení, znovu vygeneruje klíče a seznam připojovacích řetězců.|
| [Zabezpečení pomocí brány firewall protokolu IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos s nakonfigurovanou bránou firewall protokolu IP.|
| [Zabezpečený nový účet pomocí koncových bodů služby](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos a zabezpečte ho pomocí koncových bodů služby.|
| [Zabezpečení stávajícího účtu pomocí koncových bodů služby](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aktualizujte účet Cosmos na zabezpečení pomocí koncových bodů služby, když je podsíť nakonec nakonfigurovaná.|
| [Uzamčení prostředků před odstraněním](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||
