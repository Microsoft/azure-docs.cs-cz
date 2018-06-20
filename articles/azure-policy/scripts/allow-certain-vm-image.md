---
title: Ukázka kódu json pro Azure Policy – povolení jenom určité image platformy virtuálního počítače | Microsoft Docs
description: Tato ukázková zásada json vyžaduje, aby virtuální počítače používaly konkrétní verzi UbuntuServeru.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: a3fd4f6cd9df54d77fca20a941019ed928c4f890
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602291"
---
# <a name="only-allow-a-certain-vm-platform-image"></a>Povolení jenom určité image platformy virtuálního počítače

Vyžaduje, aby virtuální počítače používaly konkrétní verzi UbuntuServeru.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

[!code-json[main](../../../policy-templates/samples/compute/platform-image-policy/azurepolicy.json "Only allow a certain VM platform image")]

K nasazení této šablony můžete použít [Azure Portal](#deploy-with-the-portal) s [PowerShellem](#deploy-with-powershell) nebo s [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

[![Nasazení do Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fplatform-image-policy%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "platform-image-policy" -DisplayName "Only allow a certain VM platform image" -description "This policy ensures that only UbuntuServer, Canonical is allowed from the image repository" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/platform-image-policy/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/platform-image-policy/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Vyčištění nasazení PowerShellu

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'platform-image-policy' --display-name 'Only allow a certain VM platform image' --description 'This policy ensures that only UbuntuServer, Canonical is allowed from the image repository' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/platform-image-policy/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/platform-image-policy/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "platform-image-policy"
```

### <a name="clean-up-azure-cli-deployment"></a>Vymazání nasazení Azure CLI

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

- Další příklady najdete v [Ukázkách Azure Policy](../json-samples.md).