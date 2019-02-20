---
title: 'Konfigurace připojení VPN Site-to-Site a ExpressRoute – existovat vedle sebe: PowerShell: Azure | Dokumentace Microsoftu'
description: Konfigurace ExpressRoute a připojení VPN typu Site-to-Site, která mohou existovat vedle sebe pro model Resource Manageru pomocí prostředí PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 5242f31ff35e367211d48157fd4953751d1bb148
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416288"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>Konfigurace ExpressRoute a Site-to-Site současně existujících připojení pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Classic](expressroute-howto-coexist-classic.md)
> 
> 


Tento článek vám pomůže nakonfigurovat připojení ExpressRoute a VPN typu Site-to-Site, která existovat vedle sebe. Možnost konfigurace VPN typu site-to-site a ExpressRoute má několik výhod. Můžete nakonfigurovat VPN typu Site-to-Site jako cestu zabezpečené převzetí služeb při selhání pro ExpressRoute, nebo použít VPN typu Site-to-Site pro připojení k webům, které nejsou připojené prostřednictvím ExpressRoute. V tomto článku vám nabídneme postupy konfigurace pro oba scénáře. Tento článek se týká modelu nasazení Resource Manager.

Konfigurace ExpressRoute a současně existujících připojení VPN typu Site-to-Site má několik výhod.

* Můžete nakonfigurovat VPN typu Site-to-Site jako bezpečnou cestu převzetí služeb při selhání pro ExpressRoute. 
* Další možností je použít VPN typu Site-to-Site pro připojení k webům, které nejsou prostřednictvím ExpressRoute připojené. 

V tomto článku jsou postupy konfigurace pro oba scénáře. Tento článek se týká modelu nasazení Resource Manager a používá PowerShell. Můžete také nakonfigurovat tyto scénáře pomocí webu Azure portal, i když dokumentaci ještě není k dispozici. Buď bránu můžete nakonfigurovat první. Při přidávání nové brány nebo připojení k bráně obvykle, budou účtovat nedojde k žádnému výpadku.



>[!NOTE]
>Pokud chcete vytvořit připojení VPN typu Site-to-Site přes okruh ExpressRoute, přečtěte si prosím [tento článek](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Omezení
* **Směrování provozu není podporováno.** Nemůžete provádět směrování (přes Azure) mezi místní sítí připojenou prostřednictvím sítě VPN typu site-to-site a místní sítí připojenou přes ExpressRoute.
* **Základní brána SKU není podporována.** Pro [bránu ExpressRoute](expressroute-about-virtual-network-gateways.md) a [bránu VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) je nutné použít jinou než základní bránu SKU.
* **Podporována je pouze brána VPN na základě tras.** Je nutné použít službu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) na základě tras.
* **Pro vaši bránu VPN by měla být nakonfigurována statická trasa.** Pokud je vaše místní síť připojená k ExpressRoute a síti VPN typu site-to-site, musíte mít v místní síti konfigurovanou statickou trasu, abyste mohli směrovat připojení VPN typu site-to-site do veřejného internetu.
* **Brána VPN výchozí číslo ASN 65515, pokud není zadaný.** Brána Azure VPN podporuje směrovací protokol BGP. Můžete zadat ASN (číslo AS) pro virtuální síť přidáním přepínače - Asn. Pokud nezadáte tento parametr, výchozí hodnota je číslo 65515. Můžete použít libovolné číslo ASN pro konfiguraci, ale pokud vyberete jinou hodnotu než 65515, musíte resetovat bránu pro nastavení projevilo.

