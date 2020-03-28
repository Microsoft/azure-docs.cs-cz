---
title: Skript PowerShellu pro aktualizaci výchozí úrovně konzistence na účtu Azure Cosmos
description: Ukázka skriptu Azure PowerShellu – aktualizace výchozí úrovně konzistence na účtu Azure Cosmos DB pomocí PowerShellu
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: d3df2e91624f9b5d82d534a1d525fa6866f1a489
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366088"
---
# <a name="update-the-regions-on-an-azure-cosmos-db-account-using-powershell"></a>Aktualizace oblastí na účtu Azure Cosmos DB pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

> [!NOTE]
> Nelze změnit oblasti a změnit jiné vlastnosti účtu Cosmos ve stejné operaci. Ty musí být provedeny jako dvě samostatné operace.
> [!NOTE]
> Tato ukázka ukazuje pomocí účtu rozhraní SQL API. Chcete-li tuto ukázku použít pro jiná rozhraní API, zkopírujte související vlastnosti a použijte skript specifický pro rozhraní API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Update an Azure Cosmos DB account")]

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
| [Aktualizace AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Aktualizujte účet Cosmos DB. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).
