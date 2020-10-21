---
title: Skript prostředí PowerShell pro vytvoření účtu Azure Cosmos DB s bránou firewall protokolu IP
description: Ukázka skriptu Azure PowerShell – vytvoření účtu Azure Cosmos DB pomocí brány firewall protokolu IP
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 143b79a0e65d842daae84fb7565c0caa64e3b68a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276173"
---
# <a name="create-an-azure-cosmos-db-account-with-ip-firewall"></a>Vytvoření účtu Azure Cosmos DB pomocí brány firewall protokolu IP

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

> [!NOTE]
> Tato ukázka předvádí použití účtu rozhraní API SQL (Core). Pokud chcete tuto ukázku použít pro jiná rozhraní API, zkopírujte související vlastnosti a aplikujte je na skript specifický pro rozhraní API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-firewall-create.ps1 "Create an Azure Cosmos account with IP Firewall")]

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
| [New-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Vytvoří nový účet Cosmos DB. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).
