---
title: Skript PowerShellu pro změnu priority převzetí služeb při selhání pro účet Azure Cosmos
description: Ukázka skriptu Azure PowerShell – Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb při selhání pro účet Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 6a742486918e5134a73256ef6c7490a823f14335
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441509"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-using-powershell"></a>Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb při selhání pro účet Azure Cosmos pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

> [!NOTE]
> Jakákoli změna v oblasti s `failoverPriority=0` aktivuje ruční převzetí služeb při selhání a dá se provést jenom pro účet konfigurovaný pro ruční převzetí služeb při selhání. Změny ve všech ostatních oblastech jednoduše mění prioritu převzetí služeb při selhání pro účet Cosmos.
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
|**Prostředky Azure**| |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Vyvolá akci u prostředku. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).
