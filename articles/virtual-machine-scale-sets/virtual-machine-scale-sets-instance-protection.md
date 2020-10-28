---
title: Ochrana instancí pro instance sady škálování virtuálních počítačů Azure
description: Přečtěte si, jak chránit instance sady škálování virtuálních počítačů Azure pomocí operací škálování na více instancí a škálování sady.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 59720a3e89db29ba3eb0829084dd5252e27e9cc9
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746797"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Ochrana instancí pro instance sady škálování virtuálních počítačů Azure

Azure Virtual Machine Scale Sets umožňují lepší pružnost vašich úloh prostřednictvím [automatického škálování](virtual-machine-scale-sets-autoscale-overview.md), takže můžete nakonfigurovat, kdy se vaše infrastruktura škáluje a kdy se škáluje. Sady škálování také umožňují centrálně spravovat, konfigurovat a aktualizovat velký počet virtuálních počítačů prostřednictvím různých nastavení [zásad upgradu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) . Aktualizaci můžete nakonfigurovat v modelu sady škálování a nová konfigurace se automaticky použije na každou instanci sady škálování, pokud jste nastavili zásady upgradu na automatické nebo nové.

Jak vaše aplikace zpracovává provoz, může nastat situace, kdy chcete, aby se konkrétní instance zpracovaly jinak než ostatní instance sady škálování. Například určité instance v sadě škálování můžou provádět dlouhotrvající operace a nechcete, aby se tyto instance daly škálovat, dokud se operace nedokončí. Je také možné, že budete mít v sadě škálování několik instancí, aby se prováděly další nebo různé úlohy než ostatní členové sady škálování. Vyžadujete, aby tyto "speciální" virtuální počítače nebyly upravovány jinými instancemi v sadě škálování. Ochrana instancí poskytuje další ovládací prvky pro povolení těchto a dalších scénářů pro vaši aplikaci.

Tento článek popisuje, jak můžete použít a použít různé možnosti ochrany instancí s instancemi sady škálování.

## <a name="types-of-instance-protection"></a>Typy ochrany instance
Sady škálování poskytují dva typy funkcí ochrany instancí:

-   **Ochrana před škálováním**
    - Povoleno prostřednictvím vlastnosti **protectFromScaleIn** instance sady škálování
    - Chrání instance od škálování iniciované AutoScale-in.
    - Operace instancí iniciované uživatelem (včetně odstranění instance) nejsou **blokované** .
    - Operace iniciované v sadě škálování (upgrade, obnovení bitové kopie, zrušení přidělení atd.) nejsou **blokované** .

-   **Ochrana před akcemi sady škálování**
    - Povoleno prostřednictvím vlastnosti **protectFromScaleSetActions** instance sady škálování
    - Chrání instance od škálování iniciované AutoScale-in.
    - Chrání instanci z operací iniciované v sadě škálování (například upgrade, obnovení obrazu, zrušení přidělení atd.).
    - Operace instancí iniciované uživatelem (včetně odstranění instance) nejsou **blokované** .
    - Odstranění úplné sady škálování není **blokované** .

