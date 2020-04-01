---
title: Upgrade aplikace IPv4 na IPv6 ve virtuální síti Azure – PowerShell
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit adresy IPv6 do existující aplikace ve virtuální síti Azure pomocí Azure Powershellu.
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
ms.openlocfilehash: c733538a4e730a95008a8ec1e4d50c20d6ce24ec
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420767"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Upgrade aplikace IPv4 na IPv6 ve virtuální síti Azure – PowerShell

Tento článek ukazuje, jak přidat připojení IPv6 k existující aplikaci IPv4 ve virtuální síti Azure se standardním nástrojem pro vyrovnávání zatížení a veřejnou IP adresou. Upgrade na místě zahrnuje:
- Adresní prostor IPv6 pro virtuální síť a podsíť
- standardní nástroj pro vyrovnávání zatížení s front-endovými konfiguracemi IPv4 i IPV6
- Virtuální počítače s kanaty, které mají konfiguraci IPv4 + IPv6
- Veřejná IP adresa IPv6, takže systém vyrovnávání zatížení má připojení IPv6 směřující k Internetu



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento článek vyžaduje modul Azure PowerShell verze 6.9.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste nasadili standardní vyrovnávání zatížení, jak je popsáno v [úvodním panelu: Vytvoření standardního vyrovnávání zatížení – Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Načtení skupiny prostředků

Před vytvořením virtuální sítě se dvěma zásobníky je nutné načíst skupinu prostředků pomocí [skupiny Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Vytvoření IP adres IPv6

Vytvořte veřejnou adresu IPv6 s [novou azpublicipaddress](/powershell/module/az.network/new-azpublicipaddress) pro standardní vyrovnávání zatížení. Následující příklad vytvoří veřejnou IP adresu IPv6 s názvem *PublicIP_v6* ve skupině prostředků *myResourceGroupSLB:*

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Konfigurace front-endu pro vyrovnávání zatížení

Načtěte existující konfiguraci nástroje pro vyrovnávání zatížení a přidejte novou adresu IP Protokolu IPv6 pomocí [funkce Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) následujícím způsobem:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Konfigurace back-endového fondu vyrovnávání zatížení

Vytvořte back-endový fond v místní kopii konfigurace nástroje pro vyrovnávání zatížení a aktualizujte běžící nástroj pro vyrovnávání zatížení s novou konfigurací back-endového fondu následujícím způsobem:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Nakonfigurovat pravidla nástroje pro vyrovnávání zatížení
Načtěte existující konfiguraci front-endového a back-endového fondu nástroje Pro vyrovnávání zatížení a pak přidejte nová pravidla vyrovnávání zatížení pomocí [funkce Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
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
## <a name="add-ipv6-address-ranges"></a>Přidání rozsahů adres IPv6

Přidejte rozsahy adres IPv6 do virtuální sítě a podsítě hostující virtuální počítače následujícím způsobem:

```azurepowershell
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
## <a name="add-ipv6-configuration-to-nic"></a>Přidání konfigurace Protokolu IPv6 na nic

Nakonfigurujte všechny síťové karty virtuálních počítačů s adresou IPv6 pomocí [funkce Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) následujícím způsobem:

```azurepowershell

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

V tomto článku jste aktualizovali existující standardní nástroj pro vyrovnávání zatížení pomocí konfigurace ip protokolu ipv4 frontendna konfiguraci duálního zásobníku (IPv4 a IPv6). Také jste přidali konfigurace IPv6 do síťových připojení virtuálních počítačů v back-endovém fondu a do virtuální sítě, která je hostuje. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [Co je IPv6 pro virtuální síť Azure?](ipv6-overview.md)
