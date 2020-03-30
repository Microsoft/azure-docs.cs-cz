---
title: Konfigurace škálovací sady virtuálních strojů pomocí existujícího nástroje pro vyrovnávání zatížení Azure – rozhraní příkazového příkazu Azure
description: Zjistěte, jak nakonfigurovat škálovací sadu virtuálních strojů pomocí existujícího nástroje pro vyrovnávání zatížení Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349738"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Konfigurace škálovací sady virtuálních strojů s existujícím nástrojem Azure Load Balancer pomocí rozhraní příkazového příkazového příkazu Azure

V tomto článku se dozvíte, jak nakonfigurovat škálovací sadu virtuálních strojů s existujícím nástrojem Pro vyrovnávání zatížení Azure. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.
- Existující standardní nástroj pro vyrovnávání zatížení sku v předplatném, kde se nasadí škálovací sada virtuálního počítače.
- Virtuální síť Azure pro škálovací sadu virtuálních strojů.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete používat příkazcli místně, tento článek vyžaduje, abyste měli nainstalovanou verzi Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI

Přihlaste se do Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Nasazení škálovací sady virtuálních strojů s existujícím nástrojem pro vyrovnávání zatížení

Nahraďte hodnoty v závorkách názvy prostředků v konfiguraci.

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

Níže uvedený příklad nasazuje škálovací sadu virtuálních strojů s:

- Škálovací sada virtuálních strojů s názvem **myVMSS**
- Azure Load Balancer s názvem **myLoadBalancer**
- Back-endový fond vyrovnávání zatížení s názvem **myBackendPool**
- Virtuální síť Azure s názvem **myVnet**
- Podsíť s názvem **mySubnet**
- Skupina prostředků s názvem **myResourceGroup**
- Obrázek Ubuntu Server pro škálovací sadu virtuálních strojů

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
> Po vytvoření škálovací sady nelze port back-endu změnit pro pravidlo vyrovnávání zatížení používané sondou stavu systému vyrovnávání zatížení. Chcete-li změnit port, můžete odebrat sondu stavu aktualizací škálovací sady virtuálního počítače Azure, aktualizovat port a pak znovu nakonfigurovat sondu stavu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili škálovací sadu virtuálních strojů s existujícím nástrojem Pro vyrovnávání zatížení Azure.  Další informace o škálovacích sadách virtuálních strojů a nástroj pro vyrovnávání zatížení najdete v tématu:

- [Co je Azure Load Balancer?](load-balancer-overview.md)
- [Co jsou škálovací sady virtuálních počítačů?](../virtual-machine-scale-sets/overview.md)
                                