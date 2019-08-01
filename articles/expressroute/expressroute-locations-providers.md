---
title: 'Umístění a poskytovatelé připojení: Azure ExpressRoute | Microsoft Docs'
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
ms.date: 07/31/2019
ms.author: cherylmc
ms.openlocfilehash: b31a7c1cbd0f4f6cadc4ac139777d2cc3c451bcb
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706467"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partneři ExpressRoute a umístění partnerského vztahu

> [!div class="op_single_selector"]
> * [Umístění podle poskytovatele](expressroute-locations.md)
> * [Poskytovatelé podle umístění](expressroute-locations-providers.md)


Tabulky v tomto článku poskytují informace o geografickém pokrytí a umístěních, ExpressRoute poskytovatelích připojení ExpressRoute a Integrátorech systému ExpressRoute (SIs).

> [!Note]
> Oblasti Azure a umístění ExpressRoute jsou dvě odlišná a odlišná koncepce, protože rozdíl mezi těmito dvěma možnostmi je pro prozkoumávání hybridního připojení k síti Azure důležitý. 
>
>

## <a name="azure-regions"></a>Oblasti Azure
Oblasti Azure jsou globální datová centra, kde se nacházejí výpočetní prostředky, sítě a prostředky úložiště Azure. Při vytváření prostředku Azure musí zákazník vybrat umístění prostředku. Umístění prostředku určuje, ve kterém datovém centru Azure (nebo zóně dostupnosti) se prostředek vytvoří.

## <a name="expressroute-locations"></a>Umístění ExpressRoute
Umístění ExpressRoute (někdy označovaná jako umístění partnerského vztahu nebo dodaná umístění) jsou zařízení ve společném umístění, kde se nachází zařízení Microsoft Enterprise Edge (MSEE). Umístění ExpressRoute jsou vstupním bodem sítě Microsoftu – a jsou globálně distribuované a poskytují zákazníkům možnost připojit se k síti Microsoftu po celém světě. Tato umístění tam, kde ExpressRoute partneři a ExpressRoute přímé zákazníky, vystavují vzájemné připojení k síti Microsoftu. Obecně platí, že umístění ExpressRoute nemusí odpovídat oblasti Azure. Zákazník může například vytvořit okruh ExpressRoute s umístěním prostředku *východní USA*v umístění partnerského vztahu pro *Seattle* .

Pokud jste připojení k aspoň jednomu umístění ExpressRoute v rámci geopolitické oblasti, budete mít přístup ke službám Azure napříč všemi oblastmi v rámci geopolitické oblasti. 

## <a name="locations"></a>Oblasti Azure pro ExpressRoute umístění v rámci geopolitické oblasti
Následující tabulka obsahuje mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.

| **Geopolitická oblast** | **Zóny** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- | --- |
| **Australská vláda** | 1 | Austrálie – střed, Austrálie – střed 2 |Canberra, Canberra2 |
| **Evropa** | 1 |Francie – střed, Francie – jih, Severní Evropa, Západní Evropa, Velká Británie – západ, Velká Británie – jih |Amsterdam, Amsterdam2, Kodaň, Dublin, Frankfurt, Londýn, London2, Marseille, Newport (Wales), Paříž, Stockholm, Curych |
| **Severní Amerika** | 1 |Východní USA, Západní USA, Východní USA 2, Západní USA 2, Střed USA, Střed USA – jih, Střed USA – sever, Střed USA – západ, Střední Kanada, Východní Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asie** | 2 |Východní Asie, Jihovýchodní Asie |Hongkong – zvláštní správní oblast, Kuala Lumpur, Singapur, Singapur2, Taipei |
| **Indie** | 2 |Indie – západ, Indie – střed, Indie – jih |Čennaj, Čennaj2, Bombaj, Bombaj2 |
| **Japonsko** | 2 |Japonsko – západ, Japonsko – východ |Ósaka, Tokio |
| **Oceánie a** | 2 |Austrálie – jihovýchod, Austrálie – východ |Auckland, Melbourne, Perth, Sydney | 
| **Jižní Korea** | 2 |Jižní Korea – střed, Jižní Korea – jih |Busan, Soul|
| **SPOJENÉ ARABSKÉ EMIRÁTY** | 3 | Spojené arabské emiráty Central, Spojené arabské emiráty sever | Dubaj, Dubai2 |
| **Jižní Afrika** | 3 |Jižní Afrika – západ, Jižní Afrika – sever |Kapské město, Johannesburg |
| **Jižní Amerika** | 3 |Brazílie – jih |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Oblasti Azure a geopolitické hranice pro národní cloudy
Následující tabulka obsahuje informace o oblastech a geopolitických hranicích pro národní cloudy.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Cloud vlády USA** |US Gov – Arizona, US Gov – Iowa, US Gov – Texas, US Gov – Virginie, US DoD – střed, US DoD – východ  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Čína – východ** |Čína – východ, Čína – východ 2 |Shanghai, Shanghai2 |
| **Čína – sever** |Čína – sever, Čína – sever 2 |Peking, Beijing2 |
| **Německo** |Střední Německo, Východní Německo |Berlín, Frankfurt |

