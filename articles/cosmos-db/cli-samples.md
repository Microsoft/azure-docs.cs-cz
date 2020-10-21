---
title: Ukázky v Azure CLI pro rozhraní API pro Azure Cosmos DB Core (SQL)
description: Ukázky v Azure CLI pro rozhraní API pro Azure Cosmos DB Core (SQL)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: c5ed9af0c133f746f5761d6c41a5fdb9f25589ac
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276365"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Ukázky v Azure CLI pro rozhraní API pro Azure Cosmos DB Core (SQL)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro službu Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](/cli/azure/cosmosdb). Ukázkové skripty Azure Cosmos DB CLI můžete najít také v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Tyto ukázky vyžadují Azure CLI verze 2.12.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli) .

Ukázky Azure CLI pro další rozhraní API najdete v ukázkách [CLI pro Cassandra](cli-samples-cassandra.md), ukázky CLI pro [MongoDB API](cli-samples-mongodb.md), ukázky CLI pro [Gremlin](cli-samples-gremlin.md), ukázky rozhraní příkazového řádku pro [tabulku](cli-samples-table.md) .

## <a name="common-samples"></a>Běžné ukázky

Tyto ukázky se vztahují na všechna rozhraní Azure Cosmos DB API.

|Úloha | Popis |
|---|---|
| [Oblasti přidání nebo převzetí služeb při selhání](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Přidání oblasti, změna priority převzetí služeb při selhání, aktivace ručního převzetí služeb při selhání|
| [Klíče účtu a připojovací řetězce](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Vypíše klíče účtu, klíče jen pro čtení, znovu vygeneruje klíče a seznam připojovacích řetězců.|
| [Zabezpečení pomocí brány firewall protokolu IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos s nakonfigurovanou bránou firewall protokolu IP.|
| [Zabezpečený nový účet pomocí koncových bodů služby](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos a zabezpečte ho pomocí koncových bodů služby.|
| [Zabezpečení stávajícího účtu pomocí koncových bodů služby](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aktualizujte účet Cosmos na zabezpečení pomocí koncových bodů služby, když je podsíť nakonec nakonfigurovaná.|
|||

## <a name="core-sql-api-samples"></a>Core (SQL) – ukázky rozhraní API

|Úloha | Popis |
|---|---|
| [Vytvoření účtu Azure Cosmos, databáze a kontejneru](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a kontejner pro rozhraní API jádra (SQL). |
| [Vytvoření účtu Azure Cosmos, databáze a kontejneru pomocí automatického škálování](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a kontejner s funkcí automatického škálování pro rozhraní API jádra (SQL). |
| [Operace propustnosti](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Čtení, aktualizace a migrace mezi automatické škálování a standardní propustností databáze a kontejneru.|
| [Uzamčení prostředků před odstraněním](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||
