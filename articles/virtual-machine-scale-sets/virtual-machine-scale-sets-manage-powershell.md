---
title: Správa sady škálování virtuálního počítače v prostředí Azure PowerShell | Microsoft Docs
description: Běžné rutin prostředí Azure PowerShell ke správě sady škálování virtuálního počítače, jako je například postup spuštění a zastavení instance, nebo změnit měřítko nastavená kapacita.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: iainfou
ms.openlocfilehash: 39cd7fa2232329716ba16abf92ba4a5f2cc15487
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652776"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Spravovat škálování virtuálních počítačů, nastavit pomocí prostředí Azure PowerShell
V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Tento článek podrobně popisuje některé běžné rutin prostředí Azure PowerShell, které umožňují provádět tyto úlohy.

K dokončení těchto úloh správy, je třeba nejnovější modul Azure PowerShell. Informace najdete v tématu [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps). Pokud potřebujete vytvořit škálovací sadu virtuálních počítačů, můžete [vytvořit měřítko nastavit pomocí prostředí Azure PowerShell](quick-create-powershell.md).


## <a name="view-information-about-a-scale-set"></a>Zobrazit informace o sadě škálování
Chcete-li zobrazit celkový informace o sadě škálování, použijte [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). Následující příklad získá informace o škálování nastavení s názvem *myScaleSet* v *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Chcete-li zobrazit seznam instance virtuálního počítače ve škálovací sadě, použijte [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Následující příklad vypíše všechny instance virtuálních počítačů v pojmenované sad škálování *myScaleSet* a v *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty pro tyto názvy:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Chcete-li zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte `-InstanceId` parametru [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) a zadejte instanci zobrazení. Následující příklad zobrazení informace o instanci virtuálního počítače *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Předchozí příkazy vám ukázal, informace o vaší sadě škálování a instancí virtuálních počítačů. Chcete-li zvýšit nebo snížit počet instancí v sadě škálování, můžete změnit kapacitu. Automaticky sad škálování vytvoří nebo odstraní požadovaný počet virtuálních počítačů a potom nakonfiguruje virtuální počítače přijímat provoz aplikace.

Nejprve vytvořte objekt škálovací sady pomocí rutiny [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) a pak zadejte novou hodnotu `sku.capacity`. Změnu kapacity použijete pomocí rutiny [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Následující příklad aktualizace *myScaleSet* v *myResourceGroup* skupinu prostředků kapacity *5* instance. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud snížit kapacitu škálování sadu virtuálních počítačů s nejvyšší instance, kterou jsou identifikátory nejdřív odstranit.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zastavit a spustit virtuální počítače ve škálovací sadě
Pokud chcete zastavit jeden nebo několik virtuálních počítačů ve škálovací sadě, použijte rutinu [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny virtuální počítače ve škálovací sadě. Zastavit víc virtuálních počítačů, každý ID instance oddělte čárkou.

Následující příklad zastaví instance *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Ve výchozím nastavení se zastavené virtuální počítače uvolní a neúčtují se u nich poplatky za výpočty. Pokud chcete, aby virtuální počítače po zastavení zůstaly ve zřízeném stavu, přidejte k předchozímu příkazu parametr `-StayProvisioned`. U zastavených virtuálních počítačů, které zůstanou zřízené, se účtují obvyklé poplatky za výpočty.


### <a name="start-vms-in-a-scale-set"></a>Spustit virtuální počítače ve škálovací sadě
Pokud chcete spustit jeden nebo několik virtuálních počítačů ve škálovací sadě, použijte rutinu [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny virtuální počítače ve škálovací sadě. Spustit více virtuálních počítačů, každý ID instance oddělte čárkou.

Následující příklad spustí instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Restartujte virtuální počítače ve škálovací sadě
Pokud chcete restartovat jeden nebo několik virtuálních počítačů ve škálovací sadě, použijte rutinu [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny virtuální počítače ve škálovací sadě. Pokud chcete restartovat víc virtuálních počítačů, oddělte čárkou každé ID instance.

Následující příklad restartuje instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Odebrat ze sady škálování virtuálních počítačů
Chcete-li odebrat jeden nebo více virtuálních počítačů v sadě škálování, použijte [odebrat AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). `-InstanceId` Parametru můžete zadat jednu nebo více virtuálních počítačů k odebrání. Pokud nezadáte instance ID, se odeberou všechny virtuální počítače v sadě škálování. Odebrat víc virtuálních počítačů, každý ID instance oddělte čárkou.

Následující příklad odebere instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Další postup
Zahrnout další běžné úlohy pro sady škálování postup [nasazení aplikace](virtual-machine-scale-sets-deploy-app.md), a [upgrade instance virtuálních počítačů](virtual-machine-scale-sets-upgrade-scale-set.md). Můžete taky prostředí Azure PowerShell [konfigurace pravidel automatického škálování](virtual-machine-scale-sets-autoscale-overview.md).
