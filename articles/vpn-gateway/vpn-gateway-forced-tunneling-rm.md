---
title: Konfigurace vynuceného tunelového propojení pro připojení mezi lokalitami
description: Jak přesměrovat nebo vynutit veškerý internetový provoz zpět do místního umístění.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: fc35654403bbe1375d4188476b11fd0453f74345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244625"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manageru

Vynucené tunelování umožňuje přesměrování nebo „vynucení“ směrování veškerého provozu vázaného na internet zpět do místního umístění prostřednictvím tunelu VPN typu site-to-site pro kontrolu a auditování. Jedná se o kritický požadavek zabezpečení pro většinu podnikových zásad IT. Bez vynuceného tunelového propojení se internetový provoz z vašich virtuálních aplikací v Azure vždy pohybuje ze síťové infrastruktury Azure přímo na Internet, aniž by vám umožnil kontrolovat nebo auditovat provoz. Neoprávněný přístup k Internetu může potenciálně vést ke zveřejnění informací nebo k jiným typům narušení zabezpečení.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Tento článek vás provede konfigurací vynuceného tunelového propojení pro virtuální sítě vytvořené pomocí modelu nasazení Správce prostředků. Vynucené tunelové propojení lze nakonfigurovat pomocí prostředí PowerShell, nikoli prostřednictvím portálu. Pokud chcete nakonfigurovat vynucené tunelové propojení pro klasický model nasazení, vyberte klasický článek z následujícího rozevíracího seznamu:

> [!div class="op_single_selector"]
> * [PowerShell – Classic](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>O nuceném tunelování

Následující diagram znázorňuje, jak funguje vynucené tunelové propojení. 

![Vynucené tunelování](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Ve výše uvedeném příkladu není podsíť front-endu vynuceně tunelována. Úlohy v podsíti front-endu mohou nadále přijímat a odpovídat na požadavky zákazníků z Internetu přímo. Podsítě Střední vrstvy a Back-end jsou vynuceně tunelovány. Všechna odchozí připojení z těchto dvou podsítí do Internetu budou vynucena nebo přesměrována zpět do místního webu prostřednictvím jednoho z tunelových propojení S2S VPN.

To vám umožní omezit a kontrolovat přístup k Internetu z vašich virtuálních počítačů nebo cloudových služeb v Azure a zároveň pokračovat v povolení požadované architektury vícevrstvých služeb. Pokud ve virtuálních sítích nejsou žádné úlohy pro připojení k Internetu, můžete také použít vynucené tunelové propojení pro celou virtuální sítě.

## <a name="requirements-and-considerations"></a>Požadavky a úvahy

Vynucené tunelové propojení v Azure se nakonfiguruje pomocí uživatelem definovaných tras virtuální sítě. Přesměrování provozu na místní web se vyjadřuje jako výchozí trasa do brány Azure VPN. Další informace o uživatelem definovaných směrováních a virtuálních sítích naleznete v [tématu Uživatelem definované trasy a předávání IP](../virtual-network/virtual-networks-udr-overview.md)adres .

* Každá podsíť virtuální sítě má integrovanou systémovou směrovací tabulku. Systémová směrovací tabulka obsahuje následující tři skupiny tras:
  
  * **Místní trasy virtuální sítě:** Přímo do cílových virtuálních počítačů ve stejné virtuální síti.
  * **Místní trasy:** K bráně Azure VPN.
  * **Výchozí trasa:** Přímo na internet. Pakety určené pro privátní IP adresy, které nejsou zahrnuty v předchozích dvou trasách, jsou vynechány.
* Tento postup používá uživatelem definované trasy (UDR) k vytvoření směrovací tabulky pro přidání výchozí trasy a následné přidružování směrovací tabulky k podsíti virtuální sítě, aby bylo možné vynucené tunelování v těchto podsítích.
* Vynucené tunelové propojení musí být přidruženo k virtuální síti, která má bránu VPN založenou na trasách. Je třeba nastavit "výchozí lokalitu" mezi místními weby mezi místními prostory připojenými k virtuální síti. Místní zařízení VPN musí být také nakonfigurováno pomocí 0.0.0.0/0 jako voliče přenosů. 
* Vynucené tunelové propojení ExpressRoute není nakonfigurováno prostřednictvím tohoto mechanismu, ale místo toho je povoleno inzerováním výchozí trasy prostřednictvím relací partnerského vztahu ExpressRoute BGP. Další informace naleznete v [dokumentaci ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Přehled konfigurace

Následující postup vám pomůže vytvořit skupinu prostředků a virtuální síť. Poté vytvoříte bránu VPN a nakonfigurujete vynucené tunelové propojení. V tomto postupu virtuální síť "MultiTier-VNet" má tři podsítě: "Frontend", "Midtier" a "Backend", se čtyřmi připojeními mezi různými místy: "DefaultSiteHQ" a tři větve.

Postup kroky nastavit 'DefaultSiteHQ' jako výchozí připojení k síti pro vynucené tunelové propojení a nakonfigurovat podsítě Midtier a Back-end použít vynucené tunelové propojení.

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

> [!IMPORTANT]
> Je vyžadována instalace nejnovější verze rutin prostředí PowerShell. V opačném případě může dojít k chybám ověření při spuštění některé rutiny.
>
>

### <a name="to-log-in"></a>Přihlášení

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Konfigurace vynuceného tunelování

> [!NOTE]
> Může se zobrazit upozornění" Typ výstupního objektu této rutiny bude upraven v budoucí verzi". Toto je očekávané chování a můžete bezpečně ignorovat tato upozornění.
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
4. Vytvořte tabulku postupu a pravidlo trasy.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Přidružte směrovací tabulku k podsítím Midtier a Back-end.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Vytvořte bránu virtuální sítě. Tento krok trvá nějakou dobu k dokončení, někdy 45 minut nebo více, protože vytváříte a konfigurujete bránu. Pokud se zobrazí chyby ValidateSet týkající se hodnoty GatewaySKU, ověřte, zda jste nainstalovali [nejnovější verzi rutin prostředí PowerShell](#before). Nejnovější verze rutin prostředí PowerShell obsahuje nové ověřené hodnoty pro nejnovější skum brány.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Přiřaďte výchozí web k bráně virtuální sítě. **-GatewayDefaultSite** je parametr rutiny, který umožňuje konfiguraci vynuceného směrování, takže dávejte pozor, abyste toto nastavení správně nakonfigurovali. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Navázání připojení VPN mezi lokalitami.

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
