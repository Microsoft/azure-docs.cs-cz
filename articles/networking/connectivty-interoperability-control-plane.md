---
title: 'Interoperabilita v Azure: analýza roviny ovládacího prvku'
description: Tento článek poskytuje analýzu roviny ovládacího prvku pro instalaci testu, kterou můžete použít k analýze vzájemné funkční spolupráce mezi ExpressRoute, VPN typu Site-to-site a partnerského vztahu virtuálních sítí v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4dfd869b92e042e71eed1ee692d90fc44a8ac6c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234235"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabilita v Azure: analýza roviny ovládacího prvku

Tento článek popisuje analýzu roviny ovládacího prvku pro [nastavení testu][Setup]. Můžete si také projít [konfiguraci nastavení testu][Configuration] a [analýzou roviny dat][Data-Analysis] v nastavení testu.

Analýza roviny ovládacího prvku v podstatě ověřuje trasy vyměňované mezi sítěmi v rámci topologie. Analýza roviny ovládacího prvku vám může porozumět tomu, jak různé sítě zobrazují topologii.

## <a name="hub-and-spoke-vnet-perspective"></a>Perspektiva sítě VNet centra a paprsků

Následující obrázek znázorňuje síť z perspektivy virtuální sítě rozbočovače (VNet) a sítě rozbočovače (zvýrazněna modře). Obrázek ukazuje také číslo autonomního systému (ASN) různých sítí a tras, které jsou vyměněny mezi různými sítěmi: 

![1][1]

ASN brány Azure ExpressRoute ve virtuální síti se liší od ASN Microsoft Enterprise Edge routers (směrovači msee). Brána ExpressRoute používá soukromé číslo ASN (hodnota **65515**) a směrovači msee používá veřejné číslo ASN (hodnota **12076**) globálně. Když nakonfigurujete partnerský vztah ExpressRoute, protože MSEE je partnerským číslem, použijete **12076** jako partnerské číslo ASN. Na straně Azure MSEE vytvoří partnerský vztah eBGP s bránou ExpressRoute. Dual eBGP partnerský vztah, který MSEE stanoví pro každý partnerský vztah ExpressRoute, je transparentní na úrovni řídicích rovin. Proto když zobrazíte tabulku směrování ExpressRoute, zobrazí se číslo ASN ExpressRoute brány virtuální sítě pro předpony virtuální sítě. 

Následující obrázek ukazuje ukázkovou směrovací tabulku ExpressRoute: 

![5][5]

V rámci Azure je číslo ASN významné jenom z pohledu partnerských vztahů. Ve výchozím nastavení je číslo ASN brány ExpressRoute i brány VPN v Azure VPN Gateway **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Místní umístění 1 a perspektiva vzdálené virtuální sítě prostřednictvím ExpressRoute 1

Místní umístění 1 a Vzdálená síť VNet se připojují k virtuální síti centra prostřednictvím ExpressRoute 1. Sdílejí stejnou perspektivu topologie, jak je znázorněno v následujícím diagramu:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Místní umístění 1 a perspektiva virtuální sítě pomocí sítě VPN typu Site-to-site

Místní umístění 1 a virtuální síť pobočky jsou připojené k bráně VPN virtuální sítě rozbočovače prostřednictvím připojení VPN typu Site-to-site. Sdílejí stejnou perspektivu topologie, jak je znázorněno v následujícím diagramu:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Perspektiva místního umístění 2

Místní umístění 2 je připojené k virtuální síti centra prostřednictvím privátního partnerského vztahu ExpressRoute 2: 

![4][4]

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

Seznamte se s [analýzou roviny dat][Data-Analysis] pro nastavení testu a zobrazeními funkcí monitorování sítě Azure.

Podívejte se na téma [Nejčastější dotazy k ExpressRoute][ExR-FAQ] :
-   Přečtěte si, kolik okruhů ExpressRoute se můžete připojit k bráně ExpressRoute.
-   Podívejte se, kolik bran ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Přečtěte si o dalších omezeních škálování pro ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Perspektiva virtuální sítě hvězdicové topologie"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Poloha 1 a vzdálená perspektiva virtuální sítě pro topologii prostřednictvím ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Poloha 1 a perspektiva virtuální sítě z topologie prostřednictvím sítě VPN typu Site-to-site"
[4]: ./media/backend-interoperability/Loc2View.png "Perspektiva umístění 2 topologie"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Tabulka směrování ExpressRoute 1"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md