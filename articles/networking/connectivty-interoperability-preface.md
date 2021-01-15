---
title: 'Interoperabilita v Azure: nastavení testu | Microsoft Docs'
description: Tento článek popisuje nastavení testu, které můžete použít k analýze vzájemné funkční spolupráce mezi ExpressRoute, VPN typu Site-to-site a vytvořením partnerského vztahu virtuálních sítí v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 206cc70b5ee636ec3cc54727c3e94a10ad2426d1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234133"
---
# <a name="interoperability-in-azure--test-setup"></a>Interoperabilita v Azure: nastavení testu

Tento článek popisuje nastavení testu, které můžete použít k analýze způsobu, jakým služba Azure Networking Services pracuje na úrovni roviny ovládacího prvku a roviny dat. Pojďme se krátce podívat na síťové součásti Azure:

-   **Azure ExpressRoute**: použití privátního partnerského vztahu v Azure ExpressRoute k přímému propojení privátních IP adres ve vaší místní síti s nasazeními Azure Virtual Network. ExpressRoute vám může pomáhat dosáhnout větší šířky pásma a privátního připojení. Řada ExpressRoutech ekosystémů nabízí ExpressRoute konektivitu s SLA. Další informace o ExpressRoute a informace o tom, jak nakonfigurovat ExpressRoute, najdete v tématu [Úvod do ExpressRoute][ExpressRoute].
-   Síť **VPN typu Site-to-site**: pomocí Azure VPN Gateway jako sítě VPN typu Site-to-site můžete bezpečně připojit místní síť k Azure přes Internet nebo pomocí ExpressRoute. Informace o tom, jak nakonfigurovat síť VPN typu Site-to-site pro připojení k Azure, najdete v tématu [konfigurace VPN Gateway][VPN].
-   **Partnerský vztah** virtuálních sítí: k navázání připojení mezi virtuální sítě v Azure Virtual Network použít partnerský vztah virtuálních sítí (VNET). Další informace o partnerském vztahu virtuálních sítí najdete v [kurzu věnovaném partnerským vztahem virtuální][VNet]sítě.

## <a name="test-setup"></a>Nastavení testu

Následující obrázek znázorňuje nastavení testu:

![1][1]

Centerpiece nastavení testu je virtuální síť centra v Azure region 1. Síť VNet centra je připojená k různým sítím následujícími způsoby:

-   Virtuální síť centra je připojená k virtuální síti rozbočovače pomocí partnerského vztahu virtuálních sítí. Virtuální síť rozbočovače má vzdálený přístup ke oběma branám ve virtuální síti centra.
-   Virtuální síť centra je připojená k virtuální síti pobočky pomocí sítě VPN typu Site-to-site. Připojení používá eBGP k výměně tras.
-   Virtuální síť centra je připojená k místnímu umístění 1 sítě pomocí privátního partnerského vztahu ExpressRoute jako primární cesty. Jako cestu k záloze používá připojení VPN typu Site-to-site. Ve zbývající části tohoto článku se na tento okruh ExpressRoute odkazuje jako na ExpressRoute 1. Ve výchozím nastavení okruhy ExpressRoute poskytují redundantní konektivitu pro vysokou dostupnost. V ExpressRoute 1 je zakázané podrozhraní směrovače CE (Secondary Customer Edge), které je sekundárním směrovačem Microsoft Enterprise Edge (MSEE). Červená čára nad šipkou s dvojitou čárou na předchozím obrázku představuje podrozhraní zakázaného směrovače CE.
-   Virtuální síť centra je připojená k místní síti umístění 2 pomocí jiného privátního partnerského vztahu ExpressRoute. Ve zbývající části tohoto článku se na tento druhý okruh ExpressRoute odkazuje jako na ExpressRoute 2.
-   ExpressRoute 1 taky připojuje virtuální síť centra i místní umístění 1 síť ke vzdálené virtuální síti v oblasti Azure region 2.

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

Přečtěte si o [podrobnostech konfigurace][Configuration] pro topologii testu.

Přečtěte si o [analýze roviny ovládacího prvku][Control-Analysis] pro nastavení testu a zobrazení různých virtuální sítě nebo sítí VLAN v topologii.

Seznamte se s [analýzou roviny dat][Data-Analysis] v nastaveních testu a zobrazeních funkcí monitorování sítě Azure.

Podívejte se na téma [Nejčastější dotazy k ExpressRoute][ExR-FAQ] :
-   Přečtěte si, kolik okruhů ExpressRoute se můžete připojit k bráně ExpressRoute.
-   Podívejte se, kolik bran ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Přečtěte si o dalších omezeních škálování pro ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagram topologie testu"

<!--Link References-->
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha