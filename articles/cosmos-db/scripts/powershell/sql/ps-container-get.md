---
title: Prostředí Azure PowerShell skriptu – získání vlastnosti kontejneru v účtu služby Azure Cosmos
description: Skript Azure Powershellu ukázkový – získání vlastnosti kontejneru v účtu služby Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/086/2019
ms.author: mjbrown
ms.openlocfilehash: 09b773d1939792b97e9fd0ba21bcfdcdca4b8b2e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078202"
---
# <a name="get-the-properties-of-a-container-in-an-azure-cosmos-account-using-powershell"></a>Získá vlastnosti kontejneru v účtu služby Azure Cosmos pomocí Powershellu

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-get.ps1 "Get the properties of a container in an Azure Cosmos account")]

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
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Získá prostředek. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).