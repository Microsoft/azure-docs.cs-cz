---
title: Vzorek – vzor vyhovující shodě
description: Tato definice ukázkové zásady vyžaduje, aby prostředky splňovaly vzor shody pro konvence pojmenování.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: d5112a55e19b2481d5f453cf23d65c80862fb58e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74070430"
---
# <a name="sample---enforce-match-pattern-for-naming-conventions"></a>Vzor – vzor vyhovující vzoru pro konvence pojmenování

Vyžaduje, aby názvy prostředků splňovaly vzor like. Zadejte povolený vzor match jako parametr.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

[!code-json[main](../../../../policy-templates/samples/TextPatterns/enforce-match-pattern/azurepolicy.json "enforce match pattern")]

K nasazení této šablony můžete použít [Azure Portal](#deploy-with-the-portal) s [PowerShellem](#deploy-with-powershell) nebo s [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

[![nasazení ukázky zásad do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FTextPatterns%2Fenforce-match-pattern%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "enforce-match-pattern" -DisplayName "Ensure resource names match the naming pattern" -description "Ensure resource names match the naming pattern" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/enforce-match-pattern/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/enforce-match-pattern/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
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
az policy definition create --name 'enforce-match-pattern' --display-name 'Ensure resource names match the naming pattern' --description 'Ensure resource names match the naming pattern' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/enforce-match-pattern/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/enforce-match-pattern/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "enforce-match-pattern"
```

### <a name="clean-up-azure-cli-deployment"></a>Vyčištění nasazení Azure CLI

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

- Další ukázky najdete v [ukázkách zásad Azure](index.md).