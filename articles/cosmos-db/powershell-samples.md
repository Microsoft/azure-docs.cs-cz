---
title: Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB Core (SQL)
description: Získání ukázek Azure PowerShell pro provádění běžných úloh v rozhraní API pro jádro (SQL) Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 174458d9e5be24de669060ea8264f62017489c7a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279306"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB Core (SQL)

Následující tabulka obsahuje odkazy na běžně používané Azure PowerShell skripty pro Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny rutiny Azure Cosmos DB PowerShellu jsou k dispozici v [Referenční příručce Azure PowerShell](/powershell/module/az.cosmosdb). Pravidelně prosím zkontrolujte aktualizace `Az.CosmosDB` . Tyto ukázky PowerShellu můžete také rozvětvit pro Cosmos DB z našeho úložiště GitHub [Cosmos DB ukázky PowerShellu na GitHubu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Rutiny PowerShellu pro další rozhraní API najdete v tématu [ukázky PowerShellu pro Cassandra](powershell-samples-cassandra.md), ukázky PowerShellu pro [MongoDB API](powershell-samples-mongodb.md), ukázky PowerShellu pro Gremlin, [ukázky](powershell-samples-table.md) PowerShellu pro [PowerShell Samples for Gremlin](powershell-samples-gremlin.md)

## <a name="common-samples"></a>Běžné ukázky

|Úloha | Popis |
|---|---|
|[Aktualizace účtu](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje výchozí úroveň konzistence účtu Cosmos DB. |
|[Aktualizace oblastí účtu](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje oblasti účtu Cosmos DB. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu Azure Cosmos DB účtu. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB s povolenou bránou firewall protokolu IP. |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) – ukázky rozhraní API

|Úloha | Popis |
|---|---|
|[Vytvoření účtu, databáze a kontejneru](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB, databázi a kontejner. |
|[Vytvoření účtu, databáze a kontejneru pomocí automatického škálování](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB, databázi a kontejner s možností automatického škálování. |
|[Vytvoření kontejneru s velkým klíčem oddílu](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte kontejner s velkým klíčem oddílu. |
|[Vytvoření kontejneru bez zásad indexování](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos se zakázanými zásadami indexování.|
|[Vypsat nebo získat databáze nebo kontejnery](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo kontejnery. |
|[Operace propustnosti](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti pro databázi nebo kontejner včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
