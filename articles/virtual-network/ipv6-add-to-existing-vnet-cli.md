---
title: Přidání IPv6 do aplikace IPv4 ve službě Azure Virtual Network – Azure CLI
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit adresy IPv6 do existující aplikace ve službě Azure Virtual Network pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a321687a755f8a3d6e6d9139138d61c58764ef4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932596"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Přidání IPv6 do aplikace IPv4 ve službě Azure Virtual Network – Azure CLI

V tomto článku se dozvíte, jak přidat IPv6 adresy do aplikace, která používá veřejnou IP adresu IPv4 ve virtuální síti Azure pro Standard Load Balancer pomocí Azure CLI. Místní upgrade zahrnuje virtuální síť a podsíť, Standard Load Balancer s konfiguracemi front-endu IPv4 + IPV6, virtuálních počítačů se síťovými kartami, které mají konfigurace IPv4 + IPv6, skupiny zabezpečení sítě a veřejné IP adresy.

## <a name="prerequisites"></a>Požadavky

- V tomto článku se předpokládá, že jste nasadili Standard Load Balancer, jak je popsáno v tématu [rychlý Start: vytvoření Standard Load Balancer Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-ipv6-addresses"></a>Vytvoření IPv6 adres

Pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip) pro standard Load Balancer Vytvořte veřejnou IPv6 adresu. Následující příklad vytvoří veřejnou IP adresu IPv6 s názvem *PublicIP_v6* ve skupině prostředků *myResourceGroupSLB* :

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurace front-endu protokolu IPv6 pro vyrovnávání zatížení

Nakonfigurujte Nástroj pro vyrovnávání zatížení novou IP adresou IPv6 pomocí příkazu [AZ Network diskont-IP Create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) následujícím způsobem:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurovat back-end fond nástroje pro vyrovnávání zatížení IPv6

Vytvořte back-end fond pro síťové karty s adresami IPv6 pomocí příkazu [AZ Network Address-Pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) následujícím způsobem:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurace pravidel nástroje pro vyrovnávání zatížení IPv6

Vytvořte pravidla nástroje pro vyrovnávání zatížení IPv6 pomocí [AZ Network diskont Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create).

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Přidat rozsahy IPv6 adres

Přidejte rozsahy IPv6 adres do virtuální sítě a podsítě hostující Nástroj pro vyrovnávání zatížení následujícím způsobem:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Přidat konfiguraci IPv6 do síťových karet

Nakonfigurujte síťové karty virtuálních počítačů s adresou IPv6 pomocí příkazu [AZ Network nic IP-config Create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) následujícím způsobem:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazení virtuální sítě s duálním zásobníkem IPv6 v Azure Portal

Virtuální síť s duálním zásobníkem IPv6 se dá zobrazit v Azure Portal následujícím způsobem:
1. Na panelu hledání na portálu zadejte *myVnet*.
2. Pokud se ve výsledcích hledání zobrazí **myVnet** , vyberte ji. Tím se spustí Stránka s **přehledem** pro virtuální síť Dual stack s názvem *myVNet*. Virtuální síť Dual Stack zobrazuje tři síťové karty s konfiguracemi protokolů IPv4 i IPv6 umístěných v podsíti duálního zásobníku s názvem *mySubnet*.

  ![Virtuální síť s duálním zásobníkem IPv6 v Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste aktualizovali existující Standard Load Balancer s konfigurací protokolu IP front-endu IPv4 na konfiguraci duálního zásobníku (IPv4 a IPv6). Do síťových karet virtuálních počítačů ve fondu back-end se přidaly taky konfigurace IPv6. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [co je IPv6 pro Azure Virtual Network?](ipv6-overview.md)
