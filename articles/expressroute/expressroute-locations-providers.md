---
title: 'Umístění a poskytovatelé připojení: Azure ExpressRoute | Dokumentace Microsoftu'
description: Tento článek obsahuje podrobný přehled o umístěních, kde jsou nabízené služby, a jak se připojit k oblastem Azure. Řazení je podle umístění.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: cherylmc
ms.openlocfilehash: 3b45c20e2d3e8829913c9e566516f85866ee80d4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390323"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partneři ExpressRoute a umístění partnerského vztahu

> [!div class="op_single_selector"]
> * [Umístění podle zprostředkovatele](expressroute-locations.md)
> * [Zprostředkovatelé podle umístění](expressroute-locations-providers.md)


Tabulky v tomto článku poskytují informace o geografickém pokrytí a umístěníexpressroute, zprostředkovateli připojení ExpressRoute a systémových integrátorech ExpressRoute (SIs).

> [!Note]
> Oblasti Azure a umístění ExpressRoute jsou dva odlišné a odlišné koncepty, pochopení rozdílu mezi těmito dvěma je důležité pro zkoumání připojení k hybridní montovny Azure. 
>
>

## <a name="azure-regions"></a>Oblast Azure
Oblasti Azure jsou globální datová centra, kde se nacházejí výpočetní prostředky Azure, síťové prostředky a prostředky úložiště. Při vytváření prostředku Azure musí zákazník vybrat umístění prostředků. Umístění prostředků určuje, ve kterém datovém centru Azure (nebo v zóně dostupnosti) se prostředek vytvoří.

## <a name="expressroute-locations"></a>Umístění ExpressRoute
Umístění ExpressRoute (někdy označované jako umístění partnerského vztahu nebo seznamovací zařízení) jsou zařízení společného umístění, kde jsou umístěna zařízení Microsoft Enterprise edge (MSEE). Umístění ExpressRoute jsou vstupním bodem do sítě společnosti Microsoft – a jsou globálně distribuována, což zákazníkům poskytuje možnost připojit se k síti společnosti Microsoft po celém světě. Tato umístění jsou místa, kde partneři ExpressRoute a zákazníci ExpressRoute Direct vydávají křížová připojení k síti společnosti Microsoft. Obecně umístění ExpressRoute nemusí odpovídat oblasti Azure. Zákazník může například vytvořit okruh ExpressRoute s umístěním prostředku *– východ USA*v umístění partnerského vztahu v *Seattlu.*

Pokud jste připojení k aspoň jednomu umístění ExpressRoute v rámci geopolitické oblasti, budete mít přístup ke službám Azure napříč všemi oblastmi v rámci geopolitické oblasti. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Oblasti Azure na expressroute umístění v rámci geopolitické oblasti
Následující tabulka obsahuje mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Australská vláda** | Austrálie – střed, Austrálie – střed 2 |Canberra, Canberra2 |
| **Evropa** | Francie – střed, Francie – jih, Německo – sever, Německo – střed, severní Evropa, Norsko – východ, Norsko – západ, Švýcarsko – západ, Velká Británie – západ, Velká Británie – jih, Západní Evropa |Amsterdam, Amsterdam2, Kodaň, Dublin, Frankfurt, Ženeva, Londýn2, Marseille, Milán, Mnichov, Newport (Wales), Oslo, Paříž, Stavanger, Stockholm, Curych, Mnichov |
| **Severní Amerika** | USA – východ, USA – západ, USA – východ 2, USA – západ 2, USA – střed, USA – středojih, USA – středosever, USA – středozápad, Kanada – střed, Kanada – východ |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Asie** | Východní Asie, Jihovýchodní Asie | Bangkok, Hongkong, Hongkong2, Jakarta, Kuala Lumpur, Singapur, Singapur2, Tchaj-pej |
| **Indie** | Indie – západ, Indie – střed, Indie – jih |Čennaj, Čennaj2, Bombaj, Bombaj2 |
| **Japonsko** | Japonsko – západ, Japonsko – východ |Osaka, Tokio, Tokio2 |
| **Oceánie** | Austrálie – jihovýchod, Austrálie – východ |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Jižní Korea** | Jižní Korea – střed, Jižní Korea – jih |Busan, Soul|
| **Spojené arabské emiráty** | SAE Central, SAE Sever | Dubaj, Dubaj2 |
| **Jižní Afrika** | Jižní Afrika – západ, Jižní Afrika – sever |Kapské město, Johannesburg |
| **Jižní Amerika** | Brazílie – jih |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Oblasti Azure a geopolitické hranice pro národní cloudy
Následující tabulka obsahuje informace o oblastech a geopolitických hranicích pro národní cloudy.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Cloud vlády USA** |US Gov – Arizona, US Gov – Iowa, US Gov – Texas, US Gov – Virginie, US DoD – střed, US DoD – východ  |Atlanta,Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Čína – východ** |Čína – východ, Čína – východ 2 |Šanghaj, Šanghaj2 |
| **Čína – sever** |Čína – sever, Čína – sever 2 |Peking, Peking2 |
| **Německo** |Střední Německo, Východní Německo |Berlín, Frankfurt |

