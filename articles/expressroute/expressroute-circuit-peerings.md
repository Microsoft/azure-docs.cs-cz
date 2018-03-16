---
title: "Azure okruhy ExpressRoute a domény směrování | Microsoft Docs"
description: "Tato stránka obsahuje přehled okruhy ExpressRoute a domény směrování."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6f0c5d8e-cc60-4a04-8641-2c211bda93d9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: ganesr,cherylmc
ms.openlocfilehash: 943305c78a17031d647bf2fa0977d10c51213ef5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="expressroute-circuits-and-routing-domains"></a>Okruhy ExpressRoute a domény směrování
 Musíte uspořádat *okruh ExpressRoute* pro připojení k místní infrastruktuře společnosti Microsoft prostřednictvím poskytovatele připojení. Následující obrázek ukazuje vytvoření logického vyjádření připojení mezi vaší sítě WAN a společnosti Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>Okruhy ExpressRoute
*Okruh ExpressRoute* představuje logické připojení mezi místní infrastrukturu a cloudových služeb Microsoftu prostřednictvím poskytovatele připojení. Můžete uspořádat více okruhů ExpressRoute. Každý okruh může být ve stejné nebo různých oblastí a může být připojen k vaší místní prostřednictvím poskytovatelů jiné připojení. 

Okruhy ExpressRoute se nemapují do všech fyzických entit. Okruh je jedinečně identifikovaný standardního GUID říká klíče služby (s klíč). Jediná informace, na které se vyměňují mezi společností Microsoft, poskytovatele připojení a můžete je klíč služby. S – klíč je tajný klíč pro účely zabezpečení. Existuje mapování 1:1 mezi okruh ExpressRoute a s klíč.

Okruh ExpressRoute může mít až tři nezávislá partnerských vztahů: Azure privátní veřejné, Azure a společnosti Microsoft. Každý partnerský vztah je pár nezávislé BGP relací každý z nich redundantně nakonfigurovaný pro vysokou dostupnost. Je 1: n (1 < = N < = 3) mapování mezi okruh ExpressRoute a domény směrování. Okruh ExpressRoute může mít některého, dvě nebo všechny tři partnerské vztahy, které jsou povolené na jeden okruh ExpressRoute.

Každý okruh má pevnou šířku pásma (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s) a je namapovaná na poskytovatele připojení a umístění partnerského vztahu. Šířku pásma, kterou jste vybrali, byl sdílen napříč partnerských vztahů pro okruh. 

### <a name="quotas-limits-and-limitations"></a>Kvót, omezení a omezení
Výchozí kvóty a omezení platí pro každý okruh ExpressRoute. Odkazovat [předplatné Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md) aktuální informace o kvóty.

## <a name="expressroute-routing-domains"></a>Domény směrování ExpressRoute
Okruh ExpressRoute má přidruženo více domény směrování: Azure privátní veřejné, Azure a společnosti Microsoft. Všechny domény směrování nakonfigurované stejně jako na pár směrovače (ve sdílení zatížení nebo aktivní aktivní konfigurace) pro zajištění vysoké dostupnosti. Služby Azure jsou klasifikovány jako *Azure veřejné* a *Azure privátní* představují IP adresách schémat.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a>Azure soukromého partnerského vztahu
Azure výpočetní služby, konkrétně virtuální počítače (IaaS) a cloudové služby (PaaS), které jsou nasazeny v rámci virtuální sítě může být připojen prostřednictvím soukromého partnerského vztahu domény. Soukromého partnerského vztahu domény se považuje za důvěryhodné rozšíření základní sítě do Microsoft Azure. Můžete nastavit obousměrné připojení mezi základní sítě a virtuální sítě Azure (virtuální sítě). Partnerský vztah umožňuje připojení k virtuálním počítačům a cloudových služeb přímo na jejich soukromé IP adresy.  

Více než jednu virtuální síť můžete připojit k doméně soukromého partnerského vztahu. Zkontrolujte [stránka s nejčastějšími dotazy](expressroute-faqs.md) informace o omezení. Můžete navštívit [předplatné Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md) aktuální informace o omezení.  Odkazovat [směrování](expressroute-routing.md) podrobné informace o konfiguraci směrování.

### <a name="azure-public-peering"></a>Veřejný partnerský vztah Azure

> [!IMPORTANT]
> Všechny služby Azure PaaS jsou přístupné také prostřednictvím partnerského vztahu Microsoftu. Doporučujeme vytvořit partnerský vztah Microsoftu a přes něj se připojovat ke službám Azure PaaS.  
>   


Na veřejné IP adresy jsou nabízené služby, například Azure Storage, databáze SQL a weby. Soukromě můžete připojit ke službám hostovaným na veřejné IP adresy, včetně virtuální IP adresy z vašich cloudových služeb prostřednictvím veřejného partnerského vztahu doménu směrování. Můžete připojit veřejného partnerského vztahu domény k vaší hraniční sítě a připojení ke všem službám Azure na jejich veřejné IP adresy z vaší sítě WAN bez nutnosti připojení přes internet. 

