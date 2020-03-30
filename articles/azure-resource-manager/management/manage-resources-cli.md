---
title: Správa prostředků – Azure CLI
description: Ke správě prostředků použijte Azure CLI a Azure Resource Manager. Ukazuje, jak nasadit a odstranit prostředky.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: f00c1725201b0e49f80fec64e5d69b375ec7a233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485530"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Správa prostředků Azure pomocí Azure CLI

Zjistěte, jak používat Azure CLI s [Azure Resource Manager](overview.md) ke správě prostředků Azure. Správa skupin prostředků najdete v [tématu Správa skupin prostředků Azure pomocí Azure CLI](manage-resource-groups-cli.md).

Další články o správě zdrojů:

- [Správa prostředků Azure pomocí portálu Azure](manage-resources-portal.md)
- [Správa prostředků Azure pomocí Azure PowerShellu](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Prostředky Azure můžete nasadit přímo pomocí azure cli nebo nasadit šablonu Správce prostředků k vytvoření prostředků Azure.

### <a name="deploy-a-resource"></a>Nasazení prostředku

Následující skript vytvoří účet úložiště.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Nasazení šablony

Následující skript vytvoří nasazení šablony Rychlého startu k vytvoření účtu úložiště. Další informace najdete [v tématu Úvodní příručka: Vytvoření šablon Azure Resource Manager pomocí kódu Visual Studia](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasadit prostředky do skupiny. Další informace naleznete v [tématu Vytvoření skupiny prostředků a nasazení prostředků](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Nasazení prostředků do více předplatných nebo skupin prostředků

Obvykle nasadíte všechny prostředky v šabloně do jedné skupiny prostředků. Existují však scénáře, kde chcete nasadit sadu prostředků společně, ale umístit je do různých skupin prostředků nebo předplatných. Další informace najdete [v tématu Nasazení prostředků Azure do více předplatných nebo skupin prostředků](../templates/cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Odstranění prostředků

Následující skript ukazuje, jak odstranit účet úložiště.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Další informace o tom, jak Azure Resource Manager objednávky odstranění prostředků, najdete v [tématu Odstranění skupiny prostředků Azure Resource Manager](delete-resource-group.md).

## <a name="move-resources"></a>Přesunutí prostředků

Následující skript ukazuje, jak odebrat účet úložiště z jedné skupiny prostředků do jiné skupiny prostředků.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Uzamčení prostředků

Uzamčení zabrání ostatním uživatelům ve vaší organizaci v náhodném odstranění nebo úpravě kritických prostředků, jako je předplatné Azure, skupina prostředků nebo prostředek. 

Následující skript uzamkne účet úložiště, takže účet nelze odstranit.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Následující skript získá všechny zámky pro účet úložiště:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Následující skript odstraní zámek účtu úložiště:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](lock-resources.md).

## <a name="tag-resources"></a>Označení prostředků

Označování pomáhá logicky organizovat skupinu prostředků a prostředky. Další informace najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Správa přístupu k prostředkům

[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md) je způsob, jakým spravujete přístup k prostředkům v Azure. Další informace naleznete [v tématu Správa přístupu pomocí RBAC a Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Další kroky

- Pokud se chcete naučit Správce prostředků Azure, přečtěte si [přehled správce prostředků Azure](overview.md).
- Syntaxe syntaxe šablony Správce prostředků najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](../templates/template-syntax.md).
- Chcete-li se dozvědět, jak vyvíjet šablony, podívejte se na [podrobné výukové programy](/azure/azure-resource-manager/).
- Pokud chcete zobrazit schémata šablon Azure Resource Manager, přečtěte si [odkaz na šablonu](/azure/templates/).
