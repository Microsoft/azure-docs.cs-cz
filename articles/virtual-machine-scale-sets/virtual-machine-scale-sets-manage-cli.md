---
title: Správa Virtual Machine Scale Sets pomocí Azure CLI
description: Běžné příkazy rozhraní příkazového řádku Azure pro správu Virtual Machine Scale Sets, jako je například spuštění a zastavení instance nebo změna kapacity sady škálování.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d954f7cdda4cae65f822489828226e0364d0fc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570526"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Správa sady škálování virtuálních počítačů pomocí Azure CLI
V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Tento článek podrobně popisuje některé běžné příkazy rozhraní příkazového řádku Azure CLI, které umožňují provádět tyto úlohy.

K dokončení těchto úloh správy potřebujete nejnovější rozhraní příkazového řádku Azure. Informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Pokud potřebujete vytvořit sadu škálování virtuálního počítače, můžete [vytvořit sadu škálování pomocí Azure CLI](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Zobrazit informace o sadě škálování
Chcete-li zobrazit celkové informace o sadě škálování, použijte příkaz [AZ VMSS show](/cli/azure/vmss). Následující příklad načte informace o sadě škálování s názvem *myScaleSet* ve skupině prostředků *myResourceGroup* . Zadejte vlastní názvy následujícím způsobem:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Pokud chcete zobrazit seznam instancí virtuálních počítačů v sadě škálování, použijte příkaz [AZ VMSS list-Instances](/cli/azure/vmss). Následující příklad vypíše všechny instance virtuálních počítačů ve skupině škálování s názvem *myScaleSet* ve skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty pro tyto názvy:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Chcete-li zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte `--instance-id` parametr pro [AZ VMSS Get-instance-View](/cli/azure/vmss) a určete instanci, která se má zobrazit. Následující příklad zobrazí informace o instanci virtuálního počítače *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní názvy následujícím způsobem:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Můžete také získat podrobné informace o *instanceView* pro všechny instance v jednom volání rozhraní API, které vám může zabránit omezení velikosti rozhraní API pro velké instalace. Zadejte vlastní hodnoty pro `--resource-group` , `--subscription` a `--name` .

```azurecli
az vmss list-instances \
    --expand instanceView \
    --select instanceView \
    --resource-group <resourceGroupName> \
    --subscription <subID> \
    --name <vmssName>
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="list-connection-information-for-vms"></a>Vypsat informace o připojení pro virtuální počítače
Pokud se chcete připojit k virtuálním počítačům v sadě škálování, budete přes SSH nebo RDP přiřazenou veřejnou IP adresu a číslo portu. Ve výchozím nastavení se pravidla překladu adres (NAT) přidávají do nástroje pro vyrovnávání zatížení Azure, který předává přenosy vzdáleného připojení do každého virtuálního počítače. Pokud chcete vypsat adresu a porty pro připojení k instancím virtuálních počítačů v sadě škálování, použijte příkaz [AZ VMSS list-instance-Connection-info](/cli/azure/vmss). Následující příklad vypíše informace o připojení pro instance virtuálních počítačů v sadě škálování s názvem *myScaleSet* a ve skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty pro tyto názvy:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Předchozí příkazy ukázaly informace o vaší sadě škálování a instancích virtuálních počítačů. Pokud chcete zvýšit nebo snížit počet instancí v sadě škálování, můžete kapacitu změnit. Sada škálování vytvoří nebo odebere požadovaný počet virtuálních počítačů a potom nakonfiguruje virtuální počítače pro příjem přenosů aplikací.

Pokud chcete zobrazit počet instancí, které aktuálně máte ve škálovací sadě, použijte příkaz [az vmss show](/cli/azure/vmss) s dotazem na *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Pak můžete ručně navýšit nebo snížit počet virtuálních počítačů ve škálovací sadě pomocí příkazu [az vmss scale](/cli/azure/vmss). Následující příklad nastaví počet virtuálních počítačů ve vašem měřítku nastavených na *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud zmenšíte kapacitu sady škálování, virtuální počítače s nejvyšším ID instance se odeberou jako první.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zastavení a spuštění virtuálních počítačů ve škálovací sadě
Pokud chcete zastavit jeden nebo více virtuálních počítačů v sadě škálování, použijte příkaz [AZ VMSS stop](/cli/azure/vmss#az-vmss-stop). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny virtuální počítače ve škálovací sadě. Pokud chcete zastavit více virtuálních počítačů, oddělte každé ID instance mezerou.

Následující příklad zastaví instanci *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Zastavené virtuální počítače zůstanou přidělené a nadále se účtují poplatky za výpočetní výkon. Pokud místo toho chcete, aby se virtuální počítače nadělily a platily jenom poplatky za úložiště, použijte příkaz [AZ VMSS unallocate](/cli/azure/vmss). Chcete-li uvolnit více virtuálních počítačů, oddělte každé ID instance mezerou. Následující příklad zastaví a zruší přidělení instance *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Spuštění virtuálních počítačů v sadě škálování
Pokud chcete spustit jeden nebo více virtuálních počítačů v sadě škálování, použijte příkaz [AZ VMSS Start](/cli/azure/vmss). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny virtuální počítače ve škálovací sadě. Pokud chcete spustit více virtuálních počítačů, oddělte každé ID instance mezerou.

Následující příklad spustí instanci *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Restartování virtuálních počítačů ve škálovací sadě
Pokud chcete restartovat jeden nebo víc virtuálních počítačů v sadě škálování, použijte příkaz [AZ VMSS restart](/cli/azure/vmss). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny virtuální počítače ve škálovací sadě. Chcete-li restartovat více virtuálních počítačů, oddělte každé ID instance mezerou.

Následující příklad restartuje instanci *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Odebrání virtuálních počítačů ze sady škálování
Pokud chcete odebrat jeden nebo víc virtuálních počítačů v sadě škálování, použijte příkaz [AZ VMSS Delete-Instances](/cli/azure/vmss). `--instance-ids`Parametr umožňuje zadat jeden nebo více virtuálních počítačů, které mají být odebrány. Pokud zadáte * pro ID instance, odeberou se všechny virtuální počítače v sadě škálování. Chcete-li odebrat více virtuálních počítačů, oddělte každé ID instance mezerou.

Následující příklad odebere instanci *0* v sadě škálování s názvem *myScaleSet* a skupině prostředků *myResourceGroup* . Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Další kroky
Mezi další běžné úlohy pro sady škálování patří postup [nasazení aplikace](virtual-machine-scale-sets-deploy-app.md)a [upgrade instancí virtuálních počítačů](virtual-machine-scale-sets-upgrade-scale-set.md). Ke [konfiguraci pravidel automatického škálování](virtual-machine-scale-sets-autoscale-overview.md)můžete použít taky Azure CLI.
