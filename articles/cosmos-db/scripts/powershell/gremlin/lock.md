---
title: Skript PowerShellu pro vytvoření zámku prostředků pro databázi a graf Azure Cosmos Gremlin API
description: Vytvoření zámku prostředků pro databázi a graf rozhraní API Azure Cosmos Gremlin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 6e119fe2cc54ce38dcb8ac2d0c711056dc7d77ce
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280643"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-powershell"></a>Vytvoření zámku prostředků pro databázi a graf rozhraní API Azure Cosmos Gremlin pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří se připojují pomocí Gremlin SDK nebo webu Azure Portal, pokud není účet Cosmos DB nejdříve zamčený s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|**Prostředek Azure**| |
| [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) | Vytvoří zámek prostředku. |
| [Get-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcelock) | Získá zámek prostředku nebo vypíše zámky prostředků. |
| [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) | Odebere zámek prostředku. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShell najdete v [dokumentaci k Azure PowerShell](https://docs.microsoft.com/powershell/).
