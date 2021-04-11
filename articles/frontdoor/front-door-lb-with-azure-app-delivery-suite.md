---
title: Přední dvířka Azure – vyrovnávání zatížení s využitím sady pro doručování aplikací Azure | Microsoft Docs
description: Tento článek vám pomůže získat informace o tom, jak Azure doporučuje vyrovnávání zatížení s jeho sadou pro doručování aplikací.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 0a7e81c57552fdc24262522343a08fdabba71bfd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552576"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Vyrovnávání zatížení s využitím sady pro doručování aplikací Azure

## <a name="introduction"></a>Úvod
Microsoft Azure poskytuje různé globální a regionální služby pro správu způsobu distribuce a vyrovnávání zatížení vašeho síťového provozu: 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

Společně s mnoha regiony a architekturou oblastí Azure vám tyto služby umožňují vytvářet robustní, škálovatelné a vysoce výkonné aplikace.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Sada pro doručování aplikací":::
 
Tyto služby jsou rozdělené do dvou kategorií:
1. **Globální služby Vyrovnávání zatížení** , například Traffic Manager a přední dveře, distribuují provoz koncovými uživateli napříč místními back-endy napříč cloudy a dokonce i vašimi hybridními místními službami. Globální vyrovnávání zatížení směruje provoz do nejbližšího back-endu služby a reaguje na změny spolehlivosti služby, aby se zajistila nepřetržitá dostupnost a vysoký výkon pro vaše uživatele. 
1. **Regionální služby Vyrovnávání zatížení** , jako jsou nástroje pro vyrovnávání zatížení a aplikační brány, poskytují možnost distribuovat provoz do virtuálních počítačů v rámci virtuální sítě (virtuální sítě) nebo koncových bodů služby v rámci oblasti.

Při kombinaci těchto globálních a regionálních služeb bude vaše aplikace využívat spolehlivý a zabezpečený koncový provoz, který bude odeslán koncovým uživatelům z vašich IaaS, PaaS nebo místních služeb. V další části popisujeme každou z těchto služeb.

## <a name="global-load-balancing"></a>Globální vyrovnávání zatížení
**Traffic Manager** poskytuje globální vyrovnávání zatížení DNS. Vyhledá příchozí požadavky DNS a odpoví na zdravý back-end, a to za zásadu směrování, kterou si zákazník vybral. Pro metody směrování jsou k disdobu tyto možnosti:
- **Směrování výkonu** odesílá požadavky do nejbližšího back-endu s minimální latencí.
- **Priorita směrování** směruje veškerý provoz do back-endu a další back-endy jako zálohu.
- **Směrování váženého kruhového dotazování** distribuuje provoz na základě váhy přiřazené ke každému back-endu.
- **Geografické směrování** zajišťuje, aby žádosti, které získávají zdroje z konkrétních geografických oblastí, byly zpracovány pomocí back-endu mapovaných pro tyto oblasti. (Například všechny požadavky ze Španělska by měly být směrovány do oblasti Francie – střed Azure)
- **Směrování podsítě** umožňuje mapovat rozsahy IP adres na back-endy, aby příchozí požadavky na tyto IP adresy byly odesílány do konkrétního back-endu. (Například všichni uživatelé, kteří se připojují z rozsahu IP adres vaší podnikové sídel, by měli mít jiný webový obsah než uživatelé s obecnými uživateli.)

Klient se připojuje přímo k tomuto back-endu. Azure Traffic Manager zjistí, že back-end není v pořádku, a pak přesměruje klienty na jinou funkční instanci. Další informace o této službě najdete v dokumentaci k [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) .

**Přední dvířka Azure** poskytují akceleraci dynamického webu (DSA) včetně globálního vyrovnávání zatížení HTTP.  Vyhledá příchozí trasy požadavků HTTP do nejbližšího back-endu nebo oblasti služby pro zadaný název hostitele, cestu URL a konfigurovaná pravidla.  
Přední dveře ukončí požadavky HTTP na hranici sítě Microsoftu a aktivně sonduje, aby zjistila, že se jedná o stav aplikace nebo infrastruktury nebo změny latence.  Přední dvířka vždy směrují provoz na nejrychlejší a dostupný (zdravý) back-end. Další informace o této službě najdete v tématu Podrobnosti [architektury směrování](front-door-routing-architecture.md) na předních dveřích a [metody směrování provozu](front-door-routing-methods.md) .

## <a name="regional-load-balancing"></a>Regionální vyrovnávání zatížení
Application Gateway poskytuje aplikaci Service Delivery Controller (ADC) jako službu a nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaši aplikaci. Umožňuje zákazníkům optimalizovat produktivitu webové farmy tím, že v aplikační bráně předává ukončení protokolu TLS náročné na procesor. Mezi další možnosti směrování vrstvy 7 patří také distribuce příchozích přenosů s využitím kruhového dotazování, spřažení relací na základě souborů cookie, směrování na základě cesty URL a možnost hostování několika webů za jedinou aplikační bránou. Application Gateway lze nakonfigurovat jako koncový bod s přístupem k Internetu, koncový bod pouze interní nebo kombinaci obou. Application Gateway je plně spravovaná Azure a poskytuje škálovatelnost a vysokou dostupnost. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu.

