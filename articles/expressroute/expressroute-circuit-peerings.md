---
title: 'Azure ExpressRoute: okruhy a partnerský vztah'
description: Tato stránka poskytuje přehled okruhů ExpressRoute a směrovacích domén/partnerského vztahu.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281350"
---
# <a name="expressroute-circuits-and-peering"></a>Okruhy ExpressRoute a peering

Obvody ExpressRoute propojují místní infrastrukturu s Microsoftem prostřednictvím poskytovatele připojení. Tento článek vám pomůže pochopit okruhy ExpressRoute a směrovací domény/partnerský vztah. Následující obrázek znázorňuje logickou reprezentaci připojení mezi sítí WAN a společností Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Veřejný partnerský vztah Azure byl zastaralé a není k dispozici pro nové okruhy ExpressRoute. Nové okruhy podporují partnerský vztah Microsoftu a soukromý partnerský vztah.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>Okruhy ExpressRoute

Okruh ExpressRoute představuje logické připojení mezi místní infrastrukturou a cloudovými službami Microsoftu prostřednictvím poskytovatele připojení. Můžete si objednat více okruhů ExpressRoute. Každý okruh může být ve stejné nebo jiné oblasti a může být připojen k vašim prostorům prostřednictvím různých poskytovatelů připojení.

Obvody ExpressRoute nejsou mapovány na žádné fyzické entity. Obvod je jednoznačně identifikován standardním identifikátorem GUID nazývaným jako klíč služby (s-key). Klíč služby je jedinou informací vyměňovanou mezi společností Microsoft, poskytovatelem připojení a vámi. Klíč s není tajný majek z bezpečnostních důvodů. Mezi okruhem ExpressRoute a klíčem s je mapování 1:1.

Nové okruhy ExpressRoute mohou zahrnovat dva nezávislé partnerské partnerské vztahy: soukromý partnerský vztah a partnerský vztah Microsoftu. Vzhledem k tomu, že existující okruhy ExpressRoute může obsahovat tři partnerské vztahy: Azure Public, Azure Private a Microsoft. Každý partnerský vztah je dvojice nezávislých relací Protokolu BGP, z nichž každá je redundantně nakonfigurována pro vysokou dostupnost. Mezi okruhem ExpressRoute a směrovacími doménami je mapování 1:N (1 <= N <= 3). Okruh ExpressRoute může mít jeden, dva nebo všechny tři partnerský chod povoleny na okruh ExpressRoute.

Každý okruh má pevnou šířku pásma (50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 10 Gb/s) a je mapován na poskytovatele připojení a umístění partnerského vztahu. Vybraná šířka pásma je sdílena ve všech partnerských položkách okruhu.

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Kvóty, limity a omezení

Výchozí kvóty a limity platí pro každý okruh ExpressRoute. Aktuální informace o kvótách najdete na stránce [Omezení předplatného a služeb Azure, kvóty a omezení.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="expressroute-peering"></a><a name="routingdomains"></a>Partnerský vztah ExpressRoute

Okruh ExpressRoute má více směrovacích domén/partnerských společností, které jsou k němu přidruženy: Azure public, Azure private a Microsoft. Každý partnerský vztah je konfigurován identicky na dvojici směrovačů (v konfiguraci aktivní aktivní nebo sdílení zatížení) pro vysokou dostupnost. Služby Azure jsou kategorizovány jako *Azure veřejné* a *Azure soukromé* reprezentovat schémata adresování IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Soukromý partnerský vztah Azure

Výpočetní služby Azure, konkrétně virtuální počítače (IaaS) a cloudové služby (PaaS), které jsou nasazené v rámci virtuální sítě, se můžou připojit přes privátní doménu partnerského vztahu. Privátní partnerská doména je považována za důvěryhodné rozšíření vaší hlavní sítě do Microsoft Azure. Můžete nastavit obousměrné připojení mezi hlavní sítí a virtuálními sítěmi Azure (Virtuální sítě). Tento partnerský vztah umožňuje připojit se k virtuálním počítačům a cloudovým službám přímo na jejich privátních IP adresách.  

K privátní doméně partnerského vztahu můžete připojit více než jednu virtuální síť. Informace o limitech a omezeních [nastránce faq najděte.](expressroute-faqs.md) Můžete navštívit stránku [Omezení předplatného a služeb Azure, Kvóty a Omezení](../azure-resource-manager/management/azure-subscription-service-limits.md) pro aktuální informace o limitech.  Podrobné informace o konfiguraci směrování naleznete na stránce [Směrování.](expressroute-routing.md)

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Partnerský vztah Microsoftu

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Připojení k online službám Microsoftu (služby Office 365 a Azure PaaS) probíhá prostřednictvím partnerského vztahu Microsoftu. Povolujeme obousměrné připojení mezi vaší sítí WAN a cloudovými službami Microsoftu prostřednictvím směrovací domény partnerského vztahu Microsoftu. Ke cloudovým službám Microsoftu se musíte připojit pouze přes veřejné IP adresy, které vlastníte vy nebo váš poskytovatel připojení, a musíte dodržovat všechna definovaná pravidla. Další informace naleznete na stránce [předpokladů ExpressRoute.](expressroute-prerequisites.md)

Další informace o podporovaných službách, nákladech a podrobnostech konfigurace najdete na [stránce nejčastějších](expressroute-faqs.md) dotazů. Informace o seznamu poskytovatelů připojení, kteří nabízejí podporu partnerského vztahu microsoftu, najdete na stránce [Umístění ExpressRoute.](expressroute-locations.md)

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Porovnání partnerského vztahu

Následující tabulka porovnává tři partnerské partnerské partnerské partnerské společnosti:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Můžete povolit jednu nebo více směrovacích domén jako součást okruhu ExpressRoute. Pokud chcete zkombinovat všechny směrovací domény do stejné sítě VPN, můžete je nastavit do jedné směrovací domény. Můžete je také umístit do různých směrovacích domén, podobně jako diagram. Doporučená konfigurace je, že privátní partnerský vztah je připojen přímo k hlavní síti a veřejné a microsoft partnerský ch odpočitatých odkazů jsou připojeny k vaší DMZ.

Každý partnerský vztah vyžaduje samostatné relace protokolu BGP (jeden pár pro každý typ partnerského vztahu). Dvojice relací protokolu BGP poskytují vysoce dostupný odkaz. Pokud se připojujete prostřednictvím zprostředkovatelů připojení vrstvy 2, jste zodpovědní za konfiguraci a správu směrování. Další informace získáte kontrolou [pracovních postupů](expressroute-workflows.md) pro nastavení ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>Stav ExpressRoute

Obvody ExpressRoute mohou být monitorovány z hlediska dostupnosti, připojení k virtuálním sítím a využití šířky pásma pomocí [sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM monitoruje stav privátního partnerského vztahu Azure a partnerského vztahu Microsoftu. Podívejte se na náš [příspěvek](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) pro více informací.

## <a name="next-steps"></a>Další kroky

* Vyhledejte poskytovatele služeb. Viz [Poskytovatelé a umístění služeb ExpressRoute](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření a správa okruhů ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace směrování (partnerského vztahu) pro okruhy ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
