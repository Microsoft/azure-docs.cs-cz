---
title: 'Vzájemná funkční spolupráce v Azure připojení k back-end funkce: Podrobnosti o konfiguraci | Dokumentace Microsoftu'
description: Tento článek popisuje podrobnosti o konfiguraci pro nastavení testu, které lze použít k analýze vzájemná funkční spolupráce mezi ExpressRoute, site-to-site VPN a virtuální síť vytvoření partnerského vztahu v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4aa594769a3665908f0adce498a4a2bf3a4f4f83
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189064"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Vzájemná funkční spolupráce v Azure připojení k back-end funkce: Podrobnosti o konfiguraci testu

Tento článek popisuje podrobnosti o konfiguraci [nastavení testu][Setup]. Nastavení testu pomáhá analyzovat jak síťové služby Azure spolupracují na úrovni rovina řízení a rovinou dat na úrovni.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení virtuální sítě paprsků pomocí VNet peering

Následující obrázek znázorňuje podrobností partnerského vztahu Azure Virtual Network (VNet) virtuální sítě paprsků. Informace o nastavení partnerského vztahu mezi dvěma virtuálními sítěmi najdete v tématu [spravovat VNet peering][VNet-Config]. Pokud chcete, aby paprsky virtuální síť používat brány, které jsou připojené k virtuální síti, vyberte centra **používat vzdálené brány**.

[![1]][1]

Následující obrázek znázorňuje podrobností partnerského vztahu virtuální sítě o virtuální síti centra. Pokud chcete, aby paprsky virtuální síť používat brány virtuální sítě centra, zaškrtněte **používat vzdálené brány**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Větev připojení k virtuální síti pomocí sítě site-to-site VPN

Nastavte připojení VPN site-to-site mezi centrem a větve virtuálních sítí s použitím brány VPN Gateway ve službě Azure VPN Gateway. Ve výchozím nastavení, VPN Gateway a Azure ExpressRoute Gateway použijte hodnotu privátní autonomního systému (ASN) číslo **65515**. Můžete změnit hodnotu ASN ve službě VPN Gateway. V nastavení testu, hodnota čísla ASN větve bránu virtuální sítě VPN se změní na **65516** pro podporu eBGP směrování mezi centrem a větve virtuální sítě.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Místní umístění 1 připojení pomocí ExpressRoute a VPN typu site-to-site

### <a name="expressroute-1-configuration-details"></a>Podrobnosti o konfiguraci ExpressRoute 1

Následující obrázek znázorňuje konfiguraci okruh ExpressRoute 1 oblast Azure směrem k hraniční (CE) směrovače zákazníka v místním umístění 1:

[![4]][4]

Následující obrázek znázorňuje konfiguraci připojení mezi okruh ExpressRoute 1 a virtuální síti centra:

[![5]][5]

Následující seznam obsahuje primární konfigurace směrovače CE pro připojení privátního partnerského vztahu ExpressRoute. (Routerů cisco ASR1000 se používají jako směrovače CE v nastavení testu.) Pokud současně připojit místní síť do Azure jsou nakonfigurované okruhy ExpressRoute i VPN site-to-site, Azure upřednostňuje okruh ExpressRoute ve výchozím nastavení. Aby se zabránilo asymetrické směrování, v místní síti také by měl upřednostňovat připojení ExpressRoute přes připojení VPN typu site-to-site. Tyto konfigurace vytváří stanovení priority pomocí protokolu BGP **předvoleb místní** atribut:

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>Podrobnosti o konfiguraci sítě VPN Site-to-site

Následující seznam obsahuje primární konfigurace směrovače CE pro připojení site-to-site VPN:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Místní umístění 2 připojení pomocí ExpressRoute

Druhý okruh ExpressRoute v těsné blízkosti 2 v místním umístění, v místním umístění 2 připojuje k virtuální síti centra. Následující obrázek znázorňuje druhý konfigurací ExpressRoute:

[![6]][6]

Následující obrázek znázorňuje konfiguraci připojení mezi druhý okruh ExpressRoute a virtuální síti centra:

[![7]][7]

ExpressRoute 1 se připojí virtuální síti centra i v místním umístění 1 na vzdálené virtuální sítě v jiné oblasti Azure:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Připojení VPN typu site-to-site a ExpressRoute při vytvoření celostní

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN typu Site-to-site přes ExpressRoute

