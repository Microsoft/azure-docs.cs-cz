---
title: Použití služby Azure ExpressRoute pro podporu vzdálených uživatelů
description: Tato stránka popisuje, jak můžete využít Azure ExpressRoute k povolení práce na dálku kvůli pandemii COVID-19.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336656"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Použití Služby Azure ExpressRoute k vytvoření hybridního připojení pro podporu vzdálených uživatelů

Tento článek popisuje, jak můžete využít ExpressRoute, Azure, microsoft síť a ekosystém partnerů Azure pracovat na dálku.

## <a name="connecting-to-azure-services-with-expressroute"></a>Připojení ke službám Azure pomocí ExpressRoute

>[!NOTE]
>Tento článek popisuje, jak můžete využít ExpressRoute, Azure, microsoft sítě a ekosystém partnerů Azure pracovat vzdáleně a zmírnit problémy se sítí, kterým čelíte z důvodu krize COVID-19.
>

[ExpressRoute](expressroute-introduction.md) je služba Azure, která umožňuje privátní připojení mezi datovými centry Microsoftu a infrastrukturou, která je ve vašich prostorách nebo v zařízení pro společné umístění. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízejí zabezpečené připojení, spolehlivost a rychlosti s nižší a konzistentní latencí než typická připojení přes Internet.

## <a name="useful-links"></a>Užitečné odkazy

* [Jak zvýšit šířku pásma pro existující okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Monitorování ExpressRoute, metriky a výstrahy](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Optimalizace trasy přes ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute pro O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Asymetrické aspekty směrování](expressroute-asymmetric-routing.md)
* [Jak otevřít žádost o podporu na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Řešení potíží

* [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* Získání tabulky ARP ve [Správci prostředků](expressroute-troubleshooting-arp-resource-manager.md) a [klasické](expressroute-troubleshooting-arp-classic.md)
* [Obnovení neúspěšného okruhu](reset-circuit.md)
* [Poradce při potížích s výkonem sítě](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Další kroky

* [Informace o modelech připojení ExpressRoute](expressroute-connectivity-models.md)
* Přečtěte si další informace o připojeních ExpressRoute a doménách směrování. Viz [Obvody ExpressRoute a směrovací domény](expressroute-circuit-peerings.md)
* Vyhledejte poskytovatele služeb. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md)
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* [Vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Připojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
