---
title: Ukázky Prostředí Azure PowerShell pro rozhraní API Azure Cosmos DB – SQL (Core)
description: Získání ukázek Prostředí Azure PowerShell k provádění různých běžných úloh v účtech SQL API Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366192"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Ukázky Prostředí Azure PowerShell pro rozhraní API Azure Cosmos DB – SQL (Core)

Následující tabulka obsahuje odkazy na běžně používané skripty Azure PowerShell u rozhraní API Azure Cosmos DB for SQL (Core). Pokud chcete vyklopit tyto ukázky Prostředí PowerShell pro Cosmos DB z našeho úložiště GitHub, navštivte [Cosmos DB PowerShell Ukázky na GitHubu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Další ukázky prostředí Cosmos DB PowerShell pro rozhraní SQL (Core) API a dokumentaci najdete v tématu [Správa prostředků SQL API Azure Cosmos DB pomocí Prostředí PowerShell](manage-with-powershell.md). Ukázky prostředí Cosmos DB PowerShell pro jiná rozhraní API naleznete v [tématu Cassandra API](powershell-samples-cassandra.md), [Rozhraní API MongoDB](powershell-samples-mongodb.md), [rozhraní GREMLIN API](powershell-samples-gremlin.md)a rozhraní API [tabulky](powershell-samples-table.md).

> [!NOTE]
> Ukázky používají rutiny správy [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Upozorňujeme, `Az.CosmosDB` že rutiny jsou stále ve verzi Preview a mohou se před vydáním změnit. Pravidelně kontrolujte `Az.CosmosDB` aktualizace.

| | |
|---|---|
|[Vytvoření účtu, databáze a kontejneru](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet, databázi a kontejner Azure Cosmos DB. |
|[Vytvoření kontejneru s velkým klíčem oddílu](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte kontejner s velkým klíčem oddílu. |
|[Seznam nebo získání databází nebo kontejnerů](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Seznam nebo získat databáze nebo kontejnery. |
|[Získat RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte RU/s pro databázi nebo kontejner. |
|[Aktualizace Ru/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizace RU/s pro databázi nebo kontejner. |
|[Vytvoření kontejneru bez zásad indexu](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvořte kontejner Azure Cosmos s vypnutou zásadou indexu.|
|[Aktualizace účtu](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizujte výchozí úroveň konzistence účtu Cosmos DB. |
|[Aktualizace oblastí obchodního vztahu](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizujte oblasti účtu Cosmos DB. |
|[Změna priority převzetí služeb při selhání nebo aktivace převzetí služeb při selhání](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte místní prioritu převzetí služeb při selhání účtu Azure Cosmos nebo aktivuj ruční převzetí služeb při selhání. |
|[Klíče účtů nebo připojovací řetězce](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu účtu Azure Cosmos DB. |
|[Vytvoření účtu Cosmos pomocí ip firewallu](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB s povolenou ip bránou firewall. |
|||
