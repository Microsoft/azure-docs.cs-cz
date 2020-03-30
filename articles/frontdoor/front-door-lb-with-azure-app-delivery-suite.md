---
title: Azure Front Door – vyrovnávání zatížení pomocí sady pro doručování aplikací Azure | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže dozvědět se o tom, jak Azure doporučuje vyrovnávání zatížení s jeho sadu doručování aplikací
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 9f8d1959549eaddfb4a2c9ea271094db0073c788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471706"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Vyrovnávání zatížení s využitím sady pro doručování aplikací Azure

## <a name="introduction"></a>Úvod
Microsoft Azure poskytuje několik globálních a regionálních služeb pro správu distribuce a vyrovnávání zatížení vašeho síťového provozu: Traffic Manager, Front Door, Application Gateway a Load Balancer.  Spolu s mnoha oblastmi Azure a architekturou zonální chod, pomocí těchto služeb společně umožňují vytvářet robustní, škálovatelné vysoce výkonné aplikace.

![Sada pro doručování aplikací ][1]
 
Tyto služby jsou rozděleny do dvou kategorií:
1. **Globální služby vyrovnávání zatížení,** jako je Traffic Manager a Front Door, distribuují provoz od koncových uživatelů napříč místními back-endy, napříč cloudy nebo dokonce hybridními místními službami. Globální vyrovnávání zatížení směruje váš provoz do vašeho nejbližšího back-endu služby a reaguje na změny ve spolehlivosti nebo výkonu služeb, aby byl zachován vždy zapnutý a maximální výkon pro vaše uživatele. 
2. **Regionální služby vyrovnávání zatížení,** jako je standardní nástroj pro vyrovnávání zatížení nebo aplikační brána, poskytují možnost distribuovat provoz v rámci virtuálních sítí (VNET) mezi virtuálními počítači (VM) nebo koncovými body zónové služby v rámci oblasti.

Kombinace globálních a regionálních služeb ve vaší aplikaci poskytuje komplexní spolehlivý, výkonný a bezpečný způsob směrování provozu k uživatelům a od uživatelů do služeb IaaS, PaaS nebo místních služeb. V další části popisujeme každou z těchto služeb.

## <a name="global-load-balancing"></a>Globální vyrovnávání zatížení
**Traffic Manager** poskytuje globální vyrovnávání zatížení DNS. Podívá se na příchozí požadavky DNS a reaguje s back-endem v pořádku v souladu se zásadami směrování, které zákazník vybral. Možnosti pro metody směrování jsou:
- Směrování výkonu k odeslání žadatele do nejbližšího back-endu z hlediska latence.
- Prioritní směrování pro přesměrování veškerého provozu do back-endu, s ostatními back-endy jako zálohou.
- Vážené kruhové dotazování, které distribuuje provoz na základě vážení, které je přiřazeno každému back-endu.
- Geografické směrování, které zajistí, že žadatelé umístění v určitých zeměpisných oblastech budou přesměrováni na back-endy namapované na tyto oblasti (například všechny požadavky ze Španělska by měly být směrovány do oblasti Francie – středOvý Azure).
- Směrování podsítě, které umožňuje mapovat rozsahy IP adres na back-endy, takže požadavky přicházející z nich budou odeslány do zadaného back-endu (například všichni uživatelé, kteří se připojují z rozsahu IP adres vašeho podnikového ústředí, by měli získat jiný webový obsah než obecný uživatelů)

Klient se připojí přímo k tomuto back-endu. Azure Traffic Manager zjistí, když back-end není v pořádku a pak přesměruje klienty na jinou instanci v pořádku. Další informace o službě najdete v dokumentaci k [Azure Traffic Manageru.](../traffic-manager/traffic-manager-overview.md)

**Azure Front Door** poskytuje dynamickou akceleraci webu (DSA) včetně globálního vyrovnávání zatížení HTTP.  Podívá se na příchozí požadavky HTTP trasy do nejbližší služby back-end / oblast pro zadaný název hostitele, URL cestu a nakonfigurovaná pravidla.  
Front Door ukončí požadavky HTTP na okraji sítě společnosti Microsoft a aktivně sonduje ke zjištění stavu aplikace nebo infrastruktury nebo změn latence.  Přední dveře pak vždy směruje provoz na nejrychlejší a dostupný (zdravý) backend. Další informace o službě naleznete v [podrobnostech architektury směrování](front-door-routing-architecture.md) a [metodách směrování provozu.](front-door-routing-methods.md)

## <a name="regional-load-balancing"></a>Regionální vyrovnávání zatížení
Aplikační brána poskytuje řadič pro doručování aplikací (ADC) jako službu, která nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaši aplikaci. Umožňuje zákazníkům optimalizovat produktivitu webové farmy převedením ukončení SSL náročné na procesor na aplikační bránu. Mezi další možnosti směrování vrstvy 7 patří distribuce příchozích přenosů s každým dotazováním, spřažení relací na základě souborů cookie, směrování založené na cestě url a možnost hostovat více webů za jednou aplikační bránou. Aplikační bránu lze nakonfigurovat jako internetovou bránu, bránu pouze pro interní nebo kombinaci obou. Aplikační brána je plně spravovaná, škálovatelná a vysoce dostupná. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu.
Nástroj pro vyrovnávání zatížení je nedílnou součástí zásobníku Azure SDN a poskytuje vysoce výkonné služby vyrovnávání zatížení vrstvy 4 s nízkou latencí pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Pokud chcete spravovat dostupnost služby, můžete nakonfigurovat veřejné a interní koncové body s vyrovnáváním zatížení a definovat pravidla pro mapování příchozích připojení na cíle v backendovém fondu s využitím možností monitorování stavu protokolů TCP a HTTP.


