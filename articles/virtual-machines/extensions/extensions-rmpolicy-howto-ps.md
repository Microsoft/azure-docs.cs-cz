---
title: Omezení instalace rozšíření virtuálních počítače pomocí zásad Azure
description: Pomocí zásad Azure můžete omezit nasazení rozšíření.
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
ms.openlocfilehash: 428db340ce43463939ce71ffadd4188060f3e732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073117"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Omezení instalace rozšíření na virtuálních počítačích s Windows pomocí zásad Azure

Pokud chcete zabránit použití nebo instalaci určitých rozšíření na virtuálních počítačích s Windows, můžete vytvořit zásadu Azure pomocí PowerShellu k omezení rozšíření pro virtuální počítače v rámci skupiny prostředků. 

Tento kurz používá Azure PowerShell v rámci cloudového prostředí, které se neustále aktualizuje na nejnovější verzi. 

 

## <a name="create-a-rules-file"></a>Vytvoření souboru pravidel

Chcete-li omezit, jaká rozšíření lze nainstalovat, musíte mít [pravidlo,](../../governance/policy/concepts/definition-structure.md#policy-rule) které poskytuje logiku k identifikaci rozšíření.

Tento příklad ukazuje, jak odepřít rozšíření publikované "Microsoft.Compute" vytvořením souboru pravidel v Prostředí Azure Cloud Shell, ale pokud pracujete v Prostředí PowerShell místně, můžete také vytvořit místní soubor a nahradit cestu ($home/clouddrive) s cestou k místnímu souboru ve vašem počítači.

V [prostředí cloud](https://shell.azure.com/powershell)udáváte:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Zkopírujte a vložte do souboru následující soubor JSon.

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

Až budete hotovi, stiskněte **Ctrl + O** a pak **Enter** pro uložení souboru. Chcete-li soubor zavřít a ukončit, stiskněte **klávesu Ctrl + X.**

## <a name="create-a-parameters-file"></a>Vytvoření souboru parametrů

Potřebujete také soubor [parametrů,](../../governance/policy/concepts/definition-structure.md#parameters) který vytvoří strukturu, kterou můžete použít pro předávání v seznamu rozšíření, která chcete blokovat. 

Tento příklad ukazuje, jak vytvořit soubor parametrů pro virtuální počítače v prostředí Cloud Shell, ale pokud pracujete v prostředí PowerShell místně, můžete také vytvořit místní soubor a nahradit cestu ($home/clouddrive) s cestou k místnímu souboru v počítači.

V [prostředí Cloud Shell](https://shell.azure.com/powershell)zadejte:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Zkopírujte a vložte do souboru následující soubor JSon.

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

Až budete hotovi, stiskněte **Ctrl + O** a pak **Enter** pro uložení souboru. Chcete-li soubor zavřít a ukončit, stiskněte **klávesu Ctrl + X.**

## <a name="create-the-policy"></a>Vytvořit zásadu

Definice zásadje objekt používaný k uložení konfigurace, kterou chcete použít. Definice zásad používá k definování zásad soubory pravidel a parametrů. Vytvořte definici zásad pomocí rutiny [New-AzPolicyDefinition.](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition)

 Pravidla a parametry zásad jsou soubory, které jste vytvořili a uložili jako soubory JSON ve vašem cloudovém prostředí.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Přiřazení zásady

Tento příklad přiřadí zásadu skupině prostředků pomocí [aplikace New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Žádný virtuální virtuální ms vytvořený ve skupině prostředků **myResourceGroup** nebude moct nainstalovat rozšíření Agent pro přístup virtuálních připojení nebo Vlastní skript. 

Použití [Get-AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) rutina získat id předplatného použít místo jednoho v příkladu.

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

Chcete-li otestovat zásady, zkuste použít rozšíření Přístup k virtuálnímu připojení. Následující by měl selhat se zprávou "Set-AzVMAccessExtension : Prostředek myVMAccess' byl zakázán zásadou."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Na portálu by měla selhat změna hesla s "Nasazení šablony se nezdařilo z důvodu porušení zásad." zpráva.

## <a name="remove-the-assignment"></a>Odebrání přiřazení

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Odebrat zásadu

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [Azure Policy](../../governance/policy/overview.md).
