---
title: Okruhy Azure ExpressRoute a domény směrování | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled o okruhy ExpressRoute a domény směrování.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: cherylmc
ms.openlocfilehash: c1fbc99702e077e600f01959224f3321e7543e59
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044395"
---
# <a name="expressroute-circuits-and-routing-domains"></a>Okruhy ExpressRoute a domény směrování
 Můžete uspořádat *okruh ExpressRoute* připojení vaší místní infrastruktury společnosti Microsoft prostřednictvím poskytovatele připojení. Následující obrázek znázorňuje logickou reprezentaci připojení mezi vaší sítě WAN a Microsoftem.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>Okruhy ExpressRoute
*Okruh ExpressRoute* představuje logické propojení mezi vaši infrastrukturou v místním a cloudovým službám Microsoftu prostřednictvím poskytovatele připojení. Můžete si objednat víc okruhů ExpressRoute. Každý okruh může být v jedné nebo několika oblastech a jde připojit k místním prostřednictvím poskytovatelů jiné připojení. 

Okruhy ExpressRoute nejsou namapovány na všech fyzických entit. Okruh se jednoznačně identifikují pomocí standardního GUID volá jako klíče služby (s-key). Klíč služby je jediná výměně informací mezi Microsoft, poskytovatel připojení a. Klíč s není tajným kódem z bezpečnostních důvodů. Existuje mapování 1:1 mezi okruhu ExpressRoute a s-key.

Okruh ExpressRoute může mít až tři nezávislá partnerských vztahů: Azure privátní veřejné, Azure a Microsoft. Každý partnerský vztah je pár nezávislé BGP relace z nich redundantně nakonfigurovaný pro vysokou dostupnost. Není 1: n (1 < = N < = 3) mapování mezi okruhu ExpressRoute a domény směrování. Okruh ExpressRoute může mít libovolný, dvě nebo všechny tři partnerské vztahy, které jsou povolené jeden okruh ExpressRoute.

Každý okruh má pevnou šířku pásma (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s) a je namapována na poskytovatele připojení a umístění partnerského vztahu. Šířku pásma, kterou jste vybrali, je sdílen mezi partnerských vztahů pro okruh. 

### <a name="quotas-limits-and-limitations"></a>Kvóty, limity a omezení
Výchozí kvóty a omezení platí pro každý okruh ExpressRoute. Odkazovat [předplatné Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md) stránky pro aktuální informace o kvótách.

## <a name="expressroute-routing-domains"></a>Domény směrování ExpressRoute
Okruh ExpressRoute má přidruženo více doménách směrování: Azure privátní veřejné, Azure a Microsoft. Každá z domén směrování je stejně nakonfigurovaná na pár směrovače (ve sdílení zatížení nebo aktivní aktivní konfigurace) pro zajištění vysoké dostupnosti. Služby Azure jsou klasifikovány jako *Azure veřejného* a *Azure privátní* představující IP adresování schémata.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a>Soukromý partnerský vztah Azure
Výpočetní služby, a to virtuálních počítačích (IaaS) Azure a cloudové služby (PaaS), které jsou nasazeny v rámci virtuální sítě můžete připojit přes privátní partnerský vztah domény. Privátní partnerský vztah domény, se považuje za důvěryhodným rozšířením vaší základní sítě do Microsoft Azure. Můžete nastavit obousměrné připojení mezi vaší základní sítě a virtuální sítě Azure (Vnet). Tento partnerský vztah umožňuje připojení k virtuálním počítačům a cloudovým službám přímo na jejich privátní IP adresy.  

Více než jedné virtuální sítě můžete připojit k privátní partnerský vztah domény. Zkontrolujte [stránku s nejčastějšími dotazy](expressroute-faqs.md) informace o omezení a omezení. Můžete přejít [předplatné Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md) stránky pro aktuální informace o limitech.  Odkazovat [směrování](expressroute-routing.md) stránku podrobné informace o konfiguraci směrování.

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Připojení k online službám Microsoftu (Office 365, Dynamics 365 a Azure PaaS služby), je prostřednictvím partnerského vztahu Microsoftu. Povolujeme obousměrné připojení mezi cloudovými službami prostřednictvím partnerského vztahu směrování domény Microsoft vaší sítě WAN a Microsoft. Musíte se připojit ke cloudovým službám Microsoftu jenom přes veřejné IP adresy, které vlastníte vy nebo váš poskytovatel připojení a musí dodržovat veškerá definovaná pravidla. Další informace najdete v tématu [požadavky služby ExpressRoute](expressroute-prerequisites.md) stránky.

Zobrazit [stránku s nejčastějšími dotazy](expressroute-faqs.md) pro další informace o službách, které jsou podporovány, náklady a podrobnosti o konfiguraci. Zobrazit [umístění ExpressRoute](expressroute-locations.md) stránka informace o seznamu poskytovatelé připojení nabízejí podporu partnerského vztahu Microsoftu.

