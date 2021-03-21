---
title: Operace PowerShellových skriptů pro služby propustnosti (RU/s) pro Azure Cosmos DB rozhraní API pro tabulky
description: Operace PowerShellových skriptů pro služby propustnosti (RU/s) pro Azure Cosmos DB rozhraní API pro tabulky
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 061bd06c0fa54497d8f00db0e5d4a808e949c2d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679139"
---
# <a name="throughput-rus-operations-with-powershell-for-a-table-for-azure-cosmos-db---table-api"></a>Operace propustnosti (RU/s) s PowerShellem pro tabulku Azure Cosmos DB-rozhraní API pro tabulky
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Tato ukázka vyžaduje Azure PowerShell az 5.4.0 nebo novější. Spusťte `Get-Module -ListAvailable Az` , chcete-li zjistit, které verze jsou nainstalovány.
Pokud potřebujete instalaci, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Spusťte [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a přihlaste se k Azure.

## <a name="get-throughput"></a>Zjištění propustnosti

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-get.ps1 "Get throughput on a table for Table API")]

## <a name="update-throughput"></a>Aktualizace propustnosti

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-update.ps1 "Update throughput on a table for Table API")]

## <a name="migrate-throughput"></a>Migrace propustnosti

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a table for Table API")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbtablethroughput) | Získá hodnotu propustnosti zadané rozhraní API pro tabulky tabulky. |
| [Update – AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbsqldatabasethroughput) | Aktualizuje hodnotu propustnosti rozhraní API pro tabulky tabulky. |
| [Invoke – AzCosmosDBTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbtablethroughputmigration) | Migrace propustnosti rozhraní API pro tabulky tabulky |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).