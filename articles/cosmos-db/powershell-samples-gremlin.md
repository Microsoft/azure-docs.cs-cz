---
title: Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB Gremlin
description: Získání ukázek Azure PowerShell k provádění běžných úloh v rozhraní API pro Azure Cosmos DB Gremlin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: a0bd1c732a23ca123c391521f5895eb4dcb8c879
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503364"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Následující tabulka obsahuje odkazy na běžně používané Azure PowerShell skripty pro Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny rutiny Azure Cosmos DB PowerShellu jsou k dispozici v [Referenční příručce Azure PowerShell](/powershell/module/az.cosmosdb). `Az.CosmosDB`Modul je teď součástí `Az` modulu. Pokud chcete získat rutiny Azure Cosmos DB, [Stáhněte si a nainstalujte](/powershell/azure/install-az-ps) nejnovější verzi modulu AZ Module. Nejnovější verzi můžete získat také z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az/5.4.0). Tyto ukázky PowerShellu můžete také rozvětvit pro Cosmos DB z našeho úložiště GitHub [Cosmos DB ukázky PowerShellu na GitHubu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Běžné ukázky

|Úkol | Popis |
|---|---|
|[Aktualizace účtu](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje výchozí úroveň konzistence účtu Cosmos DB. |
|[Aktualizace oblastí účtu](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje oblasti účtu Cosmos DB. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu Azure Cosmos DB účtu. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB s povolenou bránou firewall protokolu IP. |
|||

## <a name="gremlin-api-samples"></a>Ukázky rozhraní API pro Gremlin

|Úkol | Popis |
|---|---|
|[Vytvoření účtu, databáze a grafu](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a graf. |
|[Vytvoření účtu, databáze a grafu pomocí automatického škálování](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a graf s možností automatického škálování. |
|[Vypsat nebo získat databáze nebo grafy](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo graf. |
|[Operace propustnosti](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti databáze nebo grafu, včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
