---
title: Azure PowerShell ukázky pro Azure Cosmos DB rozhraní API pro tabulky
description: Získání ukázek Azure PowerShell k provádění běžných úloh v Azure Cosmos DB rozhraní API pro tabulky
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: be0fd7db7b28ea9801e065f1327596021b3e31b1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332895"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-table-api"></a>Azure PowerShell ukázky pro Azure Cosmos DB rozhraní API pro tabulky
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Následující tabulka obsahuje odkazy na běžně používané Azure PowerShell skripty pro Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny rutiny Azure Cosmos DB PowerShellu jsou k dispozici v [Referenční příručce Azure PowerShell](/powershell/module/az.cosmosdb). Pravidelně prosím zkontrolujte aktualizace `Az.CosmosDB` . Tyto ukázky PowerShellu můžete také rozvětvit pro Cosmos DB z našeho úložiště GitHub [Cosmos DB ukázky PowerShellu na GitHubu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Běžné ukázky

|Úkol | Popis |
|---|---|
|[Aktualizace účtu](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje výchozí úroveň konzistence účtu Cosmos DB. |
|[Aktualizace oblastí účtu](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje oblasti účtu Cosmos DB. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu Azure Cosmos DB účtu. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB s povolenou bránou firewall protokolu IP. |
|||

## <a name="table-api-samples"></a>Ukázky rozhraní API pro tabulky

|Úkol | Popis |
|---|---|
|[Vytvoření účtu a tabulky](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos a tabulku. |
|[Vytvoření účtu a tabulky pomocí automatického škálování](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos a automatické škálování tabulky. |
|[Vypsat nebo získat tabulky](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo získá tabulky. |
|[Operace propustnosti](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti tabulky, včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
