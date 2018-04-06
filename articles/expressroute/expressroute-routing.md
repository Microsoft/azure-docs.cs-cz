---
title: Požadavky směrování pro Azure ExpressRoute | Dokumentace Microsoftu
description: Tato stránka obsahuje podrobné požadavky pro konfiguraci a správu směrování pro okruhy ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: ganesr
editor: ''
ms.assetid: 5b382e79-fa3f-495a-a764-c5ff86af66a2
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2018
ms.author: ganesr
ms.openlocfilehash: 829646be6404f86d9f370b3a402cfc0c0c980699
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="expressroute-routing-requirements"></a>Požadavky na směrování služby ExpressRoute
Pokud se chcete připojit ke cloudovým službám Microsoftu pomocí služby ExpressRoute, budete muset nastavit a spravovat směrování. Někteří poskytovatelé připojení nabízejí nastavení a správu směrování jako spravovanou službu. Zeptejte se svého poskytovatele připojení, jestli tuto službu nabízí. Pokud ne, je nutné splnit následující požadavky:

Přečtěte si článek [Okruhy a domény směrování](expressroute-circuit-peerings.md), který popisuje relace směrování, které musí být nastaveny k usnadnění připojení.

> [!NOTE]
> Microsoft nepodporuje žádné protokoly redundance směrovačů (např. HSRP nebo VRRP) pro konfigurace s vysokou dostupností. V případě vysoké dostupnosti spoléháme na redundantní dvojici relací protokolu BGP na partnerský vztah.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>IP adresy sloužící pro partnerské vztahy
Je nutné rezervovat několik bloků IP adres, abyste nakonfigurovali směrování mezi vaší sítí a směrovači MSEE (Microsoft Enterprise Edge). Tato část poskytuje seznam požadavků a popisuje pravidla týkající se získávání a použití těchto IP adres.

### <a name="ip-addresses-used-for-azure-private-peering"></a>IP adresy sloužící pro soukromý partnerský vztah Azure
Ke konfiguraci partnerských vztahů můžete použít buď soukromé IP adresy, nebo veřejné IP adresy. Rozsah adres použitý ke konfiguraci tras se nesmí překrývat s rozsahy adres použitými k vytvoření virtuálních sítí v Azure. 

* Pro rozhraní směrování musíte rezervovat podsíť /29 nebo dvě podsítě /30.
* Podsítě pro směrování mohou obsahovat buď soukromé IP adresy, nebo veřejné IP adresy.
* Podsítě nesmí být v konfliktu s rozsahem vyhrazeným zákazníkem pro použití v cloudu Microsoftu.
* Pokud se použije podsíť /29, rozdělí se na dvě podsítě /30. 
  * První podsíť /30 se používá pro primární propojení a druhá podsíť /30 se používá pro sekundární propojení.
  * Pro každou z těchto podsítí /30 musíte ve směrovači použít první IP adresu podsítě /30. Microsoft používá druhou IP adresu podsítě /30 k nastavení relace protokolu BGP.
  * Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.  

#### <a name="example-for-private-peering"></a>Příklad soukromého partnerského vztahu
Pokud k nastavení partnerského vztahu zvolíte a.b.c.d/29, rozdělí se do dvou podsítí /30. V následujícím příkladu se podíváme na použití podsítě a.b.c.d/29. 

a.b.c.d/29 se rozdělí na a.b.c.d/30 a a.b.c.d+4/30 a předá se Microsoftu prostřednictvím rozhraní API pro zřizování. a.b.c.d+1 použijete jako IP adresu VRF pro primární PE a Microsoft využije a.b.c.d+2 jako IP adresu VRF pro primární MSEE. a.b.c.d+5 použijete jako IP adresu VRF pro sekundární PE a Microsoft použije a.b.c.d+6 jako IP adresu VRF pro sekundární MSEE.

Představte si případ, kdy k nastavení soukromého partnerského vztahu vyberete 192.168.100.128/29. 192.168.100.128/29 obsahuje adresy od 192.168.100.128 do 192.168.100.135, kde:

* 192.168.100.128/30 se přiřadí pro link1, kde poskytovatel použije 192.168.100.129 a Microsoft použije 192.168.100.130.
* 192.168.100.132/30 se přiřadí pro link2, kde poskytovatel použije 192.168.100.133 a Microsoft použije 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-peering"></a>IP adresy používané pro veřejný partnerský vztah Azure
Pro nastavení relací protokolu BGP musíte použít veřejné IP adresy, které vlastníte. Microsoft musí být schopný ověřit vlastnictví IPv4 adres v registrech RIR a IRR. 

