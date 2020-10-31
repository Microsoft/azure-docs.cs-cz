---
title: Ukázky Azure PowerShell pro rozhraní Azure Cosmos DB API pro MongoDB
description: Získání ukázek Azure PowerShell k provádění běžných úloh v rozhraní Azure Cosmos DB API pro MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 6b2ff9371feab1de942f5e3a7de5ff02ada883b0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096219"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>Ukázky Azure PowerShell pro rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

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

## <a name="mongo-db-api-samples"></a>Ukázky rozhraní API pro Mongo DB

|Úloha | Popis |
|---|---|
|[Vytvoření účtu, databáze a kolekce](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a kolekci. |
|[Vytvoření účtu, databáze a kolekce pomocí automatického škálování](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a kolekci s použitím automatického škálování. |
|[Vypsat nebo získat databáze nebo kolekce](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo kolekci. |
|[Operace propustnosti](scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti databáze nebo kolekce, včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
