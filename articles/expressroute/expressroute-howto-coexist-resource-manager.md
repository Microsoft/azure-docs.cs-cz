---
title: 'Konfigurace současně existujících připojení ExpressRoute a S2S VPN: Azure PowerShell'
description: Nakonfigurujte ExpressRoute a připojení VPN typu Site-to-site, které může existovat společně pro Správce prostředků model pomocí prostředí PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/06/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 3b6ed39c11e3f90b986ef904ff3f8e9ff3158d0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574165"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>Konfigurace současně existujících připojení mezi ExpressRoute a mezi lokalitami pomocí PowerShellu
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Classic](expressroute-howto-coexist-classic.md)
> 
> 

Tento článek vám pomůže nakonfigurovat ExpressRoute a připojení VPN typu Site-to-site, která se nacházejí společně. Možnost konfigurace VPN typu site-to-site a ExpressRoute má několik výhod. Můžete nakonfigurovat síť VPN typu Site-to-site jako zabezpečenou cestu převzetí služeb při selhání pro ExpressRoute, nebo použít VPN typu Site-to-site pro připojení k webům, které nejsou připojené prostřednictvím ExpressRoute. V tomto článku vám nabídneme postupy konfigurace pro oba scénáře. Tento článek se týká modelu nasazení Resource Manager.

Konfigurace ExpressRoute a současně existujících připojení VPN typu Site-to-Site má několik výhod.

* Můžete nakonfigurovat VPN typu Site-to-Site jako bezpečnou cestu převzetí služeb při selhání pro ExpressRoute. 
* Další možností je použít VPN typu Site-to-Site pro připojení k webům, které nejsou prostřednictvím ExpressRoute připojené. 

V tomto článku jsou postupy konfigurace pro oba scénáře. Tento článek se týká modelu nasazení Resource Manager a používá PowerShell. Tyto scénáře můžete nakonfigurovat také pomocí Azure Portal, i když dokumentace ještě není k dispozici. Obě brány můžete nakonfigurovat jako první. Při přidávání nové brány nebo připojení brány se obvykle neztratí žádné výpadky.

