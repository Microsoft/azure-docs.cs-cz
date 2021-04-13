---
title: 'Umístění a poskytovatelé připojení: Azure ExpressRoute | Dokumentace Microsoftu'
description: Tento článek obsahuje podrobný přehled o umístěních, kde jsou nabízené služby, a jak se připojit k oblastem Azure. Řazení je podle umístění.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 8dee8e8175c97faf4877a57ac6cea821964910dc
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313228"
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

## <a name="azure-regions"></a>Oblast Azure
Oblasti Azure jsou globální datová centra, kde se nacházejí výpočetní prostředky, sítě a prostředky úložiště Azure. Při vytváření prostředku Azure musí zákazník vybrat umístění prostředku. Umístění prostředku určuje, ve kterém datovém centru Azure (nebo zóně dostupnosti) se prostředek vytvoří.

## <a name="expressroute-locations"></a>Umístění ExpressRoute
Umístění ExpressRoute (někdy označovaná jako umístění partnerského vztahu nebo dodaná umístění) jsou zařízení ve společném umístění, kde se nachází zařízení Microsoft Enterprise Edge (MSEE). Umístění ExpressRoute jsou vstupním bodem sítě Microsoftu – a jsou globálně distribuované a poskytují zákazníkům možnost připojit se k síti Microsoftu po celém světě. Tato umístění tam, kde ExpressRoute partneři a ExpressRoute přímé zákazníky, vystavují vzájemné připojení k síti Microsoftu. Obecně platí, že umístění ExpressRoute nemusí odpovídat oblasti Azure. Zákazník může například vytvořit okruh ExpressRoute s umístěním prostředku *východní USA* v umístění partnerského vztahu pro *Seattle* .

Pokud jste připojení k aspoň jednomu umístění ExpressRoute v rámci geopolitické oblasti, budete mít přístup ke službám Azure napříč všemi oblastmi v rámci geopolitické oblasti. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Oblasti Azure pro ExpressRoute umístění v rámci geopolitické oblasti
Následující tabulka obsahuje mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Australská vláda** | Austrálie – střed, Austrálie – střed 2 |Canberra, Canberra2 |
| **Evropa** | Francie – střed, Francie – jih, Německo – sever, Německo – středozápad, Severní Evropa, Norsko – východ, Norsko – západ, Švýcarsko – sever, Švýcarsko – západ, Velká Británie – západ, Velká Británie – jih, Západní Evropa |Amsterdam, Amsterdam2, Berlín, Kodaň, Dublin, Frankfurt, Frankfurt2, Ženeva, Londýn, London2, Madrid, Marseille, Milán, Mnichov, Newport (Wales), Oslo, Paříž, Stavanger, Stockholm, Curych |
| **Severní Amerika** | USA – východ, USA – západ, USA – východ 2, USA – západ 2, USA – střed, USA – středojih, USA – středosever, USA – středozápad, Kanada – střed, Kanada – východ |Brno, Chicago, Praha, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montreal, New York, Phoenix, Quebec City, Queretaro (Mexiko), Quincy, San Antonio, Seattle, Silicon sedl, Silicon Valley2, Toronto, Toronto2, Vancouver, Washington DC, Washington DC2 |
| **Asie** | Východní Asie, Jihovýchodní Asie | Bangkok, Hongkong, Hongkong, Kong2, Jakarta, Kuala Lumpur, Singapur, Singapur2, Taipei |
| **Indie** | Indie – západ, Indie – střed, Indie – jih |Čennaj, Čennaj2, Bombaj, Bombaj2 |
| **Japonsko** | Japonsko – západ, Japonsko – východ |Ósaka, Tokio, Tokyo2 |
| **Oceánie** | Austrálie – jihovýchod, Austrálie – východ |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Jižní Korea** | Jižní Korea – střed, Jižní Korea – jih |Busan, Soul|
| **Spojené arabské emiráty** | Spojené arabské emiráty Central, Spojené arabské emiráty sever | Dubaj, Dubai2 |
| **Jižní Afrika** | Jižní Afrika – západ, Jižní Afrika – sever |Kapské město, Johannesburg |
| **Jižní Amerika** | Brazílie – jih |Bogotá, Rio de Janeiro, Svatý Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Oblasti Azure a geopolitické hranice pro národní cloudy
Následující tabulka obsahuje informace o oblastech a geopolitických hranicích pro národní cloudy.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Cloud vlády USA** |US Gov – Arizona, US Gov – Iowa, US Gov – Texas, US Gov – Virginie, US DoD – střed, US DoD – východ  |Brno, Chicago, Praha, New York, Phoenix, San Antonio, Seattle, Silicon sedl, Washington DC |
| **Čína – východ** |Čína – východ, Čína – východ 2 |Shanghai, Shanghai2 |
| **Čína – sever** |Čína – sever, Čína – sever 2 |Peking, Beijing2 |
| **Německo** |Střední Německo, Východní Německo |Berlín, Frankfurt |

