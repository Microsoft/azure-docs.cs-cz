---
title: Vytvoření skupiny umístění blízkosti pomocí Azure CLI
description: Seznamte se s vytvářením a používáním skupin umístění blízkosti pro virtuální počítače v Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: a347c9284608340811f9c2388df26129baeb8837
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505643"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Nasazování virtuálních počítačů do skupin umístění bezkontaktní komunikace pomocí Azure CLI

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci [skupiny umístění blízkosti](../co-location.md#proximity-placement-groups).

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.


## <a name="create-the-proximity-placement-group"></a>Vytvořit skupinu umístění blízkosti
Vytvořte skupinu umístění blízkosti pomocí [`az ppg create`](/cli/azure/ppg#az-ppg-create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Seznam skupin umístění blízkosti

Seznam všech skupin umístění do blízkosti můžete zobrazit pomocí [AZ PPG list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač ve skupině umístění blízkosti pomocí [New AZ VM](/cli/azure/vm#az-vm-create).

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

Virtuální počítač ve skupině umístění blízkosti můžete zobrazit pomocí [AZ PPG show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Skupiny dostupnosti
Ve skupině umístění blízkosti můžete také vytvořit skupinu dostupnosti. Použijte stejný `--ppg` parametr s příkazem [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) , aby se vytvořila skupina dostupnosti, a všechny virtuální počítače ve skupině dostupnosti se taky vytvoří ve stejné skupině umístění blízkosti.

## <a name="scale-sets"></a>Škálovací sady

Ve skupině umístění blízkosti můžete také vytvořit sadu škálování. Použijte stejný `--ppg` parametr s příkazem [AZ VMSS Create](/cli/azure/vmss#az_vmss_create) k vytvoření sady škálování a všechny instance se vytvoří ve stejné skupině umístění blízkosti.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o příkazech rozhraní příkazového [řádku Azure CLI](/cli/azure/ppg) pro skupiny umístění pro Proximity.