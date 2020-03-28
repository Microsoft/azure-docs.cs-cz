---
title: Skript Prostředí PowerShell pro změnu priority převzetí služeb při selhání pro účet Azure Cosmos s jedním hlavním serverem
description: Ukázka skriptu Azure PowerShell – změna priority převzetí služeb při selhání nebo aktivace převzetí služeb při selhání pro jednohlavní účet služby Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: a81938675e72d9ec3a18c920121951e38580b91e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366109"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-db-single-master-account-using-powershell"></a>Změna priority převzetí služeb při selhání nebo aktivace převzetí služeb při selhání pro jednohlavní účet Služby Azure Cosmos DB pomocí Prostředí PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

> [!NOTE]
> Jakákoli změna oblasti `failoverPriority=0` s aktivací ručního převzetí služeb při selhání a lze ji provést pouze u účtu nakonfigurovaného pro ruční převzetí služeb při selhání. Změny ve všech ostatních oblastech jednoduše změní prioritu převzetí služeb při selhání pro účet Cosmos.
> [!NOTE]
> Tato ukázka ukazuje pomocí účtu rozhraní API SQL (Core). Chcete-li tuto ukázku použít pro jiná rozhraní API, zkopírujte související vlastnosti a použijte skript specifický pro rozhraní API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

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
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Seznam účtů Cosmos DB nebo získá zadaný účet Cosmos DB. |
| [Aktualizace-AzCosmosDBAccountFailoverPriorit](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Aktualizujte pořadí priorit převzetí služeb při selhání v oblastech účtu Cosmos DB. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).
