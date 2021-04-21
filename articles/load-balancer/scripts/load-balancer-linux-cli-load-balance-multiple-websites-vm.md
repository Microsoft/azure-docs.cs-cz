---
title: Vyrovnávání zatížení víc webů – Azure CLI – Azure Load Balancer
description: Tento ukázkový skript Azure CLI provádí vyrovnávání zatížení několika webů na stejném virtuálním počítači.
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77dfc42164419d86e174bc47297e2f596e757ffe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790090"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Ukázkový skript Azure CLI: Vyrovnávání zatížení několika webů

Tento ukázkový skript Azure CLI vytvoří virtuální síť se dvěma virtuálními počítači, které jsou členy skupiny dostupnosti. Nástroj pro vyrovnávání zatížení přesměruje provoz pro dvě samostatné IP adresy na tyto dva virtuální počítače. Po spuštění skriptu můžete na tyto virtuální počítače nasadit software webového serveru a hostovat více webů, přičemž každý z nich bude mít vlastní IP adresu.

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

Tento skript používá k vytvoření skupiny prostředků, virtuální sítě, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a podsíť. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu se statickou IP adresou a přidruženým názvem DNS. |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | Vytvoří Azure Load Balancer. |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | Vytvoří sondu nástroje pro vyrovnávání zatížení. Sonda nástroje pro vyrovnávání zatížení se používá k monitorování jednotlivých virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. Pokud nějaký virtuální počítač přestane být přístupný, nebude se do něj směrovat provoz. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří pravidlo nástroje pro vyrovnávání zatížení. V této ukázce se vytvoří pravidlo pro port 80. Když nástroj pro vyrovnávání zatížení přijme přenos HTTP, přesměruje ho na port 80 některého z virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Vytvoří front-endovou IP adresu pro Load Balancer. |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Vytvoří fond back-endových adres. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Vytvoří virtuální síťovou kartu a připojí ji k virtuální síti a podsíti. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří skupinu dostupnosti. Skupiny dostupnosti zajišťují dostupnost aplikace rozmístěním virtuálních počítačů napříč fyzickými prostředky, aby v případě selhání nebyla ovlivněná celá sada. |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Vytvoří konfiguraci protokolu IP. Je nutné, aby byla pro vaše předplatné povolená funkce Microsoft.Network/AllowMultipleIpConfigurationsPerNic. Jako primární konfiguraci IP adresy lze pro každý síťový adaptér určit jenom jednu konfiguraci, používá se k tomu příznak --make-primary. |
| [az vm create](/cli/azure/vm/availability-set#az_vm_availability_set_create) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty CLI pro sítě najdete v [dokumentaci se základními informacemi k sítím Azure](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).