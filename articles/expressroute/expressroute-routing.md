---
title: 'Požadavky směrování – ExpressRoute: Azure | Microsoft Docs'
description: Tato stránka poskytuje podrobné požadavky pro konfiguraci a správu směrování pro okruhy ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 77c90cccbbfa5865878ae4dc47e5009697560ed3
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001315"
---
# <a name="expressroute-routing-requirements"></a>Požadavky na směrování ExpressRoute
Pokud se chcete připojit ke cloudovým službám Microsoftu pomocí ExpressRoute, budete muset nastavit a spravovat směrování. Někteří poskytovatelé připojení nabízejí nastavení a správu směrování jako spravované služby. Pokud chcete zjistit, jestli nabízí tuto službu, kontaktujte svého poskytovatele připojení. Pokud ne, je nutné dodržovat následující požadavky:

Popis relací směrování, které je potřeba nastavit, najdete v článku [okruhy a domény směrování](expressroute-circuit-peerings.md) pro usnadnění připojení.

> [!NOTE]
> Společnost Microsoft nepodporuje pro konfigurace vysoké dostupnosti žádné protokoly redundance směrovače (například HSRP, nebo VRRP). Pro zajištění vysoké dostupnosti spoléháme na redundantní dvojici relací protokolu BGP na partnerský vztah.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>IP adresy používané pro partnerské vztahy
Abyste mohli konfigurovat směrování mezi vaší sítí a směrovači podnikové hraniční sítě (směrovači msee) Microsoftu, musíte si vyhradit několik bloků IP adres. Tato část obsahuje seznam požadavků a popisuje pravidla týkající se toho, jak se tyto IP adresy musí získat a použít.

### <a name="ip-addresses-used-for-azure-private-peering"></a>IP adresy, které se používají pro privátní partnerské vztahy Azure
Ke konfiguraci partnerských vztahů můžete použít buď soukromé IP adresy, nebo veřejné IP adresy. Rozsah adres, který se používá pro konfiguraci tras, se nesmí překrývat s rozsahy adres používanými k vytváření virtuálních sítí v Azure. 

* Pro rozhraní směrování musíte rezervovat podsíť/29 nebo dvě podsítě/30.
* Podsítě používané pro směrování můžou být buď soukromé IP adresy, nebo veřejné IP adresy.
* Podsítě nesmí být v konfliktu s rozsahem rezervovaným zákazníkem pro použití v cloudu Microsoftu.
* Pokud se používá podsíť/29, rozdělí se na dvě podsítě/30. 
  * První podsíť/30 se použije pro primární propojení a druhá podsíť/30 se použije pro sekundární propojení.
  * Pro každou z těchto podsítí/30 musíte na směrovači použít první IP adresu podsítě/30. Společnost Microsoft používá druhou IP adresu podsítě/30 k nastavení relace protokolu BGP.
  * Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.  

#### <a name="example-for-private-peering"></a>Příklad privátního partnerského vztahu
Pokud se rozhodnete pro nastavení partnerského vztahu použít. b. c. d/29, rozdělí se na dvě podsítě/30. V následujícím příkladu si všimněte, jak se používá podsíť a. b. c. d/29:

* a. b. c. d/29 se rozdělí na a. b. c. d/30 a a. b. c. d + 4/30 a předává se Microsoftu prostřednictvím rozhraní API pro zřizování.
  * Použijete a. b. c. d + 1 jako VRF IP adresy primárního prostředí PE a společnost Microsoft bude pro primární MSEE využívat jako VRF IP adresu. b. c. d + 2.
  * K VRF IP pro sekundární prostředí PE použijete a. b. c. d + 5 a Microsoft bude používat jako IP adresu VRF pro sekundární MSEE. b. c. d + 6.

Vezměte v úvahu případ, kdy pro nastavení privátního partnerského vztahu vyberete 192.168.100.128/29. 192.168.100.128/29 zahrnuje adresy z 192.168.100.128 do 192.168.100.135, mezi které patří:

