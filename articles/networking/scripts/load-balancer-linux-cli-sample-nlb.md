---
title: Ukázka skriptu Azure CLI - přenosy Vyrovnávání zatížení pro virtuální počítače pro zajištění vysoké dostupnosti | Microsoft Docs
description: Ukázka skriptu Azure CLI - přenosy Vyrovnávání zatížení pro virtuální počítače pro vysokou dostupnost
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 810899c671ea141f45f7ce140c3f862f568a2f87
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29848300"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Přenosy Vyrovnávání zatížení pro virtuální počítače pro vysokou dostupnost

Tento ukázkový skript vytvoří vše potřebné ke spuštění několika Ubuntu virtuální počítače nakonfigurované v s vysokou dostupností a načíst vyrovnáváním konfiguraci. Po spuštění skriptu, budete mít tři virtuální počítače připojené k Azure skupiny dostupnosti a přístupné prostřednictvím Vyrovnávání zatížení Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření skupiny prostředků, virtuální počítač, skupina dostupnosti, nástroj pro vyrovnávání zatížení a všechny související prostředky. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Vytvoření sítě vnet az](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a podsíť. |
| [Vytvoření veřejné sítě az-ip](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu se statickou IP adresu a přidružené název DNS. |
| [Vytvoření sítě lb az](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Vytvoří k nástroji pro vyrovnávání zatížení Azure. |
| [Vytvoření az sítě lb testu](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Vytvoří sondu nástroje pro vyrovnávání zatížení. Sondu nástroje pro vyrovnávání zatížení se používá k monitorování jednotlivých virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. Pokud žádné virtuální počítače bude nedostupné, není provoz směruje na virtuální počítač. |
| [Vytvořit pravidlo lb az sítě](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří pravidlo Vyrovnávání zatížení. V této ukázce se vytvoří pravidlo pro port 80. Jako HTTP přenos dorazí na nástroje pro vyrovnávání zatížení, se směruje na portu 80 mezi virtuálními počítači v sadě LB. |
| [Vytvoření az sítě lb příchozí--pravidlo nat](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Vytvoří pravidlo překladu adres (NAT) pro vyrovnávání zatížení.  Pravidla NAT namapovat port služby Vyrovnávání zatížení na port na virtuálním počítači. V této ukázce se vytvoří pravidlo NAT pro provoz SSH pro každý virtuální počítač v sadě nástroje pro vyrovnávání zatížení.  |
| [Vytvoření az sítě nsg](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Vytvoří skupinu zabezpečení sítě (NSG), což je hranice zabezpečení mezi Internetu a virtuální počítač. |
| [Vytvoření pravidla nsg az sítě](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Vytvoří pravidlo NSG chcete povolit příchozí přenosy. V této ukázce je otevřen port 22 pro SSH provoz. |
| [Vytvoření az síťových adaptérů sítě](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Vytvoří virtuální síťové karty a připojí jej k virtuální síti, podsíti a NSG. |
| [Vytvoření virtuálního počítače az sady dostupnosti.](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří skupinu dostupnosti. Skupiny dostupnosti zajistěte doba provozu aplikací tak, že se virtuální počítače napříč fyzické prostředky tak, že pokud dojde k selhání, není provedena celou sadu. |
| [Vytvoření virtuálního počítače az](/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítač a připojí jej k síťové karty, virtuální sítě, podsítě a NSG. Tento příkaz také určuje image virtuálního počítače jako přihlašovací údaje použité a správu.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu síťových rozhraní příkazového řádku Azure najdete v [sítí Azure dokumentaci](../cli-samples.md).