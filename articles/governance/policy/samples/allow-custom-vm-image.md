---
title: Ukázkový – povolit vlastní image virtuálního počítače ze skupiny prostředků
description: Tato ukázková zásada vyžaduje, aby vlastní image pocházely ze schválené skupiny prostředků.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: c1afedf9d3c3ebb68d4c5344cd8795851e2bd10a
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242052"
---
# <a name="allow-custom-vm-image-from-a-resource-group"></a>Povolení vlastní image virtuálního počítače ze skupiny prostředků

Tato ukázková zásada vyžaduje, aby vlastní image pocházely ze schválené skupiny prostředků. Zadejte název schválené skupiny prostředků.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

[!code-json[main](../../../../policy-templates/samples/compute/custom-image-from-rg/azurepolicy.json "Allow custom VM image from a Resource Group")]

K nasazení této šablony můžete použít [Azure Portal](#deploy-with-the-portal) s [PowerShellem](#deploy-with-powershell) nebo s [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

[![Nasazení do Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fcustom-image-from-rg%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "custom-image-from-rg" -DisplayName "Allow custom VM image from a Resource Group" -description "This policy allows only usage of images from a resource group" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -resourceGroupName <Resource Group Name> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Vyčištění nasazení PowerShellu

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'custom-image-from-rg' --display-name 'Allow custom VM image from a Resource Group' --description 'This policy allows only usage of images from a resource group' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "custom-image-from-rg"
```

### <a name="clean-up-azure-cli-deployment"></a>Vymazání nasazení Azure CLI

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

- Další ukázky najdete v [ukázkách pro Azure Policy](index.md).