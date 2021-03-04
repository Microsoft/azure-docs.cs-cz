---
title: 'Poskytovatelé připojení a umístění: Azure ExpressRoute | Dokumentace Microsoftu'
description: Tento článek obsahuje podrobný přehled o umístěních, kde jsou nabízené služby, a jak se připojit k oblastem Azure. Řazení je podle poskytovatele připojení.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 5fedb9d09e61540d0ee8a1fe21fbd25f31390f5d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742445"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>Partneři připojení ExpressRoute a umístění peeringu

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

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.
Následující tabulka obsahuje mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Australská vláda** |Austrálie – střed, Austrálie – střed 2 |Canberra, Canberra2 |
| **Evropa** | Francie – střed, Francie – jih, Německo – sever, Německo – středozápad, Severní Evropa, Norsko – východ, Norsko – západ, Švýcarsko – sever, Švýcarsko – západ, Velká Británie – západ, Velká Británie – jih, Západní Evropa |Amsterdam, Amsterdam2, Berlín, Kodaň, Dublin, Frankfurt, Frankfurt2, Ženeva, Londýn, London2, Madrid, Marseille, Milán, Mnichov, Newport (Wales), Oslo, Paříž, Stavanger, Stockholm, Curych |
| **Severní Amerika** |USA – východ, USA – západ, USA – východ 2, USA – západ 2, USA – střed, USA – středojih, USA – středosever, USA – středozápad, Kanada – střed, Kanada – východ |Brno, Chicago, Praha, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montreal, New York, Phoenix, Quebec City, Queretaro (Mexiko), Quincy, San Antonio, Seattle, Silicon sedl, Silicon Valley2, Toronto, Toronto2, Vancouver, Washington DC, Washington DC2 |
| **Asie** | Východní Asie, Jihovýchodní Asie | Bangkok, Hongkong, Hongkong, Kong2, Jakarta, Kuala Lumpur, Singapur, Singapur2, Taipei |
| **Indie** | Indie – západ, Indie – střed, Indie – jih |Čennaj, Čennaj2, Bombaj, Bombaj2 |
| **Japonsko** | Japonsko – západ, Japonsko – východ |Ósaka, Tokio, Tokyo2 |
| **Oceánie** | Austrálie – jihovýchod, Austrálie – východ |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Jižní Korea** | Jižní Korea – střed, Jižní Korea – jih |Busan, Soul|
| **Spojené arabské emiráty** | Spojené arabské emiráty Central, Spojené arabské emiráty sever | Dubaj, Dubai2 |
| **Jižní Afrika** | Jižní Afrika – západ, Jižní Afrika – sever |Kapské město, Johannesburg |
| **Jižní Amerika** | Brazílie – jih |Bogotá, Rio de Janeiro, Svatý Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Oblasti a geopolitické hranice pro národní cloudy
Následující tabulka obsahuje informace o oblastech a geopolitických hranicích pro národní cloudy.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Cloud vlády USA** |US Gov – Arizona, US Gov – Iowa, US Gov – Texas, US Gov – Virginie, US DoD – střed, US DoD – východ  |Brno, Chicago, Praha, New York, Phoenix, San Antonio, Seattle, Silicon sedl, Washington DC |
| **Čína – východ** |Čína – východ, Čína – východ 2 |Shanghai, Shanghai2 |
| **Čína – sever** |Čína – sever, Čína – sever 2 |Peking, Beijing2 |
| **Německo** |Střední Německo, Východní Německo |Berlín, Frankfurt |

Připojení přes geopolitické oblasti není pomocí standardní SKU pro ExpressRoute podporované. Aby bylo podporované globální připojení, budete muset povolit doplněk ExpressRoute Premium. Připojení k prostředím národních cloudů není podporované. Podle potřeby můžete spolupracovat se svým poskytovatelem připojení.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Poskytovatelé připojení ExpressRoute