Připojení přes geopolitické oblasti není pomocí standardní SKU pro ExpressRoute podporované. Aby bylo podporované globální připojení, budete muset povolit doplněk ExpressRoute Premium. Připojení k prostředím národních cloudů není podporované. Podle potřeby můžete spolupracovat se svým poskytovatelem připojení.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Poskytovatelé připojení ExpressRoute

Následující tabulka uvádí umístění připojení a poskytovatele služby pro každé umístění. Seznam poskytovatelů služeb a umístění, ve kterých působí, najdete v článku [Umístění podle poskytovatelů služeb](expressroute-locations.md).

* **Místní oblasti Azure** jsou ty, které [ExpressRoute Místní](expressroute-faqs.md) v každém umístění partnerského vztahu přístup. **n/a** označuje, že ExpressRoute Local není k dispozici v tomto umístění partnerského vztahu.

* **Zóna** odkazuje na [ceny](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Globální komerční Azure
| **Umístění** | **Adresa** | **Zóny** | **Místní oblasti Azure** | **ER Přímé** | **Poskytovatelé služeb** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Západní Evropa | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Západní Evropa | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | neuvedeno | neuvedeno | Equinix, Megaport |
| **Auckland** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | neuvedeno | 10 g | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Bangkok** | [Ais](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | neuvedeno | 10 g | Ais |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Jižní Korea – jih | neuvedeno | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrálie – střed | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrálie – střed 2| 10G, 100G | CDC |
| **Kapské město** | [Teraco ČT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Jižní Afrika – západ | 10 g | BCX, Internet Oválná řešení - Cloud Connect, Liquid Telecom, Teraco |
| **Čennaj** | Tata Communications | 2 | Indie – jih | 10 g | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Čennaj2** | Airtel | 2 | Indie – jih | neuvedeno | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | USA – středosever | 10G, 100G | Aryaka Networks, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Kodani** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | neuvedeno | 10 g | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | neuvedeno | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | USA – středozápad | neuvedeno | CoreSite, Megaport, Zayo |
| **Dubaj** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | SAE Sever | neuvedeno | Etisalat SAE |
| **Dubaj2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | SAE Sever | neuvedeno | du datamena, Megaport, Oranžová, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Severní Evropa | 10G, 100G | Colt, eir, Equinix, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Německo – západ – střed | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, GEANT, Interxion, Megaport, Orange, Telia Carrier |
| **Ženeva** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Švýcarsko Západ | 10G, 100G | Equinix, Megaport |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Východní Asie | neuvedeno | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hongkong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | neuvedeno | 10 g | |
| **Jakarta** | Telkom Indonésie | 4 | neuvedeno | 10 g | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Jižní Afrika – sever | 10 g | British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara CÍLE](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | neuvedeno | neuvedeno | TIME dotCom |
| **Las Vegas** | [Přepnout LV](https://www.switch.com/las-vegas) | 1 | neuvedeno | neuvedeno | CenturyLink Cloud Connect, Megaport |
| **Londýn** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Spojené království – jih | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Londýn2** | [Telehouse Sever Dva](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Spojené království – jih | 10G, 100G | CenturyLink Cloud Connect, Colt, IX Reach, Equinix, Megaport, Telehouse - KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | neuvedeno | neuvedeno | CoreSite, Equinix, Megaport, Neutrona sítě, NTT, Transtelco, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Francie – jih | neuvedeno | DE-CIX, GEANT, Interxion, Síť Jaguar, Ooredoo Cloud Connect |
| **Melbourne** | [Další dc M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Austrálie – jihovýchod | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | neuvedeno | 10 g | C3ntro, Equinix, Megaport, Neutrona sítě |
| **Milan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | neuvedeno | 10 g | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | neuvedeno | 10G, 100G | Bell Kanada, Cologix, Megaport, Telus, Zayo |
| **Bombaj** | Tata Communications | 2 | Indie – západ | neuvedeno | Globální CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Bombaj2** | Airtel | 2 | Indie – západ | neuvedeno | Airtel, Sify, Vodafone Idea |
| **Mnichov** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | neuvedeno | 10G, 100G | |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | neuvedeno | neuvedeno | CenturyLink Cloud Connect, Colt, Coresite, Equinix, InterCloud, Megaport, Paket, Zayo |
| **Newport (Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Spojené království – západ | neuvedeno | British Telecom, Colt, Level 3 Komunikace, Nová generace dat |
| **Ósaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japonsko – západ | 10G, 100G | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norsko východ | 10G, 100G | Megaport, Telenor, Telia Carrier |
| **Paříž** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Francie – střed | 10G, 100G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Oranžová, Telia Carrier, Zayo |
| **Perth** | [Další řadič domény P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | neuvedeno | 10 g | Megaport, Další DC |
| **Québec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kanada – východ | neuvedeno | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | USA – středojih | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brazílie – jih | neuvedeno | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | USA – západ 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Komunikace, Megaport, Telus, Zayo |
| **Soul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Jižní Korea – střed | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA – západ | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA – západ | 10G, 100G | Colt, Coresite | 
| **Singapur** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Jihovýchodní Asie | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | [Globální přepínač Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Jihovýchodní Asie | 10G, 100G | Čína Unicom Global, Colt, Epsilon Global Communications, Megaport, PCCW Global Limited, SingTel |
| **Stavanger** | [Zelená hora DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norsko Západ | 10G, 100G | |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | neuvedeno | 10 g | Equinix, Nosič Telia |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Austrálie – východ | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [Další řadič domény S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Austrálie – východ | 10G, 100G | Megaport, Další DC |
| **Taipei** | Hlavní telekomunikační | 2 | neuvedeno | 10 g | Hlavní telecom, FarEasTone |
| **Tokio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japonsko – východ | 10G, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokio2** | [VE SPOLEČNOSTI TOKYO](https://www.attokyo.com/) | 2 | Japonsko – východ | 10G, 100G | VE SPOLEČNOSTI TOKYO |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Střední Kanada | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | Cologix | 10G, 100G | |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Východní USA, Východní USA 2 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Východní USA, Východní USA 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Viasat, Zayo | 
| **Curychu** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Švýcarsko Sever | 10G, 100G | Intercloud, Interxion, Megaport, Swisscom |

 **+** označuje již brzy

### <a name="national-cloud-environments"></a>Prostředí národních cloudů

Národní cloudy Azure jsou izolované od sebe navzájem a od globální komerční Azure. ExpressRoute pro jeden cloud Azure se nemůže připojit k oblastem Azure v ostatních oblastech.

### <a name="us-government-cloud"></a>Cloud vlády USA
| **Umístění** | **Adresa** | **Místní oblasti Azure**| **ER Přímé** | **Poskytovatelé služeb** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | neuvedeno | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | neuvedeno | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | neuvedeno | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | neuvedeno | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | USA (Gov) – Arizona | neuvedeno | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | USA (Gov) – Texas | neuvedeno | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | neuvedeno | 10G, 100G | VE SPOLEČNOSTI&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | neuvedeno | neuvedeno | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD East, Americká vláda Virginie | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Čína
| **Umístění** | **Poskytovatelé služeb** |
| --- | --- |
| **Peking** |China Telecom |
| **Peking2** | China Telecom, China Unicom, GDS |
| **Šanghaj** |China Telecom |
| **Šanghaj2** | China Telecom, GDS |

Další informace najdete v tématu [ExpressRoute v Číně](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Německo
| **Umístění** | **Poskytovatelé služeb** |
| --- | --- |
| **Berlín** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Připojení prostřednictvím poskytovatelů exchange
Pokud poskytovatel připojení není v předchozích částech uvedený, můžete přesto vytvořit připojení.

* Zkontrolujte u svého poskytovatele připojení, jestli je připojený k některé z výměn v předchozí tabulce. Můžete zkontrolovat následující odkazy, abyste získali další informace o službách nabízených poskytovateli výměny. Několik poskytovatelů připojení je už připojeno k ethernetovým výměnám.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Rozšířil váš poskytovatel připojení vaši síť ke zvolenému umístění partnerského vztahu?
  * Ujistěte se, že poskytovatel připojení rozšiřuje vaše připojení vysoce dostupným způsobem tak, aby neexistovaly žádné jediné body selhání.
* Abyste se připojili k Microsoftu, objednejte si okruh ExpressRoute s výměnou jako poskytovatel připojení.
  * Při nastavení připojení postupujte podle kroků v tématu [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md).

## <a name="connectivity-through-satellite-operators"></a>Konektivita prostřednictvím satelitních operátorů
Pokud jste vzdálení a nemáte připojení optických vláken nebo chcete prozkoumat další možnosti připojení, můžete zkontrolovat následující satelitní operátory. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Připojení prostřednictvím poskytovatelů dalších služeb
| **Umístění** | **Exchange** | **Poskytovatelé připojení** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Úroveň 3 Komunikace | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrajn B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Korunní hrad
| **Kapské město** | Teraco | MTN |
| **Chicago** | Equinix| Korunní Hrad, Spectrum Enterprise, Větrovka |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Korunní hrad, Slévárna dat, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong – zvláštní správní oblast** | Equinix | Vedoucí, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londýn** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponenciální E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Korunní hrad, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Korunní hrad, Spectrum Enterprise, Webair |
| **Paříž** | Equinix | Proximus |
| **Québec** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Větrná energie, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Systémoví integrátoři ExpressRoute
Povolení soukromého připojení podle vlastních potřeb může být náročné, v závislosti na rozsahu vaší sítě. Můžete spolupracovat s kterýmkoli systémovým integrátorem uvedeným v následující tabulce, aby vám pomohl s připojením k ExpressRoute.

| **Kontinent** | **Systémoví integrátoři** |
| --- | --- |
| **Asie** |Avanade Inc., OneAs1a |
| **Austrálie** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Evropa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Severní Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Jižní Amerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Další kroky
* Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa umístění"
