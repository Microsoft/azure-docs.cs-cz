---
title: 'Interoperabilita funkcí back-end připojení Azure: podrobnosti konfigurace | Microsoft Docs'
description: Tento článek popisuje konfigurační údaje pro instalaci testu, které můžete použít k analýze vzájemné funkční spolupráce mezi ExpressRoute, VPN typu Site-to-site a partnerského vztahu virtuálních sítí v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 50fd5641c61d08939eca1f2cbafb1d077254e37d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233266"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilita funkcí back-end připojení Azure: podrobnosti konfigurace testu

Tento článek popisuje konfigurační podrobnosti [nastavení testu][Setup]. Nastavení testu vám pomůže analyzovat, jak síťové služby Azure spolupracují na úrovni roviny ovládacího prvku a roviny dat.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení k virtuální síti na straně koncové sítě pomocí partnerského vztahu virtuální sítě

Následující obrázek ukazuje podrobnosti partnerského vztahu Azure Virtual Network pro virtuální síť rozbočovače (VNet). Další informace o tom, jak nastavit partnerský vztah mezi dvěma virtuální sítě, najdete v tématu [Správa partnerského vztahu virtuální][VNet-Config]sítě. Pokud chcete, aby virtuální síť rozbočovače používala brány, které jsou připojené k virtuální síti centra, vyberte **použít vzdálené brány**.

[![první]][1]

Následující obrázek ukazuje podrobnosti partnerského vztahu virtuální sítě pro virtuální síť centra. Pokud chcete, aby virtuální síť rozbočovače povolovala použití bran rozbočovače, vyberte možnost **Povolit přenos brány**.