Připojení přes geopolitické oblasti není pomocí standardní SKU pro ExpressRoute podporované. Aby bylo podporované globální připojení, budete muset povolit doplněk ExpressRoute Premium. Připojení k prostředím národních cloudů není podporované. Podle potřeby můžete spolupracovat se svým poskytovatelem připojení.

## <a name="partners"></a>Poskytovatelé připojení ExpressRoute

Následující tabulka uvádí umístění připojení a poskytovatele služby pro každé umístění. Seznam poskytovatelů služeb a umístění, ve kterých působí, najdete v článku [Umístění podle poskytovatelů služeb](expressroute-locations.md). 

**Místní oblasti Azure** jsou ty, které [ExpressRoute místní](expressroute-faqs.md) v každém umístění partnerského vztahu mají přístup. není k dispozici **, znamená to** , že ExpressRoute Local není v tomto umístění partnerského vztahu k dispozici.


### <a name="production-azure"></a>Produkční prostředí Azure
| **Location** | **Vlastník umístění partnerského vztahu** | **Místní oblasti Azure** | **Poskytovatelé služeb** |
| --- | --- | --- | --- |
| **Amsterdam** | Equinix | Západní Evropa | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | Interxion | Západní Evropa | CenturyLink Cloud Connect, DE-CIX, Interxion, Vodafone |
| **Atlanta** | Equinix | neuvedeno | Equinix, Megaport |
| **Auckland** | Vocus skupina NZ | neuvedeno | Devoli, Kordia, Megaport, Spark NZ, Vocus skupina NZ |
| **Busan** |LG CNS | Jižní Korea – jih | LG CNS |
| **Canberra** | CDC | Austrálie – střed | CDC |
| **Canberra2** | CDC | Austrálie – střed 2| CDC |
| **Kapské město** | Teraco | Jižní Afrika – západ | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Čennaj** | Tata Communications | Jižní Indie | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Čennaj2** | Airtel | Jižní Indie | Airtel |
| **Chicago** | Equinix | Střed USA – sever | Aryaka Networks sítě, v & T NetBond, CenturyLink Cloud Connect, Cologix, Comcastu, Coresite, Equinix, spolucloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW, Telia, Verizon, Zayo |
| **Kodaňských** | Interxion | neuvedeno | Interxion |
| **Dallas** | Equinix | neuvedeno | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | CoreSite | Západní střed USA | CoreSite, Megaport, Zayo |
| **Dubai** | Etisalat Spojené arabské emiráty | Spojené arabské emiráty – sever | Etisalat Spojené arabské emiráty |
| **Dubai2** | du dataměna | Spojené arabské emiráty – sever | du dataměna, Orixcom |
| **Dublin** | Equinix | Severní Evropa | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | neuvedeno | DE-CIX, Interxion |
| **Hongkong – zvláštní správní oblast** | Equinix | Východní Asie | Aryaka Networks sítě, British Telecom, CenturyLink Cloud Connect, generální Telecom, Čína Telecom Global, Equinix, Megaport, NTT Communications, oranžová, PCCW Globaled, Tata Communications, Telia Carrier, Verizon |
| **Johannesburg** | Teraco | Jižní Afrika – sever | British Telecom, Internet Solutions – Cloud Connect, Liquid Telecom, teraco |
| **Kuala Lumpur** | TIME dotCom | neuvedeno | TIME dotCom |
| **Las Vegas** | Přepínač | neuvedeno | CenturyLink Cloud Connect, Megaport |
| **Londýn** | Equinix | Velká Británie – jih | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | Telehouse | Velká Británie – jih | IX dosažitelný, Equinix |
| **Los Angeles** | CoreSite | neuvedeno | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |Interxion | Francie – jih | DE-CIX, Interxion, Jaguar Network |
| **Melbourne** | NextDC | Austrálie – jihovýchod | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | neuvedeno | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | neuvedeno | Bell Canada, Cologix, Telus, Zayo |
| **Bombaj** | Tata Communications | Indie – západ | Global CloudXchange (GCX), Spoléhácí se na jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | Indie – západ | Airtel, Sify, Vodafone Idea |
| **New York** | Equinix | neuvedeno | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | Next Generation Data | Spojené království – západ | British Telecom, Colt, Level 3 Communications, data nové generace |
| **Ósaka** | Equinix | Japonsko – západ | Colt, Equinix, Internet Initiative Japonska Inc. – IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paříž** | Interxion | Francie – střed | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | neuvedeno | Megaport, NextDC |
| **Québec** | 4Degrees | Kanada – východ | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | Střed USA – jih | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Brazílie – jih | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | USA – západ 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Soul** | KINX | Jižní Korea – střed | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | Equinix | USA – západ | Aryaka Networks sítě, v & T NetBond, British Telecom, CenturyLink Cloud Connect, Comcastu, Coresite, Equinix, spolucloud, IX dosažitelný, Packet, PacketFabric, Level 3 Communications, Megaport, pomeranče, Sprint, Tata Communications, Telia, Verizon, Zayo |
| **Silicon Valley2** | Coresite | USA – západ | Coresite | 
| **Singapur** | Equinix | Jihovýchodní Asie | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | Globální přepínač | Jihovýchodní Asie | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stockholm** | Equinix | neuvedeno | Telia dopravce |
| **Sydney** | Equinix | Austrálie – východ | Equinix, v & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, oranžová, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **WAN** | Hlavní telekomunikační systémy | neuvedeno | Generální telekomunikace, FarEasTone |
| **Tokio** | Equinix | Japonsko – východ | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Kanada – střed | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington, D.C.** | Equinix | Východní USA Východní USA 2 | Aryaka Networks sítě, v & T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Comcastu, Coresite, Equinix, Internet2, spolucloud, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, pomeranče, PacketFabric, Sprint, Tata Komunikace, Telia dopravce, Verizon, Zayo |
| **Washington DC2** | Coresite | Východní USA Východní USA 2 |Coresite | 
| **Curych** | Interxion | neuvedeno | Intercloud, Interxion |

 **+** označuje brzké uvedení.

