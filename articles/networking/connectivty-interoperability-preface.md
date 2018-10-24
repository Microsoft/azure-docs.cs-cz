---
title: 'Vzájemná funkční spolupráce ExpressRoute, VPN typu Site-to-site a VNet Peering – nastavení testu: připojení k back-endu Azure funkce Interoperability | Dokumentace Microsoftu'
description: Tato stránka obsahuje nastavení testu, který slouží k analýze interoperability funkce ExpressRoute, VPN typu Site-to-site a VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947186"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>Vzájemná funkční spolupráce ExpressRoute, VPN typu Site-to-site a VNet-peering – nastavení testu
V tomto článku se můžeme určit nastavení testu, které můžeme použít k analýze, jak různé funkce mezi provoz mezi sebou i na úrovni rovina řízení a rovinou dat. Před diskuze o nastavení testu, stručně podívejme na významu těchto různých síťových funkcí Azure.

ExpressRoute: Pomocí ExpressRoute soukromého partnerského vztahu je můžou přímo připojit z vaší místní síti privátní adresní prostory IP pro vaše nasazení virtuální sítě Azure.  Pomocí ExpressRoute můžete dosáhnout větší šířku pásma a privátní připojení. Existuje mnoho partneři ExpressRoute Eko, kteří nabízejí připojení ExpressRoute se smlouvou SLA. Další informace o ExpressRoute a jak ho nakonfigurovat, najdete v článku [Úvod do ExpressRoute][ExpressRoute]

Síť Site-to-Site VPN: Pro zabezpečené připojení místní sítě k Azure přes Internet nebo prostřednictvím ExpressRoute, možnost Site-to-Site (S2S) VPN je k dispozici. Další informace o tom, jak konfigurovat S2S VPN pro připojení k Azure najdete v tématu [konfigurace brány VPN][VPN]

Partnerský vztah virtuální sítě: Partnerský vztah virtuální sítě je možné navázat připojení mezi virtuálními sítěmi (Vnet). Další informace o metodě VNet peering najdete v tématu [kurz VNet Peering][VNet].

##<a name="test-setup"></a>Nastavení testu

Následující diagram znázorňuje nastavení testu.

[![1]][1]

System center část nastavení testu je virtuální síť centra v 1 oblasti Azure. Virtuální síť centra je připojený k různým sítím následujícím způsobem:

1.  K virtuální síti přes partnerský vztah virtuální sítě paprsků. Vzdálený přístup k obě brány virtuální sítě paprsků má ve virtuální síti centra.
2.  Pro virtuální síť pobočky přes síť Site-to-Site VPN. Připojení eBGP používá k výměně tras.
3.  Umístění-1 místní sítě přes privátní partnerský vztah ExpressRoute jako primární cesty a připojení VPN typu Site-to-Site jako cestu zálohování. Ve zbývající části tohoto dokumentu odkazujte jako ExpressRoute1 tohoto okruhu ExpressRoute. Ve výchozím nastavení okruhy ExpressRoute poskytují redundantní připojení pro zajištění vysoké dostupnosti. Na ExpressRoute1 je zakázané sekundární CE směrovače podrozhraní připojena sekundární MSEE. Je toto označeno pomocí řádku červeně na šipku dvojité čáry v diagramu výše.
4.  Umístění 2 místní síť prostřednictvím jiného privátního partnerského vztahu ExpressRoute. Ve zbývající části tohoto dokumentu odkazujte jako ExpressRoute2 tento druhý okruh ExpressRoute.
5.  Virtuální síť centra i 1 umístění v místním ExpressRoute1 také připojuje k vzdálené virtuální sítě v 2 oblasti Azure.

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

Podrobnosti o konfiguraci topologie testu, naleznete v tématu [podrobnosti o konfiguraci][Configuration].

Ovládací prvek roviny analýzy nastavení testu a pochopit zobrazení jinou virtuální síť/síť VLAN topologie, naleznete v tématu [rovina řízení analýzy][Control-Analysis].

Analýza roviny dat z nastavení testu a zobrazení funkce monitorování sítě Azure, najdete v části [rovina dat analýzy][Data-Analysis].

Zjistěte, kolik okruhy ExpressRoute, můžete se připojit k bránu ExpressRoute nebo kolik brány ExpressRoute můžete připojit k okruhu ExpressRoute, přečtěte si další limity škálování služby ExpressRoute, najdete v článku [ExpressRoute – nejčastější dotazy][ExR-FAQ]



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "testovací topologie"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




