---
title: Skript PowerShellu pro vytvoření Azure Cosmos DB databáze a kontejneru SQL API s využitím automatického škálování
description: Azure PowerShell skriptu – Azure Cosmos DB vytvoření databáze a kontejneru SQL API pomocí automatického škálování
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 42407f3ebb0b7f3a726c0031a0af5effc338c880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677981"
---
# <a name="create-a-database-and-container-with-autoscale-for-azure-cosmos-db---sql-api"></a>Vytvoření databáze a kontejneru pomocí automatického škálování pro Azure Cosmos DB rozhraní API SQL
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Tato ukázka vyžaduje Azure PowerShell az 5.4.0 nebo novější. Spusťte `Get-Module -ListAvailable Az` , chcete-li zjistit, které verze jsou nainstalovány.
Pokud potřebujete instalaci, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Spusťte [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a přihlaste se k Azure.

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří účet Cosmos pro rozhraní API jádra (SQL) ve dvou oblastech s konzistencí úrovně relace, databází a kontejnerem s propustností automatického škálování a výchozími zásadami indexování.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-autoscale.ps1 "Create an account, database, and container with autoscale for Core (SQL) API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Vytvoří účet Cosmos DB. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Vytvoří SQL Database Cosmos DB. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Vytvoří nový Cosmos DB kontejner SQL. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).