* K nastavení partnerského vztahu BGP pro každý partnerský vztah pro každý okruh ExpressRoute (pokud jich používáte víc než jeden) musíte použít jedinečnou podsíť /29 nebo dvě podsítě /30. 
* Pokud se použije podsíť /29, rozdělí se na dvě podsítě /30. 
  * První podsíť /30 se používá pro primární propojení a druhá podsíť /30 se používá pro sekundární propojení.
  * Pro každou z těchto podsítí /30 musíte ve směrovači použít první IP adresu podsítě /30. Microsoft používá druhou IP adresu podsítě /30 k nastavení relace protokolu BGP.
  * Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.

### <a name="ip-addresses-used-for-microsoft-peering"></a>IP adresy používané pro partnerský vztah Microsoftu
Pro nastavení relací protokolu BGP musíte použít veřejné IP adresy, které vlastníte. Microsoft musí být schopný ověřit vlastnictví IPv4 adres v registrech RIR a IRR.

* K nastavení partnerského vztahu BGP pro každý partnerský vztah pro každý okruh ExpressRoute (pokud jich používáte víc než jeden) musíte použít jedinečnou podsíť /29 (protokol IPv4) nebo /125 (protokol IPv6) nebo dvě podsítě /30 (protokol IPv4) nebo /126 (protokol IPv6).
* Pokud se použije podsíť /29, rozdělí se na dvě podsítě /30.
* První podsíť /30 se použije pro primární propojení a druhá podsíť /30 se použije pro sekundární propojení.
* Pro každou z těchto podsítí /30 musíte ve směrovači použít první IP adresu podsítě /30. Microsoft používá druhou IP adresu podsítě /30 k nastavení relace protokolu BGP.
* Pokud se použije podsíť /125, rozdělí se na dvě podsítě /126.
* První podsíť /126 se použije pro primární propojení a druhá podsíť /126 se použije pro sekundární propojení.
* Pro každou z těchto podsítí /126 musíte ve směrovači použít první IP adresu podsítě /126. Microsoft používá druhou IP adresu podsítě /126 k nastavení relace protokolu BGP.
* Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.

## <a name="public-ip-address-requirement"></a>Požadavek veřejné IP adresy

### <a name="private-peering"></a>Soukromý partnerský vztah
Pro soukromý partnerský vztah si můžete zvolit použití veřejných nebo privátních IPv4 adres. Poskytujeme kompletní izolaci provozu, takže v případě soukromého partnerského vztahu není možné překrývání adres s jinými zákazníky. Tyto adresy nejsou inzerované na internetu. 


### <a name="public-peering"></a>Veřejný partnerský vztah
Cesta veřejného partnerského vztahu Azure vám umožňuje připojení ke všem službám, které jsou hostovány v Azure, přes jejich veřejné IP adresy. Sem patří služby uvedené v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md) a všechny služby hostované nezávislými dodavateli softwaru v Microsoft Azure. Připojení ke službám Microsoft Azure ve veřejném partnerském vztahu je vždycky iniciováno z vaší sítě do sítě Microsoftu. Pro přenosy směřující do služby MSN je nutné použít veřejné IP adresy.

> [!IMPORTANT]
> Všechny služby Azure PaaS jsou přístupné také prostřednictvím partnerského vztahu Microsoftu. Doporučujeme vytvořit partnerský vztah Microsoftu a přes něj se připojovat ke službám Azure PaaS.  
>   


U veřejného partnerského vztahu je povoleno soukromé číslo AS.

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu
Cesta partnerského vztahu Microsoftu vám umožní připojit se ke cloudovým službám Microsoftu, které nejsou podporované prostřednictvím cesty veřejného partnerského vztahu Azure. Mezi tyto služby patří služby Office 365, jako je Exchange Online, SharePoint Online, Skype pro firmy nebo Dynamics 365. Microsoft v partnerském vztahu Microsoftu podporuje obousměrné připojení. Přenosy směřující do cloudových služeb Microsoftu musí před vstupem do služby MSN používat platné veřejné IPv4 adresy.

Ujistěte se, že vaše IP adresa a číslo AS jsou registrované na vás v jednom z následujících registrů:


* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

Pokud vám ve výše uvedených registrech nejsou přiřazeny vaše předpony a číslo AS, musíte otevřít případ podpory pro ruční ověření předpon a čísla ASN. Podpora vyžaduje dokumentaci, například zplnomocnění, která prokáže vaše oprávnění používat příslušné prostředky.

