---
title: Běžné příkazy PowerShellu pro virtuální sítě Azure
description: Běžné příkazy PowerShellu, které vám pomohou začít vytvářet virtuální síť a její přidružené prostředky pro virtuální počítače.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b4d6b20e63c42616aad0f8776fae159a0f2aa455
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088372"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Běžné příkazy PowerShellu pro virtuální sítě Azure

Pokud chcete vytvořit virtuální počítač, musíte vytvořit [virtuální síť](../../virtual-network/virtual-networks-overview.md) nebo znát existující virtuální síť, ve které je možné virtuální počítač přidat. Při vytváření virtuálního počítače je také potřeba zvážit vytvoření prostředků popsaných v tomto článku.

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

Některé proměnné mohou být užitečné při spuštění více než jednoho z příkazů v tomto článku:

- $location – umístění síťových prostředků. Pomocí [Get-AzLocation](/powershell/module/az.resources/get-azlocation) můžete najít [geografickou oblast](https://azure.microsoft.com/regions/) , která vám bude vyhovovat.
- $myResourceGroup – název skupiny prostředků, ve které se nacházejí síťové prostředky.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

| Úloha | Příkaz |
| ---- | ------- |
| Vytvoření konfigurací podsítí |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1"-AddressPrefix xx. X. X. X/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig-Name "mySubnet2"-AddressPrefix XX. X. X. X/XX<BR><BR>Typická síť může mít podsíť pro [internetový nástroj pro vyrovnávání zatížení](../../load-balancer/load-balancer-overview.md) a samostatnou podsíť pro [interní nástroj pro vyrovnávání zatížení](../../load-balancer/load-balancer-overview.md). |
| Vytvoření virtuální sítě |$vnet = [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $MyResourceGroup-Location $Location-AddressPrefix xx. X. X. X/XX-podsíť $subnet 1, $subnet 2 |
| Testování jedinečného názvu domény |[Test-AzDnsAvailability](/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" – Umístění $Location<BR><BR>Pro [prostředek veřejné IP](../../virtual-network/public-ip-addresses.md)adresy můžete zadat název domény DNS, který vytvoří mapování pro domainname.Location.cloudapp.Azure.com na veřejnou IP adresu na serverech DNS spravovaných Azure. Název může obsahovat pouze písmena, číslice a pomlčky. První a poslední znak musí být písmeno nebo číslo a název domény musí být v rámci svého umístění Azure jedinečný. Pokud je vrácena **hodnota true** , je navržený název globálně jedinečný. |
| Vytvoření veřejné IP adresy |$pip = [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp"-ResourceGroupName $MyResourceGroup-DomainNameLabel "myDNS"-Location $Location-element allocationmethod Dynamic<BR><BR>Veřejná IP adresa používá název domény, který jste dříve otestovali a kterou používá konfigurace front-endu nástroje pro vyrovnávání zatížení. |
| Vytvoření konfigurace IP adresy front-endu |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP"-PublicIpAddress $PIP<BR><BR>Konfigurace front-endu zahrnuje veřejnou IP adresu, kterou jste dříve vytvořili pro příchozí síťový provoz. |
| Vytvoření fondu back-endových adres |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Poskytuje interní adresy pro back-end nástroje pro vyrovnávání zatížení, ke kterým se používá síťové rozhraní. |
| Vytvoření testu paměti |$healthProbe = [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe"-RequestPath "healthProbe. aspx"-Protocol HTTP-port 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>Obsahuje sondy stavu sloužící ke kontrole dostupnosti instancí virtuálních počítačů ve fondu back-end adres. |
| Vytvoření pravidla vyrovnávání zatížení |$lbRule = [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) -Name http-FrontendIpConfiguration $FrontendIP-BackendAddressPool $BeAddressPool-probe $HealthProbe-Protocol TCP-FrontendPort 80-BackendPort 80<BR><BR>Obsahuje pravidla, která přiřazují veřejný port v nástroji pro vyrovnávání zatížení do portu ve fondu back-end adres. |
| Vytvoření příchozího pravidla NAT |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1"-FrontendIpConfiguration $FrontendIP-Protocol TCP-FrontendPort 3441-BackendPort 3389<BR><BR>Obsahuje pravidla, která mapují veřejný port v nástroji pro vyrovnávání zatížení na port pro konkrétní virtuální počítač ve fondu back-end adres. |
| Vytvoření nástroje pro vyrovnávání zatížení |$loadBalancer = [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-Name "myLoadBalancer" – Location $Location-FrontendIpConfiguration $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $BeAddressPool-probe $healthProbe |
| Vytvoření síťového rozhraní |$nic 1 = [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-Name "myNIC" – Location $Location-PrivateIpAddress xx. X. X. X-Subnet $subnet 2-LoadBalancerBackendAddressPool $loadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $loadBalancer. InboundNatRules [0]<BR><BR>Vytvořte síťové rozhraní pomocí veřejné IP adresy a podsítě virtuální sítě, kterou jste vytvořili dříve. |

## <a name="get-information-about-network-resources"></a>Získat informace o síťových prostředcích

| Úloha | Příkaz |
| ---- | ------- |
| Vypsat virtuální sítě |[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Vypíše všechny virtuální sítě ve skupině prostředků. |
| Získání informací o virtuální síti |Get-AzVirtualNetwork-Name "myVNet"-ResourceGroupName $myResourceGroup |
| Vypíše podsítě ve virtuální síti. |Get-AzVirtualNetwork-Name "myVNet"-ResourceGroupName $myResourceGroup &#124; vybrat podsítě |
| Získání informací o podsíti |[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1"-VirtualNetwork $VNet<BR><BR>Načte informace o podsíti v zadané virtuální síti. Hodnota $vnet představuje objekt vrácený funkcí get-AzVirtualNetwork. |
| Seznam IP adres |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam veřejných IP adres ve skupině prostředků. |
| Seznam nástrojů pro vyrovnávání zatížení |[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam všech nástrojů pro vyrovnávání zatížení ve skupině prostředků. |
| Výpis síťových rozhraní |[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Zobrazí seznam všech síťových rozhraní ve skupině prostředků. |
| Získat informace o síťovém rozhraní |Get-AzNetworkInterface-Name "myNIC"-ResourceGroupName $myResourceGroup<BR><BR>Načte informace o konkrétním síťovém rozhraní. |
| Získání konfigurace protokolu IP síťového rozhraní |[Get-AzNetworkInterfaceIPConfig](/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP"-NetworkInterface $nic<BR><BR>Načte informace o konfiguraci protokolu IP zadaného síťového rozhraní. Hodnota $nic představuje objekt vrácený funkcí get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Správa síťových prostředků

| Úloha | Příkaz |
| ---- | ------- |
| Přidání podsítě do virtuální sítě |[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix xx. X. X. X/XX-Name "mySubnet1"-VirtualNetwork $vnet<BR><BR>Přidá podsíť do existující virtuální sítě. Hodnota $vnet představuje objekt vrácený funkcí get-AzVirtualNetwork. |
| Odstranění virtuální sítě |[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $myResourceGroup<BR><BR>Odebere zadanou virtuální síť ze skupiny prostředků. |
| Odstraní síťové rozhraní. |[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC"-ResourceGroupName $myResourceGroup<BR><BR>Odebere zadané síťové rozhraní ze skupiny prostředků. |
| Odstranění nástroje pro vyrovnávání zatížení |[Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer"-ResourceGroupName $myResourceGroup<BR><BR>Odebere zadaný Nástroj pro vyrovnávání zatížení ze skupiny prostředků. |
| Odstranění veřejné IP adresy |[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress"-ResourceGroupName $myResourceGroup<BR><BR>Odebere zadanou veřejnou IP adresu ze skupiny prostředků. |

## <a name="next-steps"></a>Další kroky
Použijte síťové rozhraní, které jste právě vytvořili při [vytváření virtuálního počítače](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
