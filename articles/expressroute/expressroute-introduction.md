---
title: 'Přehled ExpressRoute: Rozšiřte svoji místní síť do Azure pomocí soukromého připojení | Dokumentace Microsoftu'
description: Technický přehled ExpressRoute vysvětluje, jak funguje připojení ExpressRoute a jak můžete svoji místní síť rozšířit do Azure pomocí soukromého připojení.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: overview
ms.date: 10/18/2018
ms.author: mialdrid
ms.openlocfilehash: fb3e7ac449934fe9fa36b1683bda20ff3e07f0f4
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429401"
---
# <a name="expressroute-overview"></a>Přehled ExpressRoute
Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.

Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), síť Ethernet typu point-to-point nebo virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet. Informace o připojení sítě k Microsoftu pomocí ExpressRoute najdete v tématu o [modelech připojení ExpressRoute](expressroute-connectivity-models.md).

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Klíčové výhody

* Připojení vrstvy 3 mezi místní sítí a v cloudem Microsoftu prostřednictvím poskytovatele připojení. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), připojení Ethernet typu point-to-point nebo virtuální křížové připojení přes ethernetovou výměnu.
* Připojení ke cloudovým službám Microsoftu přes všechny oblasti v geopolitické oblasti.
* Globální připojení ke službám Microsoftu ve všech oblastech s doplňkem ExpressRoute Premium.
* Dynamické směrování mezi vaší sítí a Microsoftem přes protokol BGP.
* Vestavěná redundance v každém umístění partnerského vztahu z důvodu vyšší spolehlivosti.
* Smlouva [SLA](https://azure.microsoft.com/support/legal/sla/) pro provoz připojení.
* Podpora technologie QoS pro Skype pro firmy.

Další informace najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

## <a name="features"></a>Funkce

### <a name="layer-3-connectivity"></a>Připojení vrstvy 3
Microsoft používá standardní protokol dynamického směrování BGP k výměně tras mezi vaší místní sítí, vašimi instancemi v Azure a veřejnými adresami Microsoftu. Navážeme několik relací protokolu BGP s vaší sítí pro různé profily přenosu. Další informace jsou uvedené v tématu [Okruh ExpressRoute a domény směrování](expressroute-circuit-peerings.md).

### <a name="redundancy"></a>Redundance
Každý okruh ExpressRoute sestává ze dvou připojení ke dvěma hraničním směrovačům Microsoft Enterprise (MSEE) od poskytovatele připojení nebo z hranice vaší sítě. Microsoft vyžaduje od poskytovatele připojení nebo z hranice vaší sítě ke každému směrovači MSEE duální připojení BGP. Můžete se rozhodnout nenasazovat redundantní zařízení nebo ethernetové okruhy na vaší straně. Poskytovatelé připojení však používají redundantní zařízení k zajištění, že vaše připojení jsou předávána Microsoftu redundantním způsobem. Konfigurace redundantního připojení vrstvy 3 je požadavkem k tomu, aby byla naše smlouva [SLA](https://azure.microsoft.com/support/legal/sla/) platná.

### <a name="connectivity-to-microsoft-cloud-services"></a>Připojení ke cloudovým službám Microsoftu
Připojení ExpressRoute umožňují přístup k následujícím službám:
* Služby Microsoft Azure
* Služby Microsoft Office 365
* Microsoft Dynamics 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Podrobný seznam služeb podporovaných přes ExpressRoute najdete na stránce [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Připojení ke všem oblastem v geopolitické oblasti
Když se připojíte k Microsoftu v jednom z našich [umístění partnerského vztahu](expressroute-locations.md), budete mít přístup k oblastem v příslušné geopolitické oblasti.

Pokud se například připojíte k Microsoftu prostřednictvím ExpressRoute v Amsterdamu, budete mít přístup ke všem cloudovým službám Microsoftu hostovaným v oblastech Severní Evropa a Západní Evropa. Přehled geopolitických oblastí, přidružených oblastí cloudu Microsoftu a odpovídajících umístění partnerských vztahů ExpressRoute najdete v článku [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Globální připojení s doplňkem ExpressRoute Premium
Můžete povolit funkci doplňku ExpressRoute Premium, abyste rozšířili připojení přes geopolitické hranice. Pokud se například připojíte k Microsoftu prostřednictvím ExpressRoute v Amsterdamu, budete mít přístup ke všem cloudovým službám Microsoftu hostovaným ve všech oblastech po celém světě (národní cloudy jsou vyloučeny). Můžete přistupovat ke službám nasazeným v oblastech Jižní Amerika nebo Austrálie stejným způsobem, jakým přistupujete k oblastem Severní Evropa a Západní Evropa.

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Propojení místních lokalit pomocí ExpressRoute Global Reach
Můžete povolit ExpressRoute Global Reach pro výměnu dat mezi místními lokalitami díky propojení okruhů ExpressRoute. Pokud třeba máte soukromé datacentrum v Kalifornii připojené k ExpressRoute v Silicon Valley a jiné soukromé datacentrum v Texasu připojené k ExpressRoute v Dallasu, pomocí ExpressRoute Global Reach můžete svá soukromá datacentra propojit prostřednictvím dvou okruhů ExpressRoute. Provoz mezi datacentry bude procházet přes síť Microsoftu.

Další informace najdete v článku [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Bohatý ekosystém partnerů připojení
ExpressRoute má neustále se rozšiřující ekosystém poskytovatelů připojení a partnerů mezi systémovými integrátory. Nejnovější informace najdete v článku [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Připojení k národním cloudům
Microsoft provozuje izolovaná cloudová prostředí pro speciální geopolitické oblasti a segmenty zákazníků. Seznam národních cloudů a poskytovatelů najdete na stránce [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct poskytuje zákazníkům možnost připojit se přímo ke globální síti Microsoftu v umístěních partnerského vztahu strategicky rozmístěných po celém světě. ExpressRoute Direct poskytuje duální připojení 100 Gb/s, které podporuje připojení typu aktivní/aktivní ve velkém měřítku.

Ke klíčovým funkcím, které ExpressRoute Direct poskytuje, patří mimo jiné:

* Masivní příjem dat do služeb jako Storage a Cosmos DB
* Fyzická izolace pro odvětví, která jsou regulovaná a vyžadují vyhrazené a izolované připojení, například: bankovnictví, státní správa a maloobchodní prodej
* Podrobné řízení distribuce okruhů podle organizační jednotky

Další informace najdete v článku o [ExpressRoute Direct](https://go.microsoft.com/fwlink/?linkid=2022973).

### <a name="bandwidth-options"></a>Možnosti šířky pásma
Okruhy ExpressRoute můžete zakoupit pro širokou škálu šířek pásma. Seznam podporovaných šířek pásma je uvedený níže. U svého poskytovatele připojení nezapomeňte zkontrolovat, které z podporovaných šířek pásma podporuje.

* 50 Mb/s
* 100 Mb/s
* 200 Mb/s
* 500 Mb/s
* 1 Gb/s
* 2 Gb/s
* 5 Gb/s
* 10 Gb/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamické škálování šířky pásma
Můžete zvětšit šířku pásma okruhu ExpressRoute (jak kapacita systému dovolí) bez nutnosti přerušit připojení. Další informace najdete v části [Úprava okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Flexibilní modely fakturace
Můžete si vybrat fakturační model, který vám nejlépe vyhovuje. Zvolte si některý z fakturačních modelů uvedených dál. Další informace najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

* **Neomezená data** Fakturuje se na základě měsíčních poplatků a všechny příchozí a odchozí přenosy dat jsou zahrnuté zdarma.
* **Měření podle objemu dat**. Fakturuje se na základě měsíčních poplatků a všechny příchozí přenosy dat jsou zdarma. Odchozí přenosy dat se účtují podle přenesených gigabajtů. Sazby za přenos dat se liší podle oblasti.
* **Doplněk ExpressRoute Premium**. ExpressRoute Premium je doplněk k okruhu ExpressRoute. Doplněk ExpressRoute Premium poskytuje následující možnosti: 
  * Zvýšené limity tras pro veřejný partnerský vztah Azure a soukromý partnerský vztah Azure ze 4 000 tras na 10 000 tras.
  * Globální připojení pro služby. Okruh ExpressRoute vytvořený v libovolné oblasti (s výjimkou národních cloudů) bude mít přístup k prostředkům v libovolné jiné oblasti na světě. Například virtuální sítě vytvořené v oblasti Západní Evropa budou přístupné prostřednictvím okruhu ExpressRoute zřízeného ze Silicon Valley.
  * Zvýšení počtu propojení virtuálních sítí na jeden okruh ExpressRoute z 10 na vyšší limit, v závislosti na šířce pásma okruhu.

## <a name="faq"></a>Nejčastější dotazy
Nejčastější dotazy týkající se ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

## <a name="next-steps"></a>Další kroky
* Seznamte se s [modely připojení ExpressRoute](expressroute-connectivity-models.md).
* Přečtěte si další informace o připojeních ExpressRoute a doménách směrování. Viz [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Vyhledejte poskytovatele služeb. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Přečtěte si požadavky pro [směrování](expressroute-routing.md), [překlad adres (NAT)](expressroute-nat.md) a [technologii QoS](expressroute-qos.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
  * [Připojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