Následující tabulka uvádí umístění podle poskytovatele služby. Pokud chcete zobrazit dostupné poskytovatele podle umístění, přečtěte si téma [Poskytovatelé služeb podle umístění](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Globální komerční Azure

| **Poskytovatel služeb** | **Microsoft Azure** | **Microsoft 365**  | **Umístění** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Podporováno |Podporováno |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Podporováno | Podporováno | Čennaj2, Bombaj2 |
| **[SYSTÉMEM](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Podporováno | Podporováno | Bangkok |
| **[Sítě Aryaka](https://www.aryaka.com/)** |Podporováno |Podporováno |Amsterdam, Chicago, Praha, Hongkong – zvláštní správní oblast, Svatý Paulo, Seattle, Silicon sedla, Singapur, Tokio, Washington DC |
| **[Datová centra Ascenty](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Podporováno |Podporováno |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Podporováno |Podporováno |Amsterdam, Chicago, Praha, Frankfurt, Londýn, Silicon sedlo, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **[V BRNĚ](https://www.attokyo.com/connectivity/azure.html)** | Podporováno | Podporováno | Ósaka, Tokyo2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Podporováno | Podporováno | Tokio |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Podporováno |Podporováno |Kapské město, Johannesburg|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Podporováno |Podporováno |Montreal, Toronto, Quebec City |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Chicago, Hongkong – zvláštní správní oblast, Johannesburg, Londýn, London2, Newport (Wales), Paříž, Svatý Paulo, Silicon sedl, Singapur, Sydney, Tokio, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Podporováno |Podporováno |Miami |
| **CDC** | Podporováno | Podporováno | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Podporováno |Podporováno |Amsterdam2, Chicago, Dublin, Frankfurt, Hongkong, Las Vegas, London2, New York, Paříž, San Antonio, Silicon sedl, Tokio, Toronto, Washington DC, Washington DC2 |
| **[Hlavní telekomunikační systémy](https://www.chief.com.tw/)** |Podporováno |Podporováno |Hongkong, Taipei |
| **Čína – mobilní mezinárodní** |Podporováno |Podporováno | Hongkong, Hongkong Kong2, Singapur |
| **China Telecom Global** |Podporováno |Podporováno |Hongkong, Hongkong Kong2 |
| **Čína UNICOM Global** |Podporováno |Podporováno | Hongkong, Singapur2 |
| **[Chunghwa Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Podporováno |Podporováno |WAN |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Podporováno |Podporováno |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Podporováno |Podporováno |Chicago, Praha, Minneapolis, Montreal, Toronto, Vancouver, Washington DC |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Chicago, Dublin, Frankfurt, Londýn, London2, Milán, Newport, New York, Ósaka, Paříž, Silicon sedl, Silicon Valley2, Singapur2, Tokio, Washington DC |
| **[Comcastu](https://business.comcast.com/landingpage/microsoft-azure)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Podporováno |Podporováno |Chicago, Denver, Los Angeles, New York, Silicon sedl, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Podporováno |Podporováno |Amsterdam2, Dubai2, Frankfurt, Marseille, Bombaj, Mnichov, New York |
| **[Devoli](https://devoli.com/expressroute)** | Podporováno |Podporováno | Auckland, Melbourne, Sydney |
| **du dataměna** |Podporováno |Podporováno | Dubai2 |
| **eir** |Podporováno |Podporováno |Dublin|
| **[Epsilon globální komunikace](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Podporováno |Podporováno |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Brno, Berlín, Bogotá, Canberra2, Chicago, Praha, Dubai2, Dublin, Frankfurt, Frankfurt2, Ženeva, Hongkong – zvláštní správní oblast, Londýn, London2, Los Angeles, Los Angeles2, Melbourne, Miami, Milán, New York, Ósaka, Paříž, Rio de Janeiro, Svatý Paulo, Seattle, Soul, Silicon sedl, Singapur, Stockholm, Sydney, Brno, Toronto, Washington DC |
| **Etisalat Spojené arabské emiráty** |Podporováno |Podporováno |Dubaj|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Dublin, Frankfurt, Londýn |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |Podporováno |Podporováno |WAN|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Podporováno |Podporováno |Milán|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Podporováno |Podporováno |Montreal|
| **[GÉANT](https://www.geant.org/Networks)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Dublin, Frankfurt, Marseille |
| **[GlobalConnect]()** | Podporováno |Podporováno | Oslo, Stavanger | 
| **GTT** |Podporováno |Podporováno |London2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Podporováno| Podporováno | Čennaj, Bombaj |
| **Intelsat** | Podporováno | Podporováno | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Podporováno |Podporováno |Amsterdam, Chicago, Washington, Hongkong, Londýn, New York, Paříž, Silicon sedl, Singapur, Washington DC, Curych |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Podporováno |Podporováno |Chicago, Praha, Silicon sedl, Washington DC |
| **[Internet Initiative Japan Inc. – IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Podporováno |Podporováno |Ósaka, Tokio |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Podporováno |Podporováno |Kapské město, Johannesburg, Londýn |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Kodaň, Dublin, Frankfurt, Londýn, Marseille, Paříž, Curych |
| **[IRIDEOS](https://irideos.it/)** |Podporováno |Podporováno |Milán |
| **Horská oblast železa** | Podporováno |Podporováno |Washington DC |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Podporováno |Podporováno | Amsterdam, London2, Silicon sedl, Toronto, Washington DC |
| **Jaguar síť** |Podporováno |Podporováno |Marseille, Paříž |
| **[JISC](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Podporováno |Podporováno |Londýn, Newport (Wales) |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Podporováno |Podporováno |Soul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Podporováno |Podporováno |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Podporováno | Podporováno | Amsterdam |
| **[KT](https://cloud.kt.com/)** | Podporováno | Podporováno | Soul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Podporováno |Podporováno |Amsterdam, Chicago, Praha, Londýn, Newport (Wales), Svatý Paulo, Seattle, Silicon sedl, Singapur, Washington DC |
| **LG CNS** |Podporováno |Podporováno |Busan, Soul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Podporováno |Podporováno |Kapské město, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno |Podporováno |Amsterdam, Brno, Auckland, Čennaj, Chicago, Praha, Denver, Dubai2, Dublin, Frankfurt, Ženeva, Hongkong, Hongkong Kong2, Las Vegas, Londýn, London2, Los Angeles, Melbourne, Miami, Minneapolis, Montreal, New York, Ósaka, Oslo, Paříž, Perth, Quebec City, San Antonio, Seattle, Silicon sedl, Singapur, singapur2, Stavanger, Stockholm, Sydney, Sydney2, Tokio, Tokyo2 Toronto, Vancouver, Washington DC, Washington DC2, Curych |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Podporováno |Podporováno |Londýn |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Podporováno |Podporováno |Praha, Los Angeles, Miami, Svatý Paulo, Washington DC |
| **[Next Generation Data](https://vantage-dc-cardiff.co.uk/)** |Podporováno |Podporováno |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Podporováno |Podporováno |Melbourne, Perth, Sydney, Sydney2 |
| **[FAKTUR](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Podporováno |Podporováno |Amsterdam2 |
| **[NTT komunikace](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Podporováno |Podporováno |Amsterdam, Hongkong – zvláštní správní oblast, Londýn, Los Angeles, Ósaka, Singapur, Sydney, Tokio, Washington DC |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Podporováno |Podporováno |Tokio |
| **[NTT globální datacentra EMEA](https://hello.global.ntt/)** |Podporováno |Podporováno |Amsterdam2, Berlín |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Podporováno |Podporováno |Ósaka |
| **[Cloudové připojení Ooredoo](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Podporováno |Podporováno |Marseille |
| **[Optus](https://www.optus.com.au/enterprise/)** |Podporováno |Podporováno |Melbourne, Sydney |
| **[Oranžový](https://www.orange-business.com/en/products/business-vpn-galerie)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Dubai2, Frankfurt, Hongkong – zvláštní správní oblast, Johannesburg, Londýn, Paříž, Svatý Paulo, Silicon sedl, Singapur, Sydney, Tokio, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Podporováno | Podporováno | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Podporováno |Podporováno |Chicago, Las Vegas, Silicon sedl, Washington DC |
| **[PCCW globální omezení](https://consoleconnect.com/clouds/#azureRegions)** |Podporováno |Podporováno |Chicago, Hongkong, Hongkong, Kong2, Londýn, Singapur2 |
| **[REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/)** | Podporováno | Podporováno | Auckland |
| **[Retelit](https://www.retelit.it/EN/Home.aspx)** | Podporováno | Podporováno | Milán | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Podporováno |Podporováno |Soul |
| **[ŠETŘENÍ](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Podporováno |Podporováno | London2, Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Podporováno |Podporováno |Čennaj, Bombaj2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Podporováno |Podporováno |Hongkong Kong2, Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Podporováno |Podporováno |Ósaka, Tokio |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |Podporováno |Podporováno |London2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Podporováno |Podporováno |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Podporováno | Podporováno | Ženeva, Curych |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Podporováno |Podporováno |Amsterdam, Čennaj, Hongkong – zvláštní správní oblast, Londýn, Bombaj, Svatý Paulo, Silicon sedl, Singapur, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Podporováno |Podporováno |Amsterdam, Sao Paulo |
| **[Telehouse – KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Podporováno |Podporováno |Londýn, London2, Singapur2 |
| **Telenor** |Podporováno |Podporováno |Amsterdam, Londýn, Oslo |
| **[Telia dopravce](https://www.teliacarrier.com/)** | Podporováno | Podporováno |Amsterdam, Chicago, Praha, Frankfurt, Hongkong, Londýn, Oslo, Paříž, Silicon sedl, Stockholm, Washington DC |
| **[Telin](https://www.telin.net/)** | Podporováno | Podporováno |Jakarta |
| **Telmex Uninet**| Podporováno | Podporováno | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Podporováno |Podporováno |Melbourne, Singapur, Sydney |
| **[Telus](https://www.telus.com)** |Podporováno |Podporováno |Montreal, Seattle, Quebec City, Toronto, Vancouver |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Podporováno |Podporováno |Kapské město, Johannesburg |
| **[ČASOVÉ dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Podporováno | Podporováno | Kuala Lumpur |
| **[Komunikace Tokai](https://www.tokai-com.co.jp/en/)** | Podporováno | Podporováno | Ósaka, Tokyo2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Podporováno |Podporováno |Praha, Queretaro (Mexiko)|
| **T-Systems** |Podporováno |Podporováno |Frankfurt|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Podporováno |Podporováno |Sao Paulo |
| **[UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Podporováno | Podporováno | Bangkok |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Podporováno |Podporováno |Amsterdam, Chicago, Praha, Hongkong – zvláštní správní oblast, Londýn, Bombaj, Silicon sedla, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Podporováno | Podporováno | Washington DC2 |
| **[Vocus skupina NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Podporováno | Podporováno | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Podporováno |Podporováno |Amsterdam2, Londýn, Singapur |
| **[Vodafone nápad](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Podporováno | Podporováno | Bombaj2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Podporováno |Podporováno |Amsterdam, Chicago, Praha, Denver, Londýn, Los Angeles, Montreal, New York, Paříž, Seattle, Silicon sedl, Toronto, Washington DC, Washington DC2 |

 **+** označuje, že už brzy

### <a name="national-cloud-environment"></a>Prostředí národního cloudu

Národní cloudy Azure jsou oddělené od sebe navzájem i z celosvětově komerčních Azure. ExpressRoute pro jeden cloud Azure se nemůže připojit k oblastem Azure v ostatních. 

### <a name="us-government-cloud"></a>Cloud vlády USA

| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Podporováno |Podporováno |Chicago, Phoenix, Silicon sedl, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Podporováno |Podporováno |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Podporováno |Brno, Chicago, Praha, New York, Seattle, Silicon sedl, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno | Podporováno | Chicago, Praha, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Podporováno |Podporováno |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>Čína

| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **China Telecom** |Podporováno |Nepodporuje se |Peking, Beijing2, Shanghai, Shanghai2 |
| **Čína UNICOM** | Podporováno | Nepodporuje se | Beijing2, Shanghai2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Podporováno |Nepodporuje se |Beijing2, Shanghai2 |

Další informace najdete v tématu [ExpressRoute v Číně](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Německo

| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Podporováno |Nepodporuje se |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Nepodporuje se |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Podporováno |Nepodporuje se |Berlín |
| **Interxion** |Podporováno |Nepodporuje se |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno  | Nepodporuje se | Berlín |
| **T-Systems** |Podporováno |Nepodporuje se |Berlín |

## <a name="connectivity-through-exchange-providers"></a>Připojení prostřednictvím poskytovatelů Exchange

Pokud poskytovatel připojení není v předchozích částech uvedený, můžete přesto vytvořit připojení.

* Zkontrolujte u svého poskytovatele připojení, jestli je připojený k některé z výměn v předchozí tabulce. Můžete zkontrolovat následující odkazy, abyste získali další informace o službách nabízených poskytovateli výměny. Několik poskytovatelů připojení je už připojeno k ethernetovým výměnám.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
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

## <a name="connectivity-through-additional-service-providers"></a>Připojení prostřednictvím dalších poskytovatelů služeb

| **Poskytovatel připojení** | **Exchange** | **Umístění** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londýn |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, Londýn, Singapur, Washington DC |https://www.chief.com.tw/
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief]()** | Equinix | Hongkong – zvláštní administrativní oblast |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburk |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[APTUM technologie](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | Londýn |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Castle na koruna](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Brno, Chicago, Praha, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | Londýn, Singapur, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londýn |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Québec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Mezinárodní most (mezinárodní)](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londýn, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrajn B. S. C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong – zvláštní administrativní oblast 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Kapské město, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londýn |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[Core Cloud Service Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[PO roztelekomunikačním Lucembursku](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londýn, Paříž |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spektrum podniku](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Praha, Los Angeles, New York, Silicon sedlo | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londýn | 
| **[ORS Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon sedlo, Silicon sedl 2|
| **Zain** |Equinix |Londýn|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Připojení prostřednictvím poskytovatelů Datacenter

| **Poskytovatel** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Bance](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[Dataslévárenský](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX dosažitelný, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX dosažitelný, Megaport, PacketFabric |
| **[QTS datová centra](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Streamovaná datová centra]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[Datová centra RagingWire](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX dosažitelný, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX dosažitelný, Megaport |
| **[Datacentra T5](https://t5datacenters.com/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Připojení přes národní výzkumné a vzdělávací sítě (NREN)

| **Poskytovatel**|
| --- |
| **EQUINIX**| 
| **DeIC, prostřednictvím GÉANT**|
| **GARR, prostřednictvím GÉANT**|
| **GÉANT**|
| **HEAnet, prostřednictvím GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, prostřednictvím GÉANT**|
| **SINET**|
| **Surfnet, prostřednictvím GÉANT**|

* Pokud zde není uveden váš poskytovatel připojení, zkontrolujte, jestli není připojen k některému z partnerů ExpressRoute uvedených výše.

## <a name="expressroute-system-integrators"></a>Systémoví integrátoři ExpressRoute
Povolení soukromého připojení podle vlastních potřeb může být náročné, v závislosti na rozsahu vaší sítě. Můžete spolupracovat s kterýmkoli systémovým integrátorem uvedeným v následující tabulce, aby vám pomohl s připojením k ExpressRoute.

| **Systémový integrátor** | **Kontinent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Evropa |
| **[Avanade Inc.](https://www.avanade.com/)** | Asie, Evropa, Severní Amerika, Jižní Amerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Evropa
| **[Ensyst](https://www.ensyst.com.au)** | Asie
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Severní Amerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Severní Amerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Severní Amerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Austrálie |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Austrálie |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Evropa (Německo) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Evropa |
| **[Nový podpis](https://newsignature.com/technologies/express-route/)** | Evropa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asie |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Evropa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Severní Amerika |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | Severní Amerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Evropa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Jižní Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Austrálie |

## <a name="next-steps"></a>Další kroky
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa umístění"
