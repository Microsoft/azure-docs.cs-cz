---
title: Konfigurace sady škálování virtuálních počítačů s existujícím Azure Load Balancer – Azure CLI
description: Naučte se konfigurovat sadu škálování virtuálního počítače s existujícím Azure Load Balancer pomocí rozhraní příkazového řádku Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518205"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Konfigurace sady škálování virtuálních počítačů s existujícím Azure Load Balancer pomocí rozhraní příkazového řádku Azure

V tomto článku se dozvíte, jak nakonfigurovat sadu škálování virtuálních počítačů s existujícím Azure Load Balancer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky 

- Budete potřebovat existující Nástroj pro vyrovnávání zatížení Standard SKU v předplatném, kde bude nasazená sada škálování virtuálního počítače.

- Pro sadu škálování virtuálního počítače potřebujete Virtual Network Azure.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Nasazení sady škálování virtuálních počítačů s existujícím nástrojem pro vyrovnávání zatížení

Hodnoty v závorkách nahraďte názvy prostředků v konfiguraci.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

Následující příklad nasadí sadu škálování virtuálního počítače pomocí:

- Sada škálování virtuálních počítačů s názvem **myVMSS**
- Azure Load Balancer s názvem **myLoadBalancer**
- Back-end fond nástroje pro vyrovnávání zatížení s názvem **myBackendPool**
- Azure Virtual Network s názvem **myVnet**
- Podsíť s názvem **mySubnet**
- Skupina prostředků s názvem **myResourceGroup**
- Ubuntu image serveru pro sadu škálování virtuálního počítače

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Po vytvoření sady škálování se port back-endu nedá změnit pro pravidlo vyrovnávání zatížení používané sondou stavu nástroje pro vyrovnávání zatížení. Pokud chcete změnit port, můžete odstranit sondu stavu tak, že aktualizujete sadu škálování virtuálního počítače Azure, aktualizujete port a pak znovu nakonfigurujete test stavu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili sadu škálování virtuálního počítače s existujícím Azure Load Balancer.  Další informace o službě Virtual Machine Scale Sets a nástroji pro vyrovnávání zatížení najdete v těchto tématech:

- [Co je Azure Load Balancer?](load-balancer-overview.md)
- [Co jsou škálovací sady virtuálních počítačů?](../virtual-machine-scale-sets/overview.md)
                                