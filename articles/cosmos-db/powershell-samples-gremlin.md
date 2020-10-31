---
title: Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB Gremlin
description: Získání ukázek Azure PowerShell k provádění běžných úloh v rozhraní API pro Azure Cosmos DB Gremlin
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 0785b8e0428e4f6dc4b49d24e9cc53a36f8ba4f5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096236"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Následující tabulka obsahuje odkazy na běžně používané Azure PowerShell skripty pro Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny rutiny Azure Cosmos DB PowerShellu jsou k dispozici v [Referenční příručce Azure PowerShell](/powershell/module/az.cosmosdb). Pravidelně prosím zkontrolujte aktualizace `Az.CosmosDB` . Tyto ukázky PowerShellu můžete také rozvětvit pro Cosmos DB z našeho úložiště GitHub [Cosmos DB ukázky PowerShellu na GitHubu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Běžné ukázky

|Úloha | Popis |
|---|---|
|[Aktualizace účtu](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje výchozí úroveň konzistence účtu Cosmos DB. |
|[Aktualizace oblastí účtu](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje oblasti účtu Cosmos DB. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu Azure Cosmos DB účtu. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB s povolenou bránou firewall protokolu IP. |
|||

## <a name="gremlin-api-samples"></a>Ukázky rozhraní API pro Gremlin

|Úloha | Popis |
|---|---|
|[Vytvoření účtu, databáze a grafu](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a graf. |
|[Vytvoření účtu, databáze a grafu pomocí automatického škálování](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a graf s možností automatického škálování. |
|[Vypsat nebo získat databáze nebo grafy](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo graf. |
|[Operace propustnosti](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti databáze nebo grafu, včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
