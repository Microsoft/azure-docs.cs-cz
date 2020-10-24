---
title: Skript PowerShellu pro změnu priority převzetí služeb při selhání pro účet Azure Cosmos s jednou oblastí zápisu
description: Ukázkový skript Azure PowerShell – Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb při selhání pro účet Azure Cosmos s jednou oblastí zápisu
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 05770ba1d2562ec94d8771495aa66ea69523fd06
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488244"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-with-single-write-region-by-using-powershell"></a>Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb při selhání pro účet Azure Cosmos s jednou oblastí zápisu pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

> [!NOTE]
> Jakákoli změna v oblasti s `failoverPriority=0` triggerem aktivuje ruční převzetí služeb při selhání a dá se provést jenom pro účet konfigurovaný pro ruční převzetí služeb při selhání. Změny ve všech ostatních oblastech jednoduše mění prioritu převzetí služeb při selhání pro účet Cosmos.
> [!NOTE]
> Tato ukázka předvádí použití účtu rozhraní API SQL (Core). Pokud chcete tuto ukázku použít pro jiná rozhraní API, zkopírujte související vlastnosti a aplikujte je na skript specifický pro rozhraní API.

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Zobrazí seznam Cosmos DB účtů nebo získá zadaný Cosmos DB účet. |
| [Update – AzCosmosDBAccountFailoverPriority](/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Aktualizujte pořadí priority převzetí služeb při selhání v oblastech Cosmos DB účtu. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).