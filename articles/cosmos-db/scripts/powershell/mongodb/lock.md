---
title: Skript PowerShellu pro vytvoření zámku prostředků pro databázi a kolekci rozhraní Azure Cosmos MongoDB API
description: Vytvoření zámku prostředků pro databázi a kolekci rozhraní API Azure Cosmos MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 0d7b5037e3adb4a8710d117b98e93536e8a278df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677910"
---
# <a name="create-a-resource-lock-for-azure-cosmos-mongodb-api-database-and-collection-using-azure-powershell"></a>Vytvoření zámku prostředků pro databázi a kolekci rozhraní Azure Cosmos MongoDB pomocí Azure PowerShell
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Tato ukázka vyžaduje Azure PowerShell az 5.4.0 nebo novější. Spusťte `Get-Module -ListAvailable Az` , chcete-li zjistit, které verze jsou nainstalovány.
Pokud potřebujete instalaci, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Spusťte [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a přihlaste se k Azure.

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří se připojují pomocí MongoDB SDK, Mongoshell, jakýchkoli nástrojů nebo webu Azure Portal, pokud není účet Cosmos DB nejdříve zamčený s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-lock.ps1 "Create, list, and remove resource locks")]

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
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Vytvoří zámek prostředku. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Získá zámek prostředku nebo vypíše zámky prostředků. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Odebere zámek prostředku. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShell najdete v [dokumentaci k Azure PowerShell](/powershell/).