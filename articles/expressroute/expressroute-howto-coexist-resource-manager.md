---
title: 'Konfigurace ExpressRoute a připojení VPN typu site-to-site, která mohou existovat vedle sebe: Resource Manager: Azure | Dokumentace Microsoftu'
description: Tento článek vás provede konfigurací ExpressRoute a připojení VPN typu site-to-site, která mohou v modelu nasazení Resource Manager existovat vedle sebe.
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: charwen,cherylmc
ms.openlocfilehash: 905b793ab38644ea742a108741b033ddc91ee322
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>Konfigurace společně používaných připojení typu Site-to-Site a ExpressRoute
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Classic](expressroute-howto-coexist-classic.md)
> 
> 

Konfigurace ExpressRoute a současně existujících připojení VPN typu Site-to-Site má několik výhod. Můžete nakonfigurovat síť VPN typu Site-to-Site jako zabezpečenou cestu převzetí služeb při selhání pro ExpressRoute, nebo použít sítě VPN typu Site-to-Site pro připojení k webům, které nejsou připojené prostřednictvím ExpressRoute. V tomto článku nabízíme postupy konfigurace pro oba scénáře. Tento článek se týká modelu nasazení Resource Manager a používá PowerShell. Tato konfigurace není k dispozici na webu Azure Portal.

> [!IMPORTANT]
> Než budete postupovat podle dál uvedených pokynů, musí být předem nakonfigurované okruhy ExpressRoute. Než budete pokračovat, zkontrolujte, že jste provedli postupy pro [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a [konfiguraci směrování](expressroute-howto-routing-arm.md).
> 
> 

## <a name="limits-and-limitations"></a>Omezení
* **Směrování provozu není podporováno.** Nemůžete provádět směrování (přes Azure) mezi místní sítí připojenou prostřednictvím sítě VPN typu site-to-site a místní sítí připojenou přes ExpressRoute.
* **Základní brána SKU není podporována.** Pro [bránu ExpressRoute](expressroute-about-virtual-network-gateways.md) a [bránu VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) je nutné použít jinou než základní bránu SKU.
* **Podporována je pouze brána VPN na základě tras.** Je nutné použít službu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) na základě tras.
* **Pro vaši bránu VPN by měla být nakonfigurována statická trasa.** Pokud je vaše místní síť připojená k ExpressRoute a síti VPN typu site-to-site, musíte mít v místní síti konfigurovanou statickou trasu, abyste mohli směrovat připojení VPN typu site-to-site do veřejného internetu.
* **Nejprve je potřeba nakonfigurovat bránu ExpressRoute a připojit ji k okruhu.** Bránu ExpressRoute musíte vytvořit a připojit k okruhu předtím, než přidáte bránu VPN typu Site-to-Site.

