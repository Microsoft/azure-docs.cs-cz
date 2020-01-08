---
title: Skript PowerShellu pro aktualizaci účtu Azure Cosmos
description: Ukázka skriptu Azure PowerShell – aktualizace účtu Azure Cosmos nebo změna oblastí
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 075c33b0818aa3ec8b16158f538ae302446ff5f7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445030"
---
# <a name="update-an-azure-cosmos-account-or-modify-regions-using-powershell"></a>Aktualizace účtu Azure Cosmos nebo změna oblastí pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

> [!NOTE]
> V rámci jedné operace nelze upravovat oblasti a měnit jiné vlastnosti účtu Cosmos. Ty je třeba provést jako dvě samostatné operace.
> [!NOTE]
> Tato ukázka předvádí použití účtu rozhraní API SQL (Core). Pokud chcete tuto ukázku použít pro jiná rozhraní API, zkopírujte související vlastnosti a aplikujte je na skript specifický pro rozhraní API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Add a region to an Azure Cosmos account")]

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
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Vytvořte prostředek. |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Aktualizuje prostředek. |
|**Skupiny prostředků Azure**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).
