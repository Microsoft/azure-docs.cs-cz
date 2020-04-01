---
title: Přidání IPv6 do aplikace IPv4 ve virtuální síti Azure – Azure CLI
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit adresy IPv6 do existující aplikace ve virtuální síti Azure pomocí azure cli.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f3f9b32ea55f0ceebf08b22ccc7e2ceec0b6227e
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420794"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Přidání IPv6 do aplikace IPv4 ve virtuální síti Azure – Azure CLI

Tento článek ukazuje, jak přidat adresy IPv6 do aplikace, která používá veřejnou IP adresu IPv4 ve virtuální síti Azure pro standardní nástroj pro vyrovnávání zatížení pomocí azure cli. Upgrade na místě zahrnuje virtuální síť a podsíť, standardní nástroj pro vyrovnávání zatížení s front-endovými konfiguracemi IPv4 + IPV6, virtuální počítače s síťovými kartami, které mají konfigurace IPv4 + IPv6, skupinu zabezpečení sítě a veřejné IP adresy.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a použít Azure CLI místně místo, tento rychlý start vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte program `az --version`. Informace o instalaci nebo upgradu [najdete v tématu Instalace příkazového příkazového](/cli/azure/install-azure-cli) příkazu Konzumu Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste nasadili standardní vyrovnávání zatížení, jak je popsáno v [úvodním panelu: Vytvoření standardního vyrovnávání zatížení – Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>Vytvoření adres IPv6

Vytvořte veřejnou adresu IPv6 pomocí [vytvoření veřejné ip adresy az sítě](/cli/azure/network/public-ip) pro standardní vykladač zatížení. Následující příklad vytvoří veřejnou IP adresu IPv6 s názvem *PublicIP_v6* ve skupině prostředků *myResourceGroupSLB:*

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurace front-endu ipv6 pro vyrovnávání zatížení

COnfigure vyrovnávání zatížení s novou IP adresou IPv6 pomocí [frontend-ip sítě az lb vytvořit](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) takto:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurace back-endového fondu ipv6 pro vyrovnávání zatížení

Vytvořte back-endový fond pro síťové karty s adresami IPv6 pomocí [fondu adres az network lb vytvořte](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) následujícím způsobem:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurace pravidel vyrovnávání zatížení Protokolu IPv6

Vytvořte pravidla pro vyrovnávání zatížení IPv6 pomocí [vytvoření pravidla az network lb](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create).

```azurecli
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

## <a name="add-ipv6-address-ranges"></a>Přidání rozsahů adres IPv6

Přidejte rozsahy adres IPv6 do virtuální sítě a podsítě hostující nástroj pro vyrovnávání zatížení následujícím způsobem:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Přidání konfigurace Protokolu IPv6 do síťové karty

Nakonfigurujte síťové karty virtuálních počítačů s adresou IPv6 pomocí [az network nic ip-config vytvořit](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) takto:

```azurecli
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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazení virtuální sítě iPv6 dual stack na webu Azure Portal
Virtuální síť IPv6 dual stack můžete zobrazit na webu Azure Portal následujícím způsobem:
1. Na vyhledávacím panelu portálu zadejte *myVnet*.
2. Když se ve výsledcích hledání zobrazí **myVnet,** vyberte ji. Tím se spustí stránka **Přehled** virtuální sítě s názvem myVNet s názvem *myVNet*. Virtuální síť se dvěma zásobníky zobrazuje tři síťové karty s konfiguracemi IPv4 i IPv6 umístěnými v podsíti s duálním zásobníkem s názvem *mySubnet*.

  ![Virtuální síť s duálním zásobníkem IPv6 v Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít příkaz [Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků, virtuálního virtuálního času a všech souvisejících prostředků.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste aktualizovali existující standardní nástroj pro vyrovnávání zatížení pomocí konfigurace ip protokolu ipv4 frontendna konfiguraci duálního zásobníku (IPv4 a IPv6). Také jste přidali konfigurace IPv6 do nic síťové karty virtuálních počítačů v back-endovém fondu. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [Co je IPv6 pro virtuální síť Azure?](ipv6-overview.md)