### <a name="national-cloud-environments"></a>Prostředí národních cloudů

### <a name="us-government-cloud"></a>Cloud vlády USA
| **Location** | **Poskytovatelé služeb** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | V & T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington, D.C.** |AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Čína
| **Location** | **Poskytovatelé služeb** |
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
| **Location** | **Výměna** | **Poskytovatelé připojení** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion,, Level 3 Communications, měst | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, mezinárodní most v Perském zálivu, Kalaam Telecom Bahrajn B. S. C, MainOne, Nianet, post, Proximus, ORS Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castle na koruna
| **Kapské město** | Teraco | MTN |
| **Chicago** | Equinix| Koruna Castle, spektrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | AXTEL, C3ntro Telecom, Cox Business, koruna Castle, data slévárenské, spektrum Enterprise, Transtelco |
| **Frankfurt** | Interxion, měst | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburk** | Equinix | Cinia |
| **Hongkong – zvláštní správní oblast** | Equinix | Hlavní Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londýn** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon – omezené, exponenciální E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zajn |
| **Los Angeles** | Equinix |Koruna Castle, spektrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, koruna Castle, spektrum Enterprise, Webair |
| **Paříž** | Equinix | Proximus |
| **Québec** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, spektrum Enterprise, Windstream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Technologie Beanfield Metroconnect, Cogeco partner 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington, D.C.** |Equinix | Altice Business, BICS, Cox Business, koruna Castle, GTT Communications Inc, Epsilon v oblasti krátké telekomunikace, Masergy, Windstream |

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
