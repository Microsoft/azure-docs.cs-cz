---
title: Běžné příkazy prostředí PowerShell pro Azure Virtual Networks | Dokumentace Microsoftu
description: Běžné příkazy Powershellu, které vám pomůžou začít vytvářet virtuální sítě a její přidružené prostředky pro virtuální počítače.
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
ms.openlocfilehash: 31a0d486f2540ea75a57b29b8f1da21839783468
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984629"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Běžné příkazy prostředí PowerShell pro Azure Virtual Network

Pokud chcete vytvořit virtuální počítač, je potřeba vytvořit [virtuální sítě](../../virtual-network/virtual-networks-overview.md) nebo vědět o existující virtuální síť, ve kterém můžete přidat virtuální počítač. Obvykle když vytváříte virtuální počítač, je také potřeba zvážit vytváření prostředků popsaných v tomto článku.

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

Některé proměnné může být užitečné pro vás, pokud používá více než jeden z příkazů v tomto článku:

- $location – umístění síťovým prostředkům. Můžete použít [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) k vyhledání [geografické oblasti](https://azure.microsoft.com/regions/) , který vám vyhovuje.
- $myResourceGroup – název skupiny prostředků, kde jsou umístěny síťových prostředků.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření konfigurací podsítí |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Typické síť může mít podsíť pro [internetového nástroje load balancer](../../load-balancer/load-balancer-internet-overview.md) a samostatnou podsíť pro [interního nástroje load balancer](../../load-balancer/load-balancer-internal-overview.md). |
| Vytvoření virtuální sítě |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Test pro jedinečný název domény |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Můžete zadat název domény DNS [prostředek veřejné IP adresy](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), který vytvoří mapování domainname.location.cloudapp.azure.com veřejné IP adresy serverů DNS spravovaných Azure. Název může obsahovat pouze písmena, číslice a pomlčky. První a poslední znak musí být písmeno nebo číslo a název domény musí být jedinečný v rámci příslušného umístění Azure. Pokud **True** se vrátí, navržený název je globálně jedinečný. |
| Vytvoření veřejné IP adresy |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Veřejná IP adresa používá název domény, který jste dříve testovány a používá konfiguraci front-endu nástroje pro vyrovnávání zatížení. |
| Vytvořte konfiguraci IP adresy front-endu |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Front-endová konfigurace obsahuje veřejnou IP adresu, kterou jste dříve vytvořili pro příchozí síťový provoz. |
| Vytvoření fondu back-endových adres |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Poskytuje vnitřní adresy pro back-endu nástroje pro vyrovnávání zatížení, které jsou přístupné prostřednictvím síťového rozhraní. |
| Vytvořte sondu |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) – myProbe"název" - RequestPath "HealthProbe.aspx" – protokol http-Port 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Obsahuje testy stavu sloužící ke kontrole dostupnosti instancí virtuálních počítačů v back-endového fondu adres. |
| Vytvořit pravidlo Vyrovnávání zatížení |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Obsahuje pravidla, která přiřadit veřejný port v nástroji pro vyrovnávání zatížení na port v back-endového fondu adres. |
| Vytvoření příchozího pravidla NAT |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Obsahuje pravidla mapující veřejný port v nástroji pro vyrovnávání zatížení na port konkrétního virtuálního počítače v back-endového fondu adres. |
| Vytvoření nástroje pro vyrovnávání zatížení |$loadBalancer = [AzLoadBalancer nový](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) - ResourceGroupName $myResourceGroup-Name "myLoadBalancer" – umístění $location - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule – BackendAddressPool $beAddressPool-$healthProbe pro zjišťování |
| Vytvořte síťové rozhraní |$nic1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) - ResourceGroupName $myResourceGroup-Name "myNIC" - umístění $location - PrivateIpAddress XX. X.X.X-podsítě $subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Vytvořte síťové rozhraní pomocí veřejné IP adresy a podsítě virtuální sítě, kterou jste vytvořili. |

## <a name="get-information-about-network-resources"></a>Získejte informace o síťových prostředků

| Úkol | Příkaz |
| ---- | ------- |
| Seznam virtuálních sítí |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Uvádí všechny virtuální sítě ve skupině prostředků. |
| Získejte informace o službě virtual network |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Seznam podsítí ve virtuální síti |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Získání informací o podsíti |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Získá informace o podsíti v zadané virtuální sítě. Hodnota $vnet představuje objekt vrácený rutinou Get-AzVirtualNetwork. |
| List IP addresses |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Obsahuje veřejné IP adresy ve skupině prostředků. |
| Seznam nástrojů pro vyrovnávání zatížení |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Uvádí všechny nástroje pro vyrovnávání zatížení ve skupině prostředků. |
| Seznam síťových rozhraní |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Obsahuje seznam všech síťových rozhraní ve skupině prostředků. |
| Získejte informace o síťové rozhraní |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Získá informace o konkrétní síťové rozhraní. |
| Získat konfiguraci protokolu IP síťového rozhraní |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" - NetworkInterface $nic<BR><BR>Získá informace o konfiguraci protokolu IP ze zadané síťové rozhraní. Hodnota $nic představuje objekt vrácený rutinou Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Správa síťových prostředků

| Úkol | Příkaz |
| ---- | ------- |
| Přidání podsítě do virtuální sítě |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Existující virtuální sítě přidá podsíť. Hodnota $vnet představuje objekt vrácený rutinou Get-AzVirtualNetwork. |
| Odstranění virtuální sítě |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadané virtuální síti ze skupiny prostředků. |
| Odstranit síťové rozhraní |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Zadané síťové rozhraní se odebere ze skupiny prostředků. |
| Odstranění nástroje pro vyrovnávání zatížení |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Zadanému vyrovnávání zátěže odebere ze skupiny prostředků. |
| Odstranit veřejnou IP adresu |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadaný veřejnou IP adresu ze skupiny prostředků. |

## <a name="next-steps"></a>Další kroky
* Síťové rozhraní, které právě vytvořili při použití můžete [vytvoření virtuálního počítače](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Další informace o tom, jak [vytvoření virtuálního počítače s několika síťovými rozhraními](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

