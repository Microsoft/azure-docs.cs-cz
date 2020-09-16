---
title: Upgrade aplikace IPv4 na IPv6 ve službě Azure Virtual Network-PowerShell
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit adresy IPv6 do existující aplikace ve službě Azure Virtual Network pomocí Azure PowerShell.
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
ms.openlocfilehash: 9c2ea7cae26ac00c9c647704de8de1f39ebce8f0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90600819"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Upgrade aplikace IPv4 na IPv6 ve službě Azure Virtual Network – PowerShell

V tomto článku se dozvíte, jak přidat připojení IPv6 do existující aplikace IPv4 ve virtuální síti Azure pomocí Standard Load Balancer a veřejné IP adresy. Místní upgrade zahrnuje:
- Adresní prostor IPv6 pro virtuální síť a podsíť
- Standard Load Balancer s konfiguracemi front-endu IPv4 i IPV6
- Virtuální počítače se síťovými kartami, které mají konfiguraci IPv4 i IPv6
- Veřejná IP adresa IPv6, aby nástroj pro vyrovnávání zatížení měl připojení IPv6 k Internetu

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 6.9.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste nasadili Standard Load Balancer, jak je popsáno v tématu [rychlý Start: vytvoření Standard Load Balancer-Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md).

## <a name="retrieve-the-resource-group"></a>Načtení skupiny prostředků

Než budete moct vytvořit virtuální síť se dvěma zásobníky, musíte načíst skupinu prostředků pomocí [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell-interactive
$rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Vytvoření IP adres IPv6

Vytvořte veřejnou IPv6 adresu pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pro vaši Standard Load Balancer. Následující příklad vytvoří veřejnou IP adresu IPv6 s názvem *PublicIP_v6* ve skupině prostředků *myResourceGroupSLB* :

```azurepowershell-interactive  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Konfigurace front-endu nástroje pro vyrovnávání zatížení

Načtěte existující konfiguraci služby Vyrovnávání zatížení a přidejte novou IP adresu IPv6 pomocí příkazu [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) následujícím způsobem:

```azurepowershell-interactive
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"

# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Konfigurovat back-end fond nástroje pro vyrovnávání zatížení

Vytvořte back-end fond na místní kopii konfigurace nástroje pro vyrovnávání zatížení a aktualizujte běžící Nástroj pro vyrovnávání zatížení s novou konfigurací back-endu, který je následující:

```azurepowershell-interactive
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"

# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Nakonfigurovat pravidla nástroje pro vyrovnávání zatížení
Načtěte existující konfiguraci front-endu a back-endu pro vyrovnávání zatížení a pak přidejte nová pravidla vyrovnávání zatížení pomocí [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell-interactive
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb

# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Přidat rozsahy IPv6 adres

Přidejte rozsahy IPv6 adres do virtuální sítě a podsítě hostující virtuální počítače následujícím způsobem:

```azurepowershell-interactive
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 

#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")

#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]

#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")

#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork
```

## <a name="add-ipv6-configuration-to-nic"></a>Přidat konfiguraci IPv6 do síťové karty

Nakonfigurujte všechny síťové karty virtuálních počítačů s adresou IPv6 pomocí příkazu [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) následujícím způsobem:

```azurepowershell-interactive
#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName

#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazení virtuální sítě s duálním zásobníkem IPv6 v Azure Portal

Virtuální síť s duálním zásobníkem IPv6 se dá zobrazit v Azure Portal následujícím způsobem:
1. Na panelu hledání na portálu zadejte *myVnet*.
2. Pokud se ve výsledcích hledání zobrazí **myVnet** , vyberte ji. Tím se spustí Stránka s **přehledem** pro virtuální síť Dual stack s názvem *myVNet*. Virtuální síť Dual Stack zobrazuje tři síťové karty s konfiguracemi protokolů IPv4 i IPv6 umístěných v podsíti duálního zásobníku s názvem *mySubnet*.

  ![Virtuální síť s duálním zásobníkem IPv6 v Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste aktualizovali existující Standard Load Balancer s konfigurací protokolu IP front-endu IPv4 na konfiguraci duálního zásobníku (IPv4 a IPv6). Do síťových karet virtuálních počítačů ve fondu back-endu a do Virtual Network, která je hostuje, jste přidali taky konfigurace IPv6. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [co je IPv6 pro Azure Virtual Network?](ipv6-overview.md)
