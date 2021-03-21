---
title: Azure PowerShell ukázky pro Azure Cosmos DB rozhraní API Cassandra
description: Získání ukázek Azure PowerShell k provádění běžných úloh v Azure Cosmos DB rozhraní API Cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 15e0f221141062b2239b588889036849d3b1d91e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505201"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-cassandra-api"></a>Azure PowerShell ukázky pro Azure Cosmos DB rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

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

## <a name="cassandra-api-samples"></a>Ukázky rozhraní API Cassandra

|Úkol | Popis |
|---|---|
|[Vytvoření účtu, místa a tabulky](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, prostor a tabulku. |
|[Vytvoření účtu, prostoru a tabulky pomocí automatického škálování](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, prostor a tabulku s AutoScale. |
|[Vypsat nebo získat prostory a tabulky](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo získá prostor nebo tabulky. |
|[Operace propustnosti](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti pro prostor klíčů nebo tabulku, mezi které patří Get, Update a migrace mezi autoškálou a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
