---
title: Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB Core (SQL)
description: Získání ukázek Azure PowerShell pro provádění běžných úloh v rozhraní API pro jádro (SQL) Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: ab9904127d085113ba09bf6fcd3616842dade14d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503296"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB Core (SQL)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Následující tabulka obsahuje odkazy na běžně používané Azure PowerShell skripty pro Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny rutiny Azure Cosmos DB PowerShellu jsou k dispozici v [Referenční příručce Azure PowerShell](/powershell/module/az.cosmosdb). `Az.CosmosDB`Modul je teď součástí `Az` modulu. Pokud chcete získat rutiny Azure Cosmos DB, [Stáhněte si a nainstalujte](/powershell/azure/install-az-ps) nejnovější verzi modulu AZ Module. Nejnovější verzi můžete získat také z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az/5.4.0). Tyto ukázky PowerShellu můžete také rozvětvit pro Cosmos DB z našeho úložiště GitHub [Cosmos DB ukázky PowerShellu na GitHubu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Rutiny PowerShellu pro další rozhraní API najdete v tématu [ukázky PowerShellu pro Cassandra](powershell-samples-cassandra.md), ukázky PowerShellu pro [MongoDB API](powershell-samples-mongodb.md), ukázky PowerShellu pro Gremlin, [ukázky](powershell-samples-table.md) PowerShellu pro [](powershell-samples-gremlin.md)

## <a name="common-samples"></a>Běžné ukázky

|Úkol | Popis |
|---|---|
|[Aktualizace účtu](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje výchozí úroveň konzistence účtu Cosmos DB. |
|[Aktualizace oblastí účtu](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje oblasti účtu Cosmos DB. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu Azure Cosmos DB účtu. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB s povolenou bránou firewall protokolu IP. |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) – ukázky rozhraní API

|Úkol | Popis |
|---|---|
|[Vytvoření účtu, databáze a kontejneru](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB, databázi a kontejner. |
|[Vytvoření účtu, databáze a kontejneru pomocí automatického škálování](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB, databázi a kontejner s možností automatického škálování. |
|[Vytvoření kontejneru s velkým klíčem oddílu](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte kontejner s velkým klíčem oddílu. |
|[Vytvoření kontejneru bez zásad indexování](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos se zakázanými zásadami indexování.|
|[Vypsat nebo získat databáze nebo kontejnery](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo kontejnery. |
|[Operace propustnosti](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti pro databázi nebo kontejner včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
