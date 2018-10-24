---
title: 'Vzájemná funkční spolupráce ExpressRoute, VPN typu Site-to-site a VNet Peering – podrobnosti o konfiguraci: připojení k back-endu Azure funkce Interoperability | Dokumentace Microsoftu'
description: Tato stránka obsahuje podrobnosti o konfiguraci nastavení testu, který slouží k analýze interoperability funkce ExpressRoute, VPN typu Site-to-site a VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947182"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>Vzájemná funkční spolupráce ExpressRoute, Site-to-site VPN a partnerského vztahu – podrobnosti o konfiguraci testu

V tomto článku Podívejme se podrobnosti o konfiguraci nastavení testu. Pokud chcete zkontrolovat nastavení testu, naleznete v tématu [nastavení testu][Setup]. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>Připojení virtuálních sítí pomocí VNet peering paprsků

Následující Azure portal snímek obrazovky ukazuje podrobností partnerského vztahu virtuálních sítí z virtuální sítě paprsků. Podrobné pokyny ke konfiguraci partnerského vztahu mezi dvěma virtuálními sítěmi najdete v tématu [spravovat VNet Peering][VNet-Config]. Pokud chcete virtuální sítě paprsků Gateway připojené k virtuální síti centra, je potřeba zkontrolovat *používat vzdálené brány*.

[![1]][1]

Následující Azure portal snímek obrazovky ukazuje podrobností partnerského vztahu virtuální sítě o virtuální síti centra. Pokud chcete virtuální síť centra umožníte používat jeho brány sítě paprsků, je potřeba zkontrolovat *používat vzdálené brány*.

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Nepodmíněný skok připojení k virtuální síti pomocí sítě VPN typu Site-to-Site

Připojení Site-to-Site VPN mezi centrem a virtuálními sítěmi větve je nakonfigurovaný pomocí bran VPN Gateway. Ve výchozím nastavení VPN a brány ExpressRoute nakonfigurovanou hodnotu privátní čísla ASN 65515. Brána VPN vám umožní změnit hodnoty ASN. V nastavení testu jak je znázorněno v následující Azure portal snímek obrazovky, je hodnota číslo ASN větve bránu virtuální sítě VPN změní na 65516 umožňující eBGP směrování mezi centrem a virtuálními sítěmi větve.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>1 umístění místního připojení pomocí ExpressRoute a VPN typu Site-to-Site

###<a name="expressroute1-configuration-details"></a>Podrobnosti o konfiguraci ExpressRoute1

Následující snímek obrazovky portálu ukazuje konfigurace okruh ExpressRoute 1 oblast Azure pro směrovače CE v místním umístění-1.

[![4]][4]

Následující snímek obrazovky portálu ukazuje konfigurace připojení mezi ExpressRoute1 okruh a virtuální síť centra.

[![5]][5]

Seznam primární CE směrovač (Cisco ASR1000 směrovače používají jako směrovače CE v rámci zkušební instalace) je následující konfigurace související s konfigurací pro připojení privátního partnerského vztahu ExpressRoute. Když jsou nakonfigurované Site-to-Site VPN i okruh ExpressRoute paralelně pro připojení místní sítě k Azure; Azure dává přednost okruh ExpressRoute ve výchozím nastavení. Aby se zabránilo asymetrické směrování, by měl v místní síti také preferují ExpressRoute přes síť VPN typu Site-to-Site pro trasy přijímané jak přes ExpressRoute a VPN typu Site-to-Site. Toho můžete dosáhnout v následující konfiguraci pomocí atributu local preference protokolu BGP. 

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

###<a name="site-to-site-vpn-configuration-details"></a>Podrobnosti o konfiguraci sítě VPN Site-to-Site

Následuje seznam primární konfigurace směrovače CE související s konektivitou sítě Site-to-Site VPN:

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

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>Umístění 2 místního připojení pomocí ExpressRoute

Druhý okruh ExpressRoute v těsné blízkosti umístění 2 on-premises 2 umístění v místním připojuje k virtuální síti centra. Následující snímek obrazovky portálu se zobrazí druhá konfigurace ExpressRoute.

[![6]][6]

Následující snímek obrazovky portálu ukazuje konfigurace připojení mezi druhý okruh ExpressRoute a virtuální síť centra.

[![7]][7]

ExpressRoute1 připojuje virtuální síť centra i 1 umístění v místním k vzdálené virtuální sítě v jiné oblasti Azure.

[![8]][8]

## <a name="further-reading"></a>Další čtení

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Při vytvoření celostní pomocí připojení VPN typu Site-to-Site a ExpressRoute

#### <a name="site-to-site-vpn-over-expressroute"></a>VPN typu Site-to-Site přes ExpressRoute

