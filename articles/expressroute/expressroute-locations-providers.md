---
title: 'Umístění a poskytovatelé připojení: Azure ExpressRoute | Dokumentace Microsoftu'
description: Tento článek obsahuje podrobný přehled o umístěních, kde jsou nabízené služby, a jak se připojit k oblastem Azure. Řazení je podle umístění.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: cherylmc
ms.openlocfilehash: 71a0d18669ea2c5fc9e0548e195c3df0145f6cb0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967792"
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

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Australská vláda** | Austrálie – střed, Austrálie – střed 2 |Canberra, Canberra2 |
| **Evropa** | Francie – střed, Francie – jih, Severní Evropa, Západní Evropa, Velká Británie – západ, Velká Británie – jih |Amsterdam, Amsterdam2, Kodaň, Dublin, Frankfurt, Ženeva, Londýn, London2, Marseille, Milán, Mnichov, Newport (Wales), Oslo, Paříž, Stavanger, Stockholm, Curych, Mnichov |
| **Severní Amerika** | Východní USA, Západní USA, Východní USA 2, Západní USA 2, Střední USA, Středojižní USA, Středoseverní USA, Středozápadní USA, Střední Kanada, Východní Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asie** | Východní Asie, Jihovýchodní Asie |Hongkong – zvláštní správní oblast, Jakarta, Kuala Lumpur, Singapur, Singapur2, Taipei |
| **Indie** | Indie – západ, Indie – střed, Indie – jih |Čennaj, Čennaj2, Bombaj, Bombaj2 |
| **Japonsko** | Japonsko – západ, Japonsko – východ |Ósaka, Tokio |
| **Oceánie a** | Austrálie – jihovýchod, Austrálie – východ |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Jižní Korea** | Jižní Korea – střed, Jižní Korea – jih |Busan, Soul|
| **Spojené arabské emiráty** | Spojené arabské emiráty Central, Spojené arabské emiráty sever | Dubaj, Dubai2 |
| **Jižní Afrika** | Jižní Afrika – západ, Jižní Afrika – sever |Kapské město, Johannesburg |
| **Jižní Amerika** | Brazílie – jih |Sao Paulo |

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

* **Místní oblasti Azure** jsou ty, které [ExpressRoute místní](expressroute-faqs.md) v každém umístění partnerského vztahu mají přístup. není **k dispozici, znamená to** , že ExpressRoute Local není v tomto umístění partnerského vztahu k dispozici.

