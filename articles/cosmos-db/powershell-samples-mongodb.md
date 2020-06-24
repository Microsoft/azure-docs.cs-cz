---
title: Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB MongoDB
description: Získání ukázek Azure PowerShell k provádění různých běžných úloh v rozhraní Azure Cosmos DB API pro MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 368883a7eded17180a4a4259d452be09ebd221d9
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262442"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Ukázky Azure PowerShell pro rozhraní API pro Azure Cosmos DB MongoDB

Následující tabulka obsahuje odkazy na ukázkové Azure PowerShell skripty pro Azure Cosmos DB rozhraní API pro MongoDB.

> [!NOTE]
> V současné době můžete pomocí šablon PowerShellu, CLI a Správce prostředků vytvořit jenom 3,2 verze (tj. účty používající koncový bod ve formátu `*.documents.azure.com` ) Azure Cosmos DB API pro účty MongoDB. Pokud chcete vytvořit 3,6 verze účtů, použijte místo toho Azure Portal.

> [!NOTE]
> Ukázky využívají rutiny [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management. Pravidelně prosím zkontrolujte aktualizace `Az.CosmosDB` .

| | |
|---|---|
|[Vytvoření účtu, databáze a kolekce](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a kolekci. |
|[Vypsat nebo získat databáze nebo kolekce](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo kolekci. |
|[Získat RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte RU/s pro databázi nebo kolekci. |
|[Aktualizace RU/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizujte RU/s pro databázi nebo kolekci. |
|[Aktualizace účtu nebo přidání oblasti](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Přidejte oblast do účtu Cosmos. Lze ji také použít pro úpravu dalších vlastností účtu, ale musí být odděleny od změn v oblastech. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu pro účet Azure Cosmos. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos s povoleným protokolem IP firewall. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/mongodb/powershell-mongodb-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
