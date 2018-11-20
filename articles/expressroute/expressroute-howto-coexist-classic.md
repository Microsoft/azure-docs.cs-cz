---
title: 'Konfigurace ExpressRoute a připojení VPN typu site-to-site, která mohou existovat vedle sebe: Classic: Azure | Dokumentace Microsoftu'
description: Tento článek vás provede konfigurací ExpressRoute a připojení VPN typu site-to-site, která mohou v modelu nasazení Classic existovat vedle sebe.
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: ''
tags: azure-service-management
ms.assetid: dcf1a5af-a289-466a-b812-0bfedbd2bda0
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
ms.openlocfilehash: f868c4d63209da641b4faa336e7ef78b36ef6454
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165437"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>Konfigurace společně používaných připojení typu Site-to-Site a ExpressRoute (Classic)
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Classic](expressroute-howto-coexist-classic.md)
> 
> 

Možnost konfigurace VPN typu site-to-site a ExpressRoute má několik výhod. Můžete nakonfigurovat VPN typu Site-to-Site jako cestu zabezpečené převzetí služeb při selhání pro ExpressRoute, nebo použít VPN typu Site-to-Site pro připojení k webům, které nejsou připojené prostřednictvím ExpressRoute. V tomto článku vám nabídneme postupy konfigurace pro oba scénáře. Tento článek se týká modelu nasazení Classic. Tato konfigurace není k dispozici na portálu.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Než budete postupovat podle dál uvedených pokynů, musí být předem nakonfigurované okruhy ExpressRoute. Před provedením následujících kroků zkontrolujte, že jste provedli postupy pro [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md) a [konfiguraci směrování](expressroute-howto-routing-classic.md).
> 
> 

## <a name="limits-and-limitations"></a>Omezení
* **Směrování provozu není podporováno.** Nemůžete provádět směrování (přes Azure) mezi místní sítí připojenou prostřednictvím sítě VPN typu site-to-site a místní sítí připojenou přes ExpressRoute.
* **Typ point-to-site není podporován.** Nemůžete povolit připojení VPN typu point-to-site ke stejné virtuální síti, která je připojená k ExpressRoute. Připojení VPN typu point-to-site a ExpressRoute nemůžou existovat pro stejnou síť VNet souběžně.
* **Na bráně VPN typu site-to-site nelze povolit vynucené tunelové propojení.** Můžete pouze „vynutit“ veškerý provoz směřující na internet zpět do místní sítě prostřednictvím ExpressRoute.
* **Základní brána SKU není podporována.** Pro [bránu ExpressRoute](expressroute-about-virtual-network-gateways.md) a [bránu VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) je nutné použít jinou než základní bránu SKU.
* **Podporována je pouze brána VPN na základě tras.** Je nutné použít službu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) na základě tras.
* **Pro vaši bránu VPN by měla být nakonfigurována statická trasa.** Pokud je vaše místní síť připojená k ExpressRoute a síti VPN typu site-to-site, musíte mít v místní síti konfigurovanou statickou trasu, abyste mohli směrovat připojení VPN typu site-to-site do veřejného internetu.
* **Nejprve je nutné nakonfigurovat bránu ExpressRoute.** Bránu ExpressRoute musíte vytvořit předtím, než přidáte bránu VPN typu site-to-site.

## <a name="configuration-designs"></a>Návrhy konfigurace
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurace VPN typu site-to-site jako cesty převzetí služeb při selhání pro ExpressRoute
Můžete nakonfigurovat připojení VPN typu site-to-site jako záložní pro ExpressRoute. To platí jenom pro virtuální sítě, které jsou propojené s cestou soukromého partnerského vztahu Azure. Neexistuje žádné řešení převzetí služeb při selhání založené na VPN pro služby, které jsou přístupné prostřednictvím veřejného partnerského vztahu Azure nebo partnerského vztahu Microsoftu. Okruh ExpressRoute je vždy primárním propojením. Data budou procházet cestou VPN typu site-to-site jenom v případě, když okruh ExpressRoute selže. 

> [!NOTE]
> I když v případě, že jsou obě trasy stejné, je okruh ExpressRoute upřednostněný před VPN typu Site-to-Site, Azure k výběru trasy směrem k cíli paketu použije nejdelší shodu předpony.
> 
> 