>[!NOTE]
>Pokud chcete vytvořit připojení VPN typu Site-to-Site přes okruh ExpressRoute, přečtěte si prosím [tento článek](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Omezení
* **Podporována je pouze brána VPN na základě tras.** Je nutné použít [bránu sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)založenou na trasách. Můžete také použít bránu sítě VPN založenou na trasách s připojením VPN nakonfigurovaným pro "selektory provozu na základě zásad", jak je popsáno v tématu [připojení k několika zařízením VPN založeným na zásadách](../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).
* **ASN Azure VPN Gateway musí být nastavené na 65515.** Azure VPN Gateway podporuje směrovací protokol BGP. Aby ExpressRoute a Azure VPN fungovaly společně, musíte zachovat číslo autonomního systému vaší brány Azure VPN Gateway na výchozí hodnotě 65515. Pokud jste dříve vybrali číslo ASN jiné než 65515 a změníte nastavení na 65515, musíte resetovat bránu VPN, aby se nastavení projevilo.
* **Podsíť brány musí být/27 nebo kratší předpona**(například/26,/25), nebo když přidáte bránu virtuální sítě ExpressRoute, zobrazí se chybová zpráva.
* **Koexistence ve virtuální síti s více zásobníky není podporovaná.** Pokud používáte podporu protokolu IPv6 ExpressRoute a ExpressRoute bránu se dvěma zásobníky, koexistence s VPN Gateway nebude možná.

## <a name="configuration-designs"></a>Návrhy konfigurace
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurace VPN typu site-to-site jako cesty převzetí služeb při selhání pro ExpressRoute
Můžete nakonfigurovat připojení VPN typu site-to-site jako záložní pro ExpressRoute. Toto připojení platí jenom pro virtuální sítě, které jsou propojené s cestou soukromého partnerského vztahu Azure. Řešení převzetí služeb při selhání založené na VPN pro služby, které jsou přístupné prostřednictvím partnerského vztahu Azure nebo partnerského vztahu Microsoftu, neexistuje. Okruh ExpressRoute je vždy primárním propojením. Data prochází cestou VPN typu Site-to-Site jenom v případě, že okruh ExpressRoute selže. Vaše místní síťová konfigurace by také měla před VPN typu Site-to-Site preferovat okruh ExpressRoute, abyste se vyhnuli asymetrickému směrování. Cestu ExpressRoute můžete preferovat nastavením vyšší místní předvolby pro trasy přijímané přes ExpressRoute. 

>[!NOTE]
> Pokud jste ExpressRoute partnerský vztah Microsoftu povolený, můžete na připojení ExpressRoute získat veřejnou IP adresu vaší brány Azure VPN. Pokud chcete nastavit připojení VPN typu Site-to-site jako zálohu, musíte nakonfigurovat místní síť tak, aby připojení VPN bylo směrované na Internet.
>

> [!NOTE]
> I když v případě, že jsou obě trasy stejné, je okruh ExpressRoute upřednostněný před VPN typu Site-to-Site, Azure k výběru trasy směrem k cíli paketu použije nejdelší shodu předpony.
> 
> 

![Diagram, který zobrazuje připojení VPN typu Site-to-site jako zálohu pro ExpressRoute.](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Konfigurace VPN typu site-to-site pro připojení webů, které nejsou připojené prostřednictvím ExpressRoute
Svoji síť můžete nakonfigurovat tak, že některé weby jsou připojené přímo k Azure prostřednictvím VPN typu site-to-site a některé weby přes ExpressRoute. 

![Současná existence](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Virtuální síť nemůžete nakonfigurovat jako směrovač provozu.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Výběr kroků k použití
Existují dvě různé sady postupů, ze kterých si můžete vybrat. Postup konfigurace, který vyberete, závisí na tom, jestli máte existující virtuální síť, ke které se chcete připojit, nebo chcete vytvořit novou virtuální síť.

* Nemám virtuální síť a potřebuji ji vytvořit.
  
    Pokud ještě nemáte virtuální síť, tento postup vás provede procesem vytvoření nové virtuální sítě pomocí modelu nasazení Resource Manager a vytvoření nových připojení ExpressRoute a VPN typu Site-to-Site. Pokud chcete konfigurovat virtuální síť, postupujte podle kroků v části [Vytvoření nové virtuální sítě a současně existujících připojení](#new).
* Už mám virtuální síť modelu nasazení Resource Manager.
  
    Už můžete mít virtuální síť s existujícím připojením VPN typu site-to-site nebo připojením ExpressRoute. Pokud je v tomto případě maska podsítě brány /28 nebo menší (/28, /29 atd.), je potřeba existující bránu odstranit. V části [Konfigurace současně existujících připojení pro už existující virtuální síť](#add) najdete postup odstranění brány a následného vytvoření nových připojení ExpressRoute a VPN typu Site-to-Site.
  
    Pokud síťovou bránou odstraníte a znovu vytvoříte, dojde u vašich připojení mezi různými místy k výpadku. Virtuální počítače a služby však budou během konfigurace brány stále schopné komunikovat prostřednictvím nástroje pro vyrovnávání zatížení, pokud jsou tak nakonfigurované.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Vytvoření nové virtuální sítě a koexistujících připojení
Tento postup vás provede procesem vytvoření virtuální sítě a připojení ExpressRoute a VPN typu Site-to-Site, která budou existovat společně. Rutiny, které použijete pro tuto konfiguraci, se můžou mírně lišit od těch, co znáte. Ujistěte se, že používáte rutiny určené v těchto pokynech.

1. Přihlaste se a vyberte své předplatné.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Nastavte proměnné.

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. Vytvořte virtuální síť včetně podsítě brány. Další informace o vytváření virtuálních sítí najdete v tématu [Vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Další informace o vytváření podsítí najdete v tématu [Vytvoření podsítě](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
   
   > [!IMPORTANT]
   > Podsíť brány musí být /27 nebo kratší předpona (například /26 nebo /25).
   > 
   > 
   
    Vytvořte novou virtuální síť.

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    Přidejte podsítě.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Uložte konfiguraci virtuální sítě.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>Dále vytvořte bránu VPN typu site-to-site. Další informace o konfiguraci brány VPN najdete v tématu [Konfigurace virtuální sítě s připojením typu site-to-site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). GatewaySku podporují pouze následující brány VPN: *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* a *HighPerformance*. Konfigurace současného fungování ExpressRoute a služby VPN Gateway nejsou podporované v základní SKU. VpnType musí být *RouteBased*.

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Brána Azure VPN podporuje směrovací protokol BGP. Můžete zadat ASN (číslo AS) pro tuto virtuální síť přidáním přepínače -Asn do následujícího příkazu. V případě nezadání parametru se použije výchozí číslo AS 65515.

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    IP adresu partnerských vztahů protokolu BGP a číslo AS, které Azure používá pro bránu VPN, najdete v $azureVpn.BgpSettings.BgpPeeringAddress a $azureVpn.BgpSettings.Asn. Další informace najdete v tématu [Konfigurace protokolu BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) pro bránu VPN Azure.
5. Vytvořte entitu brány VPN místního webu. Tento příkaz neprovede konfiguraci vaší místní brány VPN. Místo toho umožní zadat nastavení místní brány, jako je například veřejná IP adresa a místní adresní prostor, aby se brána Azure VPN k nim mohla připojit.
   
    Pokud vaše místní zařízení VPN podporuje pouze statické směrování, můžete nakonfigurovat statické trasy následujícím způsobem:

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    Pokud vaše místní zařízení VPN podporuje protokol BGP a chcete povolit dynamické trasování, potřebujete znát IP adresu partnerských vztahů protokolu BGP a číslo AS, které vaše místní zařízení VPN používá.

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. Nakonfigurujte místní zařízení VPN pro připojení k nové bráně Azure VPN. Další informace o konfiguraci zařízení VPN najdete v tématu [Konfigurace zařízení VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Propojte bránu VPN typu site-to-site v Azure s místní bránou.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. Pokud se připojujete k existujícímu okruhu ExpressRoute, kroky 8 a 9 přeskočte a přejděte rovnou ke kroku 10. Nakonfigurujte okruhy ExpressRoute. Další informace o konfiguraci okruhu ExpressRoute najdete v tématu o [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md).


9. Nakonfigurujte soukromý partnerský vztah Azure přes okruh ExpressRoute. Další informace o konfiguraci soukromého partnerského vztahu Azure přes okruh ExpressRoute najdete v tématu o [konfiguraci partnerského vztahu](expressroute-howto-routing-arm.md).

10. <a name="gw"></a>Vytvořte bránu ExpressRoute. Další informace o konfiguraci brány ExpressRoute najdete v tématu [Konfigurace brány ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). GatewaySKU musí být *Standard*, *HighPerformance* nebo *UltraPerformance*.

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. Propojte bránu ExpressRoute s okruhem ExpressRoute. Po dokončení tohoto kroku bude připojení mezi místní sítí a Azure prostřednictvím ExpressRoute vytvořeno. Další informace o operaci propojení najdete v tématu [Propojení virtuálních sítí s ExpressRoute](expressroute-howto-linkvnet-arm.md).

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Konfigurace současně existujících připojení pro už existující virtuální síť
Pokud máte virtuální síť, která obsahuje pouze jednu bránu virtuální sítě (např. bránu VPN typu Site-to-Site), a chcete přidat další bránu jiného typu (např. bránu ExpressRoute), zkontrolujte velikost podsítě brány. Pokud je velikost podsítě brány /27 nebo větší, můžete přeskočit následující kroky a podle kroků v předchozí části přidat bránu VPN typu Site-to-Site nebo bránu ExpressRoute. Pokud je podsíť brány /28 nebo /29, musíte nejdřív bránu virtuální sítě odstranit a zvýšit velikost podsítě brány. Postup v této části ukazuje, jak to provést.

Rutiny, které použijete pro tuto konfiguraci, se můžou mírně lišit od těch, co znáte. Ujistěte se, že používáte rutiny určené v těchto pokynech.

1. Odstraňte existující bránu ExpressRoute nebo VPN typu site-to-site.

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. Odstraňte podsíť brány.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. Přidejte podsíť brány, která je /27 nebo větší.
   
   > [!NOTE]
   > Pokud vám ve virtuální síti nezbylo dost IP adres pro zvětšení velikosti podsítě brány, budete muset přidat další adresní prostor IP adres.
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Uložte konfiguraci virtuální sítě.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. V tuto chvíli máte virtuální síť, která nemá žádné brány. Pokud chcete vytvořit nové brány a nastavit připojení, použijte následující příklady:

   Nastavte proměnné.

    ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   ```

   Vytvořte bránu.

   ```azurepowershell-interactive
   $gw = New-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup> -Location <yourlocation> -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
   ```

   Vytvořte připojení.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
   New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
   ```

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Přidání konfigurace point-to-site k bráně VPN

Pomocí následujících kroků můžete přidat konfiguraci Point-to-site k bráně VPN v Nastavení koexistence. Pokud chcete nahrát kořenový certifikát sítě VPN, musíte místně nainstalovat PowerShell do svého počítače nebo použít Azure Portal.

1. Přidejte fond adres klienta VPN.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. Nahrajte do Azure [kořenový certifikát](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#Certificates) VPN pro vaši bránu VPN. V tomto příkladu se předpokládá, že kořenový certifikát je uložený v místním počítači, na kterém jsou spuštěné následující rutiny PowerShellu a že se místně spouští PowerShell. Certifikát můžete také nahrát pomocí Azure Portal.

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```
Další informace o VPN typu point-to-site najdete v tématu [Konfigurace připojení typu point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="to-enable-transit-routing-between-expressroute-and-azure-vpn"></a>Umožnění směrování přenosu mezi ExpressRoute a Azure VPN
Pokud chcete povolit připojení mezi jednou z vaší místní sítě, která je připojená k ExpressRoute a jiné z vaší místní sítě, která je připojená k připojení VPN typu Site-to-site, budete muset nastavit [Server směrování Azure](../route-server/expressroute-vpn-support.md).


## <a name="next-steps"></a>Další kroky
Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
