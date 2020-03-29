---
title: 'Interoperabilita v funkcích back-endového připojení Azure: Testovací nastavení | Dokumenty společnosti Microsoft'
description: Tento článek popisuje nastavení testu, které můžete použít k analýze interoperability mezi ExpressRoute, VPN mezi lokalitami a partnerským vztahem virtuální sítě v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 0cbd4b620a03ed26e95679cf7cb1abef277a9471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873791"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Interoperabilita v funkcích back-endu Azure: Testovací nastavení

Tento článek popisuje nastavení testu, které můžete použít k analýze toho, jak síťové služby Azure spolupracují na úrovni roviny řízení a na úrovni roviny dat. Podívejme se stručně na síťové komponenty Azure:

-   **Azure ExpressRoute:** Pomocí privátního partnerského vztahu v Azure ExpressRoute můžete přímo připojit privátní IP prostory v místní síti k nasazení virtuální sítě Azure. ExpressRoute vám může pomoci dosáhnout vyšší šířky pásma a soukromého připojení. Mnoho ekologických partnerů ExpressRoute nabízí konektivitu ExpressRoute se sla. Další informace o ExpressRoute a informace o konfiguraci ExpressRoute najdete [v tématu Úvod do ExpressRoute][ExpressRoute].
-   **VPN site-to-site**: Azure VPN Gateway můžete použít jako vpn site-to-site k bezpečnému připojení místní sítě k Azure přes internet nebo pomocí ExpressRoute. Informace o konfiguraci sítě VPN mezi lokalitami pro připojení k Azure najdete v [tématu Konfigurace brány VPN][VPN].
-   **Partnerský vztah virtuální sítě:** Pomocí partnerského vztahu virtuální sítě (Virtuální síť) navážete připojení mezi virtuálními sítěmi ve virtuální síti Azure. Další informace o partnerské síti virtuální sítě najdete v [kurzu o partnerské síti virtuální sítě][VNet].

## <a name="test-setup"></a>Nastavení testu

Následující obrázek znázorňuje nastavení testu:

![1][1]

Ústředním bodem nastavení testu je virtuální síť rozbočovače v oblasti Azure 1. Virtuální síť rozbočovače se připojí k různým sítím následujícími způsoby:

-   Virtuální síť rozbočovače se připojí k virtuální síti s paprskem pomocí partnerského vztahu virtuální sítě. Virtuální síť s paprskem má vzdálený přístup k oběma bránám ve virtuální síti rozbočovače.
-   Virtuální síť rozbočovače je připojená k virtuální síti pobočky pomocí sítě VPN mezi lokalitami. Připojení používá eBGP k výměně tras.
-   Virtuální síť rozbočovače je připojená k místní síti Lokace 1 pomocí soukromého partnerského vztahu ExpressRoute jako primární cesty. Jako cestu zálohování používá připojení VPN mezi lokalitami. Ve zbývající části tohoto článku odkazujeme na tento okruh ExpressRoute jako ExpressRoute 1. Ve výchozím nastavení poskytují obvody ExpressRoute redundantní připojení pro vysokou dostupnost. Na expressroute 1 je zakázáno sekundární hraniční směrovač (CE), který čelí sekundárnímu směrovači Microsoft Enterprise Edge Router (MSEE). Červená čára nad dvojitou šipkou na předchozím obrázku představuje zakázané podrozhraní směrovače CE.
-   Virtuální síť rozbočovače je připojena k místní síti Lokace 2 pomocí jiného soukromého partnerského vztahu ExpressRoute. Ve zbývající části tohoto článku odkazujeme na tento druhý okruh ExpressRoute jako ExpressRoute 2.
-   ExpressRoute 1 také propojuje virtuální síť rozbočovače i místní síť Lokace 1 se vzdálenou virtuální sítí v oblasti Azure 2.

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

Přečtěte si [podrobnosti o konfiguraci][Configuration] topologie testu.

Přečtěte si o [analýze roviny řízení][Control-Analysis] nastavení testu a zobrazení různých virtuálních sítí nebo sítí VLAN v topologii.

Přečtěte si o [analýze roviny dat][Data-Analysis] nastavení testu a zobrazení funkcí monitorování sítě Azure.

Podívejte se na [nejčastější dotazy k Expresní trase:][ExR-FAQ]
-   Zjistěte, kolik okruhů ExpressRoute se můžete připojit k bráně ExpressRoute.
-   Zjistěte, kolik bran ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Seznamte se s dalšími omezeními škálování ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagram topologie testu"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