### <a name="azure-public-peering"></a>Azure veřejného partnerského vztahu (zastaralé funkce pro nové okruhy)

> [!IMPORTANT]
> Veřejný partnerský vztah Azure není k dispozici pro nové okruhy.  
>   

Služby, jako je Azure Storage, SQL Database a Websites se nabízejí na veřejné IP adresy. Soukromě můžete připojit ke službám hostovaným na veřejných IP adresách, včetně virtuálních IP adres z vašich cloudových služeb prostřednictvím veřejného partnerského vztahu domény směrování. Můžete připojit veřejný partnerský vztah domény k vaší hraniční sítě a připojení ke všem službám Azure na jejich veřejné IP adresy z vaší sítě WAN, aniž byste museli připojovat prostřednictvím Internetu. 

Připojení je vždycky iniciováno z vaší sítě WAN do služby Microsoft Azure. Služby Microsoft Azure, nebude možné navázat připojení do sítě přes tuto doménu směrování. Po povolení veřejného partnerského vztahu můžete připojit ke všem službám Azure. Jsme není umožňují selektivně vyberte služby, u kterých jsme inzerování tras do.

Můžete definovat vlastní trasy filtry ve vaší síti využívat pouze trasy, které potřebujete. Odkazovat [směrování](expressroute-routing.md) stránku podrobné informace o konfiguraci směrování. 

Další informace o službách, které jsou podporované prostřednictvím veřejného partnerského vztahu domény směrování, najdete v článku [nejčastější dotazy k](expressroute-faqs.md).

## <a name="routing-domain-comparison"></a>Porovnání směrování domény
Následující tabulka porovnává tři domény směrování:

|  | **Soukromý partnerský vztah** | **Partnerský vztah Microsoftu** |  **Veřejné partnerské vztahy** (zastaralé pro okruhy) |
| --- | --- | --- | --- |
| **Maximální počet. předpony # podporuje na vytvoření partnerského vztahu** |4000 standardně 10 000 operací s ExpressRoute Premium |200 |200 |
| **Rozsahy IP adres nepodporuje** |Libovolná platná IP adresa v rámci vaší sítě WAN. |Veřejné IP adresy vlastníte vy nebo váš poskytovatel připojení. |Veřejné IP adresy vlastníte vy nebo váš poskytovatel připojení. |
| **JAKO počet požadavků** |Privátní a veřejná čísla AS. Musíte vlastnit veřejně jako číslo, pokud se rozhodnete použít. |Privátní a veřejná čísla AS. Však musí být velmi vlastnictví veřejné IP adresy. |Privátní a veřejná čísla AS. Však musí být velmi vlastnictví veřejné IP adresy. |
| **Podporované protokoly IP**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Směrování rozhraní IP adresy** |Definice RFC1918 a veřejné IP adresy |Veřejné IP adresy registrované na vás v registrech směrování. |Veřejné IP adresy registrované na vás v registrech směrování. |
| **Hodnota Hash MD5 podpory** |Ano |Ano |Ano |

Můžete povolit jeden nebo více domén směrování jako součást váš okruh ExpressRoute. Můžete zvolit, aby všechny domény směrování umístit na stejnou síť VPN, pokud chcete zkombinovat do jedné domény směrování. Můžete je také umístit na různé domény směrování, podobně jako na obrázku. Doporučená konfigurace je, že soukromého partnerského vztahu je připojený přímo k základní sítě a veřejného partnerského vztahu a partnerského vztahu Microsoftu odkazy jsou připojené k vaší hraniční síti.

Pokud budete chtít mít všechny tři partnerské relace, musíte mít tři páry relací protokolu BGP (jednu dvojici pro každý typ partnerského vztahu). Následující páry relace protokolu BGP propojují s vysokou dostupností. Pokud se chcete připojit prostřednictvím poskytovatelů vrstvy 2 připojení, zodpovídáte za konfiguraci a správu směrování. Další informace kontrolou [pracovních postupů](expressroute-workflows.md) pro nastavení ExpressRoute.

## <a name="expressroute-health"></a>Stav ExpressRoute
Okruhy ExpressRoute může monitorovat dostupnost, připojení k virtuálním sítím a pomocí využití šířky pásma [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM monitoruje stav soukromý partnerský vztah Azure a partnerský vztah Microsoftu.  Podívejte se na naše [příspěvku](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/) Další informace.

## <a name="next-steps"></a>Další postup
* Vyhledejte poskytovatele služeb. Zobrazit [poskytovatelé a umístění služby ExpressRoute](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření a správa okruhů ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace směrování (partnerského vztahu) pro okruhy ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)

