---
title: Pomocí zásad Azure můžete omezit instalace rozšíření virtuálního počítače | Microsoft Docs
description: Pomocí zásad Azure můžete omezit nasazení rozšíření.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: da5b0db997ba1aa0e998f6fe2645e955b490951d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Pomocí zásad Azure můžete omezit rozšíření Instalace na virtuálních počítačích Windows

Pokud chcete, aby se zabránilo použití nebo instalace určité rozšíření na virtuální počítače Windows, můžete vytvořit zásady služby Azure pomocí prostředí PowerShell omezit rozšíření pro virtuální počítače ve skupině prostředků. 

Tento kurz používá prostředí Azure PowerShell v rámci cloudové prostředí, které se průběžně aktualizují na nejnovější verzi. Pokud si zvolíte instalaci a použití prostředí PowerShell místně, tento kurz vyžaduje prostředí Azure PowerShell verze modulu 3,6 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-rules-file"></a>Vytvoření souboru pravidel

Chcete-li omezit, jaká rozšíření se dá nainstalovat, je potřeba mít [pravidlo](/azure/azure-policy/policy-definition#policy-rule) zajistit logiku k identifikaci rozšíření.

Tento příklad ukazuje, jak zakázat rozšíření publikováno 'Microsoft.Compute' pomocí souboru pravidel v prostředí cloudu Azure, ale pokud pracujete v prostředí PowerShell místně, můžete také vytvořit místního souboru a nahradit cestu ($home/clouddrive) s cestou k místní soubor na váš počítač.

V [cloudové prostředí](https://shell.azure.com/powershell), zadejte:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Zkopírujte a vložte následující .json do souboru.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Až skončíte, stiskněte tlačítko **Ctrl + O** a potom **Enter** k uložení souboru. Stiskněte tlačítko **kombinaci kláves Ctrl + X** zavřete soubor a ukončete.

## <a name="create-a-parameters-file"></a>Vytvořte soubor parametrů

Musíte taky [parametry](/azure/azure-policy/policy-definition#parameters) soubor, který vytvoří strukturu, musíte použít pro předávání v seznamu rozšíření blokování. 

Tento příklad ukazuje, jak vytvořit soubor parametrů pro virtuální počítače v prostředí cloudu, ale pokud pracujete v prostředí PowerShell místně, můžete také vytvořit místního souboru a nahradit cestu ($home/clouddrive) s cestou k místnímu souboru na vašem počítači.

V [cloudové prostředí](https://shell.azure.com/powershell), zadejte:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Zkopírujte a vložte následující .json do souboru.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Až skončíte, stiskněte tlačítko **Ctrl + O** a potom **Enter** k uložení souboru. Stiskněte tlačítko **kombinaci kláves Ctrl + X** zavřete soubor a ukončete.

## <a name="create-the-policy"></a>Vytvoření zásad

Definice zásady je objekt se používá k ukládání konfigurace, který chcete použít. Definice zásad používá soubory pravidel a parametry, můžete definovat zásady. Vytvoření definice zásady pomocí [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) rutiny.

 Parametry a pravidla zásad se soubory, které jste vytvořili a uloženy jako soubory .json ve své cloudové prostředí.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Přiřaďte zásady

Tento příklad přiřadí zásady skupiny prostředků pomocí [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Všechny virtuální počítač vytvořený na **myResourceGroup** skupinu prostředků, nebude možné nainstalovat agenta virtuálního počítače přístup nebo skriptu vlastní rozšíření. 

Použití [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) rutiny svoje ID předplatného používejte místo v příkladu.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Testování zásad

Chcete-li otestovat zásady, zkuste použít rozšíření pro přístup virtuálních počítačů. Následující by měl neúspěšné a zobrazí se zpráva "Set-AzureRmVMAccessExtension: prostředku"myVMAccess"byla zakázána zásadami."

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Na portálu Změna hesla by měla dojde k chybě "nasazení šablony se nezdařilo z důvodu porušení zásad." Zpráva.

## <a name="remove-the-assignment"></a>Odebrat přiřazení

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Odebrání zásady

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [zásad Azure](../../azure-policy/azure-policy-introduction.md).
