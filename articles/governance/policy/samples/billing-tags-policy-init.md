---
title: Ukázka pro Azure Policy – Iniciativa zásad fakturačních značek
description: Tato ukázková sada zásad vyžaduje zadání hodnot značek pro nákladové středisko a název produktu.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 936a794826b8923512deb4c3def2ee41499683e9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984617"
---
# <a name="billing-tags-policy-initiative"></a>Iniciativa zásad fakturačních značek

Tato sada zásad vyžaduje zadání hodnot značek pro nákladové středisko a název produktu. S využitím předdefinovaných zásad používá a vynucuje požadované značky. Pro značky zadáte požadované hodnoty.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

[!code-json[main](../../../../policy-templates/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.json "Billing Tags Policy Initiative")]

Tuto šablonu můžete nasadit pomocí webu [Azure Portal](#deploy-with-the-portal) nebo [PowerShellu](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

[![Nasazení do Azure](http://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.parameters.json"

$policyset= New-AzureRmPolicySetDefinition -Name "multiple-billing-tags" -DisplayName "Billing Tags Policy Initiative" -Description "Specify cost Center tag and product name tag" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzureRmPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -costCenterValue <required value for Cost Center tag> -productNameValue <required value for product Name tag>
```

### <a name="clean-up-powershell-deployment"></a>Vyčištění nasazení PowerShellu

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="apply-tags-to-existing-resources"></a>Použití značek na existující prostředky

Po přiřazení zásad můžete aktivovat aktualizaci všech existujících prostředků a vynutit tak přidané zásady značek. Následující skript zachová všechny ostatní značky, které u prostředků již byly:

```azurepowershell-interactive
$resources = Get-AzureRmResource -ResourceGroupName 'ExampleGroup'

foreach ($r in $resources) {
    try {
        Set-AzureRmResource -Tags ($a = if ($r.Tags -eq $NULL) { @{} } else {$r.Tags}) -ResourceId $r.ResourceId -Force -UsePatchSemantics
    }
    catch {
        Write-Host $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Další kroky

- Další ukázky najdete v [ukázkách pro Azure Policy](index.md).