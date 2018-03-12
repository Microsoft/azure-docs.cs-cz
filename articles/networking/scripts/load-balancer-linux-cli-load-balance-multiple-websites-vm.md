---
title: "Ukázka skriptu Azure CLI - vyrovnávat zatížení více webů pomocí rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Ukázka skriptu Azure CLI - vyrovnávat zatížení více webů na jednom virtuálním počítači"
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 72a780f1870d05a714aaeec879004b6f06d8bb7f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="load-balance-multiple-websites"></a>Vyrovnávat zatížení více webů

Tento ukázkový skript vytvoří virtuální síť s dva virtuální počítače (VM), které jsou členy skupiny dostupnosti. Nástroj pro vyrovnávání zatížení bude směrovat provoz pro dva samostatné IP adresy na dva virtuální počítače. Po spuštění skriptu, můžete nasadit software webového serveru do virtuálních počítačů a hostitelů více webových serverů, každý s vlastní IP adresu.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření skupiny prostředků, virtuální sítě, Vyrovnávání zatížení a všechny související prostředky. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Vytvoření sítě vnet az](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a podsíť. |
| [Vytvoření veřejné sítě az-ip](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu se statickou IP adresu a přidružené název DNS. |
| [Vytvoření sítě lb az](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Vytvoří k nástroji pro vyrovnávání zatížení Azure. |
| [Vytvoření az sítě lb testu](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Vytvoří sondu nástroje pro vyrovnávání zatížení. Sondu nástroje pro vyrovnávání zatížení se používá k monitorování jednotlivých virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. Pokud žádné virtuální počítače bude nedostupné, není provoz směruje na virtuální počítač. |
| [Vytvořit pravidlo lb az sítě](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří pravidlo Vyrovnávání zatížení. V této ukázce se vytvoří pravidlo pro port 80. Jako HTTP přenos dorazí na nástroje pro vyrovnávání zatížení, se směruje na portu 80 mezi virtuálními počítači v sadě nástroje pro vyrovnávání zatížení. |
| [Vytvoření az sítě lb ip front-endu-](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Vytvoření IP adresy front-endu nástroje pro vyrovnávání zatížení. |
| [Vytvoření az sítě lb fond adres](https://docs.microsoft.com/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Vytvoří fond back-end adresy. |
| [Vytvoření az síťových adaptérů sítě](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Vytvoří virtuální síťové karty a připojí jej virtuální sítě a podsítě. |
| [Vytvoření virtuálního počítače az sady dostupnosti.](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří skupinu dostupnosti. Skupiny dostupnosti zajistěte doba provozu aplikací tak, že se virtuální počítače napříč fyzické prostředky tak, že pokud dojde k selhání, není provedena celou sadu. |
| [Seskupování síťových az ip-config vytvořit](https://docs.microsoft.com/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Vytvoří confiuration IP. Musí mít funkci Microsoft.Network/AllowMultipleIpConfigurationsPerNic pro vaše předplatné povolený. Konfiguraci pouze jednoho může být určen jako primární konfiguraci IP adresy pro síťový adaptér, pomocí--příznak změnit na primární. |
| [Vytvoření virtuálního počítače az](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Vytvoří virtuální počítač a připojí jej k síťové karty, virtuální sítě, podsítě a NSG. Tento příkaz také určuje image virtuálního počítače jako přihlašovací údaje použité a správu.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další síťové ukázky skriptu rozhraní příkazového řádku najdete v [přehled sítě Azure dokumentaci](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
