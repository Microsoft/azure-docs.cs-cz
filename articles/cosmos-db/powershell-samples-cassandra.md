---
title: Azure PowerShell ukázky pro Azure Cosmos DB-rozhraní API Cassandra
description: Získejte ukázky Azure PowerShell k provádění různých běžných úloh Azure Cosmos DB rozhraní API Cassandra účtů
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: fec25bb3043942605895491952d963ee20ac4078
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563893"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---cassandra-api"></a>Azure PowerShell ukázky pro Azure Cosmos DB-rozhraní API Cassandra

Následující tabulka obsahuje odkazy na ukázkové Azure PowerShell skripty pro Azure Cosmos DB rozhraní API Cassandra.

> [!NOTE]
> Ukázky využívají rutiny [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management. Pravidelně prosím zkontrolujte aktualizace `Az.CosmosDB` .

|Úkol | Popis |
|---|---|
|[Vytvoření účtu, místa a tabulky](scripts/powershell/cassandra/ps-cassandra-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, prostor a tabulku. |
|[Vypsat nebo získat prostory a tabulky](scripts/powershell/cassandra/ps-cassandra-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo získá prostor nebo tabulky. |
|[Získat RU/s](scripts/powershell/cassandra/ps-cassandra-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte RU/s pro prostor klíčů nebo tabulku. |
|[Aktualizace RU/s](scripts/powershell/cassandra/ps-cassandra-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizujte RU/s pro prostor klíčů nebo tabulku. |
|[Aktualizace účtu nebo přidání oblasti](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Přidejte oblast do účtu Cosmos. Lze ji také použít pro úpravu dalších vlastností účtu, ale musí být odděleny od změn v oblastech. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu pro účet Azure Cosmos. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos s povoleným protokolem IP firewall. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/cassandra/powershell-cassandra-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
