---
title: 'Interoperabilita v funkcích back-endového připojení Azure: Podrobnosti o konfiguraci | Dokumenty společnosti Microsoft'
description: Tento článek popisuje podrobnosti konfigurace pro nastavení testu, které můžete použít k analýze interoperability mezi ExpressRoute, VPN mezi lokalitami a partnerským vztahem virtuální sítě v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68335940"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilita v funkcích back-endu Azure: Testování podrobností konfigurace

Tento článek popisuje podrobnosti konfigurace [testovacího nastavení][Setup]. Nastavení testu vám pomůže analyzovat, jak síťové služby Azure spolupracují na úrovni roviny řízení a na úrovni roviny dat.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení virtuální sítě Paprsku pomocí partnerského vztahu virtuální sítě

Následující obrázek znázorňuje podrobnosti partnerského vztahu virtuální sítě Azure o virtuální síti s paprskem. Informace o tom, jak nastavit partnerský vztah mezi dvěma virtuálními sítěmi, najdete v [tématu Správa partnerského vztahu virtuální sítě][VNet-Config]. Pokud chcete, aby virtuální síť pro paprsky používala brány připojené k virtuální síti rozbočovače, vyberte **Použít vzdálené brány**.

[![1]][1]

Na následujícím obrázku jsou zobrazeny podrobnosti partnerského vztahu virtuální sítě virtuální sítě. Pokud chcete, aby virtuální síť rozbočovače povolila virtuální síti pro paprsky používat brány rozbočovače, vyberte **Povolit přenos brány**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Připojení virtuální sítě pobočky pomocí sítě VPN mezi lokalitami

Nastavte připojení VPN mezi sítěmi SITE-TO-site mezi rozbočovačem a virtuálními sítěmi pobočky pomocí bran VPN v azure vpn gateway. Ve výchozím nastavení používají brány VPN a brány Azure ExpressRoute hodnotu číslo soukromého autonomního systému (ASN) **65515**. Hodnotu ASN můžete změnit v bráně VPN. V testovacím nastavení se hodnota ASN brány virtuální sítě VPN změní na **65516** pro podporu směrování eBGP mezi rozbočovačem a virtuálními sítěmi pobočky.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Připojení k místní poloze 1 pomocí expressroute a sítě VPN site-to-site

### <a name="expressroute-1-configuration-details"></a>Podrobnosti konfigurace ExpressRoute 1

Následující obrázek znázorňuje konfiguraci okruhu Azure Region 1 ExpressRoute směrem k místním směrovačům edge zákazníka location 1 (CE):

[![4]][4]

Následující obrázek znázorňuje konfiguraci připojení mezi okruhem ExpressRoute 1 a virtuální sítí rozbočovače:

[![5]][5]

V následujícím seznamu je uvedena primární konfigurace směrovače CE pro privátní připojení partnerského vztahu ExpressRoute. (Směrovače Cisco ASR1000 se používají jako směrovače CE v testovacím nastavení.) Když jsou okruhy VPN a ExpressRoute mezi lokalitami nakonfigurovány paralelně pro připojení místní sítě k Azure, Azure ve výchozím nastavení upřednostňuje okruh ExpressRoute. Aby se zabránilo asymetrickému směrování, měla by místní síť také upřednostňovat připojení ExpressRoute před připojením VPN mezi lokalitami. Následující konfigurace stanoví stanovení priority pomocí atributu **místní předvolby** protokolu BGP:

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

### <a name="site-to-site-vpn-configuration-details"></a>Podrobnosti o konfiguraci sítě VPN mezi lokalitami

V následujícím seznamu je uvedena primární konfigurace směrovače CE pro připojení VPN typu site-to-site:

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

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Připojení k místnímu umístění 2 pomocí expressroute

Druhý okruh ExpressRoute v těsné blízkosti místní lokace 2 spojuje místní lokaci 2 s virtuální sítí rozbočovače. Následující obrázek znázorňuje druhou konfiguraci ExpressRoute:

[![6]][6]

Následující obrázek znázorňuje konfiguraci připojení mezi druhým okruhem ExpressRoute a virtuální sítí rozbočovače:

[![7]][7]

ExpressRoute 1 propojuje virtuální síť rozbočovače i místní lokaci 1 se vzdálenou virtuální sítí v jiné oblasti Azure:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Připojení ExpressRoute a vpn site-to-site v tandemu

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN mezi lokalitami přes ExpressRoute

Síť VPN typu site-to-site můžete nakonfigurovat pomocí partnerského vztahu Microsoftu expressroute k soukromé výměně dat mezi místní sítí a virtuálními sítěmi Azure. Pomocí této konfigurace si můžete vyměňovat data s důvěrností, pravostí a integritou. Výměna dat je také anti-replay. Další informace o konfiguraci sítě IPsec VPN sítě na webu v režimu tunelového propojení pomocí partnerského vztahu Microsoft u ExpressRoute naleznete v [tématu Síť VPN nad programem ExpressRoute Microsoft][S2S-Over-ExR]. 

