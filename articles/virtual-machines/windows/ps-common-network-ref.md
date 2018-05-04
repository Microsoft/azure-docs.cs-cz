---
title: Běžné příkazy prostředí PowerShell pro virtuální sítě Azure | Microsoft Docs
description: Běžné příkazy prostředí PowerShell, abyste mohli spustit vytvoření virtuální sítě a jeho přidružené prostředky pro virtuální počítače.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: a5b3f84c27a0a5f6458808940b16a9001097b30b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Běžné příkazy prostředí PowerShell pro Azure Virtual Network

Pokud chcete vytvořit virtuální počítač, je nutné vytvořit [virtuální sítě](../../virtual-network/virtual-networks-overview.md) nebo vědět o existující virtuální síť, ve kterém můžete přidat virtuální počítač. Obvykle když vytvoříte virtuální počítač, musíte také vzít v úvahu vytváření prostředků popsaných v tomto článku.

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

Některé proměnné můžou být užitečné pro vás, pokud používá více než jeden z příkazů v tomto článku:

- $location – umístění síťové prostředky. Můžete použít [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) najít [geografické oblasti](https://azure.microsoft.com/regions/) který vám vyhovuje.
- $myResourceGroup - název skupiny prostředků, kde se nachází síťové prostředky.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření konfigurací podsítě |$subnet1 = [nové AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Typické síť může mít podsíť [internetové nástroj pro vyrovnávání zatížení](../../load-balancer/load-balancer-internet-overview.md) a samostatnou podsíť pro [nástroj pro vyrovnávání zatížení pro vnitřní](../../load-balancer/load-balancer-internal-overview.md). |
| Vytvoření virtuální sítě |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) -Name "myVNet" - ResourceGroupName $myResourceGroup-umístění $location - AddressPrefix XX. X.X.X/XX-podsíť $subnet1, Podsíť2 $ |
| Test pro název jedinečný domény |[Test-AzureRmDnsAvailability](https://docs.microsoft.com/powershell/module/azurerm.network/test-azurermdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Můžete zadat název domény DNS [prostředek veřejné IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), která vytvoří mapování pro domainname.location.cloudapp.azure.com veřejné IP adresy v Azure spravované servery. Název může obsahovat pouze písmena, číslice a pomlčky. První a poslední znak musí být písmeno nebo číslo a název domény musí být jedinečné v rámci jeho umístění Azure. Pokud **True** se vrátí, je navržený název globálně jedinečný. |
| Vytvoření veřejné IP adresy |$pip = [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) -Name "myPublicIp" - ResourceGroupName $myResourceGroup - DomainNameLabel "myDNS"-umístění $location - AllocationMethod dynamický<BR><BR>Veřejná IP adresa se používá název domény, který jste dříve otestovat a používá front-endovou konfiguraci služby Vyrovnávání zatížení. |
| Vytvoření konfigurace IP front-endu |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) -Name "myFrontendIP" - PublicIpAddress $pip<BR><BR>Front-endovou konfiguraci zahrnuje veřejnou IP adresu, kterou jste dříve vytvořili pro příchozí síťový provoz. |
| Vytvoření fondu back-endových adres |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Poskytuje interní adresy pro back-end služby Vyrovnávání zatížení, které jsou přístupné přes síťové rozhraní. |
| Vytvořit sondu |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) -název "myProbe" - RequestPath 'HealthProbe.aspx'-protokolu http-Port 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Obsahuje sondy stavu služby umožňuje zkontrolovat dostupnost této instance virtuálních počítačů v back-end fondu adres. |
| Vytvořit pravidlo Vyrovnávání zatížení |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) -název HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-testu $healthProbe-80 protokolu Tcp - FrontendPort - BackendPort 80<BR><BR>Obsahuje pravidla, která veřejný port v nástroji pro vyrovnávání zatížení přiřadit port ve fondu adres back-end. |
| Vytvoření příchozího pravidla NAT |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) -Name "myInboundRule1" - FrontendIpConfiguration $frontendIP-Protocol TCP - FrontendPort 3441 - BackendPort 3389<BR><BR>Obsahuje pravidla mapování veřejný port v nástroji pro vyrovnávání zatížení do portu pro konkrétní virtuální počítač ve fondu adres back-end. |
| Vytvoření nástroje pro vyrovnávání zatížení |$loadBalancer = [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancer) - ResourceGroupName $myResourceGroup-název "myLoadBalancer"-umístění $location - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-testu $healthProbe |
| Vytvořit rozhraní sítě |$nic1 = [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) - ResourceGroupName $myResourceGroup-Name "myNIC" - umístění $location - PrivateIpAddress XX. X.X.X-$loadBalancer.BackendAddressPools[0 - LoadBalancerBackendAddressPool podsíť $Podsíť2] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Vytvořte rozhraní sítě pomocí veřejnou IP adresu a podsíť virtuální sítě, kterou jste vytvořili. |

## <a name="get-information-about-network-resources"></a>Získat informace o síťové prostředky

| Úkol | Příkaz |
| ---- | ------- |
| Seznam virtuálních sítí |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí všechny virtuální sítě ve skupině prostředků. |
| Získat informace o virtuální sítě |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Seznam podsítí ve virtuální síti |Get-AzureRmVirtualNetwork-Name "myVNet" - ResourceGroupName $myResourceGroup &#124; vyberte podsítě |
| Získání informací o podsíti |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" - virtuální síť $vnet<BR><BR>Získá informace o podsíti v zadané virtuální síti. Hodnota $vnet představuje pro objekt vrácený rutinou Get-AzureRmVirtualNetwork. |
| Seznam IP adres |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Uvádí veřejné IP adresy ve skupině prostředků. |
| Nástroje pro vyrovnávání zatížení seznamu |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí všechny služby Vyrovnávání zatížení ve skupině prostředků. |
| Seznamu síťových rozhraní |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam všech síťových rozhraní ve skupině prostředků. |
| Získat informace o rozhraní sítě |Get-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Získá informace o konkrétním síťovém rozhraní. |
| Získat konfiguraci IP rozhraní sítě |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterfaceipconfig) -Name "myNICIP" - NetworkInterface $nic<BR><BR>Získá informace o konfiguraci IP zadané síťové rozhraní. Hodnota $nic představuje pro objekt vrácený rutinou Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Správa síťových prostředků

| Úkol | Příkaz |
| ---- | ------- |
| Přidat podsíť k virtuální síti. |[Přidat AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) - AddressPrefix XX. X.X.X/XX-Name "mySubnet1" - virtuální síť $vnet<BR><BR>Přidá do existující virtuální síť podsíť. Hodnota $vnet představuje pro objekt vrácený rutinou Get-AzureRmVirtualNetwork. |
| Odstranění virtuální sítě |[Remove-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadané virtuální síti ze skupiny prostředků. |
| Odstranit síťové rozhraní |[Remove-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermnetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Zadané síťové rozhraní se odebere ze skupiny prostředků. |
| Odstranění nástroje pro vyrovnávání zatížení |[Remove-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Zadanému vyrovnávání zátěže odebere ze skupiny prostředků. |
| Odstranit veřejnou IP adresu |[Remove-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadaný veřejnou IP adresu ze skupiny prostředků. |

## <a name="next-steps"></a>Další kroky
* Síťové rozhraní, které právě vytvořili při použití jste [vytvoření virtuálního počítače](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Další informace o tom, jak můžete [vytvoření virtuálního počítače s více síťovými rozhraními](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