* 192.168.100.128/30 se přiřadí k Link1 s poskytovatelem, který používá 192.168.100.129 a Microsoft pomocí 192.168.100.130.
* 192.168.100.132/30 se přiřadí k link2 s poskytovatelem, který používá 192.168.100.133 a Microsoft pomocí 192.168.100.134.

### <a name="ip-addresses-used-for-microsoft-peering"></a>IP adresy používané pro partnerský vztah Microsoftu
Pro nastavení relací protokolu BGP musíte použít veřejné IP adresy, které vlastníte. Microsoft musí být schopný ověřit vlastnictví IP adres prostřednictvím směrování Internet registry a Registry směrování v Internetu.

* IP adresy uvedené na portálu pro inzerované veřejné předpony pro partnerský vztah Microsoftu vytvoří seznamy ACL pro směrovače Microsoft Core, které umožní příchozí provoz z těchto IP adres. 
* K nastavení partnerského vztahu protokolu BGP pro každý partnerský vztah na okruh ExpressRoute (Pokud máte víc než jeden) musíte použít jedinečnou podsíť/29 (IPv4) nebo/125 (IPv6) nebo dvě podsítě/30 (IPv4) nebo/126 (IPv6).
* Pokud se používá podsíť/29, rozdělí se na dvě podsítě/30.
* První podsíť/30 se použije pro primární propojení a druhá podsíť/30 se použije pro sekundární propojení.
* Pro každou z těchto podsítí/30 musíte na směrovači použít první IP adresu podsítě/30. Společnost Microsoft používá druhou IP adresu podsítě/30 k nastavení relace protokolu BGP.
* Pokud se používá podsíť/125, rozdělí se na dvě podsítě/126.
* První podsíť/126 se používá pro primární propojení a druhá podsíť/126 se použije pro sekundární propojení.
* Pro každou z těchto podsítí/126 musíte na směrovači použít první IP adresu podsítě/126. Společnost Microsoft používá druhou IP adresu podsítě/126 k nastavení relace protokolu BGP.
* Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.

### <a name="ip-addresses-used-for-azure-public-peering"></a>IP adresy, které se používají pro veřejné partnerské vztahy Azure

> [!NOTE]
> Veřejný partnerský vztah Azure není pro nové okruhy k dispozici.
> 

Pro nastavení relací protokolu BGP musíte použít veřejné IP adresy, které vlastníte. Microsoft musí být schopný ověřit vlastnictví IP adres prostřednictvím směrování Internet registry a Registry směrování v Internetu. 

* K nastavení partnerského vztahu protokolu BGP pro každý partnerský vztah na okruh ExpressRoute (Pokud máte víc než jeden) musíte použít jedinečnou podsíť/29 nebo dvě podsítě/30. 
* Pokud se používá podsíť/29, rozdělí se na dvě podsítě/30. 
  * První podsíť/30 se použije pro primární propojení a druhá podsíť/30 se použije pro sekundární propojení.
  * Pro každou z těchto podsítí/30 musíte na směrovači použít první IP adresu podsítě/30. Společnost Microsoft používá druhou IP adresu podsítě/30 k nastavení relace protokolu BGP.
  * Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.

## <a name="public-ip-address-requirement"></a>Požadavek na veřejnou IP adresu

### <a name="private-peering"></a>Privátní partnerské vztahy
U privátního partnerského vztahu se můžete rozhodnout pro použití veřejných nebo privátních IPv4 adres. Poskytujeme komplexní izolaci provozu, takže v případě privátního partnerského vztahu není možné překrývání adres s jinými zákazníky. Tyto adresy nejsou inzerovány na Internet. 

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu
Cesta partnerského vztahu Microsoftu vám umožní připojit se ke cloudovým službám Microsoftu. Seznam služeb zahrnuje služby Office 365, jako je Exchange Online, SharePoint Online, Skype pro firmy a Microsoft Teams. Microsoft podporuje obousměrné připojení na partnerském vztahu Microsoftu. Provoz směřující do cloudových služeb Microsoftu musí před přechodem do sítě Microsoftu používat platné veřejné IPv4 adresy.

