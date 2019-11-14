---
title: Ukázka – několik vzorů názvů
description: Tato definice ukázkové zásady vyžaduje, aby prostředek odpovídal jednomu ze zadaných vzorů názvů.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 3dcecbf69d4170dd851f1b2678ae0136c86c7e49
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076502"
---
# <a name="sample---allow-multiple-name-patterns"></a>Ukázka – povolení více vzorů názvů

Povolíte jeden z mnoha vzorů názvů, který se použije pro prostředky. Zadáte v pravidle zásad vzory s povolenými názvy.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

[!code-json[main](../../../../policy-templates/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.json "allow multiple name patterns")]

K nasazení této šablony můžete použít [Azure Portal](#deploy-with-the-portal) s [PowerShellem](#deploy-with-powershell) nebo s [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

[![nasazení ukázky zásad do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FTextPatterns%2Fallow-multiple-name-patterns%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "allow-multiple-name-patterns" -DisplayName "Allow one of many name patterns to be used for resources." -description "Allow one of many name patterns to be used for resources." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.parameters.json' -Mode All
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
az policy definition create --name 'allow-multiple-name-patterns' --display-name 'Allow one of many name patterns to be used for resources.' --description 'Allow one of many name patterns to be used for resources.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "allow-multiple-name-patterns" 
```

### <a name="clean-up-azure-cli-deployment"></a>Vyčištění nasazení Azure CLI

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

- Další ukázky najdete v [ukázkách zásad Azure](index.md).