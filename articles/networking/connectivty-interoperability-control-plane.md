---
title: 'Vzájemná funkční spolupráce v Azure připojení k back-end funkce: Analýza rovina řízení | Dokumentace Microsoftu'
description: Tento článek poskytuje analýzu rovina řízení nastavení testu, které můžete použít k analýze vzájemná funkční spolupráce mezi ExpressRoute, site-to-site VPN a virtuální síť vytvoření partnerského vztahu v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 77a405e2f020ff764348370fc001388610ad75b6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155523"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Vzájemná funkční spolupráce v Azure připojení k back-end funkce: Analýza rovina řízení

Tento článek popisuje analýzu rovina řízení [nastavení testu][Setup]. Můžete také zkontrolovat [konfiguraci nastavení testu] [ Configuration] a [roviny dat, analýzu] [ Data-Analysis] nastavení testu.

Analýza rovina řízení v podstatě prozkoumá trasy, které se vyměňují mezi sítěmi v rámci topologie. Ovládací prvek roviny analýzy vám pomůžou pochopit, jak různé sítě zobrazit topologii.

## <a name="hub-and-spoke-vnet-perspective"></a>Hvězdicové sítě VNet perspektivy

Následující obrázek znázorňuje sítě z hlediska centrální virtuální síti (VNet) a jednoho paprsku virtuální síť (modře zvýrazněná). Obrázek zobrazuje i číslo autonomního systému (ASN) z různých sítí a tras, které se vyměňují mezi různé sítě: 

[![1]][1]

Číslo ASN brány virtuální sítě Azure ExpressRoute se liší od ASN sady Microsoft Enterprise hraniční směrovače (Msee). Bránu ExpressRoute využívá privátní čísla ASN (hodnota **65515**) a Msee použít veřejné číslo ASN (hodnota **12076**) globálně. Když konfigurujete partnerský vztah ExpressRoute, protože směrovači MSEE partnerským zařízením, můžete použít **12076** jako partnerské číslo ASN. Na straně Azure vytváří směrovači MSEE eBGP partnerský vztah s bránou ExpressRoute. Duální eBGP partnerský vztah, který směrovači MSEE vytvoří pro každý partnerský vztah ExpressRoute je transparentní na úrovni rovina řízení. Proto když zobrazujete tabulku směrování ExpressRoute, se zobrazí bránu virtuální sítě ExpressRoute číslo ASN u předpony vaší virtuální sítě. 

Následující obrázek ukazuje tabulku směrování ExpressRoute vzorku: 

[![5]][5]

V rámci Azure je důležité pouze z hlediska partnerské číslo ASN. Výchozí číslo ASN brány ExpressRoute a VPN gateway ve službě Azure VPN Gateway je **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>V místním umístění 1 i z pohledu uživatele vzdálené virtuální sítě prostřednictvím ExpressRoute 1

1 umístění místní a vzdálené virtuální sítě jsou připojené k virtuální síti přes ExpressRoute 1 centra. Sdílejí stejné pohledu topologie, jak je znázorněno v následujícím diagramu:

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>V místním umístění 1 a větve perspektivy virtuální síti prostřednictvím sítě site-to-site VPN

V místním umístění 1 a větve virtuální sítě jsou připojeny k bráně VPN virtuální sítě centra přes připojení VPN typu site-to-site. Sdílejí stejné pohledu topologie, jak je znázorněno v následujícím diagramu:

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>Perspektivy v místním umístění 2

V místním umístění 2 je připojen k virtuální síti centra prostřednictvím soukromého partnerského vztahu ExpressRoute 2: 

[![4]][4]

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

Další informace o [roviny dat, analýzu] [ Data-Analysis] nastavení testu a zobrazení funkce monitorování sítě Azure.

Zobrazit [ExpressRoute – nejčastější dotazy] [ ExR-FAQ] na:
-   Přečtěte si, kolik okruhy ExpressRoute, můžete se připojit k bránu ExpressRoute.
-   Přečtěte si, kolik brány ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Další informace o dalších limity škálování služby ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "hvězdicové sítě VNet pohledu topologie"
[2]: ./media/backend-interoperability/Loc1ExRView.png "1 umístění a vzdálené virtuální sítě pohledu topologie přes ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "1 umístění a větve VNet pohledu topologie prostřednictvím sítě site-to-site VPN"
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


