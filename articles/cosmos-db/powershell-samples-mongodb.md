---
title: Ukázky Prostředí Azure PowerShell pro Azure Cosmos DB – rozhraní API MongoDB
description: Získejte ukázky Prostředí Azure PowerShell k provádění různých běžných úloh v rozhraní API Služby Azure Cosmos DB pro MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366204"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Ukázky Prostředí Azure PowerShell pro rozhraní API MongoDB služby Azure Cosmos DB

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShell pro Azure Cosmos DB pro rozhraní API MongoDB.

> [!NOTE]
> V současné době můžete vytvořit jenom verzi 3.2 (to `*.documents.azure.com`znamená účty pomocí koncového bodu ve formátu) rozhraní API Azure Cosmos DB pro účty MongoDB pomocí šablon PowerShell, CLI a Resource Manager. Chcete-li vytvořit verzi účtů 3.6, použijte místo toho portál Azure.

> [!NOTE]
> Ukázky používají rutiny správy [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Upozorňujeme, `Az.CosmosDB` že rutiny jsou stále ve verzi Preview a mohou se před vydáním změnit. Pravidelně kontrolujte `Az.CosmosDB` aktualizace.

| | |
|---|---|
|[Vytvoření účtu, databáze a kolekce](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a kolekci. |
|[Seznam nebo získání databází nebo kolekcí](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Seznam nebo získat databázi nebo kolekci. |
|[Získat RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte RU/s pro databázi nebo kolekci. |
|[Aktualizace Ru/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizace RU/s pro databázi nebo kolekci. |
|[Aktualizace účtu nebo přidání oblasti](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Přidejte oblast do účtu Cosmos. Lze také použít k úpravě jiných vlastností účtu, ale musí být odděleny od změn oblastí. |
|[Změna priority převzetí služeb při selhání nebo aktivace převzetí služeb při selhání](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte místní prioritu převzetí služeb při selhání účtu Azure Cosmos nebo aktivuj ruční převzetí služeb při selhání. |
|[Klíče účtů nebo připojovací řetězce](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu účtu Azure Cosmos. |
|[Vytvoření účtu Cosmos pomocí ip firewallu](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos s povolenou ip bránou firewall. |
|||
