---
title: Ukázky Azure PowerShell pro rozhraní API Azure Cosmos DB-SQL (Core)
description: Získání ukázek Azure PowerShell k provádění různých běžných úloh v Azure Cosmos DB účtech rozhraní SQL API
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80366192"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Ukázky Azure PowerShell pro rozhraní API Azure Cosmos DB-SQL (Core)

Následující tabulka obsahuje odkazy na běžně používané skripty Azure PowerShell pro rozhraní API pro Azure Cosmos DB SQL (Core). Pokud chcete tyto ukázky PowerShellu rozvětvit pro Cosmos DB z našeho úložiště GitHubu, navštivte [Cosmos DB ukázky PowerShellu na GitHubu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Další Cosmos DB ukázky PowerShellu pro rozhraní API a dokumentaci k SQL (Core) najdete v tématu [správa Azure Cosmos DBch prostředků rozhraní SQL API pomocí PowerShellu](manage-with-powershell.md). Ukázky Cosmos DB PowerShellu pro jiná rozhraní API najdete v tématu [rozhraní API Cassandra](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)a [rozhraní API pro tabulky](powershell-samples-table.md).

> [!NOTE]
> Ukázky využívají rutiny [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management. Počítejte s tím, `Az.CosmosDB` že rutiny jsou stále ve verzi Preview a můžou se před vydáním změnit. `Az.CosmosDB` Pravidelně prosím zkontrolujte aktualizace.

| | |
|---|---|
|[Vytvoření účtu, databáze a kontejneru](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB, databázi a kontejner. |
|[Vytvoření kontejneru s velkým klíčem oddílu](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte kontejner s velkým klíčem oddílu. |
|[Vypsat nebo získat databáze nebo kontejnery](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo kontejnery. |
|[Získat RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte RU/s pro databázi nebo kontejner. |
|[Aktualizace RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizujte RU/s pro databázi nebo kontejner. |
|[Vytvoření kontejneru bez zásad indexování](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos se zakázanými zásadami indexování.|
|[Aktualizace účtu](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje výchozí úroveň konzistence účtu Cosmos DB. |
|[Aktualizace oblastí účtu](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje oblasti účtu Cosmos DB. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu Azure Cosmos DB účtu. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB s povolenou bránou firewall protokolu IP. |
|||
