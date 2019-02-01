---
title: 'Konfigurace vynuceného tunelování pro Azure Site-to-Site připojení: Resource Manager | Dokumentace Microsoftu'
description: Jak přesměrování nebo "Vynutit" veškerý provoz směřující na Internet zpět do místního umístění.
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
ms.openlocfilehash: 21004c29f1baf0346cd83d8483ff1862a98fc845
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506460"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manageru

Vynucené tunelování umožňuje přesměrování nebo "Vynutit" veškerý provoz směřující na Internet zpět do místního umístění prostřednictvím tunelu VPN typu Site-to-Site pro kontrolu a auditování. Toto je důležité zabezpečení požadavek pro většinu podnikových IT zásady. Bez vynucené tunelování, internetový provoz z virtuálních počítačů v Azure vždy překračuje z Azure síťovou infrastrukturu přímo na Internetu, bez možnosti a umožňuje tak kontrolovat nebo auditování provozu. Neoprávněný přístup k Internetu může potenciálně vést k zpřístupnění informací nebo jiných druhů porušení zabezpečení.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Tento článek vás provede konfigurací vynuceného tunelového propojení virtuální sítě vytvořené pomocí modelu nasazení Resource Manager. Vynucené tunelování se dá konfigurovat pomocí prostředí PowerShell, na portálu. Pokud chcete nakonfigurovat vynucené tunelování pro model nasazení classic, vyberte z rozevíracího seznamu následující klasické článku:

> [!div class="op_single_selector"]
> * [PowerShell – Classic](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Informace o vynuceném tunelování

Následující diagram znázorňuje, jak vynucené tunelování funguje. 

![Vynucené tunelování](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

V předchozím příkladu front-endové podsítě není vynucené tunelové propojení. Úlohy ve front-endové podsíti můžete nadále přijímat a reagovat na požadavky zákazníků z Internetu přímo. Střední vrstvě a back-endové podsítě jsou vynuceného tunelového propojení. Odchozí připojení k Internetu, tyto dvě podsítě se vynucené nebo přesměrován zpět do místní lokality přes jeden tunel VPN s2s.

Můžete omezit a kontrolovat přístup k Internetu z vašich virtuálních počítačů nebo cloudových služeb v Azure a přitom dál povolit architektury víceúrovňová služba vyžaduje. Pokud ve virtuálních sítích nejsou žádné úlohy přístupem k Internetu, můžete také provést vynucené tunelové propojení pro celý virtuální sítě.

## <a name="requirements-and-considerations"></a>Požadavky a předpoklady

Vynucené tunelování v Azure se konfiguruje prostřednictvím virtuální sítě trasy definované uživatelem. Přesměrování přenosů do místní lokality je vyjádřena jako výchozí trasu pro bránu Azure VPN. Další informace o směrování definovaného uživatelem a virtuálními sítěmi najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).

* Každé podsíti virtuální sítě má integrované, směrovací tabulky systému. Systémovou tabulku směrování má následující tři skupiny tras:
  
  * **Místní virtuální sítě trasy:** Přímo do cílového umístění virtuálních počítačů ve stejné virtuální síti.
  * **Místní trasy:** Ke službě Azure VPN gateway.
  * **Výchozí trasy:** Přímo k Internetu. Pakety směřující na privátní IP adresy není pokrytá předchozí dvě trasy se zahodí.
* Tento postup používá trasy definované uživatelem (UDR) k vytvoření směrovací tabulky k přidání výchozí trasy a přidružte směrovací tabulky do vaší virtuální síti tento počet podsítí: Povolit vynucené tunelování na těchto podsítí.
* Vynucené tunelování musí být přidružen virtuální síť, která má bránu sítě VPN založené na směrování. Budete muset nastavit "výchozí web" mezi místy místní servery připojené k virtuální síti. Navíc místního zařízení VPN musí být nakonfigurovaný pomocí 0.0.0.0/0 jako selektory provozu. 
* ExpressRoute se vynucené tunelování přes tento mechanismus není nakonfigurovaná, ale místo toho zajišťuje inzeruje výchozí trasu prostřednictvím relací vytvoření partnerského vztahu protokolu BGP ExpressRoute. Další informace najdete v tématu [dokumentace ke službě ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Přehled konfigurace

Následující postup vám pomůže vytvořit skupinu prostředků a virtuální sítě. Potom vytvořte bránu VPN a konfigurace vynuceného tunelování. Virtuální síť "MultiTier virtuální sítě" v tomto postupu má tři podsítě: "Frontend", "Midtier" a "Backend", s čtyřmi různými místy: "DefaultSiteHQ" a tři větve.

Kroky postupu nastavte "DefaultSiteHQ' jako výchozí web připojení pro vynucené tunelování a konfigurovat"Midtier"a"Backend"podsítě pro použití vynuceného tunelování.

## <a name="before"></a>Než začnete

Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

> [!IMPORTANT]
> Instalace nejnovější verzi rutin Powershellu je povinný. Při spuštění některých z rutiny, jinak může vyskytnout chyby ověření.
>
>

### <a name="to-log-in"></a>Pro přihlášení

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Konfigurace vynuceného tunelování

> [!NOTE]
> Může se zobrazit upozornění oznamující "typ objektu výstup této rutiny se změní v budoucí verzi". Toto je očekávané chování a tato upozornění můžete klidně ignorovat.
>
>


1. Vytvořte skupinu prostředků.

  ```powershell
  New-AzureRmResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Vytvoření virtuální sítě a zadejte podsítě.

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
4. Vytvoření směrovací tabulky a trasy pravidla.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
5. Přidružení směrovací tabulky k podsítím provozu Midtier a back-endu.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Vytvořte bránu virtuální sítě. Tento krok trvá delší dobu, někdy 45 minut nebo déle, protože jsou vytváření a konfiguraci brány. Pokud se zobrazí chyby ValidateSet týkající se hodnota GatewaySKU, ověřte, že máte nainstalovanou [nejnovější verzi rutin Powershellu](#before). Nejnovější verzi rutin Powershellu obsahuje nové ověřené hodnoty pro nejnovější skladové položky brány.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
  ```
7. Přiřadíte výchozí lokalitu k bráně virtuální sítě. **- GatewayDefaultSite** je parametr rutiny, která umožňuje konfiguraci vynucené směrování fungovat, proto opatrní nakonfigurovat toto nastavení správně. 

  ```powershell
  $LocalGateway = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
  $VirtualGateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
  ```
8. Vytvoření připojení Site-to-Site VPN.

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
