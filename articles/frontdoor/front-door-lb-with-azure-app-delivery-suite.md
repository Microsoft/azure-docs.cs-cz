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
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 685ee9feaf057e4f2fae3cfe016624806f1ad00c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399459"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Vyrovnávání zatížení s využitím sady pro doručování aplikací Azure

## <a name="introduction"></a>Úvod
Microsoft Azure poskytuje několik globálních a regionálních služeb pro správu způsobu distribuce a vyrovnávání zatížení vašeho síťového provozu: Traffic Manager, front-dveří, Application Gateway a Load Balancer.  Společně s mnoha regiony a architekturou oblastí Azure vám tyto služby umožňují vytvářet robustní a škálovatelné aplikace s vysokým výkonem.

![Sada pro doručování aplikací ][1]
 
Tyto služby jsou rozdělené do dvou kategorií:
1. **Globální služby Vyrovnávání zatížení** , například Traffic Manager a přední dveře, distribuují provoz koncovými uživateli napříč místními back-endy napříč cloudy nebo dokonce i vašimi hybridními místními službami. Globální vyrovnávání zatížení směruje provoz do nejbližšího back-endu služby a reaguje na změny spolehlivosti nebo výkonu služby, aby se zajistila nepřetržitá a maximální výkon pro vaše uživatele. 
2. **Regionální služby Vyrovnávání zatížení** , jako je Standard Load Balancer nebo Application Gateway, poskytují schopnost distribuovat provoz do virtuálních sítí (virtuální sítě) napříč virtuálními počítači nebo koncovými body služby v rámci oblasti.

Kombinování globálních a regionálních služeb ve vaší aplikaci poskytuje kompletní spolehlivý, výkonné a zabezpečené způsoby směrování provozu do a z vašich uživatelů na vaše IaaS, PaaS nebo místní služby. V další části popisujeme každou z těchto služeb.

## <a name="global-load-balancing"></a>Globální vyrovnávání zatížení
**Traffic Manager** poskytuje globální vyrovnávání zatížení DNS. Vyhledává příchozí požadavky DNS a reaguje na dobrý back-end, v souladu se zásadami směrování, které zákazník vybral. Pro metody směrování jsou k disdobu tyto možnosti:
- Směrování výkonu pro odeslání žadatele do nejbližšího back-endu z podmínek latence.
- Směrování priority, které směruje veškerý provoz do back-endu, s jinými back-endy jako zálohování.
- Vážené směrování s kruhovým dotazováním, které distribuuje provoz na základě váhy přiřazené ke každému back-endu.
- Geografické směrování, které zajišťuje, aby žadatelé v konkrétních geografických oblastech byli přesměrováni na back-endy namapované na tyto oblasti (například všechny žádosti ze Španělska by měly být směrovány do oblasti Francie centrálního Azure)
- Směrování podsítě, které umožňuje mapovat rozsahy IP adres na back-endy, aby žádosti přicházející z těchto zařízení byly odesílány do zadaného back-endu (například všichni uživatelé, kteří se připojují z rozsahu IP adres vaší firmy sídel, by měli mít jiný webový obsah než uživatelé s obecnými uživateli).

Klient se připojuje přímo k tomuto back-endu. Azure Traffic Manager zjistí, že back-end není v pořádku, a pak přesměruje klienty na jinou funkční instanci. Další informace o této službě najdete v dokumentaci k [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) .

**Přední dvířka Azure** poskytují akceleraci dynamického webu (DSA) včetně globálního vyrovnávání zatížení HTTP.  Vyhledá příchozí trasy požadavků HTTP do nejbližšího back-endu nebo oblasti služby pro zadaný název hostitele, cestu URL a konfigurovaná pravidla.  
Přední dveře ukončí požadavky HTTP na hranici sítě Microsoftu a aktivně sonduje, aby zjistila, že se jedná o stav aplikace nebo infrastruktury nebo změny latence.  Přední dvířka vždy směrují provoz na nejrychlejší a dostupný (zdravý) back-end. Další informace o této službě najdete v tématu Podrobnosti [architektury směrování](front-door-routing-architecture.md) na předních dveřích a [metody směrování provozu](front-door-routing-methods.md) .

## <a name="regional-load-balancing"></a>Regionální vyrovnávání zatížení
Application Gateway poskytuje aplikaci Service Delivery Controller (ADC) jako službu a nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaši aplikaci. Umožňuje zákazníkům optimalizovat produktivitu webové farmy tím, že v aplikační bráně předává ukončení protokolu TLS náročné na procesor. Mezi další možnosti směrování vrstvy 7 patří kruhové dotazování příchozích přenosů, spřažení relací na základě souborů cookie, směrování na základě cesty URL a možnost hostování několika webů za jedinou aplikační bránou. Application Gateway lze nakonfigurovat jako bránu internetovou bránu, pouze pro interní bránu nebo kombinaci obou. Application Gateway je plně spravovaná, škálovatelná a vysoká dostupnost Azure. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu.
Load Balancer je nedílnou součástí zásobníku Azure SDN, která poskytuje vysoce výkonné služby Vyrovnávání zatížení vrstvy 4 s nízkou latencí pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Pokud chcete spravovat dostupnost služby, můžete nakonfigurovat veřejné a interní koncové body s vyrovnáváním zatížení a definovat pravidla pro mapování příchozích připojení na cíle v backendovém fondu s využitím možností monitorování stavu protokolů TCP a HTTP.