Ujistěte se, že vaše IP adresa a číslo AS jsou registrované na vás v jednom z následujících registrů:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Pokud vám vaše předpony a číslo AS nejsou přiřazeny v předchozích registrech, musíte otevřít případ podpory pro ruční ověření předpon a čísla ASN. Podpora vyžaduje dokumentaci, jako je například písmeno autorizace, které prokáže, že je povoleno používat prostředky.

U partnerského vztahu Microsoftu je povoleno soukromé číslo AS, ale bude také vyžadovat ruční ověření. Kromě toho pro přijaté předpony odebereme soukromá čísla AS v cestě AS. V důsledku toho nebudete moci k [ovlivnění směrování pro partnerský vztah Microsoftu](expressroute-optimize-routing.md)připojit soukromé údaje jako čísla v cestě as. 

> [!IMPORTANT]
> Neinzerovat stejnou veřejnou trasu IP k veřejnému Internetu a přes ExpressRoute. Aby se snížilo riziko nesprávné konfigurace, která způsobuje asymetrické směrování, důrazně doporučujeme, aby [IP adresy NAT](expressroute-nat.md) inzerované Microsoftu přes ExpressRoute byly z rozsahu, který není inzerovaný pro Internet vůbec. Pokud to není možné dosáhnout, je nutné zajistit, abyste inzerovali konkrétnější rozsah přes ExpressRoute, než je ten v připojení k Internetu. Kromě veřejné trasy pro překlad adres (NAT) můžete také inzerovat ExpressRoute veřejné IP adresy používané servery ve vaší místní síti, které komunikují s koncovými body Office 365 v rámci Microsoftu. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Veřejný partnerský vztah (zastaralý – není k dispozici pro nové okruhy)
Cesta veřejného partnerského vztahu Azure vám umožní připojit se ke všem službám hostovaným v Azure přes jejich veřejné IP adresy. Patří sem služby uvedené v [ExpessRoute – Nejčastější dotazy](expressroute-faqs.md) a jakékoli služby hostované nezávislými výrobci softwaru na Microsoft Azure. Připojení k Microsoft Azure službám ve veřejném partnerském vztahu je vždy iniciováno z vaší sítě do sítě Microsoftu. Pro provoz směřující do sítě Microsoft je nutné použít veřejné IP adresy.

> [!IMPORTANT]
> Všechny služby Azure PaaS jsou přístupné prostřednictvím partnerského vztahu Microsoftu.
>   

U veřejného partnerského vztahu je povolené soukromé číslo AS.

## <a name="dynamic-route-exchange"></a>Dynamická výměna tras
Výměna směrování bude přes eBGP protokol. Mezi směrovači msee a vašimi směrovači se vytváří EBGP relace. Ověřování relací protokolu BGP není požadavkem. V případě potřeby je možné nakonfigurovat hodnotu hash MD5. Informace o konfiguraci relací protokolu BGP najdete v tématu pracovní postupy [Konfigurace směrování](how-to-routefilter-portal.md) a [okruhu zřizování a stavy okruhu](expressroute-workflows.md) .

## <a name="autonomous-system-numbers"></a>Čísla autonomního systému
Microsoft používá jako veřejné partnerské vztahy Azure a veřejný partnerský vztah Microsoftu jako 12076. Pro interní použití jsme rezervovali čísla ASN z 65515 na 65520. Jsou podporovány 16 i 32 bitů jako čísla.

Neexistují žádné požadavky kolem symetrie přenosu dat. Cesty pro dopředné a návratové cesty můžou procházet různými páry směrovačů. Identické trasy musí být inzerovány z obou stran napříč páry okruhů, které patří vám. Metriky tras nemusí být identické.