Připojení přes geopolitické oblasti není pomocí standardní SKU pro ExpressRoute podporované. Aby bylo podporované globální připojení, budete muset povolit doplněk ExpressRoute Premium. Připojení k prostředím národních cloudů není podporované. Podle potřeby můžete spolupracovat se svým poskytovatelem připojení.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Poskytovatelé připojení ExpressRoute

Následující tabulka uvádí umístění připojení a poskytovatele služby pro každé umístění. Seznam poskytovatelů služeb a umístění, ve kterých působí, najdete v článku [Umístění podle poskytovatelů služeb](expressroute-locations.md).

* **Místní oblasti Azure** jsou ty, které [ExpressRoute místní](expressroute-faqs.md) v každém umístění partnerského vztahu mají přístup. není **k dispozici, znamená to** , že ExpressRoute Local není v tomto umístění partnerského vztahu k dispozici.

* **Zóna** odkazuje na [ceny](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Globální komerční Azure
| **Umístění** | **Adresa** | **Zóna** | **Místní oblasti Azure** | **ER Direct** | **Poskytovatelé služeb** |
| --- | --- | --- | --- | --- | --- |
| **Smlouv** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | West Europe | 10G, 100G | Aryaka Networks sítě, v&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, spolucloud, Interxion, KPN, IX dosažitelný, Level 3 Communications, Megaport, NTT Communications, oranžová, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | West Europe | 10G, 100G | Britské telekomunikace, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, euNetworks, GÉANT, Interxion, čísla, NTT globální datacentra EMEA, oranžová, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | Není k dispozici | 10G, 100G | Equinix, Megaport |
| **Auckland** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | Není k dispozici | 10G | Devoli, Kordia, Megaport, REANNZ, Spark NZ, Vocus Group NZ |
| **Bangkok** | [SYSTÉMEM](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | Není k dispozici | 10G | AIS, UIH |
| **Berlíně** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Německo – sever | 10G | Equinix, NTT globální datacentra EMEA|
| **Bogotá** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | Není k dispozici | 10G | Equinix |
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Jižní Korea – jih | Není k dispozici | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Austrálie – střed | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Austrálie – střed 2| 10G, 100G | CDC, Equinix |
| **Kapské město** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Jižní Afrika – západ | 10G | BCX, Internet Solutions – Cloud Connect, Liquid Telecom, teraco |
| **Čennaj** | Tata Communications | 2 | Indie – jih | 10G | BSNL, Global CloudXchange (GCX), SIFY, Tata Communications, VodafoneIdea |
| **Čennaj2** | Airtel | 2 | Indie – jih | 10G | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | USA – středosever | 10G, 100G | Aryaka Networks sítě, v&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcastu, Coresite, Equinix, spolucloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW, s globálním omezením, Sprint, Telia, Verizon, Zayo |
| **Kodaňských** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | Není k dispozici | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | Není k dispozici | 10G, 100G | Aryaka Networks sítě, v&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, PacketFabric, Telmex Uninet, Telia dopravce, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | USA – středozápad | Není k dispozici | CoreSite, Megaport, Zayo |
| **Dubaj** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Spojené arabské emiráty sever | Není k dispozici | Etisalat Spojené arabské emiráty |
| **Dubai2** | [du dataměna](http://datamena.com/solutions/data-centre) | 3 | Spojené arabské emiráty sever | Není k dispozici | DE-CIX, du dataměna, Equinix, Megaport, oranžová, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Severní Evropa | 10G, 100G | CenturyLink Cloud Connect, Colt, EIR, Equinix, GEANT, euNetworks, Interxion, Megaport |
| **Frankfurtu** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Německo – středozápad | 10G, 100G | V&T NetBond, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, euNetworks, GEANT, spolucloud, Interxion, Megaport, oranžová, Telia Carrier, T-Systems |
| **Frankfurt2** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | Německo – středozápad | 10G, 100G | Equinix |
| **Ženevě** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Švýcarsko – západ | 10G, 100G | Equinix, Megaport, Swisscom |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Východní Asie | 10G | Aryaka Networks sítě, British Telecom, CenturyLink Cloud Connect, generální Telecom, Čína Telecom Global, Čína UNICOM, Equinix, Cloud, Megaport, NTT Communications, oranžová, PCCW s globálním omezením, Tata Communications, Telia, Verizon |
| **Hongkong Kong2** | [MEGABAJTŮ-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Východní Asie | 10G | Čína – mobilní mezinárodní, Čína – Telecom celosvětový, iAdvantage, Megaport, PCCW globální omezení, SingTel |
| **Jakarta** | TELiN, Telkom Indonésie | 4 | Není k dispozici | 10G | Telin |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Jižní Afrika – sever | 10G | BCX, British Telecom, Internet Solutions – Cloud Connect, Liquid Telecom, oranžová, teraco |
| **Kuala Lumpur** | [DotCom cíle Menara na čas](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | Není k dispozici | Není k dispozici | TIME dotCom |
| **Las Vegas** | [Přepínač LV](https://www.switch.com/las-vegas) | 1 | Není k dispozici | 10G, 100G | CenturyLink Cloud Connect, Megaport, PacketFabric |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Spojené království – jih | 10G, 100G | V&T NetBond, British Telecom, Colt, Equinix, euNetworks, spolucloudu, Internet Solutions – Cloud Connect, Interxion, JISC, Level 3 Communications, Megaport, MTN, NTT Communications, pomeranče, PCCW, Tata, KDDI, Telenor |
| **London2** | [Pokojová USA – 2](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Spojené království – jih | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, GTT, IX dosáhnou, Equinix, Megaport, SES, Sohonet, House-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | Není k dispozici | 10G, 100G | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Angeles2 Los** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | Není k dispozici | 10G, 100G | Equinix |
| **Madrid** | [Interxion MAD1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | West Europe | 10G, 100G | Interxion |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Francie – jih | Není k dispozici | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Austrálie – jihovýchod | 10G, 100G | Equinix, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | Není k dispozici | 10G, 100G | Claro, C3ntro, Equinix, Megaport, sítě pro Neutrona |
| **Milán** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | Není k dispozici | 10G | Colt, Equinix, Fastweb, IRIDEOS, Retelit |
| **Minneapolis** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | Není k dispozici | 10G, 100G | Cologix, Megaport |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | Není k dispozici | 10G, 100G | Zvon Kanada, Cologix, Fibrenoire, Megaport, Telus, Zayo |
| **Bombaj** | Tata Communications | 2 | Západní Indie | 10G | BSNL, DE-CIX, Global CloudXchange (GCX), spoléhá jio, Sify, Tata Communications, Verizon |
| **Bombaj2** | Airtel | 2 | Západní Indie | 10G | Airtel, Sify, Vodafone Idea |
| **Mnichov** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | Není k dispozici | 10G | DE-CIX |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | Není k dispozici | 10G, 100G | CenturyLink Cloud Connect, Colt, Coresite, DE-CIX, Equinix, spolucloud, Megaport, Packet, Zayo |
| **Newport (Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Spojené království – západ | Není k dispozici | British Telecom, Colt, JISC, Level 3 Communications, Next data generace |
| **Ósaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japonsko – západ | 10G, 100G | V Brně, Colt, Equinix, Internet Initiative Japonska Inc. – IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank, Tokai Communications |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norsko – východ | 10G, 100G | GlobalConnect, Megaport, Telenor, Telia Carrier |
| **Paříž** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Francie – střed | 10G, 100G | Britské telekomunikace, CenturyLink Cloud Connect, Colt, Equinix, mezicloud, Interxion, Jaguar síť, Megaport, oranžová, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | Není k dispozici | 10G | Megaport, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | Není k dispozici | 10G, 100G | |
| **Québec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kanada – východ | 10G, 100G | Zvon Kanada, Megaport, Telus |
| **Queretaro (Mexiko)** | [KIO sítě QR01](https://www.kionetworks.com/es-mx/) | 4 | Není k dispozici | 10G | Transtelco|
| **Quincy** | [Sabey Datacenter – sestavení](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | Západní USA 2 | 10G, 100G | |
| **Rio de Janeiro** | [Equinix – RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | Brazílie – jihovýchod | 10G | Equinix |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Středojižní USA | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brazílie – jih | 10G, 100G | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Západní USA 2 | 10G, 100G | Aryaka Networks sítě, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Soul** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | Jižní Korea – střed | 10G, 100G | KINX, KT, LG CNS, Equinix, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA – západ | 10G, 100G | Aryaka Networks sítě, v&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcastu, Coresite, Equinix, spolucloud, Internet2, IX dosažitelný, Packet, PacketFabric, Level 3 Communications, Megaport, pomeranč, Sprint, Tata Communications, Telia, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA – západ | 10G, 100G | Colt, Coresite | 
| **Singapur** | [Equinix sz1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Southeast Asia | 10G, 100G | Aryaka Networks sítě, v&T NetBond, British Telecom, Čína Mobile International, Epsilon celosvětová komunikace, Equinix, spolucloudu, Level 3 Communications, Megaport, NTT Communications, pomeranče, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | [Globální přepínač – Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Southeast Asia | 10G, 100G | Čína UNICOM Global, Colt, Epsilon Global Communications, Megaport, PCCW Globaled, SingTel, House-KDDI |
| **Stavanger** | [Zelený horská oblast DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norsko – západ | 10G, 100G |GlobalConnect, Megaport |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | Není k dispozici | 10G | Equinix, Telia dopravce |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Austrálie – východ | 10G, 100G | Equinix, v&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, oranžová, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Austrálie – východ | 10G, 100G | Megaport, NextDC |
| **WAN** | Hlavní telekomunikační systémy | 2 | Není k dispozici | 10G | Generální telekomunikace, Chunghwa Telecom, FarEasTone |
| **Tokio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japan East | 10G, 100G | Aryaka Networks sítě, v&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japonsko Inc.-IIJ, Megaport, NTT Communications, NTT východní, oranžová, Softbank, Verizon |
| **Tokyo2** | [V BRNĚ](https://www.attokyo.com/) | 2 | Japan East | 10G, 100G | PŘI komunikaci Tokio, Megaport, Tokai |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Střední Kanada | 10G, 100G | V&T NetBond, Bell Kanadě, CenturyLink Cloud Connect, Cologix, Equinix, IX dosáhnou Megaport, Telus, Verizon, Zayo |
| **Toronto2** | [Příbuzná REIT](https://www.alliedreit.com/property/905-king-st-w/) | 1 | Střední Kanada | 10G, 100G | |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | Není k dispozici | 10G | Cologix, Megaport, Telus |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Východní USA Východní USA 2 | 10G, 100G | Aryaka Networks sítě, v&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcastu, Coresite, Equinix, Internet2, spolucloud, železo, hory, Megaport, Neutrona, NTT, PacketFabric,, Tata, Telia, Verizon, SES, Sprint, Zayo Communications, Communications,, |
| **Washington DC2** | [Coresite Restonu](https://www.coresite.com/data-center-locations/northern-virginia-washington-dc) | 1 | Východní USA Východní USA 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Megaport, Viasat, Zayo | 
| **Curych** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Švýcarsko – sever | 10G, 100G | Equinix, spolucloud, Interxion, Megaport, Swisscom |

 **+** označuje, že už brzy

### <a name="national-cloud-environments"></a>Prostředí národních cloudů

Národní cloudy Azure jsou oddělené od sebe navzájem i z celosvětově komerčních Azure. ExpressRoute pro jeden cloud Azure se nemůže připojit k oblastem Azure v ostatních.

### <a name="us-government-cloud"></a>Cloud vlády USA
| **Umístění** | **Adresa** | **Místní oblasti Azure**| **ER Direct** | **Poskytovatelé služeb** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | Není k dispozici | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | Není k dispozici | 10G, 100G | V&T NetBond, British Telecom, Equinix, Communications Level 3, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | Není k dispozici | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | Není k dispozici | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | USA (Gov) – Arizona | 10G, 100G | V&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | USA (Gov) – Texas | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | Není k dispozici | 10G, 100G | V&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | Není k dispozici | 10G, 100G | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD – východ US Gov – Virginie | 10G, 100G | V&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Čína
| **Umístění** | **Poskytovatelé služeb** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | Čína Telecom, Čína UNICOM, GDS |
| **Šanghaj** |China Telecom |
| **Shanghai2** | Čína Telecom, Čína UNICOM, GDS |

Další informace najdete v tématu [ExpressRoute v Číně](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Německo
| **Umístění** | **Poskytovatelé služeb** |
| --- | --- |
| **Berlíně** |e-shelter, Megaport+, T-Systems |
| **Frankfurtu** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Připojení prostřednictvím poskytovatelů Exchange
Pokud poskytovatel připojení není v předchozích částech uvedený, můžete přesto vytvořit připojení.

* Zkontrolujte u svého poskytovatele připojení, jestli je připojený k některé z výměn v předchozí tabulce. Můžete zkontrolovat následující odkazy, abyste získali další informace o službách nabízených poskytovateli výměny. Několik poskytovatelů připojení je už připojeno k ethernetovým výměnám.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
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
Pokud jste vzdáleně a nemáte připojení k vláknu nebo chcete prozkoumat další možnosti připojení, můžete následující satelitní operátory ověřit. 

* Intelsat
* [ŠETŘENÍ](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Připojení prostřednictvím dalších poskytovatelů služeb
| **Umístění** | **Exchange** | **Poskytovatelé připojení** |
| --- | --- | --- |
| **Smlouv** | Komunikace Equinix, Interxion, Level 3 | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, mezinárodní most v Perském zálivu, Kalaam Telecom Bahrajn B. S. C, MainOne, Nianet, POST Telecom Lucemburska, Proximus, RETN, ORS Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castle na koruna
| **Kapské město** | Teraco | MTN |
| **Čennaj** | Tata Communications | Tata služby |
| **Chicago** | Equinix| Koruna Castle, spektrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | AXTEL, C3ntro Telecom, Cox Business, koruna Castle, data slévárenské, spektrum Enterprise, Transtelco |
| **Frankfurtu** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong – zvláštní administrativní oblast** | Equinix | Hlavní Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon – omezené, exponenciální E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zajn |
| **Los Angeles** | Equinix |Koruna Castle, spektrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Technologie pro kobránu, Inc. APTUM, Rogers, Zirro |
| **Bombaj** | Tata Communications | Tata služby |
| **New York** |Equinix, Megaport | Altice Business, koruna Castle, spektrum Enterprise, Webair |
| **Paříž** | Equinix | Proximus |
| **Québec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, spektrum Enterprise, Windstream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon – omezené, LGA Telecom, USA – USA (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Technologie Beanfield Metroconnect, APTUM Technologies, IVedha Inc, Core Cloud Services Inc., Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, koruna Castle, GTT Communications Inc, Epsilon v oblasti krátké telekomunikace, Masergy, Windstream |

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
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa umístění"
