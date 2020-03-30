---
title: Ochrana instance pro instance škálovací sady virtuálních strojů Azure
description: Zjistěte, jak chránit instance škálovací sady virtuálních strojů Azure před operacemi škálování a škálování.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254115"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Ochrana instance pro instance škálovací sady virtuálních strojů Azure

Škálovací sady virtuálních strojů Azure umožňují lepší elasticitu vašich úloh prostřednictvím [automatického škálování](virtual-machine-scale-sets-autoscale-overview.md), takže můžete nakonfigurovat, kdy se vaše infrastruktura škáluje a kdy se škáluje. Škálovací sady také umožňují centrálně spravovat, konfigurovat a aktualizovat velký počet virtuálních počítače prostřednictvím různých nastavení [zásad upgradu.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Můžete nakonfigurovat aktualizaci na modelu škálovací sady a nová konfigurace se automaticky použije pro každou instanci škálovací sady, pokud jste nastavili zásady upgradu na Automatické nebo Rolling.

Jak aplikace zpracovává provoz, mohou nastat situace, kdy chcete, aby se s konkrétními instancemi zacházelo jinak než se zbytkem instance škálovací sady. Například některé instance v škálovací sadě může provádět dlouhotrvající operace a nechcete, aby tyto instance škálovat až do dokončení operací. Můžete mít také specializované několik instancí v škálovací sadě provádět další nebo jiné úkoly než ostatní členy škálovací sady. Vyžadujete tyto "speciální" virtuální chody, které nemají být změněny s ostatními instancemi v škálovací sadě. Ochrana instance poskytuje další ovládací prvky povolit tyto a další scénáře pro vaši aplikaci.

Tento článek popisuje, jak můžete použít a používat různé možnosti ochrany instancí s instancemi škálovací sady.

## <a name="types-of-instance-protection"></a>Typy ochrany instancí
Škálovací sady poskytují dva typy možností ochrany instancí:

-   **Ochrana před škálovatanem**
    - Povolena prostřednictvím vlastnosti **protectFromScaleIn** v instanci škálovací sady
    - Chrání instanci před automatickým škálování inicioval scale-in
    - Operace instancí iniciované uživatelem (včetně odstranění instance) **nejsou blokovány**
    - Operace zahájené na škálovací sadě (upgrade, reimage, navrátit atd.) **nejsou blokovány**

-   **Ochrana před akcemi škálovací sady**
    - Povolena prostřednictvím **vlastnosti protectFromScaleSetActions** instance škálovací sady
    - Chrání instanci před automatickým škálování inicioval scale-in
    - Chrání instanci před operacemi iniciovaných v škálovací sadě (například upgrade, reimage, navrátit atd.)
    - Operace instancí iniciované uživatelem (včetně odstranění instance) **nejsou blokovány**
    - Odstranění celé škálovací sady **není blokováno.**

## <a name="protect-from-scale-in"></a>Ochrana před škálovatanem
Ochranu instancí lze použít pro škálovací sady instancí po vytvoření instancí. Ochrana je použita a upravena pouze u [modelu instance](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a nikoli u [modelu škálovací sady](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Existuje několik způsobů použití ochrany škálování v instancích škálovací sady, jak je podrobně popsáno v následujících příkladech.

### <a name="azure-portal"></a>portál Azure

Můžete použít zabezpečení škálování prostřednictvím portálu Azure na instanci ve škálovací sadě. Současně nelze upravit více než jednu instanci. Opakujte kroky pro každou instanci, kterou chcete chránit.
 
1. Přejděte na existující škálovací sadu virtuálních strojů.
1. V nabídce vlevo vyberte **Instance** v části **Nastavení**.
1. Vyberte název instance, kterou chcete chránit.
1. Vyberte kartu **Zásady ochrany.**
1. V okně **Zásady ochrany** vyberte možnost **Chránit před škálovat.**
1. Vyberte **Uložit**. 

### <a name="rest-api"></a>REST API

Následující příklad aplikuje ochranu škálování na instanci v škálovací sadě.

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
>Ochrana instancí je podporována pouze s rozhraním API verze 2019-03-01 a vyšší

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí rutiny [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) použijte ochranu měřítka pro instanci škálovací sady.

Následující příklad použije ochranu škálování na instanci v škálovací sadě s ID instance 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Pomocí [aktualizace az vmss](/cli/azure/vmss#az-vmss-update) použijte ochranu škálování na instanci škálovací sady.

Následující příklad použije ochranu škálování na instanci v škálovací sadě s ID instance 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Ochrana před akcemi škálovací sady
Ochranu instancí lze použít pro škálovací sady instancí po vytvoření instancí. Ochrana je použita a upravena pouze u [modelu instance](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a nikoli u [modelu škálovací sady](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Ochrana instance před akcemi škálovací sady také chrání instanci před škálováním iniciovaného automatického škálování.

Existuje několik způsobů použití ochrany akcí škálovací sady v instancích škálovací sady, jak je podrobně popsáno v níže uvedených příkladech.

### <a name="azure-portal"></a>portál Azure

Ochranu před akcemi škálovací sady prostřednictvím portálu Azure můžete použít na instanci ve škálovací sadě. Současně nelze upravit více než jednu instanci. Opakujte kroky pro každou instanci, kterou chcete chránit.
 
1. Přejděte na existující škálovací sadu virtuálních strojů.
1. V nabídce vlevo vyberte **Instance** v části **Nastavení**.
1. Vyberte název instance, kterou chcete chránit.
1. Vyberte kartu **Zásady ochrany.**
1. V okně **Zásady ochrany** vyberte možnost **Chránit z akce škálovací sady.**
1. Vyberte **Uložit**. 

### <a name="rest-api"></a>REST API

Následující příklad aplikuje ochranu z akcí škálovací sady na instanci v škálovací sadě.

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
>Ochrana instancí je podporována pouze s rozhraním API verze 2019-03-01 a vyšší.</br>
Ochrana instance před akcemi škálovací sady také chrání instanci před škálováním iniciovaného automatického škálování. Nelze zadat "protectFromScaleIn": false při nastavení "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí rutiny [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) můžete použít ochranu před akcemi škálovací sady na instanci škálovací sady.

Následující příklad použije ochranu z akcí škálovací sady na instanci v škálovací sadě s ID instance 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Pomocí [aktualizace az vmss](/cli/azure/vmss#az-vmss-update) použijte ochranu z akcí škálovací sady na instanci škálovací sady.

Následující příklad použije ochranu z akcí škálovací sady na instanci v škálovací sadě s ID instance 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Řešení potíží
### <a name="no-protectionpolicy-on-scale-set-model"></a>Žádná ochranaZásady na modelu škálovací sady
Ochrana instance je použitelná pouze pro instance škálovací sady a nikoli na modelu škálovací sady.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Žádné protectionPolicy na škálování nastavit instanci modelu
Ve výchozím nastavení není zásada ochrany použita pro instanci při jeho vytvoření.

Ochranu instancí můžete použít na škálovací sady instancí po vytvoření instancí.

### <a name="not-able-to-apply-instance-protection"></a>Nelze použít ochranu instance
Ochrana instancí je podporována pouze s rozhraním API verze 2019-03-01 a vyšší. Zkontrolujte, zda se používá verze rozhraní API a podle potřeby aktualizujte. Možná také budete muset aktualizovat prostředí PowerShell nebo CLI na nejnovější verzi.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [nasadit aplikaci](virtual-machine-scale-sets-deploy-app.md) na škálovací sady virtuálních strojů.