## <a name="route-aggregation-and-prefix-limits"></a>Agregace směrování a omezení předpony
V rámci privátního partnerského vztahu Azure podporujeme až 4000 předpony inzerované pro nás. To se dá zvýšit až 10 000 předpon, pokud je povolený doplněk ExpressRoute Premium. Pro veřejné partnerské vztahy Azure a partnerské vztahy Microsoftu přijímáme až 200 předpon na jednu relaci protokolu BGP. 

Relace protokolu BGP je vyřazena v případě, že počet předpon překračuje limit. Budeme přijímat výchozí trasy jenom na propojení privátního partnerského vztahu. Zprostředkovatel musí odfiltrovat výchozí trasu a privátní IP adresy (RFC 1918) z veřejných cest Azure a partnerských vztahů Microsoftu. 

## <a name="transit-routing-and-cross-region-routing"></a>Směrování přenosu a směrování mezi oblastmi
ExpressRoute se nedá nakonfigurovat jako tranzitní směrovače. Pro služby přenosu směrování budete muset spoléhat na poskytovatele připojení.

## <a name="advertising-default-routes"></a>Inzerování výchozích tras
Výchozí trasy jsou povolené jenom v relacích s privátním partnerským vztahem Azure. V takovém případě provedeme směrování všech přenosů z přidružených virtuálních sítí do vaší sítě. Inzerování výchozích tras do privátního partnerského vztahu bude mít za následek blokování internetové cesty z Azure. Aby bylo možné směrovat provoz z a na Internet pro služby hostované v Azure, musíte spoléhat na firemní hraniční síť. 

 Pokud chcete povolit připojení k dalším službám Azure a službám infrastruktury, musíte se ujistit, že je nastavená jedna z následujících položek:

* Veřejný partnerský vztah Azure povoluje směrování provozu do veřejných koncových bodů.
* Pomocí uživatelsky definovaného směrování povolíte připojení k Internetu pro každou podsíť, která vyžaduje připojení k Internetu.

> [!NOTE]
> Inzerování výchozích tras způsobí přerušení aktivace Windows a dalších licencí virtuálních počítačů. Pokud chcete [Tento](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) problém obejít, postupujte podle pokynů.
> 
> 

## <a name="bgp"></a>Podpora pro komunity protokolu BGP
Tato část poskytuje přehled o tom, jak se používají komunity protokolu BGP se službou ExpressRoute. Microsoft bude inzerovat trasy v cestách veřejných a partnerských vztahů Microsoftu s trasami, které jsou označené odpovídajícími hodnotami komunity. To je odůvodněné a podrobné informace o hodnotách komunity jsou popsány níže. Společnost Microsoft ale nerespektuje žádné hodnoty komunity, které jsou označeny pro trasy inzerované společnosti Microsoft.

Pokud se připojujete k Microsoftu prostřednictvím ExpressRoute v jednom umístění partnerského vztahu v rámci geopolitické oblasti, budete mít přístup ke všem cloudovým službám Microsoftu ve všech oblastech v rámci geopolitické hranice. 

Pokud jste například přes ExpressRoute připojili k Microsoftu v Amsterdam, budete mít přístup ke všem cloudovým službám Microsoftu hostovaným v Severní Evropa a Západní Evropa. 

Podrobný seznam geopolitických oblastí, přidružených oblastí Azure a odpovídajících umístění partnerského vztahu ExpressRoute najdete na stránce [partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md) .

Pro geopolitickou oblast si můžete koupit více než jeden okruh ExpressRoute. Více připojení nabízí významné výhody pro vysokou dostupnost z důvodu geografické redundance. V případech, kdy máte více okruhů ExpressRoute, obdržíte stejnou sadu předpon inzerovaných od Microsoftu na cestách partnerských vztahů Microsoftu a veřejných partnerských vztahů. To znamená, že budete mít několik cest od vaší sítě do Microsoftu. To může potenciálně způsobit, že se v rámci vaší sítě budou provádět rozhodnutí o neoptimálním směrování. V důsledku toho může docházet k neoptimálnímu prostředí připojení k různým službám. Můžete spoléhat na hodnoty komunity a učinit tak vhodná rozhodnutí o směrování, která [uživatelům nabízejí optimální směrování](expressroute-optimize-routing.md).

