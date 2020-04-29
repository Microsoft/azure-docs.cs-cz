---
title: Azure PowerShell ukázky pro Azure Cosmos DB-rozhraní API Cassandra
description: Získejte ukázky Azure PowerShell k provádění různých běžných úloh Azure Cosmos DB rozhraní API Cassandra účtů
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f09088fc001fd8c77a330938d261426528b214cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80365748"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---cassandra-api"></a>Azure PowerShell ukázky pro Azure Cosmos DB-rozhraní API Cassandra

Následující tabulka obsahuje odkazy na ukázkové Azure PowerShell skripty pro Azure Cosmos DB rozhraní API Cassandra.

> [!NOTE]
> Ukázky využívají rutiny [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management. Počítejte s tím, `Az.CosmosDB` že rutiny jsou stále ve verzi Preview a můžou se před vydáním změnit. `Az.CosmosDB` Pravidelně prosím zkontrolujte aktualizace.

| | |
|---|---|
|[Vytvoření účtu, místa a tabulky](scripts/powershell/cassandra/ps-cassandra-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, prostor a tabulku. |
|[Vypsat nebo získat prostory a tabulky](scripts/powershell/cassandra/ps-cassandra-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo získá prostor nebo tabulky. |
|[Získat RU/s](scripts/powershell/cassandra/ps-cassandra-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte RU/s pro prostor klíčů nebo tabulku. |
|[Aktualizace RU/s](scripts/powershell/cassandra/ps-cassandra-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizujte RU/s pro prostor klíčů nebo tabulku. |
|[Aktualizace účtu nebo přidání oblasti](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Přidejte oblast do účtu Cosmos. Lze ji také použít pro úpravu dalších vlastností účtu, ale musí být odděleny od změn v oblastech. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu pro účet Azure Cosmos. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos s povoleným protokolem IP firewall. |
|||
