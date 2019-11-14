---
title: Ukázka – vytvoření virtuálního počítače pomocí spravovaného disku
description: Tato ukázková definice zásad vyžaduje, aby virtuální počítače používaly spravované disky.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 0ad21e255e771cd94c6f2eb794e20dc9b67f49d5
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069788"
---
# <a name="sample---create-vm-using-managed-disk"></a>Ukázka – vytvoření virtuálního počítače pomocí spravovaného disku

Vyžaduje, aby virtuální počítače používaly spravované disky.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

[!code-json[main](../../../../policy-templates/samples/compute/use-managed-disk-vm/azurepolicy.json "Create VM using Managed Disk")]

K nasazení této šablony můžete použít [Azure Portal](#deploy-with-the-portal) s [PowerShellem](#deploy-with-powershell) nebo s [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

[![nasazení ukázky zásad do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fuse-managed-disk-vm%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "use-managed-disk-vm" -DisplayName "Create VM using Managed Disk" -description "Create VM using Managed Disk" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/use-managed-disk-vm/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/use-managed-disk-vm/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
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
az policy definition create --name 'use-managed-disk-vm' --display-name 'Create VM using Managed Disk' --description 'Create VM using Managed Disk' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/use-managed-disk-vm/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/use-managed-disk-vm/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "use-managed-disk-vm"
```

### <a name="clean-up-azure-cli-deployment"></a>Vyčištění nasazení Azure CLI

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

- Další ukázky najdete v [ukázkách zásad Azure](index.md).