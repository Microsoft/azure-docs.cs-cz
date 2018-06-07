---
title: Správa sady škálování virtuálního počítače pomocí Azure CLI 2.0 | Microsoft Docs
description: Běžné Azure CLI 2.0 příkazy ke správě sady škálování virtuálního počítače, jako je například postup spuštění a zastavení instance, nebo změnit měřítko nastavená kapacita.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: iainfou
ms.openlocfilehash: ca447f3ca0ed6656912a0d3e5082ebd2dd308a14
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652480"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Spravovat škálování virtuálních počítačů, nastavit pomocí Azure CLI 2.0
V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Tento článek podrobně popisuje některé běžné příkazy Azure CLI 2.0, které umožňují provádět tyto úlohy.

K dokončení těchto úloh správy, je třeba nejnovější 2.0 rozhraní příkazového řádku Azure. Informace najdete v tématu [nainstalovat Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud potřebujete vytvořit škálovací sadu virtuálních počítačů, můžete [vytvořit škálování s Azure CLI 2.0](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Zobrazit informace o sadě škálování
Chcete-li zobrazit celkový informace o sadě škálování, použijte [az vmss zobrazit](/cli/azure/vmss#az_vmss_show). Následující příklad získá informace o škálování nastavení s názvem *myScaleSet* v *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Chcete-li zobrazit seznam instance virtuálního počítače ve škálovací sadě, použijte [az vmss seznamu instance](/cli/azure/vmss#list-instances). Následující příklad vypíše všechny instance virtuálních počítačů v pojmenované sad škálování *myScaleSet* v *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty pro tyto názvy:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Chcete-li zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte `--instance-id` parametru [az vmss get--zobrazení instance](/cli/azure/vmss#get-instance-view) a zadejte instanci zobrazení. Následující příklad zobrazení informace o instanci virtuálního počítače *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Seznam informací o připojení pro virtuální počítače
Chcete-li se připojit k virtuální počítače ve škálovací sadě, SSH nebo protokolu RDP na přiřazenou veřejnou IP adresu a číslo portu. Ve výchozím nastavení jsou pravidla překladu adres sítě přidán do nástroje pro vyrovnávání zatížení Azure, který předává přenos vzdáleného připojení pro každý virtuální počítač. K zobrazení seznamu adresy a porty pro připojení k instancím virtuálního počítače ve škálovací sadě, použijte [az vmss seznamu--připojení-informace o instanci](/cli/azure/vmss#list-instance-connection-info). Následující příklad uvádí informace o připojení pro instance virtuálních počítačů v pojmenované sad škálování *myScaleSet* a v *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty pro tyto názvy:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Předchozí příkazy vám ukázal, informace o vaší sadě škálování a instancí virtuálních počítačů. Chcete-li zvýšit nebo snížit počet instancí v sadě škálování, můžete změnit kapacitu. Škálovací sadu vytvoří nebo odstraní požadovaný počet virtuálních počítačů a potom nakonfiguruje virtuální počítače přijímat provoz aplikace.

Pokud chcete zobrazit počet instancí, které aktuálně máte ve škálovací sadě, použijte příkaz [az vmss show](/cli/azure/vmss#az_vmss_show) s dotazem na *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Pak můžete ručně navýšit nebo snížit počet virtuálních počítačů ve škálovací sadě pomocí příkazu [az vmss scale](/cli/azure/vmss#az_vmss_scale). Následující příklad nastaví počet virtuálních počítačů ve vaší škálování nastavena na *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud snížit kapacitu škálování sadu virtuálních počítačů s nejvyšší instance, kterou jsou identifikátory nejdřív odstranit.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zastavit a spustit virtuální počítače ve škálovací sadě
Chcete-li ukončit jeden nebo více virtuálních počítačů v sadě škálování, použijte [az vmss zastavení](/cli/azure/vmss/stop). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny virtuální počítače ve škálovací sadě. Zastavit víc virtuálních počítačů, každý ID instance oddělte mezerou.

Následující příklad zastaví instance *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Zastavený virtuální počítače zůstanou přidělené a nadále toho vám být účtovány poplatky za výpočty. Pokud místo toho chcete virtuální počítače k zrušení přiřazení a pouze toho vám být účtovány poplatky za úložiště, použijte [navrácení az vmss](/cli/azure/vmss#az_vmss_deallocate). Zrušit přidělení více virtuálních počítačů, každý ID instance oddělte mezerou. V následujícím příkladu se zastaví a zruší přidělení instance *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Spustit virtuální počítače ve škálovací sadě
Chcete-li spustit jeden nebo více virtuálních počítačů v sadě škálování, použijte [spustit az vmss](/cli/azure/vmss#az_vmss_start). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny virtuální počítače ve škálovací sadě. Spustit více virtuálních počítačů, každý ID instance oddělte mezerou.

Následující příklad spustí instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Restartujte virtuální počítače ve škálovací sadě
Chcete-li restartovat jeden nebo více virtuálních počítačů v sadě škálování, použijte [restartujte az vmss](/cli/azure/vmss#az_vmss_restart). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny virtuální počítače ve škálovací sadě. Pokud chcete restartovat víc virtuálních počítačů, oddělte mezerou každé ID instance.

Následující příklad restartuje instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Odebrat ze sady škálování virtuálních počítačů
Chcete-li odebrat jeden nebo více virtuálních počítačů v sadě škálování, použijte [az vmss delete instance](/cli/azure/vmss#delete-instances). `--instance-ids` Parametru můžete zadat jednu nebo více virtuálních počítačů k odebrání. Pokud zadáte * pro instanci jsou odebrány ID, všechny virtuální počítače v sadě škálování. Odebrat víc virtuálních počítačů, každý ID instance oddělte mezerou.

Následující příklad odebere instanci *0* v pojmenované sad škálování *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Další postup
Zahrnout další běžné úlohy pro sady škálování postup [nasazení aplikace](virtual-machine-scale-sets-deploy-app.md), a [upgrade instance virtuálních počítačů](virtual-machine-scale-sets-upgrade-scale-set.md). Můžete také použít rozhraní příkazového řádku Azure pro [konfigurace pravidel automatického škálování](virtual-machine-scale-sets-autoscale-overview.md).
