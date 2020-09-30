---
title: Správa Virtual Machine Scale Sets s využitím Azure PowerShell
description: Běžné rutiny Azure PowerShell pro správu Virtual Machine Scale Sets, jako je například spuštění a zastavení instance nebo změna kapacity sady škálování.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: eee4dd7fae872f6b3ddd01f60aba732edc170766
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570585"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Správa sady škálování virtuálních počítačů pomocí Azure PowerShell

V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Tento článek popisuje některé běžné rutiny Azure PowerShell, které umožňují provádět tyto úlohy.

Pokud potřebujete vytvořit sadu škálování virtuálního počítače, můžete [vytvořit sadu škálování pomocí Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Zobrazit informace o sadě škálování
Chcete-li zobrazit celkové informace o sadě škálování, použijte [příkaz Get-AzVmss](/powershell/module/az.compute/get-azvmss). Následující příklad načte informace o sadě škálování s názvem *myScaleSet* ve skupině prostředků *myResourceGroup* . Zadejte vlastní názvy následujícím způsobem:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Pokud chcete zobrazit seznam instancí virtuálních počítačů v sadě škálování, použijte [příkaz Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Následující příklad vypíše všechny instance virtuálních počítačů v sadě škálování s názvem *myScaleSet* a ve skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty pro tyto názvy:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Pokud chcete zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte `-InstanceId` parametr do [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) a určete instanci, která se má zobrazit. Následující příklad zobrazí informace o instanci virtuálního počítače *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní názvy následujícím způsobem:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Můžete také získat podrobné informace o *instanceView* pro všechny instance v jednom volání rozhraní API, které vám může zabránit omezení velikosti rozhraní API pro velké instalace.

```powershell
Get-AzVmssVM -InstanceView -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Předchozí příkazy ukázaly informace o vaší sadě škálování a instancích virtuálních počítačů. Pokud chcete zvýšit nebo snížit počet instancí v sadě škálování, můžete kapacitu změnit. Sada škálování automaticky vytvoří nebo odebere požadovaný počet virtuálních počítačů a potom nakonfiguruje virtuální počítače pro příjem přenosů aplikací.

Nejprve vytvořte objekt sady škálování pomocí [Get-AzVmss](/powershell/module/az.compute/get-azvmss)a pak zadejte novou hodnotu pro `sku.capacity` . Pokud chcete použít změnu kapacity, použijte [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Následující příklad aktualizuje *myScaleSet* ve skupině prostředků *myResourceGroup* na kapacitu *5* instancí. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud zmenšíte kapacitu sady škálování, virtuální počítače s nejvyšším ID instance se odeberou jako první.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zastavení a spuštění virtuálních počítačů ve škálovací sadě
Pokud chcete zastavit jeden nebo více virtuálních počítačů v sadě škálování, použijte [stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny virtuální počítače ve škálovací sadě. Pokud chcete zastavit více virtuálních počítačů, oddělte jednotlivá ID každé instance čárkou.

Následující příklad zastaví instanci *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Ve výchozím nastavení se zastavené virtuální počítače uvolní a neúčtují se u nich poplatky za výpočty. Pokud chcete, aby virtuální počítače po zastavení zůstaly ve zřízeném stavu, přidejte k předchozímu příkazu parametr `-StayProvisioned`. U zastavených virtuálních počítačů, které zůstanou zřízené, se účtují obvyklé poplatky za výpočty.


### <a name="start-vms-in-a-scale-set"></a>Spuštění virtuálních počítačů v sadě škálování
Pokud chcete spustit jeden nebo více virtuálních počítačů v sadě škálování, použijte [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny virtuální počítače ve škálovací sadě. Pokud chcete spustit více virtuálních počítačů, oddělte jednotlivá ID každé instance čárkou.

Následující příklad spustí instanci *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Restartování virtuálních počítačů ve škálovací sadě
Pokud chcete restartovat jeden nebo víc virtuálních počítačů v sadě škálování, použijte [restart-AzVmss](/powershell/module/az.compute/restart-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny virtuální počítače ve škálovací sadě. Chcete-li restartovat více virtuálních počítačů, oddělte jednotlivá ID každé instance čárkami.

Následující příklad restartuje instanci *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Odebrání virtuálních počítačů ze sady škálování
Pokud chcete odebrat jeden nebo víc virtuálních počítačů v sadě škálování, použijte [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). `-InstanceId`Parametr umožňuje zadat jeden nebo více virtuálních počítačů, které mají být odebrány. Pokud nezadáte ID instance, odeberou se všechny virtuální počítače v sadě škálování. Pokud chcete odebrat víc virtuálních počítačů, oddělte jednotlivá ID každé instance čárkou.

Následující příklad odebere instanci *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Další kroky
Mezi další běžné úlohy pro sady škálování patří postup [nasazení aplikace](virtual-machine-scale-sets-deploy-app.md)a [upgrade instancí virtuálních počítačů](virtual-machine-scale-sets-upgrade-scale-set.md). Ke [konfiguraci pravidel automatického škálování](virtual-machine-scale-sets-autoscale-overview.md)můžete použít taky Azure PowerShell.
