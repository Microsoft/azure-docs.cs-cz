---
title: Ukázkový – iniciativa zásad fakturačních značek
description: Tato ukázková sada definic zásad vyžaduje zadání hodnot značek pro nákladové středisko a název produktu.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 9d9a3a5af304c1080d1d43b9bbfbafe2554a9844
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275007"
---
# <a name="sample---billing-tags-policy-initiative"></a>Ukázkový – iniciativa zásad fakturačních značek

Tato sada zásad vyžaduje zadání hodnot značek pro nákladové středisko a název produktu. S využitím předdefinovaných zásad používá a vynucuje požadované značky. Pro značky zadáte požadované hodnoty.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

[!code-json[main](../../../../policy-templates/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.json "Billing Tags Policy Initiative")]

Tuto šablonu můžete nasadit pomocí webu [Azure Portal](#deploy-with-the-portal) nebo [PowerShellu](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

[![Deploy ukázkové zásady do Azure](https://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.parameters.json"

$policyset= New-AzPolicySetDefinition -Name "multiple-billing-tags" -DisplayName "Billing Tags Policy Initiative" -Description "Specify cost Center tag and product name tag" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -costCenterValue <required value for Cost Center tag> -productNameValue <required value for product Name tag>
```

### <a name="clean-up-powershell-deployment"></a>Vyčištění nasazení PowerShellu

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="apply-tags-to-existing-resources"></a>Použití značek na existující prostředky

Po přiřazení zásad můžete aktivovat aktualizaci všech existujících prostředků a vynutit tak přidané zásady značek. Následující skript zachová všechny ostatní značky, které u prostředků již byly:

```azurepowershell-interactive
$resources = Get-AzResource -ResourceGroupName 'ExampleGroup'

foreach ($r in $resources) {
    try {
        Set-AzResource -Tags ($a = if ($r.Tags -eq $NULL) { @{} } else {$r.Tags}) -ResourceId $r.ResourceId -Force -UsePatchSemantics
    }
    catch {
        Write-Host $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Další postup

- Další ukázky najdete v [ukázkách pro Azure Policy](index.md).