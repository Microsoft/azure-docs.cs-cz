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
ms.openlocfilehash: e4f91afa86a0d99b4ce42e96295bf2ae1f9fcd9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771438"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Nasazování virtuálních počítačů do skupin umístění bezkontaktní komunikace pomocí Azure CLI

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci [skupiny umístění blízkosti](../co-location.md#proximity-placement-groups).

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.


## <a name="create-the-proximity-placement-group"></a>Vytvořit skupinu umístění blízkosti
Vytvořte skupinu umístění blízkosti pomocí [`az ppg create`](/cli/azure/ppg#az_ppg_create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Seznam skupin umístění blízkosti

Seznam všech skupin umístění do blízkosti můžete zobrazit pomocí [AZ PPG list](/cli/azure/ppg#az_ppg_list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač ve skupině umístění blízkosti pomocí [New AZ VM](/cli/azure/vm#az_vm_create).

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

Virtuální počítač ve skupině umístění blízkosti můžete zobrazit pomocí [AZ PPG show](/cli/azure/ppg#az_ppg_show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Skupiny dostupnosti
Ve skupině umístění blízkosti můžete také vytvořit skupinu dostupnosti. Použijte stejný `--ppg` parametr s příkazem [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az_vm_availability_set_create) , aby se vytvořila skupina dostupnosti, a všechny virtuální počítače ve skupině dostupnosti se taky vytvoří ve stejné skupině umístění blízkosti.

## <a name="scale-sets"></a>Škálovací sady

Ve skupině umístění blízkosti můžete také vytvořit sadu škálování. Použijte stejný `--ppg` parametr s příkazem [AZ VMSS Create](/cli/azure/vmss#az_vmss_create) k vytvoření sady škálování a všechny instance se vytvoří ve stejné skupině umístění blízkosti.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o příkazech rozhraní příkazového [řádku Azure CLI](/cli/azure/ppg) pro skupiny umístění pro Proximity.