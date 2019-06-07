---
title: 'Umístění a poskytovatelé připojení: Azure ExpressRoute | Dokumentace Microsoftu'
description: Tento článek obsahuje podrobný přehled o umístěních, kde jsou nabízené služby, a jak se připojit k oblastem Azure. Řazení je podle umístění.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: jaredr80
ms.openlocfilehash: c443d4705da83a43af10763f633f340b1eab69d3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478159"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partneři ExpressRoute a umístění partnerského vztahu

> [!div class="op_single_selector"]
> * [Umístění podle poskytovatele](expressroute-locations.md)
> * [Poskytovatelé podle umístění](expressroute-locations-providers.md)


Tabulky v tomto článku poskytují informace o poskytovatelích připojení ExpressRoute, zeměpisném pokrytí ExpressRoute, cloudových službách Microsoftu podporovaných přes ExpressRoute a systémových integrátorech (SI) ExpressRoute.

## <a name="partners"></a>Poskytovatelé připojení ExpressRoute
Služba ExpressRoute je podporovaná ve všech umístěních a oblastech Azure. Následující mapa obsahuje seznam oblastí Azure a umístění ExpressRoute. Umístění ExpressRoute odkazují na ty, kde má Microsoft partnerský vztah s několika poskytovateli služeb.

![Mapa umístění][0]

Pokud jste připojení k aspoň jednomu umístění ExpressRoute v rámci geopolitické oblasti, budete mít přístup ke službám Azure napříč všemi oblastmi v rámci geopolitické oblasti. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti
Následující tabulka obsahuje mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.

| **Geopolitická oblast** | **Zóna** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- | --- |
| **Australská vláda** | 1 | Austrálie – střed, Austrálie – střed 2 |Canberra, Canberra2 |
| **Evropa** | 1 |Francie – střed, Francie – jih, Severní Evropa, Západní Evropa, Velká Británie – západ, Velká Británie – jih |Amsterdam, Amsterdam2, Dublin, Frankfurt, Londýn, London2, Marseille, Newport (Wales), Paříž, Curych |
| **Severní Amerika** | 1 |Východní USA, Západní USA, Východní USA 2, Západní USA 2, Střed USA, Střed USA – jih, Střed USA – sever, Střed USA – západ, Střední Kanada, Východní Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asie** | 2 |Východní Asie, Jihovýchodní Asie |Hongkong – zvláštní administrativní oblast, Kuala Lumpur, Singapur, singapur2, Tchaj-pej |
| **Austrálie** | 2 |Austrálie – jihovýchod, Austrálie – východ |Melbourne, Perth, Sydney | 
| **Indie** | 2 |Indie – západ, Indie – střed, Indie – jih |Čennaj, Čennaj2, Bombaj, Bombaj2 |
| **Japonsko** | 2 |Japonsko – západ, Japonsko – východ |Ósaka, Tokio |
| **Jižní Korea** | 2 |Jižní Korea – střed, Jižní Korea – jih |Busan, Soul|
| **SPOJENÉ ARABSKÉ EMIRÁTY** | 3 | Spojené arabské emiráty – střed, Spojené arabské emiráty – sever | Dubai, Dubai2 |
| **Jižní Afrika** | 3 |Jihoafrická republika – Západ, Jihoafrická republika – sever |Kapské město, Johannesburg |
| **Jižní Amerika** | 3 |Brazílie – jih |Sao Paulo |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Oblasti a geopolitické hranice pro národní cloudy
Následující tabulka obsahuje informace o oblastech a geopolitických hranicích pro národní cloudy.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Cloud vlády USA** |US Gov – Arizona, US Gov – Iowa, US Gov – Texas, US Gov – Virginie, US DoD – střed, US DoD – východ  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Čína – východ** |Čína – východ, Čína – východ 2 |Šanghaj, Shanghai2 |
| **Čína – sever** |Čína – sever, Čína – sever 2 |Peking, Beijing2 |
| **Německo** |Střední Německo, Východní Německo |Berlín, Frankfurt |

Připojení přes geopolitické oblasti není pomocí standardní SKU pro ExpressRoute podporované. Aby bylo podporované globální připojení, budete muset povolit doplněk ExpressRoute Premium. Připojení k prostředím národních cloudů není podporované. Podle potřeby můžete spolupracovat se svým poskytovatelem připojení.

