---
title: 'Nakonfigurujte vynucené tunelování pro připojení Azure Site-to-Site: Resource Manager | Microsoft Docs'
description: Postup přesměrování nebo "Vynutit" veškerý provoz vázaný na Internet na vaše místní umístění.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: 00330f49d4acc9bd2d720a60b743b78c86b08f86
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984137"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manageru

Vynucené tunelování vám umožní přesměrování nebo "Vynutit" veškerý provoz vázaný na Internet zpět na místní umístění prostřednictvím tunelu Site-to-Site VPN pro kontrolu a auditování. Požadavek kritické zabezpečení pro většinu organizace IT zásad. Bez vynutit tunelové internetový provozu z virtuálních počítačů v Azure vždy traverses od Azure síťové infrastruktury přímo se k Internetu, bez možnosti a umožní vám na svoji provoz. Neoprávněný přístup k Internetu může potenciálně vést k informacím nebo jiné typy narušení zabezpečení.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Tento článek vás provede procesem konfigurace vynucené tunelování pro virtuální sítě vytvořené pomocí modelu nasazení Resource Manager. Vynucené tunelování se dá konfigurovat pomocí prostředí PowerShell, ne prostřednictvím portálu. Pokud chcete konfigurovat vynucené tunelování pro model nasazení classic, vyberte z rozevíracího seznamu následující klasické článku:

> [!div class="op_single_selector"]
> * [PowerShell – Classic](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>O vynuceného tunelování

Následující diagram znázorňuje, jak vynucené tunelování funguje. 

![Vynucené tunelování](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

V předchozím příkladu tunelovým propojením front-endu podsíť není vynutit. Úlohy v podsíť Frontend můžete nadále přijímat a reagovat na požadavky zákazníků z Internetu přímo. Střední vrstvě a back-end podsítě, vynuceně přesunuty tunelového propojení. Odchozí připojení k Internetu tyto dvě podsítě bude vynutit nebo přesměrován zpět na místní web prostřednictvím jednoho tunelu S2S VPN.

To vám umožňuje omezit a kontrolovat přístup k Internetu z virtuálních počítačů nebo cloudových služeb v Azure, můžete nadále povolit vaší architektury víceúrovňová služba vyžaduje. Pokud nejsou žádná internetového zatížení ve virtuálních sítích, můžete také použít vynucené tunelování na celý virtuální sítě.

## <a name="requirements-and-considerations"></a>Požadavky a důležité informace

Vynucené tunelování v Azure je nakonfigurován pomocí virtuální síti trasy definované uživatelem. Přesměrování přenosů na místní web je vyjádřen jako výchozí směrování k bráně Azure VPN. Další informace o směrování definovaného uživatelem a virtuální sítě najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).

* Každá podsíť virtuální sítě má integrovanou, směrovací tabulky systému. Systémovou tabulku směrování má tyto tři skupiny tras:
  
  * **Místní virtuální síť trasy:** přímo do cílového umístění virtuálních počítačů ve stejné virtuální síti.
  * **Místní trasy:** k Azure VPN gateway.
  * **Výchozí trasu:** přímo k Internetu. Dojde ke ztrátě paketů určené na privátní IP adresy, které nejsou pokryty předchozí dva trasy.
* Tento postup používá trasy definované uživatelem (UDR) Chcete-li vytvořit směrovací tabulku, která chcete přidat výchozí trasu a pak přidružit do směrovací tabulky pro vaši virtuální síť podsítí povolit vynucené tunelování na těchto podsítí.
* Vynucené tunelování musí být přidružený virtuální síť, která má brána sítě VPN založené na trasách. Budete muset nastavit "výchozí web" mezi místní lokality mezi různými místy připojený k virtuální síti. Navíc místního zařízení VPN musí být nakonfigurovaný pomocí 0.0.0.0/0 jako provoz selektorů. 
* ExpressRoute vynuceného tunelování přes tento mechanismus není nakonfigurovaná, ale místo toho je ve inzeruje výchozí trasu prostřednictvím relace partnerského vztahu ExpressRoute BGP povolen. Další informace najdete v tématu [dokumentace ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Přehled konfigurace

Následující postup vám pomůže vytvořit skupinu prostředků a virtuální sítě. Potom můžete vytvořit bránu sítě VPN a nakonfigurujte vynucené tunelování. V tomto postupu virtuální sítě "MultiTier-VNet, má tři podsítě:"Frontend","Midtier"a"Backend", s čtyři mezi různými místy připojení: 'DefaultSiteHQ' a tři větve.

Kroky postupu nastavte "DefaultSiteHQ' jako výchozí připojení k webu pro vynucené tunelování a nakonfigurovat Midtier a podsítě"Backend"použití vynuceného tunelování.

## <a name="before"></a>Než začnete

Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

> [!IMPORTANT]
> Vyžaduje se instalace nejnovější verzi rutin prostředí PowerShell. Při spuštění některých z rutiny, jinak může zobrazit chyby ověření.
>
>

### <a name="to-log-in"></a>Přihlásit

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Konfigurace vynuceného tunelování

> [!NOTE]
> Může se zobrazit upozornění oznamující "typ objektu výstup této rutiny se změní v budoucí verzi". Toto je očekávané chování a tato upozornění můžete bezpečně ignorovat.
>
>


1. Vytvořte skupinu prostředků.

  ```powershell
  New-AzureRmResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Vytvoření virtuální sítě a určit podsítě.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
3. Vytvoření brány místní sítě.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
4. Vytvořte směrovací tabulku a pravidlo trasy.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
5. Přidružte směrovací tabulka k podsítím Midtier a back-end.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Vytvořte bránu virtuální sítě. Tento krok trvá delší dobu, někdy 45 minut nebo déle, protože jsou vytvoření a konfiguraci brány. Pokud se zobrazí chyby ValidateSet týkající se hodnota GatewaySKU, ověřte, zda jste nainstalovali [nejnovější verzi rutin prostředí PowerShell](#before). Nejnovější verzi rutin prostředí PowerShell s hodnotami novou ověřené pro nejnovější SKU brány.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
  ```
7. Přiřadíte výchozí web bránu virtuální sítě. **- GatewayDefaultSite** je parametr rutiny, která umožňuje vynucené konfigurace směrování fungovat, takže postará nakonfigurovat toto nastavení správně. 

  ```powershell
  $LocalGateway = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
  $VirtualGateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
  ```
8. Vytvořit připojení Site-to-Site VPN.

  ```powershell
  $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```
