---
title: 'Vzájemná funkční spolupráce v části funkce Azure připojení k back-end: nastavení testu | Dokumentace Microsoftu'
description: Tento článek popisuje nastavení testu, které lze použít k analýze vzájemná funkční spolupráce mezi ExpressRoute, site-to-site VPN a virtuální síť vytvoření partnerského vztahu v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: bded5dbf0084d230997be178c1f9a7b8a184ac07
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613205"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Vzájemná funkční spolupráce v části funkce Azure připojení k back-end: nastavení testu

Tento článek popisuje nastavení testu, které lze použít k analýze jak síťové služby Azure spolupracují na úrovni rovina řízení a rovinou dat na úrovni. Podívejme se stručně na Azure síťové součásti:

-   **Azure ExpressRoute**: používání soukromý partnerský vztah v Azure ExpressRoute k přímému připojení privátní IP adresa prostory ve vaší místní sítě pro vaše nasazení Azure Virtual Network. ExpressRoute pomůže zajistit větší šířku pásma a privátní připojení. Řada úsporné partneři ExpressRoute nabízejí připojení ExpressRoute se smlouvami SLA. Další informace o ExpressRoute a další informace o konfiguraci ExpressRoute najdete v tématu [Úvod do ExpressRoute][ExpressRoute].
-   **Site-to-site VPN**: pro zabezpečené připojení místní sítě k Azure přes internet nebo prostřednictvím ExpressRoute můžete použít Azure VPN Gateway jako síť site-to-site VPN. Další informace o konfiguraci sítě VPN site-to-site pro připojení k Azure, najdete v článku [konfigurace brány VPN][VPN].
-   **Partnerský vztah virtuální sítě**: navázat připojení mezi virtuálními sítěmi v Azure Virtual Network pomocí partnerského vztahu virtuálních sítí (VNet). Další informace o metodě VNet peering, najdete v článku [kurz na VNet peering][VNet].

## <a name="test-setup"></a>Nastavení testu

Následující obrázek ukazuje nastavení testu:

[![1]][1]

Ústředním nastavení testu je virtuální síť v Azure oblast 1 centra. Virtuální síť centra je připojený k různým sítím následujícími způsoby:

-   Virtuální síť centra je připojen k virtuální sítě paprsků pomocí VNet peering. Vzdálený přístup k obě brány virtuální sítě paprsků má ve virtuální síti centra.
-   Virtuální síť centra je připojen k větev virtuální síti pomocí sítě site-to-site VPN. Připojení eBGP používá k výměně tras.
-   Virtuální síť centra je připojen k síti 1 umístění v místním pomocí privátního partnerského vztahu ExpressRoute jako primární cestě. Použije připojení VPN typu site-to-site jako cesta k záloze. Ve zbývající části tohoto článku jsme odkazují na tento okruh ExpressRoute jako ExpressRoute 1. Ve výchozím nastavení okruhy ExpressRoute poskytují redundantní připojení pro zajištění vysoké dostupnosti. Na ExpressRoute 1 je zakázané podrozhraní (CE) hraniční směrovač sekundární zákazníka, který čelí sekundární Microsoft Enterprise Edge směrovač (MSEE). Červená čára přes řádku dvojitou šipku na předchozím obrázku představuje zakázané směrovače podrozhraní CE.
-   Virtuální síť centra je připojen k místní síti 2 umístění s použitím jiného privátního partnerského vztahu ExpressRoute. Ve zbývající části tohoto článku jsme odkazují na tento druhý okruh ExpressRoute jako ExpressRoute 2.
-   ExpressRoute 1 se také připojuje virtuální síti centra a v místní síti 1 umístění k vzdálené virtuální sítě v 2 oblasti Azure.

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

Může být vhodné větve virtuální sítě, které jsou v různých oblastech a místními sítěmi mezi sebou komunikovat přes virtuální síti centra. Nativní řešení Azure pro tato konfigurace je připojení VPN typu site-to-site pomocí sítě VPN. Alternativou je použití síťové virtuální zařízení (NVA) pro směrování v centru.

Další informace najdete v tématu [co je VPN Gateway?] [ VPN] a [nasazení vysoce dostupné síťové virtuální zařízení][Deploy-NVA].

## <a name="next-steps"></a>Další postup

Další informace o [podrobnosti o konfiguraci] [ Configuration] pro testovací topologie.

Další informace o [analýzy rovina řízení] [ Control-Analysis] nastavení testu a zobrazení různých virtuálních sítí nebo sítí VLAN v topologii.

Další informace o [roviny dat, analýzu] [ Data-Analysis] nastavení testu a zobrazení funkce monitorování sítě Azure.

Zobrazit [ExpressRoute – nejčastější dotazy] [ ExR-FAQ] na:
-   Přečtěte si, kolik okruhy ExpressRoute, můžete se připojit k bránu ExpressRoute.
-   Přečtěte si, kolik brány ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Další informace o dalších limity škálování služby ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "diagram topologie testu"

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