![Současná existence](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Konfigurace VPN typu site-to-site pro připojení webů, které nejsou připojené prostřednictvím ExpressRoute
Svoji síť můžete nakonfigurovat tak, že některé weby jsou připojené přímo k Azure prostřednictvím VPN typu site-to-site a některé weby přes ExpressRoute. 

![Současná existence](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> Virtuální síť nemůžete nakonfigurovat jako směrovač provozu.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Výběr kroků k použití
Existují dvě sady postupů, ze kterých si můžete vybrat, když konfigurujete připojení, která můžou existovat společně. Postup konfigurace, který vyberete, bude záviset na tom, jestli máte existující virtuální síť, ke které se chcete připojit, nebo chcete vytvořit novou virtuální síť.

* Nemám virtuální síť a potřebuji ji vytvořit.
  
    Pokud ještě nemáte virtuální síť, tento postup vás provede procesem vytvoření nové virtuální sítě pomocí modelu nasazení Classic a vytvoření nových připojení ExpressRoute a VPN typu site-to-site. Konfiguraci provedete podle kroků v části [Vytvoření nové virtuální sítě a koexistujících připojení](#new).
* Už mám virtuální síť modelu nasazení Classic.
  
    Už můžete mít virtuální síť s existujícím připojením VPN typu site-to-site nebo připojením ExpressRoute. V části článku [konfigurace současně existujících připojení pro už existující virtuální síť](#add) uvedené části najdete postup odstranění brány a následného vytvoření nových připojení ExpressRoute a VPN typu Site-to-Site. Uvědomte si, že při vytváření nových připojení musí být kroky provedené ve velmi specifickém pořadí. Nepoužívejte pro vytvoření připojení a bran pokyny z jiných článků.
  
    V tomto postupu bude vytvoření připojení, která mohou existovat společně, vyžadovat, abyste odstranili bránu a pak nakonfigurovali nové brány. To znamená, že budete mít během odstraňování a opětného vytváření brány a připojení výpadek připojení mezi místy, ale nebude nutné migrovat žádné virtuální počítače a služby do nové virtuální sítě. Virtuální počítače a služby budou během konfigurace brány stále schopné komunikovat prostřednictvím nástroje pro vyrovnávání zatížení, pokud jsou tak nakonfigurované.

## <a name="new"></a>Vytvoření nové virtuální sítě a současně existujících připojení
Tento postup vás provede procesem vytvoření virtuální sítě a vytvoření připojení ExpressRoute a VPN site-to-site, která budou existovat společně.

1. Budete potřebovat nainstalovat nejnovější verzi rutin Azure PowerShellu. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Všimněte si, že rutiny, které budete používat pro tuto konfiguraci, se můžou mírně lišit od těch, co znáte. Ujistěte se, že používáte rutiny určené v těchto pokynech. 
2. Vytvořte schéma pro virtuální síť. Další informace o schématu konfigurace najdete v tématu [Azure Virtual Network configuration schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Schéma konfigurace Azure Virtual Network).
   
    Při vytváření schématu použijte následující hodnoty:
   
   * Podsíť brány pro virtuální síť musí být /27 nebo kratší předpona (například /26 nebo /25).
   * Typ připojení brány je Vyhrazené.
     
             <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
               <AddressSpace>
                 <AddressPrefix>10.17.159.192/26</AddressPrefix>
               </AddressSpace>
               <Subnets>
                 <Subnet name="Subnet-1">
                   <AddressPrefix>10.17.159.192/27</AddressPrefix>
                 </Subnet>
                 <Subnet name="GatewaySubnet">
                   <AddressPrefix>10.17.159.224/27</AddressPrefix>
                 </Subnet>
               </Subnets>
               <Gateway>
                 <ConnectionsToLocalNetwork>
                   <LocalNetworkSiteRef name="MyLocalNetwork">
                     <Connection type="Dedicated" />
                   </LocalNetworkSiteRef>
                 </ConnectionsToLocalNetwork>
               </Gateway>
             </VirtualNetworkSite>
3. Po vytvoření a konfiguraci souboru schématu XML tento soubor odešlete. Tím vytvoříte virtuální síť.
   
    Použijte následující rutinu k odeslání souboru (po náhradě vlastní hodnotou).
   
        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
4. <a name="gw"></a>Vytvořte bránu ExpressRoute. Je nutné zadat GatewaySKU jako *Standard*, *HighPerformance* nebo *UltraPerformance* a GatewayType jako *DynamicRouting*.
   
    Použijte následující příklad a nahraďte v něm hodnoty vlastními.
   
        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
5. Propojte bránu ExpressRoute s okruhem ExpressRoute. Po dokončení tohoto kroku bude připojení mezi místní sítí a Azure prostřednictvím ExpressRoute vytvořeno.
   
        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
6. <a name="vpngw"></a>Dále vytvořte bránu VPN typu site-to-site. GatewaySKU musí být *Standard*, *HighPerformance* nebo *UltraPerformance* a GatewayType musí být *DynamicRouting*.
   
        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
   
    Pokud chcete načíst nastavení brány virtuální sítě, včetně ID brány a veřejné IP adresy, použijte rutinu `Get-AzureVirtualNetworkGateway`.
   
        Get-AzureVirtualNetworkGateway
   
        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded
7. Vytvořte entitu brány VPN místního webu. Tento příkaz neprovede konfiguraci vaší místní brány VPN. Místo toho umožní zadat nastavení místní brány, jako je například veřejná IP adresa a místní adresní prostor, aby se brána Azure VPN k nim mohla připojit.
   
   > [!IMPORTANT]
   > Místní web připojení VPN typu site-to-site není v souboru netcfg definován. Místo toho musíte k určení parametrů místního webu použít tuto rutinu. Nelze je definovat pomocí portálu nebo souboru netcfg.
   > 
   > 
   
    Použijte následující příklad a nahraďte v něm hodnoty vlastními.
   
        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
   
   > [!NOTE]
   > Pokud vaše místní síť obsahuje víc tras, můžete je předat všechny najednou jako pole.  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    Pokud chcete načíst nastavení brány virtuální sítě, včetně ID brány a veřejné IP adresy, použijte rutinu `Get-AzureVirtualNetworkGateway`. Prohlédněte si následující příklad.

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


1. Nakonfigurujte místní zařízení VPN pro připojení k nové bráně. Při konfiguraci zařízení VPN použijte informace, které jste získali v kroku 6. Další informace o konfiguraci zařízení VPN najdete v tématu [Konfigurace zařízení VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
2. Propojte bránu VPN typu site-to-site v Azure s místní bránou.
   
    V tomto příkladu je ID místní brány connectedEntityId, které můžete najít spuštěním rutiny `Get-AzureLocalNetworkGateway`. VirtualNetworkGatewayId můžete najít pomocí rutiny `Get-AzureVirtualNetworkGateway`. Po dokončení tohoto kroku bude připojení mezi místní sítí a Azure prostřednictvím připojení VPN typu site-to-site vytvořeno.

        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>Konfigurace současně existujících připojení pro už existující virtuální síť
Pokud máte existující virtuální síť, zkontrolujte velikost podsítě brány. Pokud podsíť brány je /28 nebo /29, musíte nejdřív bránu virtuální sítě odstranit a zvýšit velikost podsítě brány. Postup v této části ukazuje, jak to provést.

Pokud podsíť brány je /27 nebo větší a virtuální síť je připojená přes ExpressRoute, můžete přeskočit následující kroky a přejít ke [kroku 6 – Vytvoření brány VPN typu site-to-site](#vpngw) v předchozí části.

> [!NOTE]
> Pokud odstraníte existující bránu, místní místo ztratí během práce na této konfiguraci připojení k virtuální síti.
> 
> 

1. Budete potřebovat nainstalovat nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Všimněte si, že rutiny, které budete používat pro tuto konfiguraci, se můžou mírně lišit od těch, co znáte. Ujistěte se, že používáte rutiny určené v těchto pokynech. 
2. Odstraňte existující bránu ExpressRoute nebo VPN typu site-to-site. Použijte následující rutinu a nahraďte v ní hodnoty vlastními.
   
        Remove-AzureVNetGateway –VnetName MyAzureVNET
3. Exportujte schéma virtuální sítě. Použijte následující rutinu PowerShellu a nahraďte v ní hodnoty vlastními.
   
        Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”
4. Upravte schéma konfiguračního souboru sítě, aby podsíť brány byla /27 nebo kratší předpona (například /26 nebo /25). Prohlédněte si následující příklad. 
   
   > [!NOTE]
   > Pokud vám ve virtuální síti nezbylo dost IP adres pro zvětšení velikosti podsítě brány, budete muset přidat další adresní prostor IP adres. Další informace o schématu konfigurace najdete v tématu [Azure Virtual Network configuration schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Schéma konfigurace Azure Virtual Network).
   > 
   > 
   
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
5. Pokud předchozí brána byla VPN typu site-to-site, musíte změnit taky typ připojení na **Dedicated**.
   
                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
6. V tuto chvíli máte virtuální síť, která nemá žádné brány. Abyste vytvořili nové brány a dokončili připojení, můžete pokračovat [krokem 4 – Vytvoření brány ExpressRoute](#gw), který se nachází v předchozí sadě kroků.

## <a name="next-steps"></a>Další postup
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