| **Oblast Microsoft Azure** | **Oblastní komunita protokolu BGP** | **Komunita protokolu BGP úložiště** | **Komunita protokolu BGP SQL** | **Cosmos DB komunita protokolu BGP** |
| --- | --- | --- | --- | --- |
| **Severní Amerika** | |
| Východní USA | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 |
| Východní USA 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 |
| Západní USA | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 |
| Západní USA 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 |
| Středozápadní USA | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 |
| Střed USA – sever | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 |
| Střed USA – jih | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 |
| Střed USA | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 |
| Kanada – střed | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 |
| Kanada – východ | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 |
| **Jižní Amerika** | |
| Brazílie – jih | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 |
| **Evropským** | |
| Severní Evropa | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 |
| Západní Evropa | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 |
| Velká Británie – jih | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 |
| Velká Británie – západ | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 |
| Francie – střed | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 |
| Francie – jih | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 |
| **Asie a Tichomoří** | |
| Východní Asie | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 |
| Jihovýchodní Asie | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 |
| **Japonsko** | |
| Japonsko – východ | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 |
| Japonsko – západ | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 |
| **Austrálie** | |
| Austrálie – východ | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 |
| Austrálie – jihovýchod | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 |
| **Austrálie – státní správa** | |
| Austrálie – střed | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 |
| Austrálie – střed 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 |
| **Indie** | |
| Indie – jih | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 |
| Indie – západ | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 |
| Indie – střed | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 |
| **Korea** | |
| Korea – jih | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 |
| Korea – střed | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 |
| **Jihoafrická republika**| |
| Jižní Afrika – sever | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 |
| Jižní Afrika – západ | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 |
| **Spojené arabské emiráty**| |
| Spojené arabské emiráty sever | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 |
| Spojené arabské emiráty – střed | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 |


Všechny trasy inzerované od Microsoftu budou označené odpovídající hodnotou komunity. 

> [!IMPORTANT]
> Globální předpony jsou označené odpovídající hodnotou komunity.
> 
> 

### <a name="service-to-bgp-community-value"></a>Hodnota služby pro komunitu BGP
Kromě výše uvedeného bude Microsoft označovat také předpony na základě služby, ke které patří. To platí jenom pro partnerský vztah Microsoftu. Následující tabulka poskytuje mapování služby na hodnotu komunity protokolu BGP. Pomocí rutiny Get-AzBgpServiceCommunity můžete spustit úplný seznam nejnovějších hodnot.

| **Service** | **Hodnota komunity protokolu BGP** |
| --- | --- |
| Exchange Online | 12076:5010 |
| SharePoint Online | 12076:5020 |
| Online Skype pro firmy | 12076:5030 |
| Globální služby Azure * | 12076:5050 |
| Další online služby Office 365 | 12076:5100 |

\* Globální služby Azure v tuto chvíli obsahují jenom Azure DevOps.


> [!NOTE]
> Společnost Microsoft nedodržuje žádné hodnoty komunity protokolu BGP, které jste nastavili na trasách inzerovaných Microsoftu.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>Podpora komunity protokolu BGP v národních cloudech

| **Oblast Azure národních cloudů**| **Hodnota komunity protokolu BGP** |
| --- | --- |
| **Státní správa USA** |  |
| US Gov – Arizona | 12076:51106 |
| US Gov – Iowa | 12076:51109 |
| US Gov – Virginie | 12076:51105 |
| US Gov – Texas | 12076:51108 |
| US DoD – střed | 12076:51209 |
| US DoD – východ | 12076:51205 |


| **Služba v národních cloudech** | **Hodnota komunity protokolu BGP** |
| --- | --- |
| **Státní správa USA** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Online Skype pro firmy |12076:5130 |
| Další online služby Office 365 |12076:5200 |

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření a úprava okruhu](expressroute-howto-circuit-arm.md)
  * [Vytvoření a úprava konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