[![odst]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Připojení k virtuální síti větví pomocí sítě VPN typu Site-to-site

Nastavte připojení VPN typu Site-to-site mezi centrem a větví virtuální sítě pomocí bran VPN Gateway v Azure VPN Gateway. Ve výchozím nastavení používají brány sítě VPN a brány Azure ExpressRoute privátní hodnotu autonomního systému číslo (ASN) **65515**. Hodnotu ASN můžete změnit v VPN Gateway. V nastavení testu se hodnota ASN brány VPN virtuální sítě pro větev změnila na **65516** , aby podporovala eBGP směrování mezi virtuální sítě centra a větví.


[![1]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Připojení k místnímu umístění 1 pomocí ExpressRoute a VPN typu Site-to-site

### <a name="expressroute-1-configuration-details"></a>Podrobnosti konfigurace ExpressRoute 1

Následující obrázek ukazuje konfiguraci okruhu ExpressRoute Azure region 1 k místním směrovačům umístění 1 pro zákazníky (CE):

[![4]][4]

Následující obrázek znázorňuje konfiguraci připojení mezi okruhem ExpressRoute 1 a virtuální sítí centra:

[![čl]][5]

Následující seznam ukazuje konfiguraci primárního směrovače CE pro připojení privátního partnerského vztahu ExpressRoute. (V nastavení testu se používají směrovače Cisco ASR1000 jako směrovače CE.) Pokud jsou okruhy VPN typu Site-to-site a ExpressRoute nastaveny paralelně pro připojení místní sítě k Azure, Azure ve výchozím nastavení upřednostní okruh ExpressRoute. Aby se zabránilo asymetrickému směrování, místní síť by měla také upřednostnit ExpressRoute připojení prostřednictvím připojení VPN typu Site-to-site. Následující konfigurace vytvoří stanovení priority pomocí atributu **místní předvolby** protokolu BGP:

```config
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
```

### <a name="site-to-site-vpn-configuration-details"></a>Podrobnosti konfigurace sítě Site-to-Site VPN

Následující seznam uvádí konfiguraci primárního směrovače CE pro připojení VPN typu Site-to-site:

```config
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
```

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Připojení k místnímu umístění 2 pomocí ExpressRoute

Druhý okruh ExpressRoute, v bližší blízkosti k místnímu umístění 2, připojuje místní umístění 2 k virtuální síti rozbočovače. Následující obrázek ukazuje druhou konfiguraci ExpressRoute:

[![6]][6]

Následující obrázek znázorňuje konfiguraci připojení mezi druhým okruhem ExpressRoute a virtuální sítí centra:

[![čl]][7]

ExpressRoute 1 připojuje virtuální síť centra i místní umístění 1 ke vzdálené virtuální síti v jiné oblasti Azure:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute a připojení VPN typu Site-to-site společně

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN typu Site-to-site přes ExpressRoute

SÍŤ VPN typu Site-to-site můžete nakonfigurovat pomocí partnerského vztahu ExpressRoute Microsoftu pro soukromou výměnu dat mezi vaší místní sítí a Azure virtuální sítě. V této konfiguraci můžete vyměňovat data s důvěrnými, pravostmi a integritou. Výměna dat je také anti-Play. Další informace o tom, jak nakonfigurovat síť VPN typu Site-to-Site VPN v tunelovém režimu pomocí partnerského vztahu Microsoftu ExpressRoute, najdete v tématu [VPN typu Site-to-site over ExpressRoute Microsoft peering][S2S-Over-ExR]. 

Primární omezení konfigurace sítě Site-to-Site VPN, která používá partnerský vztah Microsoft, je propustnost. Propustnost prostřednictvím tunelu IPsec je omezená na kapacitu brány VPN. Propustnost brány VPN Gateway je nižší než ExpressRoute propustnost. V tomto scénáři pomáhá použití tunelového propojení IPsec pro vysoce zabezpečený provoz a používání privátních partnerských vztahů pro všechny ostatní přenosy optimalizovat využití šířky pásma ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Síť VPN typu Site-to-site jako zabezpečenou cestu převzetí služeb při selhání pro ExpressRoute

ExpressRoute slouží jako redundantní dvojice okruhů, aby se zajistila vysoká dostupnost. Geograficky redundantní připojení ExpressRoute můžete nakonfigurovat v různých oblastech Azure. Jak je znázorněno v našem nastavení testu v rámci oblasti Azure, můžete použít síť VPN typu Site-to-site k vytvoření cesty převzetí služeb při selhání pro připojení ExpressRoute. Pokud jsou stejné předpony inzerovány v rámci obou ExpressRoute a VPN typu Site-to-site, Azure upřednostní ExpressRoute. Aby se zabránilo asymetrickému směrování mezi ExpressRoute a VPN typu Site-to-site, místní konfigurace sítě by se měla docházet také pomocí připojení ExpressRoute, než použije připojení Site-to-Site VPN.

Další informace o tom, jak nakonfigurovat současně existující připojení pro ExpressRoute a síť Site-to-Site VPN, najdete v tématu [ExpressRoute a koexistence typu Site][ExR-S2S-CoEx]-to-site.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozšiřování připojení back-endu k paprskovým virtuální sítě a umístěním větví

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení k virtuální síti na straně koncové sítě pomocí partnerského vztahu virtuální sítě

Architektura sítě VNet centra a paprsků se běžně používá. Centrum je virtuální síť v Azure, která funguje jako centrální bod připojení mezi virtuální sítě paprsky a vaší místní sítí. Paprsky jsou virtuální sítě v partnerském vztahu s rozbočovačem a můžete je použít k izolaci úloh. Přenos toků mezi místním datacentrem a centrem prostřednictvím připojení ExpressRoute nebo VPN. Další informace o architektuře najdete v tématu [implementace síťové topologie centra s paprsky v Azure][Hub-n-Spoke].

V rámci partnerského vztahu virtuálních sítí v rámci oblasti může paprskový virtuální sítě používat brány virtuální sítě rozbočovače (brány VPN a ExpressRoute) ke komunikaci se vzdálenými sítěmi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Připojení k virtuální síti větví pomocí sítě VPN typu Site-to-site

Můžete chtít, aby větev virtuální sítě, která je v různých oblastech, a místní sítě vzájemně komunikovaly přes virtuální síť rozbočovače. Nativní řešení Azure pro tuto konfiguraci je připojení VPN typu Site-to-site pomocí sítě VPN. Alternativou je použití síťového virtuálního zařízení (síťové virtuální zařízení) pro směrování v centru.

Další informace najdete v tématu [co je VPN Gateway?][VPN] a [nasazení vysoce dostupného síťové virtuální zařízeníu][Deploy-NVA].

## <a name="next-steps"></a>Další kroky

Přečtěte si o [analýze roviny ovládacího prvku][Control-Analysis] pro nastavení testu a zobrazení různých virtuální sítě nebo sítí VLAN v topologii.

Seznamte se s [analýzou roviny dat][Data-Analysis] pro nastavení testu a zobrazeními funkcí monitorování sítě Azure.

Podívejte se na téma [Nejčastější dotazy k ExpressRoute][ExR-FAQ] :
-   Přečtěte si, kolik okruhů ExpressRoute se můžete připojit k bráně ExpressRoute.
-   Podívejte se, kolik bran ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Přečtěte si o dalších omezeních škálování pro ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "partnerský vztah" virtuálních sítí virtuální sítě s paprsky
[2]: ./media/backend-interoperability/HubVNet-peering.pngVNet "peering pro virtuální síť ve středu"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN Gateway konfigurace virtuální sítě pobočky"
[4]: ./media/backend-interoperability/ExR1.png "Konfigurace ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Konfigurace připojení ExpressRoute 1 k ExR brány virtuální sítě rozbočovače"
[6]: ./media/backend-interoperability/ExR2.png "Konfigurace ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Konfigurace připojení ExpressRoute 2 k bráně virtuální sítě rozbočovače ExR"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Konfigurace připojení ExpressRoute 2 ke vzdálené ExR bráně VNet"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md