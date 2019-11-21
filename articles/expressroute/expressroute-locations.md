---
title: 'Poskytovatelé připojení a umístění: Azure ExpressRoute | Dokumentace Microsoftu'
description: Tento článek obsahuje podrobný přehled o umístěních, kde jsou nabízené služby, a jak se připojit k oblastem Azure. Řazení je podle poskytovatele připojení.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: cherylmc
ms.openlocfilehash: 793d88ec67fbd839851894077e8f5c481b9ebda7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217093"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partneři ExpressRoute a umístění partnerského vztahu

> [!div class="op_single_selector"]
> * [Umístění podle poskytovatele](expressroute-locations.md)
> * [Poskytovatelé podle umístění](expressroute-locations-providers.md)


The tables in this article provide information on ExpressRoute geographical coverage and locations, ExpressRoute connectivity providers,and ExpressRoute System Integrators (SIs).

> [!Note]
> Azure regions and ExpressRoute locations are two distinct and different concepts, understanding the difference between the two is critical to exploring Azure hybrid networking connectivity. 
>
>

## <a name="azure-regions"></a>Oblasti Azure
Azure regions are global datacenters where Azure compute, networking and storage resources are located. When creating an Azure resource, a customer needs to select a resource location. The resource location determines which Azure datacenter (or availability zone) the resource is created in.

## <a name="expressroute-locations"></a>Umístění ExpressRoute
ExpressRoute locations (sometimes referred to as peering locations or meet-me-locations) are co-location facilities where Microsoft Enterprise Edge (MSEE) devices are located. ExpressRoute locations are the entry point to Microsoft’s network – and are globally distributed, providing customers the opportunity to connect to Microsoft’s network around the world. These locations are where ExpressRoute partners and ExpressRoute Direct customers issue cross connections to Microsoft’s network. In general, the ExpressRoute location does not need to match the Azure region. For example, a customer can create an ExpressRoute circuit with the resource location *East US*, in the *Seattle* Peering location.

Pokud jste připojení k aspoň jednomu umístění ExpressRoute v rámci geopolitické oblasti, budete mít přístup ke službám Azure napříč všemi oblastmi v rámci geopolitické oblasti.

## <a name="locations"></a>Azure regions to ExpressRoute locations within a geopolitical region.
Následující tabulka obsahuje mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Australská vláda** |Austrálie – střed, Austrálie – střed 2 |Canberra, Canberra2 |
| **Evropa** | Francie – střed, Francie – jih, Severní Evropa, Západní Evropa, Velká Británie – západ, Velká Británie – jih |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, Geneva, London, London2, Marseille, Milan, Munich, Newport(Wales), Oslo, Paris, Stavanger, Stockholm, Zurich |
| **Severní Amerika** |Východní USA, Západní USA, Východní USA 2, Západní USA 2, Střední USA, Středojižní USA, Středoseverní USA, Středozápadní USA, Střední Kanada, Východní Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asie** | Východní Asie, Jihovýchodní Asie |Hong Kong SAR, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **Indie** | Indie – západ, Indie – střed, Indie – jih |Čennaj, Čennaj2, Bombaj, Bombaj2 |
| **Japonsko** | Japonsko – západ, Japonsko – východ |Ósaka, Tokio |
| **Oceania** | Austrálie – jihovýchod, Austrálie – východ |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Jižní Korea** | Jižní Korea – střed, Jižní Korea – jih |Busan, Soul|
| **UAE** | UAE Central, UAE North | Dubai, Dubai2 |
| **Jižní Afrika** | South Africa West, South Africa North |Kapské město, Johannesburg |
| **Jižní Amerika** | Brazílie – jih |Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Oblasti a geopolitické hranice pro národní cloudy
Následující tabulka obsahuje informace o oblastech a geopolitických hranicích pro národní cloudy.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Cloud vlády USA** |US Gov – Arizona, US Gov – Iowa, US Gov – Texas, US Gov – Virginie, US DoD – střed, US DoD – východ  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Čína – východ** |Čína – východ, Čína – východ 2 |Shanghai, Shanghai2 |
| **Čína – sever** |Čína – sever, Čína – sever 2 |Beijing, Beijing2 |
| **Německo** |Střední Německo, Východní Německo |Berlín, Frankfurt |

