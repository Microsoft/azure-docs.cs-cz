---
title: Použití skupin umístění bezkontaktní komunikace
description: Přečtěte si o vytváření a používání skupin umístění bezkontaktní komunikace pro virtuální počítače v Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: f89b28e7a3c29e45efa2796788e27325c01d7098
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759249"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Nasazení virtuálních počítačů do skupin umístění bezkontaktní komunikace pomocí azure cli

Chcete-li získat virtuální chod co nejblíže a dosáhnout nejnižší možné latence, měli byste je nasadit v [rámci skupiny umístění bez kontaktní místo](co-location.md#proximity-placement-groups).

Skupina umístění bezkontaktní komunikace je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěny blízko sebe. Skupiny umístění bezkontaktní komunikace jsou užitečné pro úlohy, kde je požadavek nízké latence.


## <a name="create-the-proximity-placement-group"></a>Vytvoření skupiny umístění bezkontaktní komunikace
Vytvořte skupinu umístění [`az ppg create`](/cli/azure/ppg#az-ppg-create)bezkontaktní komunikace pomocí aplikace . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Seznam skupin umístění bezkontaktní komunikace

Můžete zobrazit seznam všech skupin umístění bezkontaktní komunikace pomocí [seznamu az ppg](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální hovado v rámci skupiny umístění bezkontaktní pomocí [nového virtuálního virtuálního bodu](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Virtuální obraz můžete zobrazit ve skupině umístění bezkontaktní komunikace pomocí [az ppg show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Skupiny dostupnosti
Můžete také vytvořit sadu dostupnosti ve skupině umístění bezkontaktní chod. Použijte stejný `--ppg` parametr s [az vm dostupnost nastavit vytvořit](/cli/azure/vm/availability-set#az-vm-availability-set-create) vytvořit sadu dostupnosti a všechny virtuální chody v sadě dostupnosti bude také vytvořen ve stejné skupině umístění bezkontaktní.

## <a name="scale-sets"></a>Škálovací sady

Můžete také vytvořit škálovací sadu ve skupině umístění bezkontaktní chod. Použijte stejný `--ppg` parametr s [az vmss vytvořit](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) vytvořit škálovací sadu a všechny instance budou vytvořeny ve stejné skupině umístění bezkontaktní.

## <a name="next-steps"></a>Další kroky

Další informace o příkazech [Azure CLI](/cli/azure/ppg) pro skupiny umístění bezkontaktní komunikace.
