---
title: Skript Prostředí PowerShell pro vytvoření kontejneru Azure Cosmos DB s velkým klíčem oddílu
description: Ukázka skriptu Azure PowerShell – vytvoření kontejneru s velkým klíčem oddílu v účtu Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 7746154fe89b655d09345a2de655fa85a0d4cbae
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365938"
---
# <a name="create-a-container-with-a-large-partition-key-in-an-azure-cosmos-db-account-using-powershell"></a>Vytvoření kontejneru s velkým klíčem oddílu v účtu Azure Cosmos DB pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-large-partition-key.ps1 "Create a container with a large partition key in an Azure Cosmos account")]

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
| [Nový účet AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Vytvoří nový účet Cosmos DB. |
| [Set-AzCosmosDBSqlDatabáze](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Vytvoří novou nebo aktualizuje existující databázi COSMOS DB SQL Database. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Vytvoří nový nebo aktualizuje existující kontejner SQL Cosmos DB. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).