## <a name="choosing-a-global-load-balancer"></a>Výběr globálního systému vyrovnávání zatížení
Při výběru globálního nástroje pro vyrovnávání zatížení mezi Traffic Managerem a Azure Front Door pro globální směrování byste měli zvážit, co je podobné a co se liší o dvou službách.   Obě služby poskytují
- **Multigeografická redundance:** Pokud jedna oblast přejde dolů, provoz bez problémů směruje do nejbližší oblasti bez zásahu vlastníka aplikace.
- **Směrování nejbližší oblasti:** Provoz je automaticky směrován do nejbližší oblasti

</br>Následující tabulka popisuje rozdíly mezi Traffic Managerem a Azure Front Door:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Jakýkoliv protokol:** Vzhledem k tomu, že Traffic Manager pracuje ve vrstvě DNS, můžete směrovat libovolný typ síťového provozu. HTTP, TCP, UDP atd. | **Akcelerace HTTP:** S front door provoz je proxied na okraji sítě společnosti Microsoft.  Z tohoto důvodu požadavky HTTP(S) viz latence a propustnost zlepšení snížení latence pro vyjednávání SSL a pomocí připojení za tepla z AFD do aplikace.|
|**Místní směrování:** Při směrování ve vrstvě DNS se provoz vždy převádí z bodu do bodu.  Směrování z vaší pobočky do vašeho místního datového centra může mít přímou cestu; dokonce i ve vaší vlastní síti pomocí Traffic Manageru. | **Nezávislé škálovatelnosti:** Vzhledem k tomu, že front door pracuje s požadavkem HTTP, požadavky na různé cesty URL mohou být směrovány do různých fondů back-endu / regionálních služeb (mikroslužeb) na základě pravidel a stavu každé mikroslužby aplikace.|
|**Formát fakturace:** Fakturační stupnice založené na SLUŽBĚ DNS s vašimi uživateli a pro služby s více uživateli snižují náklady při vyšším využití. |**Zabezpečení v řádech:** Přední dveře umožňují pravidla, jako je omezení rychlosti a IP ACL-ing, které vám umožní chránit backendy před provoz dosáhne vaší aplikace. 

</br>Vzhledem k výkonu, provozuschopnosti a výhodzabezpečení úloh HTTP s front door doporučujeme zákazníkům používat front door pro své úlohy HTTP.    Traffic Manager a Front Door lze použít paralelně sloužit veškerý provoz pro vaši aplikaci. 

## <a name="building-with-azures-application-delivery-suite"></a>Vytváření s sadou pro doručování aplikací Azure 
Doporučujeme, aby všechny weby, api, služby byly geograficky redundantní a doručovaly jejich uživatelům provoz z nejbližšího umístění (nejnižší latence), kdykoli je to možné.  Kombinace služeb z Traffic Manager, Front Door, Application Gateway a Load Balancer umožňuje vytvářet geograficky a zónově redundantní maximalizovat spolehlivost, škálování a výkon.

V následujícím diagramu popisujeme ukázkovou službu, která používá kombinaci všech těchto služeb k vytvoření globální webové služby.   V tomto případě architekt použil Traffic Manager k směrování do globálních back-endů pro doručování souborů a objektů, zatímco pomocí front door globálně směrovat cesty URL, které odpovídají vzoru /store/* ke službě, kterou migrovali do služby App Service, při směrování všech ostatních požadavků na regionální aplikační brány.

V oblasti pro svou službu IaaS vývojář aplikace rozhodl, že všechny adresy URL, které odpovídají vzoru /images/* jsou obsluhovány z vyhrazeného fondu virtuálních stránek, které se liší od zbytku webové farmy.

Kromě toho výchozí fond virtuálních her, který slouží dynamický obsah potřebuje mluvit do back-end databáze, která je hostovaná v clusteru s vysokou dostupností. Celé nasazení se nastavuje prostřednictvím Správce prostředků Azure.

Následující diagram znázorňuje architekturu tohoto scénáře:

![Podrobná architektura sady Application Delivery Suite][2] 

> [!NOTE]
> Tento příklad je pouze jedním z mnoha možných konfigurací služby vyrovnávání zatížení, které Azure nabízí. Traffic Manager, Front Door, Application Gateway a Load Balancer mohou být smíchány a spárovány tak, aby co nejlépe vyhovovaly vašim potřebám vyrovnávání zatížení. Například pokud ssl přepětí nebo vrstva 7 zpracování není nutné, vyrovnávání zatížení lze použít místo aplikační brány.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
