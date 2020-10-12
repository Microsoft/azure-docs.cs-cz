---
title: Omezení instalace rozšíření virtuálních počítačů (Windows) pomocí Azure Policy
description: Použijte Azure Policy k omezení nasazení rozšíření.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: e4959c9dca909afde4bf6d351d79ecca1e4022a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87069766"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Omezení instalace rozšíření na virtuálních počítačích s Windows pomocí Azure Policy

Pokud chcete zabránit použití nebo instalaci určitých rozšíření na virtuální počítače s Windows, můžete vytvořit definici Azure Policy pomocí prostředí PowerShell k omezení rozšíření pro virtuální počítače v rámci skupiny prostředků. 

V tomto kurzu se používá Azure PowerShell v Cloud Shell, která se průběžně aktualizuje na nejnovější verzi. 

 

## <a name="create-a-rules-file"></a>Vytvoření souboru pravidel

Chcete-li omezit, jaká rozšíření lze nainstalovat, je nutné mít [pravidlo](../../governance/policy/concepts/definition-structure.md#policy-rule) , které poskytne Logic pro identifikaci rozšíření.

V tomto příkladu se dozvíte, jak odepřít rozšíření publikovaná pomocí ' Microsoft. COMPUTE ' vytvořením souboru pravidel v Azure Cloud Shell, ale pokud pracujete v místním prostředí PowerShell, můžete také vytvořit místní soubor a nahradit cestu ($home/clouddrive) cestou k místnímu souboru na vašem počítači.

Do [Cloud Shell](https://shell.azure.com/powershell)zadejte:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Zkopírujte následující soubor. JSON a vložte ho do souboru.

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

Až skončíte, stiskněte **klávesu CTRL + O** a pak **zadáním** uložte soubor. Stiskněte **CTRL + X** , abyste soubor zavřeli a ukončili.

## <a name="create-a-parameters-file"></a>Vytvoření souboru parametrů

Budete také potřebovat soubor [parametrů](../../governance/policy/concepts/definition-structure.md#parameters) , který vytvoří strukturu, kterou můžete použít pro předání seznamu rozšíření, která chcete zablokovat. 

V tomto příkladu se dozvíte, jak vytvořit soubor parametrů pro virtuální počítače v Cloud Shell, ale pokud pracujete v místním prostředí PowerShell, můžete také vytvořit místní soubor a nahradit cestu ($home/clouddrive) cestou k místnímu souboru na vašem počítači.

Do [Cloud Shell](https://shell.azure.com/powershell)zadejte:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Zkopírujte následující soubor. JSON a vložte ho do souboru.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "displayName": "Denied extension"
        }
    }
}
```

Až skončíte, stiskněte **klávesu CTRL + O** a pak **zadáním** uložte soubor. Stiskněte **CTRL + X** , abyste soubor zavřeli a ukončili.

## <a name="create-the-policy"></a>Vytvoření zásady

Definice zásady je objekt, který slouží k uložení konfigurace, kterou chcete použít. Definice zásady používá soubory pravidel a parametrů k definování zásad. Pomocí rutiny [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition) vytvořte definici zásady.

 Pravidla a parametry zásad jsou soubory, které jste vytvořili a uložili ve službě cloud Shell jako soubory. JSON.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Přiřazení zásady

Tento příklad přiřadí zásadu ke skupině prostředků pomocí [New-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment). Žádný virtuální počítač vytvořený ve skupině prostředků **myResourceGroup** nebude moct nainstalovat agenta přístupu k virtuálním počítačům ani rozšíření vlastních skriptů. 

Použijte [příkaz Get-AzSubscription | Rutina Format-Table](/powershell/module/az.accounts/get-azsubscription) pro získání ID předplatného, které se má použít místo v příkladu.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
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

Pokud chcete zásadu otestovat, zkuste použít rozšíření přístupu virtuálních počítačů. Následující příkaz by neměl selhat se zprávou "set-AzVMAccessExtension: prostředek" myVMAccess "byl zakázán zásadami."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

V portálu by změna hesla měla selhat s "nasazení šablony se nezdařilo z důvodu porušení zásad". .

## <a name="remove-the-assignment"></a>Odebrat přiřazení

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Odebrat zásadu

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [Azure Policy](../../governance/policy/overview.md).
