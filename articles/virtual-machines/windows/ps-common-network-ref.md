---
title: Běžné příkazy PowerShellu pro virtuální sítě Azure
description: Běžné příkazy Prostředí PowerShell, které vám pomohou začít vytvářet virtuální síť a její přidružené prostředky pro virtuální počítače.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b9debe04759772ef51946dc99943ec4eff6f61dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911827"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Běžné příkazy PowerShellu pro virtuální sítě Azure

Pokud chcete vytvořit virtuální počítač, musíte vytvořit [virtuální síť](../../virtual-network/virtual-networks-overview.md) nebo vědět o existující virtuální síti, do které se dá virtuální počítač přidat. Obvykle při vytváření virtuálního počítače, musíte také zvážit vytvoření prostředků popsaných v tomto článku.

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

Některé proměnné mohou být užitečné, pokud spuštění více než jeden z příkazů v tomto článku:

- $location - Umístění síťových prostředků. [Pomocí funkce Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) můžete najít [geografickou oblast,](https://azure.microsoft.com/regions/) která vám vyhovuje.
- $myResourceGroup - Název skupiny prostředků, kde jsou umístěny síťové prostředky.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

| Úkol | Příkaz |
| ---- | ------- |
| Vytvoření konfigurací podsítí |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Název "mySubnet1" -AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Název "mySubnet2" -AddressPrefix XX. X.X.X/XX<BR><BR>Typická síť může mít podsíť pro [internetovou rovnováhu zatížení](../../load-balancer/load-balancer-internet-overview.md) a samostatnou podsíť pro [interní systém vyrovnávání zatížení](../../load-balancer/load-balancer-internal-overview.md). |
| Vytvoření virtuální sítě |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX. X.X.X/XX -Podsíť $subnet1, $subnet2 |
| Testování jedinečného názvu domény |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Umístění $location<BR><BR>Můžete zadat název domény DNS pro [veřejný prostředek IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), který vytvoří mapování pro domainname.location.cloudapp.azure.com na veřejnou IP adresu na serverech DNS spravovaných Azure. Název může obsahovat pouze písmena, číslice a pomlčky. První a poslední znak musí být písmeno nebo číslo a název domény musí být jedinečný v rámci umístění Azure. Pokud **true** je vrácena, váš navrhovaný název je globálně jedinečný. |
| Vytvoření veřejné IP adresy |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Veřejná IP adresa používá název domény, který jste dříve testovali, a používá se front-endovou konfigurací nástroje pro vyrovnávání zatížení. |
| Vytvoření konfigurace ip adresy front-end |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Název "myFrontendIP" -PublicIpAddress $pip<BR><BR>Front-endová konfigurace zahrnuje veřejnou IP adresu, kterou jste dříve vytvořili pro příchozí síťový provoz. |
| Vytvoření fondu back-endových adres |$beAddressPool = [New-AzLoadBalancerBackendEndAddressConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Název "myBackendAddressPool"<BR><BR>Poskytuje interní adresy pro back-end vykladače zatížení, ke kterým se přistupuje prostřednictvím síťového rozhraní. |
| Vytvoření sondy |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Název "myProbe" -RequestPath 'HealthProbe.aspx' -Protokol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Obsahuje sondy stavu používané ke kontrole dostupnosti instancí virtuálních počítačů ve fondu back-endových adres. |
| Vytvoření pravidla vyrovnávání zatížení |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Název HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Obsahuje pravidla, která přiřazují veřejný port v pravidlech vyrovnávání zatížení k portu ve fondu adres back-endu. |
| Vytvoření pravidla příchozího nat |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Název "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Obsahuje pravidla mapování veřejný port na nástroj pro vyrovnávání zatížení na port pro konkrétní virtuální počítač ve fondu adres back-endu. |
| Vytvoření nástroje pro vyrovnávání zatížení |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Vytvoření síťového rozhraní |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Název "myNIC" -Umístění $location -PrivateIpAddress XX. X.X.X -Podsíť $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Vytvořte síťové rozhraní pomocí veřejné IP adresy a podsítě virtuální sítě, kterou jste dříve vytvořili. |

## <a name="get-information-about-network-resources"></a>Získání informací o síťových prostředcích

| Úkol | Příkaz |
| ---- | ------- |
| Seznam virtuálních sítí |[$myResourceGroup Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName<BR><BR>Zobrazí seznam všech virtuálních sítí ve skupině prostředků. |
| Získání informací o virtuální síti |Get-AzVirtualNetwork -Název myVNet -ResourceGroupName $myResourceGroup |
| Seznam podsítí ve virtuální síti |Get-AzVirtualNetwork -Název "myVNet" -ResourceGroupName $myResourceGroup &#124; Vybrat podsítě |
| Získání informací o podsíti |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Název "mySubnet1" -VirtualNetwork $vnet<BR><BR>Získá informace o podsíti v zadané virtuální síti. Hodnota $vnet představuje objekt vrácený get-azVirtualNetwork. |
| Seznam adres IP |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam veřejných adres IP ve skupině prostředků. |
| Vyvyčovávače zatížení seznamu |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam všech vykladačů zatížení ve skupině prostředků. |
| Seznam síťových rozhraní |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam všech síťových rozhraní ve skupině prostředků. |
| Získání informací o síťovém rozhraní |Get-AzNetworkInterface -Název "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Získá informace o konkrétní síťové rozhraní. |
| Získání konfigurace IP síťového rozhraní |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Název "myNICIP" -NetworkInterface $nic<BR><BR>Získá informace o konfiguraci IP zadaného síťového rozhraní. Hodnota $nic představuje objekt vrácený rozhraním Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Správa síťových prostředků

| Úkol | Příkaz |
| ---- | ------- |
| Přidání podsítě do virtuální sítě |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X.X.X/XX -Název "mySubnet1" -VirtualNetwork $vnet<BR><BR>Přidá podsíť do existující virtuální sítě. Hodnota $vnet představuje objekt vrácený get-azVirtualNetwork. |
| Odstranění virtuální sítě |[Odebrat-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Název "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadanou virtuální síť ze skupiny prostředků. |
| Odstranění síťového rozhraní |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Název "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadané síťové rozhraní ze skupiny prostředků. |
| Odstranění nástroje pro vyrovnávání zatížení |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Název "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadaný vytápěč zatížení ze skupiny prostředků. |
| Odstranění veřejné IP adresy |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Název myIPAddress -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadanou veřejnou IP adresu ze skupiny prostředků. |

## <a name="next-steps"></a>Další kroky
Použijte síťové rozhraní, které jste právě vytvořili [při vytváření virtuálního virtuálního soudu](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


