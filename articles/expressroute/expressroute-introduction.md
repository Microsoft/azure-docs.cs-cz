---
title: 'Přehled Azure ExpressRoute: připojení přes privátní připojení'
description: Technický přehled ExpressRoute vysvětluje, jak funguje připojení ExpressRoute a jak můžete svoji místní síť rozšířit do Azure pomocí soukromého připojení.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: 82562eae748753cd785851c5d91f5f152b4c9960
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206947"
---
# <a name="what-is-azure-expressroute"></a>Co je Azure ExpressRoute?
ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes privátní připojení za pomoci poskytovatele připojení. Pomocí ExpressRoute můžete vytvořit připojení ke cloudovým službám Microsoftu, například Microsoft Azure a Microsoft 365.

Připojení může být ze sítě mezi libovolnými body (IP VPN), Ethernetové sítě mezi dvěma body nebo virtuálního křížového připojení přes poskytovatele připojení v kolokačním zařízení. Připojení ExpressRoute neprocházejí přes veřejný internet. Díky tomu mohou připojení ExpressRoute nabídnout větší spolehlivost, vyšší rychlost, konzistentní latenci a silnější zabezpečení než typická připojení přes internet. Informace o tom, jak připojit síť k Microsoftu pomocí ExpressRoute, najdete v tématu [modely připojení ExpressRoute](expressroute-connectivity-models.md).

