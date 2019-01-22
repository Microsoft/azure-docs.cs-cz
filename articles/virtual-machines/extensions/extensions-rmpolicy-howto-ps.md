---
title: K omezení instalace rozšíření virtuálního počítače pomocí služby Azure Policy | Dokumentace Microsoftu
description: Pomocí služby Azure Policy na omezení nasazení rozšíření.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 82b01cec892f15f7f85f6b5f822475114b5b73c6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434985"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Instalace rozšíření na virtuálních počítačích s Windows pomocí zásad Azure

Pokud chcete zabránit použití nebo instalace některých rozšíření na vašich virtuálních počítačích s Windows, můžete vytvořit zásady služby Azure pomocí prostředí PowerShell k omezení rozšíření pro virtuální počítače v rámci skupiny prostředků. 

Tento kurz používá prostředí Azure PowerShell ve službě Cloud Shell, která se neustále aktualizuje na nejnovější verzi. Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte pro tento kurz použít modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). 

## <a name="create-a-rules-file"></a>Vytvoření souboru pravidel

Pokud chcete omezit, jaká rozšíření je možné nainstalovat, je potřeba mít [pravidlo](/azure/azure-policy/policy-definition#policy-rule) k poskytování logiky pro identifikaci rozšíření.

Tento příklad ukazuje, jak zakázat rozšíření publikovaná "Microsoft.Compute" tak, že vytvoříte soubor pravidel ve službě Azure Cloud Shell, ale pokud pracujete v prostředí PowerShell místně, můžete také vytvořit místního souboru a nahradíte část cesty ($home/clouddrive) cestu ke místní soubor na svůj počítač.

V [Cloud Shell](https://shell.azure.com/powershell), typ:

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

Jakmile budete hotovi, klikněte **Ctrl + O** a potom **Enter** k uložení souboru. Spuštění **Ctrl + X** zavřete soubor a ukončete.

## <a name="create-a-parameters-file"></a>Vytvoření souboru parametrů

Budete také potřebovat [parametry](/azure/azure-policy/policy-definition#parameters) soubor, který vytvoří strukturu, můžete použít k předání v seznamu rozšíření blokování. 

Tento příklad ukazuje, jak vytvořit soubor parametrů pro virtuální počítače ve službě Cloud Shell, ale pokud pracujete v prostředí PowerShell místně, můžete také vytvořit místního souboru a nahradíte část cesty ($home/clouddrive) s cestou k místnímu souboru na vašem počítači.

V [Cloud Shell](https://shell.azure.com/powershell), typ:

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

Jakmile budete hotovi, klikněte **Ctrl + O** a potom **Enter** k uložení souboru. Spuštění **Ctrl + X** zavřete soubor a ukončete.

## <a name="create-the-policy"></a>Vytvoření zásad

Definice zásad není objekt použitý k uložení konfigurace, který chcete použít. Definice zásady používá k definování zásady souborů pravidel a parametry. Vytvoření definice zásady pomocí [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) rutiny.

 Pravidla zásad a parametry jsou soubory vytvořeny a uloženy jako soubory .json ve službě cloud shell.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Přiřazení zásad

Tento příklad přiřadí zásady skupiny prostředků pomocí [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Jakýkoli virtuální počítač vytvořený v **myResourceGroup** skupinu prostředků, nebude možné nainstalovat rozšíření přístupu Agent virtuálního počítače nebo vlastního skriptu. 

Použití [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) rutiny pro získání ID vašeho předplatného oznamujícím v příkladu.

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

K otestování zásady, zkuste použít rozšíření přístupu virtuálních počítačů. Následující by selhat se zprávou "AzureRmVMAccessExtension sady: Prostředek "myVMAccess" zásady nepovolily."

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Na portálu by selhat Změna hesla se "nasazení šablony se nezdařilo z důvodu porušení zásad." zpráva.

## <a name="remove-the-assignment"></a>Odebrat přiřazení

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Odebrání zásady

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [Azure Policy](../../azure-policy/azure-policy-introduction.md).
