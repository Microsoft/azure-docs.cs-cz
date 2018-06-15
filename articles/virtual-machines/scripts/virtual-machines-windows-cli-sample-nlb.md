---
title: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače s Windows Serverem 2016 a NLB | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače s Windows Serverem 2016 a NLB
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: 55a8d44e34b8b0b6b238c5ada3d6065d86caca15
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654534"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Vyrovnávání zatížení provozu mezi vysoce dostupnými virtuálními počítači

Tento ukázkový skript vytvoří vše potřebné pro spuštění několika virtuálních počítačů s Ubuntu v konfiguraci s vysokou dostupností a vyrovnáváním zatížení. Po spuštění skriptu budete mít tři virtuální počítače připojené ke skupině dostupnosti Azure a přístupné přes službu Azure Load Balancer.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-windows-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače, skupiny dostupnosti, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a podsíť. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu se statickou IP adresou a přidruženým názvem DNS. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Vytvoří Azure Network Load Balancer (NLB). |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Vytvoří sondu NLB. Sonda NLB slouží k monitorování všech virtuálních počítačů v sadě NLB. Pokud nějaký virtuální počítač přestane být přístupný, nebude se do něj směrovat provoz. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří pravidlo NLB. V této ukázce se vytvoří pravidlo pro port 80. Když NLB přijme přenos HTTP, přesměruje ho na port 80 některého z virtuálních počítačů v sadě NLB. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Vytvoří pravidlo překladu adres (NAT) NLB.  Pravidla NAT mapují port NLB na port na virtuálním počítači. V této ukázce se vytvoří pravidlo NAT pro provoz SSH do všech virtuálních počítačů v sadě NLB.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Vytvoří skupinu zabezpečení sítě (NSG), což je hranice zabezpečení mezi internetem a virtuálním počítačem. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Vytvoří pravidlo NSG, které povolí příchozí provoz. V této ukázce se otevře port 22 pro provoz SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Vytvoří virtuální síťovou kartu a připojí ji k virtuální síti, podsíti a skupině NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří skupinu dostupnosti. Skupiny dostupnosti zajišťují dostupnost aplikace rozmístěním virtuálních počítačů napříč fyzickými prostředky, aby v případě selhání nebyla ovlivněná celá sada. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
