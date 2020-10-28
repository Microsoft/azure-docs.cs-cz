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
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: ca74c5f54cfb513f4611fafc40d34378707ff949
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746498"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Rychlý Start: vytvoření profilu Azure CDN a šablony koncového bodu – ARM

Začínáme s Azure Content Delivery Network (CDN) pomocí šablony Azure Resource Manager (šablona ARM). Šablona nasadí profil a koncový bod.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure** . Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Tato šablona je nakonfigurovaná tak, aby vytvořila:

* Profil
* Koncový bod

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

V této šabloně je definovaný jeden prostředek Azure:

* **[Microsoft. CDN/profily](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Nasazení šablony

### <a name="azure-cli"></a>Azure CLI

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

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portál

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V levém podokně vyberte **skupiny prostředků** .

3. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je **myResourceGroupCDN**

4. Ověřte, že se ve skupině prostředků vytvořily tyto prostředky:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN skupina prostředků" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="azure-cli"></a>Azure CLI

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portál

Pokud už je nepotřebujete, odstraňte skupinu prostředků, profil CDN a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupCDN** , která obsahuje profil a koncový bod CDN, a pak vyberte **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:

* Profil CDN
* Koncový bod

Další informace o Azure CDN a Azure Resource Manager najdete dál v článcích níže.

* Přečtěte si [přehled Azure CDN](cdn-overview.md)
* Další informace o [Azure Resource Manageru](../azure-resource-manager/management/overview.md)
