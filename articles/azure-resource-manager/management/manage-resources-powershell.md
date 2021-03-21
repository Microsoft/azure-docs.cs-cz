---
title: Správa prostředků – Azure PowerShell
description: Ke správě prostředků použijte Azure PowerShell a Azure Resource Manager. Ukazuje, jak nasadit a odstranit prostředky.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: ce3b1d9f8d01e722e4ec5078d92ef4148072da25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185738"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Správa prostředků Azure pomocí Azure PowerShell

Naučte se používat Azure PowerShell s [Azure Resource Manager](overview.md) ke správě prostředků Azure. Informace o správě skupin prostředků najdete v tématu [Správa skupin prostředků Azure pomocí Azure PowerShell](manage-resource-groups-powershell.md).

Další články o správě prostředků:

- [Správa prostředků Azure pomocí Azure Portal](manage-resources-portal.md)
- [Správa prostředků Azure pomocí Azure CLI](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Prostředky Azure můžete nasadit přímo pomocí Azure PowerShell, nebo můžete nasadit šablonu Správce prostředků a vytvořit prostředky Azure.

### <a name="deploy-a-resource"></a>Nasazení prostředku

Následující skript vytvoří účet úložiště.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Nasazení šablony

Následující skript vytvoří nasazení šablony pro rychlý Start pro vytvoření účtu úložiště. Další informace najdete v tématu [rychlý Start: vytváření Azure Resource Manager šablon pomocí Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasadit prostředky do skupiny. Další informace najdete v tématu [Vytvoření skupiny prostředků a nasazení prostředků](../templates/deploy-to-subscription.md#resource-groups).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Nasazení prostředků do více předplatných nebo skupin prostředků

Obvykle se všechny prostředky v šabloně nasazují do jedné skupiny prostředků. Existují však situace, kdy chcete nasadit sadu prostředků dohromady, ale umístit je do různých skupin prostředků nebo předplatných. Další informace najdete v tématu [nasazení prostředků Azure do několika předplatných nebo skupin prostředků](../templates/deploy-to-resource-group.md).

## <a name="delete-resources"></a>Odstranění prostředků

Následující skript ukazuje, jak odstranit účet úložiště.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Další informace o tom, jak Azure Resource Manager objednává odstranění prostředků, najdete v tématu [Azure Resource Manager odstranění skupiny prostředků](delete-resource-group.md).

## <a name="move-resources"></a>Přesunutí prostředků

Následující skript ukazuje, jak odebrat účet úložiště z jedné skupiny prostředků do jiné skupiny prostředků.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Uzamčení prostředků

Uzamykání brání jiným uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků, jako je například předplatné Azure, skupina prostředků nebo prostředek. 

Následující skript zamkne účet úložiště, takže účet nejde odstranit.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Následující skript získá všechny zámky pro účet úložiště:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Následující skript odstraní zámek účtu úložiště:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](lock-resources.md).

## <a name="tag-resources"></a>Označení prostředků

Označení pomáhá organizovat skupinu prostředků a prostředky logicky. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md#powershell).

## <a name="manage-access-to-resources"></a>Správa přístupu k prostředkům

[Řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) je způsob, jakým můžete spravovat přístup k prostředkům v Azure. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Další kroky

- Informace o Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](overview.md).
- Další informace o syntaxi šablon Správce prostředků naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](../templates/template-syntax.md).
- Další informace o vývoji šablon najdete v [podrobných kurzech](../index.yml).
- Chcete-li zobrazit schémata šablon Azure Resource Manager, přečtěte si téma [reference šablony](/azure/templates/).