Je možné nakonfigurovat VPN typu Site-to-Site přes partnerský vztah ExpressRoute Microsoftu k soukromě výměně dat mezi vaší místní sítě a sítě Azure Vnet s důvěrnost, zneužitím, pravosti a integrita. Další informace týkající se konfigurace IPSec Site-to-Site VPN v tunelovém režimu přes partnerský vztah ExpressRoute Microsoftu najdete v tématu [VPN typu Site-to-site přes ExpressRoute-partnerský vztah Microsoftu][S2S-Over-ExR]. 

Konfigurace sítě VPN typu S2S prostřednictvím partnerského vztahu Microsoftu hlavním omezením je propustnost. Propustnost přes tunel IPSec je omezená kapacita brány VPN. Propustnost brány sítě VPN je že menší ve srovnání s ExpressRoute propustnosti. V takových scénářích použití tunelu IPSec pro vysoké zabezpečení provozu a soukromého partnerského vztahu pro všechny ostatní provoz by vám pomůžou optimalizovat využití šířky pásma ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN typu Site-to-Site jako cestu zabezpečené převzetí služeb při selhání pro ExpressRoute
ExpressRoute se nabízí jako dvojici redundantních okruh k zajištění vysoké dostupnosti. Geograficky redundantní připojení ExpressRoute můžete nakonfigurovat v různých oblastech Azure. Také jako hotové v našich nastavení testu v rámci dané oblasti Azure, pokud chcete cestu převzetí služeb při selhání pro připojení k ExpressRoute, můžete provést pomocí Site-to-Site VPN. Pokud jsou stejné předpony inzerované prostřednictvím ExpressRoute a S2S VPN, preferuje Azure ExpressRoute přes síť VPN typu S2S. Aby se zabránilo asymetrické směrování mezi ExpressRoute a S2S VPN pro místní konfigurace sítě by měl také oplátku preferují ExpressRoute přes připojení S2S VPN.

Další informace o tom, jak konfigurace současně existujících připojení ExpressRoute a VPN typu Site-to-Site najdete v tématu [koexistence Site-to-Site a ExpressRoute][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozšíření připojení k back-end s virtuálními sítěmi paprsků a místech firemních poboček

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Připojení virtuálních sítí pomocí VNet peering paprsků

Architektura střed a paprsek virtuální sítě se běžně používá. Centrum je virtuální síť (VNet) v Azure, která funguje jako ústřední bod připojení mezi vaší virtuální sítě paprsků a v místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centra a slouží k izolaci úloh. Data jsou přenášena mezi místním datovým centrem a centra prostřednictvím připojení ExpressRoute nebo VPN. Další informace o architektuře, najdete v části [střed a paprsek architektury][Hub-n-Spoke]

Vytvoření partnerského vztahu v rámci oblasti virtuální sítě umožňuje virtuální sítě do brány virtuální sítě centra (brány sítě VPN a ExpressRoute) použít ke komunikaci se vzdálenými sítěmi paprsků.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Nepodmíněný skok připojení k virtuální síti pomocí sítě VPN typu Site-to-Site

Pokud chcete, aby větev virtuálními sítěmi (v různých oblastech) a místními sítěmi vzájemně komunikovat přes virtuální síť centra, nativní řešení Azure je připojení k síti VPN site-to-site pomocí sítě VPN. Alternativní možností je použít síťové virtuální zařízení pro směrování v centru.

Konfigurace bran VPN, naleznete v tématu [konfigurace brány VPN][VPN]. Nasazení vysoce dostupných síťových virtuálních zařízení, najdete v části [nasazení vysoce dostupné síťové virtuální zařízení][Deploy-NVA].

## <a name="next-steps"></a>Další postup

Ovládací prvek roviny analýzy nastavení testu a pochopit zobrazení jinou virtuální síť/síť VLAN topologie, naleznete v tématu [rovina řízení analýzy][Control-Analysis].

Analýza roviny dat z nastavení testu a zobrazení funkce monitorování sítě Azure, najdete v části [rovina dat analýzy][Data-Analysis].

Zjistěte, kolik okruhy ExpressRoute, můžete se připojit k bránu ExpressRoute nebo kolik brány ExpressRoute můžete připojit k okruhu ExpressRoute, přečtěte si další limity škálování služby ExpressRoute, najdete v článku [ExpressRoute – nejčastější dotazy][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "partnerský vztah virtuální sítě paprsků virtuální sítě"
[2]: ./media/backend-interoperability/HubVNet-peering.png "virtuální síť centra VNet peering"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "konfigurace sítě VPN brány virtuální sítě větve"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute1 konfigurace"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "konfiguraci připojení ExpressRoute1 k GW ExR virtuální síť centra"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute2 konfigurace"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "konfiguraci připojení ExpressRoute2 k GW ExR virtuální síť centra"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "konfiguraci připojení ExpressRoute2 na vzdálené ExR brány virtuální sítě"

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




