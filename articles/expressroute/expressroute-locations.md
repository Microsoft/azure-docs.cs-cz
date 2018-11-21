---
title: 'Poskytovatelé připojení a umístění: Azure ExpressRoute | Dokumentace Microsoftu'
description: Tento článek obsahuje podrobný přehled o umístěních, kde jsou nabízené služby, a jak se připojit k oblastem Azure. Řazení je podle poskytovatele připojení.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2018
ms.author: pareshmu
ms.openlocfilehash: 817dd1ec5cd047035099fb88d998f79cd8c39078
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276053"
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

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.
Následující tabulka obsahuje mapování oblastí Azure na umístění ExpressRoute v rámci geopolitické oblasti.

| **Geopolitická oblast** | **Zóna** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- | --- |
| **Australská vláda** |Zóna 1 | Austrálie – střed, Austrálie – střed 2 |Canberra, Canberra2 |
| **Evropa** |Zóna 1 |Francie – střed, Francie – jih, Severní Evropa, Západní Evropa, Velká Británie – západ, Velká Británie – jih |Amsterdam, Amsterdam2, Dublin, Londýn, Marseille, Newport (Wales), Paříž |
| **Severní Amerika** |Zóna 1 |Východní USA, Západní USA, Východní USA 2, Západní USA 2, Střed USA, Střed USA – jih, Střed USA – sever, Střed USA – západ, Střední Kanada, Východní Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal, Quebec City, Toronto |
| **Asie** |Zóna 2 |Východní Asie, Jihovýchodní Asie |Hongkong, Kuala Lumpur, Singapur, Singapur2 |
| **Austrálie** |Zóna 2 |Austrálie – jihovýchod, Austrálie – východ |Melbourne, Sydney | 
| **Indie** |Zóna 2 |Indie – západ, Indie – střed, Indie – jih |Čennaj, Čennaj2, Bombaj, Bombaj2 |
| **Japonsko** |Zóna 2 |Japonsko – západ, Japonsko – východ |Ósaka, Tokio |
| **Jižní Korea** |Zóna 2 |Jižní Korea – střed, Jižní Korea – jih |Busan, Soul|
| **Jižní Afrika** |Zóna 3 |[Jižní Afrika – západ+, Jižní Afrika – sever+](https://blogs.microsoft.com/blog/2017/05/18/microsoft-deliver-microsoft-cloud-datacenters-africa/) |Kapské město, Johannesburg |
| **Jižní Amerika** |Zóna 3 |Brazílie – jih |Sao Paulo |


 **+** označuje brzké uvedení.

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Oblasti a geopolitické hranice pro národní cloudy
Následující tabulka obsahuje informace o oblastech a geopolitických hranicích pro národní cloudy.

| **Geopolitická oblast** | **Oblasti Azure** | **Umístění ExpressRoute** |
| --- | --- | --- |
| **Cloud vlády USA** |US Gov – Arizona, US Gov – Iowa, US Gov – Texas, US Gov – Virginie, US DoD – střed, US DoD – východ  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Čína – východ** |Čína – východ, Čína – východ 2 |Šanghaj |
| **Čína – sever** |Čína – sever, Čína – sever 2 |Peking |
| **Německo** |Střední Německo, Východní Německo |Berlín, Frankfurt |

Připojení přes geopolitické oblasti není pomocí standardní SKU pro ExpressRoute podporované. Aby bylo podporované globální připojení, budete muset povolit doplněk ExpressRoute Premium. Připojení k prostředím národních cloudů není podporované. Podle potřeby můžete spolupracovat se svým poskytovatelem připojení.

## <a name="locations"></a>Umístění poskytovatele připojení

Následující tabulka uvádí umístění podle poskytovatele služby. Pokud chcete zobrazit dostupné poskytovatele podle umístění, přečtěte si téma [Poskytovatelé služeb podle umístění](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Produkční prostředí Azure
| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365 a Dynamics 365** | **Umístění** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Podporováno |Podporováno |Melbourne, Sydney |
| **[Airtel](http://www.airtel.in/creatingsmiles/)** | Podporováno | Podporováno | Čennaj2, Bombaj2 |
| **[Aryaka Networks](http://www.aryaka.com/)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Hongkong, Sao Paulo, Seattle, Silicon Valley, Singapur, Tokio, Washington DC |
| **[Datová centra Ascenty](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Podporováno |Podporováno |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Londýn, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Podporováno |Podporováno |Montreal, Toronto, Quebec City |
| **[British Telecom](https://globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Podporováno |Podporováno |Amsterdam, Hongkong, Londýn, Sao Paulo, Silicon Valley, Singapur, Sydney, Tokio, Washington DC |
| **[C3ntro](https://c3ntro.com/data/express-route/)** |Již brzy |Již brzy |Miami |
| **CDC** | Podporováno | Podporováno | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Podporováno |Podporováno |Las Vegas, New York, San Antonio, Silicon Valley, Tokio, Toronto |
| **China Telecom Global** |Podporováno |Nepodporuje se |Hongkong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Podporováno |Podporováno |Chicago, Dallas, Montreal, Toronto, Washington DC |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Podporováno |Podporováno |Amsterdam, Dublin, Londýn, Paříž, Tokio |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Podporováno |Podporováno |Chicago, Denver, Los Angeles, New York, Silicon Valley, Washington DC |
| **eir** |Podporováno |Podporováno |Dublin|
| **Epsilon Global Communications** |Podporováno |Podporováno |Singapur |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Podporováno |Amsterdam, Atlanta, Chicago, Dallas, Dublin, Hongkong, Londýn, Los Angeles, Melbourne, Miami, New York, Ósaka, Paříž, Sao Paulo, Seattle, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **euNetworks** |Podporováno |Podporováno |Amsterdam, Dublin, Londýn |
| **GÉANT** |Podporováno |Podporováno |Amsterdam |
| **[Global Cloud Xchange (GCX)](http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Podporováno| Podporováno | Čennaj, Bombaj |
| **[InterCloud](https://www.intercloud.com/)** |Podporováno |Podporováno |Amsterdam, Londýn, New York, Paříž, Silicon Valley, Singapur, Washington DC |
| **Internet2** |Podporováno |Podporováno |Chicago, Dallas, Washington DC |
| **[Internet Initiative Japan Inc. – IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Podporováno |Podporováno |Ósaka, Tokio |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Podporováno |Podporováno |Kapské město, Johannesburg, Londýn |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Podporováno |Podporováno |Amsterdam, Amsterdam2, Dublin, Londýn, Marseille, Paříž |
| **[IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)**|Podporováno |Podporováno | Amsterdam, Silicon Valley, Toronto |
| **Jisc** |Podporováno |Podporováno |Londýn |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Podporováno |Podporováno |Soul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Podporováno | Podporováno | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Londýn, Newport, Sao Paulo, Seattle, Silicon Valley, Singapur, Washington DC |
| **LG CNS** |Podporováno |Podporováno |Busan, Soul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Podporováno |Podporováno |Kapské město, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno |Podporováno |Amsterdam, Atlanta, Chicago, Dallas, Denver, Dublin, Hongkong, Las Vegas, Londýn, Los Angeles, Melbourne, Miami, New York, Quebec City, San Antonio, Seattle, Silicon Valley, Singapur, Singapur2, Sydney, Toronto, Washington DC |
| **MTN** |Podporováno |Podporováno |Londýn |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Podporováno |Podporováno |Dallas, Miami, Sao Paulo |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Podporováno |Podporováno |Newport(Wales) |
| **NEXTDC** |Podporováno |Podporováno |Melbourne, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Podporováno |Podporováno |Amsterdam, Hongkong, Londýn, Los Angeles, Ósaka, Singapur, Sydney, Tokio, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Podporováno |Podporováno |Tokio |
| **[NTT SmartConnect](http://cloud.nttsmc.com/cxc/azure.html)** |Podporováno |Podporováno |Ósaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Podporováno |Podporováno |Melbourne, Sydney |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Podporováno |Podporováno |Amsterdam, Hongkong, Londýn, Paříž, Sao Paulo, Silicon Valley, Singapur, Sydney, Washington DC |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Podporováno |Podporováno |Chicago, Hongkong, Londýn |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Podporováno |Podporováno |Soul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Podporováno |Podporováno |Čennaj, Bombaj2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Podporováno |Podporováno |Singapur, Singapur2 |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Podporováno |Podporováno |Ósaka, Tokio |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Podporováno |Podporováno |Amsterdam, Čennaj, Hongkong, Londýn, Bombaj, Sao Paulo, Silicon Valley, Singapur, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Podporováno |Podporováno |Amsterdam, Sao Paulo |
| **[Telehouse – KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Podporováno |Podporováno |Londýn |
| **Telenor** |Podporováno |Podporováno |Amsterdam, Londýn |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Podporováno | Podporováno |Amsterdam, Londýn, Washington DC |
| **Telmex Uninet**| Již brzy | Již brzy | Dallas+ |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Podporováno |Podporováno |Melbourne, Singapur, Sydney |
| **[Telus](http://www.telus.com)** |Podporováno |Podporováno |Montreal, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Podporováno |Podporováno |Kapské město, Johannesburg |
| **TIME dotCom** | Podporováno | Podporováno | Kuala Lumpur |
| **[UOLDIVEO](http://www.uoldiveo.com.br/solucoes/cloud.html#rmcl)** |Podporováno |Podporováno |Sao Paulo |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Hongkong, Londýn, Silicon Valley, Singapur, Sydney, Tokio, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Podporováno |Nepodporuje se |Londýn, Singapur |
| **Vodafone Idea** | Podporováno | Podporováno | Bombaj2 |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Podporováno |Podporováno |Amsterdam, Chicago, Dallas, Londýn, Los Angeles, Montreal, New York, Seattle, Silicon Valley, Toronto, Washington DC |

 **+** označuje brzké uvedení.

### <a name="national-cloud-environment"></a>Prostředí národního cloudu

### <a name="us-government-cloud"></a>Cloud vlády USA
| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Podporováno |Podporováno |Chicago, Washington DC |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Podporováno |Podporováno |New York, Phoenix |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Podporováno |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Podporováno |Podporováno |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno | Podporováno | Chicago, Dallas, San Antonio, Seattle |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Podporováno |Podporováno |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>Čína
| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **China Telecom** |Podporováno |Nepodporuje se |Peking, Šanghaj |

Další informace najdete v tématu [ExpressRoute v Číně](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Německo
| **Poskytovatel služeb** | **Microsoft Azure** | **Office 365** | **Umístění** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Podporováno |Nepodporuje se |Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Podporováno |Nepodporuje se |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Podporováno |Nepodporuje se |Berlín |
| **Interxion** |Podporováno |Nepodporuje se |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Podporováno  | Nepodporuje se | Berlín |
| **T-Systems** |Podporováno |Nepodporuje se |Berlín |

## <a name="connectivity-through-exchange-providers"></a>Možnosti připojení prostřednictvím poskytovatelů Exchange

Pokud poskytovatel připojení není v předchozích částech uvedený, můžete přesto vytvořit připojení.

* Zkontrolujte u svého poskytovatele připojení, jestli je připojený k některé z výměn v předchozí tabulce. Můžete zkontrolovat následující odkazy, abyste získali další informace o službách nabízených poskytovateli výměny. Několik poskytovatelů připojení je už připojeno k ethernetovým výměnám.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Rozšířil váš poskytovatel připojení vaši síť ke zvolenému umístění partnerského vztahu?
  * Ujistěte se, že poskytovatel připojení rozšiřuje vaše připojení vysoce dostupným způsobem tak, aby neexistovaly žádné jediné body selhání.
* Abyste se připojili k Microsoftu, objednejte si okruh ExpressRoute s výměnou jako poskytovatel připojení.
  * Při nastavení připojení postupujte podle kroků v tématu [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md).

## <a name="connectivity-through-additional-service-providers"></a>Možnosti připojení prostřednictvím dalších poskytovatelů služeb

| **Poskytovatel připojení** | **Výměna** | **Umístění** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](http://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londýn |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, Londýn, Singapur, Washington DC |
| **[BroadBand Tower, Inc.](http://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hongkong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburk |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Silicon Valley, Washington DC | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londýn, Singapur, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londýn |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Québec |
| **[Gtt Communications Inc](https://www.gtt.net/wp-content/uploads/2017/04/EtherCloud-Data-Sheet.pdf)** |Equinix | Washington DC |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londýn, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **LGA Telecom** |Equinix |Singapur|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, New York, Washington DC |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londýn |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |
| **[Post](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londýn, Paříž |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londýn | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](http://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](http://venhapranuvem.com.br/infraestrutura/oferta-especial-digital-edge-expressroute/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **Zain** |Equinix |Londýn|
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Možnosti připojení prostřednictvím poskytovatelů datacenter
| **Poskytovatel** | **Výměna** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[Datová centra RagingWire](http://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[Datacentra T5](http://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Možnosti připojení prostřednictvím sítí národního výzkumu a vzdělávání (NREN)

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
| **[Avanade Inc.](http://www.avanade.com/)** | Asie, Evropa, Severní Amerika, Jižní Amerika |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Evropa
| **[Ensyst](http://www.ensyst.com.au)** | Asie
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Severní Amerika |
| **[FlexManage](http://www.flexmanage.com/cloud)** | Severní Amerika |
| **[Inframon](http://www.inframon.com/partner/microsoft/)** | Evropa |
| **[Lightstream](https://www.ltstream.com/expressroute)** | Severní Amerika |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Austrálie |
| **[MOQdigital](http://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Austrálie |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Evropa (Německo) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Evropa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Evropa |
| **[OneAs1a](http://www.oneas1a.com/connectivity.html)** | Asie |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Evropa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Severní Amerika |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | Severní Amerika |
| **[sol-tec](https://www.sol-tec.com/services)** | Evropa |
| **[Venha Pra Nuvem](http://venhapranuvem.com.br/infraestrutura/oferta-especial-digital-edge-expressroute/)** | Jižní Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Austrálie |

## <a name="next-steps"></a>Další postup
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa umístění"