Primární maješku konfigurace sítě VPN typu site-to-site, která používá partnerský vztah Microsoftu, je propustnost. Propustnost tunelového propojení IPsec je omezena kapacitou brány VPN. Propustnost brány VPN je nižší než propustnost ExpressRoute. V tomto scénáři pomocí tunelu IPsec pro vysoce zabezpečené přenosy a pomocí soukromého partnerského vztahu pro všechny ostatní přenosy pomáhá optimalizovat využití šířky pásma ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site-to-site jako bezpečná cesta převzetí služeb při selhání pro ExpressRoute

ExpressRoute slouží jako redundantní okruh pár pro zajištění vysoké dostupnosti. Geograficky redundantní připojení ExpressRoute můžete nakonfigurovat v různých oblastech Azure. Jak je také ukázáno v našem testovacím nastavení, v rámci oblasti Azure můžete použít vpn mezi lokalitami k vytvoření cesty převzetí služeb při selhání pro připojení ExpressRoute. Když jsou stejné předpony inzerovány přes ExpressRoute i VPN site-to-site, Azure upřednostňuje ExpressRoute. Chcete-li se vyhnout asymetrickému směrování mezi ExpressRoute a sítí VPN mezi lokalitami, měla by místní konfigurace sítě také oplatit pomocí připojení ExpressRoute před použitím připojení VPN mezi lokalitami.

Další informace o konfiguraci koexistujících připojení pro ExpressRoute a síť VPN mezi lokalitami naleznete v [tématu ExpressRoute a koexistence mezi lokalitami][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozšíření back-endového připojení na virtuální sítě s paprsky a pobočky

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení virtuální sítě Paprsku pomocí partnerského vztahu virtuální sítě

Architektura virtuální sítě rozbočovače a paprsku se používá široce. Rozbočovač je virtuální síť v Azure, která funguje jako centrální bod připojení mezi virtuálními sítěmi pro paprsky a s vaší místní sítí. Paprsky jsou virtuální sítě, které se sítovky s rozbočovačem a které můžete použít k izoluje úlohy. Přenosy mezi místním datovým centrem a centrem prostřednictvím připojení ExpressRoute nebo VPN. Další informace o architektuře najdete v [tématu Implementace topologie sítě s rozbočovačem v Azure][Hub-n-Spoke].

V partnerském vztahu virtuální sítě v rámci oblasti můžou virtuální sítě pro paprsky ke komunikaci se vzdálenými sítěmi používat brány virtuální sítě hubu (brány VPN i ExpressRoute).

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Připojení virtuální sítě pobočky pomocí sítě VPN mezi lokalitami

Můžete chtít pobočkové virtuální sítě, které jsou v různých oblastech a místní sítě komunikovat mezi sebou prostřednictvím virtuální sítě rozbočovače. Nativní řešení Azure pro tuto konfiguraci je připojení VPN mezi lokalitami pomocí sítě VPN. Alternativou je použití síťového virtuálního zařízení (NVA) pro směrování v rozbočovači.

Další informace naleznete v tématu [Co je brána VPN?][VPN] a [nasazení vysoce dostupného zařízení NVA][Deploy-NVA].

## <a name="next-steps"></a>Další kroky

Přečtěte si o [analýze roviny řízení][Control-Analysis] nastavení testu a zobrazení různých virtuálních sítí nebo sítí VLAN v topologii.

Přečtěte si o [analýze roviny dat][Data-Analysis] nastavení testu a zobrazení funkcí monitorování sítě Azure.

Podívejte se na [nejčastější dotazy k Expresní trase:][ExR-FAQ]
-   Zjistěte, kolik okruhů ExpressRoute se můžete připojit k bráně ExpressRoute.
-   Zjistěte, kolik bran ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Seznamte se s dalšími omezeními škálování ExpressRoute.


<!--Image References-->
Partnerský vztah virtuální sítě virtuální sítě s [1]: ./media/backend-interoperability/SpokeVNet_peering.png "paprsky"
[Partnerský]: ./media/backend-interoperability/HubVNet-peering.pngvztah virtuální sítě virtuální sítě 2 "rozbočovače"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Konfigurace brány VPN pobočkové virtuální sítě"
[4]: ./media/backend-interoperability/ExR1.png "Konfigurace ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Konfigurace připojení ExpressRoute 1 k bráně ExR virtuální sítě rozbočovače"
[6]: ./media/backend-interoperability/ExR2.png "Konfigurace ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Konfigurace připojení ExpressRoute 2 k bráně ExR virtuální sítě rozbočovače"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Konfigurace připojení ExpressRoute 2 ke vzdálené bráně ExR virtuální sítě"

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


