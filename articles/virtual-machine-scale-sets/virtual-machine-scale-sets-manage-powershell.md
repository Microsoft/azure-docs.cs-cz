---
title: Správa Škálovací sady virtuálních počítačů pomocí Azure Powershellu | Dokumentace Microsoftu
description: Běžné rutin Azure Powershellu ke správě Škálovací sady virtuálních počítačů, jako je spuštění a zastavení instance nebo změna nastavení kapacity.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 5746d8b1f4c12a9b39f1599da753db8109790a55
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984126"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Správa virtuálního počítače škálovací sady pomocí Azure Powershellu

V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Tento článek podrobně popisuje některé běžné rutin Azure Powershellu, které vám umožňují provádět tyto úlohy.

Pokud je potřeba vytvořit škálovací sadu virtuálních počítačů, můžete si [vytvoření škálovací sady pomocí Azure Powershellu](quick-create-powershell.md).

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

## <a name="view-information-about-a-scale-set"></a>Zobrazit informace o škálovací sady
Chcete-li zobrazit celkové informace o škálovací sadě, použijte [Get-AzVmss](/powershell/module/az.compute/get-azvmss). Následující příklad získá informace o škálovací sadu s názvem *myScaleSet* v *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Chcete-li zobrazit seznam instancí virtuálních počítačů ve škálovací sadě, použijte [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Následující příklad zobrazí seznam všech instancí virtuálních počítačů ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty pro tyto názvy:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Chcete-li zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte `-InstanceId` parametr [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) a určete instanci pro zobrazení. Následující příklad zobrazí informace o instanci virtuálního počítače *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Předchozí příkazy jsme si ukázali, informace o škálovací sady a instancí virtuálních počítačů. Chcete-li zvýšit nebo snížit počet instancí ve škálovací sadě, můžete změnit kapacitu. Škálovací sady automaticky vytvoří nebo odebere požadovaný počet virtuálních počítačů a pak nakonfiguruje virtuální počítače pro příjem provozu aplikací.

Nejprve vytvořte objekt škálovací sady s [Get-AzVmss](/powershell/module/az.compute/get-azvmss), zadejte novou hodnotu pro `sku.capacity`. Chcete-li použít změnu kapacity, použijte [aktualizace AzVmss](/powershell/module/az.compute/update-azvmss). Následující příklad aktualizace *myScaleSet* v *myResourceGroup* skupinu prostředků, která kapacitou *5* instancí. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud snížit kapacitu škálovací nastaven, virtuální počítače s nejvyšším instanci, kterou ID nejprve se odeberou.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zastavení a spuštění virtuálních počítačů ve škálovací sadě
Pokud chcete zastavit jeden nebo více virtuálních počítačů ve škálovací sadě, použijte [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny virtuální počítače ve škálovací sadě. Zastavit několika virtuálních počítačů, každá instance ID oddělte čárkou.

Následující příklad zastaví instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Ve výchozím nastavení se zastavené virtuální počítače uvolní a neúčtují se u nich poplatky za výpočty. Pokud chcete, aby virtuální počítače po zastavení zůstaly ve zřízeném stavu, přidejte k předchozímu příkazu parametr `-StayProvisioned`. U zastavených virtuálních počítačů, které zůstanou zřízené, se účtují obvyklé poplatky za výpočty.


### <a name="start-vms-in-a-scale-set"></a>Spuštění virtuálních počítačů ve škálovací sadě
Chcete-li spustit jeden nebo více virtuálních počítačů ve škálovací sadě, použijte [Start AzVmss](/powershell/module/az.compute/start-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny virtuální počítače ve škálovací sadě. Spuštění několika virtuálních počítačů, každá instance ID oddělte čárkou.

Následující příklad spustí instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Restartování virtuálních počítačů ve škálovací sadě
Chcete-li restartovat jeden nebo více virtuálních počítačů ve škálovací sadě, použijte [rutinu AzVmss](/powershell/module/az.compute/restart-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny virtuální počítače ve škálovací sadě. Restartovat víc virtuálních počítačů, každá instance ID oddělte čárkou.

Následující příklad restartuje instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Odebrat ze škálovací sady virtuálních počítačů
Chcete-li odebrat jednu nebo více virtuálních počítačů ve škálovací sadě, použijte [odebrat AzVmss](/powershell/module/az.compute/remove-azvmss). `-InstanceId` Parametrů můžete zadat jednu nebo více virtuálních počítačů odebrat. Pokud nezadáte instance ID, odeberou se všechny virtuální počítače ve škálovací sadě. Odebrání více virtuálních počítačů, každá instance ID oddělte čárkou.

Následující příklad odebere instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Další postup
Další běžné úlohy pro škálovací sady obsahovat jak [nasazení aplikace](virtual-machine-scale-sets-deploy-app.md), a [upgrade instancí virtuálních počítačů](virtual-machine-scale-sets-upgrade-scale-set.md). Můžete také použít Azure PowerShell k [konfigurace pravidel automatického škálování](virtual-machine-scale-sets-autoscale-overview.md).