![Přehled připojení ExpressRoute](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Klíčové výhody

* Připojení vrstvy 3 mezi místní sítí a v cloudem Microsoftu prostřednictvím poskytovatele připojení. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), připojení Ethernet typu point-to-point nebo virtuální křížové připojení přes ethernetovou výměnu.
* Připojení ke cloudovým službám Microsoftu přes všechny oblasti v geopolitické oblasti.
* Globální připojení ke službám Microsoftu ve všech oblastech s doplňkem ExpressRoute Premium.
* Dynamické směrování mezi vaší sítí a Microsoftem přes protokol BGP.
* Vestavěná redundance v každém umístění partnerského vztahu z důvodu vyšší spolehlivosti.
* [Smlouvy SLA](https://azure.microsoft.com/support/legal/sla/)pro dobu připojení
* Podpora technologie QoS pro Skype pro firmy.

Další informace najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

## <a name="features"></a>Funkce

### <a name="layer-3-connectivity"></a>Připojení vrstvy 3
Microsoft používá standardní protokol dynamického směrování BGP k výměně tras mezi vaší místní sítí, vašimi instancemi v Azure a veřejnými adresami Microsoftu. S vaší sítí navážeme u různých profilů přenosu několik relací protokolu BGP. Další informace jsou uvedené v tématu [Okruh ExpressRoute a domény směrování](expressroute-circuit-peerings.md).

### <a name="redundancy"></a>Redundance
Každý okruh ExpressRoute sestává ze dvou připojení ke dvěma hraničním směrovačům Microsoft Enterprise (směrovači msee) na [umístění ExpressRoute](./expressroute-locations.md#expressroute-locations) od poskytovatele připojení nebo z hraniční sítě. Microsoft vyžaduje od poskytovatele připojení nebo z hranice vaší sítě ke každému směrovači MSEE duální připojení BGP. Můžete se rozhodnout nenasazovat redundantní zařízení nebo ethernetové okruhy na vaší straně. Poskytovatelé připojení však používají redundantní zařízení k zajištění, že vaše připojení jsou předávána Microsoftu redundantním způsobem. Konfigurace redundantního připojení vrstvy 3 je požadavkem k tomu, aby byla naše smlouva [SLA](https://azure.microsoft.com/support/legal/sla/) platná.

### <a name="connectivity-to-microsoft-cloud-services"></a>Připojení ke cloudovým službám Microsoftu
Připojení ExpressRoute umožňují přístup k následujícím službám:
* Služby Microsoft Azure
* Služby Microsoftu 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Podrobný seznam služeb podporovaných přes ExpressRoute najdete na stránce [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Připojení ke všem oblastem v geopolitické oblasti
Když se připojíte k Microsoftu v jednom z našich [umístění partnerského vztahu](expressroute-locations.md), budete mít přístup k oblastem v příslušné geopolitické oblasti.

Například pokud se k Microsoftu připojíte v Amsterdam prostřednictvím ExpressRoute. Budete mít přístup ke všem cloudovým službám Microsoftu hostovaným v Severní a západní Evropě. Přehled geopolitických oblastí, přidružených oblastí cloudu Microsoftu a odpovídajících umístění partnerských vztahů ExpressRoute najdete v článku [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium"></a>Globální konektivita s ExpressRoute Premium
[ExpressRoute Premium](expressroute-faqs.md) můžete povolit pro rozšiřování připojení napříč geopolitickými hranicemi. Pokud se například připojujete k Microsoftu v Amsterodami Amsterdam prostřednictvím ExpressRoute, budete mít přístup ke všem cloudovým službám Microsoftu hostovaným ve všech oblastech po celém světě. Můžete také přistupovat ke službám nasazeným v Jižní Americe nebo Austrálii stejným způsobem jako při přístupu do oblastí sever a Západní Evropa. Národní cloudy jsou vyloučené.

### <a name="local-connectivity-with-expressroute-local"></a>Místní připojení pomocí ExpressRoute Local
Náklady na data můžete efektivně přenášet tím, že povolíte [místní SKU](expressroute-faqs.md). S místní SKU můžete data přenést do umístění ExpressRoute poblíž oblasti Azure, kterou chcete. V případě místního přenosu dat se do ExpressRoute portu zařadí poplatky za přenos dat. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Propojení místních lokalit pomocí ExpressRoute Global Reach
Službě ExpressRoute Global Reach můžete umožnit výměnu dat mezi místními lokalitami propojením okruhů ExpressRoute. Například pokud máte soukromé datové centrum v Kalifornii připojené k okruhu ExpressRoute v Silicon sedlu a v jiném soukromém datovém centru v Texas připojení k okruhu ExpressRoute v Dallasu. Pomocí ExpressRoute Global Reach můžete propojit vaše privátní datová centra společně prostřednictvím těchto dvou okruhů ExpressRoute. Provoz mezi datacentry bude procházet přes síť Microsoftu.

Další informace najdete v článku [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Bohatý ekosystém partnerů připojení
ExpressRoute má neustále se rozšiřující ekosystém poskytovatelů připojení a partnerů mezi systémovými integrátory. Nejnovější informace najdete v článku [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Připojení k národním cloudům
Microsoft provozuje izolovaná cloudová prostředí pro speciální geopolitické oblasti a segmenty zákazníků. Seznam národních cloudů a poskytovatelů najdete na stránce [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct poskytuje zákazníkům možnost připojit se přímo ke globální síti Microsoftu v umístěních partnerského vztahu strategicky rozmístěných po celém světě. ExpressRoute Direct poskytuje duální připojení 100 GB/s, které podporuje škálování aktivní/aktivní.

K dispozici jsou klíčové funkce, které ExpressRoute Direct poskytuje, ale nejsou omezené na:

* Masivní příjem dat do služeb jako Storage a Cosmos DB
* Fyzická izolace pro odvětví, která jsou regulovaná a vyžadují vyhrazené a izolované připojení, například: bankovnictví, státní správa a maloobchodní prodej
* Podrobné řízení distribuce okruhů podle organizační jednotky

Další informace najdete v článku o [ExpressRoute Direct](./expressroute-erdirect-about.md).

### <a name="bandwidth-options"></a>Možnosti šířky pásma
Okruhy ExpressRoute můžete zakoupit pro širokou škálu šířek pásma. Podporované šířky pásma jsou uvedeny jako následováno. U svého poskytovatele připojení nezapomeňte zkontrolovat, které z podporovaných šířek pásma podporuje.

* 50 Mb/s
* 100 Mb/s
* 200 Mb/s
* 500 Mb/s
* 1 Gb/s
* 2 Gb/s
* 5 Gb/s
* 10 Gb/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamické škálování šířky pásma
Můžete zvětšit šířku pásma okruhu ExpressRoute (jak kapacita systému dovolí) bez nutnosti přerušit připojení. Další informace najdete v tématu [Úprava okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Flexibilní modely fakturace
Můžete si vybrat fakturační model, který vám nejlépe vyhovuje. Vyberte si mezi modely fakturace, které jsou uvedené jako sledované. Další informace najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

* **Neomezená data** Fakturuje se na základě měsíčních poplatků a všechny příchozí a odchozí přenosy dat jsou zahrnuté zdarma.
* **Měření podle objemu dat**. Fakturuje se na základě měsíčních poplatků a všechny příchozí přenosy dat jsou zdarma. Odchozí přenosy dat se účtují podle přenesených gigabajtů. Sazby za přenos dat se liší podle oblasti.
* **Doplněk ExpressRoute Premium**. ExpressRoute Premium je doplněk k okruhu ExpressRoute. Doplněk ExpressRoute Premium poskytuje následující možnosti: 
  * Zvýšené limity tras pro veřejný partnerský vztah Azure a soukromý partnerský vztah Azure ze 4 000 tras na 10 000 tras.
  * Globální připojení pro služby. Okruh ExpressRoute vytvořený v libovolné oblasti (s výjimkou národních cloudů) bude mít přístup k prostředkům v každé jiné oblasti na světě. Například virtuální sítě vytvořené v oblasti Západní Evropa budou přístupné prostřednictvím okruhu ExpressRoute zřízeného ze Silicon Valley.
  * Zvýšení počtu propojení virtuálních sítí na jeden okruh ExpressRoute z 10 na vyšší limit, v závislosti na šířce pásma okruhu.

## <a name="faq"></a>Nejčastější dotazy
Nejčastější dotazy týkající se ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Co je nového?

Přihlaste se k odběru informačního kanálu RSS a zobrazte nejnovější aktualizace funkcí ExpressRoute na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute) .

## <a name="next-steps"></a>Další kroky
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Seznamte se s [modely připojení ExpressRoute](expressroute-connectivity-models.md).
* Vyhledejte poskytovatele služeb. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).