## <a name="choosing-a-global-load-balancer"></a>Výběr globálního nástroje pro vyrovnávání zatížení
Při výběru globálního nástroje pro vyrovnávání zatížení mezi Traffic Manager a předními dveřmi Azure pro globální směrování byste měli zvážit, co je podobné a co se liší od těchto dvou služeb.   Obě služby poskytují
- **Multi-geografická redundance:** Pokud dojde k výpadku jedné oblasti, provoz se hladce směruje do nejbližší oblasti bez zásahu vlastníka aplikace.
- **Směrování nejbližší oblasti:** Provoz se automaticky směruje do nejbližší oblasti.

</br>Následující tabulka popisuje rozdíly mezi Traffic Manager a předními dvířky Azure:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Libovolný protokol:** Vzhledem k tomu, že Traffic Manager funguje ve vrstvě DNS, můžete směrovat jakýkoli typ síťového provozu; HTTP, TCP, UDP atd. | **Akcelerace http:** U front-provozních přenosů je proxy serverem na hranici sítě Microsoftu.  Z toho důvodu požadavky HTTP (S) uvidí latenci a vylepšení propustnosti při snižování latence pro vyjednávání TLS a používání Hot Connections z AFD do vaší aplikace.|
|**Místní směrování:** Při směrování na vrstvě DNS se provoz vždy přechází z bodu na Point.  Směrování z firemní pobočky do místního datacentra může mít přímou cestu. i ve vaší vlastní síti pomocí Traffic Manager. | **Nezávislá škálovatelnost:** Vzhledem k tomu, že přední dvířka fungují s požadavkem HTTP, požadavky na různé cesty URL lze směrovat do různých fondů back-endu nebo regionálních služeb (mikroslužeb) na základě pravidel a stavu jednotlivých mikroslužeb aplikací.|
|**Formát fakturace:** Fakturovaná služba DNS se škáluje s vašimi uživateli a pro služby, kteří mají více uživatelů, plateaus snížit náklady při vyšším využití. |**Vložené zabezpečení:** Přední dveře umožňují pravidla, jako je omezení četnosti a seznam ACL protokolu IP, a umožňují chránit vaše back-endy před tím, než zatížení dosáhne vaší aplikace. 

</br>Vzhledem k tomu, že výhody výkonu, operability a zabezpečení pro úlohy HTTP s předními dvířky, doporučujeme zákazníkům používat pro své úlohy HTTP přední dveře.    Pro obsluhu všech přenosů vaší aplikace je možné použít Traffic Manager a přední dveře paralelně. 

## <a name="building-with-azures-application-delivery-suite"></a>Sestavování s využitím sady pro doručování aplikací Azure 
Doporučujeme, aby všechny weby, rozhraní API, služby byly geograficky redundantní a poskytovaly svým uživatelům provoz z nejbližšího umístění (nejnižší latence), kdykoli to bude možné.  Kombinování služeb z Traffic Manager, Frontních dveří, Application Gateway a Load Balancer umožňuje sestavovat geograficky a zonally redundantní a maximalizovat tak spolehlivost, škálování a výkon.

V následujícím diagramu popisujeme ukázkovou službu, která používá kombinaci všech těchto služeb k vytvoření globální webové služby.   V tomto případě se architekt použil Traffic Manager ke směrování do globálních back-endu pro doručování souborů a objektů, zatímco používá přední dvířka k globálně trasám adres URL, které odpovídají vzoru/Store/*, ke službě, kterou migrovali do App Service při směrování všech ostatních požadavků na místní aplikační brány.

V oblasti si vývojář aplikace rozhodl, že všechny adresy URL, které odpovídají vzoru/images/*, budou obsluhovány z vyhrazeného fondu virtuálních počítačů, které se liší od zbytku webové farmy.

Kromě toho musí výchozí fond virtuálních počítačů, který obsluhuje dynamický obsah, komunikovat s back-end databází, která je hostována v clusteru s vysokou dostupností. Celé nasazení je nastaveno prostřednictvím Azure Resource Manager.

Následující diagram znázorňuje architekturu tohoto scénáře:

![Podrobná architektura sady pro doručování aplikací][2] 

> [!NOTE]
> V tomto příkladu je jenom jedna z mnoha možných konfigurací služeb vyrovnávání zatížení, které Azure nabízí. Traffic Manager, přední dvířka, Application Gateway a Load Balancer lze kombinovat a porovnat s nejlepšími požadavky na Vyrovnávání zatížení. Například pokud nepotřebujete snižování zátěže TLS/SSL nebo zpracování vrstvy 7, Load Balancer lze použít místo Application Gateway.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
