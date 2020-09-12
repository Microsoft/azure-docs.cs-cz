---
title: Konfigurace vynuceného tunelování pro připojení typu Site-to-site
description: Postup přesměrování nebo vynucení veškerého provozu vázaného na Internet zpátky do místního umístění.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e9444291c40ef504a674ee18351ba581695d1dd3
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394513"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manageru

Vynucené tunelování umožňuje přesměrování nebo „vynucení“ směrování veškerého provozu vázaného na internet zpět do místního umístění prostřednictvím tunelu VPN typu site-to-site pro kontrolu a auditování. Toto je zásadní požadavek na zabezpečení pro většinu podnikových zásad IT. Bez vynuceného tunelování se internetový provoz z vašich virtuálních počítačů v Azure vždycky projde z síťové infrastruktury Azure přímo na Internet bez možnosti kontroly a auditu provozu. Neoprávněný přístup k Internetu může potenciálně vést k odhalení informací nebo jiným typům porušení zabezpečení.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Tento článek vás provede konfigurací vynuceného tunelování pro virtuální sítě vytvořené pomocí modelu nasazení Správce prostředků. Vynucené tunelování se dá nakonfigurovat pomocí PowerShellu, ne přes portál. Pokud chcete nakonfigurovat vynucené tunelové propojení pro model nasazení Classic, vyberte z následujícího rozevíracího seznamu klasický článek:

> [!div class="op_single_selector"]
> * [PowerShell – Classic](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>O vynuceném tunelování

Následující diagram znázorňuje, jak funguje vynucené tunelování. 

![Vynucené tunelování](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

V předchozím příkladu není podsíť front-end vynucená tunelem. Úlohy v podsíti front-endu mohou nadále přijímat a reagovat na žádosti zákazníků přímo z Internetu. Podsítě střední úrovně a back-end jsou vynucené tunelování. Všechna odchozí připojení z těchto dvou podsítí na Internet se vynutí nebo přesměrují zpátky na místní lokalitu prostřednictvím jednoho z tunelových propojení VPN S2S.

Díky tomu můžete omezit a prozkoumat přístup k Internetu z virtuálních počítačů nebo cloudových služeb v Azure a zároveň pokračovat v povolení vaší vícevrstvé architektury služeb. Pokud ve virtuálních sítích neexistují žádné úlohy s přístupem k Internetu, můžete také použít vynucené tunelování pro celé virtuální sítě.

## <a name="requirements-and-considerations"></a>Požadavky a předpoklady

Vynucené tunelování v Azure se konfiguruje prostřednictvím uživatelsky definovaných tras definovaných pomocí virtuální sítě. Přesměrování provozu do místní lokality se vyjádří jako výchozí trasa k bráně Azure VPN. Další informace o uživatelem definovaných směrování a virtuálních sítích najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).

* Každá podsíť virtuální sítě má vestavěnou systémovou směrovací tabulku. Tabulka směrování systému má následující tři skupiny tras:
  
  * **Trasy místní virtuální sítě:** Přímo k cílovým virtuálním počítačům ve stejné virtuální síti.
  * **Místní trasy:** K bráně Azure VPN Gateway.
  * **Výchozí trasa:** Přímo na Internet. Pakety určené k privátním IP adresám, které nejsou pokryté předchozími dvěma trasami, jsou vyřazeny.
* Tento postup používá uživatelem definované trasy (UDR) k vytvoření směrovací tabulky pro přidání výchozí trasy a následné přidružení směrovací tabulky k vašim virtuálním podsítím, aby bylo možné v těchto podsítích povolit vynucené tunelové propojení.
* Vynucené tunelování musí být přidružené k virtuální síti, která má bránu sítě VPN založenou na trasách. Musíte nastavit výchozí lokalitu mezi místními lokalitami, které jsou připojené k virtuální síti. Místní zařízení VPN musí být také nakonfigurováno s použitím 0.0.0.0/0 jako selektorů provozu. 
* Vynucené tunelování ExpressRoute není nakonfigurované prostřednictvím tohoto mechanismu, ale místo toho je povolené inzerováním výchozí trasy prostřednictvím relací partnerských vztahů protokolu BGP ExpressRoute. Další informace najdete v [dokumentaci k ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Přehled konfigurace

Následující postup vám pomůže vytvořit skupinu prostředků a virtuální síť. Pak vytvoříte bránu VPN a nakonfigurujete vynucené tunelování. V tomto postupu má virtuální síť s více vrstvami VNet tři podsítě: front-end, Midtier a back-end se čtyřmi připojeními mezi různými místy: "DefaultSiteHQ" a tři větve.

Kroky pro vynucené tunelové propojení nastaví "DefaultSiteHQ" jako výchozí připojení lokality a nakonfigurují podsítě Midtier a back-end, aby používaly vynucené tunelování.

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/).

> [!IMPORTANT]
> Vyžaduje se instalace nejnovější verze rutin PowerShellu. V opačném případě může při spuštění některých rutin docházet k chybám ověřování.
>
>

### <a name="to-log-in"></a>Přihlášení

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Konfigurace vynuceného tunelování

> [!NOTE]
> Můžou se zobrazit upozornění, že typ výstupního objektu této rutiny se v budoucí verzi upraví. Jedná se o očekávané chování a tato upozornění můžete bezpečně ignorovat.
>
>


1. Vytvořte skupinu prostředků.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Vytvořte virtuální síť a zadejte podsítě.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Vytvořte brány místní sítě.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Vytvořte směrovací tabulku a pravidlo směrování.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Přidružte směrovací tabulku k podsítím Midtier a back-endu.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Vytvořte bránu virtuální sítě. Dokončení tohoto kroku trvá určitou dobu, někdy 45 minut nebo i déle, protože bránu vytváříte a konfigurujete. Pokud se zobrazí chyby ValidateSet týkající se hodnoty GatewaySKU, ověřte, že máte nainstalovanou [nejnovější verzi rutin PowerShellu](#before). Nejnovější verze rutin PowerShellu obsahují nové ověřené hodnoty pro nejnovější SKU brány.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Přiřaďte k bráně virtuální sítě výchozí lokalitu. Parametr **-GatewayDefaultSite** je parametr rutiny, který umožňuje, aby Konfigurace vynuceného směrování fungovala, proto je potřeba pečlivě nakonfigurovat toto nastavení. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Vytvořte připojení VPN typu Site-to-site.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
