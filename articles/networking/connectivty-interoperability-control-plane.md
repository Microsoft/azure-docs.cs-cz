---
title: 'Vzájemná funkční spolupráce ExpressRoute, VPN typu Site-to-site a VNet Peering – ovládací prvek roviny analýzy: připojení k back-endu Azure funkce Interoperability | Dokumentace Microsoftu'
description: Tato stránka poskytuje analýzu rovina řízení nastavení testu vytvořené k analýze interoperability funkce ExpressRoute, VPN typu Site-to-site a VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947188"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>Vzájemná funkční spolupráce ExpressRoute, Site-to-site VPN a partnerského vztahu – analýza rovina řízení

V tomto článku Podívejme se analýza rovina řízení nastavení testu. Pokud chcete zkontrolovat nastavení testu, přečtěte si článek [nastavení testu][Setup]. Projděte si podrobnosti o konfiguraci nastavení testu, naleznete v tématu [konfiguraci nastavení testu][Configuration].

Analýza rovina řízení v podstatě prozkoumá trasy vyměňují mezi sítěmi v rámci topologie. Jak různé nápovědy analýzy rovina řízení zobrazení topologie sítě.

##<a name="hub-and-spoke-vnet-perspective"></a>Centra a virtuální sítě paprsků perspektivy

Následující diagram znázorňuje sítě z virtuální sítě centra a perspektivy paprsků virtuální síť (modře zvýrazněná). Diagram také ukazuje číslo autonomního systému (ASN) jinou síť a trasy vyměňuje s jinou sítí. 

[![1]][1]

Všimněte si, že se liší od ASN sady Microsoft Enterprise hraniční směrovače (Msee) číslo ASN z brány virtuální sítě pro ExpressRoute. Bránu ExpressRoute využívá privátní čísla ASN (65515) a Msee globálně pomocí veřejným číslem ASN (12076). Když konfigurujete ExpressRoute partnerský vztah, protože směrovači MSEE je partnerský uzel, použijete 12076 jako partnerské číslo ASN. Na straně Azure směrovači MSEE naváže partnerský vztah ExpressRoute GW eBGP. Duální eBGP partnerský vztah, který směrovači MSEE vytvoří pro každý partnerský vztah ExpressRoute je transparentní úrovni rovina řízení. Proto při prohlížení tabulku směrování ExpressRoute se zobrazí ASN brány virtuální sítě ExpressRoute u předpony vaší virtuální sítě. Snímek obrazovky ukázkové ExpressRoute směrovací tabulce je uveden níže: 

[![5]][5]

V rámci Azure je číslo ASN pouze významné z hlediska partnerského vztahu. Ve výchozím nastavení je číslo ASN brány ExpressRoute a VPN GW 65515.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>Perspektivy v místním umístění-1 a vzdálené virtuální sítě prostřednictvím ExpressRoute-1

V místním umístění-1 a vzdálené virtuální sítě jsou připojeny k virtuální síti centra prostřednictvím ExpressRoute 1 a proto sdílejí stejné pohledu topologie, jak je znázorněno následujícím obrázku.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>V místním umístění-1 a virtuální sítí VNet větev perspektivy přes síť Site-to-Site VPN

V místním umístění-1 a virtuální sítí VNet větve jsou připojeny k virtuální síť centra brány sítě VPN prostřednictvím připojení VPN typu Site-to-Site a proto sdílejí stejné pohledu topologie, jak je znázorněno následujícím obrázku.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>Perspektivy v místním umístění-2

V místním umístění-2 je připojený k virtuální síti centra prostřednictvím soukromého partnerského vztahu ExpressRoute 2. 

[![4]][4]

## <a name="further-reading"></a>Další čtení

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Při vytvoření celostní pomocí připojení VPN typu Site-to-Site a ExpressRoute

####  <a name="site-to-site-vpn-over-expressroute"></a>VPN typu Site-to-Site přes ExpressRoute

Je možné nakonfigurovat VPN typu Site-to-Site přes partnerský vztah ExpressRoute Microsoftu k soukromě výměně dat mezi vaší místní sítě a sítě Azure Vnet s důvěrnost, zneužitím, pravosti a integrita. Další informace o konfiguraci protokolu IPSec Site-to-Site VPN v režimu tunelového propojení přes partnerský vztah ExpressRoute Microsoftu, najdete v části [VPN typu Site-to-site přes ExpressRoute-partnerský vztah Microsoftu][S2S-Over-ExR]. 

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

Analýza roviny dat z nastavení testu a zobrazení funkce monitorování sítě Azure, najdete v části [rovina dat analýzy][Data-Analysis].

Zjistěte, kolik okruhy ExpressRoute, můžete se připojit k bránu ExpressRoute nebo kolik brány ExpressRoute můžete připojit k okruhu ExpressRoute, přečtěte si další limity škálování služby ExpressRoute, najdete v článku [ExpressRoute – nejčastější dotazy][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "centra a perspektivy virtuální sítě paprsků topologie"
[2]: ./media/backend-interoperability/Loc1ExRView.png "umístění-1 a perspektivy vzdálené virtuální sítě prostřednictvím ExpressRoute 1 topologie"
[3]: ./media/backend-interoperability/Loc1VPNView.png "umístění-1 a perspektivy větev VNet přes S2S VPN topologie"
[4]: ./media/backend-interoperability/Loc2View.png "umístění 2 pohledu topologie"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 směrovací tabulky"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
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