## <a name="configuration-designs"></a>Návrhy konfigurace
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurace VPN typu site-to-site jako cesty převzetí služeb při selhání pro ExpressRoute
Můžete nakonfigurovat připojení VPN typu site-to-site jako záložní pro ExpressRoute. To platí jenom pro virtuální sítě, které jsou propojené s cestou soukromého partnerského vztahu Azure. Neexistuje žádné řešení převzetí služeb při selhání založené na VPN pro služby, které jsou přístupné prostřednictvím veřejného partnerského vztahu Azure nebo partnerského vztahu Microsoftu. Okruh ExpressRoute je vždy primárním propojením. Data prochází cestou VPN typu Site-to-Site jenom v případě, že okruh ExpressRoute selže.

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
  
    Už můžete mít virtuální síť s existujícím připojením VPN typu site-to-site nebo připojením ExpressRoute. V části [Konfigurace současně existujících připojení pro už existující virtuální síť](#add) najdete postup odstranění brány a následného vytvoření nových připojení ExpressRoute a VPN typu Site-to-Site. Při vytváření nových připojení musí být kroky provedené ve specifickém pořadí. Nepoužívejte pro vytvoření připojení a bran pokyny z jiných článků.
  
    V tomto postupu bude vytvoření připojení, která mohou existovat současně, vyžadovat, abyste odstranili bránu a pak nakonfigurovali nové brány. Během odstraňování a opětného vytváření brány a připojení budete mít výpadek připojení mezi místy, ale nebude nutné migrovat žádné virtuální počítače ani služby do nové virtuální sítě. Virtuální počítače a služby budou během konfigurace brány stále schopné komunikovat prostřednictvím nástroje pro vyrovnávání zatížení, pokud jsou tak nakonfigurované.

## <a name="new"></a>Vytvoření nové virtuální sítě a současně existujících připojení
Tento postup vás provede procesem vytvoření virtuální sítě a připojení ExpressRoute a VPN typu Site-to-Site, která budou existovat společně.

1. Nainstalujte nejnovější verzi rutin Azure PowerShellu. Informace o instalaci rutin najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Rutiny, které použijete pro tuto konfiguraci, se můžou mírně lišit od těch, co znáte. Ujistěte se, že používáte rutiny určené v těchto pokynech.
2. Přihlaste se ke svému účtu a nastavte prostředí.

  ```powershell
  Connect-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65010
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
4. <a name="gw"></a>Vytvořte bránu ExpressRoute. Další informace o konfiguraci brány ExpressRoute najdete v tématu [Konfigurace brány ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). GatewaySKU musí být *Standard*, *HighPerformance* nebo *UltraPerformance*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
5. Propojte bránu ExpressRoute s okruhem ExpressRoute. Po dokončení tohoto kroku bude připojení mezi místní sítí a Azure prostřednictvím ExpressRoute vytvořeno. Další informace o operaci propojení najdete v tématu [Propojení virtuálních sítí s ExpressRoute](expressroute-howto-linkvnet-arm.md).

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```
6. <a name="vpngw"></a>Dále vytvořte bránu VPN typu site-to-site. Další informace o konfiguraci brány VPN najdete v tématu [Konfigurace virtuální sítě s připojením typu site-to-site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). GatewaySKU musí být *Standard*, *HighPerformance* nebo *UltraPerformance*. VpnType musí být *RouteBased*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
  ```
   
    Brána Azure VPN podporuje směrovací protokol BGP. Můžete zadat ASN (číslo AS) pro tuto virtuální síť přidáním přepínače -Asn do následujícího příkazu. V případě nezadání parametru se použije výchozí číslo AS 65515.

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -Asn $VNetASN
  ```
   
    IP adresu partnerských vztahů protokolu BGP a číslo AS, které Azure používá pro bránu VPN, najdete v $azureVpn.BgpSettings.BgpPeeringAddress a $azureVpn.BgpSettings.Asn. Další informace najdete v tématu [Konfigurace protokolu BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) pro bránu VPN Azure.
7. Vytvořte entitu brány VPN místního webu. Tento příkaz neprovede konfiguraci vaší místní brány VPN. Místo toho umožní zadat nastavení místní brány, jako je například veřejná IP adresa a místní adresní prostor, aby se brána Azure VPN k nim mohla připojit.
   
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
8. Nakonfigurujte místní zařízení VPN pro připojení k nové bráně Azure VPN. Další informace o konfiguraci zařízení VPN najdete v tématu [Konfigurace zařízení VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
9. Propojte bránu VPN typu site-to-site v Azure s místní bránou.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```

## <a name="add"></a>Konfigurace současně existujících připojení pro už existující virtuální síť
Pokud máte existující virtuální síť, zkontrolujte velikost podsítě brány. Pokud podsíť brány je /28 nebo /29, musíte nejdřív bránu virtuální sítě odstranit a zvýšit velikost podsítě brány. Postup v této části ukazuje, jak to provést.

Pokud podsíť brány je /27 nebo větší a virtuální síť je připojená přes ExpressRoute, můžete přeskočit následující kroky a přejít ke [kroku 6 – Vytvoření brány VPN typu site-to-site](#vpngw) v předchozí části. 

> [!NOTE]
> Pokud odstraníte existující bránu, místní místo ztratí během práce na této konfiguraci připojení k virtuální síti. 
> 
> 

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
5. V tuto chvíli máte virtuální síť, která nemá žádné brány. Abyste vytvořili nové brány a dokončili připojení, můžete pokračovat [krokem 4 – Vytvoření brány ExpressRoute](#gw), který se nachází v předchozí sadě kroků.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Přidání konfigurace point-to-site k bráně VPN
Podle následujících pokynů můžete k bráně VPN v nastavení koexistence přidat konfiguraci point-to-site.

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
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

Další informace o VPN typu point-to-site najdete v tématu [Konfigurace připojení typu point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Další kroky
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
