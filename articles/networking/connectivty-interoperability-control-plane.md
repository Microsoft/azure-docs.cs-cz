---
title: 'Interoperabilita v Azure: Analýza roviny řízení'
description: Tento článek obsahuje analýzu roviny řízení nastavení testu, které můžete použít k analýze interoperability mezi ExpressRoute, vpn lokality k webu a partnerským vztahem virtuální sítě v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 5e41bc86533815c394077bf5276d930fe958cd19
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518277"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabilita v Azure: Analýza roviny řízení

Tento článek popisuje analýzu roviny řízení [nastavení testu][Setup]. Můžete také zkontrolovat [konfiguraci nastavení testu][Configuration] a [analýzu roviny dat][Data-Analysis] nastavení testu.

Analýza roviny řízení v podstatě zkoumá trasy, které jsou vyměňovány mezi sítěmi v rámci topologie. Analýza roviny ovládacího prvku vám může pomoci pochopit, jak různé sítě zobrazují topologii.

## <a name="hub-and-spoke-vnet-perspective"></a>Perspektiva rozbočovače a paprskové virtuální sítě

Následující obrázek znázorňuje síť z pohledu virtuální sítě (Virtuální síť) a virtuální sítě s paprskem (zvýrazněné modře). Obrázek také ukazuje číslo autonomního systému (ASN) různých sítí a tras, které jsou vyměňovány mezi různými sítěmi: 

![1][1]

Asn brány Azure ExpressRoute virtuální sítě se liší od ASN směrovačů Microsoft Enterprise Edge (MSEEs). Brána ExpressRoute používá privátní ASN (hodnota **65515)** a MSEEs používají veřejné ASN (hodnota **12076**) globálně. Při konfiguraci partnerského vztahu ExpressRoute, protože MSEE je peer, použijete **12076** jako partnerský ASN. Na straně Azure MSEE vytvoří partnerský vztah eBGP s bránou ExpressRoute. Partnerský vztah eBGP, který vytvoří MSEE pro každý partnerský vztah ExpressRoute, je transparentní na úrovni roviny ovládacího prvku. Proto při zobrazení tabulky trasy ExpressRoute se zobrazí brána Asn brány ExpressRoute virtuální sítě pro předpony virtuální sítě. 

Následující obrázek znázorňuje ukázkovou tabulku tras ExpressRoute: 

![5][5]

V rámci Azure ASN je významné pouze z hlediska partnerského vztahu. Ve výchozím nastavení je ASN brány ExpressRoute i brány VPN v bráně Azure VPN **Gateway 65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Místní umístění 1 a pohled na vzdálenou virtuální síť přes ExpressRoute 1

Místní umístění 1 i vzdálená virtuální síť jsou připojené k virtuální síti rozbočovače přes ExpressRoute 1. Sdílejí stejnou perspektivu topologie, jak je znázorněno na následujícím diagramu:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Místní umístění 1 a pohled na virtuální síť pobočky prostřednictvím sítě VPN site-to-site

Místní lokace 1 i virtuální síť pobočky jsou připojené k bráně VPN virtuální sítě v rozbočovači prostřednictvím připojení VPN mezi lokalitami. Sdílejí stejnou perspektivu topologie, jak je znázorněno na následujícím diagramu:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Místní perspektiva umístění 2

Místní lokace 2 je připojena k virtuální síti rozbočovače prostřednictvím soukromého partnerského vztahu ExpressRoute 2: 

![4][4]

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

Přečtěte si o [analýze roviny dat][Data-Analysis] nastavení testu a zobrazení funkcí monitorování sítě Azure.

Podívejte se na [nejčastější dotazy k Expresní trase:][ExR-FAQ]
-   Zjistěte, kolik okruhů ExpressRoute se můžete připojit k bráně ExpressRoute.
-   Zjistěte, kolik bran ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Seznamte se s dalšími omezeními škálování ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Perspektiva topologie virtuální sítě pro rozbočovač a paprsky"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Umístění 1 a vzdálená perspektiva topologie virtuální sítě přes ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Umístění 1 a obor virtuální sítě perspektivy topologie přes site-to-site VPN"
[4]: ./media/backend-interoperability/Loc2View.png "Umístění 2 perspektiva topologie"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Tabulka tras ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


