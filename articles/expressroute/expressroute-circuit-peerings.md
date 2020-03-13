---
title: 'Azure ExpressRoute: okruhy a partnerské vztahy'
description: Tato stránka poskytuje přehled okruhy ExpressRoute a směrování domén/vytvoření partnerského vztahu.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281350"
---
# <a name="expressroute-circuits-and-peering"></a>Okruhy ExpressRoute a partnerský vztah

Okruhy ExpressRoute připojit místní infrastrukturu k Microsoftu prostřednictvím poskytovatele připojení. Tento článek vám pomůže pochopit okruhy ExpressRoute a směrování domén/vytvoření partnerského vztahu. Následující obrázek znázorňuje logickou reprezentaci připojení mezi vaší sítě WAN a Microsoftem.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Veřejný partnerský vztah Azure se už nepoužívá a není k dispozici pro nové okruhy ExpressRoute. Nové okruhy podporují partnerský vztah Microsoftu a soukromý partnerský vztah.  
>

## <a name="circuits"></a>Okruhy ExpressRoute

Okruh ExpressRoute představuje logické propojení mezi vaši infrastrukturou v místním a cloudovým službám Microsoftu prostřednictvím poskytovatele připojení. Můžete si objednat víc okruhů ExpressRoute. Každý okruh může být v jedné nebo několika oblastech a jde připojit k místním prostřednictvím poskytovatelů jiné připojení.

Okruhy ExpressRoute nejsou namapovány na všech fyzických entit. Okruh se jednoznačně identifikují pomocí standardního GUID volá jako klíče služby (s-key). Klíč služby je jediná výměně informací mezi Microsoft, poskytovatel připojení a. Klíč s není tajným kódem z bezpečnostních důvodů. Existuje mapování 1:1 mezi okruhu ExpressRoute a s-key.

Okruhy ExpressRoute může obsahovat dva nezávislé partnerských vztahů: soukromého partnerského vztahu a partnerského vztahu Microsoftu. Zatímco existující okruhy ExpressRoute může obsahovat tři partnerské vztahy: Azure veřejné, privátní Azure a Microsoft. Každý partnerský vztah je pár nezávislé relace protokolu BGP, každý z nich redundantně nakonfigurovaný pro vysokou dostupnost. Není 1: n (1 < = N < = 3) mapování mezi okruhu ExpressRoute a domény směrování. Okruh ExpressRoute může mít libovolný, dvě nebo všechny tři partnerské vztahy, které jsou povolené jeden okruh ExpressRoute.

Každý okruh má pevnou šířku pásma (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s) a je namapována na poskytovatele připojení a umístění partnerského vztahu. Šířku pásma, kterou jste vybrali, je sdílen mezi všechny partnerské vztahy okruhu

### <a name="quotas"></a>Kvóty, omezení a omezení

Výchozí kvóty a omezení platí pro každý okruh ExpressRoute. Aktuální informace o kvótách najdete na stránce [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) .

## <a name="routingdomains"></a>Partnerský vztah ExpressRoute

Okruh ExpressRoute má několik směrování domén/partnerské vztahy s ním spojená: Azure privátní veřejné, Azure a Microsoft. Každý partnerský vztah je nakonfigurované stejně jako na pár směrovače (ve sdílení zatížení nebo aktivní aktivní konfigurace) pro zajištění vysoké dostupnosti. Služby Azure jsou zařazené do kategorií jako *veřejné* a *Azure Private* , které představují schémata přidělování IP adres.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Soukromý partnerský vztah Azure

Výpočetní služby, a to virtuálních počítačích (IaaS) Azure a cloudové služby (PaaS), které jsou nasazeny v rámci virtuální sítě můžete připojit přes privátní partnerský vztah domény. Privátní partnerský vztah domény, se považuje za důvěryhodným rozšířením vaší základní sítě do Microsoft Azure. Můžete nastavit obousměrné připojení mezi vaší základní sítě a virtuální sítě Azure (Vnet). Tento partnerský vztah umožňuje připojení k virtuálním počítačům a cloudovým službám přímo na jejich privátní IP adresy.  

Více než jedné virtuální sítě můžete připojit k privátní partnerský vztah domény. Informace o omezeních a omezeních najdete na [stránce Nejčastější dotazy](expressroute-faqs.md) . Pro aktuální informace o omezeních můžete navštívit stránku [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) .  Podrobné informace o konfiguraci směrování najdete na stránce [Směrování](expressroute-routing.md) .

### <a name="microsoftpeering"></a>Partnerský vztah Microsoftu

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Připojení k Microsoft online služby (Office 365 a Azure PaaS Services) probíhá prostřednictvím partnerského vztahu Microsoftu. Povolujeme obousměrné připojení mezi cloudovými službami prostřednictvím partnerského vztahu směrování domény Microsoft vaší sítě WAN a Microsoft. Musíte se připojit ke cloudovým službám Microsoftu jenom přes veřejné IP adresy, které vlastníte vy nebo váš poskytovatel připojení a musí dodržovat veškerá definovaná pravidla. Další informace najdete na stránce s [požadavky na ExpressRoute](expressroute-prerequisites.md) .

Další informace o podporovaných službách, nákladech a podrobnostech konfigurace najdete na [stránce s nejčastějšími dotazy](expressroute-faqs.md) . Informace o seznamu poskytovatelů připojení, které nabízí podpora partnerského vztahu Microsoftu, najdete na stránce [ExpressRoute umístění](expressroute-locations.md) .

## <a name="peeringcompare"></a>Porovnání partnerských vztahů

Následující tabulka porovnává tři partnerské vztahy:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Jako součást váš okruh ExpressRoute můžete povolit jeden nebo více domén směrování. Můžete zvolit, aby všechny domény směrování umístit na stejnou síť VPN, pokud chcete zkombinovat do jedné domény směrování. Můžete je také umístit na různé domény směrování, podobně jako na obrázku. Doporučená konfigurace je, že soukromého partnerského vztahu je připojený přímo k základní sítě a veřejného partnerského vztahu a partnerského vztahu Microsoftu odkazy jsou připojené k vaší hraniční síti.

Každý partnerský vztah vyžaduje samostatné relace protokolu BGP (jednu dvojici pro každý typ partnerského vztahu). Následující páry relace protokolu BGP propojují s vysokou dostupností. Pokud se chcete připojit prostřednictvím poskytovatelů vrstvy 2 připojení, zodpovídáte za konfiguraci a správu směrování. Další informace najdete v [pracovních postupech](expressroute-workflows.md) pro nastavení ExpressRoute.

## <a name="health"></a>Stav ExpressRoute

Okruhy ExpressRoute je možné monitorovat kvůli dostupnosti, připojení k virtuální sítě a využití šířky pásma pomocí [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (npm).

NPM monitoruje stav soukromý partnerský vztah Azure a partnerský vztah Microsoftu. Další informace najdete na našem [příspěvku](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) .

## <a name="next-steps"></a>Další kroky

* Vyhledejte poskytovatele služeb. Viz [ExpressRoute a poskytovatelé služeb a umístění](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření a správa okruhů ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace směrování (partnerského vztahu) pro okruhy ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
