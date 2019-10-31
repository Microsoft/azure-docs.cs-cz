---
title: Použití skupin umístění blízkosti pro virtuální počítače se systémem Linux
description: Seznamte se s vytvářením a používáním skupin umístění blízkosti pro virtuální počítače se systémem Linux v Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171048"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Nasazení virtuálních počítačů do skupin umístění pro Proximity pomocí Azure CLI

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci [skupiny umístění blízkosti](co-location.md#proximity-placement-groups).

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.


## <a name="create-the-proximity-placement-group"></a>Vytvořit skupinu umístění blízkosti
Vytvořte skupinu umístění blízkosti pomocí [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

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
Ve skupině umístění blízkosti můžete také vytvořit skupinu dostupnosti. Použijte stejný parametr `--ppg` s příkazem [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) , aby se vytvořila skupina dostupnosti, a všechny virtuální počítače ve skupině dostupnosti se taky vytvoří ve stejné skupině umístění blízkosti.

## <a name="scale-sets"></a>Škálovací sady

Ve skupině umístění blízkosti můžete také vytvořit sadu škálování. Použijte stejný parametr `--ppg` s příkazem [AZ VMSS Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) k vytvoření sady škálování a všechny instance se vytvoří ve stejné skupině umístění blízkosti.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o příkazech rozhraní příkazového [řádku Azure CLI](/cli/azure/ppg) pro skupiny umístění pro Proximity.
