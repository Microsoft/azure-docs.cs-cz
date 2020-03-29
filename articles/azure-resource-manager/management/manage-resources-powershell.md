---
title: Správa prostředků – Azure PowerShell
description: Ke správě prostředků použijte Azure PowerShell a Azure Resource Manager. Ukazuje, jak nasadit a odstranit prostředky.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: de6f24ea66ef41b5ee4bfdda5948de9639f10a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485400"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Správa prostředků Azure pomocí Azure PowerShellu

Přečtěte si, jak používat Azure PowerShell ve [Správci prostředků Azure](overview.md) ke správě prostředků Azure. Správa skupin prostředků najdete v [tématu Správa skupin prostředků Azure pomocí Azure PowerShellu](manage-resource-groups-powershell.md).

Další články o správě zdrojů:

- [Správa prostředků Azure pomocí portálu Azure](manage-resources-portal.md)
- [Správa prostředků Azure pomocí Azure CLI](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Prostředky Azure můžete nasadit přímo pomocí Azure PowerShellu nebo nasadit šablonu Správce prostředků k vytvoření prostředků Azure.

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

Následující skript vytvoří nasazení šablony Rychlého startu k vytvoření účtu úložiště. Další informace najdete [v tématu Úvodní příručka: Vytvoření šablon Azure Resource Manager pomocí kódu Visual Studia](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasadit prostředky do skupiny. Další informace naleznete v [tématu Vytvoření skupiny prostředků a nasazení prostředků](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Nasazení prostředků do více předplatných nebo skupin prostředků

Obvykle nasadíte všechny prostředky v šabloně do jedné skupiny prostředků. Existují však scénáře, kde chcete nasadit sadu prostředků společně, ale umístit je do různých skupin prostředků nebo předplatných. Další informace najdete [v tématu Nasazení prostředků Azure do více předplatných nebo skupin prostředků](../templates/cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Odstranění prostředků

Následující skript ukazuje, jak odstranit účet úložiště.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Další informace o tom, jak Azure Resource Manager objednávky odstranění prostředků, najdete v [tématu Odstranění skupiny prostředků Azure Resource Manager](delete-resource-group.md).

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

Uzamčení zabrání ostatním uživatelům ve vaší organizaci v náhodném odstranění nebo úpravě kritických prostředků, jako je předplatné Azure, skupina prostředků nebo prostředek. 

Následující skript uzamkne účet úložiště, takže účet nelze odstranit.

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

Označování pomáhá logicky organizovat skupinu prostředků a prostředky. Další informace najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md#powershell).

## <a name="manage-access-to-resources"></a>Správa přístupu k prostředkům

[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md) je způsob, jakým spravujete přístup k prostředkům v Azure. Další informace najdete [v tématu Správa přístupu pomocí RBAC a Azure PowerShell .](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Další kroky

- Pokud se chcete naučit Správce prostředků Azure, přečtěte si [přehled správce prostředků Azure](overview.md).
- Syntaxe syntaxe šablony Správce prostředků najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](../templates/template-syntax.md).
- Chcete-li se dozvědět, jak vyvíjet šablony, podívejte se na [podrobné výukové programy](/azure/azure-resource-manager/).
- Pokud chcete zobrazit schémata šablon Azure Resource Manager, přečtěte si [odkaz na šablonu](/azure/templates/).
