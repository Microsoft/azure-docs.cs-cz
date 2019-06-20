---
title: Správa prostředků Azure pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Ke správě prostředků pomocí rozhraní příkazového řádku Azure a Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 3293d08f63cf573c7833fae5dd15bfe3119fd6c7
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206514"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Správa prostředků Azure pomocí Azure CLI

Další informace o použití Azure CLI využijte [Azure Resource Manageru](resource-group-overview.md) ke správě vašich prostředků Azure. Skupiny prostředků, přečtěte si téma [skupin prostředků Azure spravovat pomocí rozhraní příkazového řádku Azure](./manage-resource-groups-cli.md).

Další články o správě prostředků:

- [Správa prostředků Azure pomocí webu Azure portal](./manage-resources-portal.md)
- [Správa prostředků Azure pomocí Azure Powershellu](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Můžete nasazujte prostředky Azure přímo pomocí Azure Powershellu nebo nasazovat šablony Resource Manageru k vytvoření prostředků Azure.

### <a name="deploy-a-resource"></a>Nasazení prostředku

Tento skript vytvoří účet úložiště.

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

Tento skript vytvoří nasazení šablony rychlý start k vytvoření účtu úložiště. Další informace najdete v tématu [rychlý start: Vytváření šablon Azure Resource Manageru pomocí Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasazení prostředků do skupiny. Další informace najdete v tématu [vytvořte skupinu prostředků a nasazení prostředků](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Nasazení prostředků do více předplatných nebo skupinách prostředků

Zpravidla nasazujete, všechny prostředky ve vaší šabloně do jedné skupiny prostředků. Existují ale scénáře, ve které chcete nasadit sadu prostředků společně, ale je umístit do různých skupin prostředků nebo předplatných. Další informace najdete v tématu [prostředků nasazení Azure k několika předplatných nebo skupinách prostředků](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Odstranění prostředků

Tento skript ukazuje, jak odstranit účet úložiště.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Další informace o tom, jak Azure Resource Manageru orders odstranění prostředků najdete v tématu [odstranění skupiny prostředků Azure Resource Manageru](./resource-group-delete.md).

## <a name="move-resources"></a>Přesunutí prostředků

Tento skript ukazuje, jak odebrat účet úložiště z jedné skupiny prostředků do jiné skupiny prostředků.

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

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

## <a name="lock-resources"></a>Uzamčení prostředků

Zamknutí zabrání ostatním uživatelům ve vaší organizaci omylem odstranit nebo upravit důležité prostředky, jako je předplatné Azure, skupinu prostředků nebo prostředek. 

Následující skript zamkne účet úložiště, aby účet nelze odstranit.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Následující skript načte všech zámků pro účet úložiště:

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

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Označení prostředků

Označování pomáhá logicky uspořádání skupinu prostředků a prostředky. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Správa přístupu k prostředkům

[Řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) je způsob správy přístupu k prostředkům v Azure. Další informace najdete v tématu [správě přístupu pomocí RBAC a rozhraní příkazového řádku Azure](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Další postup

- Další Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](./resource-group-overview.md).
- Seznamte se se syntaxí šablony Resource Manageru, najdete v článku [Princip struktury a syntaxe šablon Azure Resource Manageru](./resource-group-authoring-templates.md).
- Zjistěte, jak vyvíjet šablony, najdete v článku [podrobné kurzy](/azure/azure-resource-manager/).
- Schémata šablon Azure Resource Manageru najdete v tématu [referenčními informacemi k šablonám](/azure/templates/).