## <a name="protect-from-scale-in"></a>Ochrana před škálováním
Po vytvoření instancí lze použít ochranu instance pro instance sady škálování. Ochrana se aplikuje a upravuje jenom v [modelu instance](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a ne v [modelu sady škálování](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

V následujících příkladech jsou k dispozici několik způsobů, jak v instancích sady škálování použít ochranu s možností horizontálního rozšíření kapacity, jak je popsáno níže.

### <a name="azure-portal"></a>Azure Portal

Ochranu pomocí škálování můžete v Azure Portal použít na instanci v sadě škálování. V jednom okamžiku nelze upravovat více než jednu instanci. Opakujte postup pro všechny instance, které chcete chránit.
 
1. Přejít na existující sadu škálování virtuálního počítače.
1. V nabídce vlevo v části **Nastavení** vyberte **instance** .
1. Vyberte název instance, kterou chcete chránit.
1. Vyberte kartu **Zásady ochrany** .
1. V okně **Zásady ochrany** vyberte možnost **chránit před škálováním** .
1. Vyberte **Uložit** . 

### <a name="rest-api"></a>REST API

Následující příklad aplikuje ochranu se škálováním na instanci v sadě škálování.

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
>Ochrana instancí se podporuje jenom s rozhraním API verze 2019-03-01 a novějším.

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí rutiny [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) aplikujte ochranu škálováním na více instancí na instanci sady škálování.

Následující příklad aplikuje ochranu se škálováním na instanci v sadě škálování s ID instance 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Pomocí [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) aplikujte ochranu škálováním na více instancí na instanci sady škálování.

Následující příklad aplikuje ochranu se škálováním na instanci v sadě škálování s ID instance 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Ochrana před akcemi sady škálování
Po vytvoření instancí lze použít ochranu instance pro instance sady škálování. Ochrana se aplikuje a upravuje jenom v [modelu instance](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a ne v [modelu sady škálování](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Ochrana instance před akcemi sady škálování také chrání instance z automatického škálování iniciované.

Existuje několik způsobů, jak použít ochranu akcí sady škálování na instancích sady škálování, jak je popsáno níže v následujících příkladech.

### <a name="azure-portal"></a>Azure Portal

Ochranu můžete použít z akcí sady škálování pomocí Azure Portal do instance v sadě škálování. V jednom okamžiku nelze upravovat více než jednu instanci. Opakujte postup pro všechny instance, které chcete chránit.
 
1. Přejít na existující sadu škálování virtuálního počítače.
1. V nabídce vlevo v části **Nastavení** vyberte **instance** .
1. Vyberte název instance, kterou chcete chránit.
1. Vyberte kartu **Zásady ochrany** .
1. V okně **Zásady ochrany** vyberte možnost **nastavit akce chránit ze sady škálování** .
1. Vyberte **Uložit** . 

### <a name="rest-api"></a>REST API

Následující příklad aplikuje ochranu z akcí sady škálování na instanci v sadě škálování.

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
>Ochrana instancí se podporuje jenom s rozhraním API verze 2019-03-01 a novějším.</br>
Ochrana instance před akcemi sady škálování také chrání instance z automatického škálování iniciované. Nemůžete zadat "protectFromScaleIn": false při nastavování "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí rutiny [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) aplikujte ochranu ze všech akcí sady škálování na vaši instanci sady škálování.

Následující příklad aplikuje ochranu z akcí sady škálování na instanci v sadě škálování s ID instance 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Pomocí [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) aplikujte ochranu ze všech akcí sady škálování na vaši instanci sady škálování.

Následující příklad aplikuje ochranu z akcí sady škálování na instanci v sadě škálování s ID instance 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Řešení potíží
### <a name="no-protectionpolicy-on-scale-set-model"></a>Žádné protectionPolicy v modelu sady škálování
Ochrana instancí se vztahuje pouze na instance sady škálování, nikoli na model sady škálování.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Žádné protectionPolicy v modelu instance sady škálování
Ve výchozím nastavení zásady ochrany nejsou při vytvoření instance aplikovány na instanci.

Můžete použít ochranu instancí pro škálování instancí sady po vytvoření instancí.

### <a name="not-able-to-apply-instance-protection"></a>Nejde použít ochranu instancí.
Ochrana instancí se podporuje jenom s rozhraním API verze 2019-03-01 a novějším. Ověřte, jakou verzi rozhraní API používáte, a podle potřeby ho aktualizujte. Může být také nutné aktualizovat PowerShell nebo CLI na nejnovější verzi.

## <a name="next-steps"></a>Další kroky
Naučte se, jak [nasadit vaši aplikaci do služby](virtual-machine-scale-sets-deploy-app.md) Virtual Machine Scale Sets.