Můžete nakonfigurovat VPN typu site-to-site s využitím partnerského vztahu pro soukromě výměnu dat mezi vaší místní sítí a virtuální sítě Azure ExpressRoute Microsoftu. V této konfiguraci může vyměňovat data s důvěrnost, pravosti a integrita. Výměna dat je taky zneužitím. Další informace o konfiguraci sítě VPN site-to-site protokolu IPsec v režimu tunelového propojení s využitím partnerského vztahu Microsoftu ExpressRoute najdete v části [VPN typu Site-to-site přes partnerský vztah ExpressRoute Microsoftu][S2S-Over-ExR]. 

Omezení na primární konfigurace site-to-site VPN, který používá partnerského vztahu Microsoftu je propustnost. Propustnost přes tunel IPsec je omezená kapacita brány VPN. Propustnost brány sítě VPN je nižší než propustnost pro ExpressRoute. V tomto scénáři použití tunelu IPsec pro vysoce zabezpečená provozu a používání soukromý partnerský vztah pro veškerý ostatní provoz optimalizovat využití šířky pásma ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN typu Site-to-site jako cestu zabezpečené převzetí služeb při selhání pro ExpressRoute

ExpressRoute slouží jako dvojici redundantních okruh k zajištění vysoké dostupnosti. Geograficky redundantní připojení ExpressRoute můžete nakonfigurovat v různých oblastech Azure. Také jak je ukázáno v našich nastavení testu, v rámci oblasti Azure, můžete použít VPN typu site-to-site vytvoříte cestu převzetí služeb při selhání pro připojení k ExpressRoute. Pokud jsou stejné předpony inzerované přes VPN typu site-to-site a ExpressRoute, upřednostňuje Azure ExpressRoute. Aby se zabránilo asymetrické směrování mezi ExpressRoute a VPN typu site-to-site, pro místní konfigurace sítě by měl také oplátku pomocí připojení ExpressRoute, použije připojení VPN typu site-to-site.

Další informace o tom, jak konfigurace současně existujících připojení pro ExpressRoute a VPN typu site-to-site najdete v tématu [ExpressRoute a site-to-site koexistence][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozšíření back-end připojení k virtuální sítě paprsků a místech firemních poboček

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení virtuální sítě paprsků pomocí VNet peering

Hvězdicová Architektura virtuální sítě se běžně používá. Centrum je virtuální sítě v Azure, která funguje jako ústřední bod připojení mezi vaší virtuální sítě paprsků a v místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s rozbočovači, a který můžete použít k izolaci úloh. Data jsou přenášena mezi místním datovým centrem a centra prostřednictvím připojení ExpressRoute nebo VPN. Další informace o architektuře, najdete v části [implementace síťové topologie centra s rameny v Azure][Hub-n-Spoke].

V partnerského vztahu v rámci oblasti virtuální sítě můžete použít s virtuálními sítěmi paprsků brány virtuální sítě centra (brány sítě VPN a ExpressRoute) ke komunikaci se vzdálenými sítěmi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Větev připojení k virtuální síti pomocí sítě site-to-site VPN

Může být vhodné větve virtuální sítě, které jsou v různých oblastech a místními sítěmi mezi sebou komunikovat přes virtuální síti centra. Nativní řešení Azure pro tuto konfiguraci je připojení VPN typu site-to-site pomocí sítě VPN. Alternativou je použití síťové virtuální zařízení (NVA) pro směrování v centru.

Další informace najdete v tématu [co je VPN Gateway?] [ VPN] a [nasazení vysoce dostupné síťové virtuální zařízení][Deploy-NVA].

## <a name="next-steps"></a>Další postup

Další informace o [analýzy rovina řízení] [ Control-Analysis] nastavení testu a zobrazení různých virtuálních sítí nebo sítí VLAN v topologii.

Další informace o [roviny dat, analýzu] [ Data-Analysis] nastavení testu a zobrazení funkce monitorování sítě Azure.

Zobrazit [ExpressRoute – nejčastější dotazy] [ ExR-FAQ] na:
-   Přečtěte si, kolik okruhy ExpressRoute, můžete se připojit k bránu ExpressRoute.
-   Přečtěte si, kolik brány ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Další informace o dalších limity škálování služby ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "partnerský vztah virtuální sítě paprsků virtuální sítě"
[2]: ./media/backend-interoperability/HubVNet-peering.png "virtuální síť centra VNet peering"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "konfigurace brány VPN větve virtuální sítě"
[4]: ./media/backend-interoperability/ExR1.png "konfigurace ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "konfigurace připojení ExpressRoute 1 k rozbočovači brány virtuální sítě ExR"
[6]: ./media/backend-interoperability/ExR2.png "konfigurace ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "konfigurace připojení ExpressRoute 2 k rozbočovači brány virtuální sítě ExR"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "konfigurace připojení ExpressRoute 2 služby Brána vzdálené ExR virtuální sítě"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


