---
title: Podpora vzdálených uživatelů pomocí Azure ExpressRoute
description: Tato stránka popisuje, jak můžete využít Azure ExpressRoute k vzdálené práci z důvodu COVID-19 PANDEMIC.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336656"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Použití Azure ExpressRoute k vytvoření hybridního připojení pro podporu vzdálených uživatelů

Tento článek popisuje, jak můžete využívat ExpressRoute, Azure, síť Microsoftu a ekosystém partnerů Azure pro vzdálenou práci.

## <a name="connecting-to-azure-services-with-expressroute"></a>Připojení ke službám Azure pomocí ExpressRoute

>[!NOTE]
>Tento článek popisuje, jak můžete využít ExpressRoute, Azure, síť Microsoftu a partnerský ekosystém Azure k tomu, abyste mohli vzdáleně pracovat a zmírnit problémy se sítí, na které jste nastali, z důvodu krize v COVID-19.
>

[ExpressRoute](expressroute-introduction.md) je služba Azure, která umožňuje privátní připojení mezi datovými centry Microsoftu a infrastrukturou ve vašich prostorách nebo v zařízení se systémem. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízí zabezpečené připojení, spolehlivost a rychlost s nižší a konzistentní latencí než typická připojení přes Internet.

## <a name="useful-links"></a>Užitečné odkazy

* [Jak zvýšit šířku pásma stávajícího okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute monitorování, metriky a výstrahy](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Optimalizace tras přes ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute pro O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Otázky asymetrického směrování](expressroute-asymmetric-routing.md)
* [Postup otevření žádosti o podporu v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Řešení potíží

* [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* Získání tabulky ARP v [Správce prostředků](expressroute-troubleshooting-arp-resource-manager.md) a [Classic](expressroute-troubleshooting-arp-classic.md)
* [Resetování neúspěšného okruhu](reset-circuit.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Další kroky

* [Další informace o modelech připojení ExpressRoute](expressroute-connectivity-models.md)
* Přečtěte si další informace o připojeních ExpressRoute a doménách směrování. Viz [okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md) .
* Vyhledejte poskytovatele služeb. Viz [partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md) .
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* [Vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Připojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