Připojení přes geopolitické oblasti není pomocí standardní SKU pro ExpressRoute podporované. Aby bylo podporované globální připojení, budete muset povolit doplněk ExpressRoute Premium. Připojení k prostředím národních cloudů není podporované. Podle potřeby můžete spolupracovat se svým poskytovatelem připojení.

## <a name="partners"></a>Poskytovatelé připojení ExpressRoute

Následující tabulka uvádí umístění podle poskytovatele služby. Pokud chcete zobrazit dostupné poskytovatele podle umístění, přečtěte si téma [Poskytovatelé služeb podle umístění](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Global commercial Azure

| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365**  | **Umístění** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Podporováno |Podporováno |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Podporováno | Podporováno | Čennaj2, Bombaj2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Hong Kong SAR, Sao Paulo, Seattle, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Datová centra Ascenty](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Podporováno |Podporováno |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Londýn, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Podporováno |Podporováno |Montreal, Toronto, Quebec City |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Podporováno |Podporováno |Amsterdam, Hong Kong SAR, Johannesburg, London, Newport(Wales), Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Podporováno |Podporováno |Miami |
| **CDC** | Podporováno | Podporováno | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Podporováno |Podporováno |Amsterdam2, Chicago, Hong Kong, Las Vegas, New York, Paris, San Antonio, Silicon Valley, Tokyo, Toronto, Washington DC |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Podporováno |Podporováno |Hong Kong, Taipei |
| **China Telecom Global** |Podporováno |Podporováno |Hong Kong SAR |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Podporováno |Podporováno |Chicago, Dallas, Montreal, Toronto, Washington DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Dublin, London, Newport, New York, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapore2, Tokyo |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Podporováno |Podporováno |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Podporováno |Podporováno |Amsterdam2, Frankfurt, Marseille|
| **[Devoli](https://devoli.com/expressroute)** | Podporováno |Podporováno | Auckland, Melbourne, Sydney |
| **du datamena** |Podporováno |Podporováno | Dubai2 |
| **eir** |Podporováno |Podporováno |Dublin|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Podporováno |Podporováno |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Podporováno |Amsterdam, Atlanta, Chicago, Dallas, Dublin,  Hong Kong SAR, London, London2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silicon Valley, Singapore, Stockholm, Sydney, Tokyo, Toronto, Washington DC |
| **Etisalat UAE** |Podporováno |Podporováno |Dubaj|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Dublin, London |
| **FarEasTone** |Podporováno |Podporováno |Tchaj-pej|
| **GÉANT** |Podporováno |Podporováno |Amsterdam |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Podporováno| Podporováno | Čennaj, Bombaj |
| **[InterCloud](https://www.intercloud.com/)** |Podporováno |Podporováno |Amsterdam, Chicago, London, New York, Paris, Silicon Valley, Singapore, Washington DC, Zurich |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Podporováno |Podporováno |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. – IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Podporováno |Podporováno |Ósaka, Tokio |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Podporováno |Podporováno |Kapské město, Johannesburg, Londýn |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, London, Marseille, Paris, Zurich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Podporováno |Podporováno | Amsterdam, London2, Silicon Valley, Toronto |
| **Jaguar Network** |Podporováno |Podporováno |Marseille|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Podporováno |Podporováno |Londýn |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Podporováno |Podporováno |Soul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Podporováno |Podporováno |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Podporováno | Podporováno | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Podporováno |Podporováno |Busan, Soul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Podporováno |Podporováno |Kapské město, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno |Podporováno |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Hong Kong SAR, Las Vegas, London, Los Angeles, Melbourne, Miami, Montreal, New York, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Sydney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Podporováno |Podporováno |Londýn |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Podporováno |Podporováno |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Next Generation Data](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Podporováno |Podporováno |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Podporováno |Podporováno |Melbourne, Perth, Sydney, Sydney2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Podporováno |Podporováno |Amsterdam, Hong Kong SAR, London, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Podporováno |Podporováno |Tokio |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Podporováno |Podporováno |Ósaka |
| **[Optus](https://www.optus.com.au/enterprise/)** |Podporováno |Podporováno |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Podporováno |Podporováno |Amsterdam, Frankfurt, Hong Kong SAR, Johannesburg, London, Paris, Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Podporováno | Podporováno | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Podporováno |Podporováno |Chicago, Hong Kong SAR, London |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Podporováno |Podporováno |Soul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Podporováno |Podporováno | Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Podporováno |Podporováno |Čennaj, Bombaj2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Podporováno |Podporováno |Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Podporováno |Podporováno |Ósaka, Tokio |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Podporováno |Podporováno |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Podporováno | Podporováno | Curych |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Podporováno |Podporováno |Amsterdam, Chennai, Hong Kong SAR, London, Mumbai, Sao Paulo, Silicon Valley, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Podporováno |Podporováno |Amsterdam, Sao Paulo |
| **[Telehouse – KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Podporováno |Podporováno |London, London2 |
| **Telenor** |Podporováno |Podporováno |Amsterdam, London, Oslo |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Podporováno | Podporováno |Amsterdam, Chicago, Dallas, Hong Kong, London, Paris, Silicon Valley, Stockholm, Washington DC |
| **Telmex Uninet**| Podporováno | Podporováno | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Podporováno |Podporováno |Melbourne, Singapur, Sydney |
| **[Telus](https://www.telus.com)** |Podporováno |Podporováno |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Podporováno |Podporováno |Kapské město, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Podporováno | Podporováno | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Podporováno |Podporováno |Dallas, Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Podporováno |Podporováno |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Hong Kong SAR, London, Mumbai, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Podporováno | Podporováno | Washington DC2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Podporováno | Podporováno | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Podporováno |Podporováno |Amsterdam2, London, Singapore |
| **[Vodafone Idea](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Podporováno | Podporováno | Mumbai, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** označuje brzké uvedení.

### <a name="national-cloud-environment"></a>Prostředí národního cloudu

Azure national clouds are isolated from each other and from global commerical Azure. ExpressRoute for one Azure cloud can't connect to the Azure regions in the others. 

### <a name="us-government-cloud"></a>Cloud vlády USA

| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Podporováno |Podporováno |Chicago, Phoenix,  Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Podporováno |Podporováno |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Podporováno |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno | Podporováno | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Podporováno |Podporováno |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>Čína

| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **China Telecom** |Podporováno |Nepodporuje se |Beijing, Beijing2, Shanghai, Shanghai2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Podporováno |Nepodporuje se |Beijing2, Shanghai2 |

Další informace najdete v tématu [ExpressRoute v Číně](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Německo

| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Podporováno |Nepodporuje se |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Nepodporuje se |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Podporováno |Nepodporuje se |Berlín |
| **Interxion** |Podporováno |Nepodporuje se |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno  | Nepodporuje se | Berlín |
| **T-Systems** |Podporováno |Nepodporuje se |Berlín |

## <a name="connectivity-through-exchange-providers"></a>Connectivity through Exchange providers

Pokud poskytovatel připojení není v předchozích částech uvedený, můžete přesto vytvořit připojení.

* Zkontrolujte u svého poskytovatele připojení, jestli je připojený k některé z výměn v předchozí tabulce. Můžete zkontrolovat následující odkazy, abyste získali další informace o službách nabízených poskytovateli výměny. Několik poskytovatelů připojení je už připojeno k ethernetovým výměnám.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Rozšířil váš poskytovatel připojení vaši síť ke zvolenému umístění partnerského vztahu?
  * Ujistěte se, že poskytovatel připojení rozšiřuje vaše připojení vysoce dostupným způsobem tak, aby neexistovaly žádné jediné body selhání.
* Abyste se připojili k Microsoftu, objednejte si okruh ExpressRoute s výměnou jako poskytovatel připojení.
  * Při nastavení připojení postupujte podle kroků v tématu [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md).

## <a name="connectivity-through-satellite-operators"></a>Connectivity through satellite operators
If you are remote and don't have fiber connectivity or you want to explore other connectivity options you can check the following satellite operators. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Connectivity through additional service providers

| **Poskytovatel připojení** | **Výměna** | **Umístění** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londýn |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, London, Singapore, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hong Kong SAR |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburk |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | Londýn |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londýn, Singapur, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londýn |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Québec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londýn, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hong Kong SAR 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Kapské město, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londýn |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londýn, Paříž |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londýn | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |Londýn|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Connectivity through datacenter providers

| **Poskytovatel** | **Výměna** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[Datová centra RagingWire](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[Datacentra T5](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connectivity through National Research and Education Networks (NREN)

| **Poskytovatel**|
| --- |
| **AARNET**| 
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
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Austrálie |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Evropa (Německo) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Evropa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Evropa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asie |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Evropa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Severní Amerika |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Severní Amerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Evropa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Jižní Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Austrálie |

## <a name="next-steps"></a>Další kroky
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa umístění"
