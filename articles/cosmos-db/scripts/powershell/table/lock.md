---
title: Skript PowerShellu pro vytvoření zámku prostředků pro Azure Cosmos rozhraní API pro tabulky Table
description: Vytvoření zámku prostředků pro službu Azure Cosmos rozhraní API pro tabulky Table
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: a56846e3bfbc9e5392c509ff1f484b8109ce9eac
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098786"
---
# <a name="create-a-resource-lock-for-azure-cosmos-table-api-table-using-azure-powershell"></a>Vytvoření zámku prostředků pro Azure Cosmos rozhraní API pro tabulky Table pomocí Azure PowerShell
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-PowerShell-install](../../../../../includes/sample-PowerShell-install-no-ssh.md)]

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří se připojují pomocí Cosmos DB SDK, všech nástrojů, které se připojují prostřednictvím klíčů účtu nebo webu Azure Portal, pokud není účet Cosmos DB nejdříve uzamčen s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Ukázkový skript

[!code-PowerShell[main](../../../../../PowerShell_scripts/cosmosdb/table/ps-table-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```PowerShell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|**Prostředek Azure**| |
| [New-AzResourceLock](/PowerShell/module/az.resources/new-azresourcelock) | Vytvoří zámek prostředku. |
| [Get-AzResourceLock](/PowerShell/module/az.resources/get-azresourcelock) | Získá zámek prostředku nebo vypíše zámky prostředků. |
| [Remove-AzResourceLock](/PowerShell/module/az.resources/remove-azresourcelock) | Odebere zámek prostředku. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShell najdete v [dokumentaci k Azure PowerShell](/PowerShell/).