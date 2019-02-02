---
title: Správa Škálovací sady virtuálních počítačů pomocí Azure CLI | Dokumentace Microsoftu
description: Běžné příkazy Azure CLI ke správě Škálovací sady virtuálních počítačů, jako je spuštění a zastavení instance nebo změna nastavení kapacity.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: e8a41c3bb1401552e7f1a56ec4cdbbce52354075
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658925"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Správa virtuálního počítače škálovací sady pomocí Azure CLI
V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Tento článek podrobně popisuje některé běžné příkazy rozhraní příkazového řádku Azure, které umožňují provádět tyto úlohy.

K dokončení těchto úloh správy, potřebujete nejnovější rozhraní příkazového řádku Azure. Informace najdete v tématu [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli). Pokud je potřeba vytvořit škálovací sadu virtuálních počítačů, můžete si [vytvoření škálovací sady pomocí Azure CLI](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Zobrazit informace o škálovací sady
Chcete-li zobrazit celkové informace o škálovací sadě, použijte [az vmss show](/cli/azure/vmss#az_vmss_show). Následující příklad získá informace o škálovací sadu s názvem *myScaleSet* v *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Chcete-li zobrazit seznam instancí virtuálních počítačů ve škálovací sadě, použijte [az vmss list-instances](/cli/azure/vmss). Následující příklad zobrazí seznam všech instancí virtuálních počítačů ve škálovací sadě s názvem *myScaleSet* v *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty pro tyto názvy:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Chcete-li zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte `--instance-id` parametr [az vmss get-instance-view](/cli/azure/vmss) a určete instanci pro zobrazení. Následující příklad zobrazí informace o instanci virtuálního počítače *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní názvy následujícím způsobem:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Výpis informací o připojení pro virtuální počítače
Pro připojení k virtuálním počítačům ve škálovací sadě, můžete SSH nebo RDP k přiřazenou veřejnou IP adresu a číslo portu. Ve výchozím nastavení pravidla překladu síťových adres přidají k nástroji pro vyrovnávání zatížení Azure, který směruje provoz vzdáleného připojení pro každý virtuální počítač. Chcete-li vypsat adresu a porty pro připojení k instancím virtuálních počítačů ve škálovací sadě, použijte [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Následující příklad Vypíše informace o připojení pro instance virtuálních počítačů ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty pro tyto názvy:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Předchozí příkazy jsme si ukázali, informace o škálovací sady a instancí virtuálních počítačů. Chcete-li zvýšit nebo snížit počet instancí ve škálovací sadě, můžete změnit kapacitu. Škálovací sada vytvoří nebo odebere požadovaný počet virtuálních počítačů a pak nakonfiguruje virtuální počítače pro příjem provozu aplikací.

Pokud chcete zobrazit počet instancí, které aktuálně máte ve škálovací sadě, použijte příkaz [az vmss show](/cli/azure/vmss#az_vmss_show) s dotazem na *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Pak můžete ručně navýšit nebo snížit počet virtuálních počítačů ve škálovací sadě pomocí příkazu [az vmss scale](/cli/azure/vmss). Následující příklad nastaví počet virtuálních počítačů ve škálovací sadě *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud snížit kapacitu škálovací nastaven, virtuální počítače s nejvyšším instanci, kterou ID nejprve se odeberou.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zastavení a spuštění virtuálních počítačů ve škálovací sadě
Pokud chcete zastavit jeden nebo více virtuálních počítačů ve škálovací sadě, použijte [az vmss stop](/cli/azure/vmss#az-vmss-stop). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny virtuální počítače ve škálovací sadě. Zastavit několika virtuálních počítačů, každá instance ID oddělte mezerou.

Následující příklad zastaví instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Zastavených virtuálních počítačů zůstanou přidělené a nadále účtovat poplatky za výpočty. Pokud místo toho chcete virtuální počítače k zrušení přiřazení a pouze se vám účtovat poplatky za úložiště, použijte [az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate). Zrušit přidělení více virtuálních počítačů, každá instance ID oddělte mezerou. Následující příklad zastaví a uvolní instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Spuštění virtuálních počítačů ve škálovací sadě
Chcete-li spustit jeden nebo více virtuálních počítačů ve škálovací sadě, použijte [az vmss start](/cli/azure/vmss). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny virtuální počítače ve škálovací sadě. Spuštění několika virtuálních počítačů, každá instance ID oddělte mezerou.

Následující příklad spustí instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Restartování virtuálních počítačů ve škálovací sadě
Chcete-li restartovat jeden nebo více virtuálních počítačů ve škálovací sadě, použijte [az vmss restart](/cli/azure/vmss#az_vmss_restart). Pomocí parametru `--instance-ids` můžete zadat jeden nebo několik virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny virtuální počítače ve škálovací sadě. Restartovat víc virtuálních počítačů, každá instance ID oddělte mezerou.

Následující příklad restartuje instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Odebrat ze škálovací sady virtuálních počítačů
Chcete-li odebrat jednu nebo více virtuálních počítačů ve škálovací sadě, použijte [az vmss delete-instances](/cli/azure/vmss#delete-instances). `--instance-ids` Parametrů můžete zadat jednu nebo více virtuálních počítačů odebrat. Pokud zadáte * pro instanci ID, všechny virtuální počítače ve škálovací sadě se odeberou. Odebrání více virtuálních počítačů, každá instance ID oddělte mezerou.

Následující příklad odebere instanci *0* ve škálovací sadě s názvem *myScaleSet* a *myResourceGroup* skupinu prostředků. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Další postup
Další běžné úlohy pro škálovací sady obsahovat jak [nasazení aplikace](virtual-machine-scale-sets-deploy-app.md), a [upgrade instancí virtuálních počítačů](virtual-machine-scale-sets-upgrade-scale-set.md). Můžete také použít rozhraní příkazového řádku Azure k [konfigurace pravidel automatického škálování](virtual-machine-scale-sets-autoscale-overview.md).
