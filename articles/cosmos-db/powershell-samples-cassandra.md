---
title: Ukázky Prostředí Azure PowerShell pro Azure Cosmos DB – rozhraní CASSANDRO API
description: Získání ukázek Prostředí Azure PowerShell k provádění různých běžných úloh v účtech rozhraní API Azure Cosmos DB Cassandra
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f09088fc001fd8c77a330938d261426528b214cd
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365748"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---cassandra-api"></a>Ukázky Prostředí Azure PowerShell pro Azure Cosmos DB – rozhraní CASSANDRO API

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShell pro Azure Cosmos DB pro rozhraní CASSAndRo API.

> [!NOTE]
> Ukázky používají rutiny správy [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Upozorňujeme, `Az.CosmosDB` že rutiny jsou stále ve verzi Preview a mohou se před vydáním změnit. Pravidelně kontrolujte `Az.CosmosDB` aktualizace.

| | |
|---|---|
|[Vytvoření účtu, klíčového prostoru a tabulky](scripts/powershell/cassandra/ps-cassandra-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, keyspace a tabulka. |
|[Seznam nebo získání klíčových prostorů nebo tabulek](scripts/powershell/cassandra/ps-cassandra-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Seznam nebo získat keyspaces nebo tabulky. |
|[Získat RU/s](scripts/powershell/cassandra/ps-cassandra-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte RU/s pro keyspace nebo tabulky. |
|[Aktualizace Ru/s](scripts/powershell/cassandra/ps-cassandra-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizace RU/s pro keyspace nebo tabulka. |
|[Aktualizace účtu nebo přidání oblasti](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Přidejte oblast do účtu Cosmos. Lze také použít k úpravě jiných vlastností účtu, ale musí být odděleny od změn oblastí. |
|[Změna priority převzetí služeb při selhání nebo aktivace převzetí služeb při selhání](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte místní prioritu převzetí služeb při selhání účtu Azure Cosmos nebo aktivuj ruční převzetí služeb při selhání. |
|[Klíče účtů nebo připojovací řetězce](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu účtu Azure Cosmos. |
|[Vytvoření účtu Cosmos pomocí ip firewallu](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos s povolenou ip bránou firewall. |
|||
