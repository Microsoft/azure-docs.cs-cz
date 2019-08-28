---
title: Běžné příkazy PowerShellu pro virtuální sítě Azure | Microsoft Docs
description: Běžné příkazy PowerShellu, které vám pomohou začít vytvářet virtuální síť a její přidružené prostředky pro virtuální počítače.
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
ms.openlocfilehash: d7ab705291b8705994aed96f1d270f792e4b2fb0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102530"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Běžné příkazy PowerShellu pro virtuální sítě Azure

Pokud chcete vytvořit virtuální počítač, musíte vytvořit [virtuální síť](../../virtual-network/virtual-networks-overview.md) nebo znát existující virtuální síť, ve které je možné virtuální počítač přidat. Při vytváření virtuálního počítače je také potřeba zvážit vytvoření prostředků popsaných v tomto článku.

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

Některé proměnné mohou být užitečné při spuštění více než jednoho z příkazů v tomto článku:

- $location – umístění síťových prostředků. Pomocí [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) můžete najít [geografickou oblast](https://azure.microsoft.com/regions/) , která vám bude vyhovovat.
- $myResourceGroup – název skupiny prostředků, ve které se nacházejí síťové prostředky.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

| Úloha | Příkaz |
| ---- | ------- |
| Vytvoření konfigurací podsítí |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Typická síť může mít podsíť pro [internetový nástroj](../../load-balancer/load-balancer-internet-overview.md) pro vyrovnávání zatížení a samostatnou podsíť pro [interní nástroj](../../load-balancer/load-balancer-internal-overview.md)pro vyrovnávání zatížení. |
| Vytvoření virtuální sítě |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Testování jedinečného názvu domény |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" – Umístění $Location<BR><BR>Pro [prostředek veřejné IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)adresy můžete zadat název domény DNS, který vytvoří mapování pro domainname.Location.cloudapp.Azure.com na veřejnou IP adresu na serverech DNS spravovaných Azure. Název může obsahovat pouze písmena, číslice a pomlčky. První a poslední znak musí být písmeno nebo číslo a název domény musí být v rámci svého umístění Azure jedinečný. Pokud je vrácena **hodnota true** , je navržený název globálně jedinečný. |
| Vytvoření veřejné IP adresy |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp"-ResourceGroupName $MyResourceGroup-DomainNameLabel "myDNS"-Location $Location-element allocationmethod Dynamic<BR><BR>Veřejná IP adresa používá název domény, který jste dříve otestovali a kterou používá konfigurace front-endu nástroje pro vyrovnávání zatížení. |
| Vytvoření konfigurace IP adresy front-endu |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Konfigurace front-endu zahrnuje veřejnou IP adresu, kterou jste dříve vytvořili pro příchozí síťový provoz. |
| Vytvoření fondu back-endových adres |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Poskytuje interní adresy pro back-end nástroje pro vyrovnávání zatížení, ke kterým se používá síťové rozhraní. |
| Vytvoření testu paměti |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe"-RequestPath "healthProbe. aspx"-Protocol HTTP-port 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>Obsahuje sondy stavu sloužící ke kontrole dostupnosti instancí virtuálních počítačů ve fondu back-end adres. |
| Vytvoření pravidla vyrovnávání zatížení |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Obsahuje pravidla, která přiřazují veřejný port v nástroji pro vyrovnávání zatížení do portu ve fondu back-end adres. |
| Vytvoření příchozího pravidla NAT |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Obsahuje pravidla, která mapují veřejný port v nástroji pro vyrovnávání zatížení na port pro konkrétní virtuální počítač ve fondu back-end adres. |
| Vytvoření nástroje pro vyrovnávání zatížení |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-Name "myLoadBalancer"-Location $Location-FrontendIpConfiguration $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $ beAddressPool-PROBE $healthProbe |
| Vytvoření síťového rozhraní |$nic 1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-Name "myNIC" – Location $Location-PrivateIpAddress xx. X. X. X-Subnet $subnet 2-LoadBalancerBackendAddressPool $loadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $loadBalancer. InboundNatRules [0]<BR><BR>Vytvořte síťové rozhraní pomocí veřejné IP adresy a podsítě virtuální sítě, kterou jste vytvořili dříve. |

## <a name="get-information-about-network-resources"></a>Získat informace o síťových prostředcích

| Úloha | Příkaz |
| ---- | ------- |
| Vypsat virtuální sítě |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Vypíše všechny virtuální sítě ve skupině prostředků. |
| Získání informací o virtuální síti |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Vypíše podsítě ve virtuální síti. |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Získání informací o podsíti |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1"-VirtualNetwork $VNet<BR><BR>Načte informace o podsíti v zadané virtuální síti. Hodnota $vnet představuje objekt vrácený funkcí get-AzVirtualNetwork. |
| Seznam IP adres |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam veřejných IP adres ve skupině prostředků. |
| Seznam nástrojů pro vyrovnávání zatížení |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam všech nástrojů pro vyrovnávání zatížení ve skupině prostředků. |
| Výpis síťových rozhraní |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam všech síťových rozhraní ve skupině prostředků. |
| Získat informace o síťovém rozhraní |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Načte informace o konkrétním síťovém rozhraní. |
| Získání konfigurace protokolu IP síťového rozhraní |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP"-NetworkInterface $nic<BR><BR>Načte informace o konfiguraci protokolu IP zadaného síťového rozhraní. Hodnota $nic představuje objekt vrácený funkcí get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Správa síťových prostředků

| Úloha | Příkaz |
| ---- | ------- |
| Přidání podsítě do virtuální sítě |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Přidá podsíť do existující virtuální sítě. Hodnota $vnet představuje objekt vrácený funkcí get-AzVirtualNetwork. |
| Odstranění virtuální sítě |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadanou virtuální síť ze skupiny prostředků. |
| Odstraní síťové rozhraní. |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadané síťové rozhraní ze skupiny prostředků. |
| Odstranění nástroje pro vyrovnávání zatížení |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadaný Nástroj pro vyrovnávání zatížení ze skupiny prostředků. |
| Odstranění veřejné IP adresy |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Odebere zadanou veřejnou IP adresu ze skupiny prostředků. |

## <a name="next-steps"></a>Další kroky
* Použijte síťové rozhraní, které jste právě vytvořili při [vytváření virtuálního počítače](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Přečtěte si, jak můžete [vytvořit virtuální počítač s více síťovými rozhraními](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

