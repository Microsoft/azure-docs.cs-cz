---
title: Ukázkový skript Azure CLI – Vytvoření dvou virtuálních počítačů s interní a externí skupinou zabezpečení sítě | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření dvou virtuálních počítačů s interní a externí skupinou zabezpečení sítě
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: a74eead266451d15ea22538e19c5f12bf04c08b8
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Zabezpečení provozu sítě mezi virtuálními počítači

Tento skript vytvoří dva virtuální počítače a zabezpečí příchozí provoz do obou z nich. Jeden virtuální počítač je přístupný na internetu a má nakonfigurovanou skupinu zabezpečení sítě (NSG), která povoluje provoz na portu 3389 a 80. Druhý virtuální počítač není přístupný na internetu a má nakonfigurovanou skupinu NSG, která povoluje pouze provoz z prvního virtuálního počítače. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a podsíť. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Vytvoří podsíť. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_update) | Aktualizuje pravidlo NSG. V této ukázce se back-endové pravidlo aktualizuje tak, aby přes něj procházel pouze provoz z front-endové podsítě. |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_list) | Vrátí informace o pravidle skupiny zabezpečení sítě. V této ukázce se název pravidla uloží do proměnné pro použití v pozdější části skriptu. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