* **Zóna** odkazuje na [ceny](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Globální komerční Azure
| **Umístění** | **Adresa** | **Zóny** | **Místní oblasti Azure** | **ER Direct** | **Poskytovatelé služeb** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1\. místo | Západní Evropa | 10G, 100G | Aryaka Networks sítě, v & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, spolucloudu, Interxion, KPN, IX dosáhnou, Level 3 Communications, Megaport, NTT Communications, oranžová, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1\. místo | Západní Evropa | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, oranžová, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1\. místo | – | – | Equinix, Megaport |
| **Auckland** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | – | 10G | Devoli, Kordia, Megaport, Spark NZ, Vocus skupina NZ |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Korea – jih | – | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1\. místo | Austrálie – střed | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1\. místo | Austrálie – střed 2| 10G, 100G | CDC |
| **Kapské město** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Jižní Afrika – západ | 10G | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Čennaj** | Tata Communications | 2 | Jižní Indie | 10G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Čennaj2** | Airtel | 2 | Jižní Indie | – | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1\. místo | Středoseverní USA | 10G, 100G | Aryaka Networks sítě, v & T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcastu, Coresite, Equinix, spolucloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Globaled, Sprint, Telia dopravce, Verizon, Zayo |
| **Kodaňských** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1\. místo | – | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1\. místo | – | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1\. místo | Středozápadní USA | – | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Spojené arabské emiráty – sever | – | Etisalat Spojené arabské emiráty |
| **Dubai2** | [du dataměna](http://datamena.com/solutions/data-centre) | 3 | Spojené arabské emiráty – sever | – | du dataměna, Megaport, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1\. místo | Severní Evropa | 10G, 100G | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1\. místo | Německo – středozápad | 10G, 100G | Colt, DE-CIX, GEANT, Interxion, oranžová, Telia Carrier |
| **Ženevě** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1\. místo | Švýcarsko – západ | 10G, 100G | |
| **Hongkong – zvláštní správní oblast** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Východní Asie | – | Aryaka Networks sítě, British Telecom, CenturyLink Cloud Connect, generální Telecom, Čína Telecom Global, Equinix, Cloud, Megaport, NTT Communications, oranžová, PCCW s globálním omezením, Tata Communications, Telia Carrier, Verizon |
| **Jakarta** | Telkom Indonésie | 4 | – | 10G | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Jižní Afrika – sever | – | British Telecom, Internet Solutions – Cloud Connect, Liquid Telecom, oranžová, teraco |
| **Kuala Lumpur** | [DotCom cíle Menara na čas](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | – | – | TIME dotCom |
| **Las Vegas** | [Přepínač LV](https://www.switch.com/las-vegas) | 1\. místo | – | – | CenturyLink Cloud Connect, Megaport |
| **Londýn** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1\. místo | Velká Británie – jih | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Pokojová USA – 2](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 1\. místo | Velká Británie – jih | 10G, 100G | Colt, IX dosah, Equinix, Megaport, House-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1\. místo | – | – | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Transtelco, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1\. místo | Francie – jih | – | DE-CIX, GEANT, Interxion, Jaguar síť |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Austrálie – jihovýchod | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1\. místo | – | 10G | C3ntro, Equinix, Megaport, Neutrona sítě |
| **Milán** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1\. místo | – | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1\. místo | – | – | Zvon Kanada, Cologix, Megaport, Telus, Zayo |
| **Bombaj** | Tata Communications | 2 | Západní Indie | – | Global CloudXchange (GCX), Spoléhácí se na jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Západní Indie | – | Airtel, Sify, Vodafone Idea |
| **Mnichov** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1\. místo | – | 10G, 100G | |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1\. místo | – | – | CenturyLink Cloud Connect, Colt, Coresite, Equinix, spolucloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | [Brána firewall příští generace](https://www.nextgenerationdata.co.uk) | 1\. místo | Velká Británie – západ | – | British Telecom, Colt, Level 3 Communications, data nové generace |
| **Ósaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japonsko – západ | – | Colt, Equinix, Internet Initiative Japonska Inc. – IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1\. místo | Norsko – východ | 10G, 100G | Telenor |
| **Paříž** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1\. místo | Francie – střed | – | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | – | 10G | Megaport, NextDC |
| **Québec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1\. místo | Kanada – východ | – | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1\. místo | Středojižní USA | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brazílie – jih | – | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1\. místo | Západní USA 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Soul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Korea – střed | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1\. místo | Západní USA | 10G, 100G | Aryaka Networks sítě, v & T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcastu, Coresite, Equinix, spolucloud, Internet2, IX dosažitelný, Packet, PacketFabric, Level 3 Communications, Megaport, pomeranče, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1\. místo | Západní USA | 10G, 100G | Colt, Coresite | 
| **Singapur** | [Equinix sz1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Jihovýchodní Asie | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | [Globální přepínač – Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Jihovýchodní Asie | 10G, 100G | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Zelený horská oblast DC1](https://greenmountain.no/dc1-stavanger/) | 1\. místo | – | 10G, 100G | |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1\. místo | – | 10G | Equinix, Telia dopravce |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Austrálie – východ | 10G, 100G | Equinix, v & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, oranžová, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Austrálie – východ | 10G, 100G | NextDC |
| **WAN** | Hlavní telekomunikační systémy | 2 | – | 10G | Generální telekomunikace, FarEasTone |
| **Tokio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japonsko – východ | 10G, 100G | Aryaka Networks sítě, v & T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japonsko Inc.-IIJ, Megaport, NTT Communications, NTT východní, oranžová, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1\. místo | Kanada – střed | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington, D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1\. místo | Východní USA Východní USA 2 | 10G, 100G | Aryaka Networks sítě, v & T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Comcastu, Coresite, Equinix, Internet2, spolucloud, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, pomeranče, PacketFabric, SES, Sprint, Tata Komunikace, Telia dopravce, Verizon, Zayo |
| **Washington DC2** | [Coresite Restonu](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1\. místo | Východní USA Východní USA 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Viasat, Zayo | 
| **Curych** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1\. místo | – | – | Spolucloud, Interxion, Megaport, Swisscom |

 **+** označuje brzké uvedení.

### <a name="national-cloud-environments"></a>Prostředí národních cloudů

Národní cloudy Azure jsou oddělené od sebe navzájem a z globálního Commerical Azure. ExpressRoute pro jeden cloud Azure se nemůže připojit k oblastem Azure v ostatních.

### <a name="us-government-cloud"></a>Cloud vlády USA
| **Umístění** | **Adresa** | **Místní oblasti Azure**| **ER Direct** | **Poskytovatelé služeb** |
| --- | --- | --- | --- | --- |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | – | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | – | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | – | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov – Arizona | – | V & T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov – Texas | – | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | – | 10G, 100G | Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | – | – | Equinix, Megaport |
| **Washington, D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD – východ US Gov – Virginie | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

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

## <a name="c1partners"></a>Připojení prostřednictvím poskytovatelů Exchange
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

## <a name="connectivity-through-satellite-operators"></a>Konektivita prostřednictvím satelitních operátorů
Pokud jste vzdáleně a nemáte připojení k vláknu nebo chcete prozkoumat další možnosti připojení, můžete následující satelitní operátory ověřit. 

* Intelsat
* [ŠETŘENÍ](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Připojení prostřednictvím dalších poskytovatelů služeb
| **Umístění** | **Výměna** | **Poskytovatelé připojení** |
| --- | --- | --- |
| **Amsterdam** | Komunikace Equinix, Interxion, Level 3 | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, mezinárodní most v Perském zálivu, Kalaam Telecom Bahrajn B. S. C, MainOne, Nianet, POST Telecom Lucemburska, Proximus, ORS Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castle na koruna
| **Kapské město** | Teraco | MTN |
| **Chicago** | Equinix| Koruna Castle, spektrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | AXTEL, C3ntro Telecom, Cox Business, koruna Castle, data slévárenské, spektrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburk** | Equinix | Cinia |
| **Hongkong – zvláštní správní oblast** | Equinix | Hlavní Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londýn** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon – omezené, exponenciální E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zajn |
| **Los Angeles** | Equinix |Koruna Castle, spektrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Technologie pro kobránu, Inc. APTUM, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, koruna Castle, spektrum Enterprise, Webair |
| **Paříž** | Equinix | Proximus |
| **Québec** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, spektrum Enterprise, Windstream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon – omezené, LGA Telecom, USA – USA (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Technologie Beanfield Metroconnect, APTUM Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
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
## <a name="next-steps"></a>Další kroky
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa umístění"