## <a name="configuration-designs"></a>Návrhy konfigurace
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurace VPN typu site-to-site jako cesty převzetí služeb při selhání pro ExpressRoute
Můžete nakonfigurovat připojení VPN typu site-to-site jako záložní pro ExpressRoute. Toto připojení platí jenom pro virtuální sítě, které jsou propojené s cestou soukromého partnerského vztahu Azure. Řešení převzetí služeb při selhání založené na VPN pro služby, které jsou přístupné prostřednictvím partnerského vztahu Azure nebo partnerského vztahu Microsoftu, neexistuje. Okruh ExpressRoute je vždy primárním propojením. Data prochází cestou VPN typu Site-to-Site jenom v případě, že okruh ExpressRoute selže. Vaše místní síťová konfigurace by také měla před VPN typu Site-to-Site preferovat okruh ExpressRoute, abyste se vyhnuli asymetrickému směrování. Cestu ExpressRoute můžete preferovat nastavením vyšší místní předvolby pro trasy přijímané přes ExpressRoute. 

> [!NOTE]
> I když v případě, že jsou obě trasy stejné, je okruh ExpressRoute upřednostněný před VPN typu Site-to-Site, Azure k výběru trasy směrem k cíli paketu použije nejdelší shodu předpony.
> 
> 

![Současná existence](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

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

## <a name="new"></a>Vytvoření nové virtuální sítě a současně existujících připojení
Tento postup vás provede procesem vytvoření virtuální sítě a připojení ExpressRoute a VPN typu Site-to-Site, která budou existovat společně.

1. Nainstalujte nejnovější verzi rutin Azure PowerShellu. Informace o instalaci rutin najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/azurerm/overview). Rutiny, které použijete pro tuto konfiguraci, se můžou mírně lišit od těch, co znáte. Ujistěte se, že používáte rutiny určené v těchto pokynech.

1. Přihlaste se ke svému účtu a nastavení prostředí.

  ```powershell
  Connect-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65515
  ```
