---
title: 'Přehled ExpressRoute: Rozšiřte svoji místní síť do Azure pomocí soukromého připojení | Dokumentace Microsoftu'
description: Tento technický přehled ExpressRoute vysvětluje, jak funguje připojení ExpressRoute a jak můžete svoji místní síť rozšířit do Azure pomocí soukromého připojení.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: 75d69669945d31fbc9876d0c1e709b37f96aee6d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="expressroute-overview"></a>Přehled ExpressRoute
Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.

Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), síť Ethernet typu point-to-point nebo virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. To umožňuje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet. Informace o připojení sítě k Microsoftu pomocí ExpressRoute najdete v tématu o [modelech připojení ExpressRoute](expressroute-connectivity-models.md).

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Klíčové výhody

* Připojení vrstvy 3 mezi místní sítí a v cloudem Microsoftu prostřednictvím poskytovatele připojení. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), připojení Ethernet typu point-to-point nebo virtuální křížové připojení přes ethernetovou výměnu.
* Připojení ke cloudovým službám Microsoftu přes všechny oblasti v geopolitické oblasti.
* Globální připojení ke službám Microsoftu přes všechny oblasti s doplňkem ExpressRoute Premium.
* Dynamické směrování mezi vaší sítí a Microsoftem prostřednictvím standardních protokolů (BGP).
* Vestavěná redundance v každém umístění partnerského vztahu z důvodu vyšší spolehlivosti.
* Smlouva [SLA](https://azure.microsoft.com/support/legal/sla/) pro provoz připojení.
* Podpora technologie QoS pro Skype pro firmy.

Další informace najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

## <a name="features"></a>Funkce

### <a name="layer-3-connectivity"></a>Připojení vrstvy 3
Microsoft používá standardní protokol dynamického směrování (BGP) k výměně tras mezi místní sítí, vašimi instancemi v Azure a veřejnými adresami Microsoftu.  Navážeme několik relací protokolu BGP s vaší sítí pro různé profily přenosu. Další informace jsou uvedené v tématu [Okruh ExpressRoute a domény směrování](expressroute-circuit-peerings.md).

### <a name="redundancy"></a>Redundance
Každý okruh ExpressRoute sestává ze dvou připojení ke dvěma hraničním směrovačům Microsoft Enterprise (MSEE) od poskytovatele připojení nebo z hranice vaší sítě. Microsoft vyžaduje od poskytovatele připojení nebo z vaší strany ke každému směrovači MSEE duální připojení BGP. Můžete se rozhodnout nenasazovat redundantní zařízení nebo ethernetové okruhy na vaší straně. Poskytovatelé připojení však používají redundantní zařízení k zajištění, že vaše připojení jsou předávána Microsoftu redundantním způsobem. Konfigurace redundantního připojení vrstvy 3 je požadavkem k tomu, aby byla naše smlouva [SLA](https://azure.microsoft.com/support/legal/sla/) platná.

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
Když se připojíte k Microsoftu v jednom z našich [umístění partnerského vztahu](expressroute-locations.md), budete mít přístup ke všem oblastem v geopolitické oblasti. 

Pokud jste například připojení k Microsoftu prostřednictvím ExpressRoute v Amsterdamu, máte přístup ke všem cloudovým službám Microsoftu hostovaným v oblastech Severní Evropa a Západní Evropa. Přehled geopolitických oblastí, přidružených oblastí cloudu Microsoftu a odpovídajících umístění partnerských vztahů ExpressRoute najdete v článku [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Globální připojení s doplňkem ExpressRoute Premium
Můžete povolit funkci doplňku ExpressRoute Premium, abyste rozšířili připojení přes geopolitické hranice. Pokud jste například připojení k Microsoftu prostřednictvím ExpressRoute v Amsterdamu, budete mít přístup ke všem cloudovým službám Microsoftu hostovaným ve všech oblastech po celém světě (národní cloudy jsou vyloučeny). Můžete přistupovat ke službám nasazeným v oblastech Jižní Amerika nebo Austrálie stejným způsobem, jakým přistupujete k oblastem Severní Evropa a Západní Evropa.

### <a name="rich-connectivity-partner-ecosystem"></a>Bohatý ekosystém partnerů připojení
ExpressRoute má stále rostoucí ekosystém poskytovatelů připojení a partnerů SI. Nejnovější informace najdete v článku [Poskytovatelé a umístění služby ExpressRoute](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Připojení k národním cloudům
Microsoft provozuje izolovaná cloudová prostředí pro speciální geopolitické oblasti a segmenty zákazníků. Seznam národních cloudů a poskytovatelů najdete na stránce [Poskytovatelé a umístění služby ExpressRoute](expressroute-locations.md).

### <a name="bandwidth-options"></a>Možnosti šířky pásma
Okruhy ExpressRoute můžete zakoupit pro širokou škálu šířek pásma. Seznam podporovaných šířek pásma je uvedený dál. U svého poskytovatele připojení zkontrolujte, které z podporovaných šířek pásma poskytuje.

* 50 Mb/s
* 100 Mb/s
* 200 Mb/s
* 500 Mb/s
* 1 Gb/s
* 2 Gb/s
* 5 Gb/s
* 10 Gb/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamické škálování šířky pásma
Můžete zvětšit šířku pásma okruhu ExpressRoute (jak kapacita systému dovolí) bez nutnosti přerušit připojení. 

### <a name="flexible-billing-models"></a>Flexibilní modely fakturace
Můžete si vybrat fakturační model, který vám nejlépe vyhovuje. Zvolte si některý z fakturačních modelů uvedených dál. Další informace najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

* **Neomezená data** Okruh ExpressRoute je účtován na základě měsíčních poplatků a všechny příchozí a odchozí přenosy dat jsou zahrnuté zdarma. 
* **Měření podle objemu dat**. Okruh ExpressRoute je účtován na základě měsíčních poplatků. Všechny příchozí přenosy dat jsou zadarmo. Odchozí přenosy dat se účtují podle přenesených gigabajtů. Sazby za přenos dat se liší podle oblasti.
* **Doplněk ExpressRoute Premium**. ExpressRoute Premium je doplněk nad rámec okruhu ExpressRoute. Doplněk ExpressRoute Premium poskytuje následující možnosti: 
  * Zvýšené limity tras pro veřejný partnerský vztah Azure a soukromý partnerský vztah Azure ze 4 000 tras na 10 000 tras.
  * Globální připojení pro služby. Okruh ExpressRoute vytvořený v libovolné oblasti (s výjimkou národních cloudů) bude mít přístup k prostředkům v libovolné jiné oblasti na světě. Například virtuální sítě vytvořené v oblasti Západní Evropa budou přístupné prostřednictvím okruhu ExpressRoute zřízeného ze Silicon Valley.
  * Zvýšení počtu propojení virtuálních sítí na jeden okruh ExpressRoute z 10 na vyšší limit, v závislosti na šířce pásma okruhu.

## <a name="faq"></a>Nejčastější dotazy

Nejčastější dotazy týkající se ExpressRoute najdete v tématu [Nejčastější dotazy k ExpressRoute](expressroute-faqs.md).

## <a name="next-steps"></a>Další kroky

* Seznamte se s [modely připojení ExpressRoute](expressroute-connectivity-models.md).
* Přečtěte si další informace o připojeních ExpressRoute a doménách směrování. Viz [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Vyhledejte poskytovatele služeb. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Přečtěte si požadavky pro [směrování](expressroute-routing.md), [překlad adres (NAT)](expressroute-nat.md) a [technologii QoS](expressroute-qos.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
  * [Připojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
