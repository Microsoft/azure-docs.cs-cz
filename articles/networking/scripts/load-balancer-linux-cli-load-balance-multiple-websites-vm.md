---
title: Ukázkový skript Azure CLI – vyrovnávání zatížení několika webů pomocí Azure CLI | Microsoft Docs
description: Ukázkový skript Azure CLI – vyrovnávání zatížení několika webů na stejný virtuální počítač
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: allensu
ms.openlocfilehash: 7a3bd4cab8123814fe360fe4ab724650c785e9f1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234105"
---
# <a name="load-balance-multiple-websites"></a>Vyrovnávání zatížení několika webů

Tento ukázkový skript vytvoří virtuální síť se dvěma virtuálními počítači, které jsou členy skupiny dostupnosti. Nástroj pro vyrovnávání zatížení přesměruje provoz pro dvě samostatné IP adresy na tyto dva virtuální počítače. Po spuštění skriptu můžete na tyto virtuální počítače nasadit software webového serveru a hostovat více webů, přičemž každý z nich bude mít vlastní IP adresu.

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
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet) | Vytvoří virtuální síť Azure a podsíť. |
| [az network public-ip create](/cli/azure/network/public-ip) | Vytvoří veřejnou IP adresu se statickou IP adresou a přidruženým názvem DNS. |
| [az network lb create](/cli/azure/network/lb) | Vytvoří Azure Load Balancer. |
| [az network lb probe create](/cli/azure/network/lb/probe) | Vytvoří sondu nástroje pro vyrovnávání zatížení. Sonda nástroje pro vyrovnávání zatížení se používá k monitorování jednotlivých virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. Pokud nějaký virtuální počítač přestane být přístupný, nebude se do něj směrovat provoz. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Vytvoří pravidlo nástroje pro vyrovnávání zatížení. V této ukázce se vytvoří pravidlo pro port 80. Když nástroj pro vyrovnávání zatížení přijme přenos HTTP, přesměruje ho na port 80 některého z virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) | Vytvoří front-endovou IP adresu pro Load Balancer. |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool) | Vytvoří fond back-endových adres. |
| [az network nic create](/cli/azure/network/nic) | Vytvoří virtuální síťovou kartu a připojí ji k virtuální síti a podsíti. |
| [az vm availability-set create](/cli/azure/network/lb/rule) | Vytvoří skupinu dostupnosti. Skupiny dostupnosti zajišťují dostupnost aplikace rozmístěním virtuálních počítačů napříč fyzickými prostředky, aby v případě selhání nebyla ovlivněná celá sada. |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config) | Vytvoří konfiguraci protokolu IP. Je nutné, aby byla pro vaše předplatné povolená funkce Microsoft.Network/AllowMultipleIpConfigurationsPerNic. Jako primární konfiguraci IP adresy lze pro každý síťový adaptér určit jenom jednu konfiguraci, používá se k tomu příznak --make-primary. |
| [az vm create](/cli/azure/vm/availability-set) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az group delete](/cli/azure/vm/extension) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty CLI pro sítě najdete v [dokumentaci se základními informacemi k sítím Azure](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).