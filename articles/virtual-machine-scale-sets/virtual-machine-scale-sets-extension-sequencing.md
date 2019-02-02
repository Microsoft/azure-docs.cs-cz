---
title: Použití nastavení posloupnosti rozšíření se škálovacími sadami virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak pořadí rozšíření zřizování při nasazování několika rozšíření na škálovací sady virtuálních počítačů.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: 2e5dfda16c4828b3113fc50d4cffc79fe6ff19e8
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563996"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Nastaví pořadí zřizování rozšíření virtuálního počítače škálovací
Rozšíření virtuálního počítače Azure poskytují funkce, jako je například konfigurace po nasazení a správu, monitorování, zabezpečení a další. Nasazení v produkčním prostředí většinou pomocí kombinace více rozšíření, které jsou nakonfigurované pro instance virtuálních počítačů k dosažení požadovaných výsledků.

Pokud používám několik rozšíření na virtuálním počítači, je důležité zajistit, že rozšíření, které vyžadují stejné prostředky operačního systému se pokoušel se získat tyto prostředky ve stejnou dobu. Některá rozšíření závisí také na dalších rozšířeních poskytnout požadované konfigurace, jako je například nastavení prostředí a tajných kódů. Bez správné pořadí a řazení v místě může selhat závislé rozšíření nasazení.

Tento článek podrobně popisuje, jak můžete pořadí rozšíření nakonfigurovat pro instance virtuálních počítačů ve škálovací sady virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že už znáte:
-   Virtuální počítač Azure [rozšíření](../virtual-machines/extensions/overview.md)
-   [Úprava](virtual-machine-scale-sets-upgrade-scale-set.md) škálovací sady virtuálních počítačů

## <a name="when-to-use-extension-sequencing"></a>Kdy použít nastavení posloupnosti rozšíření
Pořadí rozšíření v not nastaví povinné pro škálování, a není uvedeno, je možné zřídit rozšíření na instanci škálovací sady v libovolném pořadí.

Například pokud vaše modelu škálovací sady má dvě rozšíření – ExtensionA a ExtensionB – zadáno v modelu, pak jednu z následujících pořadí zřizování může dojít k:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Pokud vaše aplikace vyžaduje rozšíření A vždy mají zřídit před rozšíření B, měli byste použít nastavení posloupnosti rozšíření, jak je popsáno v tomto článku. Pomocí nastavení posloupnosti rozšíření dojde nyní pouze jedno pořadí:
-   ExtensionA - > ExtensionB

Žádná rozšíření není zadáno v určeném pořadí zřizování se dá zřídit v okamžiku, včetně před, po nebo během určeném pořadí. Nastavení posloupnosti rozšíření pouze určuje, že konkrétní rozšíření budou zřízené po jiné konkrétní rozšíření. To ale nijak neovlivní zřizování kteréhokoli jiného rozšíření, které jsou definované v modelu.

Například pokud vaše modelu škálovací sady obsahuje tři rozšíření – rozšíření A, B rozšíření a rozšíření C – zadáno v modelu, a rozšíření jazyka C je nastavená na zřízené po A rozšíření, pak jednu z následujících pořadí zřizování může dojít k:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Pokud je potřeba zajistit, že nemá příponu zřizován při provádění pořadí definované rozšíření, doporučujeme sekvencování všechna rozšíření v modelu škálovací sady. V příkladu výše lze nastavit rozšíření B zřídit po rozšíření C tak, že může dojít pouze jedno pořadí:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Jak používat nastavení posloupnosti rozšíření
Pořadí rozšíření zřízení, je nutné aktualizovat definici rozšíření v modelu škálovací sady vlastnost "provisionAfterExtensions", který přijímá pole názvů rozšíření zahrnout. Rozšíření uvedený v poli hodnota vlastnosti musí být plně definován v modelu škálovací sady.

### <a name="template-deployment"></a>Nasazení šablony
Následující příklad definuje šablony, pokud škálovací sada obsahovat tři rozšíření – ExtensionA ExtensionB a ExtensionC – tak, že rozšíření jsou zřízené v pořadí:
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Vzhledem k tomu, že vlastnost "provisionAfterExtensions" přijímá pole názvů rozšíření, výše uvedeném příkladu je možné upravit tak, aby ExtensionC je zřízené po ExtensionA a ExtensionB, ale žádné řazení se musí být mezi ExtensionA a ExtensionB. Následující šablony lze použít k dosažení tohoto scénáře:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
Následující příklad přidá nové rozšíření s názvem ExtensionC do modelu škálovací sady. ExtensionC má závislosti na ExtensionA a ExtensionB, které už je definován v modelu škálovací sady.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Pokud ExtensionC byl definován v škálovací sady modelu a teď ho přidat její závislosti, můžete spustit `PATCH` k úpravě vlastností již nasazených rozšíření.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Změny v existujících instancích škálovací sady se použijí při dalším [upgradovat](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Použití [přidat AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) definice modelu rutiny pro přidání rozšíření stav aplikací do škálovací sady. Nastavení posloupnosti rozšíření vyžaduje použití powershellu Az 1.2.0 nebo vyšší.

Následující příklad přidá [stav aplikací – rozšíření](virtual-machine-scale-sets-health-extension.md) k `extensionProfile` ve škálovací nastavení modelu na základě Windows škálovací sady. Stav aplikací – rozšíření se zřídí po zřízení [rozšíření vlastních skriptů](../virtual-machines/extensions/custom-script-windows.md)již definovaná ve škálovací sadě.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Použití [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) přidat rozšíření stav aplikací do škálovací sady definice modelu. Nastavení posloupnosti rozšíření vyžaduje použití Azure CLI 2.0.55 nebo vyšší.

Následující příklad přidá [stav aplikací – rozšíření](virtual-machine-scale-sets-health-extension.md) škálovací nastavení modelu na základě Windows škálovací sady. Stav aplikací – rozšíření se zřídí po zřízení [rozšíření vlastních skriptů](../virtual-machines/extensions/custom-script-windows.md)již definovaná ve škálovací sadě.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Řešení potíží

### <a name="not-able-to-add-extension-with-dependencies"></a>Není možné přidat rozšíření se závislostmi?
1. Ujistěte se, že rozšiřujících modulů uvedených v provisionAfterExtensions jsou definovány v modelu škálovací sady.
2. Ujistěte se, neexistují žádné cyklické závislosti zabrání zavedení. Například následující text není povolený: ExtensionA -> ExtensionB -> ExtensionC ExtensionA ->
3. Zajistěte, aby měly všechna rozšíření, které provedete závislosti, vlastnost "nastavení" v části "vlastnosti" rozšíření. Například pokud ExtentionB musí být zřízená po ExtensionA, pak by ExtensionA položka musí mít pole "nastavení" v části ExtensionA "properties". Pokud rozšíření nenutí všechna požadovaná nastavení, můžete zadat vlastnost prázdný "nastavení".

### <a name="not-able-to-remove-extensions"></a>Není možné odebrat rozšíření?
Ujistěte se, že rozšíření odebírá nejsou uvedené v části provisionAfterExtensions pro libovolná rozšíření.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [při nasazování aplikace](virtual-machine-scale-sets-deploy-app.md) nastaví na škálování virtuálního počítače.