Nástroje pro vyrovnávání zatížení jsou nedílnou součástí zásobníku Azure SDN, která poskytuje vysoce výkonné služby Vyrovnávání zatížení vrstvy 4 s nízkou latencí pro všechny protokoly UDP a TCP. Veřejné nebo interní koncové body s vyrovnáváním zatížení můžete nakonfigurovat tak, že definujete pravidla, která mapují příchozí připojení k fondům back-endu. Díky sledování stavu pomocí protokolu TCP nebo HTTPS vám může pomoci se správou dostupnosti služby.

## <a name="choosing-a-global-load-balancer"></a>Výběr globálního nástroje pro vyrovnávání zatížení
Při výběru globálního nástroje pro vyrovnávání zatížení mezi Traffic Manager a předními dveřmi Azure pro globální směrování byste měli zvážit, co je podobné a co se liší od těchto dvou služeb.   Obě služby poskytují
- **Multi-geografická redundance:** Pokud se jedna oblast přestane vysílat, provoz se bez jakýchkoli zásahů od vlastníka aplikace plynule směruje do nejbližší oblasti.
- **Směrování nejbližší oblasti:** Provoz se automaticky směruje do nejbližší oblasti.

</br>Následující tabulka popisuje rozdíly mezi Traffic Manager a předními dvířky Azure:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Libovolný protokol:** Vzhledem k tomu, že Traffic Manager funguje ve vrstvě DNS, můžete směrovat jakýkoli typ síťového provozu; HTTP, TCP, UDP atd. | **Akcelerace http:** S předními dvířky je provoz proxy serverem na hranici sítě Microsoft. Požadavky HTTP/S uvidí vylepšení latence a propustnosti, což snižuje latenci při vyjednávání TLS.|
|**Místní směrování:** Při směrování na vrstvě DNS se provoz vždy přechází z bodu na Point.  Směrování z firemní pobočky do místního datacentra může mít přímou cestu. i ve vaší vlastní síti pomocí Traffic Manager. | **Nezávislá škálovatelnost:** Vzhledem k tomu, že přední dvířka fungují s požadavkem HTTP, můžou požadavky na různé cesty URL směrovat do různých skupin back-endu nebo regionálních fondů služeb (mikroslužby) na základě pravidel a stavu jednotlivých mikroslužeb aplikací.|
|**Formát fakturace:** Fakturovaná služba DNS se škáluje s vašimi uživateli a pro služby, kteří mají více uživatelů, plateaus snížit náklady při vyšším využití. |**Vložené zabezpečení:** Přední dveře umožňují pravidla, jako je omezení četnosti a seznam ACL protokolu IP, a umožňují chránit vaše back-endy před tím, než zatížení dosáhne vaší aplikace. 

</br>Zákazníkům doporučujeme, aby pro svou úlohu HTTP používali přední dvířka z důvodu výhod výkonu, operability a zabezpečení, které HTTP funguje s předními dveřmi. Pro obsluhu všech přenosů vaší aplikace je možné použít Traffic Manager a přední dveře paralelně. 

## <a name="building-with-azures-application-delivery-suite"></a>Sestavování s využitím sady pro doručování aplikací Azure 
Doporučujeme, aby všechny weby, rozhraní API, služby byly geograficky redundantní, aby bylo možné doručovat přenosy na uživatele z nejbližšího umístění, kdykoli je to možné.  Kombinování více služeb vyrovnávání zatížení vám umožní sestavovat geografickou a geografickou redundanci, aby se maximalizovala spolehlivost a výkon.

V následujícím diagramu popisujeme ukázkovou architekturu, která používá kombinaci všech těchto služeb k vytvoření globální webové služby. Architekt používá Traffic Manager ke směrování provozu do globálních back-endu pro doručování souborů a objektů. Při použití front-dveří pro globální směrování cest URL, které odpovídají vzoru/Store/*, na službu, kterou migrovali na App Service. Nakonec vysměrování všech ostatních požadavků na místní aplikační brány.

V každé oblasti služby IaaS se vývojář aplikace rozhodl, že všechny adresy URL, které odpovídají vzoru/images/*, budou obsluhovány z vyhrazeného fondu virtuálních počítačů. Tento fond virtuálních počítačů se liší od zbytku webové farmy.

Kromě toho musí výchozí fond virtuálních počítačů, který obsluhuje dynamický obsah, komunikovat s back-end databází, která je hostována v clusteru s vysokou dostupností. Celé nasazení je nakonfigurované prostřednictvím Azure Resource Manager.

Následující diagram znázorňuje architekturu tohoto scénáře:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Podrobná architektura sady pro doručování aplikací":::

> [!NOTE]
> V tomto příkladu je jenom jedna z mnoha možných konfigurací služeb vyrovnávání zatížení, které Azure nabízí. Traffic Manager, přední dvířka, Application Gateway a Load Balancer lze kombinovat a porovnat s nejlepšími požadavky na Vyrovnávání zatížení. Například pokud nepotřebujete snižování zátěže TLS/SSL nebo zpracování vrstvy 7, Load Balancer lze použít místo Application Gateway.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
