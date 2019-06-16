---
title: Instance sady ochrany instancí systému pro škálování virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak chránit instancí škálovací sady virtuálních počítačů Azure z operace škálování a škálovací sady.
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
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 61430f5a43a04fa0e5b2f0c79ff03419c73aaf28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416551"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Nastavená ochrana instance pro virtuální počítač Azure škálování instance (Preview)
Škálovací sady virtuálních počítačů Azure umožňují lepší pružnost úloh prostřednictvím [automatického škálování](virtual-machine-scale-sets-autoscale-overview.md), tak můžete konfigurovat při škálovat infrastrukturu, a když se škáluje na méně instancí. Škálovací sady také umožňují centrálně spravovat, konfigurovat a aktualizovat velký počet virtuálních počítačů prostřednictvím různých [zásad upgradu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) nastavení. Aktualizace můžete konfigurovat v modelu škálovací sady a novou konfiguraci platí automaticky pro všechny instance škálovací sady Pokud jste nastavili zásady upgradu automaticky nebo Hromadná.

Jak vaše aplikace zpracovává přenosy, nesmí být instance situací, ve kterém chcete konkrétní instance zpracovávat odděleně od zbytku škálovací sady. Například dlouho běžící operace může provádět určité instance ve škálovací sadě, a nechcete, aby tyto instance se škálovat se změnami až do dokončení operace. Vám může také specializovaný několik instancí ve škálovací sadě provádět další nebo jiné úlohy než ostatní členy škálovací sady. Budete potřebovat tyto "speciální" virtuální počítače se mění s jinými instancemi ve škálovací sadě. Instance protection nabízí další ovládací prvky na tyto a další scénáře pro vaši aplikaci.

Tento článek popisuje, jak můžete použít a využít možnosti ochrany jinou instanci s instancí škálovací sady.

> [!NOTE]
>Ochrana instance je aktuálně ve verzi Public Preview. Bez výslovného souhlasu postup je potřeba pro použití funkce ve verzi public preview je popsáno níže. Instance protection ve verzi preview se podporuje jenom s rozhraním API verze 2019-03-01 a ve škálovacích sadách použití spravovaných disků.

## <a name="types-of-instance-protection"></a>Typy instancí ochrany
Škálovací sady poskytují možnosti ochrany instance dva typy:

-   **Ochrana před škálování na méně instancí**
    - Prostřednictvím **protectFromScaleIn** instance vlastnosti škálovací sady
    - Chrání instance z automatického škálování iniciované škálování na méně instancí
    - Operace zahájená uživatelem instance (včetně odstranění instance) jsou **neblokován**
    - Operací spuštěných ve škálovací sadě (upgrade, obnovení z Image, uvolnit, atd.) jsou **neblokován**

-   **Ochrana před akcí škálovací sady**
    - Prostřednictvím **protectFromScaleSetActions** instance vlastnosti škálovací sady
    - Chrání instance z automatického škálování iniciované škálování na méně instancí
    - Chrání instance z operací spuštěných ve škálovací sadě (jako je upgrade, obnovení z Image, uvolnit, atd.)
    - Operace zahájená uživatelem instance (včetně odstranění instance) jsou **neblokován**
    - Odstranění celé škálovací sady je **neblokován**

## <a name="protect-from-scale-in"></a>Ochrana před škálování na méně instancí
Instance ochranu můžete použít k instancím škálovací sady vytvořené instance. Je použita a upravit pouze na ochranu [instance modelu](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a ne na [modelu škálovací sady](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Použití ochrany škálování na méně instancí na instancím škálovací sady, jak je uvedeno v následující příklady několika způsoby.

### <a name="rest-api"></a>REST API

Následující příklad použije ochranu škálování na méně instancí na instance ve škálovací sadě.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Instance ochrany je podporována pouze pomocí rozhraní API verze 2019-03-01 a novějších

### <a name="azure-powershell"></a>Azure PowerShell

Použití [aktualizace AzVmssVM](/powershell/module/az.compute/update-azvmssvm) instance k použití ochrany škálování na méně instancí škálovací sady.

Následující příklad použije ochranu škálování na méně instancí na instance ve škálovací sadě, s instance ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Použití [az vmss update](/cli/azure/vmss#az-vmss-update) použije ochrana škálování na méně instancí na instance vaší škálovací sadě.

Následující příklad použije ochranu škálování na méně instancí na instance ve škálovací sadě, s instance ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Ochrana před akcí škálovací sady
Instance ochranu můžete použít k instancím škálovací sady vytvořené instance. Je použita a upravit pouze na ochranu [instance modelu](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a ne na [modelu škálovací sady](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Ochrana instancí ze škálovací sady akcí taky chrání instance automatického škálování iniciované škálování na méně instancí.

Existuje více způsobů použití škálovací akce ochrany ve škálovací sadě v instancích sady popsané v následujících příkladech.

### <a name="rest-api"></a>REST API

Následující příklad použije ochranu z akce škálovací sady do instance ve škálovací sadě.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Ochrany instancí se podporuje jenom s rozhraním API verze 2019-03-01 a novějších.</br>
Ochrana instancí ze škálovací sady akcí taky chrání instance automatického škálování iniciované škálování na méně instancí. Nelze zadat "protectFromScaleIn": false při nastavování "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Použití [aktualizace AzVmssVM](/powershell/module/az.compute/update-azvmssvm) rutina pro použití ochrany ze škálovací sady akcí k vaší instanci škálovací sady.

Následující příklad použije ochranu z akce škálovací sady do instance ve škálovací sadě, s instance ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Použití [az vmss update](/cli/azure/vmss#az-vmss-update) pro použití ochrany z škálovací sadu akcí k vaší instanci škálovací sady.

Následující příklad použije ochranu z akce škálovací sady do instance ve škálovací sadě, s instance ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Řešení potíží
### <a name="no-protectionpolicy-on-scale-set-model"></a>Žádné protectionPolicy v modelu škálovací sady
Ochrany instancí systému platí jenom v instancích škálovací sady a ne na modelu škálovací sady.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Žádné protectionPolicy na modelu instance škálovací sady
Ve výchozím nastavení neplatí zásady ochrany pro instanci při jeho vytvoření.

Můžete použít instanci ochrany k instancím škálovací sady vytvořené instance.

### <a name="not-able-to-apply-instance-protection"></a>Není možné použít ochranu instance
Ochrany instancí se podporuje jenom s rozhraním API verze 2019-03-01 a novějších. Zkontrolujte verze rozhraní API používá a doplňte podle potřeby. Potřebujete také aktualizovat na nejnovější verzi prostředí PowerShell nebo rozhraní příkazového řádku.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [při nasazování aplikace](virtual-machine-scale-sets-deploy-app.md) nastaví na škálování virtuálního počítače.