## <a name="locations"></a>Umístění poskytovatele připojení

Následující tabulka uvádí umístění připojení a poskytovatele služby pro každé umístění. Seznam poskytovatelů služeb a umístění, ve kterých působí, najdete v článku [Umístění podle poskytovatelů služeb](expressroute-locations.md#locations). 

**Místních oblastech Azure** jsou ty, které [ExpressRoute místní](expressroute-faqs.md) přístup v každém umístění partnerského vztahu. **není k dispozici** znamená, že místní ExpressRoute není v tomto umístění partnerského vztahu k dispozici.


### <a name="production-azure"></a>Produkční prostředí Azure
| **Umístění** | **Vlastník umístění partnerského vztahu** | **Místní oblasti Azure** | **Poskytovatelé služeb** |
| --- | --- | --- | --- |
| **Amsterdam** | Equinix | Západní Evropa | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | Interxion | Západní Evropa | DE-CIX, Interxion |
| **Atlanta** | Equinix | neuvedeno | Equinix, Megaport |
| **Busan** |LG CNS | Jižní Korea – jih | LG CNS |
| **Canberra** | CDC | Austrálie – střed | CDC |
| **Canberra2** | CDC | Austrálie – střed 2| CDC |
| **Kapské město** | Teraco | Jižní Afrika – západ | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Čennaj** | Tata Communications | Indie – jih | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Čennaj2** | Airtel | Indie – jih | Airtel |
| **Chicago** | Equinix | Středoseverní USA | Aryaka Networks, AT&T NetBond, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Dallas** | Equinix | neuvedeno | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | CoreSite | Západní střed USA | CoreSite, Megaport, Zayo |
| **Dubai** | Spojené arabské emiráty Etisalat | Spojené arabské emiráty – sever | Spojené arabské emiráty Etisalat |
| **Dubai2** | rozlišované sjednocení typu datamena | Spojené arabské emiráty – sever | rozlišované sjednocení typu datamena, Orixcom |
| **Dublin** | Equinix | Severní Evropa | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | neuvedeno | DE-CIX, Interxion |
| **Hongkong – zvláštní administrativní oblast** | Equinix | Východní Asie | Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Johannesburg** | Teraco | Jižní Afrika – sever | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Kuala Lumpur** | TIME dotCom | neuvedeno | TIME dotCom |
| **Las Vegas** | Přepínač | neuvedeno | CenturyLink Cloud Connect, Megaport |
| **Londýn** | Equinix | Velká Británie – jih | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | Telehouse | Velká Británie – jih | IX Reach, Equinix |
| **Los Angeles** | CoreSite | neuvedeno | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |Interxion | Francie – jih | Interxion |
| **Melbourne** | NextDC | Austrálie – jihovýchod | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | neuvedeno | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | neuvedeno | Bell Canada, Cologix, Telus, Zayo |
| **Bombaj** | Tata Communications | Indie – západ | Globální CloudXchange (GCX), umožňující Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | Indie – západ | Airtel, Sify, Vodafone Idea |
| **New York** | Equinix | neuvedeno | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | Next Generation Data | Spojené království – západ | British Telecom, Colt, Level 3 Communications, příští generace |
| **Ósaka** | Equinix | Japonsko – západ | Equinix, Internet Initiative Japan Inc. – IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paříž** | Interxion | Francie – střed | Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | neuvedeno | Megaport, NextDC |
| **Québec** | 4Degrees | Kanada – východ | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | Středojižní USA | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Brazílie – jih | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | Západní USA 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Soul** | KINX | Korea – střed | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | Equinix | Západní USA | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, tak Coresite, Equinix, InterCloud, IX oslovit paketů, PacketFabric, Level 3 Communications, Megaport, Orange, Sprintu, Tata Communications, Verizon, Zayo |
| **Silicon Valley2** | Coresite | Západní USA | Coresite | 
| **Singapur** | Equinix | Jihovýchodní Asie | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | Globální přepínač | Jihovýchodní Asie | Colt epsilon – globální Communications, Megaport, SingTel |
| **Sydney** | Equinix | Austrálie – východ | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, TPG Telecom, Verizon |
| **Tchaj-pej** | Hlavní telekomunikaci | neuvedeno | Hlavní Telecom, FarEasTone |
| **Tokio** | Equinix | Japonsko – východ | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Kanada – střed | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington, D.C.** | Equinix | USA – východ, USA – východ 2 | Aryaka Networks, AT & T NetBond, British Telecom, Cologix, tak, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, Neutrona sítě, NTT Communications, Orange, PacketFabric, Sprintu, Tata Communications, Telia dopravce Verizon, Zayo |
| **Washington DC2** | Coresite | USA – východ, USA – východ 2 |Coresite | 
| **Curych** | Interxion | neuvedeno | Interxion |

 **+** označuje brzké uvedení.

### <a name="national-cloud-environments"></a>Prostředí národních cloudů

### <a name="us-government-cloud"></a>Cloud vlády USA
| **Umístění** | **Poskytovatelé služeb** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington, D.C.** |AT&T NetBond, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Čína
| **Umístění** | **Poskytovatelé služeb** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | Čína Telecom, GDS |
| **Šanghaj** |China Telecom |
| **Shanghai2** | Čína Telecom, GDS |

Další informace najdete v tématu [ExpressRoute v Číně](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Německo
| **Umístění** | **Poskytovatelé služeb** |
| --- | --- |
| **Berlín** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Možnosti připojení prostřednictvím poskytovatelů Exchange
Pokud poskytovatel připojení není v předchozích částech uvedený, můžete přesto vytvořit připojení.

* Zkontrolujte u svého poskytovatele připojení, jestli je připojený k některé z výměn v předchozí tabulce. Můžete zkontrolovat následující odkazy, abyste získali další informace o službách nabízených poskytovateli výměny. Několik poskytovatelů připojení je už připojeno k ethernetovým výměnám.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Rozšířil váš poskytovatel připojení vaši síť ke zvolenému umístění partnerského vztahu?
  * Ujistěte se, že poskytovatel připojení rozšiřuje vaše připojení vysoce dostupným způsobem tak, aby neexistovaly žádné jediné body selhání.
* Abyste se připojili k Microsoftu, objednejte si okruh ExpressRoute s výměnou jako poskytovatel připojení.
  * Při nastavení připojení postupujte podle kroků v tématu [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md).

## <a name="c1partners"></a>Možnosti připojení prostřednictvím dalších poskytovatelů služeb
| **Umístění** | **Výměna** | **Poskytovatelé připojení** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Telecity | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, MainOne, Nianet, Post, Proximus, TDC Erhverv, Telecom Italia Sparkle, Telia |
| **Kapské město** | Teraco | MTN |
| **Chicago** | Equinix | Windstream Lightower spektra Enterprise |
| **Dallas** | Equinix, Megaport | Firma Axtel C3ntro Telecom Cox obchodní Data Foundry spektra Enterprise, Transtelco |
| **Frankfurt** | Telecity | BICS, Cinia, Nianet, QSC AG |
| **Hamburg** | Equinix | Cinia |
| **Hongkong – zvláštní administrativní oblast** | Equinix | Ředitel pro Macroview telekomunikaci |
| **Johannesburg** | Teraco | MTN |
| **Londýn** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Spektrum podnikových, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice obchodní Lightower, Opensourcová Enterprise, Webair |
| **Paříž** | Equinix | Proximus |
| **Québec** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Equinix | Cox Business, Enterprise spektra Windstream |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix | Airgate Technologies, Inc. Cogeco Peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington, D.C.** |Equinix | Altice Business, BICS, Cox Business, Gtt Communications Inc, Epsilon Telecommunications Limited, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Systémoví integrátoři ExpressRoute
Povolení soukromého připojení podle vlastních potřeb může být náročné, v závislosti na rozsahu vaší sítě. Můžete spolupracovat s kterýmkoli systémovým integrátorem uvedeným v následující tabulce, aby vám pomohl s připojením k ExpressRoute.

| **Kontinent** | **Systémoví integrátoři** |
| --- | --- |
| **Asie** |Avanade Inc., OneAs1a |
| **Austrálie** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Evropa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Severní Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Jižní Amerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Další postup
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa umístění"
