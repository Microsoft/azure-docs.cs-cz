---
title: 'Azure ExpressRoute: okruhy a partnerské vztahy'
description: Tato stránka poskytuje přehled okruhů ExpressRoute a domén směrování/partnerských vztahů.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: 072eeb0bee0d0441549d2edad448f3b1c85a28a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566496"
---
# <a name="expressroute-circuits-and-peering"></a>Okruhy ExpressRoute a peering

Okruhy ExpressRoute spojují vaši místní infrastrukturu s Microsoftem prostřednictvím poskytovatele připojení. Tento článek vám pomůže pochopit okruhy ExpressRoute a směrování domén/partnerských vztahů. Následující obrázek znázorňuje logickou reprezentaci propojení mezi sítí WAN a Microsoftem.

![Diagram znázorňující, jakým způsobem ExpressRoute okruhy připojují místní infrastrukturu k Microsoftu prostřednictvím poskytovatele připojení.](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Veřejný partnerský vztah Azure se už nepoužívá a není k dispozici pro nové okruhy ExpressRoute. Nové okruhy podporují partnerský vztah Microsoftu a soukromý partnerský vztah.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>Okruhy ExpressRoute

Okruh ExpressRoute představuje logické propojení mezi vaší místní infrastrukturou a cloudovou službou Microsoftu prostřednictvím poskytovatele připojení. Můžete objednat více okruhů ExpressRoute. Každý okruh může být ve stejné nebo jiné oblasti a může být připojen k vašemu prostoru prostřednictvím různých poskytovatelů připojení.

Okruhy ExpressRoute nejsou namapovány na žádné fyzické entity. Okruh je jednoznačně identifikovaný standardním identifikátorem GUID, který se označuje jako klíč služby (s-Key). Klíčem služby jsou jediné informace vyměňované mezi společností Microsoft, poskytovatelem připojení a vámi. S-Key není tajný kód pro účely zabezpečení. Mezi okruhem ExpressRoute a s-Key je mapování 1:1.

Nové okruhy ExpressRoute můžou zahrnovat dvě nezávislé partnerské vztahy: privátní partnerské vztahy a partnerské vztahy Microsoftu. Zatímco stávající okruhy ExpressRoute můžou obsahovat tři partnerské vztahy: Azure Public, Private Azure a Microsoft. Každý partnerský vztah je dvojicí nezávislých relací protokolu BGP, každé z nich je pro účely vysoké dostupnosti nakonfigurované redundantní. Mezi okruhem ExpressRoute a doménami směrování je mapování 1: N (1 <= N <= 3). Okruh ExpressRoute může mít jednu, dvě nebo všechny tři partnerské vztahy povolené na okruh ExpressRoute.

Každý okruh má pevnou šířku pásma (50 MB/s, 100 MB/s, 200 MB/s, 500 Mbps, 1 GB/s, 10 GB/s) a je namapovaný na poskytovatele připojení a umístění partnerského vztahu. Vybraná šířka pásma se sdílí napříč všemi partnerskými vztahy okruhu.

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Kvóty, omezení a omezení

Výchozí kvóty a omezení platí pro každý okruh ExpressRoute. Aktuální informace o kvótách najdete na stránce [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) .

## <a name="expressroute-peering"></a><a name="routingdomains"></a>Partnerský vztah ExpressRoute

Okruh ExpressRoute má k dispozici více domén směrování/partnerských vztahů: Azure Public, Private Azure a Microsoft. Každý partnerský vztah se nakonfiguruje stejným způsobem na páru směrovačů (v konfiguraci aktivní-aktivní nebo nasdílení zátěže) pro zajištění vysoké dostupnosti. Služby Azure jsou zařazené do kategorií jako *veřejné* a *Azure Private* , které představují schémata přidělování IP adres.

![Diagram znázorňující, jak se v okruhu ExpressRoute konfigurují veřejné partnerské vztahy Azure a partnerské vztahy Microsoftu.](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Privátní partnerský vztah Azure

Výpočetní služby Azure, konkrétně virtuální počítače (IaaS) a cloudové služby (PaaS), které jsou nasazené ve virtuální síti, se dají připojit prostřednictvím privátní domény partnerského vztahu. Doména privátního partnerského vztahu se považuje za důvěryhodné rozšíření vaší základní sítě do Microsoft Azure. Můžete nastavit obousměrné připojení mezi základní sítí a virtuálními sítěmi Azure (virtuální sítě). Tento partnerský vztah umožňuje připojit se k virtuálním počítačům a cloudovým službám přímo na jejich privátních IP adresách.  

K doméně privátního partnerského vztahu můžete připojit více než jednu virtuální síť. Informace o omezeních a omezeních najdete na [stránce Nejčastější dotazy](expressroute-faqs.md) . Pro aktuální informace o omezeních můžete navštívit stránku [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) .  Podrobné informace o konfiguraci směrování najdete na stránce [Směrování](expressroute-routing.md) .

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Partnerský vztah Microsoftu

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Připojení k Microsoft online služby (Microsoft 365 a Azure PaaS Services) probíhá prostřednictvím partnerského vztahu Microsoftu. V rámci domény směrování partnerského vztahu Microsoftu povolujeme obousměrné připojení mezi cloudovou službou sítě WAN a Microsoftu. Musíte se připojit ke cloudovým službám Microsoftu jenom přes veřejné IP adresy, které vlastníte vy nebo váš poskytovatel připojení, a musíte dodržovat všechna definovaná pravidla. Další informace najdete na stránce s [požadavky na ExpressRoute](expressroute-prerequisites.md) .

Další informace o podporovaných službách, nákladech a podrobnostech konfigurace najdete na [stránce s nejčastějšími dotazy](expressroute-faqs.md) . Informace o seznamu poskytovatelů připojení, které nabízí podpora partnerského vztahu Microsoftu, najdete na stránce [ExpressRoute umístění](expressroute-locations.md) .

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Porovnání partnerských vztahů

Následující tabulka porovnává tři partnerské vztahy:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

V rámci okruhu ExpressRoute můžete povolit jednu nebo více domén směrování. Pokud je chcete sloučit do jedné domény směrování, můžete zvolit, aby všechny domény směrování byly umístěny ve stejné síti VPN. Můžete je také umístit do různých domén směrování, podobně jako v diagramu. Doporučená konfigurace je, že privátní partnerský vztah je připojen přímo k základní síti a odkazy veřejného a partnerského vztahu Microsoftu jsou připojené k vašemu DMZ.

Každý partnerský vztah vyžaduje samostatné relace protokolu BGP (jednu dvojici pro každý typ partnerského vztahu). Páry relací protokolu BGP poskytují vysoce dostupný odkaz. Pokud se připojujete přes poskytovatele připojení vrstvy 2, zodpovídáte za konfiguraci a správu směrování. Další informace najdete v [pracovních postupech](expressroute-workflows.md) pro nastavení ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>Stav ExpressRoute

Okruhy ExpressRoute je možné monitorovat kvůli dostupnosti, připojení k virtuální sítě a využití šířky pásma pomocí [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (npm).

NPM monitoruje stav privátního partnerského vztahu Azure a partnerského vztahu Microsoftu. Další informace najdete na našem [příspěvku](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) .

## <a name="next-steps"></a>Další kroky

* Vyhledejte poskytovatele služeb. Viz [ExpressRoute a poskytovatelé služeb a umístění](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření a správa okruhů ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace směrování (partnerského vztahu) pro okruhy ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
