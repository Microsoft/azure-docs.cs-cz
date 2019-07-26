---
title: Použití náhledu skupin umístění blízkosti pro virtuální počítače se systémem Linux | Microsoft Docs
description: Seznamte se s vytvářením a používáním skupin umístění blízkosti pro virtuální počítače se systémem Linux v Azure.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: a3009c9863d8322e3fe34bd99d64b93f0aa3d858
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385731"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Verze Preview: Nasazení virtuálních počítačů do skupin umístění pro Proximity pomocí Azure CLI

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci [skupiny umístění blízkosti](co-location.md#preview-proximity-placement-groups).

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.

> [!IMPORTANT]
> Skupiny umístění blízkosti jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Skupiny umístění blízkosti nejsou v těchto oblastech k dispozici v průběhu verze Preview: **Japonsko – východ**, **Austrálie – východ** a **Indie – střed**

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

## <a name="availability-sets"></a>Sady dostupnosti
Ve skupině umístění blízkosti můžete také vytvořit skupinu dostupnosti. Použijte stejný `--ppg` parametr s příkazem [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) , aby se vytvořila skupina dostupnosti, a všechny virtuální počítače ve skupině dostupnosti se taky vytvoří ve stejné skupině umístění blízkosti.

## <a name="scale-sets"></a>Škálovací sady

Ve skupině umístění blízkosti můžete také vytvořit sadu škálování. Použijte stejný `--ppg` parametr s příkazem [AZ VMSS Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) k vytvoření sady škálování a všechny instance se vytvoří ve stejné skupině umístění blízkosti.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o příkazech rozhraní příkazového [řádku Azure CLI](/cli/azure/ppg) pro skupiny umístění pro Proximity.
