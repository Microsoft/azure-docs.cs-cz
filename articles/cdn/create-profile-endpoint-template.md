---
title: 'Rychlý Start: vytvoření profilu a koncového bodu – Správce prostředků šablony'
titleSuffix: Azure Content Delivery Network
description: Naučte se vytvořit profil Content Delivery Network Azure a koncový bod a Správce prostředků šablonu.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: bdb30fed4dadef84fe012c11893661b8d9d70325
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483036"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---resource-manager-template"></a>Rychlý Start: vytvoření profilu Azure CDN a koncového bodu – šablona Správce prostředků

Začněte s Azure CDN pomocí Azure Resource Manager šablony.  Tato šablona nasadí profil a koncový bod.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-cdn-profile-and-endpoint"></a>Vytvořit profil a koncový bod CDN

Tato šablona je nakonfigurovaná tak, aby vytvořila:

* Profil
* Koncový bod

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json) .

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json" range="1-125" highlight="46-117":::


V této šabloně je definovaný jeden prostředek Azure:

**Microsoft. CDN**

* **[Microsoft. CDN/profily](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

### <a name="deploy-the-template"></a>Nasazení šablony

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V levém podokně vyberte **skupiny prostředků** .

3. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je **myResourceGroupCDN**

4. Ověřte, že se ve skupině prostředků vytvořily tyto prostředky:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN skupina prostředků" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

**Azure CLI**

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

**Azure PowerShell**

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

**Azure Portal**

Pokud už je nepotřebujete, odstraňte skupinu prostředků, profil CDN a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupCDN** , která obsahuje profil a koncový bod CDN, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:

* Profil CDN
* Koncový bod

Další informace o Azure CDN a Azure Resource Manager najdete dál v článcích níže.

* Přečtěte si [přehled Azure CDN](cdn-overview.md)
* Další informace o [Azure Resource Manageru](../azure-resource-manager/management/overview.md)