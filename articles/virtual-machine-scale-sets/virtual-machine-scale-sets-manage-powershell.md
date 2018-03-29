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
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: c463dd26c106b3178becc977a8afd742220d7973
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Spravovat škálování virtuálních počítačů, nastavit pomocí prostředí Azure PowerShell
V průběhu cyklu škálovací sadu virtuálních počítačů můžete spustit jeden nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Tento článek podrobně popisuje některé běžné rutin prostředí Azure PowerShell, které umožňují provádět tyto úlohy.

K dokončení těchto úloh správy, je třeba nejnovější modul Azure PowerShell. Informace najdete v tématu [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps). Pokud potřebujete vytvořit škálovací sadu virtuálních počítačů, můžete [vytvořit měřítko nastavit pomocí prostředí Azure PowerShell](quick-create-powershell.md).


## <a name="view-information-about-a-scale-set"></a>Zobrazit informace o sadě škálování
Chcete-li zobrazit celkový informace o sadě škálování, použijte [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). Následující příklad získá informace o škálování nastavení s názvem *myScaleSet* v *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Chcete-li zobrazit seznam instance virtuálního počítače ve škálovací sadě, použijte [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Následující příklad zobrazí seznam všech instancí virtuálních počítačů v pojmenované sad škálování *myScaleSet* a v *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty pro tyto názvy:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Chcete-li zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte `-InstanceId` parametru [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) a zadejte instanci zobrazení. Následující příklad zobrazení informace o instanci virtuálního počítače *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sadě
Předchozí příkazy vám ukázal, informace o vaší sadě škálování a instancí virtuálních počítačů. Chcete-li zvýšit nebo snížit počet instancí v sadě škálování, můžete změnit kapacitu. Automaticky sad škálování vytvoří nebo odstraní požadovaný počet virtuálních počítačů a potom nakonfiguruje virtuální počítače přijímat provoz aplikace.

Nejprve vytvořte objekt sady škálování s [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), pak zadejte novou hodnotu `sku.capacity`. Na použití změny kapacitu, použijte [aktualizace AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Následující příklad aktualizace *myScaleSet* v *myResourceGroup* skupinu prostředků kapacity *5* instance. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Pokud se používá několik minut aktualizovat kapacitu vaší škálování nastavit. Pokud snížit kapacitu škálování sadu virtuálních počítačů s nejvyšší instance, kterou jsou identifikátory nejdřív odstranit.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zastavit a spustit virtuální počítače ve škálovací sadě
Chcete-li ukončit jeden nebo více virtuálních počítačů v sadě škálování, použijte [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). `-InstanceId` Parametru můžete zadat jednu nebo více virtuálních počítačů k zastavení. Pokud nezadáte instance ID, zastaví se všechny virtuální počítače v sadě škálování. Zastavit víc virtuálních počítačů, každý ID instance oddělte čárkou.

Následující příklad zastaví instance *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte hodnoty takto:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Ve výchozím nastavení zastavený virtuální počítače jsou navrácena a nevznikají poplatky za výpočty. Pokud chcete virtuálnímu počítači zůstat v zřízené stavu, když se zastavil, přidejte `-StayProvisioned` parametru předchozí příkaz. Zastavený virtuální počítače, které zůstanou zřízené platit poplatky regulární výpočty.


### <a name="start-vms-in-a-scale-set"></a>Spustit virtuální počítače ve škálovací sadě
Chcete-li spustit jeden nebo více virtuálních počítačů v sadě škálování, použijte [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). `-InstanceId` Parametru můžete zadat jednu nebo více virtuálních počítačů spustit. Pokud nezadáte instance ID, jsou spuštěny všechny virtuální počítače v sadě škálování. Spustit více virtuálních počítačů, každý ID instance oddělte čárkou.

Následující příklad spustí instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte hodnoty takto:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Restartujte virtuální počítače ve škálovací sadě
Chcete-li restartovat jeden nebo více virtuálních počítačů v sadě škálování, použijte [Retart AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). `-InstanceId` Parametru můžete zadat jednu nebo více virtuálních počítačů k restartování. Pokud nezadáte instance ID, všechny virtuální počítače v sadě škálování se restartuje. Pokud chcete restartovat víc virtuálních počítačů, oddělte čárkou každé ID instance.

Následující příklad restartuje instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte hodnoty takto:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Odebrat ze sady škálování virtuálních počítačů
Chcete-li odebrat jeden nebo více virtuálních počítačů v sadě škálování, použijte [odebrat AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). `-InstanceId` Parametru můžete zadat jednu nebo více virtuálních počítačů k odebrání. Pokud nezadáte instance ID, se odeberou všechny virtuální počítače v sadě škálování. Odebrat víc virtuálních počítačů, každý ID instance oddělte čárkou.

Následující příklad odebere instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte hodnoty takto:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Další postup
Zahrnout další běžné úlohy pro sady škálování postup [nasazení aplikace](virtual-machine-scale-sets-deploy-app.md), a [upgrade instance virtuálních počítačů](virtual-machine-scale-sets-upgrade-scale-set.md). Můžete taky prostředí Azure PowerShell [konfigurace pravidel automatického škálování](virtual-machine-scale-sets-autoscale-overview.md).