Připojení je vždycky iniciováno z vaší sítě WAN do služby Microsoft Azure. Služby Microsoft Azure nebudou moct iniciovat připojení do sítě prostřednictvím této domény směrování. Po povolení veřejného partnerského vztahu můžete připojit ke všem službám Azure. Můžeme vám nepovolují selektivně vyberte služby, pro které jsme Inzerovat trasy k.

V rámci vaší sítě využívat jenom trasy, které potřebujete, můžete definovat vlastní trasy filtry. Odkazovat [směrování](expressroute-routing.md) podrobné informace o konfiguraci směrování. 

Další informace o službách, které jsou podporovány prostřednictvím veřejného partnerského vztahu doménu směrování, najdete v článku [– nejčastější dotazy](expressroute-faqs.md).

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Připojení ke službám Microsoft online services (služby Office 365, Dynamics 365 a Azure PaaS) je prostřednictvím partnerského vztahu Microsoftu. Můžeme povolit obousměrné připojení mezi vaší sítě WAN a cloudové služby Microsoft prostřednictvím partnerského vztahu směrování domény Microsoft. Je nutné se připojit ke cloudovým službám Microsoftu jenom přes veřejné IP adresy, které jsou vlastněny vy nebo váš poskytovatel připojení a musí dodržovat veškerá definovaná pravidla. Další informace najdete v tématu [požadavky služby ExpressRoute](expressroute-prerequisites.md) stránky.

Najdete v článku [stránka s nejčastějšími dotazy](expressroute-faqs.md) Další informace o služeb podporovaných, náklady a podrobnosti o konfiguraci. Najdete v článku [umístění ExpressRoute](expressroute-locations.md) informace o seznamu poskytovatelů připojení nabízí podporu partnerského vztahu Microsoftu.

## <a name="routing-domain-comparison"></a>Porovnání směrování domény
Následující tabulka porovnává tři domény směrování:

|  | **Soukromého partnerského vztahu** | **Veřejný partnerský vztah** | **Partnerský vztah Microsoftu*** |
| --- | --- | --- | --- |
| **Max. # předpony podporované na partnerský vztah** |4000 standardně 10 000 s ExpressRoute Premium |200 |200 |
| **Podporované rozsahy IP adres** |Všechny platnou IP adresu v rámci vaší sítě WAN. |Veřejné IP adresy vlastníkem vy nebo váš poskytovatel připojení. |Veřejné IP adresy vlastníkem vy nebo váš poskytovatel připojení. |
| **JAKO počet požadavků** |Privátní a veřejné jako čísla. Musí být vlastníkem veřejnosti jako číslo, pokud se rozhodnete použít. |Privátní a veřejné jako čísla. Však musí prokázat vlastnictví veřejné IP adresy. |Privátní a veřejné jako čísla. Však musí prokázat vlastnictví veřejné IP adresy. |
| **Podporované protokoly IP**| IPv4 | IPv4 | IPv4, IPv6 |
| **Směrování rozhraní IP adresy** |RFC1918 a veřejné IP adresy |Veřejné IP adresy zaregistrované na vás v registrech směrování. |Veřejné IP adresy zaregistrované na vás v registrech směrování. |
| **Hodnota Hash MD5 podpory** |Ano |Ano |Ano |

(*) Vyžaduje úroveň Premium rozšíření SKU

Můžete povolit jeden nebo více domén směrování v rámci okruhu ExpressRoute. Můžete mít všechny domény směrování umístit na stejnou síť VPN, pokud chcete sloučit je do jedné domény směrování. Můžete je také umístit na různé domény směrování, podobně jako na obrázku. Doporučená konfigurace je soukromého partnerského vztahu je připojený přímo k základní síti, a veřejných a odkazy partnerského vztahu Microsoftu jsou připojené k vaší hraniční sítě.

Pokud je se rozhodli pro všechny tři relace partnerského vztahu, musí obsahovat tři dvojici relací protokolu BGP (jednu dvojici pro každý typ partnerského vztahu). Páry relace protokolu BGP poskytovat vysoce dostupné odkaz. Pokud se připojujete pomocí poskytovatelů vrstvy 2 připojení, jste zodpovědní za konfiguraci a správu směrování. Další informace najdete [pracovních](expressroute-workflows.md) pro nastavení ExpressRoute.

## <a name="next-steps"></a>Další postup
* Vyhledejte poskytovatele služeb. V tématu [ExpressRoute služby poskytovatelé a umístění](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření a správa okruhů ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace směrování (partnerského vztahu) pro okruhy ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)