U partnerského vztahu Microsoftu je povoleno soukromé číslo AS, které ale také vyžaduje ruční ověření. Kromě toho v AS PATH odebereme soukromá čísla AS pro přijaté předpony. V důsledku toho nebudete moct připojit soukromá čísla AS k AS PATH, abyste [ovlivnili směrování pro partnerský vztah Microsoftu](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Veřejné IP adresy inzerované do Microsoftu prostřednictvím ExpressRoute nesmí být inzerované na internetu. To by mohlo způsobit přerušení připojení k jiným službám Microsoftu. Nicméně veřejné IP adresy používané servery ve vaší síti, které komunikují s koncovými body O365 v rámci Microsoftu, lze inzerovat prostřednictvím ExpressRoute. 
> 
> 

## <a name="dynamic-route-exchange"></a>Dynamická výměna tras
Výměna směrování bude přes protokol EBGP. Relace EBGP se vytvoří mezi směrovači MSEE a vašimi směrovači. Ověřování relací BGP není povinné. V případě potřeby lze nakonfigurovat hodnotu hash MD5. Informace o konfiguraci relací BGP najdete v tématu [Konfigurace směrování](expressroute-howto-routing-classic.md) a [Pracovní postupy zřizování okruhů a stavy okruhu](expressroute-workflows.md).

## <a name="autonomous-system-numbers"></a>Čísla autonomního systému
Microsoft pro veřejný partnerský vztah Azure, soukromý partnerský vztah Azure a partnerský vztah Microsoftu používá číslo AS 12076. Pro interní použití jsme vyhradili čísla ASN od 65515 do 65520. Jsou podporována 16bitová a 32bitová čísla AS.

Nejsou žádné požadavky týkající se symetrie přenosu dat. Cesty vpřed a zpět můžou procházet různými dvojicemi směrovačů. Můžou být inzerovány identické trasy z obou stran přes víc dvojic okruhů, které vám patří. Metriky tras nemusejí být identické.

## <a name="route-aggregation-and-prefix-limits"></a>Agregace tras a omezení předpon
Podporujeme až 4000 předpon, které jsou nám inzerované prostřednictvím soukromého partnerského vztahu Azure. To omezení může být zvýšeno až 10 000 předpon, pokud je povolen doplněk ExpressRoute Premium. Přijímáme až 200 předpon na každou relaci BGP pro veřejný partnerský vztah Azure a partnerský vztah Microsoftu. 

Pokud počet předpon překročí toto omezení, relace BGP se ukončí. Budeme přijímat výchozí trasy jenom na propojeních soukromého partnerského vztahu. Poskytovatel musí odfiltrovat výchozí trasy a privátní IP adresy (RFC 1918) z cest pro veřejný partnerský vztah Azure a partnerský vztah Microsoftu. 

## <a name="transit-routing-and-cross-region-routing"></a>Tranzitní směrování a směrování mezi oblastmi
Službu ExpressRoute nejde nakonfigurovat jako tranzitní směrovače. Ohledně služeb tranzitního směrování se budete muset spoléhat na svého poskytovatele připojení.

## <a name="advertising-default-routes"></a>Inzerování výchozích tras
Výchozí trasy jsou povolené jenom na relacích soukromého partnerského vztahu Azure. V takovém případě bude směrovat veškerý provoz z přidružených virtuálních sítí do své sítě. Inzerování výchozích tras do soukromého partnerského vztahu povede k tomu, že internetové trasy z Azure budou blokovány. Musíte spoléhat na vaše podnikové hraniční servery, že pro služby hostované v Azure přesměrují provoz z internetu a do něj. 

 Chcete-li povolit připojení k dalším službám Azure a službám infrastruktury, je třeba zajistit, že platí jedna z následujících položek:

* Veřejný partnerský vztah Azure má povolené přesměrování provozu na veřejné koncové body.
* Používáte uživatelsky definované směrování umožňující připojení k internetu pro každou podsíť, která připojení k internetu vyžaduje.

> [!NOTE]
> Inzerování výchozích tras poruší aktivaci licencí pro Windows a jiné virtuální počítače. Náhradní řešení najdete [zde](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx).
> 
> 

## <a name="bgp"></a>Podpora komunit protokolu BGP
Tato část obsahuje přehled použití komunit protokolu BGP se službou ExpressRoute. Microsoft bude inzerovat trasy v cestách veřejného partnerského vztahu a partnerského vztahu Microsoftu s trasami, které jsou označené odpovídajícími hodnotami komunity. Důvody tohoto postupu a podrobnosti o hodnotách komunity jsou popsané dál. Microsoft ale nebude ctít žádné hodnoty komunity přiřazené trasám inzerovaným Microsoftu.

Pokud se připojujete k Microsoftu prostřednictvím ExpressRoute v libovolném umístění partnerského vztahu v rámci geopolitické oblasti, budete mít přístup ke všem cloudovým službám Microsoftu přes všechny oblasti v rámci geopolitické hranice. 

Pokud jste například připojení k Microsoftu prostřednictvím ExpressRoute v Amsterdamu, budete mít přístup ke všem cloudovým službám Microsoftu hostovaným v oblastech Severní Evropa a Západní Evropa. 

Podrobný seznam geopolitických oblastí, přidružených oblastí Azure a odpovídajících umístění partnerského vztahu ExpressRoute najdete na stránce [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

Můžete zakoupit víc než jeden okruh ExpressRoute na geopolitickou oblast. Použití víc připojení nabízí významné výhody vysoké dostupnosti z důvodu georedundance. V případech, kdy máte víc okruhů ExpressRoute, obdržíte stejnou sadu předpon inzerovaných Microsoftem na cestě veřejného partnerského vztahu i partnerského vztahu Microsoftu. To znamená, že bude mít z vaší sítě do Microsoftu víc cest. To může potenciálně v rámci vaší sítě způsobovat přijímání neoptimálních rozhodnutí o směrování. V důsledku toho se můžete u různých služeb setkat s neoptimálním průběhem připojení. Při rozhodování o směrování se na tyto hodnoty komunity můžete spoléhat, abyste nabízeli [uživatelům optimální směrování](expressroute-optimize-routing.md).

| **Oblast Microsoft Azure** | **Hodnota komunity protokolu BGP** |
| --- | --- |
| **Severní Amerika** | |
| Východ USA | 12076:51004 |
| Východní USA 2 | 12076:51005 |
| Západní USA | 12076:51006 |
| Západní USA 2 | 12076:51026 |
| Západní střed USA | 12076:51027 |
| Střed USA – sever | 12076:51007 |
| Střed USA – jih | 12076:51008 |
| Střed USA | 12076:51009 |
| Střední Kanada | 12076:51020 |
| Východní Kanada | 12076:51021 |
| **Jižní Amerika** | |
| Brazílie – jih | 12076:51014 |
| **Evropa** | |
| Severní Evropa | 12076:51003 |
| Západní Evropa | 12076:51002 |
| Spojené království – jih | 12076:51024 |
| Spojené království – západ | 12076:51025 |
| Francie – střed | 12076:51030 |
| Francie – jih | 12076:51031 |
| **Asie a Tichomoří** | |
| Východní Asie | 12076:51010 |
| Jihovýchodní Asie | 12076:51011 |
| **Japonsko** | |
| Japonsko – východ | 12076:51012 |
| Japonsko – západ | 12076:51013 |
| **Austrálie** | |
| Austrálie – východ | 12076:51015 |
| Austrálie – jihovýchod | 12076:51016 |
| **Indie** | |
| Indie – jih | 12076:51019 |
| Indie – západ | 12076:51018 |
| Indie – střed | 12076:51017 |
| **Korea** | |
| Korea – jih | 12076:51028 |
| Korea – střed | 12076:51029 |


Všechny trasy inzerované Microsoftem budou označené odpovídající hodnotou komunity. 

> [!IMPORTANT]
> Globální předpony se označují odpovídající hodnotou komunity a budou se inzerovat, jenom když bude povolený doplněk ExpressRoute Premium.
> 
> 

Kromě výše uvedeného bude Microsoft také označovat předpony podle služby, ke které patří. To se týká jenom partnerského vztahu Microsoftu. Následující tabulka poskytuje mapování služby na hodnotu komunity protokolu BGP.

| **Služba** | **Hodnota komunity protokolu BGP** |
| --- | --- |
| Exchange Online | 12076:5010 |
| SharePoint Online | 12076:5020 |
| Online Skype pro firmy | 12076:5030 |
| Dynamics 365 | 12076:5040 |
| Jiné online služby Office 365 | 12076:5100 |

> [!NOTE]
> Microsoft nectí žádné hodnoty komunity protokolu BGP, které jste přiřadili trasám inzerovaným Microsoftu.
> 
> 

### <a name="bgp-community-support-in-national-clouds-preview"></a>Podpora komunity protokolu BGP v národních cloudech (Preview)

| **Oblast Azure národních cloudů**| **Hodnota komunity protokolu BGP** |
| --- | --- |
| **US Government** |  |
| USA (Gov) – Arizona | 12076:51106 |
| USA (Gov) – Iowa | 12076:51109 |
| USA (Gov) – Virginia | 12076:51105 |
| USA (Gov) – Texas | 12076:51108 |
| US DoD – střed | 12076:51209 |
| US DoD – východ | 12076:51205 |


| **Služba v národních cloudech** | **Hodnota komunity protokolu BGP** |
| --- | --- |
| **US Government** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Online Skype pro firmy |12076:5130 |
| Dynamics 365 |12076:5140 |
| Jiné online služby Office 365 |12076:5200 |

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
  * [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