3. Vytvořte virtuální síť včetně podsítě brány. Další informace o vytváření virtuálních sítí najdete v tématu [Vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Další informace o vytváření podsítí najdete v tématu [Vytvoření podsítě](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
   
   > [!IMPORTANT]
   > Podsíť brány musí být /27 nebo kratší předpona (například /26 nebo /25).
   > 
   > 
   
    Vytvořte novou virtuální síť.

  ```powershell
  $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
  ```
   
    Přidejte podsítě.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Uložte konfiguraci virtuální sítě.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
4. <a name="vpngw"></a>Dále vytvořte bránu VPN typu site-to-site. Další informace o konfiguraci brány VPN najdete v tématu [Konfigurace virtuální sítě s připojením typu site-to-site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). GatewaySku podporují pouze následující brány VPN: *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* a *HighPerformance*. Konfigurace současného fungování ExpressRoute a služby VPN Gateway nejsou podporované v základní SKU. VpnType musí být *RouteBased*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
  ```
   
    Brána Azure VPN podporuje směrovací protokol BGP. Můžete zadat ASN (číslo AS) pro tuto virtuální síť přidáním přepínače -Asn do následujícího příkazu. V případě nezadání parametru se použije výchozí číslo AS 65515.

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
  ```
   
    IP adresu partnerských vztahů protokolu BGP a číslo AS, které Azure používá pro bránu VPN, najdete v $azureVpn.BgpSettings.BgpPeeringAddress a $azureVpn.BgpSettings.Asn. Další informace najdete v tématu [Konfigurace protokolu BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) pro bránu VPN Azure.
5. Vytvořte entitu brány VPN místního webu. Tento příkaz neprovede konfiguraci vaší místní brány VPN. Místo toho umožní zadat nastavení místní brány, jako je například veřejná IP adresa a místní adresní prostor, aby se brána Azure VPN k nim mohla připojit.
   
    Pokud vaše místní zařízení VPN podporuje pouze statické směrování, můžete nakonfigurovat statické trasy následujícím způsobem:

  ```powershell
  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
  ```
   
    Pokud vaše místní zařízení VPN podporuje protokol BGP a chcete povolit dynamické trasování, potřebujete znát IP adresu partnerských vztahů protokolu BGP a číslo AS, které vaše místní zařízení VPN používá.

  ```powershell
  $localVPNPublicIP = "<Public IP>"
  $localBGPPeeringIP = "<Private IP for the BGP session>"
  $localBGPASN = "<ASN>"
  $localAddressPrefix = $localBGPPeeringIP + "/32"
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
  ```
6. Nakonfigurujte místní zařízení VPN pro připojení k nové bráně Azure VPN. Další informace o konfiguraci zařízení VPN najdete v tématu [Konfigurace zařízení VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Propojte bránu VPN typu site-to-site v Azure s místní bránou.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```
 

8. Pokud se připojujete k existujícímu okruhu ExpressRoute, kroky 8 a 9 přeskočte a přejděte rovnou ke kroku 10. Nakonfigurujte okruhy ExpressRoute. Další informace o konfiguraci okruhu ExpressRoute najdete v tématu o [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md).


9. Nakonfigurujte soukromý partnerský vztah Azure přes okruh ExpressRoute. Další informace o konfiguraci soukromého partnerského vztahu Azure přes okruh ExpressRoute najdete v tématu o [konfiguraci partnerského vztahu](expressroute-howto-routing-arm.md).

10. <a name="gw"></a>Vytvořte bránu ExpressRoute. Další informace o konfiguraci brány ExpressRoute najdete v tématu [Konfigurace brány ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). GatewaySKU musí být *Standard*, *HighPerformance* nebo *UltraPerformance*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
11. Propojte bránu ExpressRoute s okruhem ExpressRoute. Po dokončení tohoto kroku bude připojení mezi místní sítí a Azure prostřednictvím ExpressRoute vytvořeno. Další informace o operaci propojení najdete v tématu [Propojení virtuálních sítí s ExpressRoute](expressroute-howto-linkvnet-arm.md).

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```

## <a name="add"></a>Konfigurace současně existujících připojení pro už existující virtuální síť
Pokud máte virtuální síť, která obsahuje pouze jednu bránu virtuální sítě (např. bránu VPN typu Site-to-Site), a chcete přidat další bránu jiného typu (např. bránu ExpressRoute), zkontrolujte velikost podsítě brány. Pokud je velikost podsítě brány /27 nebo větší, můžete přeskočit následující kroky a podle kroků v předchozí části přidat bránu VPN typu Site-to-Site nebo bránu ExpressRoute. Pokud je podsíť brány /28 nebo /29, musíte nejdřív bránu virtuální sítě odstranit a zvýšit velikost podsítě brány. Postup v této části ukazuje, jak to provést.

1. Budete potřebovat nainstalovat nejnovější verzi rutin Azure PowerShellu. Další informace o instalaci rutin najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Rutiny, které použijete pro tuto konfiguraci, se můžou mírně lišit od těch, co znáte. Ujistěte se, že používáte rutiny určené v těchto pokynech. 
2. Odstraňte existující bránu ExpressRoute nebo VPN typu site-to-site.

  ```powershell 
  Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
  ```
3. Odstraňte podsíť brány.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
  ```
4. Přidejte podsíť brány, která je /27 nebo větší.
   
   > [!NOTE]
   > Pokud vám ve virtuální síti nezbylo dost IP adres pro zvětšení velikosti podsítě brány, budete muset přidat další adresní prostor IP adres.
   > 
   > 

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Uložte konfiguraci virtuální sítě.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
5. V tuto chvíli máte virtuální síť, která nemá žádné brány. Pokud chcete vytvořit nové brány a nastavit připojení, postupujte podle kroků v předchozí části.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Přidání konfigurace point-to-site k bráně VPN
Provedením následujících kroků, abyste k vaší brány VPN v nastavení koexistence Přidat konfiguraci Point-to-Site.

1. Přidejte fond adres klienta VPN.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
  ```
2. Odešlete kořenový certifikát VPN pro bránu VPN do Azure. V tomto příkladu se předpokládá, že kořenový certifikát je uložený v místním počítači, kde se spustí následující rutiny PowerShellu.

  ```powershell
  $p2sCertFullName = "RootErVpnCoexP2S.cer" 
  $p2sCertMatchName = "RootErVpnCoexP2S" 
  $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
  if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

Další informace o VPN typu point-to-site najdete v tématu [Konfigurace připojení typu point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Další postup
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
